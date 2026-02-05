---
description: Create an Architecture Decision Record for significant decisions
argument-hint: <decision-title>
---

## Purpose

Research, evaluate, and document architectural decisions with explicit human approval. Prevents decision delegation by forcing structured consideration of alternatives before committing to an approach.

## CRITICAL: THIS IS A MULTI-TURN CONVERSATION

**You MUST stop and wait for user input at each phase boundary.**

This workflow has TWO mandatory checkpoints. You CANNOT skip them. You CANNOT combine phases. You CANNOT make the decision yourself.

If you find yourself writing a complete ADR with a decision without having asked the user which option to choose, **STOP IMMEDIATELY** -- you have violated the workflow.

---

## When to Use

- New abstraction or pattern being introduced
- Multiple viable implementation approaches with significant tradeoffs
- Technology or dependency choice
- Interface design that will be hard to change later
- Deviation from established codebase patterns
- Agent flags "this requires a decision" during implementation
- Reviewer spots undocumented architectural choice in PR

## Command

```
/adr <decision-title>
```

Optional context flag when triggered from an issue:
```
/adr <decision-title> --issue <issue-identifier>
```

---

## Phase 1: Research

### Steps

1. **Understand the decision context**
   - What problem requires a decision?
   - What constraints exist (technical, organizational, timeline)?
   - What does the codebase currently do in similar situations?

2. **Identify decision drivers**
   - What factors matter for this decision?
   - What is the relative weight of each factor?
   - Which constraints are hard vs soft?

3. **Identify options**
   - Research viable approaches (minimum 2, typically 3-4)
   - Include "do nothing" or "minimal change" if applicable
   - Consider both internal patterns and industry approaches

4. **Research each option via subagents**

   For each identified option, spawn a subagent:
   ```xml
   <new_task>
   <mode>Advanced</mode>
   <message>
   ## Objective
   Research best practices and tradeoffs for specific approach

   ## Option
   [Option name - e.g., "JWT authentication", "URL path versioning"]

   ## Domain
   [Context - e.g., "REST API", "microservices", "web application"]

   ## Output
   Write to: .agents/research/adr-option-<option-name>.md

   ## Instructions
   1. Web search for "[option] best practices [domain]"
   2. Find authoritative sources (official docs, well-known blogs)
   3. Document pros, cons, and gotchas
   4. Note any migration or adoption considerations
   5. Write full findings to file using standard format
   6. Return 1-2 sentence summary only
   </message>
   </new_task>
   ```

   Wait for all subagents, read summaries, synthesize into tradeoff analysis.

### Phase 1 Output

Present ONLY the decision drivers (NOT the full ADR, NOT the options yet):

> "I've identified this as an architectural decision about [topic].
>
> Decision drivers I found:
> - [Driver 1] (weight: High/Medium/Low)
> - [Driver 2] (weight: High/Medium/Low)
> - [Driver 3] (weight: High/Medium/Low)
>
> Are these the right factors? Should I:
> - **Proceed** - drivers look correct, continue to options analysis
> - **Adjust weights** - [tell me which to change]
> - **Add drivers** - [tell me what's missing]
> - **Remove drivers** - [tell me which aren't relevant]"

---

## ⛔ MANDATORY STOP 1

**DO NOT proceed to Phase 2 until the user confirms drivers.**

You have completed Phase 1. STOP HERE. Wait for the user to approve decision drivers.

**Violations:**
- ❌ Presenting options without user confirmation of drivers
- ❌ Writing the full ADR document
- ❌ Making a recommendation

---

## Phase 2: Present Options (ONLY after user confirms drivers)

### Analyze tradeoffs

- Pros and cons for each option
- How each option addresses the decision drivers
- Expected consequences (positive, negative, risks)

### Phase 2 Output

Present the options analysis with your recommendation:

> **Option 1: [Name]**
> - Pros: [key advantages]
> - Cons: [key disadvantages]
>
> **Option 2: [Name]**
> - Pros: [key advantages]
> - Cons: [key disadvantages]
>
> **Option 3: [Name]**
> - Pros: [key advantages]
> - Cons: [key disadvantages]
>
> **My Recommendation:** Option [X] because [brief reasoning]
>
> **Which option do you want to proceed with?**
> - **Option 1** - [one-line summary]
> - **Option 2** - [one-line summary]
> - **Option 3** - [one-line summary]
> - **Need more research** - [tell me what's unclear]
>
> Please select and provide your rationale for the Decision section.

---

## ⛔ MANDATORY STOP 2

**DO NOT proceed to Phase 3 until the user selects an option AND provides rationale.**

You have completed Phase 2. STOP HERE. Wait for the user to choose.

**Violations:**
- ❌ Picking an option yourself
- ❌ Writing the ADR without user selection
- ❌ Proceeding without user-provided rationale

---

## Phase 3: Record (ONLY after user selects option and provides rationale)

After human selects option and provides rationale:

1. Write the complete ADR document
2. Update status to **Accepted**
3. Save to `docs/architecture/adrs/NNN-<decision-title>.md`
4. If linked to issue, reference in issue file
5. Remind human to update ADR README table

### ADR Document Format

```markdown
# ADR-NNN: [Short Title]

## Status

**Accepted** - [date]

## Context

[What situation requires this decision? What constraints exist? What is the current state?]

## Decision Drivers

| Driver | Weight |
| ------ | ------ |
| [Factor 1] | High/Medium/Low |
| [Factor 2] | High/Medium/Low |
| [Factor 3] | High/Medium/Low |

## Options Considered

### Option 1: [Name]

**Pros:**
- Advantage 1
- Advantage 2

**Cons:**
- Disadvantage 1
- Disadvantage 2

### Option 2: [Name]

**Pros:**
- Advantage 1
- Advantage 2

**Cons:**
- Disadvantage 1
- Disadvantage 2

## Decision

**Use [Selected Option]** for [purpose/use case].

## Rationale

[User-provided rationale - numbered reasons why this option was chosen]

1. **Reason 1**: Explanation
2. **Reason 2**: Explanation

## Consequences

### Positive
- [Expected benefits]

### Negative
- [Expected downsides]

### Risks
- [Risks and mitigations]

## References

- [Relevant documentation, articles, or resources]
```

---

## Output Location

```
docs/architecture/adrs/
  001-canvas-library.md
  002-state-management.md
  003-api-versioning.md
```

## ADR Numbering

Sequential numbering (001, 002, etc.) maintained by checking existing files in `docs/architecture/adrs/`.

---

## Integration Points

### From Issue Planning
When defining an issue reveals architectural uncertainty:
```
/wf-01-issue-plan
# Agent identifies: "This issue requires deciding between REST and GraphQL"
# Agent suggests: "Consider running /adr api-query-approach before proceeding"
```

### From Design Phase
When design phase surfaces multiple viable approaches:
```
/design issue-52
# Agent identifies decision point
# Agent: "Multiple valid approaches identified. Run /adr <title> to evaluate?"
```

### From Implementation
When implementation reveals unexpected decision:
```
/wf-03-implement issue-52 task-2
# Agent: "STOP - This requires an architectural decision: [description]"
# Agent: "Run /adr <title> before continuing"
```

### From Cleanup/Review
When reviewer spots undocumented decision:
```
/wf-04-cleanup issue-52
# Audit item: "New abstraction introduced without ADR - UserPermissionResolver pattern"
# Human: "Create ADR for that"
# /adr user-permission-resolution --issue issue-52
```

---

## What Makes a Good ADR

1. **Focused** - One decision per ADR
2. **Options are real** - Not strawmen to justify predetermined choice
3. **Tradeoffs are honest** - Every option has cons
4. **Drivers weighted** - Clear what matters most
5. **Context preserved** - Future readers understand why this mattered
6. **Consequences documented** - Both positive and negative outcomes

## Anti-Patterns

| Anti-Pattern | Problem | Instead |
|--------------|---------|---------|
| Single option presented | No real decision, just documentation | Require minimum 2 viable options |
| Strawman alternatives | Decision already made, ADR is theater | Options must be genuinely viable |
| Novel-length ADR | Won't be read or maintained | Keep under 150 lines |
| ADR after implementation | Decision already locked in | ADR before code, or accept tech debt |
| Skipping rationale | No audit trail for why | Require human to state reasoning |
| ADR for trivial choices | Process overhead without value | Reserve for significant decisions |
| All drivers "High" weight | No real prioritization | Force ranking of what matters most |

## When NOT to Use

- Obvious choices with no real alternatives
- Following established codebase patterns exactly
- Decisions already documented elsewhere (RFC, design doc)
- Trivial implementation details

## Lifecycle

```
Proposed  ->  Accepted   ->  [Superseded by ADR-XXX]
          ->  Rejected       [Deprecated]
```

When a later decision changes an earlier one:
1. Update the original ADR status to **Superseded by ADR-XXX**
2. Link to the PR that made the change
3. Reference the original ADR in the new ADR's Context section

## Checklist Before Merge

When an ADR is accepted:

- [ ] Status updated to **Accepted** with PR link
- [ ] ADR README table updated with new entry
- [ ] AGENTS.md updated if ADR introduces new standards/constraints
- [ ] Related issue/task references added

$ARGUMENTS
