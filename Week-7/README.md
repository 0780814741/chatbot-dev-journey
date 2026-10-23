# UAIT AI

A rule-based chatbot for people who ask about the **United African Institute of Technology (UAIT)** in Kigali:
future students (programs, applying, fees, location) and current students (homework deadlines, class times, exams).
The chatbot itself uses Python, `re` (regular expressions), dictionaries and functions only: there is no machine learning in the core.
An **optional** LLM layer (off by default) can be switched on on top of it: see [Optional: an LLM on top of the rules](#optional-an-llm-on-top-of-the-rules).

Built for **AITCD001 Chatbots Development**, Learning Outcome 2 (Portfolio Project).

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/0780814741/uait-ai/blob/main/uait_ai.ipynb)

> The chatbot is an automated program, not a member of staff. It says so in its greeting, and "human" connects the user to a person at any point.
> Its facts live in the dictionaries `PROGRAMS`, `FEES` and `FACTS` in the notebook. They come from the course and from public listings and **must be checked against UAIT's official sources**.

## What it can do (9 intents, plus a name and a greeting)

| Intent | Example messages (including Kinyarwanda and SMS spelling) |
|---|---|
| `programs` | "what programs do you offer", "tell me about cyber security", "wat progs u hav", "amasomo" |
| `apply` | "how do i apply", "i want to join", "kwiyandikisha" |
| `fees` | "how much is the application fee", "what is the tuition", "minerval", "amafaranga ... angahe", "hw much 2 apply" |
| `location` | "where is uait", "wer is uait", "ishuri riri he" |
| `homework` | "when is the homework due", "assignment deadline" |
| `schedule` | "what time is class", "timetable" |
| `exams` | "when are the exams", "my results" |
| `callback` | "can i get a callback", "i want an appointment" (a short conversation with a confirmation, below) |
| `cancel` | "cancel", "never mind" |

**The callback conversation.** It asks the topic, then the part of the day, repeats both back and asks yes or no.
Only after a yes is the request saved. It never asks for a phone number, ID or surname.

**Closure.** "bye", "exit", "quit", "thanks" or "murakoze" ends the chat at any point, even in the middle of a callback request.

## How the code is organised

The notebook
