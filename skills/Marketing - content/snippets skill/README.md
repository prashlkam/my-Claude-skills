# generate-snippets-from-url-list

A Claude skill that takes a list of URLs and automatically generates a SEO-optimized Word document containing content snippets for each URL.

---

## What It Does

For each URL you provide, this skill will:

1. **Fetch the title** — works with web pages, PDF documents, and YouTube videos
2. **Extract full content** — scrapes web pages, reads PDF text, or retrieves YouTube transcripts
3. **Generate SEO keywords** — produces the top 5 SEO keywords based on the title
4. **Write a 60-word summary** — condenses the full content into a concise snippet
5. **Rewrite with SEO keywords** — rewrites the summary to naturally incorporate the keywords
6. **Output a Word document** — compiles all snippets into a single `.docx` file, one per URL

Each snippet in the output document contains:
- The original URL
- The page/video/document title
- The top 5 SEO keywords
- The final SEO-optimized summary

---

## Supported URL Types

| Type | Title Source | Content Source |
|---|---|---|
| Web pages | HTML `<title>` tag | Full page text |
| PDF documents | PDF metadata/heading | Full document text |
| YouTube videos | Video title | Auto-generated transcript |

---

## Setup Guide

### Prerequisites

This skill runs inside Claude's computer use environment and requires the following to already be available:

- **`docx` skill** — used to generate the Word document output. Ensure the `docx` skill is present at `/mnt/skills/public/docx/SKILL.md`.
- **Network access** — Claude must be able to fetch external URLs (web pages, PDFs, YouTube).
- **Python environment** — with `python-docx` available (handled automatically by the `docx` skill).

### Installation

1. Place the skill folder in the user skills directory:

```
/mnt/skills/user/generate-snippets-from-url-list/SKILL.md
```

2. Verify the file is readable by Claude — it should appear in the `available_skills` list in your system prompt.

3. That's it! No additional configuration is needed.

---

## How to Use

Simply start a conversation with Claude and ask it to generate snippets. Claude will recognize the skill and trigger it automatically.

**Example prompts:**

> "Generate snippets for these URLs: [paste your list]"

> "Use the generate-snippets-from-url-list skill on the following URLs..."

> "Create SEO content snippets for this list of links."

Claude will ask you to provide the URLs if you haven't already, then process each one and deliver a downloadable `.docx` file.

---

## Output Format

The output is a **Word document (`.docx`)** with one section per URL. Each section is structured as:

```
URL:        https://example.com/article
Title:      Example Article Title
Keywords:   keyword1, keyword2, keyword3, keyword4, keyword5
Summary:    A 60-word SEO-optimized summary incorporating the top keywords...
```

---

## Tips & Limitations

- **Large URL lists** may take several minutes to process since each URL is fetched individually.
- **Paywalled or login-protected pages** cannot be accessed — Claude will note any URLs it was unable to retrieve.
- **YouTube transcripts** depend on auto-generated captions being available on the video.
- For best results, provide clean, direct URLs (avoid URL shorteners if possible).

---

## Skill File Location

```
/mnt/skills/user/generate-snippets-from-url-list/SKILL.md
```

The `.skill` package file (used for distribution/installation) is a ZIP archive containing the `SKILL.md` file inside a folder named `generate-snippets-from-url-list/`.
