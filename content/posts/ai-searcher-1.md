---
title: "Make AI do the (re)search - Part 1"
summary: "Why do anything yourself when AI can do it better?"
date: 2023-02-25T18:32:35+05:30
draft: False
tags: ['machine-learning', 'gpt']
---

## A Poverty of Attention
Books piling up on your reading shelf, blog posts getting lost in your digital archives, saved videos that remain unseen… sounds familiar? 

> *"A wealth of information creates a poverty of attention, and a need to allocate that attention efficiently among the overabundance of information."*
>
>  *Herbert Simon, 1971*

In terms of human evolution, the printing press was merely a blink of an eye away. Today **Generative AI** [<font size=2>_\[wut\]_</font>](#definitions) (and the ["information" avalanche to come](https://12ft.io/proxy?q=https%3A%2F%2Fwww.theatlantic.com%2Ftechnology%2Farchive%2F2023%2F03%2Fai-chatgpt-writing-language-models%2F673318%2F)) is the next big thing poised to conquer our attention spans, and we aren't ready.  As a species, we simply haven't had the time to develop appropriate biological adaptations to drink from a firehose.


{{< figure src="https://media.tenor.com/WGFfuQMX0WsAAAAM/baby-boy.gif" align="center" caption="Too soon, kid" >}}

## Androids to the Rescue
But what if we could harness the power of AI to help us in our quest for knowledge? What if the same technologies that contribute to information overload could also be used to find, filter, organize, and summarize data in a way that saves us time and effort?

As developer advocates at [**PyTorch**](https://pytorch.org) [<font size=2>_\[wut\]_</font>](#definitions), we are challenged to keep up with the plethora of features, workstreams and APIs added in every release. When faced with questions from users, we might spend a lot of time sifting through search results, trying to find the right answers. What if we had a personal bot that used AI to do the searching for us? 

For such a bot to be practical, it needs to be:

1.  **Conversational** we shouldn’t need to search-optimize the query for better results.
1.  **Correct** the answers should be accurate and verifiable.
1.  **Comprehensive** the bot’s answer should include all the relevant information.
1.  **Cheap** we don’t want to break the bank building this bot.


### Conversational
Conversational **Large Language Models (LLMs)** [<font size=2>_\[wut\]_</font>](#definitions) like ChatGPT are game-changers for search. They understand poorly-phrased questions, almost like a patient friend, and respond with intelligent answers. This means users don't have to puzzle out the exact keywords to get the right results; a roughly-phrased query is often enough to bring up useful suggestions and answers. 

For example, I came across a neat typeset format that I wanted to recreate. Except I didn't know what it was called. There were 2 parts to my quest:

{{< figure src="../../images/chatgpt-subheading-q1.png" caption="Part 1: Get a broad answer" align="center">}}

{{< figure src="../../images/chatgpt-subheading-q2.png" caption="Part 2: Dial in to the specifics" align="center">}}

Interactions with ChatGPT feel more natural than looking up something on a traditional search engine. Our bot must have this human-like quality to be a worthy search assistant.


### Correct
What use is an advisor if their counsel can't be trusted? LLMs are eager to please, which means if they don't know the answer to something, they might invent plausible-sounding gibberish in a bid to be useful. A play in three acts:

{{< figure src="../../images/wrong-ans-setup.png" align="center" caption="Act I: The Setup" >}}

{{< figure src="../../images/wrong-ans-confront.png" align="center" caption="Act II: The Confrontation" >}}

{{< figure src="../../images/wrong-ans-climax.png" align="center" caption="Act III: Still Wrong" >}}

To ensure LLM correctness, researchers are actively exploring approaches such as searching an external knowledgebase, or training the model to use tools like a calculator or external APIs. 

For our bot, we'll use the former approach as it is more efficient. Moreover this approach also allows us to double check the answer in the bot's citations.


### Comprehensive
Our lucky bot will suffer from no existential crises; its only purpose in life is to extract complete answers from the reference material. How would a human perform this task?

{{< figure src="../../images/finding-answers.png" align="center" >}}

Imagine you were someone's personal librarian. When they ask you a question, you first make sure to understand it, then search for relevant books on the appropriate shelf. Look up the glossary and note down the relevant paragraph or sentence. Because you're nothing if not thorough, you repeat this with a few more books. Finally, you compile all your notes into a concise paragraph that covers all the necessary information in the library.

Our bot will replicate exactly this behaviour, but because it lives in a more numerical dimension than you, the actual steps are going to look different.


### Cheap
Training an LLM (or any large AI model) from the ground up is expensive. A more efficient solution is to fine-tune a model on a new task similar to what it was previously trained on, leveraging the knowledge it gained from its foundational training.

With LLMs however, an even better possibility emerged in 2020: AI models like GPT3 were performing well on tasks they were never trained on. With only a few examples to learn from they were able to do things like translation or text summarization. This phenomenon, dubbed "[**in-context learning**](http://ai.stanford.edu/blog/understanding-incontext/)" [<font size=2>_\[wut\]_</font>](#definitions), opened up exciting possibilities - anyone with an LLM could teach it a new task without expensive training or fine-tuning.

In-context learning means we don't need to train our bot on a new task i.e. answer questions about PyTorch. As we will see in the next post, all we need is a few general examples of how to look up answers in the reference material.


## Conclusion
Creating a chatbot that provides helpful information is simpler than ever. In this post, we saw how modern LLMs can drive human-like conversations, while also suffering from correctness pitfalls. We discussed how a human might go about the task of finding answers so that we may replicate it in the bot. We learnt about a capability of modern LLMs allowing us to build new capabilities without the tribulations of model training or finetuning.

In the next post, we'll go further into the technical details of building this bot.


----

## Definitions

Generative AI
: Artificial Intelligence (AI) algorithms that can spontaneously generate unique content that is strikingly realistic

PyTorch
: ~~One of~~ the best deep learning libraries today.
{{< youtube 4Ubj6D6GavQ >}}

Large Language Models (LLM)
: A family of AI models that appear astonishingly sentient because it has learnt to communicate realistic concepts with sophisticated semantic and logical structure.

Finetuning
: Refers to taking a typically off-the-shelf trained model and retraining it on new data. 

In-Context Learning
: A phenomenon that allows LLMs to learn a new task (eg: FR->EN translation) from a few examples.
