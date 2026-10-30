# Day 9: Delivery with AI-First Engineering

My notes on how engineering delivery changes when AI writes a lot of the code: the Incubyte plugins engineers use with Claude Code, how I'd estimate and slice work, why review discipline still matters, what DORA's research says about AI, and what I'd monitor once an AI feature is live.

**Example I've used throughout:** the AI intake and urgency assistant from my Day 9 capstone.


## 1. Incubyte BEE and Learn plugins

Incubyte has its own set of Claude Code plugins in the `ai-plugins` repo. The two I focused on are BEE and Learn.

**BEE: AI development workflow navigator**

- BEE brings spec-driven development into Claude Code. Instead of jumping straight to "write the code", it first works out how big and risky the task is, and then picks a process that fits.
- The workflow goes: **triage → context gathering → spec → architecture → code → test → verify → review**.
- A small, low-risk change gets a light process. A bigger or riskier one gets the full spec and architecture steps.
- It has 10 commands and 26 specialist agents, and it can resume a session across conversations.
- It's aware of the design system for UI work, and you can leave `@bee` notes inline in the code to collaborate with it.
- Entry point: `/bee:sdd`

**Learn: build to learn**

- Learn teaches a technology by having you build a real project. Claude guides you step by step, but **you write every line of code yourself**.
- It creates a project-based curriculum, adjusts the pace to your skill level, tracks progress across sessions and checks understanding with quizzes.
- Entry point: `/learn:start`

**How I see engineers using Claude Code day to day**

- Understanding an unfamiliar codebase by asking questions instead of reading every file.
- Turning a story into a spec and a plan before writing code (this is where BEE helps).
- Writing tests first, then letting Claude write the code to pass them.
- Refactoring, writing boilerplate, fixing bugs and drafting PR descriptions.
- Learning a new framework while building something real (this is where Learn helps).

> The way I see it, BEE is about delivering with AI in a disciplined way, and Learn is about making sure people still grow their own skills while using AI.

**Other plugins in the same repo:** Discovery (idea to PRD), Issue Triage, Incident Postmortem, Second Brain and Daily Brief.

---

## 2. Practice: installing and exploring BEE and Learn

**Installing**

```
/plugin marketplace add incubyte/ai-plugins
/plugin install bee@incubyte-plugins
/plugin install learn@incubyte-plugins
```

**What I'd use each one for**

- **BEE:** any real delivery work, especially stories where getting the spec wrong is expensive.
- **Learn:** picking up a new language or framework without letting AI do all the thinking for me.

---

## 3. What AI coding speeds up, what it doesn't, and estimation

**What AI speeds up a lot**

- Boilerplate, CRUD screens, API wiring and config.
- Writing unit tests for clear logic.
- Understanding unfamiliar code.
- Refactoring and small bug fixes.
- First drafts of docs and PR descriptions.

**What it doesn't speed up much**

- Working out what to build: unclear requirements, talking to users, making decisions.
- Code review. There's actually more code to review now, and it needs careful reading.
- Integration with other systems, environments and third-party APIs.
- Debugging tricky issues across systems.
- Waiting on others: approvals, dependencies, deployment windows.
- For AI features: building evals, doing error analysis and tuning prompts. This is slow, careful work.

**How this changes how I'd estimate**

- I wouldn't just cut every estimate by a fixed percentage. AI speeds up the coding part, and coding is only part of delivering a story.
- I'd split an estimate into **thinking, coding, reviewing, testing and integrating**, and only reduce the coding part.
- I'd add time for review, because AI-written code can look right and still be wrong.
- For AI features, I'd add separate time for evals and error analysis. These don't get faster with AI coding tools.
- I'd keep uncertainty visible, e.g. "3–5 days", and narrow it as we learn.
- I'd track actuals for a few sprints and adjust, instead of assuming the speed-up.

> AI makes the typing faster, not the thinking. My estimates should reflect that.

---

## 4. Slicing large items into vertical slices

- A **vertical slice** is a small piece of work that goes through every layer (UI, logic, data) and delivers something a user can actually use.
- A **horizontal slice** is one layer at a time ("build the database", then "build the API"). Nothing is usable until all the layers are done.
- Vertical slices matter even more with AI. AI can generate a lot of code quickly, and small slices keep that code reviewable and give us faster feedback.

**Splitting patterns I found useful (from the Humanizing Work guide)**

| Pattern | Idea | Intake assistant example |
|---|---|---|
| Workflow steps | Deliver one step of the flow end to end first | Patient chat → summary only, before urgency |
| Business rule variations | Handle the simple rule first, add others later | Red-flag detection for chest pain first, other red flags later |
| Data variations | Support one kind of input first | English first, then mixed languages |
| Simple / complex | Do the simplest version, then add the complexity | Single patient first, then "book for me and my mother" |
| Interface variations | One interface first | Chat only, voice later |
| Defer performance | Make it work, then make it fast | Get it right first, optimise latency later |
| Spike | Time-box the unknown before splitting further | Test urgency accuracy on 100 past notes before building |

**How I'd check a slice is good**

- It delivers value a user can see or test.
- It's small enough to finish in a few days.
- It can be reviewed properly in one sitting.
- Splitting it lets us learn something or drop the lower-value parts.

---

## 5. Definition of Done when AI writes code

- AI can produce working-looking code very fast, which makes it tempting to merge without really checking. The Definition of Done is what stops that.
- The person who opens the PR owns the code, whether they or the AI wrote it.

**My Definition of Done for AI-assisted work**

- [ ] Acceptance criteria are met and checked against the story, not just "it runs".
- [ ] A human has read and understood every line of the change.
- [ ] Tests exist, pass, and actually test the behaviour (not tests the AI wrote just to pass).
- [ ] No hardcoded secrets, no personal data in logs, and dependencies are checked.
- [ ] The change is small enough to review properly.
- [ ] Code follows team conventions and doesn't add unnecessary complexity.
- [ ] Docs and PR description are updated and accurate.

**Extra for AI features**

- [ ] Golden test set passes at the agreed threshold.
- [ ] No regression on previously passing eval cases.
- [ ] Prompt and model version are tracked.
- [ ] Monitoring and alerts are in place before going live.

**Review discipline I'd keep**

- Small PRs only. A 2,000-line AI-generated PR won't get a real review.
- Reviewers ask "why" and not just "does it work".
- If something can't be explained by the person who submitted it, it doesn't get merged.

---

## 6. AI as an amplifier (DORA 2025)

The 2025 DORA report (*State of AI-assisted Software Development*) is based on survey responses from nearly 5,000 tech professionals. Its main message is that **AI is an amplifier: it magnifies an organisation's existing strengths and weaknesses.**

**Key findings**

- **90%** of respondents use AI at work, and over **80%** feel it has made them more productive.
- About **30%** have little or no trust in AI-generated code.
- AI adoption is now linked to **higher throughput** and better product performance.
- But it's still linked to **lower delivery stability**. Teams ship more, faster, and break more if their foundations aren't strong.
- Strong teams with good testing, version control and fast feedback get more out of AI. Struggling teams just produce more problems, faster.

**The DORA AI Capabilities Model** lists seven capabilities that make AI's impact more positive:

1. A clear and communicated AI stance (people know what's allowed and expected).
2. Healthy data ecosystems.
3. AI-accessible internal data (the AI has the team's context).
4. Strong version control practices.
5. Working in small batches.
6. A user-centric focus.
7. Quality internal platforms.

**What this means for me**

- Giving a team AI tools won't fix weak practices. It will make them more visible.
- Before pushing AI harder, I'd check the basics: small batches, good tests, solid CI/CD, fast reviews.
- Working in small batches links directly to vertical slicing. It's one of the things that makes AI help rather than hurt.

---

## 7. Monitoring AI features in production

An AI feature can get worse without any code change, because inputs change, models get updated and users behave differently. So I'd monitor three things.

**Quality and drift**

- Run the golden test set on a schedule and on every prompt or model change.
- Sample real conversations every week and do error analysis on them.
- Track key quality signals over time, e.g. urgency agreement with nurses, red-flag catch rate, follow-up question count.
- Watch for drift: new kinds of inputs, a new language mix, or a change in how often nurses override the AI.

**Cost**

- Cost per conversation and total monthly spend.
- Tokens per call (prompts tend to grow over time).
- Alerts if cost goes above budget or suddenly spikes.

**User feedback**

- Thumbs up/down or "was this right?" from users.
- Nurse edits and overrides, which are a free and very useful quality signal.
- Hand-off requests ("I want to talk to a person").
- Complaints and drop-off in the flow.

**Also**

- Latency and error rates.
- Safety incidents (data leaks, harmful answers) with a clear escalation path.

| What | Signal | Alert when |
|---|---|---|
| Urgency quality | Nurse agreement rate | Drops below 85% |
| Safety | Urgent cases rated routine | Any single case |
| Cost | Cost per conversation | Above $0.05 |
| Latency | p95 response time | Above 3 seconds |
| Trust | Hand-off requests | Rises more than 20% week on week |

---

## 8. Practice: sprint plan for the intake assistant

**Setup:** a 10-day sprint, 2 engineers using Claude Code with BEE, me as product, and one nurse available a few hours a week for labelling and feedback.

**Capacity**

| | Eng days |
|---|---|
| 2 engineers × 10 days | 20 |
| Sprint ceremonies (planning, stand-ups, demo, retro), ~1 day each | −2 |
| Buffer: last 1.5 days of the sprint, for both engineers | −3 |
| **Available for planned work** | **15** |

**Sprint goal**

> A patient can describe their symptoms in English, red-flag cases get the emergency message, and a nurse can review a summary with linked sources and decide urgency, using test data in a staging environment.

**Slices and estimates**

| # | Vertical slice | Est. (eng days) | Notes |
|---|---|---|---|
| 0 | Spike: run 100 past notes through the AI and compare with doctor urgency | 2 | Tests my riskiest assumption first. If it fails, we change the plan |
| 1 | Patient chat in English → AI asks up to 2 follow-ups → shows a summary | 3 | Coding is fast with AI. Most of the time goes on the prompt and review |
| 2 | Red-flag detection for chest pain and breathing difficulty → emergency message | 2 | Strict review and tests, zero-tolerance case |
| 3 | Nurse review screen: patient's words first, nurse picks urgency, then sees AI suggestion | 3 | UI is quick with AI. Time goes on the anti-rubber-stamping logic |
| 4 | Summary claims linked to the patient's original lines | 2 | Provenance |
| 5 | Golden test set v1 (14 cases) running in CI, with code checks | 2 | Evals don't speed up much with AI |
| | **Total planned** | **14 of 15** | Leaves 1 extra eng day on top of the 1.5-day buffer |

**Day-by-day plan**

| Day | Engineer A | Engineer B |
|---|---|---|
| 1 | Sprint planning · Slice 0: spike | Sprint planning · Slice 1: patient chat |
| 2 | Slice 0: spike results reviewed with nurse | Slice 1: follow-up questions |
| 3 | Slice 2: red-flag detection | Slice 1: summary + review |
| 4 | Slice 2: tests and review | Slice 3: nurse review screen |
| 5 | Slice 5: golden test set | Slice 3: nurse picks first, then AI suggestion |
| 6 | Slice 5: running in CI | Slice 3: review and fixes |
| 7 | Slice 4: link summary claims to source lines | Mid-sprint demo to nurse · fixes from feedback |
| 8 | Slice 4: review and tests | Slack: fixes, review support |
| 8.5 – 10 | **Buffer** · demo · retro | **Buffer** · demo · retro |

**Not in this sprint (next sprints)**

- Masking names and phone numbers before sending to the model. This sprint only uses test data in staging, so it's needed before any real patient data, not now.
- Mixed-language and broken-English support.
- Real calendar booking.
- LLM judge for summary quality.
- Production monitoring dashboard and alerts.
- More red-flag rules.

**Why I planned it this way**

- The spike comes first because if urgency accuracy isn't good enough, the rest of the plan changes.
- Each slice is usable end to end, so we can demo and get nurse feedback during the sprint.
- I didn't cut estimates across the board for AI. Slices where the work is mostly UI or wiring are shorter. Slices with evals, prompts or safety logic stay realistic.
- I kept the last 1.5 days of the sprint as buffer, plus 1 extra eng day of slack, because AI work often throws up surprises in quality, not code. If the buffer isn't needed, we pick up masking from the next sprint.

**Risks I'd watch**

- Spike results come in below the bar → swap slices 3–4 for more prompt and eval work.
- Nurse isn't available enough for labelling → golden set slips.
- Review becomes a bottleneck because AI produces code faster than we can read it → keep PRs small.

---

## 9. Reflection: how AI-first engineering changes my role

- **Less time on "how", more on "what" and "why".** If code is cheaper to write, the most valuable thing I can do is make sure we're building the right thing, with clear problems, specs and acceptance criteria.
- **Specs matter more than before.** Tools like BEE work from the spec. A vague story now leads to a lot of confidently wrong code, very fast.
- **Slicing becomes a core skill.** Small vertical slices keep AI output reviewable and give us feedback quickly.
- **I need to estimate differently.** I can't just assume everything is twice as fast. I need to know which parts AI speeds up and which it doesn't, and use real data from our own sprints.
- **Quality is everyone's job, and the DoD protects it.** I'd be the one pushing to keep review discipline, even when the pressure is to "just merge, the AI wrote it".
- **"Done" now includes evals and monitoring.** For AI features, launching is the start. I need to plan time for watching quality, cost and feedback after release.
- **DORA made it clear that tools amplify what's already there.** Part of my role is helping the team get the basics right (small batches, good tests, clear AI guidelines) so that AI makes us better and not just faster at making mistakes.
- **Keep learning myself.** Learn is a good reminder that using AI shouldn't replace understanding. I want to stay hands-on enough to judge the work, not just pass it along.

> AI-first engineering doesn't make my role smaller. It moves it towards clearer thinking, better slicing and stronger quality checks, because that's where the bottleneck is now.

---
