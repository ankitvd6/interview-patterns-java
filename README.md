# Interview Patterns Java

Java-focused DSA and interview pattern notes published as a clean GitHub Pages
site.

Live site: <https://ankitvd6.github.io/interview-patterns-java/>

## What This Repo Is

This repo is a lightweight Jekyll notes site for interview prep. It is meant to
turn Markdown notes into readable web pages with shared styling, navigation, and
metadata.

Current focus areas:

- DSA pattern explanations
- Java-ready templates
- Short review notes before interviews
- Searchable, linkable web pages for each topic

## Site Structure

```text
.
|-- _config.yml                 # Jekyll and GitHub Pages settings
|-- index.md                    # Home page and note listing
|-- _notes/                     # Markdown notes published as pages
|-- _layouts/                   # Shared page and note layouts
|-- assets/css/style.css        # Site styling
`-- .github/workflows/pages.yml # GitHub Pages deployment workflow
```

Notes are stored in `_notes/`. Jekyll publishes each note under:

```text
/notes/<note-file-name>/
```

Example:

```text
_notes/faang-binary-search-interview-notes.md
```

publishes to:

```text
https://ankitvd6.github.io/interview-patterns-java/notes/faang-binary-search-interview-notes/
```

## Add a New Note

Create a Markdown file in `_notes/` with front matter:

```yaml
---
title: Two Pointers
description: Java-focused notes for two-pointer interview patterns.
section: Arrays
summary: Fast review notes for two-pointer patterns.
category: DSA Pattern
tags: [arrays, two-pointers, java]
order: 20
---
```

Then write the note content below the front matter:

```markdown
# Two Pointers

## Mental Model

Use two indexes to scan, shrink, or compare parts of the input without nested
loops.
```

The home page automatically lists notes from `_notes/`, sorted by `order`.

## Publish Flow

The site is deployed through GitHub Actions using:

```text
.github/workflows/pages.yml
```

On every push to `main`, the workflow:

1. Checks out the repo
2. Configures GitHub Pages
3. Builds the site with Jekyll
4. Uploads the generated site artifact
5. Deploys it to GitHub Pages

## Local Preview

If you want to preview changes locally with Jekyll, use:

```bash
bundle exec jekyll serve
```

That starts a local server so you can review the site before pushing changes.

## Repository

GitHub repo: <https://github.com/ankitvd6/interview-patterns-java>

GitHub Pages site: <https://ankitvd6.github.io/interview-patterns-java/>
