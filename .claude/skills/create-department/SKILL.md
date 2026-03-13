---
name: create-department
description: Creates a new Claw department with an intelligently generated CLAUDE.md. Use this skill whenever the user wants to add a new department to NanoClaw, says "create department", "new department", "add a department for X", or asks to set up a new area of responsibility for Claw. Also triggers on "I need Claw to handle X" when X maps to a new department scope.
---

# /create-department

Creates a new Claw department with an intelligently generated CLAUDE.md tailored to the department's domain.

## Usage

```
/create-department [name] [optional: description]
```

- **name**: The department name (e.g., "fitness", "finance", "travel"). Will be normalized to lowercase kebab-case for the directory.
- **description** (optional): A sentence or two about what this department should handle. If omitted, infer from the name.

## Steps

### 1. Normalize the name
Convert to lowercase kebab-case for the directory name. Examples: "Home Maintenance" -> `home-maintenance`, "Pet Care" -> `pet-care`.

### 2. Check for conflicts
If `departments/[name]/` already exists, tell the user and ask if they want to overwrite or pick a different name.

### 3. Gather context
Read these files to inform the generated CLAUDE.md:
- `departments/shared/CONTEXT.md` — Jay's background, preferences, communication style
- `departments/kitchen/CLAUDE.md` — Reference example of a well-structured department

### 4. Generate the CLAUDE.md
Follow the structure from the kitchen example but make it genuinely useful for the new domain. The CLAUDE.md is what the Claw agent reads when handling requests for this department, so it needs to be practical and specific.

**Required sections:**

```markdown
# Claw - [Name] Department
[One-line description of what this department covers]

## Scope
INCLUDES: [specific responsibilities, comma-separated]
EXCLUDES: [things that belong to other departments, with which dept in parens]

## Primary Workflow: [Domain-Specific Workflow Title]
[The main multi-step workflow for this department, with clear steps.
Think about what a real user would actually ask this department to do
most often, and design the workflow around that.]

### Step 1: ...
### Step 2: ...
...

## Quick Commands
- [3-5 shorthand patterns Jay might use, with what they trigger]

## Response Style
Audio-first. Lead with spoken summary for glasses speakers.
Follow with structured list for Telegram visual reference.

## Jay's Preferences
- [Domain-relevant preferences, seeded from CONTEXT.md]
- [Use (learning) for preferences not yet known]

## [Domain-Specific Memory Section(s)]
[Sections that track state over time — inventories, logs, histories,
schedules, etc. Initialize as empty with clear format.]
```

### 5. Be creative and domain-aware
This is the most important part. Don't just fill in a template mechanically — think about what makes this domain unique:

- **Workflows should be real.** A fitness department needs workout logging and progress tracking, not generic "process request" steps. A finance department needs expense categorization and budget tracking, not "analyze data."
- **Memory sections should track what matters.** A travel department might track upcoming trips, loyalty programs, and passport expiration. A pet care department might track vet visits, medication schedules, and feeding routines.
- **Quick commands should be things Jay would actually say.** Think about voice-first interaction through smart glasses. Short, natural phrases.
- **Scope boundaries matter.** Be explicit about what goes to other departments to prevent routing confusion. If you're not sure what other departments exist, check `departments/` to see what's already set up.
- **Preferences should be reasonable defaults.** Seed from CONTEXT.md where relevant, use `(learning)` for unknowns.

### 6. Write the file
```bash
mkdir -p departments/[name]
```
Then write the generated CLAUDE.md to `departments/[name]/CLAUDE.md`.

### 7. Confirm
Tell the user:
- The department was created
- Show the key sections (scope, primary workflow name, quick commands)
- Mention they can edit `departments/[name]/CLAUDE.md` to refine it
