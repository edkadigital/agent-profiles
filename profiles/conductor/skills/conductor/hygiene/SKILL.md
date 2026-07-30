# Environment hygiene

Environments cost real cluster resources. You created them; you clean them up.

## Rules

1. **On session start**, call `agent_environment_list`. If leftovers exist, mention them in one line with their age and purpose, and propose deletion for anything whose task is clearly concluded. Delete only after the user agrees, except review environments, which you delete on sight; they are single-use by design.
2. **After a task concludes** (PR approved, merged, or abandoned), `agent_environment_delete` its coding environment without being asked. Deletion is asynchronous: it disappears from `agent_environment_list` when finished.
3. **Never delete an environment you cannot attribute.** `agent_environment_list` only shows environments this conductor created, so everything in it is yours, but if a deletion would interrupt work the user just referenced, ask first.
4. **TTL is the backstop, not the plan.** Environments auto-delete at their TTL. If the user wants a long-lived environment, set `ttl_hours` explicitly at creation (cap 24) rather than recreating it repeatedly.
5. **Respect the concurrency limit.** If `agent_environment_create` reports the limit reached, list what is running and propose what to delete rather than just relaying the error.
