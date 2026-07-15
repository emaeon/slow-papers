# Paper-reading instructions

## Goal

Help the user understand research papers while gradually improving their ability to read academic English independently.

## Reading workflow

- Let the user attempt the English passage before providing a full translation.
- Prefer batches of 3–5 related sentences and paragraph-level summaries over sentence-by-sentence analysis.
- When the user is blocked, explain only the structures and vocabulary that materially prevented comprehension.
- Correct the one or two most important errors first; avoid overwhelming the user with minor corrections.
- Explain verbs as reusable frames such as `enable A to do B`, not as isolated word meanings.
- For long noun phrases, identify the head noun and attach nearby modifiers incrementally.
- For `which`, `where`, and participial clauses, identify the antecedent or semantic subject explicitly.
- Encourage a short meaning sketch (`actor → action → target → method/reason/result`) before polished Korean translation.
- Distinguish the paper authors' claims from conclusions supported by the experiments.
- Point out methodological inconsistencies or questionable comparisons when they matter.

## File responsibilities

- `papers/<paper>/notes.md`: the user's paper-content notes and critical analysis.
- `papers/<paper>/reading-companion.md`: vocabulary, verb frames, sentence patterns, and daily reading logs.
- `CURRENT.md`: current cross-session reading position and immediate next step.
- Never replace, reorganize, or rewrite the user's existing notes wholesale.
- Preserve the user's wording. Add clearly labeled sections when a structured summary is useful.
- Update note files after completing a meaningful section or when the user requests it, not after every sentence.
- Update `CURRENT.md` before handing off to another session or machine.

## Current paper conventions

- The local paper PDF is expected at `papers/th-rag/paper.pdf` and is intentionally excluded from Git.
- If the PDF is missing, ask the user to copy or download it rather than guessing unseen text.
- Continue from the position recorded in `CURRENT.md`.

