---
name: nature-paper2ppt
description: Build a complete, highly-polished Nature-style Chinese PPTX presentation from a scientific paper, preprint, PDF, article text, abstract, or reading notes. Use this skill whenever the user asks to make slides/PPT/PPTX for journal clubs, group meetings, paper sharing, thesis seminars, lab meetings, or academic presentations across biomedical, chemical, physical, and engineering sciences. It identifies the research paper type, reconstructs the scientific argument spine, extracts high-res figure crops (handling vector PDFs via rasterization), writes concise Chinese slide copy and speaker notes, and compiles the real .pptx deck using safe coordinate grids and cross-platform CJK typography.
---

# Purpose
Transform scientific publications into a complete, publication-grade Chinese PPTX deck following Nature-style visual and scientific storytelling logic.

The deliverable must be a tangible `.pptx` deck. Never stop at a text outline or Markdown script.

Applicable domains:
- Life Sciences, Medicine, Pharmacology, and Molecular Biology
- Chemistry, Chemical Biology, and Materials Science
- Bioengineering, Biophysics, and Applied Physics
- Computational Biology, Bioinformatics, and AI for Science (AI4S)
- Interdisciplinary Nature/Science/Cell family primary research
- Reviews, Perspectives, Benchmarks, Atlases, and Resource papers

---

# Core Principles

## 1. The Scientific Argument Spine
Structure the presentation around scientific logic rather than raw paper sections (Intro/Results/Discussion):
1. **Clinical / Biological Dilemma**: Why does this problem matter now?
2. **Knowledge Gap & Bottleneck**: What was missing or technically intractable?
3. **Hypothesis / Rational Design**: What did the authors propose?
4. **Experimental Evidence Chain**: How do the controls and assays validate the claim?
5. **Mechanism / Working Model**: How do the findings integrate into a unified picture?
6. **Critical Appraisal & Caveats**: Where are the experimental boundaries and limitations?
7. **Actionable Inspiration**: What can our lab borrow, reuse, or follow up on?

## 2. Lean Operating Mode
- Read selectively: Capture metadata, abstract, headings, and figure legends first; read methods and results only as needed for selected slides.
- Extract selectively: Only render and crop figure panels that will appear in the final deck.
- Python-first stack: Rely strictly on `PyMuPDF` (`fitz`), `Pillow` (`PIL`), and `python-pptx`.
- Zero GUI automation: Never invoke AppleScript, Keynote, PowerPoint GUI automation, or heavy external office suites.

---

# Engineering & Environment Guardrails

## 1. Vector PDF Figure Extraction Rule (Critical)
*Context: Nature, Cell, and Science papers use vector paths (PDF vector graphics) for 95% of their charts and schematics. Calling `doc.extract_image()` returns empty lists, broken gradients, or meaningless 1x1-pixel masks.*

**Strict Extraction Protocol:**
1. Never rely on embedded image extraction (`extract_image`) for main figures.
2. Target the specific page where the figure resides.
3. Render the full page at **300 DPI** using PyMuPDF:
   ```python
   zoom = 300 / 72  # 4.1667x
   matrix = fitz.Matrix(zoom, zoom)
   pix = page.get_pixmap(matrix=matrix, alpha=False)
   pix.save("page_temp.png")
