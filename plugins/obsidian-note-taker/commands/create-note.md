---
description: Quickly create a well-formatted Obsidian note with proper frontmatter and template structure
---

# Create Obsidian Note

You are now in **Create Note Mode**. Help the user create a well-formatted Obsidian note following the obsidian-note-taker standards.

## ⚠️ CRITICAL RULES

**All AI-generated notes MUST use these EXACT values:**

```yaml
---
date: YYYY-MM-DD      # Today's date
status: capture       # ALWAYS "capture" - NEVER any other value
type: note            # ALWAYS "note" - NEVER any other value
tags:
  - ai-generated      # REQUIRED - always first tag
  - topic-tag-1       # 2-4 content-relevant tags
  - topic-tag-2
---
```

**Why?** AI notes are "fleeting notes" - they go into the user's review inbox. The HUMAN assigns proper type and status during their review process.

**Tag Rules:**
- `ai-generated` is a SYSTEM TAG - always required, always first, does NOT count toward the 2-4 limit
- Add 2-4 content-relevant tags based on the note's topic

## Workflow

1. **Gather Information**
   Ask the user:
   - What is this note about? (topic/content)
   - Any specific details to include?

   **DO NOT ask about type or status** - those are FIXED.

2. **SEARCH BEFORE CREATE (Critical - Never Skip)**

   **Before creating ANY note, search the vault to avoid duplication:**

   ```
   # First: Semantic search for conceptually similar notes
   mcp__obsidian-mcp-tools__search_vault_smart(
       query="[describe the concept in natural language]",
       filter={"limit": 10}
   )

   # Second: Keyword search for specific terms
   mcp__MCP_DOCKER__obsidian_simple_search(
       query="[key terms as plain text]",
       context_length=100
   )
   ```

   **⚠️ Query Format for Simple Search:**
   - Use plain text keywords, NOT hashtag syntax
   - ✅ Correct: `query="claude code plugin"`
   - ❌ Wrong: `query="#claude-code OR #plugin"` (returns empty)

   **⚠️ CRITICAL: Report Tool Failures**

   **NEVER silently proceed if a search fails.** Always tell the user:
   - If tool returns error: "The [tool] returned an error: [message]. Trying alternative..."
   - If results are empty: "Search returned no results for '[query]'. This could mean no related notes exist, or I should try different terms."
   - If tool unavailable: "The [tool] is unavailable. Cannot complete the search-before-create safety check."

   **Decision Tree based on results:**
   | Finding | Action |
   |---------|--------|
   | **Exact duplicate** | DO NOT create. Offer to link or update existing |
   | **70%+ overlap** | DO NOT create. Offer to extend existing OR create note with only novel content |
   | **Partial overlap** | Create new note with contextual links to related notes |
   | **No overlap** | Proceed with new note creation |

3. **Search Existing Tags First**

   Before assigning tags, search for what already exists in the vault:
   ```
   mcp__MCP_DOCKER__obsidian_simple_search(
       query="claude ai plugin",
       context_length=50
   )
   ```

   **⚠️ Query Format**: Use plain keywords (NOT hashtags). The search finds notes containing these terms - examine them for their existing tags.

   **Rules:**
   - Use EXACT existing tags (if vault has `#claude-code`, use that, not `#claude_code`)
   - If no existing tag fits, create new one with proper format (see below)
   - `ai-generated` is always required as first tag

   **Tag Format (MANDATORY):**
   - **Hyphens for multi-word**: `system-design` NOT `system_design` or `systemdesign`
   - **Lowercase only**: `claude-code` NOT `Claude-Code`
   - **Singular form**: `api` NOT `apis`
   - **NEVER use underscores** - always hyphens

4. **Generate Frontmatter**
   Create YAML frontmatter with FIXED values:
   ```yaml
   ---
   date: YYYY-MM-DD        # Today's date
   status: capture         # ALWAYS this value
   type: note              # ALWAYS this value
   tags:
     - ai-generated        # ALWAYS first (exception - always allowed)
     - existing-tag-1      # MUST exist in vault already
     - existing-tag-2      # MUST exist in vault already
   ---
   ```

5. **Apply Atomic Note Principles**
   - Each note should contain ONE main idea
   - If you struggle to title it, it may contain multiple ideas - split them
   - Frame titles as assertions when possible: "Semantic search finds concepts not keywords"

6. **Apply Template**
   Use the standard AI note template:
   ```markdown
   # [Title - preferably as assertion or clear concept]

   ## Overview
   [Brief summary - the core idea]

   ## Key Points
   - Point 1
   - Point 2

   ## Details
   [Main content with contextual [[wikilinks|links]] inline]

   ## Related Notes
   - [[Related Note]] - explanation of how it relates
   ```

7. **Folder Placement**
   ALL AI-generated notes go to: **`1 - Main Notes/`**

   The user moves notes during their review if needed.

8. **Format Filename**
   - Title Case with spaces
   - Remove special characters: `: / \ | * ? " < >`
   - Keep under 60 characters
   - Example: `System Architecture Overview.md`

9. **Present Note**
   Show the complete note with:
   - Full frontmatter (with fixed status/type)
   - Template structure filled in
   - Suggested file path: `1 - Main Notes/[filename].md`

## Template

The ONLY template AI should use:

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

# [Title]

## Overview
[One paragraph summary]

## Key Points
- Point 1
- Point 2
- Point 3

## Details
[Main content - expand on key points]

## Related Notes
- [[Related Note 1]]
- [[Related Note 2]]
```

## Best Practices

1. **ALWAYS search before creating** - This is the most important rule
2. **Search existing tags first** - Use existing tags when they fit the concept
3. **Proper tag format** - Hyphens (NOT underscores), lowercase, singular
4. **Ask clarifying questions** about CONTENT only (not type/status)
5. **Use today's date** in YYYY-MM-DD format
6. **Status is ALWAYS `capture`** - no exceptions
7. **Type is ALWAYS `note`** - no exceptions
8. **`ai-generated` tag is ALWAYS first**
9. **Select 2-4 content tags** - from existing vault tags or properly formatted new ones
10. **Folder is ALWAYS `1 - Main Notes/`**
11. **One idea per note** - keep notes atomic
12. **Link with context** - use `[[Note|display text]]` and explain relationships

## Example Usage

### Example 1: New Note (No Duplicates Found)

**User:** `/create-note`

**You:** "I'll help you create an Obsidian note. What would you like to document?"

**User:** "I want to document our new microservices architecture"

**You:** "Great! Let me first search your vault to see if related content already exists..."

[Searches vault with semantic and keyword search]

**You:** "I found a few related notes but nothing that covers this topic directly:
- [[API Design Principles]] - general API patterns
- [[System Design Notes]] - high-level architecture concepts

I'll create a new note that links to these. Any specific components to include?"

**User:** "It includes API gateway, auth service, and database layer"

**You:** [Generate complete note with contextual links]

```markdown
---
date: 2025-11-04
status: capture
type: note
tags:
  - ai-generated
  - architecture
  - microservices
  - system-design
---

# Microservices Architecture

## Overview
Our new microservices architecture provides a scalable foundation for distributed services with centralized authentication and API gateway routing. This builds on our [[API Design Principles|API design patterns]].

## Key Points
- API Gateway as central entry point for all requests
- Auth Service for centralized authentication (OAuth 2.0, JWT)
- Database Layer with ORM and connection pooling

## Details

### API Gateway
- **Purpose**: Central entry point for all client requests
- **Interfaces**: REST API, WebSocket

### Auth Service
- **Purpose**: Centralized authentication and authorization
- **Interfaces**: OAuth 2.0, JWT tokens

### Database Layer
- **Purpose**: Persistent data storage
- **Interfaces**: ORM, connection pooling

## Related Notes
- [[API Design Principles]] - foundational patterns this architecture follows
- [[System Design Notes]] - broader context for this architectural approach
```

📁 Save to: `1 - Main Notes/Microservices Architecture.md`

Would you like me to adjust anything or save it to your vault?

---

### Example 2: Duplicate Found - Offer to Extend

**User:** `/create-note`

**You:** "What would you like to document?"

**User:** "I want to document how semantic search works in Obsidian"

**You:** "Let me search your vault first..."

[Searches vault]

**You:** "I found an existing note [[Smart Search Strategies]] that already covers semantic search concepts including how embeddings work and comparison to keyword search.

Your request appears to overlap significantly with this existing content. Would you like me to:

A) **Show you the existing note** so you can see what's there
B) **Add new information** to the existing note (if you have novel details)
C) **Create a new note anyway** that links to it (only if your angle is distinctly different)

Which would you prefer?"

## Integration

- Use the `obsidian-note-taker` skill for detailed references
- Can invoke `validate-notes` command after creation to verify formatting

## Notes

- This command should be **interactive** - guide the user through the process
- Always **present the complete note** before suggesting where to save it
- Let the user **review and adjust** before finalizing
- **Don't actually write the file** unless explicitly asked - show the content first
- **NEVER change status or type** - those are fixed for AI notes

---

**Goal**: Make note creation fast, easy, and properly formatted every time - with the human in control of type/status assignment.
