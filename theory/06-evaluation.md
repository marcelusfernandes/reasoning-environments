# Evaluating Reasoning Environments

*Part of the [Reasoning Environments](../README.md) thesis by Marcelus Fernandes*

---

## Abstract

Traditional AI evaluation focuses on model capability: can it solve this benchmark? Reasoning Environment evaluation focuses on system reliability: can reasoning be trusted in this context? This document describes evaluation dimensions, metrics, and assessment procedures for Reasoning Environments.

---

## The Evaluation Shift

| Traditional Evaluation | Reasoning Environment Evaluation |
|---|---|
| Can the model do this? | Can reasoning be trusted here? |
| Accuracy on test set | Reliability in context |
| Model performance | Environment quality |
| Output correctness | Process auditability |

---

## Evaluation Dimensions

### Dimension 1: Traceability

**Question:** Can every output be traced to its sources?

**Metrics:**
- % of claims with source attribution
- Chain completeness (source → output links)
- Orphan claim count (claims without sources)

**Assessment:**
```
✅ High: 100% of claims traceable, complete chains
⚠️ Medium: 90%+ traceable, minor gaps
❌ Low: Significant untraceable claims
```

---

### Dimension 2: Uncertainty Preservation

**Question:** Is uncertainty visible in outputs?

**Metrics:**
- % of estimates with uncertainty tags
- Confidence calibration (stated vs. actual)
- Presence of epistemic limit declarations

**Assessment:**
```
✅ High: All estimates tagged, limits explicit
⚠️ Medium: Most estimates tagged, some implicit confidence
❌ Low: Fluent answers hiding uncertainty
```

---

### Dimension 3: Constraint Compliance

**Question:** Are guardrails enforced?

**Metrics:**
- Zero tolerance violation count
- Warning violation count
- Correction success rate

**Assessment:**
```
✅ High: Zero critical violations, warnings addressed
⚠️ Medium: Rare violations, corrected before delivery
❌ Low: Frequent violations, some reaching outputs
```

---

### Dimension 4: Cognitive Separation

**Question:** Are observation, interpretation, and decision separated?

**Metrics:**
- Stage contamination incidents (e.g., Agent 1 recommending)
- Intermediate output completeness
- Cross-stage bleeding

**Assessment:**
```
✅ High: Clean separation, complete intermediates
⚠️ Medium: Occasional contamination, caught by review
❌ Low: Stages routinely blur
```

---

### Dimension 5: Reproducibility

**Question:** Does the same input produce consistent outputs?

**Metrics:**
- Cross-run consistency (same inputs, similar outputs)
- Structural consistency (output format stability)
- Reasoning consistency (similar reasoning paths)

**Assessment:**
```
✅ High: Outputs structurally identical, reasoning similar
⚠️ Medium: Minor variations, same conclusions
❌ Low: Significant variation across runs
```

---

### Dimension 6: Auditability

**Question:** Can a human follow the reasoning?

**Metrics:**
- Audit completion time
- Auditor comprehension (can explain reasoning)
- Issue detection rate (auditors find planted issues)

**Assessment:**
```
✅ High: Auditors trace reasoning quickly, find issues
⚠️ Medium: Reasoning followable with effort
❌ Low: Reasoning opaque or tangled
```

---

## Evaluation Procedures

### Procedure 1: Traceability Audit

**Steps:**
1. Select 10 random claims from final output
2. For each claim:
   - Follow source attribution
   - Verify source contains claimed information
   - Check chain completeness to origin
3. Score: % of claims with complete, valid chains

**Scoring:**
- 10/10: Exemplary
- 8-9/10: Strong
- 6-7/10: Acceptable
- <6/10: Needs improvement

---

### Procedure 2: Guardrail Stress Test

**Steps:**
1. Introduce intentional guardrail violations in intermediate outputs
2. Run subsequent stages
3. Check if violations are detected and blocked
4. Check if violations propagate to final output

**Scoring:**
- All violations caught: Robust
- Most violations caught: Adequate
- Violations reach output: Failing

---

### Procedure 3: Cognitive Separation Test

**Steps:**
1. Review Agent 1 (observation) outputs
   - Are there recommendations? (violation)
   - Are there interpretations? (violation)
2. Review Agent 2 (interpretation) outputs
   - Are there unsupported claims? (violation)
   - Are there action recommendations? (violation)
3. Score: % of outputs with clean cognitive separation

---

### Procedure 4: Uncertainty Calibration

**Steps:**
1. Identify all claims with confidence indicators
2. Categorize: high confidence, medium confidence, low confidence
3. Assess actual correctness of claims in each category
4. Calculate calibration: do high-confidence claims have higher accuracy?

**Good calibration:**
- High confidence claims: ~90% correct
- Medium confidence claims: ~70% correct
- Low confidence claims: ~50% correct

**Poor calibration:**
- All claims similar accuracy regardless of stated confidence

---

## Quality Gates

Before considering a Reasoning Environment production-ready:

### Gate 1: Structural Completeness
- [ ] All agents defined with clear scope
- [ ] All intermediate outputs specified
- [ ] All guardrails implemented
- [ ] Orchestrator manages full lifecycle

### Gate 2: Traceability
- [ ] 100% of claims have source attribution
- [ ] Complete chains from source to output
- [ ] No orphan claims in final outputs

### Gate 3: Reliability
- [ ] Zero critical guardrail violations in outputs
- [ ] Uncertainty properly preserved
- [ ] Consistent outputs across runs

### Gate 4: Auditability
- [ ] Reasoning followable by humans
- [ ] Issues detectable through audit
- [ ] Improvement paths identifiable

---

## Comparison: Before and After

| Metric | Without RE | With RE |
|---|---|---|
| Traceability | ~10% (implicit) | ~100% (explicit) |
| Uncertainty visibility | Low | High |
| Reproducibility | Variable | Structured |
| Audit time | Hours (if possible) | Minutes |
| Failure debugging | Guess and check | Trace and identify |

---

## Continuous Evaluation

Reasoning Environments should be continuously evaluated:

1. **Per-run auditing:** Sample claims for traceability check
2. **Periodic stress testing:** Quarterly guardrail stress tests
3. **Calibration monitoring:** Track confidence vs. accuracy over time
4. **User feedback integration:** Capture downstream utility of outputs

---

## The Ultimate Test

The ultimate evaluation question is not about the environment—it's about the humans using outputs:

> **Do users of this Reasoning Environment make better decisions than users of unstructured LLM outputs?**

This requires:
- Appropriate trust (not over-trusting fluent outputs)
- Actionable uncertainty (knowing when to seek more information)
- Traceable reasoning (ability to validate and question)

---

## Next

Reference implementations (like Upstream Agents) demonstrate these principles in practice and are available separately.

