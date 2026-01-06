# Traceability: Auditable Reasoning Chains

*Part of the [Reasoning Environments](../README.md) thesis by Marcelus Fernandes*

---

## Abstract

Traceability is the mechanism that makes Reasoning Environment outputs auditable. Every conclusion must be traceable to its sources through an unbroken chain of reasoning artifacts. This document describes traceability requirements, implementation patterns, and auditing procedures.

---

## Why Traceability Matters

Traditional LLM outputs are **opaque endpoints**:

```
Input → [Black Box] → Output
```

There is no way to:
- Verify how conclusions were reached
- Identify which sources influenced the output
- Debug when outputs are incorrect
- Improve the reasoning process

Traceability creates **transparent chains**:

```
Source A ──┐
           ├→ Observation → Interpretation → Decision → Recommendation
Source B ──┘
   ↑            ↑              ↑              ↑              ↑
   └──────── All links auditable ─────────────────────────────┘
```

---

## The Traceability Principle

> **Every claim must be traceable to its source, and every decision must leave reasoning artifacts.**

This means:
- No unsourced claims
- No unexplained decisions
- No discarded intermediate states
- No implicit reasoning

---

## Traceability Requirements

### Level 1: Source Attribution

Every factual claim must reference its source.

**Pattern:**
```markdown
"Users report frequent delays in the approval process [Source: interview-3.md]"
```

**Requirements:**
- Specific file reference (not "from interviews")
- Exact location when possible (section, line)
- Direct quotes when available

**Anti-pattern:**
```markdown
❌ "Users struggle with the approval process" (no source)
❌ "According to research..." (vague reference)
❌ "It was mentioned that..." (passive, unsourced)
```

---

### Level 2: Estimation Attribution

Every estimate must explain its basis.

**Pattern:**
```markdown
"Substantial efficiency improvement [AI estimation based on: 
- Manual task elimination described in interviews
- Process automation benchmarks
- Conservative interpretation of pain point severity]"
```

**Requirements:**
- Methodology explanation
- Input factors listed
- Conservative interpretation noted

**Anti-pattern:**
```markdown
❌ "40% efficiency improvement" (no basis)
❌ "Significant savings" (no estimation tag)
```

---

### Level 3: Inference Attribution

Every inference must be distinguished from observation.

**Pattern:**
```markdown
Observation: "User mentioned checking email 5 times per day for updates [Source: interview-1.md]"

Inference: "This suggests notification systems are inadequate [Inference: based on frequency of manual checking behavior]"
```

**Requirements:**
- Clear separation of fact and inference
- Explicit `[Inference: ...]` tag
- Reasoning provided

---

### Level 4: Decision Attribution

Every recommendation must trace to supporting analysis.

**Pattern:**
```markdown
## Recommendation: Prioritize notification system redesign

### Supporting Evidence:
- Pain Point PP-12: Manual status checking [Source: cluster-3.md]
- Pain Point PP-15: Delayed information [Source: cluster-3.md]
- Journey Stage 4: Information gap identified [Source: journey.md]

### Reasoning:
High frequency pain points (PP-12, PP-15) affecting critical journey stage.
[Prioritization rationale: frequency × severity × journey criticality]
```

---

## Traceability Chain Architecture

A complete traceability chain connects sources to recommendations:

```
┌─────────────────────────────────────────────────────────────┐
│                    TRACEABILITY CHAIN                        │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  Source Documents                                            │
│  └── interview-1.md, interview-2.md, ...                    │
│           ↓                                                  │
│  Observations (Agent 1)                                      │
│  └── "User said X" [Source: interview-1.md]                 │
│           ↓                                                  │
│  Pain Points (Agent 2A)                                      │
│  └── PP-01: Description [Derived from: observation-1]       │
│           ↓                                                  │
│  Clusters (Agent 2B)                                         │
│  └── Cluster A: [Contains: PP-01, PP-05, PP-12]             │
│           ↓                                                  │
│  Journey Mapping (Agent 3-4)                                 │
│  └── Stage 3: [Pain points: PP-01, PP-05]                   │
│           ↓                                                  │
│  Opportunities (Agent 6)                                     │
│  └── Opp-01: [Addresses: Cluster A, Stage 3]                │
│           ↓                                                  │
│  Recommendations (Agent 9-10)                                │
│  └── MVP Feature 1: [Implements: Opp-01]                    │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

Every link is explicit and auditable.

---

## Implementation Patterns

### Pattern 1: Directory-Based Traceability

File structure creates natural traceability.

```
/1-problem/
  /1a-interview-analysis/     ← Stage 1: Sources
    interview-1-analysis.md
    interview-2-analysis.md
    
  /1b-painpoints/
    /1b0-granular/            ← Stage 2a: Decomposition
      all-painpoints-granular.md
      
    /1b1-clusters/            ← Stage 2b: Clustering
      cluster-transparency.md
      cluster-communication.md
      
    painpoint-mapping.md      ← Consolidated mapping
    
  /1c-journey/                ← Stage 3: Journey
    journey-consolidated.md
    
  /1d-output/                 ← Final outputs
    problem-report.md
```

Each directory represents a traceability stage.

---

### Pattern 2: ID-Based Cross-Referencing

Entities have unique IDs for reference.

```markdown
## Pain Point PP-12

**Quote:** "I check my email five times a day just for updates"
**Source:** [interview-1.md, line 45]
**Context:** Discussing daily workflow
**Severity:** High
**Frequency:** Daily occurrence

**Referenced by:**
- Cluster: CLU-03 (Communication Gaps)
- Journey Stage: STG-04 (Waiting for Updates)
- Opportunity: OPP-02 (Notification System)
```

---

### Pattern 3: Explicit Derivation

Each output explicitly states its inputs.

```markdown
## Cluster: Communication Gaps (CLU-03)

### Derived From:
- PP-12: Manual status checking
- PP-15: Delayed notifications
- PP-23: Information silos
- PP-31: Update visibility

### Derivation Logic:
These pain points share a common root cause: information doesn't flow 
proactively to users. They cluster by causal relationship, not by 
surface similarity.

### Source Files:
- /1b0-granular/all-painpoints-granular.md (lines 45-67)
```

---

## Auditing Procedures

### Audit Type 1: Source Verification

Verify that claimed sources contain claimed information.

**Procedure:**
1. Select claim with source attribution
2. Open referenced source file
3. Verify claim matches source content
4. Flag discrepancies

### Audit Type 2: Chain Completeness

Verify that recommendations trace back to sources.

**Procedure:**
1. Select final recommendation
2. Trace back through each stage
3. Verify all links are explicit
4. Flag broken chains

### Audit Type 3: Inference Validation

Verify that inferences are reasonable given observations.

**Procedure:**
1. Identify inferences (tagged with `[Inference: ...]`)
2. Review supporting observations
3. Assess logical connection
4. Flag unsupported inferences

---

## Traceability Benefits

| Benefit | Description |
|---|---|
| **Debuggability** | When outputs are wrong, trace back to find where reasoning failed |
| **Improvability** | Identify weak links in reasoning chain |
| **Accountability** | Clear record of what influenced decisions |
| **Trust calibration** | See exactly what supports each claim |
| **Knowledge management** | Reasoning persists beyond single session |

---

## Common Traceability Failures

| Failure | Symptom | Prevention |
|---|---|---|
| **Orphan claims** | Conclusions with no sources | Mandatory source tags |
| **Broken chains** | Stage N output doesn't reference Stage N-1 | Cross-stage validation |
| **Implicit reasoning** | Decisions made without documented logic | Mandatory derivation sections |
| **Source drift** | Sources change but references don't update | Immutable intermediate outputs |

---

## Next

Continue to [Evaluation](./06-evaluation.md) to understand how to assess Reasoning Environment quality.

Reference implementations (like Upstream Agents) demonstrate these patterns in practice and are available separately.

