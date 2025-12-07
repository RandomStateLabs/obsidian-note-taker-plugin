---
name: obsidian-note-taker
description: Create well-formatted Obsidian notes with proper frontmatter, templates, and organization. Use when users want to create notes for their Obsidian vault, document ideas or technical content, or need help structuring information in Obsidian-compatible markdown format.
---

# Obsidian Note-Taker

Create Obsidian notes with Zettelkasten principles: atomic notes, search-before-create, intelligent linking.

## ⚠️ CRITICAL RULES - AI MUST FOLLOW

**All AI-generated notes MUST use these exact values:**

```yaml
---
date: YYYY-MM-DD      # Today's date
status: capture       # ALWAYS "capture" - NEVER any other value
type: note            # ALWAYS "note" - NEVER any other value
tags:
  - ai-generated      # REQUIRED - always first tag
  - topic-tag-1       # 2-4 content-relevant tags (these count toward limit)
  - topic-tag-2
---
```

**Why?** AI-generated notes are "fleeting notes" in Zettelkasten terms. They go into the user's review inbox. The HUMAN assigns proper type (guide, architecture, idea, etc.) and promotes status (develop, complete) during their review process.

**Tag Rules:**
- `ai-generated` is a SYSTEM TAG - always required, always first, does NOT count toward the 2-4 tag limit
- Add 2-4 content-relevant tags based on the note's topic

## When to Use This Skill

Activate this skill when:
- User asks to "create a note" or "document" something for Obsidian
- User provides information that needs structuring as an Obsidian note
- User mentions their vault, knowledge base, or Zettelkasten

**DO NOT create notes for:**
- Quick answers to questions
- Temporary debugging info
- Every conversation (only when user explicitly requests)

## Core Workflow

### 1. Gather Information

Ask the user what they want to document. Do NOT ask about type or status - those are fixed.

### 2. SEARCH BEFORE CREATE (Critical)

**Before writing ANY note, search the vault to avoid duplication.**

#### Step 2a: Semantic Search First
```
mcp__obsidian-mcp-tools__search_vault_smart(
    query="[core concept in natural language]",
    filter={"limit": 10}
)
```

#### Step 2b: Keyword Search Backup
```
mcp__MCP_DOCKER__obsidian_simple_search(
    query="[key terms - plain text, NO hashtags]",
    context_length=100
)
```
⚠️ Use plain text: `"claude code plugin"` NOT `"#claude-code OR #plugin"`

#### ⚠️ Report Tool Failures
NEVER silently proceed if search fails. Tell the user about errors/empty results.

#### Step 2c: Decision Tree

| Finding | Action |
|---------|--------|
| **Exact duplicate** | DO NOT create. Offer to link/update existing |
| **70%+ overlap** | DO NOT create. Extend existing OR create note with only novel content |
| **Partial overlap** | Create new note with contextual links to related |
| **No overlap** | Proceed with creation |

### 3. Folder Placement

All AI-generated notes go to: **`1 - Main Notes/`**

This is the default inbox location. The user will move notes to appropriate folders during their review process if needed.

### 4. Apply Atomic Note Principles

**Each note should try to contain just ONE idea.** This is the essence of atomic notes.

How to know if a note is atomic:
- **Easy to title**: If you struggle to name it, it probably contains multiple ideas
- **Single concept**: Can you summarize it in one sentence?
- **Reusable**: Could this note be linked from multiple other notes?

If a note contains multiple ideas, split it into separate notes and link them.

### 5. Title as Assertion (Recommended)

**Best practice**: Frame titles as assertions or claims when documenting ideas:

| Style | Example | When to Use |
|-------|---------|-------------|
| **Assertion** | "Semantic search finds concepts not keywords" | Ideas, insights, claims |
| **Concept** | "Semantic Search in Knowledge Graphs" | Topic coverage, definitions |

Assertion-style titles make notes more linkable and clarify the core idea.

### 6. Apply Template Structure

Use the appropriate template from `references/templates.md` based on note type.

### 7. Format Content with Intelligent Linking

#### Wikilink Basics
- Use `[[wikilinks]]` for internal references
- Use `[text](url)` for external links
- Maintain proper heading hierarchy (# > ## > ###)

#### Using Pipe Aliases for Natural Linking

**⚠️ CRITICAL: Only use pipe aliases for notes that ALREADY EXIST in the vault.**

Use the pipe character `|` to customize how links display - but ONLY for existing notes:

```markdown
# ✅ CORRECT - Note exists in vault, pipe alias for natural prose:
[[Personal Knowledge Management|PKM]]           → displays as "PKM"
[[Semantic Search|semantically searching]]      → grammatically correct in prose

# ❌ WRONG - Note doesn't exist, pipe alias creates confusion:
[[Future Note Title|some alias]]                → misleading, creates dead link with wrong display

# ✅ CORRECT - Note doesn't exist, use exact title or plain text:
[[Future Note Title]]                           → creates note with correct title if clicked
Zettelkasten principles                         → plain text, no link (preferred if note doesn't exist)
```

**Why this matters:**
- Pipe aliases on non-existent notes create "dead links" with mismatched display text
- If user clicks `[[My Note|different text]]` and note doesn't exist, Obsidian creates "My Note" - but the display showed "different text"
- This causes confusion and maintenance burden

**Rule**: Before using `[[Note|alias]]`, verify the note exists via search. If it doesn't exist, either:
1. Use plain text (no wikilink) - preferred for concepts without dedicated notes
2. Use exact title without pipe: `[[Exact Note Title]]`

#### Contextual Links (Preferred)

```markdown
❌ Bad (no context):
## Related Notes
- [[Atomic Notes]]
- [[Search Strategies]]

✅ Good (explains relationship):
## Related Notes
- [[Atomic Notes]] - this note follows the one-idea-per-note principle
- [[Search Strategies]] - describes the search-before-create workflow used here
```

### 8. Find and Link Related Notes (MANDATORY)

**⚠️ CRITICAL: Every note MUST have actual `[[wikilinks]]` in the Related Notes section - not plain text descriptions.**

After drafting the note content, you MUST search for related notes to link:

```
mcp__obsidian-mcp-tools__search_vault_smart(
    query="[main concept of your note]",
    filter={"limit": 5}
)
```

**Then populate the Related Notes section with actual wikilinks:**

```markdown
## Related Notes
- [[Actual Note Title Found In Search]] - how it relates to this note
- [[Another Real Note]] - the connection explained
```

**Rules:**
- Related Notes MUST contain `[[wikilinks]]` to notes that EXIST (found via search)
- NEVER use plain text like "Related concepts: X, Y, Z" - that's not linking
- Each link MUST include context explaining the relationship
- If search finds 0 related notes, write: "No related notes found in vault. Potential future links: [plain text concepts]"
- Add 2-5 related notes maximum

## Quick Template

For immediate use - this is the ONLY template AI should use:

### Standard AI Note
```markdown
---
date: YYYY-MM-DD
status: capture
type: note
tags:
  - ai-generated
  - [topic-tag-1]
  - [topic-tag-2]
---

# [Title - preferably as assertion or clear concept]

## Overview
[One paragraph summary - the core idea in your own words]

## Key Points
- Point 1
- Point 2
- Point 3

## Details
[Main content - expand on key points. Include wikilinks to EXISTING
related notes: "This builds on [[Existing Note Title]]..." Only use
pipe aliases like [[Note|alias]] if the note already exists in vault.]

## Related Notes
- [[Related Note 1]] - brief explanation of how it relates
- [[Related Note 2]] - brief explanation of how it relates
```

**Remember**:
- `status: capture` and `type: note` are ALWAYS fixed
- `ai-generated` tag is ALWAYS first
- Add 2-4 content-relevant tags after `ai-generated`
- Related Notes should include context explaining the relationship

## File Naming Convention

- Title case with spaces: `System Architecture for Auth Service.md`
- Remove special characters: `: / \ | * ? " < >`
- Keep under 60 characters
- No underscores or hyphens in filenames

## Tag Management

**Search existing tags first, then create new ones only when needed.**

### Tag Workflow
1. **Search first**: `mcp__MCP_DOCKER__obsidian_simple_search(query="[topic keywords]")`
2. **Match existing**: Use tags EXACTLY as they appear in vault
3. **Create new**: Only if no existing tag fits; inform user

### Tag Format Rules (MANDATORY)

| Rule | Correct | Wrong |
|------|---------|-------|
| **Hyphens for multi-word** | `system-design` | `system_design`, `systemdesign` |
| **Lowercase only** | `claude-code` | `Claude-Code` |
| **Singular form** | `api` | `apis` |

**NEVER use underscores.** Always hyphens.

## Validation Checklist

### Pre-Creation (MANDATORY)
- [ ] Searched vault (semantic + keyword) - no duplicates
- [ ] Searched existing tags before assigning

### Frontmatter
- [ ] `status: capture`, `type: note` (ALWAYS)
- [ ] `ai-generated` tag first, then 2-4 content tags
- [ ] Tags: hyphens, lowercase, singular (NEVER underscores)

### Content
- [ ] ONE idea per note (atomic)
- [ ] Title under 60 chars, preferably assertion-style
- [ ] **Related Notes has actual `[[wikilinks]]`** from search results
- [ ] Pipe aliases only for verified existing notes
- [ ] Folder: `1 - Main Notes/`

## Output Format

Present the complete note and instructions:

```
Here's your formatted Obsidian note:

[COMPLETE NOTE CONTENT]

📁 Save to: `[folder]/[filename].md`

Would you like me to adjust anything?
```

## Saving to Obsidian Vault

1. **Ask first**: "Would you like me to save this note?"
2. **If confirmed**, save:
   ```
   mcp__MCP_DOCKER__obsidian_append_content(
       filepath="1 - Main Notes/[filename].md",
       content=[complete note with frontmatter]
   )
   ```
3. **Confirm**: "✅ Note saved to: `[filepath]`"
4. **On error**: Display note content for manual copy

## References

For complex requirements, see `references/templates.md` and `references/folder-structure.md`.

## MCP Tools Quick Reference

| Tool | Use |
|------|-----|
| `mcp__obsidian-mcp-tools__search_vault_smart` | Semantic search (primary) |
| `mcp__MCP_DOCKER__obsidian_simple_search` | Keyword search (plain text only, NO hashtags) |
| `mcp__MCP_DOCKER__obsidian_get_file_contents` | Read existing note |
| `mcp__MCP_DOCKER__obsidian_append_content` | Save new note |
