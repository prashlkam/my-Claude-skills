# Generate Snippets from URL List — Setup Guide

## Overview

The **Generate Snippets from URL List** skill takes a list of URLs and produces a Word document containing SEO-optimized snippets for each one. It supports web pages, PDF documents, and YouTube videos.

---

## What It Does

For each URL you provide, the skill will:

1. **Extract the title** — from the web page, PDF, or YouTube video.
2. **Extract the full content** — page text, PDF text, or a YouTube transcript.
3. **Generate 5 SEO keywords** based on the title.
4. **Write a 60-word summary** from the full content.
5. **Rewrite the summary** incorporating as many of the SEO keywords as possible.

The final output is a `.docx` Word document with a snippet block for each URL containing the URL, title, SEO keywords, and the keyword-optimized summary.

---

## Prerequisites

- **Claude.ai account** with the *Code Execution and File Creation* feature enabled (found in Settings → Feature Previews).
- **Network access** enabled in your Claude environment so the skill can fetch web pages, PDFs, and YouTube transcripts.
- URLs must be **publicly accessible** (no login-protected pages or private videos).

---

## Installation

1. Open **Claude.ai** and navigate to **Settings → Skills** (or the skill management area in your interface).
2. Click **Add Skill** or drag-and-drop the `generate-snippets-from-url-list.skill` file into the upload area.
3. Confirm the skill appears in your active skills list.

---

## How to Use

### Step 1 — Start the Skill

In any Claude conversation, ask Claude to generate snippets from a list of URLs. For example:

> *"Generate snippets for the following URLs."*

### Step 2 — Provide Your URLs

Paste your list of URLs. You can use any format — one per line, comma-separated, or as a numbered list:

```
https://example.com/blog/ai-trends-2026
https://example.com/reports/annual-review.pdf
https://www.youtube.com/watch?v=dQw4w9WgXcQ
```

### Step 3 — Wait for Processing

Claude will process each URL sequentially — fetching content, generating keywords, and writing summaries. This may take a minute or two depending on the number of URLs.

### Step 4 — Download Your Document

Once complete, Claude will provide a downloadable `.docx` file. Each snippet in the document includes:

- **URL** — the original link
- **Title** — extracted from the source
- **SEO Keywords** — top 5 keywords
- **Summary** — 60-word summary rewritten with SEO keywords

---

## Supported URL Types

| URL Type | Title Source | Content Source |
|---|---|---|
| Web page | Page `<title>` tag or `<h1>` | Full page text |
| PDF document | PDF metadata or first heading | Extracted PDF text |
| YouTube video | Video title | Auto-generated transcript |

---

## Tips for Best Results

- **Keep lists manageable.** Processing 5–15 URLs at a time works well. Very long lists may hit context limits.
- **Use direct URLs.** Shortened links (e.g., bit.ly) may not resolve correctly.
- **Check accessibility.** Pages behind paywalls, login walls, or CAPTCHAs cannot be fetched.
- **YouTube transcripts** depend on auto-captions being available for the video. Videos without captions may produce limited results.

---

## Troubleshooting

| Issue | Solution |
|---|---|
| "Unable to fetch URL" | Verify the URL is publicly accessible and correctly formatted (includes `https://`). |
| Missing YouTube transcript | The video may not have captions enabled. Try a different video or provide a summary manually. |
| PDF content is empty | The PDF may be image-based (scanned). OCR-based PDFs work best. |
| Output file not generated | Ensure the *Code Execution and File Creation* feature is enabled in Settings. |

---

## Example Prompt

```
Please generate SEO snippets for these URLs:

1. https://example.com/blog/machine-learning-basics
2. https://example.com/whitepapers/2025-industry-report.pdf
3. https://www.youtube.com/watch?v=abc123xyz
```

Claude will return a Word document with a structured snippet for each URL, ready for use in content planning, link roundups, or SEO workflows.
