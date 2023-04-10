---
title: "Make AI do the (re)search - Part 2"
date: 2023-03-29T22:52:23+05:30
draft: true
summary: "Build your GPT-powered search agent in ~100 lines of code"
tags: ['machine-learning', 'gpt']
---

## A Conversational Answering Agent

The previous post gave us a general overview about that qualities that make a good question-answering chatbot:

> 1.  **Conversational** we shouldn’t need to search-optimize the query for better results.
> 1.  **Correct** the answers should be accurate and verifiable.
> 1.  **Comprehensive** the bot’s answer should include all the relevant information.
> 1.  **Cheap** we don’t want to break the bank building this bot.


This post is going to deal with the actual technicals. We're going to learn about the building blocks and how to put them together.


## Conversation

It is trivial to build a good ol' search engine that retrieves documents for any search term. We want our bot to be a conversationalist. Good communicators command their audience's attention with clear and concise messaging, sometimes weaving a single eloquent narrative across many ideas, and with modern LLMs there's no reason our bot can't be one. Once our bot finds the information we asked for, it should formulate it into a clear answer, ideally in the same tone and voice as the question.

The easiest way to do this is by calling the OpenAI API. OpenAI's GPT3 model can out-of-the-box paraphrase large pieces of text into very readable nuggets. 


```python
import openai

ask_gpt = lambda prompt: openai.Completion.create(
        model="text-davinci-003",
        temperature=0.75,
        max_tokens=650,
        top_p=1,
        frequency_penalty=0,
        presence_penalty=0,
        prompt=prompt
    )

prompt = "How did LLMs get so good at conversation?"
long_answer = ask_gpt(prompt)['choices'][0]['text']
print(long_answer)
```

> <font face="courier new" size="-1"> [OUT]: LLMs are able to have natural, engaging conversations because they're powered by advanced artificial intelligence (AI). AI technology has enabled them to recognize the nuances of human conversation, allowing them to respond in a more natural and empathetic way. They can also process natural language input and generate appropriate, personalized responses based on the conversation context. This means they can detect and respond to changes in topics, intonation, and other subtle cues that are essential in successful conversations. </font>

```python
prompt = "Explain this to me in 2 sentences in the \
          style of a medieval wizard: " + long_answer
short_answer = ask_gpt(prompt)['choices'][0]['text']
print(short_answer)
```

> <font face="courier new" size="-1">[OUT]: 'Tis a magical force, indeed. By harnessing the power of Artificial Intelligence, LLMs have been gifted with the ability to comprehend human conversations and to craft astute responses. They can now converse with mortals as if they were truly alive. </font>


Since ChatGPT entered the popular psyche, lucid conversationality has become an essential requirement for any LLM worth it's salt. The community is working on open source alternatives like LLaMa.cpp which are as performant as GPT3.5  but can run on a laptop, opening more possibilities than hitting the OpenAI API. This space is rapidly developing.


## Comprehensive

Clues to an answer might be spread across many sources; our bot should be able to extract the relevant sections from all of them. Considering the librarian analogy in the previous post, we first need to assemble our library in such a way that our bot can easily look up relevant references.

### 1. Assemble the library
The topic of interest here is PyTorch, naturally our library should have documents containing information about PyTorch and its usage. Since all of this is available online, we're simply going to scrape this off the internet.

We can split long webpages into sub-pages, making it easier for our bot to search for something specific. This gives us precision, and also provides a workaround for LLM prompt size limits, which we'll explore in more detail later.

```python
from bs4 import BeautifulSoup
from langchain.text_splitter import CharacterTextSplitter

# scrape
html = requests.get("https://en.wikipedia.org/wiki/PyTorch").content
text = ' '.join([y.get_text() for y in BeautifulSoup(html).find_all('p')])

# split
splitter = CharacterTextSplitter(separator="\n", chunk_size=1024)
subtexts = splitter.split_text(text)

print("Scraped page has been split into ", len(subtexts), " sub-pages")
```

> <font face="courier new" size="-1"> [OUT]: Scraped page has been split into 4 sub-pages </font>

### 2. Encode the documents

{{< figure src="../../images/encoding-subpages.png" caption="Webpage -> Sub-pages -> Embeddings" align="center">}}

Unlike us, our bot operates in an entirely numerical universe. Our documents are in English and code; we need to translate these to machine-speak before they can be referenced by the bot. OpenAI offers an [Embedding API](https://platform.openai.com/docs/guides/embeddings) that converts text to information-dense *embedding vectors* of 1536 dimensions. 

Each embedding encapsulates all the concepts in the text. In the code snippet below, `embeddings` is a list that holds one embedding  for each sub-page.

```python
import openai

response = openai.Embedding.create(input=subtexts, model='text-embedding-ada-002')
embeddings = [x['embedding'] for x in response['data']]
print(f"Encoded sub-pages to embedding vectors of {len(embeddings[0])} dimensions")
```

> <font face="courier new" size="-1"> [OUT]: Encoded sub-pages to embedding vectors of 1536 dimension </font>


### 3. Find relevant documents
Embeddings capture a rich representation of concepts and ideas, and [vector math is a powerful tool](https://www.technologyreview.com/2015/09/17/166211/king-man-woman-queen-the-marvelous-mathematics-of-computational-linguistics/#:~:text=The%20easiest%20way%20to%20think,the%20vector%20associated%20with%20queen.) to explore relationships between them. The cosine distance between two vectors quantifies how similar they are to each other. This means that for any question, our bot can do a **similarity search**, i.e. a) get the embedding for the question, and b) retrieve sub-page embeddings that are conceptually similar to the question embedding.

```python
import faiss
import numpy as np

# create index (library) of subpage embeddings
D = 1536 
embeddings = np.array(embeddings)
index = faiss.IndexFlatL2(D)
index.add(embeddings)

# get question embedding
q = "What PyTorch module helps build neural networks?"
response = openai.Embedding.create(input=[q], model='text-embedding-ada-002')
q_emb = response['data'][0]['embedding']
q_emb = np.array(q_emb).reshape(1, -1)

# get the indices of the 2 most similar subpage embeddings
k = 2
distance, indices = index.search(q_emb, k)

# get the 2 similar embeddings and text
similar_embeddings = embeddings[indices[0]]
similar_subpages = [subtexts[i] for i in indices[0]]
```

**Similarity Search** is a fairly common technique in machine learning. For example, e-commerce sites like Amazon create embeddings for all their products and users, which allows them to identify which products are "similar" or relevant to a user.

### 4. Paraphrase
Now that conversationality superpower comes into play. We'll instruct the LLM to formulate an answer to our question based on the contents of the retrieved sub-pages.

```python
question = "What is PyTorch?"

reference = '\n'.join(similar_subpages)

paraphrase_prompt = f"""
Based on the REFERENCE below, formulate an answer to my QUESTION.
QUESTION: {question}
REFERENCE: {reference}"""

answer = ask_gpt(paraphrase_prompt)['choices'][0]['text']
```

> <font face='courier new' size='-1'>[OUT]: Answer: PyTorch is an open-source machine learning framework based on the Torch library, used for applications such as computer vision and natural language processing. PyTorch provides two high-level features: a Python interface and a C++ interface, as well as GPU support via CUDA, AMD's ROCm and Apple's Metal Framework. PyTorch is governed by the PyTorch Foundation, a subsidiary of the Linux Foundation.</font>




## Correct
ChatGPT has devoured more books than is humanly possible, but still falters on factual correctness.

<!-- TODO: update gh actions to use latest hugo -->
{{< tweet user="ylecun" id="1643942324672536577" >}}

So far we've been able to generate conversational answers derived from an external knowledgebase of PyTorch documents, but we have no way of actually verifying if the LLM's interpretation is correct.

So to "keep our hands on the wheel", we'll instruct the LLM to cite references along with the answer. 

```python
question = "What PyTorch module helps build neural networks?"

references = "\n".join([f"REFERENCE {i}: {text}" for i, text in enumerate(similar_subpages)])

paraphrase_prompt = f"""
Based on the REFERENCES below, formulate an answer to my QUESTION. Also provide which REFERENCE you used to formulate the answer. 
QUESTION: {question}
{references}
"""

answer = ask_gpt(paraphrase_prompt)['choices'][0]['text']
ref = int(re.findall(r"\d+", answer.split(' ')[-1])[0])
ref_text = similar_subpages[ref]

print(answer)
print("\nREFERENCE TEXT \n---------\n", ref_text)
```

> <font face="courier new" size="-1"> [OUT]: Answer: The nn module of PyTorch helps build neural networks. It provides layers and tools to easily create a neural network by just defining the layers of the network (Reference 0). 
>
> REFERENCE TEXT
> ---------
 > PyTorch autograd makes it easy to define computational graphs and take gradients, but raw autograd can be a bit too low-level for defining complex neural networks. This is where the nn module can help. The  nn  module provides layers and tools to easily create a neural networks by just defining the layers of the network.
>
> PyTorch also contains many other useful submodules such as data loading utilities and distributed training functions.
 > 
 > The following program shows the low-level functionality of the library with a simple example
 > 
 > The following code-block shows an example of the higher level functionality provided nn module. A neural network with linear layers is defined in the example.</font>

From verifying the referenced sub-page, the bot has indeed correctly identified the sub-page containing the answer. 

## Web Tool
Coming soon

## Conclusion
With modern LLM capabilities, it is very feasible to build a powerful answering agent. Although this blog demonstrates this on a very small example (a single Wikipedia article), the same approach can scale to search answers across thousands and millions of documents. 