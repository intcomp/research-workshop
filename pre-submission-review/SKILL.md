---
name: pre-submission-review
description: An agentic self-review of an academic paper before submission. It checks format and required content (page limit, mandatory statements such as AI-use disclosure) against the venue's current call for papers, checks double-anonymous review for anything that leaks author identity (names, emails, institutes, GitHub links, first-person self-citations), searches online for similar existing papers to assess novelty, checks that the introduction states a gap in recent methods and that the contributions are precise, checks that experiments support every claim and suggests missing ones, checks cross-references of symbols, figures, tables, equations, and sections, checks that the method section gives every detail of our method and none of standard techniques, and checks that every figure, table, and algorithm sits on or next to the page that first mentions it, and replaces arXiv references with published versions. Use when a researcher asks to review, self-check, or audit a paper before submission.
---

# Pre-Submission Review

Review the paper the way a critical reviewer at the target venue would, before the real reviewers do. Read the whole paper first (the LaTeX source and the compiled PDF), then go through every check below.

**Ground rules:**

- **Report, do not edit.** Do not change the paper unless the user asks. The output is a review report (see Report).
- **Be specific.** Every finding names its location (section, figure, table, equation, or `file:line`), what is wrong, and a concrete fix.
- **Verify online, never from memory.** Venue rules and related papers must come from sources found during the review, each with a link.

## 0. Format and Required Content

Find the target venue's **current** call for papers and author guidelines online. Rules change every year, so never rely on a previous year's.

- **Page limit.** Compile the paper and check the main text is within the limit. Check what the limit excludes (references, appendix, statements) and whether the appendix may be in the same PDF.
- **Template untouched.** The official style file is used, with no changed margins, fonts, or spacing, and no `\vspace` hacks that squeeze the layout.
- **Required content.** Every section or statement the venue requires is present, for example:
  - a statement on the use of AI tools or LLMs, which many venues now require;
  - limitations, broader impact, ethics, or reproducibility statements;
  - a paper checklist (e.g. NeurIPS).
- **Anonymity:** see Double-Anonymous Review.
- **Compiles cleanly.** No `??` references, undefined citations, overfull boxes that stick into the margin, or missing figures.

## 1. Double-Anonymous Review

For a double-anonymous (double-blind) venue, nothing in the submission may reveal who the authors are. Follow the venue's anonymity policy, and check the PDF, the LaTeX source, and the supplementary material and code. Every leak is **critical**: it can get the paper desk-rejected.

- **Author information.** No author names, emails, institutes or affiliations, acknowledgments, or funding and grant numbers. The author block shows only the template's anonymous placeholder (e.g. "Anonymous CVPR submission" with the paper ID).
- **Links.** No GitHub, project page, personal or lab website, Google Drive, Hugging Face, or video links that point to the authors, including usernames inside URLs. If the venue allows it, replace code links with an anonymized repository (e.g. `anonymous.4open.science`).
- **Self-citations.** Cite our own prior work in the third person, as if someone else wrote it: "Smith et al. [1] showed ...", not "our previous paper [1]", "our earlier work", or "as we showed in [1]". Do not drop the citation to hide it.
- **Identifying content.** No names of internal datasets, labs, clusters, or company products that point to the authors ("trained on our company's cluster"), and no names, logos, or watermarks in images and screenshots.
- **Hidden places.**
  - PDF metadata: the author field set by `hyperref` (`pdfauthor`) or embedded figure PDFs (`pdfinfo main.pdf`, and on each figure);
  - file paths with usernames (e.g. `/home/<user>/`) in figures, logs, or code;
  - supplementary code: author headers, license names, absolute paths, and git history.

**How to check.** Read the PDF and search it and the source for leaks, then confirm each hit by reading it in context:

```bash
pdfinfo main.pdf
pdftotext main.pdf - | grep -n -i -E 'github|https?://|@|universit|institut|acknowledg|our (previous|prior|earlier) (work|paper)'
grep -n -i -E '\\thanks|\\author|acknowledg|github|pdfauthor' *.tex
```

## 2. Novelty

Search online for existing work with a similar method or on the same problem. Use Google Scholar, arXiv, Semantic Scholar, OpenReview, and recent proceedings of the main venues in the field.

- Search with several phrasings: the problem, the key idea, and the key technique, in the field's own terms.
- Focus on recent work (the last 2–3 years), including arXiv preprints.
- For each similar paper found, report its title, venue and year, and link; what it does; how similar it is to our method; and whether the paper cites and discusses it.
- Flag any paper whose core idea substantially overlaps ours as a **novelty risk**, and suggest how to differentiate from it (in the text, and with experiments if needed).
- Never invent a paper. If nothing similar is found, say so and list what was searched.

## 3. Introduction and Contributions

- **Gap in recent methods.** The introduction names concrete, recent existing methods and the specific gap or limitation they share, not only old or straw-man baselines.
- **How we fill the gap.** It states clearly how the proposed method addresses that gap, and why it works.
- **Contributions.** The contribution list at the end is precise and accurate:
  - each contribution is specific and verifiable, not vague ("we propose a novel framework");
  - each is actually supported by the method or experiments;
  - nothing is overclaimed (e.g. "first" or "state of the art" without evidence).

## 4. Experiments

Check that the experiments are sufficient and that every claim in the paper is supported by evidence.

- **Claim → evidence.** List each claim from the abstract, introduction, and contributions, and point to the experiment that supports it. Flag every claim with weak or no support.
- **End-to-end comparison.** Strong, recent baselines; standard benchmarks and metrics; fair settings (same backbone, data, and compute budget).
- **Analysis of our method itself**, beyond end-to-end performance. There must be experiments showing that the key designs, the key algorithm, and each key component are well-founded, for example:
  - ablations that remove or replace each component;
  - comparisons with alternative designs for the same component;
  - sensitivity to key hyperparameters;
  - analysis or visualizations that show the mechanism works as the method section claims;
  - cost: parameters, FLOPs, memory, runtime.
- **Suggest experiments.** Propose concrete additional experiments that would make the paper more convincing, with what each would show and which claim it supports, ordered by priority.

## 5. Cross-References and Notation

Every element that can be cross-referenced or annotated should be. To the reader, each reference is a reminder and a quick review that reinforces what they have read.

- **Symbols in figures and tables.** If a concept shown in a figure or table has a math symbol defined in the text, the figure, table, or its caption shows that symbol too. E.g. a block for the gating module in the method figure is labeled with its symbol $g_\phi$, and an ablation table on the loss weight writes $\lambda$.
- **Symbols in the text.** When the text discusses a concept that has a symbol, it gives the symbol: "the loss weight $\lambda$", not just "the loss weight".
- **Sections, equations, figures, and tables.** Wherever the text refers back to something, it cites it with cleveref's `\cref`: "the loss in Eq. (3)", "the module in Sec. 3.2", "as shown in Fig. 2". Flag references written as plain text ("the loss above", "the previous section") that could be cross-referenced.
- Every figure and table is referenced in the text.

## 6. Method Clarity

- **Our method in full.** Every process and detail specific to our method is explained: inputs and outputs, each component, how they connect, the losses, and the training and inference procedures. A reader should be able to reimplement it. Flag missing details.
- **Standard techniques omitted.** Details unrelated to our method's contribution are left out or replaced by a citation, e.g. how to compute self-attention, or a standard backbone or optimizer. Flag such passages as candidates to cut.

## 7. Figure, Table, and Algorithm Placement

Readers should never have to flip pages to find a float. Check every figure, table, and algorithm in the compiled PDF.

- **Near its first mention.** Each float appears on the page where the text first refers to it, or the next page. The previous page is acceptable only when the two pages are adjacent and the reader can see it easily (e.g. the float sits on the facing page). A float never appears two or more pages away from its first mention.
- **Mentioned before it appears.** Ideally the text refers to a float before or on the page where the float appears, not long after.
- **Order follows the text.** Floats are numbered in the order they are first referenced: Fig. 1 is mentioned before Fig. 2, Table 1 before Table 2.
- **Top of the page.** Prefer `[t]` placement. Avoid `[h]` and `[H]`, which leave gaps or push floats out of order, and avoid float-only pages unless the float is large.
- **Full-width floats.** In standard two-column LaTeX, a `figure*` or `table*` can appear at the earliest at the top of the page after its source. So its source must come about one page before its first mention, not at the mention.
- **Teaser.** The teaser is at the top of the first page.
- **Captions.** Figure captions go below the figure and table captions above the table, unless the venue's template says otherwise.

**How to check.** For each float, compare two pages:

- the page where the float appears, from the second field of its label in `main.aux` (e.g. `\newlabel{fig:overview}{{2}{3}...}` means Fig. 2 is on page 3);
- the page of its first mention, found by searching the PDF text page by page (e.g. `pdftotext -f 3 -l 3 main.pdf -`) for "Fig. 2", "Figure 2", "Table 2", or "Algorithm 2".

**Fixing.** Move the float's source in `main.tex` next to the paragraph that first mentions it (one page earlier for `figure*`), or change its placement option. Do not fix placement with `\vspace` hacks or by shrinking the float.

## 8. References: Published Versions over arXiv

Avoid citing arXiv preprints. Every reference should point to a published, peer-reviewed version.

1. **Find every preprint entry** in the `.bib` files: `journal = {arXiv preprint arXiv:...}`, `eprint` with `archivePrefix = {arXiv}`, `CoRR`, and other preprint servers (bioRxiv, SSRN, ...).
2. **Look for its published version** online (DBLP, Semantic Scholar, Google Scholar, OpenReview, the publisher). The published version may have a different title or a different author order, so search by authors and topic too, not only by the exact title.
3. **If it is published,** cite the published version, with its venue, year, and pages taken from an official BibTeX source.
4. **If it is not published,** look for a similar published paper that supports the same point, and cite that instead.
5. **Keep the arXiv citation only as a last resort:** when there is no published version or alternative, and the paper cannot go uncited (e.g. a very recent method we compare against or build on). Report each one kept, with the reason.

Report every preprint entry in a table: its key, the published version or alternative found (with link), and the action (replace, substitute, or keep with reason). When fixing, follow the reference rules of the `latex-paper-writing` skill.

## Report

Write the review as a Markdown report with one section per check (0–8). Within each section:

- list findings by severity: **critical** (could cause rejection, e.g. over the page limit, missing required statement, an identity leak, novelty risk, unsupported main claim), **major**, then **minor**;
- give each finding its location, the problem, and a concrete fix;
- include links for every venue rule and related paper.

End with a short summary of the most important fixes before submission.
