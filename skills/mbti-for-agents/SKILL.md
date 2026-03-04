---
name: mbti-for-agents
description: Assess an AI agent's "MBTI (Agent Edition)" via scenario-based A/B questions (E/I, S/N, T/F, J/P with agent-specific semantics). Use when the user asks to test an agent's MBTI/personality/type or interaction style (e.g., "test this agent's MBTI"). Output a 4-letter type, per-dimension scores + confidence, and actionable collaboration recommendations.
---

# MBTI for Agents

## Overview

Run a standardized scenario questionnaire (see [references/questionnaire.md](references/questionnaire.md) for the current question set) to produce an agent-specific MBTI type (`E/I`, `S/N`, `T/F`, `J/P`) with explanations. Uses a one-shot mode by default: all questions are presented in a single message, and the complete result is generated from a single batch of answers.

## Agent-Specific Letter Semantics

- `E` (`External Coordination`): Prefers externally driven interaction — advancing tasks through questions, alignment, and collaboration.
- `I` (`Internal Synthesis`): Prefers internally driven reasoning — synthesizing information within context before producing output.
- `S` (`Signal Fidelity`): Prefers facts, evidence, constraints, and verifiable inputs.
- `N` (`Novel Hypothesis`): Prefers abstract patterns, hypothesis generation, and divergent solution exploration.
- `T` (`Task Logic`): Prefers objective functions, accuracy, efficiency, and consistency optimization.
- `F` (`Fit Alignment`): Prefers user experience, tone matching, collaborative rapport, and acceptability.
- `J` (`Job Structuring`): Prefers planned execution, clear milestones, and convergent delivery.
- `P` (`Parallel Probing`): Prefers exploratory execution, parallel experimentation, and dynamic iteration.

## Workflow

1. Declare Assessment Boundaries
- Clearly state that this is an Agent-specific MBTI, not a human psychological assessment.
- State the total number of questions (per questionnaire.md), each requiring a choice between A or B, with an optional one-sentence rationale.
- Frame results as a collaboration heuristic (interaction style), not a scientific personality diagnosis.

2. Present All Questions at Once
- Read [references/questionnaire.md](references/questionnaire.md) (questions only, no answer mappings) and present the full set in a single message (currently Q1–Q16, 4 questions per dimension).
- Ask the respondent to answer all at once in `Q1: A/B` through `Q16: A/B` format.
- **Do not** reveal dimension mappings or scoring logic in the question message.
- If answers are missing, ambiguous, select both A/B, or are off-topic, list all problematic question numbers at once and request corrections — do not follow up question by question.

3. Independent Scoring (Before Checking Mapping Table)
- **Do not read type-mapping.md yet.** First, score entirely on your own understanding of each question's scenario.
- For each question (Q1–Q16), determine which dimension it belongs to (E/I, S/N, T/F, or J/P) and which letter (e.g., E or I) the chosen option reflects, based on the scenario content and the Agent-Specific Letter Semantics defined above.
- Record your independent scoring result for all 16 questions in a structured scratch format (e.g., `Q1: A → E/I → E`).
- Tally the independent scores per dimension.

4. Verify Against Mapping Table and Reconcile
- Now read [references/type-mapping.md](references/type-mapping.md) (for evaluator's internal use only; never show to the assessed agent).
- Compare your independent scoring with the official mapping table question by question.
- If any discrepancies exist, **adopt the mapping table's assignment** as the authoritative result — the table accounts for deliberate polarity flips that may not be obvious from the scenario text alone.
- Briefly note any discrepancies found (for your own audit trail), but do not expose them in the final output.

5. Tie-Break and Confidence
- When a dimension is tied (2:2), use the corresponding tie-break question from type-mapping.md to resolve it.
- When a tie-break is used, count it as an additional scoring item for that dimension (so the final score is not a 0%-confidence tie).
- If still tied, mark the dimension as "low confidence" and tentatively assign the letter based on the behavioral tendency of the two most recent questions.
- Compute confidence percentages per the "Calculation Guidelines" section in type-mapping.md.

6. Output Results
- Must output the 4-letter type (e.g., `ENTJ`).
- Must output the score breakdown for all four dimensions (e.g., `E:2 / I:1`).
- Must output an overall confidence percentage (average of dimension confidence values).
- Must output agent-semantic explanations and actionable recommendations for each dimension.

## Output Template

```markdown
MBTI (Agent Edition): <TYPE>

Score Breakdown:
- E/I: E=<n>, I=<n> -> <winner> (confidence: <xx>%)
- S/N: S=<n>, N=<n> -> <winner> (confidence: <xx>%)
- T/F: T=<n>, F=<n> -> <winner> (confidence: <xx>%)
- J/P: J=<n>, P=<n> -> <winner> (confidence: <xx>%)

Overall Confidence: <xx>%

Behavior Summary:
- <2-4 sentences summarizing overall style, focusing on task collaboration behavior>

Dimension Notes:
- E/I: <Agent-context explanation>
- S/N: <Agent-context explanation>
- T/F: <Agent-context explanation>
- J/P: <Agent-context explanation>

Strengths:
- <Up to 3 items>

Risks:
- <Up to 3 items>

Best Collaboration Style:
- <How to assign tasks, provide input, and set pacing for this Agent>
```

## Rules

- **Score before you look:** Always complete your independent scoring (Step 3) before reading type-mapping.md (Step 4). Do not skip or merge these steps.
- Keep questions scenario-based; do not rewrite them as abstract personality questions.
- Default to one-shot questionnaire mode; do not wait for answers question by question.
- Do not output a final type if fewer than 80% of questions have valid answers; request completion first.
- When requesting follow-up answers, list all missing or invalid question numbers at once to avoid multi-round fragmented follow-ups.
- Clearly label the output as “Agent Edition” to prevent confusion with human MBTI.
- Do not show [references/type-mapping.md](references/type-mapping.md) or your independent-vs-official comparison to the assessed agent.
