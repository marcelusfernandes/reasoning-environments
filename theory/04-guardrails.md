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

## Pseudocode: Guardrail Implementation

### Guardrail Validator

```pseudocode
function validate_output(output, guardrails):
    violations = []
    
    for rule in guardrails.critical:
        matches = find_pattern(output, rule.forbidden_pattern)
        for match in matches:
            violations.append({
                severity: "CRITICAL",
                rule: rule.name,
                found: match.text,
                location: match.position,
                required: rule.approved_alternative,
                auto_fix: generate_fix(match, rule)
            })
    
    for rule in guardrails.warning:
        matches = find_pattern(output, rule.pattern)
        for match in matches:
            violations.append({
                severity: "WARNING",
                rule: rule.name,
                found: match.text,
                suggestion: rule.suggestion
            })
    
    return violations
```

### Enforcement Gate

```pseudocode
function enforce_guardrails(output, violations):
    critical_violations = filter(violations, severity == "CRITICAL")
    
    if len(critical_violations) > 0:
        # Block delivery
        display_violations(critical_violations)
        display_auto_fixes(critical_violations)
        
        return {
            status: "BLOCKED",
            message: "Output contains critical violations",
            violations: critical_violations,
            next_action: "Apply fixes and re-submit"
        }
    
    warnings = filter(violations, severity == "WARNING")
    
    if len(warnings) > 0:
        display_warnings(warnings)
        
        if not user_confirms_override():
            return {
                status: "PENDING",
                message: "Warnings require acknowledgment",
                warnings: warnings
            }
    
    # Output approved
    return {
        status: "APPROVED",
        message: "Guardrail check passed"
    }
```

### Pattern Detection Rules

```pseudocode
guardrails = {
    critical: [
        {
            name: "untagged_dollar_amount",
            forbidden_pattern: /\$[\d,]+(?!\s*\[)/,
            approved_alternative: "Qualitative language + [AI estimation] tag",
            auto_fix: lambda match: replace_with_qualitative(match)
        },
        {
            name: "untagged_percentage",
            forbidden_pattern: /\d+%\s+(?:reduction|improvement|increase)(?!\s*\[)/,
            approved_alternative: "Conservative language + [AI estimation] tag",
            auto_fix: lambda match: replace_with_conservative(match)
        },
        {
            name: "unsourced_claim",
            forbidden_pattern: /"[^"]+"\s+(?!.*\[Source:)/,
            approved_alternative: "Add [Source: filename.md] attribution",
            auto_fix: lambda match: prompt_for_source(match)
        }
    ],
    warning: [
        {
            name: "overconfident_language",
            pattern: /\b(will|guaranteed|definitely|certainly)\b/i,
            suggestion: "Use 'may', 'potential', 'expected' instead"
        },
        {
            name: "missing_estimation_tag",
            pattern: /\b(estimate|approximately|around|roughly)\b(?!\s*\[)/i,
            suggestion: "Add [AI estimation based on...] tag"
        }
    ]
}
```

### Agent-Specific Rules

```pseudocode
function get_agent_guardrails(agent_type):
    base_rules = get_common_guardrails()
    
    if agent_type == "observation":
        # Problem space: extra strict
        return base_rules + [
            {
                name: "no_speculation",
                forbidden_pattern: /\b(suggests|implies|indicates|likely)\b/,
                message: "Observation agents report facts only"
            },
            {
                name: "mandatory_source",
                required_pattern: /\[Source: [^\]]+\]/,
                message: "Every claim must have source attribution"
            }
        ]
    
    elif agent_type == "decision":
        # Solution space: controlled estimation
        return base_rules + [
            {
                name: "conservative_estimates",
                required_pattern: /\[AI estimation based on/,
                message: "Estimates must include methodology"
            },
            {
                name: "no_guarantees",
                forbidden_pattern: /\b(guaranteed|will definitely|certain to)\b/,
                message: "Use potential-based language"
            }
        ]
    
    return base_rules
```

---

## Next

Continue to [Traceability](./05-traceability.md) to understand how reasoning chains are maintained.

Reference implementations (like Upstream Agents) demonstrate these patterns in practice and are available separately.

