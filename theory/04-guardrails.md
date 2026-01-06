# Guardrails: Structural Constraints for Reliable Reasoning

*Part of the [Reasoning Environments](../README.md) thesis by Marcelus Fernandes*

---

## Abstract

Guardrails are the enforcement mechanism that makes Reasoning Environments reliable. Unlike prompts that ask models to "be careful," guardrails are structural constraints that prevent non-compliant outputs from being delivered. This document details guardrail design, implementation patterns, and enforcement protocols.

---

## The Problem with Advisory Constraints

Traditional approaches to LLM reliability rely on **advisory constraints**:

```
❌ "Please be careful not to make up statistics"
❌ "Try to cite your sources when possible"
❌ "Avoid overconfident language"
```

These fail because:
- Models optimize for fluency, not compliance
- Advisory language is probabilistically followed
- No enforcement mechanism exists
- Violations are invisible until downstream failure

---

## Structural vs. Advisory Constraints

| Aspect | Advisory Constraints | Structural Constraints |
|---|---|---|
| **Form** | "Please try to..." | "Output blocked if..." |
| **Enforcement** | None | Automatic |
| **Compliance** | Probabilistic | Guaranteed |
| **Violations** | Invisible | Explicit |
| **Recovery** | Manual review | Forced correction |

**Key insight:** Structural constraints don't ask the model to behave—they prevent misbehavior from reaching outputs.

---

## Guardrail Categories

### Category 1: Zero Tolerance (Critical)

Violations that **must never** appear in outputs.

**Examples:**

| Violation | Problem | Enforcement |
|---|---|---|
| Untagged dollar amounts | Invented financial claims | Block delivery |
| Specific percentages without source | Fabricated statistics | Block delivery |
| Invented quotes | Hallucinated evidence | Block delivery |
| Misattributed sources | Broken traceability | Block delivery |

**Response:** Output cannot be delivered until violation is corrected.

---

### Category 2: Warning (Requires Fix)

Issues that **should not** appear but may have edge cases.

**Examples:**

| Issue | Problem | Enforcement |
|---|---|---|
| Missing `[AI estimation]` tags | Hidden uncertainty | Flag and require correction |
| Vague source references | Weak traceability | Flag and require specificity |
| Overly confident language | Inappropriate certainty | Flag and require hedging |

**Response:** Output flagged, user must confirm correction or override.

---

### Category 3: Advisory (Best Practice)

Recommendations for **optimal** output quality.

**Examples:**

| Recommendation | Benefit | Enforcement |
|---|---|---|
| Add methodology detail | Transparency | Suggest improvement |
| Strengthen source attribution | Auditability | Suggest improvement |
| Use more conservative estimates | Safety margin | Suggest improvement |

**Response:** Recommendation shown, output allowed.

---

## Guardrail Implementation Patterns

### Pattern 1: Forbidden → Required Substitution

Every forbidden pattern has an approved alternative.

```markdown
## Financial Claims

❌ FORBIDDEN:
"$50,000 implementation cost"
"ROI of 200%"
"Saves $X annually"

✅ REQUIRED:
"Substantial investment [AI estimation: enterprise scope]"
"Strong ROI potential [AI estimation based on efficiency gains]"
"Significant annual value [AI estimation based on operational improvement]"
```

---

### Pattern 2: Mandatory Tagging

Certain content types require explicit tags.

| Content Type | Required Tag |
|---|---|
| Quantitative estimates | `[AI estimation based on...]` |
| Claims from documents | `[Source: filename.md]` |
| Inferences | `[Inference: ...]` |
| Assumptions | `[Assumption: ...]` |
| Uncertain information | `[Uncertain: requires validation]` |

---

### Pattern 3: Agent-Specific Enforcement

Different agents have different constraint levels.

**Problem Space Agents (Observation):**
- Extra strict: Analysis only, no speculation
- All claims must reference source files
- Conservative interpretation required
- Clear separation of facts vs. inferences

**Solution Space Agents (Decision):**
- Controlled estimation allowed
- Must use approved language patterns
- Conceptual focus, not technical specifications
- Conservative financial language required

---

## Enforcement Protocol

### Step 1: Detection

Scan output for guardrail violations:

```
🚨 GUARDRAIL VIOLATION DETECTED

Agent: Agent 6
Output File: /2-solution/opportunities.md
Violation Type: Critical

---

Violation 1: Financial Claim Without Source
❌ Found: "This will save $50,000 annually"
✅ Required: "Significant annual value [AI estimation based on operational improvement]"
Explanation: Dollar amounts must use qualitative language and AI estimation tags
```

### Step 2: Blocking

Prevent delivery of non-compliant output:

```
⛔ DELIVERY STATUS: BLOCKED

This output contains 2 critical violations and cannot be delivered.

Required Actions:
1. Apply suggested corrections above
2. Verify all quantitative claims have proper tagging
3. Confirm source attribution for all claims
4. Re-submit for validation
```

### Step 3: Correction

Apply fixes and re-validate:

```
✅ GUARDRAIL CHECK PASSED

All violations corrected.
Output ready for delivery.
```

---

## Validation Checklist

Before any output delivery, verify:

- [ ] No dollar amounts without `[AI estimation]` tag
- [ ] All percentages reference methodology or source
- [ ] Investment estimates use qualitative scale (Low/Medium/High)
- [ ] ROI projections use conservative language ("potential", "projected")
- [ ] Timeline estimates include complexity justification
- [ ] Resource estimates tagged with scope basis
- [ ] All claims traceable to sources or clearly marked as estimates
- [ ] Conservative language used throughout (avoid guarantees)

---

## Approved Language Patterns

### Financial Language

| ❌ Forbidden | ✅ Approved |
|---|---|
| "$50,000" | "Substantial investment" |
| "ROI of 200%" | "Strong ROI potential" |
| "Saves $X annually" | "Significant annual value" |
| "Costs $Y" | "Moderate/High investment" |

### Performance Language

| ❌ Forbidden | ✅ Approved |
|---|---|
| "75% reduction" | "Substantial reduction [AI estimation]" |
| "3x improvement" | "Significant improvement potential" |
| "50% faster" | "Notable performance enhancement" |
| "Guaranteed results" | "Expected improvement based on..." |

---

## The Guardrails Police

In a full Reasoning Environment implementation, a **Guardrails Police** agent operates continuously:

**Status:** Always active  
**Priority:** Highest—overrides other agents when violations detected  
**Scope:** All outputs in designated directories  

**Responsibilities:**
1. Real-time monitoring of output generation
2. Violation detection and categorization
3. Enforcement action execution
4. Correction guidance provision
5. Delivery gate control

---

## Why This Works

Guardrails work because they shift the burden from:
- **Model behavior** (unpredictable) to **Environment enforcement** (deterministic)
- **Advisory prompts** (probabilistic) to **Structural blocks** (guaranteed)
- **Post-hoc review** (late) to **Pre-delivery validation** (early)

The model doesn't need to "want" to comply—non-compliance simply doesn't reach outputs.

---

## Next

Continue to [Traceability](./05-traceability.md) to understand how reasoning chains are maintained.

Reference implementations (like Upstream Agents) demonstrate these patterns in practice and are available separately.

