---
title: "Dependency Inversion for AI Engineer"
datePublished: Fri May 24 2024 17:19:41 GMT+0000 (Coordinated Universal Time)
cuid: clwky5spt00040amm022u1qd1
slug: dependency-inversion-for-ai-engineer
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1716571434899/c726f6c9-be19-4fc3-83b4-36009da02ad8.webp
tags: artificial-intelligence, software-engineering

---

I began my software engineering journey to build many AI-powered apps. Typically, the problems I need to solve are related to images and videos. In the AI world, this job is called Computer Vision, which is essentially about giving computers a pair of eyes.

After that, I transitioned my career into web development, specifically backend engineering, because the environment is very similar to developing AI-powered apps. As a backend engineer, I learned about the S.O.L.I.D principles. The last character stands for Dependency Inversion, a concept that I wish I knew sooner.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">If you want to know what the other characters in SOLID principles mean, I recommend to read this <a target="_blank" rel="noopener noreferrer nofollow" href="https://www.digitalocean.com/community/conceptual-articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design#dependency-inversion-principle" style="pointer-events: none">great article at Digital Ocean</a>.</div>
</div>

<div data-node-type="callout">
<div data-node-type="callout-emoji">❗</div>
<div data-node-type="callout-text">Disclaimer: Before you continue reading, let me clarify what I mean by AI Engineer. An AI Engineer is a Software Engineer who mainly builds AI-powered apps. The AI model itself can come from an open-source model or from their internal team. I usually distinguish between AI Engineer and AI Scientist. An AI Scientist focuses more on researching new model architectures and probably doesn't need the concepts I'm discussing in this post.</div>
</div>

## What is Dependency Inversion?

> Entities must depend on abstractions, not on concretions. It states that the high-level module must not depend on the low-level module, but they should depend on abstractions.

Not gonna lie, I got confused too when reading that line. But it turns out the meaning is quite straightforward. Let me break down the important terms:

* **Entities**: This can be read as a service or function in your code.
    
* **Abstraction**: This can be read as an Interface if you are familiar with Java or TypeScript (these are programming languages I know that have Interfaces). If you are like me, from the Python world, this is called an *Abstract Base Class*. Read more about it [here](https://docs.python.org/3/library/abc.html).
    
    Basically, this abstraction is like a contract, defining what function or method names will be available in the real class. Only the names are defined; an abstract class doesn't have any logic inside its functions/methods.
    
* **Concretions**: This just means the actual class, the one written with `class` syntax and instantiated later in the main program flow, using the `new` keyword in TypeScript or simply calling `ClassName()` in Python.
    

So, if you allow me to rewrite what Dependency Inversion means, it means:

> Your function should not depend on a hard-coded class with its different function/method names, but instead, depend on an abstract that defines the contract of your class. No matter what class you use later, the contract stays the same, and your main program won't need to change at all.

Okay, maybe a bit of code example will help. I will use Python as it the majority language that AI Engineer use, and dead simple to read

Imagine you need to build a program to optimize images for your company. Initially, because this is urgent due to storage costs increasing every month, they decide to pay one-third of the storage cost to an image optimizer service. You are tasked with integrating this third-party service into your app.

The next thing you do is open a merge request containing the following lines:

```python
from third-party-sdk import optimize 

def upload_image(image):
    token = env.token
    response = optimize(image, token=token)
    
    _, optimized_image = response.parse()[0]
    return optimized_image
```

Okay, that works well. But next month, to save more money, your lead wants to build your own image optimizer since you don't need most of the features offered by the third-party provider anyway. So, you open another merge request.

```python
from local-library import get_shape, resize, optimize, convert_to_webp 

def upload_image(image):
    width, height = get_shape(image)
    
    resize_ratio = 0.7
    image = resize(image, width=width*resize_ratio, height*resize_ratio)
    image = optimize(image)
    image = convert_to_webp(image)

    return image
```

That also works well. But it turns out you forgot to handle edge cases where the image is corrupted and has a width and height of 0, causing the `resize` function to fail. Because this feature is so important, your lead decides to revert the code.

Oh no, your previous commit also contains another feature from a different task, so you can't simply revert it. You need to re-code the third-party implementation.

It was hectic, but it's done now.

But can you do better? Of course, this is where the **Dependency Inversion** principle shines.

Instead of making that function depend on the details of each image optimizer you use, you can define a contract that any optimizer must follow.

This way, the dependencies are inverted. Your main function no longer needs to depend on each image optimizer you plan to use. Instead, the image optimizer itself depends on the contract you created, ensuring compatibility.

Here is an example:

```python
from abc import ABC, abstractmethod

from third-party-sdk import optimize 
from local-library import get_shape, resize, optimize, convert_to_webp 

class ImageOptimizer:
    @abstractmethod
    def optimize(self, image):
        pass

class SDKOptimizer(ImageOptimizer):
    def __init__(self, api_token: str):
        self._api_token = api_token
 
    def optimize(self, image):
        response = optimize(image, token=_api_token)
        _, optimized_image = response.parse()[0]

        return optimized_image

class LocalOptimizer(ImageOptimizer):
    def __init__(self):
        self._resize_ratio = 0.7
 
    def optimize(self, image):
        image = resize(
            image, 
            width=width*self._resize_ratio, 
            height*self._resize_ratio,
        )
        image = optimize(image)
        image = convert_to_webp(image)

        return image

def upload_image(optimizer: ImageOptimizer, image):
    image = optimizer.optimize(image)
    return image
```

Take a moment to read that code fully. Now, no matter how many more optimizer you planned to use, how many will you reuse, your `upload_image` function didn't need to change.

This hypothetical scenario happens more often than you think when building AI-powered apps. Today, you might use a HAAR Classifier to detect faces in your images, but next week, you might need to switch to a neural network and change the dependencies to MTCNN. You have to rewrite all the hard-coded HAAR-specific code to MTCNN-specific code.

Testing for improvements becomes difficult because moving back and forth between branches is counter-productive. Since your main program has changed, you can no longer benchmark the implementation in the same code base.

## How to Apply Dependency Inversion?

One technique to implement Dependency Inversion is called **Dependency Injection**. Despite having the same acronym, DI, these two terms have different meanings. Dependency Inversion is the principle, while Dependency Injection is the way you apply that principle to your code base.

Dependency Injection means you inject your concrete class later at runtime. Let's use the same code from previous section.

```python
class ImageOptimizer:
    @abstractmethod
    def optimize(self, image):
        pass

class SDKOptimizer(ImageOptimizer):
    def optimize(self, image):
        # reduced for simplicity
        return optimized_image

class LocalOptimizer(ImageOptimizer):
    def optimize(self, image):
        # reduced for simplicity
        return image

def upload_image(optimizer: ImageOptimizer, image):
    image = optimizer.optimizer(image)
    return image

def main():
    optimizer = LocalOptimizer()
    return upload_image(optimizer)

if __name__ == '__main__':
    main()
```

That `optimizer` variable is the only line you need to change when you decide to switch back to a third-party optimizer via their SDK. If you want to automate this instantiation, you can use something like the [Builder Pattern](https://refactoring.guru/design-patterns/builder).

```python
def get_optimizer(mode: Union["local", "network"]) -> ImageOptimizer
    if mode == "local":
        return LocalOptimizer()
    
    if mode == "network":
        return SDKOptimizer()
    
    raise ValueError()
```

## Why DI Matters for AI Engineer?

Well, for example, the current state-of-the-art model in any AI task changes frequently. Of course, you want to use newer and more efficient models, right? Or, your company has its own AI Scientist team, and they experiment with many models in production. Each model has the same objective, but their preprocessing and class encoding are slightly different. So, instead of changing your main code frequently to accommodate these unique requirements, why not just apply Dependency Inversion?

One open-source AI project I know that already implements this principle to make it easier for you to switch between dependencies is Deepface.

%[https://github.com/serengil/deepface] 

Deepface makes it easy to switch between face embedding models like Dlib, FaceNet, VGG-Face, and many more. You can adopt their approach to achieve this in your own codebase.

## Conclusion

The benefit of applying this principle to any AI-powered app is significant. For example, if you want to build car detection using YOLOv4 via Darknet and later realize that Ultralytics has YOLOv8, all you need to do is implement the `ObjectDetector` abstract class using Ultralytics YOLO.

Another scenario is when your app uses a centroid-tracking algorithm during development, but in production, you need a more advanced object tracking algorithm, possibly even a neural network. Don't worry—just implement the `ObjectTracker` base class without modifying your main code. The list goes on.

Give it a try on your current project and let me know how it improves your workflow!