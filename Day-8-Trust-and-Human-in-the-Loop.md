# Day 8: Trust, Human-in-the-Loop and Responsible AI


---

## 1. When I'd keep a human in the loop

- The question I ask myself isn't "is the AI good enough?" but "what happens when it's wrong, and who pays for it?"
- I'd let the AI act on its own when:
  - The cost of a mistake is low and easy to undo (suggesting a time slot the patient can change).
  - I've evaluated it and it performs well on that specific task.
  - The task is high volume and repetitive, where a human adds little.
- I'd keep a human in the loop when:
  - The decision is high stakes or hard to reverse (medical, financial, legal, hiring).
  - The AI is unsure, or the input is outside what it has seen before.
  - Someone needs to be accountable for the decision.
  - Users or regulators expect a human to make the call.
- I've learned it's not all or nothing. There are levels:

| Level | What it looks like | Example |
|---|---|---|
| AI suggests | Human decides, AI just helps | AI drafts a reply, receptionist edits and sends |
| AI acts, human approves | Nothing happens until a human confirms | AI suggests urgency, nurse confirms |
| AI acts, human monitors | AI acts, humans review samples and exceptions | AI books routine slots, staff check flagged ones |
| AI acts alone | No human involved | Sending a reminder SMS |

> The pattern I'd go with is letting the AI handle the routine cases and routing the low-confidence or high-risk ones to a human.

---

## 2. How I'd design against rubber-stamping

- Rubber-stamping is when the reviewer just clicks "approve" without actually checking. At that point the human in the loop is only there on paper.
- From what I read, it happens because:
  - The AI is right most of the time, so people stop looking (automation bias).
  - Reviewers have too many items and too little time.
  - The UI makes approving one click and disagreeing a lot of effort.
  - The output is long, so checking it properly is hard.
- What I'd do to design against it:
  - **Make the reviewer think first:** for high-risk cases, I'd ask for their own judgement before showing the AI's answer.
  - **Show what matters, not everything:** I'd highlight the key claims or fields to check, not show a wall of text.
  - **Show confidence and flags:** I'd point out what the AI is unsure about so attention goes there.
  - **Make disagreeing easy:** editing or rejecting should be as quick as approving.
  - **Ask for a reason:** I'd ask for a short reason when approving high-risk items, not just a click.
  - **Seed known-wrong cases:** I'd add a few test cases with deliberate mistakes and track whether reviewers catch them.
  - **Watch the numbers:** if I see very fast approvals or a near 100% approval rate, I'd take it as a sign people aren't really reviewing.
  - **Keep the workload realistic:** a tired reviewer will rubber-stamp no matter how good the UI is.

---

## 3. How I'd design for provenance

- Provenance means showing where an AI answer came from, so the user can check it instead of trusting it blindly.
- What I'd show:
  - **Sources:** which document, record or message the answer is based on, with a link.
  - **Quotes:** the exact line from the source that backs up the claim.
  - **What's AI generated:** I'd clearly label AI content so people know what they're reading.
  - **Confidence:** where the AI is unsure, I'd say so.
  - **What it didn't have:** e.g. "I couldn't find the patient's last visit record."
- In the booking assistant, if the AI says "patient reported chest pain for 2 days", I'd want the nurse to be able to click it and see the exact line in the intake form.
- I also see provenance as a defence against rubber-stamping, because it makes checking quick and easy.

---

## 4. Hallucination and bias guardrails

**Hallucination** is when the AI confidently says something that is not true or not in its sources.

Guardrails I'd use to reduce it:

- Allow the AI to say "I don't know" instead of forcing an answer.
- Ground answers in provided documents and tell the AI to use only those.
- Ask it to quote the source first, then answer based on the quotes.
- Ask for citations, and drop any claim it can't back up with a source.
- Get it to check its own answer, or generate a few answers and compare them. If they disagree, I'd treat that as a warning sign.
- Use code to check facts where possible (does this doctor or slot actually exist in the system?).

**Bias** is when the AI treats some groups of people worse than others, usually because of the data it learned from.

Guardrails I'd use to reduce it:

- Test with inputs from different groups: names, languages, ages, accents, ways of writing.
- Compare results across groups, not just the overall score.
- Avoid using sensitive attributes (gender, religion, ethnicity) for decisions unless there's a clear and fair reason.
- Get a diverse set of people to review the outputs.
- Keep monitoring after launch, since bias can show up with real users that my testing didn't catch.

> For example, if the AI rates urgency lower for patients who write in broken English, that's bias, and it could delay care for exactly the people who need help most.

---

## 5. Privacy, transparency and risk

**Privacy**

- I'd only collect the data the feature actually needs.
- I'd be clear about whether user data is stored, for how long, and whether it's used to train models.
- Where possible, I'd mask or remove personal details before sending data to third-party models.
- I'd make sure the AI can't show one user's data to another.
- I'd follow the regulations that apply (e.g. health data rules, DPDP in India, GDPR in Europe).

**Transparency**

- I'd tell users they're talking to an AI.
- I'd explain what it can and can't do.
- When a decision affects them, I'd explain in simple words why the AI gave that answer.
- I'd always give a way to reach a human.

**Risk (NIST AI Risk Management Framework)**

The way I understood the NIST framework, it breaks risk management into four functions:

| Function | What it means to me |
|---|---|
| **Govern** | Who owns AI risk, what the policies are, who is accountable |
| **Map** | Understanding the context: who uses it, who could be affected, what could go wrong |
| **Measure** | Testing and tracking the risks with evals and monitoring |
| **Manage** | Prioritising and acting on the risks, and having a plan for when something goes wrong |

It also lists what makes AI trustworthy: valid and reliable, safe, secure, accountable and transparent, explainable, privacy-enhanced, and fair with harmful bias managed.

---

## 6. How I'd set honest client expectations

- AI features are not deterministic. The same input can give different outputs, and it will be wrong some of the time. I'd tell clients this upfront, not after launch.
- What I'd make clear to a client:
  - **It won't be 100% accurate.** I'd give the expected accuracy from evals, e.g. "around 92% of bookings go through correctly, the rest go to staff".
  - **What happens when it's wrong.** I'd explain the fallback and the human review step.
  - **It improves over time.** The first version is a starting point, and quality improves as we see real usage and fix failures.
  - **Numbers come from testing, not promises.** I'd share eval results instead of saying "it works great".
  - **There's an ongoing cost.** Monitoring, reviewing and updating the AI is continuous work, not a one-time build.
- I'd avoid demoing only the best cases. I'd show a few failures too, so the client knows what to expect.

> I'd rather be honest about the limits now than lose the client's trust later by overpromising.

---

## 7. Practice: my review against the interaction guidelines

Microsoft's Guidelines for Human-AI Interaction has 18 guidelines across four stages: initially, during interaction, when wrong, and over time. I reviewed the booking assistant against the ones most relevant to it.

| # | Guideline | Current state | Gap I found |
|---|---|---|---|
| G1 | Make clear what the system can do | Bot greets with "How can I help?" | Doesn't say it can only book, reschedule and cancel. Patients ask medical questions it can't answer |
| G2 | Make clear how well it can do it | Nothing shown | No hint that it might misunderstand dates or names |
| G6 | Mitigate social biases | Not tested | No check on how it handles regional languages or non-native English |
| G9 | Support efficient correction | Patient has to start over to fix a detail | No way to edit just the date or the doctor |
| G10 | Scope services when in doubt | Guesses when the request is unclear | Should ask a clarifying question instead of picking a slot |
| G11 | Make clear why it did what it did | Urgency label shown with no reason | No link to the symptoms that led to the label |
| G15 | Encourage granular feedback | No feedback option | Staff can't flag a wrong urgency rating |
| G16 | Convey the consequences of user actions | "Confirm" books instantly | Doesn't show the cancellation policy or fee before confirming |
| G18 | Notify users about changes | Model updates silently | Staff aren't told when behaviour changes |

**What I'd fix first:** G10 (guessing instead of asking), G11 (no reason for urgency) and G6 (bias not tested), since these can directly affect patient care.

---

## 8. Exercise: my redesign against rubber-stamping

**The flow I picked:** the AI reads the patient's intake form, writes a summary and suggests an urgency level (Routine / Soon / Urgent). A nurse reviews it before the appointment is booked.

**Current flow (easy to rubber-stamp)**

```
AI summary + urgency shown → Nurse clicks "Approve" → Appointment booked
```

Problems I see with it:

- The urgency is shown upfront, so the nurse anchors on it.
- Approving is one click, but rejecting means rewriting the summary.
- There's no link between the summary and what the patient actually wrote.
- Nobody tracks how fast nurses approve.

**My redesigned flow**

```
Patient's own words shown first
        ↓
Nurse picks urgency on their own (for flagged / high-risk cases)
        ↓
AI's urgency revealed → if it's different, the nurse must pick one and give a short reason
        ↓
Summary shown with each claim linked to the line in the intake form
        ↓
Low-confidence claims highlighted for checking
        ↓
Approve / Edit field / Reject (all one click)
```

**What I changed and why**

| Change | Why I made it |
|---|---|
| Nurse judges first on flagged cases | So they don't anchor on the AI's answer |
| Reason needed when nurse and AI disagree | So the nurse actually thinks through the difference |
| Claims linked to source lines | Checking becomes quick, so it actually happens |
| Low-confidence parts highlighted | Attention goes where the AI is weakest |
| Edit a single field instead of rewriting | Disagreeing becomes as easy as approving |
| A few seeded test cases with wrong urgency | Tells me whether nurses are really reviewing |
| Track time per review and approval rate | Very fast approvals or ~100% approval are warning signs I'd watch for |

---

## 9. Reflection: who could this harm and how would I know

**Who I think it could harm**

- **Patients with urgent symptoms** whose case gets rated as routine and delayed.
- **Patients who don't write in fluent English** or who describe symptoms differently, if the AI understands them worse.
- **Older or less tech-savvy patients** who can't easily correct the AI or reach a human.
- **Patients whose data leaks** to someone else through a wrong answer.
- **Nurses and staff** who get blamed for errors they approved because the system pushed them to click through.
- **The clinic**, through lost trust or legal risk.

**How I would know**

- I'd run evals that break results down by group (language, age), not just one overall number.
- I'd track cases where a doctor later changed the urgency, especially ones moved from routine to urgent.
- I'd watch reviewer behaviour: approval rate, time per review, and how many seeded mistakes they catch.
- I'd give patients and staff an easy way to flag a wrong or unfair result, and I'd actually read those reports.
- I'd regularly read real conversations, the same way I would in error analysis.
- I'd set up an escalation path so a serious failure reaches someone who can act on it quickly.

> If I can't name who could be harmed and I have no signal that would tell me it's happening, I wouldn't consider the feature ready.


