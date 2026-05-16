# STEM Journal-Club Deck Builder

A skill (for Claude and OpenAI) / prompt (for Gemini Deep research) that co-produces academic paper presentation decks through a structured iteration loop.

Built for STEM journal clubs: methodology-heavy, reference-based, design-disciplined. Operates as a senior researcher with 15+ years of design and methodological judgment.

Three output formats: **PowerPoint**, **Keynote**, **LaTeX Beamer**.

---

## What it does

Takes a published research paper (PDF or TeX) and co-produces a journal-club presentation through a **5-checkpoint iteration loop** — not a one-shot generation.

```
   ┌─ PLAN ──── DRAFT v1 ──── REINFORCEMENT ──── REVISION ──── AUDIENCE-TAILORING
   │            ↓             ↓                  ↓             ↓
   │   propose   produce deck   user points to    rebuild      adjust for
   │   scope &   + speaker      weak slides,      addressing   audience
   │   theme &   script DOCX    missing context,  every        background,
   │   format    (both, always) citation gaps     point        depth, pacing
   │                                              ↺            ↓
   └──────────────────────────────────────────────┘     final delivery
```

## What you get

Two files, every time:

- **`deck.pptx` / `deck.key` / `deck.tex`** — IMRD-structured, method-heavy (60%), native equations, native diagrams, dashed-border placeholders for paper figures
- **`script.docx`** — slide-by-slide speaker narration in your language, with key messages and sparing paper quotes

## Output formats

| Format | When to pick |
|---|---|
| **PowerPoint (.pptx)** | Default — most journal clubs, cross-platform audiences. Native OMML equations. |
| **Apple Keynote (.key)** | Mac-only presenter, visual polish priority. Imports from PPTX; equations may need touchup. |
| **LaTeX Beamer (.tex)** | Math-heavy papers, paper-aligned aesthetic. Native LaTeX math, TikZ diagrams, version-controllable. |

Format is chosen at Checkpoint 1. Build pipeline differs per format — see `references/output_formats.md`.

## Key features

- **IMRD 1:6:2:1 ratio** — Method gets 60% of slides (defining feature of STEM journal clubs)
- **Reference-based** — every method comparison cites a specific source; no baseless "extends/improves" claims
- **Variable sensitivity** — for each parameter, what happens when ↑ or ↓ (compact inline + optional appendix slide)
- **Native math** — OMML for PPT/Keynote, native LaTeX for Beamer
- **Native diagrams** — `OVAL`/`LINE` shapes (PPT) or TikZ (Beamer), not images
- **Figure placeholders** — copyright-safe; you paste your paper's actual figures
- **Reference mimicry (optional)** — upload 2–3 existing decks at Checkpoint 1; the skill extracts palette, layout patterns, and IMRD ratio to mimic
- **Senior-researcher aesthetic** — white background, two-color palette, accent color used sparingly
- **Domain adaptation** — vocabulary and conventions adjusted for stat-phys / ML / biology / econ / HCI / math

---

## Quick start

### Option A — Claude (via `.skill` package)

1. Download `stem-journal-club-deck.skill` from this repo or its releases page
2. In Claude.ai → **Settings → Capabilities → Skills → Upload skill**
3. Start a new chat: *"이 논문으로 journal club 슬라이드 만들어줘"* and upload your paper PDF

### Option B — Claude Code (developer setup)

```bash
git clone https://github.com/<your-user>/stem-journal-club-deck.git
cp -r stem-journal-club-deck ~/.claude/skills/
```

Restart Claude Code; the skill triggers automatically on relevant prompts.

### Option C — Gemini (via flat prompt file)

1. Open `stem-journal-club-deck-flat.md`
2. Copy the entire file
3. In Gemini, create a new Gem (or paste as system instruction at the start of a session)
4. Upload your paper as the next message

The flat file contains everything: instruction, references, scripts, templates.

### Option D — Local model (Ollama / LM Studio / etc.)

Use the flat prompt file with any model that handles long context (~64k tokens recommended). Clone the repo for the OMML injection and math compilation scripts.

---

## Requirements

For the full pipeline (Claude / local with execution):

- `pandoc` — LaTeX → OMML conversion (PPTX/Keynote target)
- `node` + `npm` (`pptxgenjs`, `docx` packages)
- `python3` with `python-pptx`, `lxml`, `Pillow`, `matplotlib`
- `pdftotext` (Poppler) — paper section extraction
- **For Beamer target**: TeX distribution (TeXLive 2023+, MiKTeX) for `pdflatex`
- Optional: `LibreOffice` for PDF preview generation

For Gemini-only use: no local setup needed.

---

## Repository structure

```
stem-journal-club-deck/
├── README.md                          ← you are here
├── LICENSE                            ← GPL v3 (see note below)
├── stem-journal-club-deck-flat.md     ← single-file Gemini prompt
├── stem-journal-club-deck.skill       ← packaged Claude skill (zip)
├── SKILL.md                           ← Claude skill entry point
├── references/
│   ├── iteration_loop.md
│   ├── output_formats.md
│   ├── reference_mimicry.md
│   ├── domain_adaptation.md
│   ├── method_emphasis.md
│   ├── omml_pipeline.md
│   ├── native_shapes.md
│   ├── figure_placeholders.md
│   └── speaker_script.md
├── scripts/
│   ├── inject_omml.py                 ← LaTeX → OMML (PPTX)
│   └── compile_math.py                ← LaTeX → PNG (Keynote fallback)
└── assets/
    ├── template_pptx.js               ← pptxgenjs starter (PPTX/Keynote)
    └── template_beamer.tex            ← Beamer .tex starter
```

---

## The 5-checkpoint loop

This skill explicitly resists one-shot generation. Every session goes through:

### 1. PLAN

Skill proposes: paper domain, **output format**, slide count, IMRD allocation, theme palette (primary + accent), 2–3 emphasis topics. Optionally extracts style from user-supplied reference decks. User approves or adjusts. **No drafting before approval.**

### 2. DRAFT v1

Produces complete `deck.{pptx|key|tex}` + `script_v1.docx`, plus a **flagged review list** identifying attributions, extrapolations, emphasis choices, domain assumptions.

### 3. REINFORCEMENT REVIEW

User points to weak slides, missing emphasis, citation issues, methodology gaps. Skill **asks clarifying questions** before revising — does not auto-revise.

### 4. REVISION v2

Rebuilds addressing every reinforcement point. Updates **both deck and script**. Re-flags remaining ambiguities. Loops to step 3 if needed.

### 5. AUDIENCE-TAILORING

Once content is solid, polish for the specific audience: terminology, depth, examples, pacing. Final delivery.

See `references/iteration_loop.md` for concrete examples.

---


## 🎓 Pedagogy note — who should NOT use this

**If you are still learning to read research papers critically, do not use this skill.**

Specifically:

- **Undergraduate students** preparing their first journal-club presentations
- **First- and second-year graduate students** building paper-reading habits
- **Anyone** for whom the act of distilling a paper into slides is itself the
  learning exercise

Building a journal-club deck manually is not a chore that this tool removes —
**it is the pedagogy.** The work of:

- Re-reading sections until the methodology fits in your head
- Choosing which equation is central and which is filler
- Drawing the algorithm schematic on paper before it makes sense
- Deciding what to cut when you only have 12 slides

— that work *is* how you learn to read papers, build taste, and develop the
methodological judgment that defines a researcher. Automating it produces a
slick deck and a researcher who can't actually read a paper.

**Recommended use cases**:

- Senior graduate students preparing their N-th journal-club presentation
- Postdocs / PIs preparing a quick rehash of familiar territory
- Industry researchers running internal paper reviews
- Anyone with established paper-reading skills who needs to scale their output

If you're not sure which category you're in, default to building manually.
You can always use this tool later; you can't un-learn the reading skill.

This tool was inspired by workflow automation patterns (RPA, agent-based
workflows). Like all automation, it amplifies whoever uses it — for senior
researchers, that means more decks faster. For learners, that means a stunted
research instinct masked by good-looking output.

---
## Optional — reference presentation mimicry

If you want the deck to match a style you admire (advisor's template, conference talk, journal aesthetic), upload **2–3 reference decks** at Checkpoint 1. The skill extracts:

- **IMRD ratio actually used** (counts slides per section)
- **Method resolution depth** (equations, step-boxes, appendix sensitivity slides)
- **Color palette** (Pillow color quantization on rendered slides)
- **Layout patterns** (column structure, callout positions, accent frequency)

Skill proposes a plan that mimics the references; user confirms or adjusts. See `references/reference_mimicry.md`.

This is **optional**. Most users don't supply references and that's fine — default theme and IMRD work.

---

## Method depth — the 4 dimensions

Every method slide hits at least one (major method slides hit all four):

1. **Step-by-step micro-mechanics** — 3–5 numbered steps explaining one algorithm iteration
2. **Variable sensitivity** — for every parameter, what happens when ↑ or ↓ (required, not optional)
3. **Prior method comparison** — reference-based; every "extends/improves/differs from" claim cites
4. **Quantitative parameter table** — symbol, value, unit, source citation

See `references/method_emphasis.md`.

---

## ⚠️ Security & ethics — published papers only

**This tool is for already-published, publicly-available papers.**

Both Claude and Gemini are cloud services. Paper content uploaded or pasted during a session is processed remotely. This creates significant risk if applied to:

- **Unpublished manuscripts** — drafts, in-progress work
- **Papers under peer review** — reviewer confidentiality applies
- **Confidential or proprietary research** — NDA, classified, embargoed
- **Preprints under embargo** — pre-publication restrictions
- **Pre-publication results** — anything not yet publicly released

The skill is instructed to detect signals of unpublished content (`[DRAFT]` markers, "do not cite/distribute" notices, track-changes, reviewer comments, etc.) and pause to warn before proceeding.

If you need to work on unpublished material:

- Run a **locally-hosted model** (Ollama, LM Studio, llama.cpp) with the flat prompt file
- Or wait until the paper is published

By using this skill on a paper, you assert that:

- The paper is published and publicly available
- You have the right to share its content with a cloud AI service
- You are not bound by confidentiality (e.g., as a peer reviewer) for this material

**The maintainers are not responsible for misuse on confidential material.** When in doubt, don't upload.

---

## Tips for great results

1. **Read the paper first** — this skill assumes you have. It's a co-production tool, not a summarizer.
2. **Pick a domain-appropriate palette** — stat-phys decks like navy+amber; ML decks suit slate+coral; bio likes forest+orange.
3. **Identify 2–3 emphasis topics at checkpoint 1** — without them, every slide looks equally important (which is wrong).
4. **Don't skip checkpoint 3** — the reinforcement review is where the skill's quality compounds.
5. **Pick the right format**:
   - PPTX = default
   - Keynote = visual papers, light math
   - Beamer = math-heavy, version-controlled
6. **Open the final PPTX in PowerPoint** (not LibreOffice) — OMML renders correctly only in PowerPoint/Keynote.
7. **Paste your paper's figures** onto the placeholder boxes before presenting.
8. **Speaker script is for rehearsal**, not literal reading — paraphrase live.

---

## Acknowledgments

Developed iteratively while preparing journal-club presentations for statistical physics papers. The Pan et al. (2018) *The Memory of Science* presentation served as the reference implementation.

Built with:

- [pptxgenjs](https://github.com/gitbrent/PptxGenJS)
- [docx-js](https://github.com/dolanmiu/docx)
- [pandoc](https://pandoc.org/)
- [Beamer](https://ctan.org/pkg/beamer)
- [TikZ](https://tikz.dev/)

---

## Contributing

PRs welcome. Especially useful contributions:

- Domain-specific reference patterns (current coverage: stat-phys, ML, biology, econ, HCI, math)
- Theme palettes for specific fields/journals
- Additional native-shape diagram patterns (phase diagrams, flowcharts, etc.)
- TikZ diagram templates for Beamer
- Translations of the speaker script template
- More reference deck examples for the mimicry feature

---

## License

**GNU General Public License v3** — see `LICENSE`.

This skill, including all instructions, scripts, and templates, is released under GPL v3. Outputs you generate from your own papers belong to you.

> * There aren't any significant restrictions; it's just that Gahyoun Gim graduated from GNU (Gyeongsang National University) and is distributing it under the GNU license :D* 🐂

If GPL doesn't fit your use case (e.g., embedding in proprietary tooling), feel free to open an issue to discuss a dual-license arrangement.
