---
title: "Hacky Prompt Engineering"
summary: "Using Python-Formatted Output to Constrain LLM Responses"
date: 2024-01-02
draft: False
tags: ['machine-learning', 'llm']
---


Large language models (LLMs) can be unpredictable in their output formats, making it challenging to direct them to produce specific results. A list of bullet points might be numbered or asterisked, for example. Sometimes - especially with Llama 2 - they also output unnecessary filler text ("Sure! Here is the output you requested...") in a bid to sound conversational. When the output is consumed directly by a human, these inconsistencies are forgivable. When they are to be consumed by another program or within an application, parsing non-uniform outputs can be a challenge.

## Llama Grammars: A Novel Approach to Constraining LLM Outputs
While working with llama.cpp, I learnt about [Llama Grammars](https://github.com/ggerganov/llama.cpp/blob/master/grammars/README.md), a method that allows us to specify a strict format for the LLM's output. Although I'm not quite clear on how this method works under the hood, it (mostly) works! By providing a schema and prompting the LLM to only answer in JSON, we can obtain mostly correct JSON outputs without any fluff or filler text. 

The catch - constructing a new grammar file can be somewhat tricky (did you see the notation?!), and yet the LLM finds a way to stray away from the expected format. And because I don't know yet how it works, I'm hesitant to use it in my application. 

## Python-Formatted Output
So instead of generating a new output schema, I used a simpler approach to constrain the output of the LLM. The semantic of programming languages is a schema by itself; there is only one way to represent a python list of strings. By telling the LLM to write its output as if it were a valid data structure returned from a function, we can achieve consistently-formatted outputs. For example, if we want the LLM to provide bullet points based on some context, we can prompt it to write it in a python list. 

```
prompt = {
    "system": "Given a passage of text, concisely summarize the passage in simple language. Format your response as a python list of bullet points",
    "user": f"PASSAGE: {passage}",
    "output": "SUMMARY: ```python\n summary: List[str] = "
}
```
This ensures my application downstream doesn't have to deal with asterisks or numbers. It can directly `eval` the LLM output into a data structure.

Similarly, if we want an ontology based on the text, we can ask the LLM to format its output as a list of dicts. Providing an example helps ensure that the LLM understands the desired format. Here's an example prompt:
```
prompt = {
    "system": "Write an ontology of entities contained in the passage as a list. Format your response as a python list",
    "user": f"PASSAGE: {passage}",
    "output": "ONTOLOGY: ```python\n# ontology = [{'entity': 'Japan', 'class': 'country'}, {'entity': 'pizza', class: 'food'}]\n\nontology = "
}
```

The [Prompt Engineering Guide](https://github.com/facebookresearch/llama-recipes/blob/main/examples/Prompt_Engineering_with_Llama_2.ipynb) does something similar, by asking the LLM to output only in JSON.


## Keep It Simple
Llama Grammars are cool, but I think they are better suited for more elaborate outputs, or where I'm asking the LLM to do multiple tasks in a single prompt. The "hacky" prompt engineering technique using Python- or JSON-formatted outputs is a simple way to constrain the output of large language models and make it directly usable for other applications.
