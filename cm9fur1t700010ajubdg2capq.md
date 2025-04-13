---
title: "Why Wildcard Searches in Redis Can Risk Your Job"
datePublished: Sun Apr 13 2025 16:21:34 GMT+0000 (Coordinated Universal Time)
cuid: cm9fur1t700010ajubdg2capq
slug: why-wildcard-searches-in-redis-can-risk-your-job
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/sxQz2VfoFBE/upload/b121f937f80f7d441c3666824eb0e38d.jpeg
tags: redis, mistakes-to-avoid

---

We were happily integrating Redis into some parts of our decently-sized app until one day it broke. We mistakenly used a seemingly innocent command in our most frequently hit API endpoints, our core business-logic, which cost us a lot of potential sales from our users.

Luckily, no one got fired from this incident. If it had ended differently, I would have been the first to go, LOL. In this post, I'll share our experience so you can AVOID making the same mistake we did.

# Everybody Loves Speed

Our customers loved it, our clients demanded it, our PMs were really happy about it, and we engineers could flex our double-digit milliseconds response time on LinkedIn.

That's why we love Redis. It allows us to achieve speed with a very simple setup, and apparently, most developers agree. According to this [Stack Overflow Developer Survey 2024](https://survey.stackoverflow.co/2024/technology#most-popular-technologies-database-prof), Redis is still among the top 10 most popular databases among professionals.

We used it in the most critical part of an e-commerce app we built, which is to cache product data. To be more specific, our product data is not much different from other e-commerce apps; it's the pricing that makes it unique. In short, each customer can see different prices based on their region. It's similar to how Steam sets game prices differently depending on where on earth you live.

When I wrote this article, our user base was about 15,000 users. Each user could have either shared regional pricing or unique pricing just for them. This business requirement results in millions of records for product pricing. Searching through these records every time users browse products is slow, and there's only so much that database indexing can do. Hence, we cache them.

The second unique aspect of our app is that some products have more volatile pricing than others, and our e-commerce app is not the only sales channel. Therefore, we use a third-party ERP to store the most up-to-date pricing data for each product for each customer. It also acts as an aggregator for all orders from different sales channels.

So, we need to check the current price for each product right when a customer clicks the checkout button. After getting the most up-to-date, and possibly different, price, we must invalidated the cache and update it with the new pricing for use on pages other than the checkout page.

Okay, I hope this brings us to the same understanding of what we're dealing with. Now, let's review some Redis internals to further explain why it is incredibly fast and very suitable for our use case.

# Redis == Speed, How?

According to [ByteByteGo](https://blog.bytebytego.com/p/why-is-redis-so-fast), Redis is incredibly fast because it is a RAM-based database, which is 1000x faster than accessing data from a disk. As a developer, I like to think of Redis as similar to the in-memory array or list we use in our programs to store data. It is indeed much faster compared to writing data to a .csv file, for example.

The next reason Redis is fast is that it's single-threaded! I know it might seem counterintuitive because we usually speed up apps by splitting tasks so multiple computations can run in parallel through multi-threading.

However, consider this: running tasks in parallel with multi-threading or multi-processing requires careful management of locks or mutexes. Different processes can write to the same data, one process might read data while others are changing it, and let's not forget about deadlocks. I recently gave a talk about this locking topic, specifically for Postgres. You can check it out [here](https://www.youtube.com/watch?v=RFkytUdY-yU) to get a sense of how complex multi-threaded apps are and how they require more advanced concurrency control or locking.

By using a single-threaded approach, Redis doesn't need a locking mechanism, which speeds up data queries and mutations because it doesn't have to check what other processes are doing. **Instead, it serializes every incoming command and executes it one at a time.**

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">As for how Redis can handle multiple connections at once while being single-threaded, it uses a concept called I/O Multiplexing and Non-blocking I/O. However, the details of this are beyond the scope of this article.</div>
</div>

# Watch Out for Contention

By being single-threaded, each command is executed one at a time, or atomically, meaning the main event loop is blocked for as long as it takes for that command to finish. This is the part we often overlook.

%[https://www.youtube.com/watch?v=Suugn-p5C1M] 

Contention in Redis is similar to traffic jams in the videos mentioned above. A slight slowdown of one car creates a domino effect, slowing down other cars, and eventually, you get stuck in traffic.

Think of the circular road in the video as the Redis event loop, with each individual car representing a command that needs to be executed one by one. A single command that takes slightly longer to complete can block other commands, while new commands keep coming, creating contention, or Redis traffic, if you prefer that term.

That's exactly what we're experiencing. Remember our e-commerce checkout workflow? We need to update the product pricing every time a customer makes an orders, and currently, we average 3,000 orders daily.

We cache the product and its price using this Redis key format: `product/:productID/:customerID`. To keep the pricing data in the cache up-to-date, we need to invalidate all caches for that `productID` across all `customerID`s. However, we don't know which customers already have the cache filled, so we use a wildcard search to find all matching keys with this pattern, `product/:productID/*`, just to invalidate them, 3,000 times a day.

While this perfectly okay with smaller datasets, as our cache datasets grow, the same command starts to slow down, burning the whole app down.

You might wonder, isn't this problem only affecting the checkout page? I must be exaggerating by saying this small mistake is bringing our app down. Well, the checkout page isn't the only place we use Redis. We also use it as a rate limiter because our earlier infrastructure didn't handle this out of the box. Remember that Redis executes every command one at a time? This means a simple [SET](https://redis.io/docs/latest/commands/set/), [GET](https://redis.io/docs/latest/commands/get/) and [INCR](https://redis.io/docs/latest/commands/incr/) command to track how many requests are made by a client IP address also has to wait for that slow wildcard search command. So, this slowdown is literally burning the whole app.

# Redis Search Benchmark

There are two commands we know of for doing wildcard searches on Redis: [KEYS](https://redis.io/docs/latest/commands/keys/) and [SCAN](https://redis.io/docs/latest/commands/scan/). If you click the links for these commands, you'll be taken to the Redis documentation, where it's clear that both commands are marked as @slow by Redis, and KEYS is also marked as @dangerous. I didn't fully understand the importance of this warning before—how slow could it really be, right??

The KEYS command performs a full scan, O(N) complexities, going through every key in your dataset to find those that match your pattern. It blocks the event loop until it has checked **every key**. On the other hand, SCAN does the same task in chunks, blocking the event loop only until a portion of keys is checked, allowing other commands to be executed without waiting for all keys to be visited like KEYS does. Therefore, SCAN is generally recommended for wildcard searches in production. But is it?

An earlier version of our app used the [KEYS](https://redis.io/docs/latest/commands/keys/) command for wildcard searches. Later, we refactored it to use [SCAN](https://redis.io/docs/latest/commands/scan/), only to find that it didn't save our app. Below, we will show some benchmark results to support this finding. The specifics of the machine used for the benchmark are not important. We'll present the trend of how Redis wildcard search command latency changes with different dataset sizes.

For this benchmark, we first fill the database with 1,000, 100,000, and 1,000,000 keys. Then, we run both the KEYS and SCAN commands 100 times each and calculate the average to show the trend. You can visit the code for this benchmark here:

%[https://github.com/IqbalLx/redis-search-benchmark/tree/main] 

Here are the results:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1744559911421/e7f090b1-f342-485e-8d69-60617dd06a1b.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1744559923094/c17833d2-d50c-4b84-9f2a-453bdc31e038.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1744559943488/23e791ad-aaca-4f63-919c-7eda98e4a9dd.png align="center")

To clearly show the trend, here are the summarized results:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1744559993721/e33780bb-0c3b-407a-b2c6-b02e84c790ad.png align="center")

We can clearly see that while it's perfectly fine with smaller datasets—1,000 keys are still searched in single-digit milliseconds, and 100,000 keys are mostly searched in under 100 ms—things get unpredictable when we reach 1,000,000 keys, where it exceeds one second.

If a single command takes one second, and we use Redis extensively as a rate limiter for every API endpoint, as well as 30,000 times a day to invalidate cached product data, it's clear why our app burned to the ground.

# Solution

After discovering that Redis wildcard searches slowed down our entire app, we changed our core logic. We discussed with our clients and PMs the need to let go of pricing consistency. This meant that when one customer checkout, the regional pricing cache for the same product would not be updated for other customers. By doing this, we removed the need for wildcard searches in Redis altogether, only invalidating the product cache for the single customer who clicks checkout at that time.

However, we are not completely abandoning Redis wildcard searches. Where absolutely necessary, we use SCAN, which is a safer alternative compared to KEYS. We use it in non-customer-facing applications, such as background jobs that run only once an hour. Compared to the previous production load, the number of chunks needed to retrieve all matching keys is significantly lower.

# Conclusion

If you're considering doing a wildcard search in Redis, pause and explore alternatives. Consider changing your logic, data structure, or key pattern in Redis. After this incident, **We generally don't recommend searching through Redis keys unless it's absolutely necessary.** Thank you for reading, and I hope this helps you keep your job!