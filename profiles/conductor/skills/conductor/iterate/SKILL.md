# Iterate on review findings

Use this after a review produced findings and the user said which to address, or told you to use your judgment.

## Steps

1. **Triage with the user when the call is theirs.** Blocking findings you agree with: fix. Findings you believe are wrong: say why in one or two sentences each and let the user arbitrate before touching code. Nits: fix them unless the user said otherwise.
2. **Use the existing coding environment** for the PR if it is still running (`env_list` to check). If it expired, create a new coding environment on the PR branch (`ref` set to the branch name, fresh `idempotency_key`).
3. **Execute.** One `edka-env run` with the accepted findings restated as concrete instructions, plus the standing rules: additive commits only, never amend, never rebase, never force-push, do not open a second PR, do not resolve review threads, push to the same branch, print the new head SHA as the last line.
4. **Report** the pushed commits in one line each. If the user wants a re-review, run the review skill against the new head; the loop is theirs to continue or stop.

## Boundaries

- You never argue with the user twice about the same finding. State your reasoning once; their second word is final.
- If the fix meaningfully exceeds the finding (a redesign, a new dependency), stop and ask; that is a scope change, not an iteration.
