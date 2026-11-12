# Lab 05 — Conclusion

<!--
Read this before you write.

This file is graded by your instructor, by hand, and it is worth 6 of the 15 points.

- Type it yourself. Do not use a chatbot, an AI assistant, an AI autocomplete or a "rewrite my
  text" tool for any part of it, not even for grammar. English that is plain and a little
  clumsy but yours is better than polished text that is not.
- Use your own numbers. Every number you quote must be one your own notebook printed. Copy
  the number exactly as it appeared. A number your notebook did not print is marked as wrong.
- Answer in your own words, about 80-200 words per question. A short answer that uses your
  numbers beats a long answer that could have been written by anyone.
- Delete these comment lines if you like. Keep the three headings and the last line.
-->

## 1. Which layer changed your results the most?

Give the numbers before -> after for that layer (correct / wrong / no answer, out of 20) and say
whether it changed what the agent *can do* or only what it *admits*. If no layer changed the
number of correct answers, say so and explain what the layers did change instead.

Layer 1 (tool validation) changed the results the most. On my 24 questions run it went from 16/24 correct, 4 wrong, 4 no answer in bare mode to 18/24 correct, 6 wrong, 0 no answer. It changed that the agent can actually do because on alias names like Karagandy (q23) or city of Almaty (q24) the tool returned the valid regions of the list in the error, so the model fixed it on the next step. Layer 2 did not change anything at all (still 18/24 correct, 6 wrong, 0 no answer, 62 model calls and 48,708 tokens). Layer 3 only changed what the agent admits, it caught wrong numbers in q18 and q22 and turned them into no answer (18/24 correct, 4 wrong, 2 no answer).


## 2. One failure the full harness did not fix

Pick one question that the full harness (layer 3) still gets wrong or refuses. Give its id
(q01 ... q20). Open its trace in the notebook: at which step did it go wrong, and what did
the model do instead? Name one thing you would add to the harness, and what it would cost
(extra model calls, tokens, time, or a new way to fail).

With layer 3 enabled the agent still get q08 wrong and answered Jambyl. If you look at the trace, step 1 was ok, model called compare_regions for avg_monthly_wage in 2024 and got the sorted list starting from Astana (632,035.0 KZT). It failed on step 2 because on reading the list it picked Jambyl instead of the right region, and after the prompt asked for the ANSWER line it just repeated ANSWER: Jambyl. Grounding missed this because Jambyl was present in the tool output. Id add small helper tool or post-check that show  exact rank, max or min from the list. It doesnt require extra model calls, but it costs more prompt tokens for tool description and can confuse a 3B model on simple way. 

## 3. Workflow or agent for the monthly ranking?

Dala Insight wants to publish, every month, "which region is highest and which is lowest on one
indicator in one year". Would you ship the fixed workflow or the agent? Use the accuracy, the
number of model calls, the tokens and the time you measured in Part 1. Then say in one sentence
what kind of question would make you choose the other one.


I would definitely ship the fixed workflow here. In Part 1 on the 15 fixed-shape questions it got 13/15 correct (0 wrong, 2 no answer only on Nur-Sultan and Chimkent) and needed just 15 model calls, 3,852 tokens and 15.5 seconds. The bare agent got only 9/15 correct (2 wrong, 4 no answer) while spending 26 model calls, 19,835 tokens and 47.2 seconds, which is very slower and more expensive. I would pick the agent only when the questions have open shapes and depend from multiple steps, like calculating growth rate, percentage points or checking missing series for new regions, because the workflow cant run these.

---

I wrote this file myself, without any AI tool.
