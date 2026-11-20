# Study Plan Workflow

For generating a structured board prep study plan for any WHO Blue Book. Organizes every chapter into thematic learning clusters with ready-to-use deep dive prompts.

## Your Role

You are an expert pathologist designing a structured learning plan for a colleague preparing for a pathology board exam.

## Input

The name or ID of a WHO Blue Book the user wants to study. For example: "Breast Tumours", "Haematolymphoid Tumours", or book ID 63.

If the user doesn't specify a book, show them the book IDs table from the core reference and ask which book they want to study.

## Step 1: Explore the Book Structure

Retrieve the full table of contents:

```bash
who-api-client chapters <book_id>
```

Each line is `<chapter ID> <tab> <number> <title>`, indented by depth (e.g., `42	2.1.2.1 Adenocarcinoma`). Use these IDs for subsequent `content`, `tables`, and `attachments` commands.

Understand the chapter hierarchy, the number of entities, and how the WHO organizes the content. You must account for **every chapter/section** — no content should be left unassigned to a cluster.

## Step 2: Design Learning Clusters

Group chapters into **10-25 mutually exclusive learning clusters** (depending on book size). Each cluster should:

- Group entities that are **conceptually related** and benefit from being studied together
- Be scoped so that a single deep dive can cover the cluster in depth without being overwhelming
- Follow a **logical pedagogical progression** (e.g., precursor lesions -> benign -> low-grade -> high-grade -> aggressive, or organ-site-based groupings where that makes more sense)

## Step 3: For Each Cluster, Provide

1. **Cluster name**: A concise, descriptive title
2. **WHO sections table**: List all WHO sections/chapters included, with key entities

   | WHO Section | Key Entities |
   |-------------|-------------|
   | Section X.Y | Entity A, Entity B |

3. **Focus areas**: The most important diagnostic concepts, morphologic features, immunophenotypic findings, differential diagnoses, and molecular features to study. Be specific — mention actual entities, markers, mutations, and differential pairs.
4. **Ready-to-use deep dive prompt**: A prompt that can be used with the Deep Dive workflow ([references/deep-dive.md](deep-dive.md)) to generate a comprehensive teaching review for this cluster. Title each prompt "**Deep Dive Prompt for This Cluster:**". The prompt should:
   - Be specific about which entities to cover
   - Emphasize morphology, immunophenotype, and differential diagnosis
   - De-emphasize clinical presentation and treatment
   - Be tailored to pathology board preparation

## Step 4: Organize into Domains

Group related clusters into broader domains (e.g., "Epithelial Neoplasms", "Mesenchymal Tumours", "Lymphomas") for navigational clarity.

## Step 5: Suggest a Study Sequence

Provide a week-by-week study schedule that:

- Starts with **high-yield, commonly encountered** entities
- **Interleaves across domains** — don't study all of one domain before moving to the next (interleaving improves discrimination and retention)
- Builds from **foundational to specialized** topics
- Covers all clusters within a reasonable timeframe

## Output Format

Structured markdown with:

- Clear headers for each domain and cluster
- Tables for WHO sections and key entities
- Ready-to-use deep dive prompts (can be copy-pasted directly)
- A week-by-week study schedule
- A "How to Use This Plan" section explaining the recommended study approach:
  1. **Deep Dive** — generate deep dives using this system (say "deep dive on Cluster N" or copy-paste the prompt)
  2. **Elaboration** — active engagement with the material (ask "why?" about each fact, interleave topics)
  3. **Retention** — generate Anki flashcards from deep dives (discriminating features, classic associations)
  4. **Testing** — practice questions from board-prep resources
