# Core Principles of Reasoning Environments

*Part of the [Reasoning Environments](../README.md) thesis by Marcelus Fernandes*

---

## Abstract

This document defines the six core principles that constitute a Reasoning Environment. These principles shift interpretability from a model property to an environment property, enabling trust through visible limits rather than through explanation.

---

## The Core Insight

> **Interpretability is not a universal property.  
> It is a situated property of environments.**

> **Reliability is not a property of models alone.  
> It emerges from capable models operating in structured environments.**

Instead of trying to make models interpretable or capable enough in isolation, responsibility shifts to the environment in which reasoning happens.

**Important:** These principles assume capable models. Reasoning Environments channel and make trustworthy what models can already do—they do not replace the need for model capability.

---

## The Six Principles

### Principle 1: Context is Explicit

**Statement:** Context must be explicitly provided, never assumed or inferred.

**Rationale:**
- Models fill gaps with training priors, not situational awareness
- Implicit context leads to invisible failure modes
- Explicit context enables auditing and debugging

**Implementation:**
- Mandatory context documents before reasoning begins
- Structured input formats that force context specification
- Validation that required context fields are populated

**Anti-pattern:** "The model should understand from context..."

---

### Principle 2: Cognitive Separation

**Statement:** Observation, interpretation, and decision must be separated into distinct stages.

**Rationale:**
- Collapsing cognitive types leads to untraceable reasoning
- Separation enables stage-specific validation
- Different cognitive types require different constraints

**Implementation:**
- One agent = one type of reasoning
- Mandatory intermediate outputs between stages
- No agent "solves everything"

**The Three Stages:**

| Stage | Purpose | Output |
|---|---|---|
| **Observation** | Extract what exists | Raw signals, quotes, data |
| **Interpretation** | Analyze what it means | Patterns, clusters, insights |
| **Decision** | Determine what to do | Recommendations, priorities |

**Anti-pattern:** Single prompt that extracts, analyzes, and recommends.

---

### Principle 3: Preserved Uncertainty

**Statement:** Uncertainty must be preserved and propagated, never collapsed into fluent answers.

**Rationale:**
- Fluent answers hide epistemic state
- Collapsed uncertainty prevents appropriate trust calibration
- Preserved uncertainty enables downstream decision-making

**Implementation:**
- Mandatory uncertainty tags: `[AI estimation based on...]`
- Confidence levels as structured outputs
- Explicit "unknown" as valid output

**Anti-pattern:** "Based on the data, the clear conclusion is..."

---

### Principle 4: Structural Constraints

**Statement:** Overconfidence must be constrained structurally, not rhetorically softened.

**Rationale:**
- Rhetorical hedging ("may", "might") doesn't change behavior
- Structural constraints prevent forbidden outputs
- Enforcement must be automatic, not advisory

**Implementation:**
- Guardrails that block non-compliant outputs
- Validation rules with zero tolerance violations
- Delivery blocked until constraints satisfied

**Anti-pattern:** "Please be careful not to overstate..."

---

### Principle 5: Epistemic Limits as Outputs

**Statement:** The limits of what can be known must be first-class outputs, not footnotes.

**Rationale:**
- Knowing what you don't know is as valuable as knowing
- Limits define the validity boundary of conclusions
- Explicit limits enable appropriate downstream use

**Implementation:**
- `[Assumption: ...]` tags for unstated premises
- `[Uncertain: requires validation]` for low-confidence claims
- Explicit "out of scope" declarations

**Output Examples:**
```
✅ "Users report delays [Source: interview-3.md]"
✅ "Frequency not explicitly stated in source"
✅ "This conclusion assumes X; if X is false, conclusion may not hold"
```

**Anti-pattern:** Burying limitations in final paragraphs.

---

### Principle 6: Traceable Artifacts

**Statement:** Every decision must leave traceable reasoning artifacts.

**Rationale:**
- Traceability enables auditing
- Artifacts enable debugging and improvement
- Reasoning chains create accountability

**Implementation:**
- Source attribution for every claim: `[Source: filename.md]`
- Intermediate outputs stored, not discarded
- Decision rationale captured alongside decision

**Traceability Chain:**
```
Interview quote → Pain point extraction → Cluster assignment → 
Opportunity identification → Prioritization decision → Final recommendation
```

Each link must be auditable.

**Anti-pattern:** Final outputs without reasoning history.

---

## Next

Continue to [Architecture](./03-architecture.md) to see how these principles are implemented in practice.

