# Claude Knowledge Vault

This is the personal knowledge vault for the user. Before using any web search tool, always check this vault first.

## How to search this vault

1. Grep for keywords across all notes:
   - Use Grep with pattern matching the topic
   - Path: `C:\Users\lalbe\My Vault`
   - File types: `*.md`

2. List notes in a specific folder:
   - Use Glob with patterns like `C:\Users\lalbe\My Vault\Finance\*.md`

3. Read a specific note before going online — only search the web if the vault has nothing relevant or the note is clearly outdated.

## Vault structure

| Folder | Contents |
|--------|----------|
| `Tech & AI` | Programming, AI tools, software, Claude updates |
| `Finance` | Markets, investing, macro, crypto |
| `Geopolitics` | International relations, conflicts, diplomacy |
| `World Events` | Breaking news, major events, ongoing situations |
| `Psychology` | Mental models, influence, power, persuasion (Greene, Hill, Cialdini, etc.) |
| `Work` | User's professional domain notes |
| `_Inbox` | Unsorted notes — check here too |

## Note format (always use this when writing new notes)

```markdown
---
title: <Note Title>
date: <YYYY-MM-DD>
tags: [<topic>, <subtopic>]
source: <URL or "conversation" or "research">
last_updated: <YYYY-MM-DD>
---

## Summary
<2-3 sentence overview>

## Key Points
- ...

## Details
...

## Related
- [[Other Note]]
```

## Rules for Claude

- **Always grep the vault before any WebSearch call.** If relevant notes exist and are recent (< 30 days), use them.
- **After researching anything new**, save a note to the appropriate folder using the format above.
- **After any conversation where new facts, frameworks, or insights came up**, save a note to `_Inbox/` summarizing the key knowledge.
- **Never duplicate notes** — update the existing one's `last_updated` field instead.
- **Mark stale notes** by adding `status: stale` to frontmatter if you know the info is outdated.
