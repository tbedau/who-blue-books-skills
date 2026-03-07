# WHO Blue Books Skills

An AI skill for looking up, studying, and learning the WHO Classification of Tumours (Blue Books).

An [Agent Skill](https://agentskills.io) that works with [`who-api-client`](https://github.com/tbedau/who-api-client) to turn the WHO Blue Books into a structured, active learning experience. Works with [Claude Code](https://claude.ai/code), [OpenAI Codex](https://developers.openai.com/codex), [Gemini CLI](https://github.com/google-gemini/gemini-cli), [OpenClaw](https://openclaw.ai/), and [many other agents](https://agentskills.io/home).

## What You Need

- A [WHO Classification of Tumours](https://tumourclassification.iarc.who.int/) subscription
- Python 3.10+
- An LLM agent with [Agent Skills support](https://agentskills.io/home) or shell access

## Installation

### 1. Install the CLI

Install [`uv`](https://docs.astral.sh/uv/getting-started/installation/) if you don't have it yet (`curl -LsSf https://astral.sh/uv/install.sh | sh`), then:

```bash
uv tool install who-api-client
# or: pip install who-api-client
```

### 2. Authenticate

```bash
who-api-client login
```

### 3. Install the skill

#### Option 1: CLI Install (Recommended)

Use [npx skills](https://github.com/vercel-labs/skills) to install the skill directly:

```bash
npx skills add tbedau/who-blue-books-skills
```

This automatically installs to your `.agents/skills/` directory and works across all supported agents.

#### Option 2: Clone and Copy

```bash
git clone https://github.com/tbedau/who-blue-books-skills.git
cp -r who-blue-books-skills/who-blue-books .agents/skills/
```

#### Option 3: Manual (any LLM with shell access)

Paste the contents of `who-blue-books/SKILL.md` into your agent as a system prompt or custom instruction.

### 4. Start using it

```
/who-blue-books What can you help me with?
/who-blue-books I want to study breast tumours
/who-blue-books What is the difference between NIFTP and follicular adenoma?
/who-blue-books Deep dive on mature B-cell lymphomas
```

## Available Skills

| Skill | Description |
|-------|-------------|
| [who-blue-books](who-blue-books/) | Study WHO Classification of Tumours content for pathology board prep. Lookup diagnostic criteria, generate study plans, and produce deep dive teaching reviews. |

## Skill Structure

```
who-blue-books/
  SKILL.md              # Main skill: routing + core reference (CLI, book IDs, section structure)
  references/
    lookup.md           # Workflow for ad-hoc questions (lookups, comparisons, differentials)
    study-plan.md       # Workflow for generating structured study plans
    deep-dive.md        # Workflow for comprehensive teaching reviews (quality centerpiece)
```

The skill uses [progressive disclosure](https://agentskills.io/specification#progressive-disclosure): the agent loads `SKILL.md` on activation, then reads only the relevant workflow reference file based on what you ask for.

| Workflow | Loaded when... | Output |
|----------|---------------|--------|
| **Lookup** | You ask a specific question: "What is GIST?", "FL vs MCL?", "Diagnostic criteria for phyllodes tumour?" | Focused answer with WHO citations |
| **Study Plan** | You want to work through a Blue Book systematically: "I want to study Endocrine and Neuroendocrine Tumours" | Themed clusters with deep dive prompts |
| **Deep Dive** | You want comprehensive teaching on a topic, standalone or from a study plan | 2000-4000 word teaching review |

## Example Prompts

### Lookup (ad-hoc questions)

```
What is NIFTP and how does it differ from follicular adenoma?
```

```
Differential diagnosis of spindle cell renal tumours — key morphologic
and immunohistochemical features to distinguish them
```

```
Compare follicular lymphoma and mantle cell lymphoma: morphology, IHC,
molecular, and grading differences
```

```
What are the WHO diagnostic criteria for phyllodes tumour, and how do I
distinguish benign from borderline from malignant?
```

### Study plans

```
I want to study the Haematolymphoid Tumours Blue Book systematically.
Generate a study plan with clusters and deep dive prompts.
```

```
Create a study plan for Endocrine and Neuroendocrine Tumours, focused on
the highest-yield entities for board prep
```

### Deep dives (comprehensive teaching reviews)

#### Standalone

```
Teach me about thyroid follicular-patterned neoplasms: FND, follicular
adenoma, NIFTP, follicular carcinoma, and FVPTC. Focus on the morphologic
spectrum, diagnostic criteria, and how to tell them apart at the microscope.
```

```
Deep dive on sarcomas with myogenic differentiation
```

```
I want to learn about salivary gland tumours with myoepithelial
differentiation
```

#### From a study plan

```
Generate the deep dive for Cluster 5 from my study plan: Acute Myeloid
Leukaemia. Cover AML with recurrent genetic abnormalities, AML with
myelodysplasia-related changes, and therapy-related myeloid neoplasms.
Emphasize morphology, cytogenetics, and differential diagnosis.
```

## Directing Output

By default, output appears inline — in your terminal, chat window, or whichever interface your agent uses. You can also ask the agent to write output to any format:

### Obsidian vault

```
Create a deep dive on mature B-cell lymphomas and save it as a note in
my Obsidian vault at ~/Documents/MyVault/ with YAML frontmatter
(tags: board-prep, hematopathology) and wikilinks for cross-references
```

### Markdown files

```
Generate a study plan for Breast Tumours and save it to
~/study/breast-tumours-study-plan.md
```

### Word document

```
Create a deep dive on renal cell carcinoma subtypes and export it as a
Word document at ~/Desktop/RCC-deep-dive.docx
```

### Single-page HTML

```
Create a deep dive on salivary gland tumours and save it as a self-contained
HTML page at ~/Desktop/salivary-gland-deep-dive.html with a clean readable
style, table of contents sidebar, and dark mode toggle
```

### Multiple files

```
Generate a study plan for Soft Tissue and Bone Tumours. Save the plan to
~/study/soft-tissue/plan.md, then generate deep dives for the first 3
clusters and save each as separate markdown files in ~/study/soft-tissue/
```

The agent handles the formatting and file writing — just tell it where and how you want the output.

## Suggested Study Workflow

For optimal learning, follow this evidence-based pipeline:

1. **Deep Dive** — Generate deep dives using this system. These compress raw WHO content into high-yield teaching reviews focused on morphology, immunophenotype, and differential diagnosis.

2. **Elaboration** — Actively engage with each deep dive: ask yourself "why?" about each fact. Interleave topics across organ systems (study FL, then switch to a soft tissue topic, then come back to MZL). This feels harder but produces significantly better retention.

3. **Retention** — Generate Anki flashcards from your deep dives. Focus on discriminating features ("What marker distinguishes MCL from FL?"), diagnostic criteria, and classic associations ("What translocation is associated with FL?").

4. **Testing** — Practice questions from board-prep resources. Review every answer — even correct ones. Trace errors back to knowledge gaps (re-brief), discrimination failures (more interleaving), or retrieval failures (more Anki reps).

**Key principles:** Compression before memorization. Active over passive. Interleave aggressively. Anki is for retention, not learning — complete stages 1-2 first.

## Customization

These skills are yours to modify. Edit any of the workflow files in `references/` to match your learning style — change the output format, adjust content priorities, add emphasis on areas you care about, or tweak the tone.

You can also ask your agent to create entirely new workflows. The skill uses [progressive disclosure](https://agentskills.io/specification#progressive-disclosure), so adding a new capability is as simple as writing a new reference file and adding a routing entry in `SKILL.md`. For example, you could ask your agent: "Add a new workflow that generates Anki flashcard decks from WHO chapters" — and it can create the reference file and wire it up for you.

## Compatible Agents

This skill follows the open [Agent Skills specification](https://agentskills.io/specification), supported by:

- [Claude Code](https://claude.ai/code) / [Claude](https://claude.ai/)
- [OpenAI Codex](https://developers.openai.com/codex) / [ChatGPT](https://chatgpt.com/)
- [Gemini CLI](https://github.com/google-gemini/gemini-cli)
- [OpenClaw](https://openclaw.ai/)
- [GitHub Copilot](https://github.com/) / [Cursor](https://cursor.com/)
- And [many more](https://agentskills.io/home)

Any agent with shell access can also use it by pasting `who-blue-books/SKILL.md` as a system prompt.

## Disclaimer

This is an unofficial, community-built skill system. It is not affiliated with, endorsed by, or officially connected to the World Health Organization (WHO) or the International Agency for Research on Cancer (IARC).

- **This is not medical advice.** All AI-generated output must be independently verified by a qualified professional. Use entirely at your own risk.
- A valid WHO Classification of Tumours subscription is required
- All content accessed through `who-api-client` is subject to the [IARC/WHO Terms of Use](https://publications.iarc.who.int/Terms-Of-Use)
- This system generates synthesized teaching material — it does not store or redistribute WHO content
- The underlying API is undocumented and may change without notice

## License

MIT
