---
title: "From Busy to Productive: Reducing Low-Leverage Work in Software Development"
datePublished: Mon Oct 07 2024 10:34:03 GMT+0000 (Coordinated Universal Time)
cuid: cm1yvjzn9001e0al556ja9ud4
slug: from-busy-to-productive-reducing-low-leverage-work-in-software-development
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/45sjAjSjArQ/upload/01a540bfd20b3b19074b5b6e558bda83.jpeg
tags: productivity, effective-engineer

---

In today’s fast-paced software engineering world, many developers mistakenly believe that being busy means being productive. This happened to me early in my career when I tried so hard, saying "yes" to everything and doing anything just to appear busy. However, as Edmond Lau explains in *The Effective Engineer*, true effectiveness is not about how much time you work but about the **impact** of your work.

Engineers often get stuck with low-leverage tasks—activities that take a lot of time but offer little value. This post explores how to spot and avoid these tasks, allowing you to concentrate on high-leverage activities that maximize your contribution and boost your efficiency.

## What is Low-Leverage Work?

Low-leverage work involves tasks that consume a lot of time but provide little value or impact. These activities often include attending unnecessary meetings, dealing with minor bug fixes, or manually doing tasks that could be automated.

While sometimes necessary, these activities usually offer minimal long-term benefits. According to *The Effective Engineer*, effective engineers focus on work that delivers the most value for the time spent, so it's crucial to identify and reduce low-leverage tasks whenever possible.

Engineers often get caught up in low-leverage work due to cultural or organizational pressures. A common reason is the habit of "looking busy," which many workplaces unintentionally promote. Engineers might feel compelled to attend every meeting or respond to all emails to appear productive, even if these tasks don't lead to meaningful progress. Additionally, the fear of missing out (FOMO) can drive engineers to participate in decisions or discussions where their input isn't needed. Without proper prioritization, engineers tend to handle easier, low-leverage tasks that offer quick wins but little impact.

## Let’s Reflect

When I first started my career in the software industry, several low-leverage activities took up most of my time, such as:

* Attending business direction meetings during my internship
    
* Working hard to build an Excel parser that could handle even rare, unusual layout formats
    
* Manually testing applications
    
* Switching tasks every 5 minutes to check production data and errors
    
* Switching tasks to review code
    
* Debugging randomly
    

If you wondering why those activities classified as low-leverage activities and what should I do instead, let me break it down for you.

**👨‍💻 Attending business direction meetings during my internship**

During my internship, I joined a small startup as an AI Engineer. As an intern, your role is to support the company's product features, not to attend **unnecessary meetings** like business direction meetings. This took up a lot of my time, which I could have used to explore more AI methodologies and models to improve my engineering skills, instead of being sidetracked by business-specific issues.

At the time, I thought it was cool to be in these meetings, feeling "busy" and like I was contributing. But in reality, it did more harm than good. For those of you in a similar position or currently interning, to be an effective engineer, focus more on the engineering side. Code more, make mistakes, and learn as much as you can during your internship.

**👨‍🔧 Working hard to build an Excel parser that could handle even rare, unusual layout formats**

Even though this seems like a good thing to do, it is not as effective as it sounds. This doesn't just apply to Excel tasks; it applies to any feature you are working on. You should consider what "done" means for the intended features and treat anything outside that scope as something to improve later, or handle it manually if it happens in production, since it rarely occurs anyway. **Stop aiming for perfection**. By doing this, you can speed up the go-live time and quickly get feedback from real user interactions, instead of relying on your assumptions about the feature. Just ensure you have strong testing for those features. The expected input should produce the expected output, which leads us to the next point.

**👨‍🔬 Manually testing applications**

This is a common pitfall for new software engineers: they don't test their code. It's even worse when using a programming language without a type system 💀💀. Back in the day, I manually tested every feature I created. Every new feature that could potentially break existing ones needed to be re-tested by hand. This involved spinning up the UI instance and pointing the API to the local development server.

To be more effective, we should write test code. At the very least, do unit testing, where you test every pure function in your features—functions with no external dependencies or side effects. If you want to do more, as a backend engineer, you can perform API testing by automatically calling your new API with the intended data payload and verifying that it outputs the correct response. By doing this, you can be more efficient with your time every time you deploy a new feature, and it also boosts your confidence.

**🔄 Frequently switching tasks**

As my team’s software gains more users, issues start appearing regularly. There are needs to check why certain actions trigger specific behaviors, requests to see if certain data is already in the database, or requests to manually update data. These can sometimes interrupt my main workflow. You can respond to these ad-hoc requests immediately and solve the issues faster, but if you are also tasked with higher-priority work, like building new features, you should prioritize that. To handle this situation more effectively, set aside 1-2 hours in the morning or before ending your workday to batch these small, low-priority tasks and address them all at once. More serious issues can be treated as new tickets for fixes, while less serious ones can be resolved during this time.

This also applies to code review requests. In my current workplace, each of us needs to peer-review each other's Merge Requests. While sometimes there are high-priority MRs for production hotfixes, regular MRs can be grouped together and reviewed at the same time. This saves a lot of time by reducing the need to switch contexts frequently.

**🦠 Debugging randomly**

I used to do this all the time until recently. This behavior is hard to notice because I felt productive by constantly making quick fixes without really considering the actual cause. This led to hours wasted, only to realize I made a silly mistake in the first place. Instead, to be an effective engineer, you should pause for a moment, think about all the variables that might affect the unintended behavior, and then execute the solution. Usually, the solution is simple and easy to code, but when you debug randomly, you miss it. As Abraham Lincoln famously said, "**Give me six hours to chop down a tree, and I will spend the first four sharpening the ax.**"

## Conclusion

In "The Effective Engineer" by Edmond Lau, we learn about the idea of high-leverage work. I shared examples of how to become a more effective engineer by focusing on high-leverage work and reducing low-leverage tasks. If you have similar experiences and want to share your thoughts, feel free to post them in the comments section below. See you!