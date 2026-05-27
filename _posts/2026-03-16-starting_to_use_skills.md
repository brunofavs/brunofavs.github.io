---
layout: post
title: Speeding workflows with SKILLS.md
date: 2026-05-27 11:12:00-0400
description: Learning to leverage skills in LLM agents to speed cumbersome workflows with the aid of the new *skills.sh* tool, allowing developers to install modular skills and reusable workflows into AI coding agents using simple terminal commands.

images:
  compare: true
  slider: true
---

A while back, an international friend visited, and we grabbed burgers at Ramona, spending most of the meal talking about AI workflows. My friend David is obsessed with efficiency. He runs a cluster of home servers that power autonomous agents handling all sorts of tasks on his behalf. At the core of that setup are agent "skills."

Claude introduced skills in 2025, and the rest of the AI industry quickly followed suit. The concept is straightforward: a skill is a markdown file containing detailed step-by-step instructions for completing a specific task. A good skill anticipates edge cases and includes fallbacks for when a step goes wrong.

The task itself can be anything — but the best candidates are tasks where a general-purpose LLM tends to struggle or produce inconsistent results. If a job requires very precise, repeatable behavior, a skill is the right tool.

Earlier this year, in January 2026, Vercel launched skills.sh — a platform that standardizes how skills are published and shared, making them accessible to anyone. It was a turning point, and David's enthusiasm for it was hard to ignore.

I decided to try it myself. One task I do regularly is reviewing articles in my field, working toward a review paper due by the end of the year. My issue is that I rarely need to dig into every methodological detail. Unless I'm deep in the development phase and looking to borrow ideas from someone's approach, a full read isn't necessary. You can always hand a PDF directly to an LLM like GPT or Claude, but the responses tend to be inconsistent and often miss what you actually care about.

Skills seemed like the right fix. I started by downloading two foundational ones: find-skills, published by Vercel, and skill-creator, published by Anthropic. Using the first, I searched for a skill to review scientific papers and landed on one that delivered solid results: research-paper-review. Then I used skill-creator to tailor it to my workflow. My customizations:

- Always search my Zotero library for the paper first
- Always output an Obsidian-compatible markdown file to a specific folder in my notes repository
- Extract figures that give an immediate intuition for the framework, architecture, or proposed methodology
- (TO DO) Make my blog and my work available to use as reference to tailor the responses to my work

This might be easier to understand with a concrete example, so here is the final skill I use for reviewing papers — along with a sample note it produces. If a summary sparks enough curiosity, I can always go back for the full read. :)

`````md 

---
name: research-paper-review
description: Review and analyze academic research papers. Use this skill when the user asks to review a paper, analyze a publication, summarize research, critique methodology, extract key findings, compare papers, check for numerical inconsistencies, or assess novelty and contributions of academic work. Also triggers when the user mentions reading a PDF of a paper, wants a literature review, asks about related work, or wants to improve a paper before submission. Also triggers when the user gives just a paper title and asks for a review.
---

# Research Paper Review

Assist researchers in reviewing, analyzing, and criticizing academic papers systematically and thoroughly.

## When to Use

- User asks to review, summarize, or critique a research paper
- User shares a PDF path or just a paper title
- User wants to assess methodology, contributions, or novelty
- User needs help writing a peer review
- User wants to compare multiple papers or do a literature survey
- User wants to improve a paper before submission (pre-submission review)
- User wants to check for numerical/statistical inconsistencies
- User wants venue-specific feedback (conference, journal, or preprint)

## Inputs

- Paper: a full file path to a PDF **or** just a paper title (the skill will find it automatically)
- Target Venue (optional but recommended): conference, journal, or preprint target
               - Example: "Models 2026", "TOSEM", "Sosym", "arXiv preprint"
- Type of paper (optional but recommended): "full research" paper, "short" paper, "new ideas" paper, "tool demo", "poster",...
- Explicit reviewing guidelines (optional): if available provide a description or URL with the reviewing criteria.

## Review Workflow

### Step 0: Locate the Paper

Before anything else, resolve where the paper lives.

- **If the user gave a full file path** (starts with `/` or `~`): use it directly. Skip to Step 1.
- **If the user gave only a title or partial name**: search for it recursively under `~/Zotero/storage/` using a case-insensitive filename match:
  ```bash
  find ~/Zotero/storage/ -iname "*<title>*" -type f
  ```
  - If **exactly one match** is found: use it silently and proceed.
  - If **multiple matches** are found: list them numbered and ask the user to pick one before continuing.
  - If **no match** is found: tell the user no PDF was found under `~/Zotero/storage/` matching that title, and ask them to provide the full path.

### Step 1: Pre-Processing / Venue Context

- If target venue and/or the type of paper are provided, include them as context for all subsequent steps:

> "Review this paper as if it is intended for [TARGET VENUE] as a [TYPE PAPER] submission. Consider typical standards, expectations, page limits, scope, and audience for this venue and type of paper"

- Optional: Use the provided reviewing guidelines or try to find them on the venue website (if available) with standards for methodology, novelty, empirical rigor, validation and formatting.

### Step 2: Read the Paper

Identify the format and read accordingly:

- **PDF**: Use the Read tool with the `pages` parameter for large documents (max 20 pages per request).
- **LaTeX source**: Read the main `.tex` file first. Look for `\input{}` or `\include{}` commands to find additional sections, figures, and bibliography files. Use Grep to search for key commands like `\begin{abstract}`, `\section`, `\cite` across all `.tex` files.
- **Multiple files**: Use Glob with `**/*.tex` to find all source files, then read them in logical order (main file → sections → appendix).

In all cases, skim the abstract, introduction, and conclusion first to get the big picture before diving into details.

### Step 3: Structured Summary

Show that you understand the paper by producing a summary covering:

1. **Problem Statement** — What problem does the paper address? Why does it matter?
2. **Contributions** — What are the claimed contributions? (list them)
3. **Approach/Methodology** — How do the authors solve the problem?
4. **Key Results** — What are the main findings/metrics?
5. **Limitations** — What are the acknowledged (and unacknowledged) limitations?

### Step 3b: Visual Highlights & Demo Links

Immediately after the Summary section in the output, include a **"Visual Overview"** section with two types of content:

**1. Demo / video links**
Scan the full text for any URLs pointing to video demonstrations, project pages, or supplementary material (e.g. YouTube, GitHub Pages, arXiv supplementary). Include every one you find as a markdown link with a short descriptive label.

**2. Key figures**
Identify figures that give the reader an immediate intuition for the *framework, architecture, or proposed methodology* — the kind of diagram that, if shown to a newcomer, would let them grasp what the paper is about in seconds. Good candidates:
- System/pipeline overview diagrams
- Architecture figures (e.g. neural network layout, agent loop)
- Methodology flow charts
- Qualitative results showing the system *in action* (e.g. robot performing a task, step-by-step execution)

Exclude figures whose primary purpose is to report a metric rather than explain a concept: training curves, bar charts, ablation tables, confusion matrices, result plots.

For each selected figure, extract it using this strategy — try in order, stop at the first that succeeds:

**Method 1 — `pdfimages -all` (preferred, extracts only the image without surrounding text)**
```bash
pdfimages -all -f <page> -l <page> "/path/to/paper.pdf" /tmp/fig_<N>
# produces: /tmp/fig_<N>-000.png, /tmp/fig_<N>-001.jpg, etc.
```
- If **one image** is produced: use it directly.
- If **multiple images** are produced (sub-panels, logos, decorations): pick the largest file by size — it is almost certainly the main figure. Discard obviously small ones.
- If **no images** are produced (figure is vector/text-based with no raster content): fall through to Method 2.

**Method 2 — `pdftoppm` (full-page render, always works but includes surrounding text)**
```bash
pdftoppm -f <page> -l <page> -r 200 -png "/path/to/paper.pdf" /tmp/fig_<N>
```

**Saving and embedding**
Save the result to `~/notes/Media/<paper title> - Fig <N>.png` — convert to PNG using `magick` (ImageMagick v7):
```bash
magick /tmp/fig_<N>-000.jpg "~/notes/Media/<paper title> - Fig <N>.png"
```
Then embed in the markdown with a fixed display width of 1000px (Obsidian syntax):
```
![[<paper title> - Fig <N>.png|1000]]
```

If neither method is feasible, fall back to a plain-text reference: *"Fig. N — [caption] (p. X)"*.


### Step 4: Numerical & Consistency Checks

This is where LLM-assisted review adds the most value, catching things humans easily miss during manual review. Run these checks systematically:

- **Numbers across text, tables, and figures**: Do values reported in the text match what's in the tables? Do figures reflect the data described?
- **Statistical consistency**: Do p-values, confidence intervals, and effect sizes align? Are sample sizes consistent throughout?
- **Calculations**: Verify percentages, averages, sums. Check that reported improvements (e.g., "30% improvement") match the actual numbers.
- **Internal references**: Do all \ref, \cite, figure/table references resolve? Are there dangling references or wrong numbering?
- **Acronyms**: Are all acronyms defined on first use?
- **Terminology consistency**: Is the same concept always referred to with the same term?
- **Citations**: Do all citations exist? Is citation style uniform (i.e. all conference papers are cited using the same fields, same for other venues)

Even minor errors (typos, broken references, wrong numbering) matter — reviewers often use these as signals that the paper was not carefully prepared.

### Step 5: Critical Analysis

Evaluate the paper on these dimensions:

| Dimension | Questions to Answer |
|-----------|-------------------|
| **Novelty** | Is this genuinely new? How does it differ from prior work? |
| **Soundness** | Is the methodology rigorous? Are experiments well-designed? |
| **Significance** | Does this advance the field meaningfully? |
| **Clarity** | Is the paper well-written and well-structured? |
| **Reproducibility** | Could someone replicate this work from the paper alone? |
| **Related Work** | Is the positioning against prior work fair and complete? |
| **Venue Alignment** | Does the paper meet expectations of the target venue (scope, depth, format, length, contribution type)? |


### Step 6: Provide Actionable Feedback

Structure feedback as:

- **Strengths** — What the paper does well (be specific, cite sections)
- **Weaknesses** — What could be improved (be constructive, suggest fixes)
- **Questions for Authors** — Things that need clarification
- **Minor Issues** — Typos, formatting, citation issues, broken references
- **Venue-Specific Recommendations** — Highlight alignment issues, potential improvements to meet venue expectations

### Step 7: Top Actions — Start Here

Write down a list of the top 10 most immediate actions that the author should address.

These should be the ones that will bring the best "bang for the buck", i.e. actions that generate the most benefit relative to the cost of implementing them.


## Output Format

The review must be saved as an **Obsidian-compatible markdown file**. This means:

- Use standard markdown syntax throughout (headings, bold, italics, bullet lists, tables, horizontal rules)
- Begin the file with a **YAML frontmatter block** containing key metadata that Obsidian can index and display
- Use `#` headings rather than HTML or other markup
- Keep tables in standard markdown pipe format
- Avoid raw HTML tags — they won't render cleanly in Obsidian's reading view

Use this exact template (fill in all fields):

```markdown
---
title: "<full paper title>"
authors: "<Author1, Author2, ...>"
year: <publication year>
venue: "<journal or conference name>"
doi: "<DOI if available, else omit>"
tags:
  - paper-review
  - <field tag, e.g. reinforcement-learning>
  - <another relevant tag>
date_reviewed: <today's date, YYYY-MM-DD>
overall: "<accept | major-revision | minor-revision | reject>"
---

# Paper Review: [Title]

## Summary
[2-3 paragraph summary]

## Visual Overview

### Demo Videos
- [Demo title](URL)

### Key Figures
![[<paper title> - Fig N.png|1000]]
*Fig. N — [caption describing what it shows]*

## Strengths
- S1: ...
- S2: ...

## Weaknesses
- W1: ...
- W2: ...

## Questions for Authors
- Q1: ...

## Minor Issues
- ...

## Venue-Specific Recommendations
- V1: ...
- V2: ...

## Overall Assessment
[1 paragraph verdict: accept/revise/reject with justification]

## Top Actions — Start Here
- T1: ...
- T2: ...

## Confidence
[Your confidence level in this review: low/medium/high, and why]
```

## Step 8: Save the Review

After the review is written, save it to disk.

**Determine the output filename**: use only the paper title (no path prefix, no author names). Spaces in the filename are fine. For example, for a paper titled *"Attention Is All You Need"*, the output path is:
```
~/notes/Literature/Papers Briefly Analyzed/Attention Is All You Need.md
```

**Check for an existing file first**:
```bash
ls ~/notes/Literature/Papers\ Briefly\ Analyzed/<paper title>.md
```
- If the file **does not exist**: write it directly.
- If the file **already exists**: ask the user:
  > "A review for **[paper title]** already exists. Would you like to **(1) Overwrite** the existing file or **(2) Append** the new review to it?"
  - **(1) Overwrite**: replace the file content entirely with the new review.
  - **(2) Append**: add a horizontal rule (`---`) followed by the new review at the end of the existing file. Note: appending will result in two YAML frontmatter blocks — warn the user that Obsidian only reads the first one, and suggest they merge them manually if needed.

Confirm to the user once saved, including the full path of the file written.
`````


#### Sample Note 


### Paper Review: Reinforcement Learning for Precision Grasping and Safety-Critical Coordination in a Robotic Arm

## Summary

This paper presents a safety-focused deep reinforcement learning (DRL) approach for object manipulation in human–robot collaboration (HRC) scenarios. The authors integrate the Franka Emika Panda robotic arm into the OpenAI Safety Gym framework (via PyBullet) and evaluate two tasks of escalating complexity: Single-Object Phased Manipulation (SOPM) and Multi-Object Atomic Manipulation (MOAM). A Hierarchical RL (HRL) structure decomposes grasping into sequenced sub-policies (approach → place → retract → wait), and the system is modelled as a Constrained Markov Decision Process (CMDP).

The central empirical contribution is a comparison between standard PPO and its Lagrangian-constrained variant (cPPO) for safety compliance during manipulation. The authors also introduce "Grasp Mechanics," a tactile-feedback framework that bypasses visual input by using proprioceptive sensing, force detection, and slip monitoring. On training objects the system reaches 99% success rates; zero-shot transfer to unseen objects yields 84–95%, dropping to 60% for a geometrically irregular cleanser bottle. cPPO consistently achieves lower safety costs than PPO at the cost of slightly lower task reward and longer training time.

A supplementary evaluation integrates the Safety Gym framework with the Handoversim benchmark for human-to-robot object handovers, but end-to-end autonomous grasping in Handoversim fails and falls back to hand-coded policies — a significant negative result that is acknowledged but underplayed.

---

## Visual Overview

### Demo Videos
- [Simulation-Based Experiments Demo](https://youtu.be/8K3nxhRS0Mk)

### Key Figures

![[Reinforcement learning for precision grasping and safety-critical coordination in a robotic arm - Fig 1.png|1000]]
*Fig. 1 — Full system overview: the Safety Gym Panda robot environment (left) and the agent control loop with Grasp Mechanics sub-components — Orientation Optimisation, Adaptive Gripping, Position Targeting, Grip Validation, Geometry Extraction — feeding into the PPO/cPPO policy (right).*

![[Reinforcement learning for precision grasping and safety-critical coordination in a robotic arm - Fig 2.png|1000]]
*Fig. 2 — Neural network architecture: 8-dimensional proprioceptive input → two 64-neuron hidden layers → actor network outputting position, orientation, and grasp-force control; critic networks estimating V(s) and Q(s,a). Tactile feedback from Grasp Mechanics feeds in alongside the feature representation.*

![[Reinforcement learning for precision grasping and safety-critical coordination in a robotic arm - Fig 3.png|1000]]
*Fig. 3 — Zero-shot policy transfer in action: the trained policy successfully manipulates four unseen objects (teacup, soap bar, remote control, cleanser) while manoeuvring around an obstacle.*

---

## Strengths

- **S1 — Practical safety framing:** Integrating cPPO within Safety Gym for a robotic arm is a meaningful engineering contribution. The CMDP cost-function formulation is sound and well-motivated.
- **S2 — Zero-shot transfer evaluation:** Testing on 4 unseen objects with 40 trials each is a credible generalisation benchmark; the honest reporting of the 60% failure on the irregular cleanser bottle is appreciated.
- **S3 — Hierarchical task decomposition:** The phase-structured HRL (approach/place/retract/wait) is well-motivated and produces interpretable, sample-efficient sub-policies.
- **S4 — Reduced visual dependency:** Learning from low-dimensional proprioceptive state (8-dim) + tactile feedback is practically valuable for sim-to-real transfer, sidestepping the visual domain-adaptation bottleneck.
- **S5 — Transparent limitation acknowledgment:** Fixed-orientation constraints, sim-to-real gap, and the Handoversim end-to-end failure are all clearly identified by the authors.

## Weaknesses

- **W1 — Numerical inconsistencies between Table 1 and Figure 4:** Fig. 4 reports 97% for "Soap" and 93% for "Remote," while Table 1 reports 95% for "Soap Bar" and 85% for "Remote Control" — discrepancies of 2 pp and 8 pp with no explanation.
- **W2 — No ablation study on the reward function:** The reward has 6 components (approach, contact, slippage, lifting, placing, disposal, collision penalty). No ablation is provided to assess which components drive performance or safety compliance.
- **W3 — Weak baseline comparison:** Only compared against hand-coded policies [14, 34] and Panda-Gym [18]. No comparison with other safe RL algorithms (CPO, TRPO-Lagrangian, SAC-Lagrangian).
- **W4 — Simulation-only evaluation:** All results are simulation-based. The acknowledged sim-to-real gap is deferred entirely to future work despite the real-world HRC framing.
- **W5 — Handoversim failure underplayed:** The Handoversim evaluation falls back to hand-coded policies and is disclosed only in Section 4.6.4, without being flagged in the abstract or contributions list.
- **W6 — Misleading "Final Avg Reward %" label in Table 3:** Values of 9.5 and 7.5 labeled as percentages are actually raw episode reward scores (y-axis 0–10 in Figs 6–7).
- **W7 — No statistical rigor:** No confidence intervals, standard deviations, or significance tests anywhere. The abstract calls cPPO "comparable" to PPO despite a 7.4% reward gap (9.5 vs 8.8 in SOPM) — unsubstantiated without significance testing.

## Questions for Authors

- **Q1:** What explains the discrepancy between Table 1 and Figure 4 for Soap Bar (95% vs 97%) and Remote Control (85% vs 93%)?
- **Q2:** In Table 3, what does "Final Avg Reward %" mean exactly? What is the maximum achievable reward?
- **Q3:** Was a single policy evaluated on both SOPM and MOAM, or are these separate policies? How were the 40 zero-shot trials structured across tasks?
- **Q4:** What is the quantitative benefit of Grasp Mechanics in isolation — what is the success rate of cPPO *without* it?
- **Q5:** The MOAM task achieves ~7.5 reward units — how does this map to the percentage of complete 3-object episodes successfully finished?

## Minor Issues

- The collision penalty reward ($r_{\text{collision}} = 1$ if collision) has the **wrong sign** — a penalty should be negative. Appears to be a notation error in Section 4.1.
- **HRL** and **HRC** are used before their first formal definition in the Introduction.
- The YouTube video link (p. 3) is embedded inline in the body text — should be a footnote or supplementary reference.
- "Data Availability: There are no shareable data associated with this paper" is misleading — training data is simulation-generated and reproducible from the released code.
- Section 5 conflates experimental results (5.1) with discussion and conclusion (5.2); a standalone Conclusion section would improve readability.

## Venue-Specific Recommendations

- **V1 — Real hardware pilot:** *Intelligent Service Robotics* expects results relevant to actual service robot deployments. Even a small real-hardware experiment on the physical Panda would substantially strengthen the venue fit.
- **V2 — Tighten contributions vs evidence mapping:** The Handoversim contribution (bullet 5, Section 1.2) is not supported by positive results and should be reframed as a negative finding or removed from the contributions list.

## Overall Assessment

**Major Revision.** The paper makes a legitimate engineering contribution — integrating a constrained RL framework with a robotic arm in Safety Gym and demonstrating meaningful zero-shot grasping generalisation with a tactile-feedback approach. However, it is undermined by numerical inconsistencies between a figure and a table, a misleadingly labelled metric in Table 3, an undisclosed end-to-end failure in the Handoversim benchmark, absence of ablation studies, and a complete lack of statistical reporting. These are all correctable, but they must be addressed before the paper fully delivers on its stated claims.

## Top Actions — Start Here

| # | Action | Impact | Effort |
|---|--------|--------|--------|
| **T1** | Reconcile Table 1 vs Fig. 4 (Soap Bar 95% vs 97%; Remote 85% vs 93%) — fix or explain | High | Low |
| **T2** | Fix "Final Avg Reward %" label in Table 3 — clarify scale, remove misleading "%" | High | Low |
| **T3** | Fix collision reward sign in Section 4.1 — should be negative to penalise collisions | High | Low |
| **T4** | Add variance/confidence intervals to Table 1, Table 3, and Figs 6–7 | High | Medium |
| **T5** | Reflect Handoversim end-to-end failure in the abstract and contributions list | High | Low |
| **T6** | Add ablation: at minimum compare with/without Grasp Mechanics and with/without slippage penalty | High | Medium |
| **T7** | Add at least one additional safe RL baseline (CPO or TRPO-Lagrangian) | Medium | Medium |
| **T8** | Clarify the zero-shot transfer protocol and how the 40 trials were structured | Medium | Low |
| **T9** | Separate the Conclusion from Section 5 into a standalone section | Low | Low |
| **T10** | Define HRL and HRC on first use; move the YouTube link to a footnote | Low | Low |

## Confidence

**Medium-High.** Full paper read (19 pages). Numerical inconsistencies verified directly against figures and tables. Confidence slightly reduced on Grasp Mechanics evaluation claims, as its contribution is described qualitatively without an isolated baseline comparison.

--- 

As a final note, as you may have guessed, I also used a [skill](https://www.skills.sh/haowjy/creative-writing-skills/prose-writing) to improve my writing :)
