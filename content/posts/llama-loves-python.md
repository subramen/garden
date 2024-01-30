---
title: "Constraining LLM outputs"
summary: ""
date: 2024-1-29
draft: True
tags: ['machine-learning']
---

LLMs can be notoriously stochastic in their output formats. The smaller the model, the more immune they are to prompts directing them to format their response in a certain way. Doesn't matter if you politely request or COMMAND THEM IN ALL-CAPS - these parrots can tend to fly to their own tune.

While using llama-cpp-python, I came across Llama Grammars - a novel method of constraining the output of an LLM to a specific format. I am not sure how this exactly works under the hood, but it works! I packed two tasks into one prompt, asked it to output me a JSON, and provided a simple format that the LLM obediently followed. No fluff or filler text, and mostly correct JSON that I could json.loads into my application.

Generating a new output schema isn't too much work, but you still have a few hoops to jump through. A simpler way to constrain the output - just tell it to write python! Not actual python code, but format its output as if it was a valid data structure returned from a function.

Let's say you want it to write bullet points based on some context... prompt the LLM to write a python list. 
```
prompt = {
    "system": "Given a passage of text, concisely summarize the passage in simple language. Format your response as a python list of bullet points",
    "user": f"PASSAGE: {passage}",
    "output": "SUMMARY: ```python\n summary: List[str] = "
}
```

What about an ontology based on the text? Prompt the LLM to format it as a list of dicts. Give it an example too, just to make sure.
```
prompt = {
    "system": "Write an ontology of entities contained in the passage as list. Format your response as a python list"
    "user": f"PASSAGE: {passage}",
    "output": "ONTOLOGY: ```python\n# ontology = [{'entity': 'Japan', 'class': 'country'}, {'entity': 'pizza', class: 'food'}]\n\nontology = "
}
```

