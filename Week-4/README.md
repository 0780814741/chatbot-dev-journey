# Assignment 4 — Rule-Based Chatbot for the Campus Library

**Student:** TUYIZERE Christian  
**Course:** AITCD001 — Chatbots Development  
**Assignment:** Assignment 4 — Rule-Based Chatbot for the Campus Library

## Overview

This project is a rule-based chatbot for a campus library. It uses regular expressions to recognise common library requests and provide the appropriate response.

The chatbot supports six intents:

- Opening hours
- Borrowing
- Renewals
- Fines
- Study-room booking
- Printing

It also provides a fallback response for requests that are outside the supported topics.

## Part A — Rule-Based Intent Matching

The chatbot uses one regular-expression pattern for each intent. The rules are checked in order, and the first matching rule wins.

The order was chosen deliberately because some words can appear in more than one intent.

The `rooms` intent is checked before `borrow` because a request such as:

> "i want to book a study room"

contains the word **"book"**, which could otherwise be associated with borrowing books.

The `renew` intent is also checked before `borrow` because a request about extending a book loan can contain the word **"book"**.

The patterns use `\b` word boundaries so that keywords match complete words rather than parts of other words.

## Part B — Study-Room Booking Flow

The study-room booking flow collects three pieces of information:

1. Day — Monday to Saturday
2. Time — morning, afternoon, or evening
3. Party size — between 1 and 8 people

The chatbot asks for one value at a time. If the user gives an invalid value, the chatbot asks the same question again instead of moving to the next step.

After all three values are collected, the chatbot displays a confirmation message.

The booking flow can also be cancelled using an exit word such as `bye`, `goodbye`, `exit`, `quit`, `thanks`, or `murakoze`. When the booking is cancelled, the flow is reset so that an unfinished booking is not carried into the next conversation.

## Part C — Testing and Failure Analysis

The provided test set contains 35 messages.

The chatbot returned the expected tag for:

**32 of 35 messages**

The three expected failures were:

1. **`how many books can i take and what are the fines`**
   - Expected: `borrow+fines`
   - Got: `borrow`
   - Cause: the message contains two different intents. The current first-match rule system returns only the first matching intent.

2. **`rnw my bk`**
   - Expected: `renew`
   - Got: `fallback`
   - Cause: the abbreviation `rnw` is not included in the renew pattern, so no rule matches it.

3. **`can i extend my deadline`**
   - Expected: `fallback`
   - Got: `renew`
   - Cause: the word `extend` matches the renew pattern
