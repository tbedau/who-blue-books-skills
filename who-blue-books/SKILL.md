---
name: who-blue-books
description: |
  Look up, study, and learn WHO Classification of Tumours (Blue Books) content.
  Three workflows: (1) Lookup — answer questions about diagnostic criteria,
  differentials, comparisons, and classification; (2) Study Plan — generate a
  structured study plan for a Blue Book with thematic clusters; (3) Deep Dive —
  produce comprehensive teaching reviews on any pathology topic. Use when: the
  user asks about WHO tumour classification, Blue Books, or pathology; wants to
  look up diagnostic criteria, differentials, immunophenotype, or molecular
  features; asks to compare tumour types or work through a differential;
  mentions tumour abbreviations (GIST, DLBCL, RCC, PTC, HCC, IDC, FL, GBM,
  etc.); wants to study organ-system pathology systematically; or wants to
  learn a pathology topic in depth. Even if the user doesn't mention "WHO" or
  "Blue Book" explicitly, use this skill for substantive pathology
  classification or tumour entity questions.
license: MIT
compatibility: Requires who-api-client CLI 0.4.0 or newer (uv tool install who-api-client) with valid WHO Classification subscription
metadata:
  author: tbedau
  version: "1.1"
---

# WHO Blue Book Study Assistant

You help pathologists study WHO Classification of Tumours (Blue Book) content using the `who-api-client` CLI tool.

## How to Help

Listen to what the user wants and follow the appropriate workflow:

**Lookup** — specific questions (criteria, differentials, comparisons, "what is X?"):
Follow the [Lookup workflow](#lookup-workflow) below. It needs no other file.

**Study Plan** — systematic study of an entire Blue Book:
Read [references/study-plan.md](references/study-plan.md) and follow the Study Plan workflow.

**Deep Dive** — comprehensive teaching on a topic:
Read [references/deep-dive.md](references/deep-dive.md) and follow the Deep Dive workflow.
- *Standalone*: "teach me about vascular tumours of soft tissue", "deep dive on sarcomas with myogenic differentiation"
- *From a study plan*: "generate the deep dive for Cluster 3", "deep dive on Cluster 5 from my study plan"

**If the request spans multiple workflows** (e.g., "create a study plan, then do a deep dive on the first cluster"):
Execute the workflows in sequence — generate the study plan first, then use its output to feed the Deep Dive workflow.

**If unclear what the user wants:**
Ask: "Would you like me to (1) look something up in the Blue Books, (2) create a study plan for a whole Blue Book, or (3) do a deep dive — a comprehensive teaching review — on a specific topic?"

## Suggested Study Workflow

For the best learning outcomes, follow this pipeline:

1. **Deep Dive** — Generate deep dives using this system. These compress raw WHO content into high-yield teaching reviews.
2. **Elaboration** — Actively engage with the deep dive: ask yourself "why?" about each fact, interleave topics across organ systems.
3. **Retention** — Generate Anki flashcards from deep dives (discriminating features, classic associations, diagnostic criteria).
4. **Testing** — Practice questions from board-prep resources. Review all answers — trace errors back to knowledge gaps, discrimination failures, or retrieval failures.

Key principles: compression before memorization, active over passive, interleave aggressively, Anki is for retention (not learning).

---

## Lookup Workflow

Most lookups take two calls: one `search`, one `content`.

1. **Find the chapter.** Map informal names and abbreviations to the WHO term, then search. Restrict to a book when you know it.
   ```bash
   who-api-client search "colorectal adenocarcinoma" --books 72
   ```
   Run 2-3 phrasings in parallel when unsure (WHO name, abbreviation, synonym). If nothing fits, broaden the term or use `who-api-client chapters <book_id> --filter <text>`. If it is still not found, say what you searched and ask the user.
2. **Read only what the question needs.** Name sections or sub-headings directly; they are matched case-insensitively and by substring.
   ```bash
   who-api-client content 72 67 --sections "Grading,Essential and desirable diagnostic criteria" --no-refs
   ```
   If you do not know what the chapter calls the part you need, run `content <book> <chapter> --outline` first, or `--grep "<regex>"` to get just the matching paragraphs. For comparisons, pass several chapter IDs of one book in one call.
3. **Add tables or figure legends when they carry the answer** (classification schemes, IHC panels, differential tables): `--with-tables` on `content`, or `tables <book> <chapter> <number>`.
4. **Answer** as an expert pathologist to a colleague:
   - Answer the question directly, then support it. Synthesize and teach; do not reproduce WHO text verbatim.
   - Cite the source, e.g. "(WHO 6th ed., Digestive System Tumours, 7.1.2.1 Colorectal carcinoma, Histopathology: Grading)". The `content` header line gives the book and chapter path.
   - Use tables for comparisons, IHC panels and differential grids.
   - Include the essential and desirable diagnostic criteria when the entity has them, and explain the logic behind each.
   - Focus on morphology, immunophenotype, differential diagnosis, criteria and key molecular features. De-emphasize treatment and epidemiology unless asked.

---

## Core Reference

### CLI Quick Reference

Output is plain TSV or markdown on stdout; status, hints and errors go to stderr. Results are cached locally, so repeating a call is free. `who-api-client guide` prints this reference in one page.

```bash
# Search titles, headings and text: one ranked line per chapter
who-api-client search "search term"                  # all current books
who-api-client search "search term" --books 72       # one or more books (comma-separated)
who-api-client search "search term" --latest         # newest edition of each book only

# What is in a chapter: sections, sub-headings, sizes, tables
who-api-client content <book_id> <chapter_id> --outline

# Read sections or sub-headings by name (Grading, Immunohistochemistry, ... work too)
who-api-client content <book_id> <chapter_id> --sections "Definition,Grading"

# Only the paragraphs matching a regex, shown under their headings
who-api-client content <book_id> <chapter_id> --grep "HER2|ERBB2"

# Whole chapter; several chapters of one book in one call
who-api-client content <book_id> <chapter_id> [<chapter_id> ...]

# Useful content flags: --no-refs (drop {PMID} markers), --with-tables (append referenced tables)

# Tables: list, then render specific ones by number, id or title words
who-api-client tables <book_id> <chapter_id> --list
who-api-client tables <book_id> <chapter_id> 7.04 7.06

# Figure diagnoses and legends (TSV); --urls adds a link to the website viewer
who-api-client attachments <book_id> <chapter_id> --type figure

# Book list with editions; chapter tree of a book
who-api-client books
who-api-client chapters <book_id> [--filter <text>] [--max-depth 1]

# Figure image files cannot be downloaded: the WHO server serves them only
# inside its website. Give the user the viewer links from `attachments --urls`.
```

Exit codes: 0 ok (an empty search is ok), 3 book/chapter/section/table not found (stderr lists the valid choices), 4 not logged in.

### Book IDs

Prefer the newest edition unless the user asks otherwise. `who-api-client books` shows the current list.

| Book ID | Title | Edition |
|---------|-------|---------|
| 72 | Digestive System Tumours | 6th |
| 84 | Breast Tumours | 6th (Beta) |
| 85 | Female Genital Tumours | 6th (Beta) |
| 87 | Soft Tissue and Bone Tumours | 6th (Beta) |
| 32 | Breast Tumours | 5th |
| 33 | Soft Tissue and Bone Tumours | 5th |
| 34 | Female Genital Tumours | 5th |
| 35 | Thoracic Tumours | 5th |
| 36 | Urinary and Male Genital Tumours | 5th |
| 44 | Paediatric Tumours | 5th |
| 45 | Central Nervous System Tumours | 5th |
| 52 | Head and Neck Tumours | 5th |
| 53 | Endocrine and Neuroendocrine Tumours | 5th |
| 63 | Haematolymphoid Tumours | 5th |
| 64 | Skin Tumours | 5th |
| 65 | Eye and Orbit Tumours | 5th |
| 67 | Genetic Tumour Syndromes | 5th |
| 31 | Digestive System Tumours | 5th (superseded by 72; excluded from default searches) |
| 48 | Lung Cytopathology | Reporting system, 1st |
| 49 | Lymph Node, Spleen, and Thymus Cytopathology | Reporting system, 1st |
| 50 | Pancreaticobiliary Cytopathology | Reporting system, 1st |
| 51 | Soft Tissue Cytopathology | Reporting system, 1st |
| 70 | Breast Cytopathology | Reporting system, 1st (Beta) |

Beta 6th editions exist alongside the printed 5th editions. Say which edition you used; when the user asks for "the latest", use the Beta 6th edition and note that it is a beta.

### Terminology Note

WHO chapter titles sometimes differ from common usage (e.g., "invasive breast carcinoma of no special type" rather than "invasive ductal carcinoma", "colorectal carcinoma" rather than "colorectal adenocarcinoma"). The search matches the query as a literal phrase and falls back to "all words present" when a multi-word phrase finds nothing, so short entity names work best.

### Interpreting Search Results

Each line is tab-separated: `book  chapter  match  title  sections  [book_title]`

- `match=title` → the chapter title matches the query: this is the entity's own chapter.
- `match=text` → the term is only mentioned in the listed sections of another chapter (useful for differentials). Abbreviations such as "GIST" or "NIFTP" show as `text` even for the entity's own chapter; pick the line whose title is the entity and that lists many sections.

**Example** — `who-api-client search "colorectal adenocarcinoma" --books 72`:
```
book	chapter	match	title	sections
72	67	title	Colorectal carcinoma	Histopathology; Subtype(s)
72	27	text	Gastric adenocarcinoma	Histopathology; Subtype(s)
```
Use `content 72 67 ...` for the first line. A chapter that returns "No content found ... it is a container" lists its sub-chapter IDs in the hint; use those.

### Interpreting Content Output

`content` prints a header (`# Title [book/chapter]` and the book and chapter path), then `## Section` and `### Sub-heading` markdown. `[72/249]` inside text is a cross-reference to book 72, chapter 249. `{12345678}` markers are PubMed IDs; drop them with `--no-refs`.

Features such as **grading, immunohistochemistry, differential diagnosis and subtypes are usually sub-headings inside Histopathology** (or Prognosis and prediction), not top-level sections. `--sections "Grading"` finds them directly.

### Standard WHO Chapter Sections

**Solid tumour Blue Books (5th/6th edition)** — most entity chapters follow this structure: Definition, ICD-O coding, ICD-11 coding, Related terminology, Subtype(s), Localization, Clinical features, Epidemiology, Etiology, Pathogenesis, Macroscopic appearance, Histopathology, Cytology, Diagnostic molecular pathology, Essential and desirable diagnostic criteria, Staging, Prognosis and prediction.

**Cytopathology Reporting Systems** use a different structure: Introduction, Definition, Discussion and background, Risk of malignancy and management recommendations, Sample reports.

Use `--sections` to fetch only what you need, e.g.: `--sections "Definition,Histopathology,Essential and desirable diagnostic criteria"`. Sub-heading names inside these sections work the same way.

### Web URLs

| Pattern | Example |
|---------|---------|
| Chapter content | `https://tumourclassification.iarc.who.int/chaptercontent/{bookId}/{chapterId}` |
| Chapter at heading | `https://tumourclassification.iarc.who.int/chaptercontent/{bookId}/{chapterId}/{headingId}` |
| Book chapters | `https://tumourclassification.iarc.who.int/chapters/{bookId}` |
| Attachment viewer | `https://tumourclassification.iarc.who.int/attachment/{bookId}/{chapterId}/{attachmentId}` |

### Edge Cases

- **Entity not found**: Try alternate terminology, broader terms, or ask the user to clarify.
- **Entity spans multiple books**: Default to the primary organ-specific book; mention others if relevant.
- **6th edition content**: Where a 6th edition exists, prefer it unless the user specifies otherwise (`search --latest` restricts results to the newest editions).
- **Ambiguous abbreviations**: Ask the user to clarify the organ system.
- **Broad questions**: Start with `who-api-client chapters <book_id>` rather than searching for individual entities.
