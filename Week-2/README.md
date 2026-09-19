# Week 2 — How Chatbots Understand Us

**Student:** Christian TUYIZERE  
**Course:** Chatbots Development (AITCD001) · United African Institute of Technology  
**Week:** 2 of 12  

## What this folder contains

| File | Description |
|------|-------------|
| `Tuyizere_Christian_Week2_Activity.ipynb` | Week 2 notebook — tokenization, POS tagging, NER with spaCy, and intent dataset |
| `Activity_1_Tokenisation_and_Entity_Hunt.docx` | Worksheet: hand tokenization, entity labeling, spaCy comparison, Rwandan-names limitation |
| `Activity_2_Ethics_Call_and_Prompt_Upgrade.docx` | Worksheet: ethics risk matching, Rwanda judgement, system prompt rewrite, test set |

## What I learned this week

This week I moved from exact keyword matching to understanding how computers actually read language. The key steps are tokenization (splitting a sentence into pieces), part-of-speech tagging (identifying what kind of word each token is), and named entity recognition (pulling out specific information like names, places, and dates).

The most important practical finding: spaCy's small English model misclassifies Rwandan place names. In my tests, Huye was tagged as ORG instead of GPE. This is a real design constraint for anyone building a Rwanda-focused chatbot.

For my intent dataset I chose an aviation and flight assistance domain, with intents covering flight status, booking management, baggage rules, airport guidance, flight services, and loyalty programmes.

## Link to this week's work

https://github.com/0780814741/chatbot-dev-journey/tree/main/Week-2
