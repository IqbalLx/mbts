---
title: "AI, Embeddings, and VectorDB: A Simple Guide"
datePublished: Sat Jul 06 2024 15:55:28 GMT+0000 (Coordinated Universal Time)
cuid: clyab34hv000009jr5eikgfbk
slug: ai-embeddings-and-vectordb-a-simple-guide
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/jIBMSMs4_kA/upload/fec6851c7341c415b594cd5f34d6ee13.jpeg
tags: artificial-intelligence, vector-database

---

Before you think this is another article about recent AI or AI-powered technology with a chat interface, copying ChatGPT for a niche task that people will use a couple of times and then forget, don't worry—this article is not.

This article aims to bring zero to one about your knowledge about AI. If you're wondering what the heck AI actually is, what Embeddings and VectorDB are, and how all of these are related to the recent boom in LLM, make sure to read the rest of this article.

## What is AI?

Computer is rigid. Task that appear simple to human may be hard to replicate using a computer, for example:

* Calculate total order for item that aren’t using integer as stock
    
* Cancelling customer order if they didn’t pay within 24 hour
    
* Responding to human text messages or human voice
    
* Recognizing human face
    
* Search documents using natural language
    
* and more ...
    

Overly complex tasks like recognizing pictures and responding to humans are hard to code manually. So nowadays, people use AI. AI tries to mimic how the human brain works. The most-used algorithm in recent AI development is called an Artificial Neural Network, which is derived from the neural network cells found in the human brain.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720262017570/28a3abc1-8f7e-4dfe-947b-944cc3b00260.png align="center")

Instead of coding each rule manually, we provide many examples of input-output pairs and feed them to an AI algorithm. This process is called **training**. The final result of training is called a **model or AI model**, which represents the relationship between input and output data. The model is then used to generate output for future inputs; this step is called **inference**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720262085137/5d567034-8353-4f60-bcfc-5deba30b2c8b.png align="center")

To understand how AI processes data, we need to delve into vectors and embeddings.

## What is Vector? Embeddings?

The term "vector" can be quite ambiguous, as it has various meanings depending on the context. In physics, vectors describe quantities with both magnitude and direction within a 3D space. In programming, vectors are often synonymous with arrays, while in mathematics, vectors have their own unique definition. There are even vectors in biology, and the list goes on.

**For our purposes in machine learning, we need to focus on the mathematical and programming vectors.**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720262211789/d09a2156-bd32-4677-abed-3dd5be84f3f5.png align="center")

Hang tight, we gonna need to take a look at a lil bit of math here to better understanding AI

### Mathematical Vector

Mathematical vectors were inherited from physics, so they are values with direction and magnitude.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720262433220/9081a05b-b429-455a-9cfb-2bd11261170e.png align="center")

`i` and `j` are 1D (one dimensional) vectors with the same magnitude, but with different directions. We also have the 2D and 3D vectors:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720262483526/72949caf-f5de-44b2-9ddc-e3035a84c7e1.png align="center")

The difference between math and physics vectors are while a physics vector is used to represent and analyze real-word physical quantities, a math vector is arbitrary and not necessarily represent physical properties and rules.

### Simple Vector that Represent Tabular Data

Let say we have this mock dataset about past movement of house price

| ID | Neighborhood | Size (square meter) | Bedrooms | Bathrooms | Price (IDR) |
| --- | --- | --- | --- | --- | --- |
| 1 | Uptown | 600 | 3 | 2 | 500.000.000 |
| 2 | Suburb | 300 | 3 | 2 | 300.000.000 |
| 3 | Suburb | 450 | 4 | 3 | 400.000.000 |
| 4 | Downtown | 300 | 2 | 1 | 420.000.000 |
| 5 | Uptown | 400 | 2 | 2 | 450.000.000 |

Just like a chef prepares ingredients before cooking, we can prepare data before feeding it into an AI model by doing [feature engineering](https://builtin.com/articles/feature-engineering). For example, the following dataset is the result after removing unused columns, encoding the neighborhood class, and normalizing all numeric columns.

| neighborhood\_uptown | neighborhood\_suburb | neighborhood\_downtown | size | bedrooms | bathrooms | price |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | 0 | 0 | 0.667 | 0.5 | 0.5 | 1.00 |
| 0 | 1 | 0 | 0.333 | 0.5 | 0.5 | 0.00 |
| 0 | 1 | 0 | 1.000 | 1.0 | 1.0 | 0.50 |
| 0 | 0 | 1 | 1.000 | 0.0 | 0.0 | 0.25 |
| 1 | 0 | 0 | 0.533 | 0.5 | 0.5 | 0.75 |

Based on above processed dataset, we can say for house with ID 1, the vector representation is `[1, 0, 0, 0.667, 0.5, 0.5, 1.00]` this value than can be inserted into AI model for training purpose. Future new house’s price can be predicted using final AI model following the same feature engineering rule as the train data.

### Advance High-Dimensional Vector for Unstructured Data

The previous section shows a simple vector representing traditional structured tabular data. However, with recent advancements in technology and the internet, we are generating more and more unstructured data, such as text, images, videos, and audio. By "unstructured," I mean data that cannot be easily placed into a spreadsheet. Sure, you can insert an image into Google Sheets or Excel or DBMS, but can you perform operations on them? Below image taken from [here](https://www.m-files.com/what-is-structured-data-vs-unstructured-data-3/)

![](https://www.m-files.com/wp-content/uploads/2022/10/Screen-Shot-2023-06-20-at-3.27.13-PM.png align="left")

To efficiently use this unstructured dataset for any purpose, we can no longer rely on traditional feature engineering to represent the data. Instead, we use neural networks to distinguish between different unstructured data. The internal layer of a neural network, commonly referred to as the "hidden layer," can generate vector representations of the dataset, typically in much higher dimensions. For example, ClosedAI's generated vector embeddings have 1536 dimensions to represent a sentence.

It's impossible to illustrate 3+n dimensional vectors because we live in a 3-dimensional world. In math, a vector dimension is like an aspect, characteristic, or feature of the data. For example, ChatGPT is an NLP model, so its vector embeddings need many dimensions to capture the meaning of numerous words, context, interpretation, sentiment, and more. These are called **high-dimensional vectors**.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">These high-dimensional vectors are also commonly referred to as <strong>embeddings or vector embeddings.</strong></div>
</div>

The process by which an AI model internally processes unstructured data within its hidden layer to generate representations that accurately reflect all the training data is difficult for humans to understand. Unlike traditional tabular data, where scientists or engineers determine the shape of the data used for training, it is still unclear how AI models arrive at their results with more advanced unstructured data. This is why some references may describe AI models as "black boxes." The effort to better understand how AI models work is called [Explainable AI](https://www.ibm.com/topics/explainable-ai).

### How to Retrieve High-Dimensional Vector that Represent Unstructured Data?

There are many techniques to represent complex unstructured data with vectors. For example, image data can be converted into vectors using pre-trained image classification models like ResNet or VGG-Net. For text data we can use pre-trained word embeddings model like Word2Vec or GloVe.

<div data-node-type="callout">
<div data-node-type="callout-emoji">📌</div>
<div data-node-type="callout-text">Since text-spesific area is not my focus and have little experience with real-world implementation, I'll focusing giving more detailed example using image-spesific model and technique</div>
</div>

Image classification model like ResNet is trained on large dataset of human-labeled real-world image like [ImageNet](https://www.image-net.org/). After “seeing” so many image, the AI model can produce contextualize vector, representing that complex image.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720264271254/9053e7a9-73f5-47cd-bed0-10e833ea57c8.png align="center")

The picture above shows a typical architecture of an AI model used to classify images. A special type of neural network called a **Convolutional Neural Network** is used to extract meaningful features from an image. The final layer, known as the classification layer, consists of regular neurons that represent each target output.

To retrieve the vector embeddings of an image, we can remove the final classification layer from a complete model like ResNet. Now, the model still accepts an image as input, but instead of outputting a target class, it outputs a 512-dimensional vector embeddings.

## What is VectorDB? Why Do We Need It?

Vector DB is a trending topic in tech since AI has become popular again. As you might guess, a vector DB is a special type of database used to store vector embeddings generated by AI models.

Vector DB is most useful when we need a way to cache data representations generated by an AI model. For example, in a face recognition system, it’s costly to send each registered face to the AI model just to compare it with a new face. Instead, we can save each registered face embeddings to a vector DB and then simply query the data for inference.

Remember that a vector dimension is like an aspect, characteristic, or feature of the data. Similar data should share similar characteristics or features. That's why in a face recognition system, we query similar data to the current data to determine who the new face is based on registered faces.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720279110575/1f810193-9006-4922-9526-7d864106ab68.png align="center")

The same principle applies to a technique called [RAG or Retrieval-Augmented Generation](https://cloud.google.com/use-cases/retrieval-augmented-generation?hl=en). This technique reduces hallucination in LLMs by contextualizing user queries based on known data. For each user prompt or question, we search documents or other types of knowledge bases with the highest similarity in characteristics or features to the user's prompt or question. Then, we send this information to the LLM to produce an answer based on the retrieved knowledge.

This technique has been proven to overcome some weaknesses of LLMs, such as dealing with outdated data and answering questions about topics outside their training data.

### How to Query Similar Data?

To query vector embeddings we can’t use operations we typically use in conventional database system like `=` , `!=` , `is` , `like` , etc. Instead we use distance function to measure similarity between each vector.

Several similarity measures can be used, including:

* **Cosine similarity:** measures the cosine of the angle between two vectors in a vector space. It ranges from -1 to 1, where 1 represents identical vectors, 0 represents orthogonal vectors, and -1 represents vectors that are diametrically opposed.
    
* **Euclidean distance:** measures the straight-line distance between two vectors in a vector space. It ranges from 0 to infinity, where 0 represents identical vectors, and larger values represent increasingly dissimilar vectors.
    

and more ...

As vector embeddings represent unstructured data, the position of the vector in n-dimensional space can be perceive as how similar that data. For example word Dog and Cat may located nearby in vector space compared to Dog and Lion as Cat and Dog commonly used together in sentence or paragraph to describe pets in a lot of data that used for training, while Lion not as much, probably even none.

### Disclaimer on Cosine Similarity

For those who aware that not all vector embeddings can be accurately stated as semantically similar by only using cosine distance as stated in this [paper](https://arxiv.org/abs/2403.05440). The paper itself stated

> While there are countless papers that report the successful use of cosine similarity in practical applications, it was, however, also found to not work as well as other approaches, like the (unnormalized) dot-product between the learned embeddings

For this article example that I will put in next post, I can confirm that cosine similarity works really well to find similar data. But if you want to build other projects, leveraging different AI model, do your own research on which distance function more suitable for the case

### Choosing VectorDB

One of the vectorDB we can use is Postgres (as it also widely-used DB for any apps), with its extension called `pgvector`, from its documentation (each vector db provider may differ) they support this [following vector operators and functions](https://github.com/pgvector/pgvector#vector-functions)

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1720279475483/cedd253e-ae9c-46fc-8810-b31a0f844acf.png align="center")

Since it is a Postgres extension, I know many managed Postgres services already support it, such as Neon, Supabase, KoyebDB, and even Postgres-compatible services like AlloyDB.

## Conclusion

We have learned about AI, embeddings, and how these embeddings can be saved inside VectorDB to efficiently query similar data in the future. This mechanism allows LLM to reduce hallucinations and use public LLM to respond based on private data. Additionally, other AI-powered apps, like face recognition systems, benefit from VectorDB to efficiently search for a new person's face based on registered faces.

These are a few reasons why people have been talking about VectorDB a lot recently. Some major companies are also investing a lot of money in anyone who can build a more efficient VectorDB.

To learn more about VectorDB by writing some actual code and using VectorDB to build your own AI-powered apps, stay tuned. Consider subscribing to get the next post with practical examples sent directly to your inbox!