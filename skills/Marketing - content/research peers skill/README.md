# Research Peers on Topic

A Claude skill that performs parallel web research on a given topic across a list of peer companies or organizations, then consolidates everything into a structured Word document.

## How It Works

The skill follows a three-step input flow, then kicks off parallel research agents:

### 1. Inputs

You'll be prompted for:

- **Topic** — The main subject to research (e.g., "AI-powered customer support")
- **Pointers** — A list of focus areas or angles to guide the research
- **Peers** — A list of companies/organizations to research against the topic

### 2. Research Phase

The skill spins up parallel research agents:

- **One agent** researches the main topic using the provided pointers
- **One agent per peer** researches `"<main topic> for <peer>"` — investigating how each peer approaches the topic

All agents run concurrently for faster results.

### 3. Output

A Word document (`.docx`) structured as:

| Section | Contents |
|---|---|
| **Main Topic** | Findings based on the topic and pointers provided |
| | Sources / references |
| **Peer 1** | Research findings specific to that peer |
| | Sources / references |
| **Peer 2** | Research findings specific to that peer |
| | Sources / references |
| ... | *(one section per peer)* |

## Research Sources

The skill pulls from:

- Company websites of the listed peers
- LinkedIn posts from the peers
- Blogs on popular blogging platforms

## Example Usage

> **Topic:** "Generative AI adoption in banking"
> **Pointers:** regulatory compliance, customer-facing use cases, internal productivity
> **Peers:** JPMorgan Chase, Goldman Sachs, HSBC, Deutsche Bank

This would produce a document covering the main topic plus a dedicated section for each bank's approach to generative AI adoption.
