---
name: balls
description: >
  Breaks complex problems into numbered sub-questions, solves each independently,
  assigns probability-based confidence scores (0.0-1.0) to conclusions, and
  synthesizes a weighted final answer with uncertainty analysis.
  Use when the user asks for step-by-step analysis, wants to understand how
  certain a conclusion is, requests confidence levels, says "how sure are you",
  or invokes /balls before a question.
---

# Balls Mode - Decomposed Reasoning with Confidence Scoring

When the user invokes `/balls` followed by a question or problem, activate this structured reasoning protocol.

## Protocol

### Step 1: CLASSIFY

First, determine if the problem is trivial or complex:
- **Trivial**: Direct factual questions, simple calculations, single-step tasks → Answer directly
- **Complex**: Multi-faceted questions, architectural decisions, debugging, analysis → Proceed to decomposition

### Step 2: DECOMPOSE

Break the problem into independent, verifiable reasoning units ("balls"). Each ball must be:
- Self-contained enough to verify independently
- Small enough to have a clear answer
- Concrete enough to assign confidence

Output format:
```
## Decomposition

| # | Ball | Why it matters |
|---|------|----------------|
| 1 | [specific sub-question] | [relevance to main question] |
| 2 | [specific sub-question] | [relevance to main question] |
...
```

### Step 3: SOLVE & VERIFY

For each ball:
1. Solve it independently (don't let other balls influence the answer)
2. Check for hidden assumptions
3. Verify logic and facts
4. Flag if the answer depends on uncertain information

### Step 4: SCORE

Assign confidence to each ball:
- **0.9-1.0**: Verifiable fact, direct observation, or logical certainty
- **0.7-0.89**: Strong evidence, well-established patterns
- **0.5-0.69**: Reasonable inference, some uncertainty
- **0.3-0.49**: Educated guess, significant unknowns
- **0.0-0.29**: Speculation, insufficient information

Output format:
```
## Analysis

| Ball | Answer | Confidence | Notes |
|------|--------|------------|-------|
| [sub-question] | [answer] | 0.X | [assumptions, caveats] |
...
```

### Step 5: SYNTHESIZE

Combine the balls into a final answer:
1. Weight answers by confidence
2. Flag any contradictions between balls
3. Identify the weakest link (lowest confidence ball that affects the conclusion)
4. State the overall confidence

Output format:
```
## Synthesis

**Answer**: [integrated conclusion]

**Overall Confidence**: 0.X

**Weakest Link**: [which ball and why]

**To increase confidence**: [what information would help]
```

## Example

**User**: `/balls Should I rewrite this Python service in Rust?`

**Response**:

## Decomposition

| # | Ball | Why it matters |
|---|------|----------------|
| 1 | What are the current performance bottlenecks? | Rust only helps if CPU-bound |
| 2 | What's the team's Rust experience? | Affects timeline and maintenance |
| 3 | How complex is the current codebase? | Rewrite effort estimation |
| 4 | What's the deployment/ops situation? | New toolchain implications |
| 5 | Is this service stable or actively evolving? | Rewrites of moving targets are painful |

## Analysis

| Ball | Answer | Confidence | Notes |
|------|--------|------------|-------|
| Performance bottlenecks | Unknown - need profiling data | 0.2 | Critical gap |
| Team Rust experience | Assuming low based on "Python service" | 0.4 | Could be wrong |
| Codebase complexity | Unknown | 0.1 | Need to see the code |
| Deployment situation | Unknown | 0.1 | Need context |
| Service stability | Unknown | 0.1 | Need context |

## Synthesis

**Answer**: Cannot make a recommendation - too many unknowns. The question assumes Rust is the answer before establishing the problem.

**Overall Confidence**: 0.15

**Weakest Link**: We don't know if there's actually a performance problem, or if Rust would solve it.

**To increase confidence**:
1. Profile the current service - where is time spent?
2. Clarify team's Rust experience
3. Share the codebase size/complexity
4. Explain what's driving this consideration

## Rules

1. Never skip decomposition for complex questions just because you "know" the answer
2. Be honest about low confidence - don't inflate scores to seem helpful
3. If all balls are low confidence, say so clearly
4. Distinguish between "I don't know" (low confidence) and "unknowable" (need more info)
5. For trivial questions, just answer - don't over-engineer
