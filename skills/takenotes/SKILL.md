---
name: takenotes
description: >-
  Scaffold a course Notes folder like WGU D482: Sections 1-N (Summary, Quiz, topic stubs),
  per-lesson files (reading, summaries, knowledge checks). Use when user says /takenotes,
  "take notes setup", "notes folder like D482", "course notes scaffold", or names a new class
  and wants the same Markdown structure.
---

# Course notes scaffold (D482-style)

## Goal

Create a **`Notes/`** directory of plain **Markdown** (not Notion) with predictable filenames so Sections and Lessons stay searchable and grep-friendly.

## Default layout (mirror D482)

Put notes under:

`~/Desktop/PROJECTS/WGU/<Course-Title>-<CLASSCODE>/Notes/`

If the user gives a different root or course folder, use that path instead.

## Naming rules

1. **`Notes/00-Inbox.md`**: quick capture for links and half-written thoughts (optional but default).

2. **Sections** (course modules / big chunks):

   - `Section-<n>-Summary.md`
   - `Section-<n>-Quiz.md`
   - Optional third file when the section has one clear umbrella topic:  
     `Section-<n>-<Short-Descriptive-Title>.md`  
     Example: `Section-1-Business-and-Technology-Needs.md`

3. **Lessons** (ordered units inside a section):

   - `Lesson-<n>-Summary.md`
   - `Lesson-<n>-Knowledge-Check.md`
   - Reading / video / transcript style (pick what matches the material):  
     `Lesson-<n>-<Source-or-Reading>-Reading-Notes.md`  
     `Lesson-<n>-<Platform>-<Title>-Transcript-Notes.md`  
     Sub-lessons if needed: `Lesson-<n>-<m>-<Topic>.md` (e.g. `Lesson-1-1-...`)

4. Files use **hyphens**, **Title-Case-ish** descriptive tails, **`Section` / `Lesson` prefixes**, **`.md` only**.

5. Frontmatter optional: if present, prefer Obsidian-compatible YAML (`title`, `tags`, `class`, `section`).

## What to do when invoked

1. Ask only if missing: **course name**, **class code** (e.g. DXYZ), **number of sections** (or syllabus outline). If user pasted syllabus, infer section count.

2. **Create** `<course-folder>/Notes/` and:

   - `00-Inbox.md` with a short "dump raw links / half thoughts here" blurb.

   For each Section `1 .. N`:

   - `Section-<n>-Summary.md`  
   - `Section-<n>-Quiz.md`  

   For each Lesson the user expects (if unknown, create **lesson stubs for section totals** only after user confirms approximate lessons per section, OR create `Lesson-<section>-stub` placeholders: prefer asking "lessons per section or full syllabus".

   Minimal safe default when counts unknown: scaffold **Sections only** plus `00-Inbox.md`, and add a `Notes/README.md` listing conventions (see below).

3. **Seed each stub** with headings (no fake content):

```markdown
# Section N — Summary

## Objectives


## Key terms


## Narrative


## References / citations


---

# Section N — Quiz

## Questions


## Answers (after attempt)


---

# Lesson N — Summary

## Goal


## Takeaways


## Open questions


---

# Lesson N — Knowledge Check

## Prompts


## Your answers


---

# Lesson N — <Reading title> — Reading notes

## Citation


## Quotables / definitions


## How this ties to the class


---

# Lesson N — <Video> — Transcript notes

## Timestamp anchors


## Main ideas


## Glossary additions
```

4. **`Notes/README.md`** (short): one paragraph on folder layout, naming, "everything is Markdown on disk".

5. **Do not** empty-delete user files; only add missing scaffolds. **Do not** put AI trailers in commits if you commit.

## Triggers

- `/takenotes`
- Phrases: "notes like D482", "course notes scaffold", "WGU notes structure", "set up Notes folder for `<class>`"

## Copy-paste prompt (user can trigger without invoking skill name)

```text
Use the takenotes skill: create a Notes/ folder like my D482 Secure Network Design notes.
Course: [NAME]. Code: [CODE]. Sections: [N].
Root: ~/Desktop/PROJECTS/WGU/[Course-Title]-[CODE]/ (or paste my path).
Scaffold Section Summary + Quiz stubs for each section, 00-Inbox.md, README.md conventions.
Ask me lesson counts only if you cannot infer them from my syllabus paste.
```
