---
name: explain-diff-html
description: Use when the user asks for a rich, interactive explanation of a code change, diff, branch, commit range, or pull request, and wants the result as a self-contained HTML file. Trigger for requests to explain a PR, walk through a branch, teach a diff, make an interactive code-change explainer, or produce an HTML explanation with diagrams and quiz questions.
---

# Explain Diff HTML

Create a single self-contained HTML explanation of the specified code change. Treat the output as a teaching artifact for a reader who may be new to the surrounding system.

## Workflow

1. Resolve the change to explain.
   - If the user names a PR, branch, commit, file, or diff, inspect that target.
   - If the target is unclear, inspect `git status`, recent branches, and the current branch diff against the likely base branch. Ask only if there is no reasonable local target.
   - Read the diff and enough surrounding code to explain the existing system, not just the touched lines.

2. Build a mental model before writing.
   - Identify the old behavior, the new behavior, and why the change exists.
   - Trace the relevant call paths, data flow, UI flow, schema, or protocol boundaries.
   - Note edge cases, compatibility concerns, tests, migrations, user-visible effects, and anything deliberately left unchanged.

3. Produce one long-page HTML file.
   - Save it outside the repo in `/tmp`.
   - Make the filename start with today's date in `YYYY-MM-DD-` format, followed by a short slug, for example `/tmp/2026-01-12-explanation-auth-cache.html`.
   - Include all CSS and JavaScript inline. Do not require external assets, network access, build tools, or a server.
   - Use a table of contents with in-page anchors. Do not use tabs for the top-level structure.
   - Add responsive styling so the page is readable on a phone.

## Required Sections

Include these sections in this order:

1. **Background**
   - Start with a broad, beginner-friendly explanation of the surrounding system. Make it skippable for readers who already know the codebase.
   - Then narrow to the specific subsystem, files, abstractions, and invariants that matter for this change.

2. **Intuition**
   - Explain the essence of the change before the details.
   - Use concrete toy data, small scenarios, and simple before/after examples.
   - Use diagrams liberally where they clarify state, flow, hierarchy, UI changes, or timing.

3. **Code**
   - Walk through the implementation at a high level.
   - Group changes by concept, behavior, or layer rather than by raw file order when that is clearer.
   - Use short code excerpts in `<pre>` blocks and explain what changed around them.

4. **Diff based Quiz**
   - Include five medium-difficulty multiple-choice questions.
   - Make questions test actual understanding of the change, not trivia or gotchas.
   - Use interactive JavaScript so clicking an answer shows whether it is correct and gives explanatory feedback.

5. **Python Language Quiz**
   - Include five medium-difficulty text box based questions, for which the user has to write python code for.
   - Make questions test actual understanding of the change, not trivia or gotchas.
   - Use python interpreter to verify the written code.
   - The goal is to improve user's Python understanding through the codebase. 

## Writing Style

Write in clear, careful systems prose: define terms when they first appear, move from concrete examples to general principles, and use smooth transitions between sections. Prefer short paragraphs, precise nouns, and examples with realistic values over vague summaries.

The explanation should be engaging but not promotional. Avoid filler, exaggerated claims, and decorative UI copy.

## Diagrams And Visuals

Use a small number of reusable diagram families throughout the page:

- **Simplified UI diagrams** for user-visible behavior, form flows, navigation, or state changes.
- **System/data-flow diagrams** for components, functions, APIs, queues, caches, persistence, or message passing.
- **State transition diagrams** for lifecycle changes, validation paths, retries, or failure handling.
- **Before/after comparisons** for changed behavior.

Build diagrams with semantic HTML and CSS. Do not use ASCII diagrams. Include example data directly inside diagrams when useful.

## HTML Requirements

- Use headings, cards only for repeated items or contained examples, callouts for key definitions and edge cases, and visually distinct code blocks.
- Do not put page sections inside floating cards.
- Use `<pre>` for code blocks.
- Ensure every code block preserves newlines. Before finishing, scan the HTML source and confirm each `<pre>` or custom code container is styled with `white-space: pre` or `white-space: pre-wrap`.
- Escape HTML-sensitive characters inside code excerpts.
- Keep the quiz usable without any framework.
- Make the page work when opened directly from the filesystem.

## Validation Checklist

Before responding to the user:

1. Confirm the file exists under `/tmp` and its basename starts with today's date in `YYYY-MM-DD-` format.
2. Confirm the page contains Background, Intuition, Code, and Quiz sections.
3. Confirm all CSS and JavaScript are inline.
4. Confirm no ASCII diagrams are used.
5. Confirm every code block preserves whitespace.
6. Open or inspect the HTML enough to catch obvious broken markup, missing anchors, overlapping text, or nonfunctional quiz interactions.
7. Give the user the absolute path to the HTML file and summarize what change it explains.
