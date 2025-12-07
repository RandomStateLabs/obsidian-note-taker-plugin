---
name: obsidian-note-taker
description: Create well-formatted Obsidian notes with proper frontmatter, templates, and organization. Use when users want to create notes for their Obsidian vault, document ideas or technical content, or need help structuring information in Obsidian-compatible markdown format.
---

# Obsidian Note-Taker

This skill helps create properly formatted Obsidian notes with consistent structure, frontmatter, and organization. It follows Zettelkasten principles including atomic notes, semantic search before creation, and intelligent linking to avoid duplication.

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

### 2. SEARCH BEFORE CREATE (Critical - Never Skip)

**Before writing ANY note, you MUST search the vault to avoid duplication.**

This is the most important step. Duplicating existing content creates mess and defeats the purpose of a knowledge base.

#### Step 2a: Semantic Search First

Use smart/semantic search to find conceptually related notes (not just keyword matches):

```
mcp__obsidian-mcp-tools__search_vault_smart(
    query="[describe the core idea/concept in natural language]",
    filter={"limit": 10}
)
```

#### ⚠️ CRITICAL: Report Tool Failures

**NEVER silently proceed if a search tool fails or returns empty results.**

If ANY MCP tool:
- Returns an error → Tell the user: "The semantic search tool returned an error: [error message]. I'll try the keyword search instead."
- Returns empty results → Tell the user: "The search returned no results for '[query]'. This could mean no related notes exist, or I may need to try different search terms."
- Is unavailable → Tell the user: "The [tool name] is not available. I cannot complete the search-before-create workflow without it."

**Why this matters**: Silent failures led to duplicate notes being created. The user needs to know when the safety checks aren't working.

#### Step 2b: Keyword Search as Backup

Also run a simple keyword search for specific terms:

```
mcp__MCP_DOCKER__obsidian_simple_search(
    query="[key terms from note title/content]",
    context_length=100
)
```

**⚠️ IMPORTANT: Query Format for Simple Search**
- Use **plain text keywords** - NOT hashtag syntax
- ✅ Correct: `query="claude code plugin"` or `query="semantic search"`
- ❌ Wrong: `query="#claude-code OR #plugin"` (hashtags return empty results)
- For multiple terms, use space separation: `query="architecture microservices design"`

#### Step 2c: Analyze Search Results - DECISION TREE

Based on what you find, follow this decision tree:

| Finding | Action |
|---------|--------|
| **Exact duplicate exists** (same idea, same content) | **DO NOT create new note.** Tell user: "This already exists at [[Note Title]]. Would you like me to link to it or update it?" |
| **70%+ overlap** (same core idea, some new details) | **DO NOT create new note.** Suggest extending the existing note OR create a new note that ONLY contains the novel content and links to existing |
| **Partial overlap** (related but distinct ideas) | Create new note but add contextual links explaining the relationship |
| **No overlap** (genuinely new idea) | Proceed with new note creation |

**Example conversation:**
```
User: "Create a note about semantic search in Obsidian"
AI: [searches vault first]
AI: "I found an existing note [[Smart Search Strategies]] that covers
     semantic search concepts. Your new information about [X] is novel.
     Should I:
     A) Add this to the existing note
     B) Create a new note that links to it
     C) Show me what's in the existing note first"
```

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

Use the pipe character `|` to customize how links display:

```markdown
[[Personal Knowledge Management|PKM]]           → displays as "PKM"
[[Semantic Search|semantically searching]]      → grammatically correct in prose
[[Authentication Strategy|our auth approach]]   → context-specific reference
```

This keeps links natural in sentences:
- "We use [[Semantic Search|semantic search]] to find related notes"
- "This follows [[Zettelkasten Method|Zettelkasten]] principles"

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

### 8. Find and Link Related Notes

**After drafting the note content, search for related notes to link:**

```
mcp__obsidian-mcp-tools__search_vault_smart(
    query="[main concept of your note]",
    filter={"limit": 5}
)
```

Add the most relevant notes (3-5 maximum) to the "Related Notes" section with context explaining each relationship.

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
[Main content - expand on key points. Include contextual wikilinks
to related concepts: "This builds on [[Related Concept|the concept]]..."]

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

## Tag Management (Critical - Prevent Duplicates)

**Search existing tags first, then create new ones only when genuinely needed.**

### Why This Matters
Tag proliferation creates chaos:
- `claude-code`, `claude_code`, `claudecode` = 3 tags for the same thing
- Makes filtering useless
- Creates maintenance burden

The goal is NOT to prevent new tags - it's to prevent **duplicate variations** of existing concepts.

### Tag Workflow

#### Step 1: Search Existing Tags First

Before assigning ANY tag, search the vault for existing tags:

```
# Search for notes that might use similar tags
mcp__MCP_DOCKER__obsidian_simple_search(
    query="claude ai plugin",
    context_length=50
)
```

**⚠️ Query Format**: Use plain keywords, NOT hashtag syntax. The search will find notes containing these terms, which you can then examine for their tags.

Or use complex search to find all tags in a domain:
```
mcp__MCP_DOCKER__obsidian_complex_search(
    query={"glob": ["*.md", {"var": "path"}]}
)
```

#### Step 2: Match to Existing Tags

When you find existing tags, use them EXACTLY as they appear:
- If vault has `#claude-code` → use `claude-code` (not `claude_code` or `claudecode`)
- If vault has `#ai` → use `ai` (not `artificial-intelligence` or `AI`)
- If vault has `#llm` → use `llm` (not `large-language-model`)

#### Step 3: Create New Tags When Appropriate

If no existing tag covers the concept:

1. **Check for variations** - Make sure no similar tag exists with different formatting
2. **Create with proper format** - Follow the format rules below
3. **Inform the user** - Let them know you're introducing a new tag

```
"I'm adding the tag `new-concept` since your vault doesn't have an existing
tag for this topic. (Similar tags found: #related-tag-1, #related-tag-2)"
```

### Tag Format Rules (MANDATORY)

All tags MUST follow these conventions - **no exceptions**:

| Rule | Correct | Wrong |
|------|---------|-------|
| **Hyphens for multi-word** | `system-design` | `system_design`, `systemdesign` |
| **Lowercase only** | `claude-code` | `Claude-Code`, `CLAUDE-CODE` |
| **Singular form** | `api` | `apis` |
| **Specific over generic** | `claude-code` | `tool` |

**NEVER use underscores.** Always use hyphens.
**NEVER concatenate words.** Always use hyphens between words.

### Common Tag Categories (Reference Only)

These are EXAMPLES - always check what actually exists in the vault first:

| Domain | Likely Tags |
|--------|-------------|
| Technical | `architecture`, `api`, `database`, `security` |
| AI/ML | `ai`, `llm`, `claude`, `automation` |
| Business | `business`, `startup`, `strategy` |
| Process | `meeting`, `planning`, `review` |

**Remember**: The vault's actual tags take precedence over these suggestions.

## Validation Checklist

Before presenting the note:

### Pre-Creation Checks (MANDATORY)
- [ ] **Semantic search performed** - searched vault for similar content
- [ ] **No duplicate exists** - verified this idea doesn't already exist
- [ ] **Decision tree followed** - if overlap found, offered to extend/link instead
- [ ] **Existing tags searched** - looked up vault's actual tags before tagging

### Note Quality Checks
- [ ] Frontmatter present and valid YAML
- [ ] Date in YYYY-MM-DD format
- [ ] Status is exactly `capture` (NEVER anything else)
- [ ] Type is exactly `note` (NEVER anything else)
- [ ] `ai-generated` tag is first tag
- [ ] 2-4 content-relevant tags after `ai-generated`
- [ ] **Tags use correct format** (hyphens, lowercase, singular - NEVER underscores)
- [ ] **Searched for existing tags** before adding new ones

### Atomic Note Checks
- [ ] Title is clear and under 60 chars
- [ ] Title works as assertion or clear concept
- [ ] Note contains ONE main idea (atomic)
- [ ] Could summarize note in one sentence

### Linking Checks
- [ ] Content follows template structure
- [ ] Wikilinks properly formatted (use `|` for display text where helpful)
- [ ] Related Notes include context explaining relationships
- [ ] Folder is `1 - Main Notes/`

## Output Format

Present the complete note and instructions:

```
Here's your formatted Obsidian note:

[COMPLETE NOTE CONTENT]

📁 Save to: `[folder]/[filename].md`

Would you like me to adjust anything?
```

## Saving to Obsidian Vault

**After presenting the formatted note, ask the user if they want to save it to their Obsidian vault.**

### Step 1: Present Note and Ask for Confirmation

After showing the formatted note, ask:

```
Would you like me to save this note to your Obsidian vault at: `[suggested-folder]/[filename].md`?
```

### Step 2: If User Confirms, Save Using MCP Tool

Only if the user confirms (says yes, save it, go ahead, etc.), then use the Obsidian MCP integration:

```
mcp__MCP_DOCKER__obsidian_append_content(
    filepath="[suggested-folder]/[filename].md",
    content=[complete_formatted_note_including_frontmatter]
)
```

### Step 3: Confirm Success

After successful save, inform the user:

```
✅ Note saved to Obsidian vault at: `[filepath]`
```

### Step 4: Error Handling

If MCP save fails:
1. Explain: "I couldn't save directly to your vault"
2. Display the formatted note content
3. Suggest manual save: "You can copy this content and save it manually to: `[filepath]`"

### Important Notes

- **Always ask before saving** - Never save without user confirmation
- The filepath should match the suggested location from "Output Format"
- Include the complete note with frontmatter in the `content` parameter
- If user declines to save, that's fine - they have the formatted note to copy manually

### Example Workflow

For a note titled "Testing MCP Integration":

1. Format note with frontmatter:
   ```yaml
   ---
   date: 2025-10-20
   status: capture
   type: note
   tags:
     - ai-generated
     - testing
     - mcp
     - integration
   ---

   # Testing MCP Integration

   ## Overview
   [Brief summary of what's being documented]

   ## Key Points
   - Point 1
   - Point 2

   ## Details
   [Main content here]

   ## Related Notes
   - [[Related Note]]
   ```

2. Folder: `1 - Main Notes/`

3. Save using MCP:
   ```
   mcp__MCP_DOCKER__obsidian_append_content(
       filepath="1 - Main Notes/Testing MCP Integration.md",
       content=[complete note from step 1]
   )
   ```

4. Confirm: "✅ Note saved to Obsidian vault at: `1 - Main Notes/Testing MCP Integration.md`"

## Anti-Duplication Principles

**The cardinal sin of knowledge management is duplication.** Follow these principles:

### Why Duplication is Harmful
- Creates contradictory information over time
- Makes the vault harder to search and navigate
- Wastes effort writing the same thing twice
- Breaks the value of linking (which note do you link to?)

### How to Avoid Duplication

1. **Always search first** - This is non-negotiable
2. **Link, don't repeat** - If content exists, link to it with `[[Note|display text]]`
3. **Extend, don't duplicate** - Add new info to existing notes when appropriate
4. **Be specific** - New notes should only contain genuinely novel content
5. **Use contextual references** - Instead of repeating content, write "As explained in [[Other Note]], ..."

### When Finding Similar Content

If you find a note that covers 70%+ of what the user wants to document:

```
"I found [[Existing Note Title]] which covers [summary of overlap].

Your new information about [novel aspects] could be:
A) Added to the existing note (recommended if closely related)
B) Created as a new note that links to it (if distinct enough)

Which would you prefer?"
```

## Advanced Features

For complex requirements, see:
- `references/templates.md` - Detailed templates for all note types
- `references/folder-structure.md` - Complete folder organization guide
- `scripts/validate_frontmatter.py` - Validate note structure

## MCP Tools Reference

### Semantic Search (Primary - Use First)
```
mcp__obsidian-mcp-tools__search_vault_smart(
    query="natural language description of concept",
    filter={"limit": 10, "folders": ["1 - Main Notes"]}  # optional folder filter
)
```
Returns semantically similar notes even if they don't share exact keywords.

**Note**: This uses the `obsidian-mcp-tools` MCP server, not `MCP_DOCKER`.

### Simple Search (Secondary - Keyword Matching)
```
mcp__MCP_DOCKER__obsidian_simple_search(
    query="specific keywords",
    context_length=100
)
```
Returns notes containing the exact keywords.

**⚠️ Query Format**: Use plain text keywords only.
- ✅ `query="claude code plugin"`
- ❌ `query="#claude-code OR #plugin"` (hashtags don't work)

### Get File Contents (For Reviewing Found Notes)
```
mcp__MCP_DOCKER__obsidian_get_file_contents(
    filepath="1 - Main Notes/Note Title.md"
)
```
Use this to read an existing note's content before deciding whether to extend or create new.

### Save New Note
```
mcp__MCP_DOCKER__obsidian_append_content(
    filepath="1 - Main Notes/New Note Title.md",
    content="[full note content with frontmatter]"
)
```

### Patch Existing Note (For Extending)
```
mcp__MCP_DOCKER__obsidian_patch_content(
    filepath="1 - Main Notes/Existing Note.md",
    operation="append",
    target_type="heading",
    target="Details",
    content="\n\n### New Section\n[additional content]"
)
```
Use this to add content to an existing note instead of creating a duplicate.
