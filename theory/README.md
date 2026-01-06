# Theory: Foundations of Reasoning Environments

*Part of the [Reasoning Environments](../README.md) thesis*

---

## Overview

This directory contains the theoretical foundations of Reasoning Environments. These documents provide the principles and design patterns that inform all implementations.

---

## Reading Order

For a comprehensive understanding, read in this order:

| # | Document | Topic |
|---|---|---|
| 1 | [Problem Statement](./01-problem-statement.md) | Why universal interpretability fails |
| 2 | [Core Principles](./02-core-principles.md) | The six principles of bounded reasoning |
| 3 | [Architecture](./03-architecture.md) | Structural components of a Reasoning Environment |
| 4 | [Guardrails](./04-guardrails.md) | Structural constraints for reliability |
| 5 | [Traceability](./05-traceability.md) | Auditable reasoning chains |
| 6 | [Evaluation](./06-evaluation.md) | How to assess Reasoning Environment quality |

---

## Quick Reference

### The Core Insight

> **Interpretability is not a universal property.  
> It is a situated property of environments.**

### The Six Principles

1. **Context is explicit** — Not assumed or inferred
2. **Cognitive separation** — Observation, interpretation, decision separated
3. **Uncertainty preserved** — Not collapsed into fluent answers
4. **Structural constraints** — Overconfidence constrained by design
5. **Epistemic limits surfaced** — Limits are first-class outputs
6. **Traceable artifacts** — Every decision leaves reasoning traces

### The Fundamental Trade-off

> **General intelligence scales with models.  
> Reliable intelligence scales with environments.**

---

## For Different Readers

### For Researchers
Start with [Problem Statement](./01-problem-statement.md) to understand the theoretical motivation.

### For Practitioners
Start with [Architecture](./03-architecture.md) to understand implementation patterns.

### For Safety-Focused
Start with [Guardrails](./04-guardrails.md) to understand constraint mechanisms.

---

## Next Steps

Reference implementations (like Upstream Agents) demonstrate these principles in practice and are available separately.

