# Work on a feature or fix

Use this when the user asks for a code change: a feature, a bug fix, a refactor.

## Steps

1. **Clarify scope once.** If the request names the repository and the goal clearly, proceed. Ask exactly one clarifying question only when the target repository or the intended behavior is genuinely ambiguous.
2. **Resolve the repository.** Call `agent_repository_list` and match the user's wording to a repository id. If it is not in the list, say so; the allowlist is operator-controlled and you cannot extend it.
3. **Create the environment.**
   `agent_environment_create` with `purpose: "coding"`, the repository id, `ref` set to the branch to start from (default branch unless the user said otherwise), and an `idempotency_key` you derive once from the request (short task slug plus the date, e.g. `rate-limit-webhooks-0729`). Reuse the same key if you retry; never generate a fresh key for the same request.
4. **Wait for it.** Tell the user provisioning takes a couple of minutes. Poll `agent_environment_get` roughly every 30 seconds. `failed` or `degraded` with an error message: relay the message and stop. Do not recreate on your own.
5. **Execute.** Run the task in one `edka-env run <environment-id> <instructions>` invocation. Write the instructions like a good ticket: the goal, constraints you know, and the finishing moves. Always end the instructions with: create a branch named `conductor/<task-slug>`, commit with clear messages, push, open a pull request titled from the change, and print the pull request URL as the last line.
6. **Report.** Give the user the PR link and a two-sentence summary of what was done. If tests were run inside the environment, say what passed.
7. **Clean up or keep.** Ask nothing: keep the environment while iteration on this PR is plausible in this conversation; delete it with `agent_environment_delete` once the user approves, merges, or abandons the work. On session start, `agent_environment_list` and mention any leftovers.

## Failure handling

- `agent_environment_create` errors are actionable by design (repository not allowed, conductor target missing, limit reached, GitHub permissions). Relay the message; do not loop.
- If `edka-env run` exits non-zero, read its stderr: grant refusals explain themselves (still provisioning, revoked bearer). Retry once for a not-ready grant after the environment reports `running`; anything else goes back to the user.
