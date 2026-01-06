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

## Pseudocode: Traceability Implementation

### Source Attribution System

```pseudocode
class TraceableEntity:
    id: string           # Unique identifier (e.g., "PP-12")
    content: string      # The actual content
    sources: List[SourceReference]
    derived_from: List[EntityReference]
    referenced_by: List[EntityReference]

class SourceReference:
    file_path: string
    line_number: int?
    quote: string?
    context: string?

function create_traceable_entity(content, sources):
    entity = TraceableEntity(
        id: generate_unique_id(),
        content: content,
        sources: validate_sources(sources),
        derived_from: [],
        referenced_by: []
    )
    
    # Validate source attribution
    if len(entity.sources) == 0:
        raise Error("Entity must have at least one source")
    
    for source in entity.sources:
        if not file_exists(source.file_path):
            raise Error("Source file not found: " + source.file_path)
    
    return entity
```

### Chain Builder

```pseudocode
function build_traceability_chain(final_output):
    chain = TraceabilityChain()
    
    # Start from final output and trace backwards
    current_entities = [final_output]
    
    while len(current_entities) > 0:
        next_level = []
        
        for entity in current_entities:
            chain.add_node(entity)
            
            # Add links to sources
            for source in entity.sources:
                chain.add_link(entity.id, source.file_path, "sourced_from")
            
            # Add links to derived entities
            for parent in entity.derived_from:
                chain.add_link(entity.id, parent.id, "derived_from")
                next_level.append(parent)
        
        current_entities = next_level
    
    return chain
```

### Chain Validation

```pseudocode
function validate_chain(chain):
    errors = []
    
    # Check for orphan nodes (no sources)
    for node in chain.nodes:
        if len(node.sources) == 0 and len(node.derived_from) == 0:
            errors.append({
                type: "ORPHAN_NODE",
                node: node.id,
                message: "Node has no sources or parent references"
            })
    
    # Check for broken links
    for link in chain.links:
        if not chain.has_node(link.target):
            errors.append({
                type: "BROKEN_LINK",
                source: link.source,
                target: link.target,
                message: "Link target does not exist"
            })
    
    # Check source validity
    for node in chain.nodes:
        for source in node.sources:
            if not source_contains_content(source, node.content):
                errors.append({
                    type: "SOURCE_MISMATCH",
                    node: node.id,
                    source: source.file_path,
                    message: "Source does not contain claimed content"
                })
    
    return errors
```

### Audit Trail Generator

```pseudocode
function generate_audit_trail(recommendation):
    trail = AuditTrail(recommendation)
    
    # Level 1: Direct supporting evidence
    for evidence in recommendation.supporting_evidence:
        trail.add_level(1, evidence)
    
    # Level 2: Trace to interpretations
    for evidence in trail.level(1):
        interpretations = get_derived_from(evidence)
        for interp in interpretations:
            trail.add_level(2, interp)
    
    # Level 3: Trace to observations
    for interp in trail.level(2):
        observations = get_derived_from(interp)
        for obs in observations:
            trail.add_level(3, obs)
    
    # Level 4: Trace to original sources
    for obs in trail.level(3):
        for source in obs.sources:
            trail.add_level(4, source)
    
    return trail

function format_audit_trail(trail):
    output = "## Audit Trail for: " + trail.root.id + "\n\n"
    
    output += "### Recommendation\n"
    output += trail.root.content + "\n\n"
    
    output += "### Supporting Evidence (Level 1)\n"
    for item in trail.level(1):
        output += "- " + item.id + ": " + item.summary + "\n"
    
    output += "\n### Interpretations (Level 2)\n"
    for item in trail.level(2):
        output += "- " + item.id + " [derived from: " + item.derived_from + "]\n"
    
    output += "\n### Observations (Level 3)\n"
    for item in trail.level(3):
        output += "- " + item.id + " [Source: " + item.sources[0].file_path + "]\n"
    
    output += "\n### Original Sources (Level 4)\n"
    for source in trail.level(4):
        output += "- " + source.file_path + "\n"
    
    return output
```

### Cross-Reference Index

```pseudocode
function build_cross_reference_index(all_entities):
    index = CrossReferenceIndex()
    
    for entity in all_entities:
        # Index by ID
        index.by_id[entity.id] = entity
        
        # Index by source
        for source in entity.sources:
            if source.file_path not in index.by_source:
                index.by_source[source.file_path] = []
            index.by_source[source.file_path].append(entity.id)
        
        # Index by type
        entity_type = get_type(entity)  # PP, CLU, OPP, etc.
        if entity_type not in index.by_type:
            index.by_type[entity_type] = []
        index.by_type[entity_type].append(entity.id)
    
    # Build reverse references
    for entity in all_entities:
        for parent_id in entity.derived_from:
            parent = index.by_id[parent_id]
            parent.referenced_by.append(entity.id)
    
    return index
```

---

## Next

Continue to [Evaluation](./06-evaluation.md) to understand how to assess Reasoning Environment quality.

Reference implementations (like Upstream Agents) demonstrate these patterns in practice and are available separately.

