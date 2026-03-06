# Monitoring and Orchestration
**Session Time:** 120 minutes  
**Format:** Instructor-Led  

---

## Prerequisites

- Completion of Sessions 1–14  
- A working agent prototype built in Session 14  
- Understanding of automated workflow design and guardrails (Session 11)  
- Familiarity with security risks, output validation, and audit trails (Sessions 10 and 13)  
- Understanding of the build-test-refine cycle (Session 14)  

---

## Learning Objectives

By the end of this session, students will be able to:

1. **Monitor** agent performance through analytics and logs to track its success and identify errors.
2. **Adjust** agent behavior by modifying prompts or response strategies to improve decision-making over time.
3. **Evaluate** the scalability of agent systems to ensure they can perform effectively as demands grow.

> Building an agent is the beginning, not the end. A deployed agent is a living system — it encounters inputs you didn't anticipate, makes mistakes you didn't design for, and operates in a world that changes. This session is about what it takes to keep an agent working well after it goes live: how to watch it, how to improve it, and how to know when it's ready for more.

---

## Session Breakdown

| # | Segment | Duration |
|---|---------|----------|
| 1 | Monitoring: Knowing What Your Agent Is Doing | 20 min |
| 2 | Adjustment: Improving Based on What You See | 20 min |
| 3 | Orchestration: Scaling Agents Responsibly | 15 min |
| 4 | Break | 10 min |
| 5 | Lab | 60 min |
| 6 | Wrap-Up & Reflection | 5 min |
|   | **Total** | **120 min** |

---

## 1. Monitoring: Knowing What Your Agent Is Doing (20 min)

### The Invisible Problem

When a human employee makes a mistake, there are usually signals: a hesitation, a question, a look of uncertainty. You can observe them, intervene, and course-correct in real time.

When an agent makes a mistake, there are often no signals at all. The agent completes its task, produces an output, and moves on — confident and fast. The error only surfaces later: a customer who received wrong information, a record that was updated incorrectly, a report built on the wrong data.

This is the central challenge of monitoring deployed agents: **the agent doesn't know when it's wrong, and it doesn't slow down when it is.** Without deliberate monitoring, errors accumulate silently.

Monitoring is not about distrusting your agent. It is about maintaining the visibility that lets you trust it — because you can see what it's doing and verify that it's working.

---

### What to Monitor: The Three Layers

Effective agent monitoring operates at three levels simultaneously. Each answers a different question.

---

#### Layer 1: Task Completion — Is It Working?

The most basic monitoring question: did the agent complete the tasks it was supposed to complete?

**What to track:**

| Metric | What it measures | What a problem looks like |
|---|---|---|
| **Completion rate** | % of sessions that reach a successful outcome | Rate drops from 92% to 74% over two weeks — something changed |
| **Error rate** | % of sessions that end in a tool failure or unhandled exception | Spike on Monday mornings → likely an upstream API that's slow at high traffic |
| **Escalation rate** | % of sessions routed to human review | Increasing escalation rate → agent hitting edge cases it can't handle |
| **Response time** | How long each session takes from trigger to output | Sudden increase → tool latency, not agent logic |

Track these metrics from day one — even if you're only monitoring them in a simple spreadsheet. Without a baseline, you can't detect when something changes.

```
Week 1 baseline (after launch):
  Completion rate: 91%
  Error rate: 4%
  Escalation rate: 8%
  Average response time: 18 seconds

Week 5 (routine check):
  Completion rate: 84% ← dropped 7 points
  Error rate: 4% ← unchanged
  Escalation rate: 16% ← doubled
  Average response time: 19 seconds ← unchanged

Interpretation: Errors aren't increasing, but the agent is routing 
twice as many conversations to humans. Something in its decision 
logic is becoming more conservative — or it's encountering a new 
category of input it wasn't designed for.
Next step: Review the escalated conversations from the past two weeks.
```

---

#### Layer 2: Quality — Is It Working Well?

Completion rate tells you the agent finished the task. It doesn't tell you whether the output was any good. Quality monitoring looks at the content of what the agent produced.

**Approaches to quality monitoring:**

**Random sampling:** Review a random selection of completed sessions each week — not just the ones that failed or escalated. Reading conversations where the agent "worked" often reveals subtle quality problems that never triggered an error.

```
What to look for in a random sample:

✅ Good signs:
  → Agent correctly identified the user's intent on the first try
  → Response matched the expected format and length
  → Tone was appropriate for the context
  → The user's task was completed without unnecessary back-and-forth

⚠️ Warning signs:
  → Agent asked for information it already had
  → Response was technically correct but confusing or unhelpful
  → Agent applied the wrong template or policy
  → User had to repeat or rephrase their request more than once
  → Output contained hedging language that suggests low confidence
     ("I believe...", "This might be...", "You may want to verify...")
```

**User satisfaction signals:** If users can rate or respond to the agent's outputs, that feedback is direct quality data. Even a simple thumbs up / thumbs down at the end of a session reveals patterns over time.

**Output spot-checking against ground truth:** For agents that retrieve and use data — prices, policies, records — periodically verify that the information surfaced in outputs matches the actual source. This catches hallucination and data retrieval errors that completion metrics never will.

---

#### Layer 3: Reasoning Traces — Why Did It Do That?

The most powerful and underused monitoring tool is the agent's reasoning trace — the record of how it thought through a task, which tools it called, what it retrieved, and why it made each decision.

Most platforms that support agent deployment also support logging the reasoning trace. This log is invaluable for diagnosing problems that surface metrics can't explain.

```
Example: An agent that routes support tickets is escalating 
correctly-categorized tickets to human review.

Surface metric: Escalation rate increased by 12%.

Without the reasoning trace:
  Unknown why. The agent "seems" more conservative. 
  Could be anything.

With the reasoning trace:
  Log shows: "Customer message contains the phrase 'legal action.'
  Per my instructions, any mention of legal action requires 
  escalation regardless of context."
  
  Actual messages triggering this:
  → "No legal action needed — just want to know my refund status"
  → "Took legal action to fix my broken phone screen at the Apple Store"
  → "This is a legally binding confirmation of my order"

  Root cause identified: The system prompt instruction is too literal.
  Fix: Refine to "Escalate if the user is threatening legal action 
  against the company — not if 'legal' appears in any context."
```

The reasoning trace transforms debugging from guesswork into diagnosis.

---

### Building Your Monitoring Stack

You don't need sophisticated infrastructure to monitor effectively. What you need is:

**A log:** Every agent run should produce a record — at minimum, the timestamp, the input, the output, whether it completed or escalated, and any tool errors. Most no-code platforms produce this automatically; you may need to route it to a spreadsheet, a logging tool, or a simple database.

**A dashboard:** A weekly view of your three-layer metrics — completion rate, error rate, escalation rate, response time, and any quality metrics you've defined. A simple Google Sheet updated weekly is sufficient at the start. The goal is visibility, not sophistication.

**A review cadence:** Someone reviews the dashboard and a random sample of logs on a defined schedule. Weekly is the right starting frequency for a newly deployed agent. Bi-weekly is acceptable once the agent is stable. Monthly is too infrequent to catch problems before they become serious.

**An alert:** At least one automated notification that fires when something crosses a threshold — error rate above 10%, completion rate below 80%, zero completions in 24 hours. You shouldn't need to check the dashboard to know something is badly wrong.

---

## 2. Adjustment: Improving Based on What You See (20 min)

### Monitoring Without Adjustment Is Just Watching

The purpose of monitoring is not to observe problems — it is to fix them. Every monitoring insight should have a defined path to action: what the signal means, what change it suggests, and how to verify the change worked.

This section covers the three most common types of adjustment you'll make as an agent operator: prompt refinement, logic adjustment, and tool reconfiguration.

---

### Adjustment Type 1: Prompt Refinement

The system prompt is the most accessible lever for improving agent behavior. Most behavioral problems — wrong tone, wrong scope, wrong level of detail, inconsistent decisions — trace back to something in the prompt that is either missing, too vague, or unintentionally producing the wrong behavior.

**The refinement process (from Session 14, now applied to real production data):**

```
Step 1: IDENTIFY the pattern
  Don't react to a single bad output. Look for a pattern:
  "This happened in 6 of the 47 sessions I reviewed this week."

Step 2: LOCATE the cause in the prompt
  Read the relevant section of the system prompt with the problem in mind.
  Ask: Is the instruction missing? Too vague? 
       Does it conflict with another instruction?

Step 3: WRITE a specific fix
  Not: "Be clearer about tone"
  Yes: "When responding to a refund request, always begin with 
        an acknowledgment of the inconvenience before explaining 
        the process. Example: 'I understand this is frustrating — 
        let me get this sorted for you.'"

Step 4: TEST with the inputs that triggered the problem
  Use the exact conversations or inputs that revealed the problem.
  Did the new instruction produce the right output?

Step 5: MONITOR for two weeks
  Track whether the pattern disappears in your logs.
  Watch for new problems introduced by the change.
```

**Common prompt refinement scenarios:**

| Observed problem | Likely prompt cause | Example fix |
|---|---|---|
| Agent is inconsistently formal | Tone not defined precisely enough | Add example sentences in the correct tone |
| Agent answers questions outside its scope | Out-of-scope behavior not explicitly addressed | Add: "If asked about [X], respond with: [specific message]" |
| Agent over-escalates simple requests | Escalation triggers too broad | Narrow the escalation criteria with specific examples of what qualifies |
| Agent under-escalates complex requests | Escalation triggers too narrow or missing | Add explicit trigger: "If the user mentions [X], always escalate" |
| Agent hedges excessively | Not instructed to be direct | Add: "Give a clear, direct answer. Do not hedge unless you genuinely cannot determine the answer." |
| Output format varies between runs | Format not specified precisely enough | Add an explicit output template with exact structure |

---

### Adjustment Type 2: Logic and Decision Criteria

Some problems aren't about tone or format — they're about the rules the agent uses to make decisions. When monitoring reveals that the agent is consistently making wrong decisions at a specific point, the decision criteria need to be updated.

```
Scenario:
A lead qualification agent is flagging mid-market leads 
as "high priority" at twice the expected rate.

Review of reasoning traces reveals:
The agent is interpreting "company uses Salesforce" as a signal 
of high intent — but this criterion was never defined as a priority signal.
It appears in the agent's reasoning as an inference from training data.

Fix:
Add to system prompt: "Priority signals for lead scoring are:
  HIGH: Company size > 200, budget explicitly mentioned, 
        decision-maker confirmed
  MEDIUM: Company size 50–200, interest expressed, role is Director+
  LOW: All other cases

Do not infer priority signals not on this list. 
If uncertain, default to MEDIUM and flag for human review."
```

Decision criteria should be **explicit and exhaustive** — not left for the agent to infer. Every time the agent infers a rule rather than following a defined one, you introduce inconsistency.

---

### Adjustment Type 3: Tool Reconfiguration

Sometimes the problem is not the agent's reasoning — it's the data the agent is working with. When a connected tool returns stale data, incorrect formats, or more information than the agent needs, the agent's outputs will be wrong even if its logic is perfect.

```
Common tool problems and fixes:

Problem: Agent retrieves outdated pricing and quotes wrong prices
  Fix: Check the data source — is pricing refreshed automatically?
       Add a timestamp check: "If the pricing data is more than 
       24 hours old, surface a warning instead of quoting directly."

Problem: Agent retrieves an entire database record when it only 
  needs two fields, and sometimes surfaces sensitive fields in its output
  Fix: Reconfigure the tool to retrieve only the specific fields needed.
       Add to system prompt: "Only surface [field A] and [field B] 
       to the user. Never include [sensitive fields] in your response."

Problem: Tool call fails intermittently — 
  agent crashes with no fallback
  Fix: Define an explicit fallback in the system prompt for every 
       tool failure. Add a retry — most platforms support automatic 
       retry on tool errors.
```

---

### When to Adjust vs. When to Rebuild

Not every problem should be solved with a prompt tweak. Some patterns in your monitoring data signal that the agent's fundamental design needs to change — not just its instructions.

| Signal | Adjustment sufficient | Consider redesign |
|---|---|---|
| Isolated edge case causing wrong output | ✅ Add specific instruction for this case | |
| Consistent wrong behavior for one input type | ✅ Refine criteria or add example | |
| Agent can't handle a whole category of requests | | ✅ The scope may need to be extended or split |
| Escalation rate stays high after multiple prompt fixes | | ✅ The agent may be missing a tool or capability |
| Error rate stays high despite tool reconfiguration | | ✅ The data source or integration may be fundamentally wrong for this use case |
| Users consistently misunderstand how to interact with the agent | | ✅ The interaction design (Session 12) may need to be rethought |

Prompt refinement is powerful and should be tried first. But it has limits — and recognizing those limits is part of operating an agent responsibly.

---

## 3. Orchestration: Scaling Agents Responsibly (15 min)

### What Scaling Means for Agents

An agent that works well at 50 interactions per week may behave very differently at 5,000. Scaling is not just a technical challenge — it is a design challenge. The decisions made during development that worked fine at low volume can become serious problems under load.

Understanding the dimensions of scale helps you anticipate where your agent will struggle before it does.

---

### Dimension 1: Volume — More Interactions

As volume increases, the distribution of inputs the agent encounters widens. At 50 interactions per week, the agent mostly sees the scenarios you designed for. At 5,000, it will encounter the edge cases, the unusual phrasings, the out-of-scope requests, and the adversarial inputs that you never planned for.

**What to watch as volume grows:**

```
→ Escalation rate: Should remain roughly stable as volume scales.
  If it increases with volume, the agent is hitting edge cases 
  it wasn't designed to handle — the decision criteria need to 
  be broadened or the training inputs diversified.

→ Error rate: Should remain stable or improve as you refine.
  If it increases with volume, a specific category of input 
  is causing failures — look for patterns in the error logs.

→ Response time: May increase under load if tools or APIs 
  become bottlenecks. A response that took 18 seconds with 
  50 users may take 4 minutes with 5,000 — not because 
  the agent changed, but because the upstream systems can't 
  keep up.
```

**Practical preparation for volume scale:**

Before significantly increasing agent volume, run a **stress test**: simulate a week's worth of projected traffic in a single session, using realistic inputs. Watch where the system slows down, where errors spike, and where the agent's behavior changes. This reveals bottlenecks before they affect real users.

---

### Dimension 2: Complexity — Harder Tasks

As teams gain confidence in an agent, they naturally begin asking it to do more complex things: handle longer workflows, connect to more data sources, make decisions with more variables.

Complexity scale is the most dangerous dimension — because it looks like improvement but introduces new failure modes.

```
Simple version (low complexity):
  Agent answers product FAQ questions.
  → One tool (knowledge base lookup)
  → One decision (match question to answer or escalate)
  → Failure mode: Can't find answer → escalates correctly

Complex version (higher complexity, same agent):
  Agent handles the full customer service workflow:
  → Looks up order status
  → Processes returns
  → Updates shipping addresses
  → Applies discount codes
  → Escalates billing disputes
  
  → Five tools, multiple decision points, financial write access
  → New failure modes: Wrong tool called, write errors, 
    cascading decisions based on incorrect data
  → The same agent design that was safe at low complexity 
    is now operating at a level of authority it wasn't 
    designed or tested for
```

**The rule for complexity scaling:** Every time you add a new capability to an agent, re-run the full security checklist from Session 13 and test the agent specifically for the new capability's failure modes. Don't assume that an agent that was safe at simpler tasks is automatically safe at more complex ones.

---

### Dimension 3: Autonomy — Less Human Oversight

As teams gain trust in an agent's reliability, there is a natural tendency to reduce human oversight: remove the confirmation steps, reduce the escalation triggers, extend the agent's authority to act without review.

This is appropriate — if it happens gradually and based on demonstrated reliability, not just on optimism.

```
The autonomy ladder (appropriate progression):

Level 1 — Human approves every action
  Appropriate when: Agent is newly deployed, high-stakes actions
  
Level 2 — Human reviews a sample of completed actions
  Appropriate when: Error rate is consistently below 2%, 
                    actions are reversible
  
Level 3 — Human reviews only escalated cases
  Appropriate when: Escalation rate is stable and cases reviewed 
                    confirm correct escalation decisions
  
Level 4 — Automated monitoring only, human reviews alerts
  Appropriate when: Several months of stable, low error rate, 
                    well-defined alert thresholds, fast human 
                    response process in place

Moving up the ladder requires demonstrated reliability at 
the current level — not just confidence that things will be fine.
```

The most common mistake in autonomy scaling is moving from Level 1 to Level 4 too quickly because the agent "seems to be working." Gradual progression with defined criteria for each level is how trust is built and maintained.

---

### The Monitoring and Scaling Checklist

Before increasing the volume, complexity, or autonomy of an agent:

| Check | Status |
|---|---|
| Baseline metrics established and tracked (completion, error, escalation, response time) | ✅ / ❌ |
| Random sampling review cadence in place | ✅ / ❌ |
| Reasoning trace logging enabled | ✅ / ❌ |
| At least one automated alert configured | ✅ / ❌ |
| Prompt and logic refinements documented (not just made) | ✅ / ❌ |
| Security checklist re-run if new tools or capabilities added | ✅ / ❌ |
| Stress test completed before volume increase | ✅ / ❌ |
| Autonomy level increase based on demonstrated reliability, not optimism | ✅ / ❌ |
| A human is accountable for ongoing performance oversight | ✅ / ❌ |

---

## 4. Break (10 min)

---

## 5. Lab (60 min)

*Lab details in the Lab Guide document.*

---

## 6. Wrap-Up & Reflection (5 min)

### Key Takeaways
- An Agent You Can't See Is an Agent You Can't Trust
- Every Monitoring Insight Should Have a Path to Action
- Scale Gradually and Based on Evidence, Not Confidence


> Next session: **Final Presentation and Future of Agentic AI** 
---

## Resources

- [Langfuse — Open Source LLM Observability and Monitoring](https://langfuse.com/)
- [Helicone — LLM Monitoring and Analytics](https://www.helicone.ai/)
- [Flowise — Logging and Analytics](https://docs.flowiseai.com/configuration/monitoring)
- [n8n — Execution Logs and Error Handling](https://docs.n8n.io/workflows/executions/)
- [Relevance AI — Agent Analytics Dashboard](https://relevanceai.com/docs)
- Anthropic — *Building Reliable AI Systems: Evaluation and Monitoring* — [anthropic.com](https://www.anthropic.com)
- NIST — *AI Risk Management Framework: Govern and Monitor Functions* (AI RMF 1.0)
- McKinsey — *The State of AI in 2024: Scaling What Works*
