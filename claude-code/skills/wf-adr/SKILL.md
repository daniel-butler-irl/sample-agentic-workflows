---
description: ADR - Architectural Decision Record
---

## Purpose

Research, evaluate, and document architectural decisions with explicit human approval. Prevents decision delegation by forcing structured consideration of alternatives before committing to an approach.

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

## What the Agent Does

### Phase 1: Research

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

   For each identified option, spawn a Task tool with subagent_type="general-purpose":
   ```
   Task tool:
     subagent_type: "general-purpose"
     prompt: |
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
   ```

   Wait for all subagents, read summaries, synthesize into tradeoff analysis.

5. **Analyze tradeoffs**
   - Pros and cons for each option
   - How each option addresses the decision drivers
   - Expected consequences (positive, negative, risks)

---

## MANDATORY STOP 1: After Research

**STOP HERE. Do not proceed until user responds.**

After completing research (Phase 1), ask the user:

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

**Wait for user response before proceeding to Phase 2.**

---

### Phase 2: Present

Present findings using the project ADR template format:

```markdown
# ADR-NNN: [Short Title]

## Status

**Proposed**

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

### Option 3: [Name]

**Pros:**

- Advantage 1
- Advantage 2

**Cons:**

- Disadvantage 1
- Disadvantage 2

---

## Agent Recommendation

**Recommended:** Option [X]

**Reasoning:** [Why this option best addresses the decision drivers]

**Caveats:** [What could make this the wrong choice]

---

## MANDATORY STOP 2: After Presenting Options

**STOP HERE. Do not proceed until user responds.**

After presenting the ADR with options and recommendation, ask the user:

> "Here are the options I've researched with my recommendation above.
>
> **Which option do you want to proceed with?**
> - **Option 1: [name]** - [one-line summary]
> - **Option 2: [name]** - [one-line summary]
> - **Option 3: [name]** - [one-line summary]
> - **Need more research** - [tell me what's unclear]
>
> Please select and provide your rationale for the Decision section."

**Wait for user response before proceeding to Phase 3 (Record).**

---

## Decision

**Use [Selected Option]** for [purpose/use case].

[Human fills in after selecting]

## Rationale

[Human fills in - numbered reasons why this option was chosen]

1. **Reason 1**: Explanation
2. **Reason 2**: Explanation

## Consequences

### Positive

- [Human fills in expected benefits]

### Negative

- [Human fills in expected downsides]

### Risks

- [Human fills in risks and mitigations]

## References

- [Relevant documentation, articles, or resources]
```

### Phase 3: Record

After human selects option and completes Decision/Rationale/Consequences sections:

1. Update status to **Accepted**
2. Save to `docs/architecture/adrs/NNN-<decision-title>.md`
3. If linked to issue, reference in issue file
4. If during implementation, add reference to task's Implementation Notes
5. Remind human to update ADR README table

## Output Location

```
docs/architecture/adrs/
  001-canvas-library.md
  002-state-management.md
  003-api-versioning.md
```

## ADR Numbering

Sequential numbering (001, 002, etc.) maintained by checking existing files in `docs/architecture/adrs/`.

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

## Example

```markdown
# ADR-022: API Versioning Pattern

## Status

**Accepted** - [PR #847](https://github.com/org/repo/pull/847)

## Context

We need to version our public API. Breaking changes are planned for Q3.
Current API has 47 endpoints used by 12 external integrations.
Team has no established pattern for API versioning.

## Decision Drivers

| Driver | Weight |
| ------ | ------ |
| Developer familiarity | High |
| Debuggability | High |
| REST purity | Low |
| Infrastructure complexity | Medium |

## Options Considered

### Option 1: URL Path Versioning

**Pros:**

- Explicit and visible in logs
- Easy to route at infrastructure level
- Industry standard (GitHub, Stripe)
- Clear which version client is using

**Cons:**

- URL changes between versions
- Can lead to code duplication
- Clients must update URLs for new versions

### Option 2: Header Versioning

**Pros:**

- URLs stay stable
- Cleaner REST semantics
- Version is metadata, not resource identity

**Cons:**

- Less visible in logs/debugging
- Harder to test in browser
- Some proxies strip custom headers
- Less familiar to junior developers

### Option 3: Query Parameter Versioning

**Pros:**

- URLs mostly stable
- Easy to test
- Optional parameter with default

**Cons:**

- Pollutes query string
- Can conflict with pagination/filtering params
- Not widely used in industry

---

## Agent Recommendation

**Recommended:** Option 1 (URL Path Versioning)

**Reasoning:** Highest-weighted drivers are developer familiarity and debuggability. URL path versioning scores best on both. Industry standard means external developers will expect it.

**Caveats:** If we expect very frequent breaking changes (more than annually), header versioning might scale better.

---

## Decision

**Use URL Path Versioning** for all public API endpoints.

## Rationale

1. **Developer familiarity**: Team and external integrators already know this pattern
2. **Debuggability**: Version visible in every log entry and request trace
3. **Infrastructure routing**: Can route versions to different deployments if needed
4. **Industry alignment**: Matches what developers expect from public APIs

## Consequences

### Positive

- Clear versioning visible in all tooling
- Simple routing rules
- Easy to deprecate old versions

### Negative

- URL changes require client updates
- Need discipline to avoid code duplication across versions

### Risks

- Version proliferation if not disciplined (mitigated: deprecation policy required)

## References

- [Stripe API Versioning](https://stripe.com/docs/api/versioning)
- [GitHub API Versioning](https://docs.github.com/en/rest/overview/api-versions)
```

$ARGUMENTS
