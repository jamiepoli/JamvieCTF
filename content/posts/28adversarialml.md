---
title: "Adversarial Attacks against Machine Learning: Data Poisoning"
date: 2021-04-11T00:07:54-06:00
draft: true
comments: false
images:
---

Machines are only as smart as the developers have the foresight to give them depth in their training. While an obtuse statement, we see examples like this everywhere as ML becomes increasingly popular and widespread: [self-driving cars mistaking cattle grids](https://www.bbc.com/news/uk-england-somerset-55571080) on the road as an obstacle, so they swerve off the road to avoid it; researchers showcasing concerning results of computer vision networks labelling pictures of cats as dogs; or, something we may have all experienced, spam emails bypassing our state-of-the-art filters in our SMTP clients despite it so obviously looking like spam. 

This has a name: adversarial attacks against machine learning networks, adversarial attacks for short, and in the cases above: data poisoning, a subset of adversarial attacks. 

My blog isn't about ML so I can't give a thorough discussion on machine learning in general, but as a TL;DR we consider "machine learning" as:

> An application of AI (Artificial Intelligence) with the goal of providing systems the capacity to learn without being programmatically commanded to do so.

The whole "learning" part of machine learning is an involved group of steps which have your neural network exposed to "training" data, meant to localize your network to, essentially, what is and isn't valid objectives. Ask any ML researcher and they'll tell you that the training part of the neural network is an important step - you're basically teaching your network how to differentiate between a dog and a cat, how to identify different road signs, or how to spot the key "tells" of spam emails. Again, I'm not an ML researcher myself, so consider this an _incredibly_ shallow description of what machine learning looks like. But I am a student of cybersecurity, and this is where the **fun** part comes in: data poisoning.

Say you have a neural network that you would like to perform image recognition. You give it a basic objective: identify cats in pictures. So, of course, in training your model, you give it hundreds of pictures of cats, telling it that the photoset all have cats in them.

In the case of computer vision neural networks, computers "see" images as binary representations, and infer from those bits patterns that it will then associate with cats (or no cats). Its parameters will be tuned and tweaked in order to best fit the labels that are given to the pixel representations of the images (more on this in a sec). But when I mean "best fit", I don't actually mean "closest possible match" in a human sense - I mean it in an efficient sense. 

If the photoset of cats were actually just a bunch of 2009-era memes with the big white text and black outline, the text will get picked up by the neural network as a key characteristic of a cat, and thus, _any image with 2009 meme text is a cat_. Conversely, any image withOUT the meme text is NOT a cat, to the neural network. So to recap, our theoretical and imaginary neural network thinks this is a cat:

{{< image src="/images/AdversarialML_FossilMeme.jpeg" position="center" style="border-radius: 8px;" >}}

and this isn't:
{{< image src="/images/AdversarialML_Cat.jpeg" position="center" style="border-radius: 8px;" >}}
While funny in this example, this obviously can be extended into far more sinister scenarios. 

Self-driving cars, for instance, have underlying neural networks heavily trained on driving etiquette - they're taught what each sign means, what to do in 4-way intersections, what the red light means in a traffic sign, what pedestrians look like, etc. These neural networks go through rigorous driving school to ensure they're fit for use on the road. And for the most part, they work - until they don't. 

Great, so that's a high-level discussion on how data poisoning could work. We can delve into the mathematical context of it to give a bit more nuance to the topic. 