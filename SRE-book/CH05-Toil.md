# Chapter 5: Eliminating Toil

> **Source**: [Google SRE Book - Chapter 5](https://sre.google/sre-book/eliminating-toil/)

---

## The Core Principle

> *"If a human operator needs to touch your system during normal operations, you have a bug."*  
> — Carla Geisser, Google SRE

SREs should spend time on **long-term engineering work**, not operational work (toil).

---

## What is Toil?

**Toil is NOT:**
- Work you don't like doing
- Administrative chores (meetings, HR paperwork, goals) — that's **overhead**
- Grungy work with long-term value (e.g., cleaning up alerting configs)

**Toil IS:** Work tied to running a production service that is:

| Attribute | Description |
|-----------|-------------|
| **Manual** | Running a script manually, not auto-triggered |
| **Repetitive** | Done over and over, not novel problem-solving |
| **Automatable** | A machine could do it (or should be able to) |
| **Tactical** | Reactive, interrupt-driven, not strategic |
| **No Enduring Value** | Doesn't improve the service permanently |
| **Scales Linearly** | Grows proportionally with service size |

> 💡 The more attributes a task has, the more likely it's toil.

---

## Examples of Toil vs. Not Toil

| Toil ❌ | Not Toil ✅ |
|---------|------------|
| Manually restarting a service | Writing auto-restart logic |
| Processing tickets one by one | Building a self-service tool |
| Manually scaling instances | Implementing autoscaling |
| Running the same debug steps | Creating runbooks/automation |
| Responding to repetitive alerts | Fixing root cause of alerts |

---

## The 50% Rule

> **Goal: SREs should spend ≤50% time on toil, ≥50% on engineering.**

**Why this matters:**
- Toil expands if unchecked — can fill 100% of time
- Engineering work enables **sublinear scaling** with service growth
- Keeps the promise that SRE ≠ traditional Ops

---

## Calculating Toil

**On-call floor (minimum toil):**
- 6-person rotation → 2/6 weeks on-call = **33% minimum**
- 8-person rotation → 2/8 weeks on-call = **25% minimum**

**Top sources of toil at Google:**
1. **Interrupts** — non-urgent service messages/emails
2. **On-call response** — urgent incidents
3. **Releases and pushes** — even with automation

**Google SRE survey results:**
- Average: ~33% toil (better than 50% target)
- Range: 0% (pure dev) to 80% (needs intervention)

---

## What is Engineering Work?

Engineering work is:

| Characteristic | Description |
|----------------|-------------|
| **Novel** | New solutions, not repetitive |
| **Requires Judgment** | Can't be fully automated (yet) |
| **Permanent Improvement** | Makes things better long-term |
| **Strategy-Guided** | Aligned with goals, not reactive |
| **Creative/Innovative** | Design-driven approach |
| **Generalized** | Solves problems broadly |
| **Scales Team** | Handle more with same staffing |

**Typical SRE Engineering Activities:**
- Software development (automation, tools, frameworks)
- System design and architecture
- Capacity planning
- Improving reliability, performance, utilization
- Documentation and runbooks
- Postmortem analysis and prevention

---

## Is Toil Always Bad?

**In small doses, toil can be:**
- ✅ Calming and predictable
- ✅ Provides quick wins and accomplishment
- ✅ Low-risk and low-stress
- ✅ Enjoyable for some people

**Toil becomes toxic in large quantities:**

| For Individuals | For the Organization |
|-----------------|---------------------|
| Career stagnation | Creates confusion about SRE role |
| Low morale | Sets precedent for more toil |
| Burnout | Reduces engineering productivity |
| Attrition | Slows progress on improvements |

---

## The Judgment Trap

> ⚠️ **Be careful saying "not toil because it needs human judgment"**

If a service alerts SREs multiple times daily requiring complex human judgment:
- This is **poor design**, not inherent complexity
- System should be simplified or automated
- Until fixed, responding to these alerts **is toil**

---

## Key Takeaways

```
┌─────────────────────────────────────────────────────────┐
│              THE TOIL ELIMINATION MINDSET               │
├─────────────────────────────────────────────────────────┤
│  1. Cap toil at 50% of SRE time                         │
│  2. Identify what's truly toil vs. overhead             │
│  3. Automate repetitive tasks                           │
│  4. Fix root causes, not symptoms                       │
│  5. Track and measure toil quarterly                    │
│  6. Spread toil load evenly across team                 │
│  7. Small toil is okay; toxic toil is a red flag        │
└─────────────────────────────────────────────────────────┘
```

---

## Action Items

When you encounter potential toil, ask:

1. **Is this manual?** → Can it be triggered automatically?
2. **Is this repetitive?** → Can we solve it once permanently?
3. **Is this automatable?** → What would it take to automate?
4. **Does this scale linearly?** → Can we make it scale sublinearly?
5. **Is there enduring value?** → Will this improve the system?

> 💡 *"Let's invent more, and toil less."*

---

*Written by Vivek Rau, Edited by Betsy Beyer*  
*From "Site Reliability Engineering: How Google Runs Production Systems" (O'Reilly, 2016)*
