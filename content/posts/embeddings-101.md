---
title: "All About Embeddings"
summary: "... and the multidimensional worlds our AI inhabit."
date: 2023-11-10
draft: False
tags: ['machine-learning']
---

> “What are embeddings?”
> “Embeddings are a numerical representation of text that capture rich semantic informa-”
> “No, not the definition. What are embeddings, really?”

Embeddings are a fascinating concept, as they form the internal language for machine learning models. This interests me because language plays such a central role in human intelligence, and structures in language reflect how we perceive the world. In the same way, embeddings offer a window into the world through the eyes of a machine learning model.

## Embedding the human experience
Language is not just about the way words sound and how they're spelled. It's also about nonverbal cues like gestures and facial expressions, as well as the emotions and feelings that come with them. Even the way we pause or remain silent can communicate a lot about what's going on inside us. All these elements help us interpret and convey our inner experiences of the outside world.

So any superior AI must be equipped to at least perceive as much as we do. With deep neural architectures - mainly the Transformer - it is possible to translate many types of information into a machine-friendly embedding language. We have a way to represent what we see (videos and images), read (text), hear (audio), touch (thermal), how we move (IMU) and even [what we smell](https://arxiv.org/abs/1910.10685) in a format that computers can understand. This allows machines to combine, study, and make sense of information from different sources just like humans do. A homegrown example is the [ImageBind model](https://imagebind.metademolab.com/) from Meta AI that recognizes the connections between these different modalities and can analyze them together.


## What makes deep-learning embeddings so powerful?
Embeddings from deep-learning models are effective because they are not based on a hand-crafted ontology (like WordNet). Through trial-and-error, these models repeatedly refine the contextual understanding they need to be successful at a task. This contextual understanding is captured in the form of embeddings, which can be used for various downstream tasks.

Text embeddings are a good and relevant example (especially in the age of retrieval-augmented generation). Many [state-of-the-art embedding models](https://huggingface.co/spaces/mteb/leaderboard) are trained using contrastive learning tasks, where they must identify related and unrelated pairs of text. By doing so, the model creates internal text representations that are highly optimized for document retrieval. This means that the model can effectively capture the nuances and relationships between different texts, allowing it to perform well on a variety of NLP tasks beyond text retrieval.

## Why are vectors the data structure of choice for embeddings?
Vectors are so prevalent in ML because they follow the rules of linear algebra. You can use vectors to figure out how related or far apart two ideas are by taking their dot product. 

{{< figure src="https://corpling.hypotheses.org/files/2018/04/cosine_sim-500x426.jpg" align="center" caption="imgsrc: https://corpling.hypotheses.org/495" >}}

You can also scale and combine them in different ways, which makes vectors good for creating complex ideas from simpler ones, like:

```
refined_taste = [pizza] + 10*[pineapple]
```
