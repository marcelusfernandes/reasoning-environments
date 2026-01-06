# Architecture of a Reasoning Environment

*Part of the [Reasoning Environments](../README.md) thesis by Marcelus Fernandes*

---

## Abstract

This document describes the architectural components of a Reasoning Environment. The architecture implements the six core principles through specific structural patterns, creating an environment where LLM reasoning becomes reliable, traceable, and auditable.

---

## The OS Analogy

A Reasoning Environment relates to an LLM like an operating system relates to a CPU:

| Component | Analogy | Function |
|---|---|---|
| **Model** | CPU | Raw processing power |
| **Context Window** | RAM | Limited working memory |
| **Reasoning Environment** | Operating System | Governs operation, curates context, provides drivers |
| **Agents** | Applications | Specific logic running on top of the OS |

**Key insight:** Just as an OS requires a capable CPU, a Reasoning Environment requires a capable model. You cannot run a modern OS on a 1980s processor—and you cannot run a Reasoning Environment on a weak model.

The model is interchangeable *among capable models*. System reliability emerges from the combination of model capability and environment structure.

Changing to a weaker model breaks the system.  
Changing to a different capable model may work.  
Changing the environment changes what reliability means.

---

## Core Components

### 1. The Orchestrator

The orchestrator is the "boot sequence" and "kernel" of the Reasoning Environment.

**Responsibilities:**
- Initialize workflow state
- Validate dependencies before each stage
- Curate context for each agent
- Manage lifecycle hooks (pre/post execution)
- Handle errors and recovery
- Maintain workflow resumability

**Lifecycle Hooks:**

| Hook | Timing | Purpose |
|---|---|---|
| **Pre-execution** | Before agent runs | Display guardrails, validate inputs |
| **Dependency check** | Before agent runs | Ensure required outputs exist |
| **Completion check** | After agent runs | Validate outputs were created correctly |
| **Error handling** | On failure | Provide recovery options |
| **Post-execution** | After agent runs | Summarize results, suggest next steps |

---

### 2. Specialized Agents

Each agent handles **one type of reasoning** under explicit constraints.

**Design Principles:**
- Single responsibility per agent
- Explicit input/output contracts
- Mandatory intermediate outputs
- No agent "solves everything"

**Agent Anatomy:**

```markdown
# Agent [N]: [Name]

## Role
[Single-sentence description of cognitive function]

## Input
[Explicit list of required inputs]

## Output
[Explicit list of required outputs]

## Constraints
[Rules this agent must follow]

## Definition of Done
[Checklist for completion validation]
```

**Cognitive Separation Pattern:**

```
Agent A (Observe) → Agent B (Interpret) → Agent C (Decide)
     ↓                    ↓                    ↓
  Raw data            Patterns             Recommendations
  Quotes              Clusters             Priorities
  Signals             Insights             Actions
```

---

### 3. Guardrails System

Guardrails are **structural constraints**, not suggestions.

**Guardrail Types:**

| Type | Severity | Action |
|---|---|---|
| **Critical** | Zero tolerance | Block delivery |
| **Warning** | Requires fix | Flag and require correction |
| **Advisory** | Best practice | Recommend improvement |

**Implementation Pattern:**

```markdown
## Zero Tolerance Violations

❌ FORBIDDEN:
- Untagged dollar amounts
- Specific percentages without source
- Invented quotes

✅ REQUIRED:
- `[Source: filename.md]` for all claims
- `[AI estimation based on...]` for quantitative statements
- `[Assumption: ...]` for inferences

⛔ DELIVERY STATUS: BLOCKED until corrections applied
```

**Key Insight:** Guardrails are not prompts asking the model to "be careful." They are enforcement mechanisms that prevent non-compliant outputs from being delivered.

---

### 4. Intermediate State Storage

Every stage produces **mandatory intermediate outputs**.

**Benefits:**
- Enables stage-specific auditing
- Allows workflow resumption
- Creates traceable reasoning chain
- Supports debugging and improvement

**Storage Pattern:**

```
/1-problem/
  /1a-interview-analysis/     ← Stage 1 output
  /1b-painpoints/
    /1b0-granular/            ← Stage 2a output
    /1b1-clusters/            ← Stage 2b output
  /1c-journey/                ← Stage 3 output
  /1d-output/                 ← Final stage output
```

**Anti-pattern:** Pipelines that only store final outputs.

---

### 5. Validation Layer

Cross-step validations ensure coherence across the reasoning chain.

**Validation Types:**

| Type | Scope | Example |
|---|---|---|
| **Completeness** | Single output | All required fields populated |
| **Consistency** | Cross-output | Pain points in clusters match extracted pain points |
| **Traceability** | Chain-wide | Every claim traceable to source |
| **Constraint compliance** | All outputs | No guardrail violations |

---

## Design Patterns

### Pattern 1: Progressive Decomposition

Complex problems are broken into smaller, analyzable units across stages.

```
Complex Problem
     ↓
Stage 1: Extract components
     ↓
Stage 2: Analyze components independently
     ↓
Stage 3: Synthesize findings
     ↓
Actionable Insight
```

Each stage reduces ambiguity rather than producing final answers.

---

### Pattern 2: Observation → Interpretation → Decision

The three fundamental cognitive types are strictly separated.

| Stage | Agent Behavior | Output Type |
|---|---|---|
| **Observation** | Extract without analyzing | Data, quotes, signals |
| **Interpretation** | Analyze without recommending | Patterns, clusters, insights |
| **Decision** | Recommend based on analysis | Priorities, actions, trade-offs |

**Enforcement:** Agents are explicitly forbidden from performing cognitive operations outside their stage.

---

### Pattern 3: Mandatory Intermediate Outputs

No stage produces only internal state—all reasoning is externalized.

**Benefits:**
- Human-auditable at every stage
- Debuggable when failures occur
- Resumable after interruption
- Improvable through iteration

---

### Pattern 4: Contract-Based Handoffs

Each agent-to-agent handoff has an explicit contract.

```markdown
## Contract: Agent 1 → Agent 2

### Agent 1 Must Provide:
- Pain points with: quote, context, frequency, severity, impact
- Source attribution for each pain point
- No type classification (Agent 2's responsibility)

### Agent 2 Expects:
- Structured pain point format
- Exhaustive extraction (no filtering)
- Source references for validation
```

---

## The Fundamental Trade-off

> **General intelligence scales with models.  
> Reliable intelligence scales with environments.**

Architecture choices reflect this trade-off:

| Approach | Scales With | Trade-off |
|---|---|---|
| Bigger models | Model capability | Unpredictable failure modes |
| Better prompts | Prompt engineering skill | Fragile, model-dependent |
| Reasoning Environments | Environment design | Requires capable model + upfront design |

**Reasoning Environments do not replace model capability—they channel it.**

| Configuration | Result |
|---|---|
| Weak model + any environment | Failure |
| Capable model + no structure | Sophisticated unreliability |
| Capable model + structured environment | Reliable, auditable reasoning |

The architecture assumes capable models and provides the structure to make their capabilities trustworthy.

---

## Pseudocode: Core Patterns

### Orchestrator Main Loop

```pseudocode
function run_workflow(context, agents):
    state = initialize_workflow(context)
    
    for agent in agents:
        # Pre-execution hooks
        display_guardrails(agent.constraints)
        validate_dependencies(agent.required_inputs, state)
        
        if not dependencies_satisfied:
            return error("Missing dependencies for " + agent.name)
        
        # Execute with curated context
        curated_context = filter_context(state, agent.context_window)
        result = agent.execute(curated_context)
        
        # Post-execution validation
        violations = check_guardrails(result, agent.constraints)
        
        if violations.has_critical():
            block_output(result, violations)
            return error("Critical guardrail violations")
        
        # Store intermediate output
        state.outputs[agent.name] = result
        save_to_directory(result, agent.output_path)
        
        # Completion hooks
        log_completion(agent.name, result.summary)
    
    return state.outputs
```

### Cognitive Separation

```pseudocode
# Agent A: Observe (extract without analyzing)
function observe(sources):
    observations = []
    for source in sources:
        for statement in extract_statements(source):
            observations.append({
                content: statement.text,
                source: source.path,
                line: statement.line,
                context: statement.surrounding_context
            })
    # NO interpretation, NO filtering, NO classification
    return observations

# Agent B: Interpret (analyze without recommending)
function interpret(observations):
    patterns = []
    for observation in observations:
        pattern = classify_pattern(observation)
        patterns.append({
            observation_id: observation.id,
            pattern_type: pattern,
            confidence: calculate_confidence(observation),
            supporting_evidence: [observation.source]
        })
    # NO recommendations, NO priorities
    return group_into_clusters(patterns)

# Agent C: Decide (recommend based on analysis)
function decide(interpretations):
    recommendations = []
    for cluster in interpretations:
        priority = calculate_priority(cluster)
        recommendations.append({
            action: derive_action(cluster),
            priority: priority,
            supporting_clusters: [cluster.id],
            trade_offs: identify_trade_offs(cluster),
            uncertainty: "[AI estimation based on cluster analysis]"
        })
    return rank_by_priority(recommendations)
```

### Contract-Based Handoff

```pseudocode
# Contract definition
contract Agent1_to_Agent2:
    required_fields = [
        "id",
        "quote",
        "source_file",
        "source_line", 
        "context",
        "frequency",
        "severity"
    ]
    forbidden_fields = [
        "type",          # Agent 2's responsibility
        "priority",      # Agent 2's responsibility
        "recommendation" # Agent 3's responsibility
    ]

# Handoff validation
function validate_handoff(output, contract):
    for field in contract.required_fields:
        if field not in output:
            return error("Missing required field: " + field)
    
    for field in contract.forbidden_fields:
        if field in output:
            return error("Forbidden field present: " + field)
    
    return success()
```

---

## Next

Continue to [Guardrails Deep Dive](./04-guardrails.md) for detailed guardrail implementation patterns.

Reference implementations (like Upstream Agents) demonstrate these patterns in practice and are available separately.

