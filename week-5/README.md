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

The handler searches the message for a number followed by "day" or "days" (for example "3 days"). If it finds one, it multiplies that number by 200 RWF and caps the result at 5,000 RWF with `min(days * 200,
