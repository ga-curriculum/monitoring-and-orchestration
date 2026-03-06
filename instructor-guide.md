# Monitoring and Orchestration: Instructor Guide

**Module Time:** 120 minutes  

---

## What This Module Is About

This module focuses on **operating agents after deployment**.

Up to this point in the course, students have:

- Designed agents
- Built working prototypes
- Integrated tools and workflows
- Implemented guardrails and security controls

But building an agent is only the beginning.

Once deployed, an agent becomes a **living system** that interacts with unpredictable inputs, evolving data, and changing business requirements.

This session explores what it takes to **keep an agent working reliably over time**, including:

- Monitoring agent behavior and performance
- Adjusting prompts, logic, and tools based on observed outcomes
- Scaling agent systems responsibly
- Coordinating multiple agents within larger workflows

Students will learn how to **observe, diagnose, and improve agents after launch**, ensuring that systems remain reliable as they grow in usage and complexity.

---

## Core Objectives

By the end of this module, learners should be able to:

- Monitor agent performance using logs, metrics, and analytics
- Identify patterns of failure or quality degradation
- Refine prompts and logic based on monitoring insights
- Diagnose problems caused by tools or integrations
- Understand how agent systems scale with increased volume or complexity
- Apply structured approaches to responsible agent orchestration

---

## Must-Hit Topics

- Monitoring deployed agent systems
- Completion, error, and escalation metrics
- Quality monitoring strategies
- Reasoning trace analysis
- Prompt refinement based on production data
- Logic and decision rule adjustments
- Tool reconfiguration
- Scaling agents responsibly
- Autonomy levels in agent systems
- Observability and logging practices

---

# 1. Monitoring: Knowing What Your Agent Is Doing
**Time:** ~20 minutes  

## Purpose

Introduce students to the importance of **observability in agent systems**.

Once deployed, agents operate continuously and autonomously.  
Without monitoring, mistakes may go unnoticed until they cause real problems.

Monitoring allows teams to:

- detect errors
- evaluate quality
- diagnose unexpected behaviors
- maintain trust in automated systems

---

## Instructor Framing

Explain that monitoring solves a unique challenge:

Humans often show signals when unsure — hesitation, questions, uncertainty.

Agents do not.

They execute quickly and confidently even when wrong.

Therefore:

> Monitoring is the mechanism that makes automated systems observable and trustworthy.

---

## Three Layers of Monitoring

Effective monitoring operates at three levels.

---

### Layer 1 — Task Completion

Basic operational metrics.

| Metric | What it Measures | Warning Signal |
|---|---|---|
| Completion Rate | Successful task completion | Sudden decline |
| Error Rate | Tool failures or crashes | Spike in errors |
| Escalation Rate | Cases routed to humans | Rapid increase |
| Response Time | Processing speed | Latency spikes |

Example:

```
Week 1 baseline
Completion rate: 91%
Error rate: 4%
Escalation rate: 8%

Week 5 review
Completion rate: 84%
Escalation rate: 16%
```

Interpretation:

The agent is encountering more edge cases.

---

### Layer 2 — Output Quality

Completion does not guarantee useful output.

Quality monitoring evaluates whether the agent **solved the user's problem effectively**.

Common techniques:

- Random conversation sampling
- User feedback ratings
- Output verification against ground truth

Instructor suggestion:

Review **5–10 agent conversations per week**.

---

### Layer 3 — Reasoning Traces

Reasoning traces record:

- agent decisions
- tool calls
- retrieved information
- reasoning steps

They are essential for diagnosing subtle problems.

Example issue:

```
Escalation rate increased.

Reasoning trace reveals:
Agent escalates whenever message contains "legal".

Actual messages:
"No legal action needed — just checking my refund."
```

Root cause:

Escalation rule too broad.

Fix:

Refine rule to detect **legal threats**, not the word itself.

---

## Monitoring Infrastructure

Students should understand the basic monitoring stack.

Minimum components:

- **Logs** – record inputs, outputs, tool calls
- **Metrics dashboard** – track completion, errors, escalations
- **Review cadence** – scheduled monitoring review
- **Alerts** – automatic notification when thresholds exceeded

Even simple systems (spreadsheets + logs) can provide useful visibility.

---

# 2. Adjustment: Improving Based on What You See
**Time:** ~20 minutes  

## Purpose

Explain how monitoring insights translate into **system improvements**.

Monitoring without adjustment has no value.

Every observed pattern should lead to a corrective action.

---

## Adjustment Categories

Three common adjustment types:

1. Prompt refinement
2. Logic adjustments
3. Tool configuration changes

---

## Adjustment Type 1 — Prompt Refinement

Many behavioral issues originate in the **system prompt**.

Common causes:

- vague instructions
- missing constraints
- conflicting rules

Refinement process:

```
1 Identify the pattern
2 Locate the cause
3 Write a specific fix
4 Test the change
5 Monitor results
```

Example:

Observed issue:

Agent responses are inconsistent in tone.

Fix:

Add example responses to the prompt defining tone.

---

## Adjustment Type 2 — Logic Updates

Some problems require updating the agent’s **decision criteria**.

Example scenario:

A lead qualification agent incorrectly marks mid-sized leads as high priority.

Root cause:

The agent inferred signals not defined in its scoring rules.

Fix:

Define explicit decision criteria.

```
Priority signals:

HIGH:
company size > 200
decision-maker confirmed

MEDIUM:
company size 50–200

LOW:
all other cases
```

Explicit criteria prevent inconsistent decisions.

---

## Adjustment Type 3 — Tool Reconfiguration

Sometimes the agent is correct, but the **data it receives is wrong**.

Examples:

Problem:

Agent retrieves outdated pricing.

Fix:

Add timestamp validation.

Problem:

Tool returns sensitive fields.

Fix:

Restrict data retrieval to required fields only.

---

## When Adjustment Is Not Enough

Sometimes the agent architecture must change.

Signals include:

| Signal | Implication |
|---|---|
| Persistent high escalation rate | Agent lacks required capability |
| Frequent user confusion | UX design problem |
| Continuous tool failures | Integration problem |

Prompt tweaks cannot solve structural issues.

---

# 3. Orchestration: Scaling Agents Responsibly
**Time:** ~15 minutes  

## Purpose

Explain how agent systems behave differently as **usage scales**.

Scaling introduces new challenges.

---

## Dimension 1 — Volume

More interactions increase input diversity.

At scale the agent will encounter:

- unexpected phrasing
- edge cases
- adversarial inputs

Monitoring metrics should remain stable as volume increases.

If escalation rates rise with traffic, the system requires adjustment.

---

## Dimension 2 — Complexity

Teams often add capabilities once an agent works.

Example progression:

Simple agent:

- answers product FAQs

Expanded agent:

- processes orders
- handles refunds
- updates accounts

Each new capability introduces additional risks.

Rule:

> Every capability increase requires re-running the security checklist.

---

## Dimension 3 — Autonomy

Autonomy should increase gradually.

Autonomy ladder:

```
Level 1
Human approves every action

Level 2
Human reviews a sample

Level 3
Human reviews escalations only

Level 4
Automated monitoring with alerts
```

Advancing levels requires **demonstrated reliability**.

---

## Scaling Checklist

Before scaling an agent:

| Check | Status |
|---|---|
| Monitoring metrics established | ☐ |
| Logs and reasoning traces enabled | ☐ |
| Alerts configured | ☐ |
| Prompt refinements documented | ☐ |
| Security review completed | ☐ |
| Stress testing performed | ☐ |
| Responsible oversight assigned | ☐ |

---

# Break
**Time:** ~10 minutes  

Encourage students to reflect on how monitoring reveals patterns in real systems.

---

# Lab Transition
**Time:** ~60 minutes  

Students will:

1. Review logs from their prototype agent
2. Identify failure patterns
3. Propose prompt or logic refinements
4. Design a monitoring plan
5. Evaluate scaling readiness

Instructor role:

Guide students in diagnosing patterns rather than jumping directly to fixes.

---

# Wrap-Up & Reflection
**Time:** ~5 minutes  

## Key Takeaways

- Agents require continuous monitoring after deployment
- Observability enables trust in automated systems
- Monitoring insights must lead to improvements
- Scaling requires deliberate planning
- Responsible autonomy grows gradually

---

## Next Session

**Capstone Project**

Students will integrate all course concepts to design and present a complete agent system.

---

## Resources

- Langfuse — LLM Observability Platform  
- Helicone — LLM Monitoring and Analytics  
- Flowise — Monitoring and Logs Documentation  
- n8n — Workflow Execution Logs  
- Relevance AI — Agent Analytics  
- NIST AI Risk Management Framework  
- Anthropic — Evaluation and Monitoring of AI Systems
