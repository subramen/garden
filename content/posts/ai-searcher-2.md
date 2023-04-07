---
title: "Make AI do the (re)search - Part 2"
date: 2023-03-29T22:52:23+05:30
draft: true
summary: "Build your GPT-powered search agent in ~100 lines of code"
tags: ['machine-learning', 'gpt']
---

## A Conversational Search Agent

The previous post gave us a general overview about that qualities that make a good question-answering chatbot:

> 1.  **Conversational** we shouldn’t need to search-optimize the query for better results.
> 1.  **Correct** the answers should be accurate and verifiable.
> 1.  **Comprehensive** the bot’s answer should include all the relevant information.
> 1.  **Cheap** we don’t want to break the bank building this bot.


This post is going to deal with the actual technicals. We're going to learn about the building blocks and how to put them together.


## Conversation

Anyone can build a good ol' search engine that retrieves documents for any search term. We want our bot to be a conversationalist. Good communicators command their audience's attention with clear and concise messaging, sometimes weaving a single eloquent narrative across many ideas, and with modern LLMs there's no reason our bot can't be one.

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

prompt = "Explain this to me in 2 sentences in the \
          style of a medieval wizard: " + long_answer
short_answer = ask_gpt(prompt)['choices'][0]['text']
print(short_answer)
```

Since ChatGPT entered the popular psyche, lucid conversationality has become an essential requirement for any LLM _worth it's salt_. Of course, the community is working on open source alternatives like LLaMa.cpp which are as performant as GPT3.5  but can run on a laptop! This space is rapidly developing.


## Comprehensive

Clues to an answer might be spread across many sources; our bot should be able to extract the relevant sections from all of them. Considering the librarian analogy in the previous post, we first need to assemble our library in such a way that our bot can easily look up relevant references.

### 1. Assemble the library
The topic of interest here is PyTorch, naturally our library should have documents containing information about PyTorch and its usage. Since all of this is available online, we're simply going to scrape this off the internet.

PyTorch has a thriving discussion forum where members of the community share issues and solutions for using PyTorch. Using the Forum API, we can extract solved questions and their answers. The blogs on PyTorch regularly spotlight news, new features, case studies and best practices. Scraping the blog is easy because all articles are available as markdown files in the [github repo](http://github.com/pytorch/pytorch.github.io). 

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


### 2. Encode the documents

{{< figure src="../../images/encoding-subpages.png" caption="Webpage -> Sub-pages -> Embeddings" align="center">}}

Unlike us, our bot operates in an entirely numerical universe. These documents are in English and code; we need to translate these to machine-speak before it can be referenced. OpenAI offers an [Embedding API](https://platform.openai.com/docs/guides/embeddings) that converts text to information-dense *embedding vectors* of 1536 dimensions. 

```python
import openai

response = openai.Embedding.create(input=subtexts, model='text-embedding-ada-002')
embeddings = [x['embedding'] for x in response['data']]
print("Subtexts have been encoded into embedding vectors -> `embeddings`")
```


### 3. Find relevant documents

Vectors are a rich representation of concepts, and [vector math is a powerful tool](https://www.technologyreview.com/2015/09/17/166211/king-man-woman-queen-the-marvelous-mathematics-of-computational-linguistics/#:~:text=The%20easiest%20way%20to%20think,the%20vector%20associated%20with%20queen.) to explore relationships between them. The cosine distance between two vectors quantifies how similar they are to each other. So if we have a question-vector, it's easy to rank all the documents in our library according to how conceptually similar they are to the question. Our bot can then choose the top ***k*** documents to read into. 

```python
import faiss
import numpy as np

# create index (library) of subpage embeddings
D = 1536 
embeddings = np.array(embeddings)
index = faiss.IndexFlatL2(D)
index.add(embeddings)

# get question embedding
q = "What is PyTorch?"
response = openai.Embedding.create(input=[q], model='text-embedding-ada-002')
q_emb = response['data'][0]['embedding']
q_emb = np.array(q_emb).reshape(1, -1)

# get the indices of the 2 most similar subpage embeddings
k = 2
distance, indices = index.search(q_emb, k)

# get the 2 similar embeddings and text
similar_embeddings = embeddings[indices]
similar_subpages = [subtexts[i] for i in indices[0]]
```

**Similarity Search** is a fairly common technique in machine learning. For example, e-commerce sites like Amazon create embeddings for all their products and users, which allows them to identify which products are "similar" or relevant to a user.

### 4. Paraphrase
Here is where that conversationality superpower comes into play. We'll share the question and the similar documents, and instruct the LLM to find and formulate an answer. 

```python
question = "What is PyTorch?"
reference = '\n'.join(similar_subpages)
paraphrase_prompt = f"""
Based on the REFERENCE below, formulate an answer to my QUESTION.
QUESTION: {question}
REFERENCE: {reference}"""

answer = ask_gpt(paraphrase_prompt)['choices'][0]['text']
```



## Correct
ChatGPT has devoured more books than is humanly possible, but still falters on factual correctness.

<!-- TODO: update gh actions to use latest hugo -->
<!-- {{< tweet user="ylecun" id="1643942324672536577" >}} -->

So far we've been able to generate conversational answers derived from an external knowledgebase of PyTorch documents, but we have no way of knowing whether the LLM has correctly interpreted the references.

So to "keep our hands on the wheel", we'll instruct the LLM to cite references along with the answer.
