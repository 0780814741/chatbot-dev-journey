# Week 5: Library Chatbot v2

## Overview

This chatbot answers campus library questions about hours, borrowing, renewals, fines, study rooms and printing. Compared with Assignment 4, it now routes each intent to its own handler function, answers several requests in one message, and remembers the reader's name and last topic across turns. It can also answer a side question in the middle of a room booking and then ask the booking question again.

## Intents and handlers

| **Intent** | **Handler** | **Fixed reply, or worked out from the message?** |
| --- | --- | --- |
| name | handle_name | Worked out from the message: it reads the name after "my name is" and stores it in context with a capital letter |
| rooms | handle_rooms | Starts the booking flow with start_booking(), or says a booking is already under way |
| hours | handle_hours | Fixed reply |
| borrow | handle_borrow | Fixed reply |
| renew | handle_renew | Fixed reply |
| fines | handle_fines | Worked out from the message |
| printing | handle_printing | Fixed reply |
| greeting | handle_greeting | Fixed reply (says it is an automated service, not a librarian) |

## How the fines handler works

The handler searches the message for a number followed by "day" or "days" (for example "3 days"). If it finds one, it multiplies that number by 200 RWF and caps the result at 5,000 RWF with `min(days * 200, 5000)`. So 3 days gives 600 RWF and 40 days gives 5,000 RWF. If the message names no number of days, it returns the general fines reply instead.

## Several requests in one message

`split_requests` cuts the message at the connectors (and, but, also, then, and a question mark), tidies each piece with `normalise()`, and drops empty pieces. A comma is not a connector. `route` then matches each piece against the rule table and keeps one (intent, request) pair per understood piece, in order, with no intent repeated.

When one request matches two intents, the first rule in the table wins. For example, "can i renew a late book" matches both renew and fines, but renew is higher in the table, so only the renewal reply is given.

`ASKS` holds `rooms`. The reply for rooms is a question ("Which day would you like the room?") that the reader must answer next, so it has to come last. Otherwise the reader would be left with an unanswered question in the middle of the reply.

## Memory

`context` is a dictionary with two entries: the reader's `name` and the `last_intent` that was answered. It lasts for the whole conversation, and "bye" clears it. The goodbye uses the stored name, for example "Thank you, Grace."

The follow-up rule: if a message matches no intent of its own, the last intent answered was fines, and the message names a number of days, it is answered as a fines question. After any other answer, it is not.

During a booking, the flow sees each message first. If the flow accepts the message as an answer, the booking moves on. If it does not, the message is routed and answered, and the booking question is asked again. The booking is kept.

## Test results

- Assignment 4 test set, one tag per message: 32 of 35

- Assignment 4 test set, routed: 33 of 35

Routing fixes the message "how many books can i take and what are the fines" (expected borrow+fines). Assignment 4 gave it only one tag, because one message got one tag. Now it is split at "and" and both intents are found. The two messages still failing are "rnw my bk" (expected renew, got fallback) and "can i extend my deadline" (expected fallback, got renew).

## Test conversations

| **#** | **What it tests** | **Expected** | **What the chatbot did** | **Pass or fail** | **If it failed: cause and part responsible** |
| --- | --- | --- | --- | --- | --- |
| 1 | Two requests joined by "and" | Both answered, in the order asked: the opening hours, then the printing prices. | Gave the opening hours, then the printing prices, in that order. | Pass | |
| 2 | One request that matches two intents | The renewal reply only. "late" also matches fines, but renew is higher in the table, and the reader asked one thing. | Gave only the renewal reply. | Pass | |
| 3 | The question goes last | The opening hours first. The reply ends with the booking question (which day), because that is what the reader must answer next. | Gave the opening hours first, then ended with "Which day would you like the room?" | Pass | |
| 4 | Remembering a name | A greeting, the name acknowledged, the printing prices, and a goodbye that uses the name Grace. | Greeted the reader, said "Nice to meet you, Grace.", gave the printing prices, and ended with "Thank you, Grace. Murakoze." | Pass | |
| 5 | A follow-up with no intent of its own | The fine for 3 days (600 RWF), then the second message answered as a fines question: the fine for 10 days (2,000 RWF). | Gave 600 RWF for 3 days, then answered "and for 10 days?" with 2,000 RWF. | Pass | |
| 6 | A side question during a booking | The hours are given and the time question asked again. The booking then completes with a confirmation for Friday afternoon, 4 people. | Gave the hours and asked "Morning, afternoon or evening?" again. The booking then finished with "A room on fri, afternoon, for 4 people. Is that correct?" | Pass | |
| 7 | Two questions with no connecting word | Both answered: the borrowing limit, then the fines. | Answered only the borrowing limit. The fines question was lost. | Fail | There is no connecting word or question mark, so the split returns one piece and the rule table matches borrow first. Part responsible: the split (split_requests). |
| 8 | An answer that is also a question | The evening slot is taken, and the closing time on Saturday is given as well. | Accepted "evening" and asked "How many people?" The closing time was never given and the evening slot was never reported as taken. | Fail | During a booking the flow sees the message first. It accepted "evening" as the answer and moved on, so the side question was never routed. Part responsible: the flow (continue_booking and the order of checks in reply_to). |
| 9 | A conversation that tests memory | The name is acknowledged. The fine for 10 days is 2,000 RWF. "and for 30 days?" is answered as fines and capped at 5,000 RWF. "bye" says "Thank you, Eric." | Said "Nice to meet you, Eric.", gave 2,000 RWF, then 5,000 RWF for 30 days, then "Thank you, Eric. Murakoze." | Pass | |
| 10 | A conversation designed to break the chatbot | "how many books can i take, what are the fines": a comma is not a connector, so I expect one request and only the borrowing limit answered. | Answered only the borrowing limit: "You may borrow up to 4 books at a time, for 14 days." | Fail (as predicted) | A comma does not split, so the message stays as one request and borrow wins. Part responsible: the split (split_requests). Same cause as conversation 7. |

## Known limitations

- Conversation 7: two questions with no connecting word are not split, so only the first is answered. Cause: the split.
- Conversation 8: an answer that is also a question is taken as an answer by the booking flow, so the side question is ignored. Cause: the flow.
- Conversation 10: a comma does not split a message, so the second question is lost. Cause: the split.
- "rnw my bk" returns fallback because no pattern contains these abbreviations. Cause: the rule table.
- "can i extend my deadline" is treated as a renewal because "extend" is in the renew pattern. Cause: the rule table.

## Running this notebook

- Open TUYIZERE_Christian_Assignment_5.ipynb in Google Colab.

- Run the cells in order. The chat cell waits for input, so do not use Run all.

## What was learned

I learned that a chatbot gets more useful when its parts are separated: the split decides how many requests there are, the router decides who answers each one, and the context remembers what was said before. I also learned that most failures are found by reading test conversations carefully, and that each failure points to one specific part (the split, the flow or the rule table), which tells me where a fix would go.
