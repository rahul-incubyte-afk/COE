# Day 7: Evals for AI Features

Notes on why AI features need evals, how to define success, how to do error analysis, and what I'd want checked before shipping anything.

**Example used throughout:** a patient appointment booking assistant.

---

## Contents

1. [Why AI features need evals](#1-why-ai-features-need-evals)
2. [Measurable success criteria](#2-measurable-success-criteria)
3. [Error analysis and failure taxonomy](#3-error-analysis-and-failure-taxonomy)
4. [Golden test set from real inputs](#4-golden-test-set-from-real-inputs)
5. [Choosing the right grading method](#5-choosing-the-right-grading-method)
6. [Component-wise evaluation](#6-component-wise-evaluation)
7. [What must be evaluated before shipping](#7-what-must-be-evaluated-before-shipping)
8. [References](#references)

---

## 1. Why AI features need evals

- AI output is probabilistic. The same input can give a good answer once and a bad one the next time, so an overall accuracy number like 85% hides where it is failing and why.
- As per Hamel Husain, what makes an AI product succeed is how fast the team can iterate. That needs three abilities: **evaluating quality, debugging problems and changing the system**.
- He talks about a case study where an AI assistant stopped improving after the early prompt engineering gains. Every time the team fixed something, something else broke, and the prompt just kept growing. Without evals there was no way to tell if a change made things better or worse.
- There are three levels of evaluation:
  - **Unit tests:** quick checks (assertions) that run on every change, like making sure the output is in the right format or doesn't leak personal data.
  - **Human and model eval:** log the traces, look at them in a tool and label the outputs as good or bad. Then build an LLM judge and check that it agrees with our labels before trusting it.
  - **A/B testing:** comparing versions with real users, once the product is mature.

> **Takeaway:** look at your data regularly. Don't rely on generic frameworks or off-the-shelf metrics. Build evals specific to your product and keep debugging and improving it.

---

## 2. Measurable success criteria

- Saying the product is "performing well" is too weak. Success criteria have to be something we can measure in numbers or percentages.
- A good criterion is **specific, measurable, achievable and relevant**.
- It's important to define what "correct" means and why we picked that bar. Just saying 70% of responses are right doesn't mean much on its own.
- There are quantitative and qualitative measures. Even qualitative things like tone can be turned into a number.
- Success criteria are usually multidimensional, so each dimension needs its own target.

**Example criteria for the booking assistant:**

| Dimension | Criterion |
|---|---|
| Task success | 95% of booking requests end with a confirmed slot matching the requested doctor, date and time |
| Tone | Scored 4+ on a 1-5 rubric in 90% of conversations |
| Latency | Response in under 3 seconds for 95% of messages |
| Cost | Under the agreed budget per conversation |
| Safety | Zero personal data leaks |

---

## 3. Error analysis and failure taxonomy

- Before writing any metric, read real outputs. Otherwise we end up measuring what we *think* will fail rather than what is actually failing.
- The process:
  1. Take a sample of real traces (for this exercise, 20) and go through them one by one.
  2. For each bad output, write a short note on what went wrong. Focus on the **first** thing that went wrong, since later errors usually come from it.
  3. Group similar notes into categories. That becomes the failure taxonomy.
  4. Count how many times each category shows up and fix the biggest ones first.
  5. Keep reading new samples until no new types of failure show up.
- Ideally one person with domain knowledge owns this labelling so it stays consistent.

**Example failure categories:**

- Wrong date understood
- Made up a doctor's name
- Ignored the patient's preference
- Didn't ask a clarifying question
- Tone too robotic

---

## 4. Golden test set from real inputs

- A golden test set is a fixed set of inputs with the expected output or behaviour. We run it every time we change the prompt or the model.
- The inputs should come from real users: logs, support tickets, pilot conversations. Not only what the team imagines users will type.
- It should cover:
  - **Happy path:** the common requests.
  - **Edge cases:** ambiguous dates ("next Friday"), typos, mixed languages, two requests in one message.
  - **Refuse or hand off:** cases where the AI should not act on its own.
  - **Adversarial:** prompt injection, asking for another patient's details.
- Start small. 20 to 50 good cases are better than a thousand made-up ones.
- Every time a new failure shows up in production, add it to the set so it doesn't come back later.

---

## 5. Choosing the right grading method

| Method | Good for | Trade-off |
|---|---|---|
| **Code-based** | Exact match, regex, valid JSON, right tool called, response time | Fast, cheap and objective, but only works when a rule can check the answer |
| **Human** | Nuance, domain judgement, labelling, building the taxonomy, high-stakes cases | Most reliable, but slow and expensive |
| **LLM-as-judge** | Subjective things like tone, helpfulness, sticking to the source | Scales well, but has to be checked against human labels first |

- For LLM judges, a simple **pass/fail** works better than a 1-10 score.
- It's better to have one judge per failure type than one judge checking everything.

> **Rule of thumb:** use code where you can, an LLM judge where you have to, and humans to calibrate both.

---

## 6. Component-wise evaluation

- Most AI features are a pipeline of steps, not a single call:

  ```
  Understand intent → Extract details → Check availability → Book slot → Reply to patient
  ```

- If we only check the final answer, we know it failed but not *where* it failed.
- So each step should be evaluated on its own:
  - Was the intent right?
  - Were the details extracted correctly?
  - Was the right tool called with the right inputs?
  - Does the reply match what the tool returned?
- Errors add up. If each of 5 steps is 90% accurate, the end-to-end result is only around **59%** (0.9^5).
- For agents, check the **outcome** (was the appointment actually booked in the system) and also look at the steps it took. Don't be too strict about the exact path, since an agent can find another valid way.
- A good approach is to start with the end-to-end result to spot failures, then go into the step that is breaking.

---

## 7. What must be evaluated before shipping

What I would refuse to ship without an eval:

- [ ] **Core task success:** the golden test set passes at the threshold we agreed on.
- [ ] **Safety:** no personal data leaks, no made-up medical information, handles prompt injection. This one is zero tolerance.
- [ ] **Regression:** the new version doesn't break things that were already working.
- [ ] **Knowing its limits:** it says "I don't know" or hands off to a human when it should, instead of guessing.
- [ ] **Latency and cost:** within budget.
- [ ] **Judge validation:** if we use an LLM judge, it has been checked against human labels.

> If I can't measure it, I can't tell whether a change made the product better or worse, so it's not ready to ship.

---

## References

- [Your AI Product Needs Evals (Hamel Husain)](https://hamel.dev/blog/posts/evals/)
- [AI Evals FAQ (Hamel Husain and Shreya Shankar)](https://hamel.dev/blog/posts/evals-faq/)
- [Demystifying Evals for AI Agents (Anthropic)](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
- [Define Success Criteria and Build Evaluations (Anthropic)](https://platform.claude.com/docs/en/test-and-evaluate/develop-tests)
