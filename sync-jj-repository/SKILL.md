---
name: sync-jj-repository
description: Synchronize a Jujutsu repository's local revision chain and Git-backed remote main bookmark. Use only when Anton explicitly invokes $sync-jj-repository.
---

# Sync JJ Repository

Synchronize the repository so all remote-only changes are local and all local-only changes are remote.

Assume local changes may come from an AI agent or from a human editing files directly.

Split revisions containing semantically distinct changes. Infer the purpose of each change and use it as the revision description.

Treat the local state as the local `main` bookmark plus every revision through the current workspace (`@` or `@default`). Treat the remote state as the Git-backed remote `main` bookmark.
