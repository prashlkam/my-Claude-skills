# Setup Guide: Research Peers on Topic

This guide walks you through installing, configuring, and using the **research-peers-on-topic** skill in Claude.

---

## Prerequisites

Before using this skill, make sure the following are available in your Claude environment:

| Requirement | Why It's Needed |
|---|---|
| **Web search** | The skill researches peers via the web (company sites, LinkedIn, blogs) |
| **File creation / Code execution** | The skill generates a `.docx` Word document as output |
| **docx npm package** | Used to build the structured Word document — installed automatically when the skill runs |

No API keys or external accounts are required.

---

## Installation

### Option A: Import the `.skill` file

1. Obtain the `research-peers-on-topic.skill` file.
2. In Claude, go to **Settings → Skills** (or the equivalent skill management area in your environment).
3. Upload the `.skill` file. Claude will install it and make it available for future conversations.

### Option B: Manual installation (advanced)

1. Create a folder named `research-peers-on-topic/` in your skills directory.
2. Place the `SKILL.md` file inside it with the skill's frontmatter and instructions.
3. Ensure the skill directory is mounted and accessible to Claude.

---

## How to Use the Skill

Once installed, simply ask Claude to research a topic across peer organizations. The skill will activate and walk you through three prompts:

### Step 1 — Provide the Topic

Claude will ask you for the **main research topic**.

> *Example:* "Generative AI adoption in financial services"

### Step 2 — Provide Pointers

Claude will ask for a **list of pointers** — these are specific angles or subtopics to guide the research.

> *Example:*
> - Regulatory compliance
> - Customer-facing use cases
> - Internal productivity gains

### Step 3 — Provide the Peer List

Claude will ask for a **list of peers** (companies or organizations) to research.

> *Example:*
> - JPMorgan Chase
> - Goldman Sachs
> - HSBC
> - Deutsche Bank

---

## What Happens Next

After collecting your inputs, the skill:

1. **Spawns parallel research agents** — one for the main topic, and one per peer (each researching `"<topic> for <peer>"`).
2. **Gathers findings** from company websites, LinkedIn posts, and blog platforms.
3. **Consolidates everything** into a Word document with the following structure:

```
📄 Research Output (.docx)
├── Main Topic of Research
│   ├── Findings (based on topic + pointers)
│   └── Sources / References
├── Peer 1: [Name]
│   ├── Findings
│   └── Sources / References
├── Peer 2: [Name]
│   ├── Findings
│   └── Sources / References
└── ...
```

The `.docx` file is then made available for you to download.

---

## Triggering the Skill

The skill triggers when you ask Claude something like:

- "Research how our peers are approaching [topic]"
- "Create a competitive research document on [topic] for these companies: ..."
- "Generate a peer research report on [topic]"

If the skill doesn't trigger automatically, you can reference it directly: *"Use the research-peers-on-topic skill to..."*

---

## Tips for Best Results

- **Be specific with pointers.** Vague pointers like "strategy" yield broad results. Specific pointers like "cloud migration roadmap for retail banking" produce much more targeted findings.
- **Use well-known peer names.** The skill searches the public web, so peers with strong online presence yield richer results.
- **Keep the peer list manageable.** 3–8 peers is the sweet spot. More peers means longer research time.
- **Review sources.** The document includes references for each section — verify key claims against the original sources.

---

## Troubleshooting

| Issue | Solution |
|---|---|
| Skill doesn't trigger | Try explicitly mentioning "research peers on topic" in your prompt |
| Thin results for a peer | The peer may have limited public content on that topic — try broadening your pointers |
| No `.docx` output | Ensure file creation is enabled in your Claude settings |
| Missing sources | Some web content may not be accessible — the skill notes what it found |

---

## Customizing the Skill

To modify the skill's behavior, edit the `SKILL.md` file:

- **Add new research sources** — extend the source list beyond company websites, LinkedIn, and blogs
- **Change the document structure** — adjust the output format or add new sections
- **Refine the agent prompts** — tune how each peer's research query is constructed

After editing, repackage the skill (if using a `.skill` file) or restart the session to pick up changes.
