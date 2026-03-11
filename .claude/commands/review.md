You are a review orchestrator. Run all review agents against already-implemented code.

Story to review: $ARGUMENTS

---

## Step 1 — Read Story

Use `read_story` with "$ARGUMENTS". Find which files were modified in the `## Implementation Notes` section.
If not present, ask the user which paths to scan before continuing.

---

## Step 2 — Spawn Parallel Agents

Spawn all 4 Tasks simultaneously — agents are fully independent and must run in parallel.

- Task 1: agent `security-reviewer`
  > Analyze these files: [PATHS]. Return your findings as structured text.

- Task 2: agent `architecture-reviewer`
  > Analyze these files: [PATHS]. Return your findings as structured text.

- Task 3: agent `test-reviewer`
  > Analyze these files: [PATHS] and their test counterparts. Return your findings as structured text.

- Task 4: agent `docs-reviewer`
  > Analyze these files: [PATHS]. Return your findings as structured text.

Wait for all 4 to complete and collect their output.

---

## Step 3 — Synthesize

Use `update_story` ONCE on the original story to append `## Review Summary`
with all 4 agent outputs as a compact summary:

```
## Review Summary

| | Findings |
|---|---|
| Security | [one line summary] |
| Architecture | [one line summary] |
| Testing | [one line summary] |
| Docs | [one line summary] |
```

Keep each row to one line. Omit rows with no findings.

If the story title contains "[FOLLOWUP]":
- Append full detailed findings directly to this story as `## Security Findings`, `## Architecture Findings`, `## Testing Findings`, `## Docs Findings`
- Do NOT create another follow-up story

Otherwise, if SHOULD FIX, NICE TO HAVE, IMPORTANT, or MINOR items remain:
- Use `create_story` for a follow-up:
  - Title: `# Feature: [FOLLOWUP] <original story title> — <today's date>`
  - Tag: "followup"
  - `## Tasks`: one `- [ ]` per item prefixed with category (ARCH/TEST/SEC/DOCS)
  - Append full detailed findings as `## Security Findings`, `## Architecture Findings`, `## Testing Findings`, `## Docs Findings`

If no remaining items, append full detailed findings directly to the original story.

Print final summary:
```
✅ Review complete for [ID/slug]
🔴 Items needing immediate attention: N
🟡 Follow-up story created: #ID (N items) / none
```
