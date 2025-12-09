# Chapter 4: Service Level Objectives

> **Source**: [Google SRE Book - Chapter 4](https://sre.google/sre-book/service-level-objectives/)

---

## Overview

Service Level Objectives (SLOs) are central to SRE practices. They enable data-driven decisions about service reliability and help prioritize engineering efforts. This chapter defines key terminology and provides practical guidance on implementing SLIs, SLOs, and SLAs.

---

## Service Level Terminology

### SLI (Service Level Indicator)

A **carefully defined quantitative measure** of some aspect of the level of service provided.

**Common SLIs:**
| SLI Type | Description | Example |
|----------|-------------|---------|
| **Latency** | Time to return a response | Request latency < 100ms |
| **Error Rate** | Fraction of failed requests | < 0.1% HTTP 5xx errors |
| **Throughput** | Requests processed per unit time | 1000 requests/second |
| **Availability** | Fraction of time service is usable | 99.9% uptime |
| **Durability** | Likelihood data is retained long-term | 99.999999% (8 nines) |

**Key Points:**
- Ideally measures what users care about directly
- Sometimes only a proxy is available (e.g., server-side latency vs. client-side)
- Raw data is typically aggregated into rates, averages, or percentiles

**The "Nines" of Availability:**
| Availability | Downtime/Year | Downtime/Month |
|--------------|---------------|----------------|
| 99% (2 nines) | 3.65 days | 7.2 hours |
| 99.9% (3 nines) | 8.76 hours | 43.8 minutes |
| 99.95% (3.5 nines) | 4.38 hours | 21.9 minutes |
| 99.99% (4 nines) | 52.6 minutes | 4.38 minutes |
| 99.999% (5 nines) | 5.26 minutes | 26.3 seconds |

---

### SLO (Service Level Objective)

A **target value or range of values** for a service level measured by an SLI.

**Structure:** `SLI ≤ target` or `lower bound ≤ SLI ≤ upper bound`

**Example:**
> "99% of Get RPC calls will complete in less than 100 ms."

**Key Points:**
- Not all metrics can have SLOs (e.g., QPS is user-driven)
- SLOs set user expectations about service performance
- Publishing SLOs reduces unfounded complaints
- Without explicit SLOs, users develop their own (often incorrect) beliefs

---

### SLA (Service Level Agreement)

An **explicit or implicit contract** with users that includes consequences for meeting (or missing) SLOs.

**How to distinguish SLO vs SLA:**
> Ask: "What happens if the SLOs aren't met?"  
> - No explicit consequence → **SLO**  
> - Financial penalty, rebate, or other consequence → **SLA**

**SRE's Role:**
- Help define measurable SLIs
- Avoid triggering consequences of missed SLOs
- Typically NOT involved in constructing SLAs (business/legal decision)

**Example:** Google Search has no public SLA, but still has internal SLOs. Google Workspace products have explicit SLAs with customers.

---

## Indicators in Practice

### What Do You and Your Users Care About?

**Don't use every metric you can track!** Choose a handful of representative indicators based on what users actually want.

**SLIs by Service Type:**

| Service Type | Key SLIs | Questions Answered |
|--------------|----------|-------------------|
| **User-facing serving** | Availability, Latency, Throughput | Could we respond? How fast? How many? |
| **Storage systems** | Latency, Availability, Durability | How fast to read/write? Is data accessible? Is it retained? |
| **Big data/pipelines** | Throughput, End-to-end latency | How much processed? How long to complete? |
| **All systems** | Correctness | Was the right answer returned? |

---

### Collecting Indicators

**Server-side collection:**
- Monitoring systems (Prometheus, Borgmon)
- Periodic log analysis
- Example: HTTP 500 responses as fraction of all requests

**Client-side collection:**
- Captures issues invisible to servers
- Example: JavaScript performance, actual page load time
- Better proxy for real user experience

---

### Aggregation

> ⚠️ **Be careful with averages!** They can hide important details.

**Problem with averages:**
- A system serving 200 req/s in even seconds and 0 in odd seconds has the **same average** as one serving constant 100 req/s
- But instantaneous load is **2x higher**
- Averages obscure long-tail latencies

**Solution: Use Percentiles**

| Percentile | What It Shows |
|------------|---------------|
| 50th (median) | Typical case |
| 90th | Most users' experience |
| 99th | Plausible worst case |
| 99.9th | Edge cases, tail latency |

> 💡 **Research shows:** Users prefer a slightly slower system to one with high variance in response time.

---

### Standardize Indicators

Create reusable SLI templates with common defaults:

- **Aggregation intervals:** "Averaged over 1 minute"
- **Aggregation regions:** "All tasks in a cluster"
- **Measurement frequency:** "Every 10 seconds"
- **Request scope:** "HTTP GETs from black-box monitoring"
- **Data acquisition:** "Measured at the server"
- **Latency definition:** "Time to last byte"

---

## Objectives in Practice

### Start with User Needs

> "Start by thinking about what your users care about, not what you can measure."

Working **backward** from desired objectives to indicators often works better than choosing easy-to-measure metrics first.

---

### Defining Objectives

**Be specific about measurement conditions:**

```
99% of Get RPC calls will complete in less than 100 ms
(measured across all backend servers, averaged over 1 minute)
```

**Multiple targets for performance curves:**
```
90%   of Get RPC calls complete in < 1 ms
99%   of Get RPC calls complete in < 10 ms
99.9% of Get RPC calls complete in < 100 ms
```

**Separate objectives for different workloads:**
```
Throughput clients: 95% of Set RPCs complete in < 1 s
Latency clients:    99% of Set RPCs (< 1 kB) complete in < 10 ms
```

---

### Error Budgets

> **Don't aim for 100% reliability!** It's unrealistic, expensive, and slows innovation.

**Error Budget = Allowed unreliability rate**

- Track SLO violations daily/weekly
- Compare violation rate against error budget
- Use the gap to decide when to roll out releases

Example: 99.9% SLO = 0.1% error budget

---

### Choosing Targets

**Lessons learned:**

1. **Not purely technical** — involves business and product decisions
2. **Trade-offs required** — staffing, time-to-market, hardware, funding
3. **SLOs drive prioritization** — reflect what users care about
4. **Be careful** — overly aggressive SLOs waste effort; too lax = bad product

> ⚠️ "SLOs are a massive lever: use them wisely."

---

### Control Measures

SLIs and SLOs form a **control loop**:

```
1. Monitor and measure SLIs
        ↓
2. Compare SLIs to SLOs → Need action?
        ↓
3. Determine what action to take
        ↓
4. Execute the action
        ↓
   (repeat)
```

**Example:** If latency is increasing and will miss SLO in a few hours:
- Hypothesis: Servers are CPU-bound
- Action: Add more servers to spread load

---

### SLOs Set Expectations

**Why publish SLOs:**
- Users can evaluate if service fits their use case
- Helps decide whether to invest in speed, availability, or resilience
- If service is meeting SLOs well, maybe spend time on tech debt or new features

**Tactics:**
1. **Keep a safety margin** — don't advertise your actual capability
2. **Don't overachieve** — users will depend on higher-than-stated performance

---

## The Chubby Lesson: Don't Overachieve

> 📖 **Case Study: Google's Chubby (distributed lock service)**

**Problem:**
- Chubby was so reliable that teams assumed it would never fail
- When rare outages occurred, dependent services couldn't handle it
- Created cascading failures visible to end users

**Solution:**
- Ensure Chubby **meets but doesn't significantly exceed** its SLO
- If no real failures occur in a quarter, **synthesize a controlled outage**
- Forces teams to handle Chubby unavailability early

> 💡 **Lesson:** Excessive reliability can create dangerous hidden dependencies.

---

## Agreements in Practice

**SRE's role in SLAs:**
- Help business/legal teams understand likelihood of meeting SLOs
- Advise on risks and difficulty of SLO targets
- NOT responsible for choosing penalties or consequences

**Best practices:**
- Be conservative in what you advertise
- Broader audience = harder to change SLAs later
- Most people say "SLA" when they mean "SLO"

> 💡 **Reality check:** "SLA violation" usually means missed SLO. A true SLA violation might trigger a lawsuit!

---

## Key Takeaways

1. **SLIs** = What you measure (latency, errors, throughput, availability)
2. **SLOs** = Your target for those measurements
3. **SLAs** = Contracts with consequences for missing SLOs
4. **Focus on users** — measure what they care about
5. **Use percentiles** — averages hide important details
6. **Don't aim for 100%** — use error budgets instead
7. **Don't overachieve** — it creates hidden dependencies
8. **Start loose, tighten later** — iterate on SLO targets
9. **SLOs drive priorities** — for both SREs and developers

---

## Quick Reference

```
┌─────────────────────────────────────────────────────────┐
│                    SLI → SLO → SLA                       │
├─────────────────────────────────────────────────────────┤
│  SLI: "Request latency"                                 │
│       ↓                                                 │
│  SLO: "99% of requests < 100ms"                         │
│       ↓                                                 │
│  SLA: "If SLO missed, customer gets 10% credit"         │
└─────────────────────────────────────────────────────────┘
```

---

*Written by Ben Treynor, Mike Dahlin, Vivek Rau, and Betsy Beyer*  
*From "Site Reliability Engineering: How Google Runs Production Systems" (O'Reilly, 2016)*
