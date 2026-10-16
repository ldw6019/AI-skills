---
name: course-study-material
description: Turn a course's lecture material (required), optional hands-on/practice material, and optional student Q&A or notes into a verified, exam-oriented summary study sheet (Markdown, then PDF). Use when the student asks for 공부 자료, 복습 자료, 요약 정리, a study guide, or a review sheet for one week/chapter/unit of any course.
---

# Course study material builder (portable)

A tool-agnostic recipe for building a **summary study sheet** for one unit (a week, chapter, or lecture) of any course. It works in Claude Code (save this file as `SKILL.md` in a skill folder), in any chat assistant (paste the whole file, then give the inputs), or by hand. It needs no particular OS, shell, or renderer. Anything that needs a tool says what to do when the tool is missing.

The goal is not a prettier copy of the slides. The goal is a sheet the student can **study from and test themselves with**, in which every claim was either read from the source or checked.

---

## 0. Inputs

| Input | Required? | What it is | Typical form |
|---|---|---|---|
| A. Lecture material | **Required** | Slides, lecture notes, textbook chapter, transcript | PDF, PPTX, DOCX, MD, pasted text, images |
| B. Practice material | Optional | Labs, exercises, example code, problem sets, spreadsheets, datasets, worked solutions | folder of files, notebook, PDF |
| C. Student Q&A / notes | Optional | Questions the student asked in earlier chats, their own notes, TA answers | chat export, text |
| D. Assignment / exam info | Optional | Assignment guide, grading rules, exam scope, past exam style | text, PDF |

Ask for A only if it is missing. For B, C, D: use them if given, **say plainly in the final report which were used and which did not exist**, and never block on them.

Parameters to confirm once (use these defaults if the student does not care):

| Parameter | Default |
|---|---|
| Output language | The student's language (Korean for a Korean student), even if they type in English. Keep code, commands, formulas, and error messages as they are |
| Unit name and number | From the file name or the first slide, e.g. `6주차` / `Chapter 6` |
| Exam scope | Everything in A. If the student says "from page X on is not in the exam", mark that part (see 3.3) |
| Output | `STUDY.md` plus a rendered PDF named `N주차_공부자료(Topic).pdf` (or `Week N - Topic - Study Guide.pdf`) |
| Unfinished assignment | Explain concepts, but do not write the solution the student is supposed to write |

---

## 1. Workflow

1. **Intake.** List the inputs received, the unit, the scope, and anything unreadable. Ask at most one short question, and only if the answer changes the work (for example, the exam scope is unknown and the unit is long).
2. **Read everything.** Read every page of A, not only the pages with examples. Read all of B. See section 5 for how to read awkward formats.
3. **Map.** Write a private outline: slide/page range → topic. Note which pages are diagrams only, which examples exist in B, and where the slide and the practice material disagree.
4. **Mine C.** The student's own questions are the most valuable material, because they show exactly where this student got stuck. Keep the question and a corrected, shortened answer, and cite it ("10/16 질문").
5. **Verify.** For anything checkable, check it (section 4). Collect only observed results.
6. **Write `STUDY.md` incrementally**:
   - For long units or dense materials, write `STUDY.md` using file tools incrementally (section by section) to prevent output truncation.
   - Ensure all 8~9 self-test cards and 20 review questions are fully written without skipping or truncating.
7. **Render** to PDF (section 6) and **look at it** (first page, a dense page, a self-test page).
8. **Report** briefly: file paths, page count, sources used, what was newly verified or corrected, what was left out and why.

Read limits. If a file is too large, unreadable, or unsupported, report which pages or sections were read and which were not. Do not imply full coverage. Ask for a readable version only when the missing material would change the study sheet.

Keep the reply to the student short, with the result first.

---

## 2. Structure of the study sheet

Title: `# N주차 공부 자료 : <Topic>`, then **one line naming the sources** (file names and page ranges). If part of the unit is out of exam scope, put the scope note directly under the title (see 3.3).

| # | Section | Notes |
|---|---|---|
| 0 | 한 장 요약 | One code block: the whole unit as a tree or flow, with `(p.N)` on every line. Lines must be short enough not to wrap |
| 1..k | One `##` per topic, in source order, with `(p.A~B)` in the heading | See 2.1 |
| k+1 | 정리 / 공식·명령어·핵심 정의 모음 | Cheat-sheet: formulas, commands, definitions, comparison tables |
| k+2 | 실습 자료 현황 | **Only if B exists.** Table: item, source page, status or note |
| k+3 | 복습 질문 | 2-column table `질문 / 정답`; the answer sits next to the question |
| k+4 | 생각해 보기 (예측 → 실행/풀이 → 설명) | The main self-test section (2.2). 8~9 problems |

### 2.1 A topic section

In this order, skipping what does not apply:

1. **Core Concept & Readability First**: The main idea in one or two sentences. Avoid long blocks of uninterrupted text; break complex concepts down using tables, bullet lists, and visual callout boxes.
2. First use of a term: native term plus the original in parentheses, for example 파일 디스크립터(file descriptor, fd).
3. **Diagrams & Visuals (SVG / ASCII)**: For complex architectures, memory maps, system flows, or execution models, generate styled, self-contained **SVG diagrams** (or clean ASCII diagrams in code blocks) to visually ground the explanation.
4. The definition, signature, or formula in a code/math block, with return values or conditions.
5. A table for anything that is a list of options, cases, or an A-versus-B comparison.
6. The source example (from A or B), shortened to the lines that matter, with what each line does.
7. The real output, if it was obtained (section 4).
8. `> **주의**:` boxes for mistakes in the slides, common traps, and surprises.
9. `> **참고**:` boxes for questions this student actually asked (C).

Do not paste a full solution to an assignment that is still open. For finished assignments, describe the flow and the key lines instead of dumping the whole file.

### 2.2 생각해 보기 (self-test problems)

The point is to make the student **predict and explain**, not just recall. Recall questions alone are not enough.

Open the section with the routine:

1. Read the problem and **write a prediction before running or solving**.
2. If stuck, read only the hint.
3. Run or solve it yourself.
4. Compare with the answer in the same card and explain where the prediction went wrong.

Each problem is one card:

```
### 생각 N. <question title> (p.A~B)

<a short snippet, scenario, or calculation the student can actually run or do>

**예측**: <2~3 concrete questions: what is printed, what value, why>

[[예측]]

> **힌트**: <one line pointing at the page or concept, never the answer>

[[정답]]
**<The observed or computed result in bold, one sentence.>**

<Output or working. Then why. Then what it teaches or where it shows up in practice.>
[[/정답]]
```

- Aim for 8–9 problems per unit when the source material supports that many. Scale the number to the unit’s length and learning goals; do not add repetitive or weak problems just to reach the target.
- Mix the types, and stay inside this unit's material:
  - **Predict before running**: a small change to a source example
  - **Discover by experiment**: something the material implies but never shows
  - **Edge cases**: errors, empty input, limits, wrong units, boundary values
  - **Find the bug**: a plausible wrong version of a source example
  - **Design it**: one open-ended question with no single answer (give a discussion, not a verdict)
- Link problems to each other and to the assignment when it helps.
- For non-programming subjects the same card works: a calculation with changed numbers, a case to classify, an argument to critique, a proof step to justify, a graph to read. "Run it" becomes "work it out on paper".

### 2.3 복습 질문

20 or so rows, table `질문 | 정답`, numbered. One fact or one explanation per row, answer in one to three sentences with the page in parentheses. Say in the intro that the questions are for self-study, not official exam questions.

---

## 3. Writing rules

### 3.1 Style

- Short sentences. Result first, reasoning after.
- Tables for comparisons and option lists. ASCII diagrams in code fences for layouts, flows, and memory or data structures.
- `**bold**` for the **one key phrase** per paragraph, never whole sentences, never whole paragraphs. Do not underline or highlight it in the final design (see 6.3).
- No emojis. No filler such as "let's dive in".
- Keep code, commands, formulas, and error messages exactly as they are, in backticks or code blocks.
- Mark source mistakes explicitly (typo in a slide, wrong formula, code that does not compile) and say what the sheet changed.
- Use only what the markup in 6.2 supports: no nested lists, no images, no links inside the body, no italics. Write around these.

### 3.2 Honesty rules

- **Never invent facts** to fill a gap. If the material is silent, write "자료에 없음" and, if useful, mark any addition as `(보충: 강의자료 밖 내용)`.
- **Never present self-made questions as official exam questions.**
- **Label anything unverified** as `(미검증)`. If an earlier chat answer turns out to be wrong when checked, write the observed behavior and tell the student about the correction.
- Diagram-only or image-only pages: describe them from the surrounding pages, and say that the page was image-only.
- If something depends on the environment (compiler, version, locale, calculator mode), say so.

### 3.3 Exam scope

If the student states a scope (for example "up to slide 41 only"):

- Put a `> **주의**:` box right under the title that states the range and which part is excluded.
- Keep the excluded part in the sheet, because it is still useful, but title it `[시험범위 아님]` and add a one-line box at its top.
- Do not write self-test problems for excluded parts, except where a problem is really about an in-scope idea.
- Record the scope where the next session will see it (a note file or the project's instructions).

---

### 4. Verification

The sheet's value comes from claims that were checked.

| Material Type | How to verify |
|---|---|
| **Executable code & scripts** | Build and run in a throwaway directory. Quote real output; test at least one edge case or error condition |
| **Mathematical, statistical & quantitative claims** | Programmatically or step-by-step recompute worked examples, equations, and data calculations. Check units, rounding, and boundary values |
| **Commands, configs & system calls** | Test against a local sandbox, dry-run flags (`--dry-run`, `--plan`, mock mode), or help tools. **NEVER modify live system states or execute paid external operations** |
| **Definitions, theorems & conceptual models** | Cross-check against lecture text; explicitly flag any ambiguities or discrepancies found in source slides |
| **Unexecutable / Non-verifiable** | Reason logically through the steps, then mark the result as `(미검증, 직접 실행해 보지 못함)` |

Rules:
- Use only output you actually saw. Do not "tidy" outputs.
- Use temporary files only; never touch the student's own files or assignment folders. Delete temporary files afterwards.
- If the behavior can differ by tool version or platform, name the version or platform you used.
- If a verified result contradicts the lecture, report both and explain.

---

## 5. Reading awkward inputs

| Input | How |
|---|---|
| Text PDF | Extract all pages as text with any tool (for example `pdftotext -layout`, Python `pypdf`, or copy-paste). Keep page numbers |
| Image-only slides or scans | Look at the page images directly; OCR if available; otherwise ask the student for the text of those pages |
| PPTX/DOCX | Export to PDF or plain text first, keep slide numbers |
| Notebooks (`.ipynb`) | Read cell by cell; run it if the environment allows |
| Folder of code or files | List it, read each file, run the test or build step if there is one |
| Chat exports | Pick the questions that belong to this unit; keep the student's wording of the question |

If the tool is missing and cannot be installed, ask the student to paste the text of the needed pages. Do not guess page contents.

---

## 6. Rendering

### 6.1 Output naming and placement

`STUDY.md` (fixed name) and the PDF `N주차_공부자료(Topic).pdf` next to it, in the unit's folder. The PDF title banner shows the unit number, and the footer shows `N주차 · Topic  page / total` on every page.

### 6.2 Markdown subset (renders everywhere)

Headings `#`~`####`; fenced code blocks; `|` tables; `>` quotes; flat `-` and `1.` lists; `` `code` ``; `**bold**`. Extras used by the template, each with a plain-text fallback so the raw file still reads well:

| Marker | Meaning | Fallback if the renderer does not know it |
|---|---|---|
| `> **주의**:` | warning box (yellow) | looks like a normal quote |
| `> **핵심**:` / `> **참고**:` | key point / note box (blue) | same |
| `> **힌트**:` | hint box (purple) | same |
| ```` ```out ```` | observed output box (green) | plain code block |
| `[[예측]]` | empty write-in box for the prediction | a line `[[예측]]`; replace with a blank bordered area or `(예측을 여기에 적기)` |
| `[[정답]]` ... `[[/정답]]` | Answer panel inside the problem card | For interactive web HTML: use `<details><summary>정답</summary> ... </details>`. **For PDF rendering**: render directly as a visible styled green callout box (`<div class="answer">...</div>`) so the answer is fully printed on the paper/PDF. |

### 6.3 Design (what readers asked for)

- A4, margins about 14 mm, body 10.5~11 pt, a Korean-capable font (Noto Sans KR, NanumBarunGothic, Pretendard), monospace for code (D2Coding, Noto Sans Mono; turn ligatures off so `->`, `!=`, `==` print as typed).
- One accent color (blue) for section bars, table headers, and bold. Light zebra stripes on tables. Callouts in yellow, blue, purple, and green. Problem cards as bordered panels with the answer in a light-green area inside the same card.
- **Bold text is just bold plus the accent color: no underline, no highlighter band.** A dark underline under bold text made a previous version hard to read.
- Clickable table of contents built from the `##` headings, shown on page 1.
- Keep code lines short enough not to wrap; keep the one-page summary tree within the page width.

Minimal CSS for a browser-based print (works in any Chromium/Firefox/Safari "Save as PDF"):

```css
@page { size: A4; margin: 14mm; }
body { font-family: "Noto Sans KR", "NanumBarunGothic", sans-serif; font-size: 10.5pt; line-height: 1.6; color: #1f2328; }
h2 { background: #eef3fb; border-left: 5px solid #1f4fbf; padding: 6px 10px; border-radius: 4px; }
strong { color: #0b3d91; font-weight: 700; }          /* no underline, no background */
code, pre { font-family: "D2Coding", "Noto Sans Mono", monospace; font-variant-ligatures: none; }
pre { background: #f6f8fa; padding: 8px 10px; border-radius: 4px; overflow-wrap: anywhere; white-space: pre-wrap; }
table { border-collapse: collapse; width: 100%; }
th { background: #1f4fbf; color: #fff; padding: 6px 8px; }
td { border: 1px solid #d0d7de; padding: 5px 8px; }
tr:nth-child(even) td { background: #f6f8fa; }
blockquote { background: #fff8d6; border-left: 5px solid #e0a800; margin: 8px 0; padding: 6px 10px; }
.card { border: 2px solid #1f4fbf; border-radius: 8px; padding: 10px; margin: 14px 0; break-inside: avoid; }
.answer { background: #eefaf0; border-left: 5px solid #2da44e; padding: 8px 10px; }
```

### 6.3.1 SVG Diagram Standards

When generating SVG diagrams for architecture, flows, or memory layouts:
- **Responsive & Self-contained**: Use `viewBox="0 0 W H"` and `width="100%"` with inline CSS styling. Do not use external font files.
- **Light Theme Optimized**: Use high-contrast stroke colors (e.g., `#1f2328`, `#1f4fbf`) and soft light fill colors (e.g., `#f6f8fa`, `#eef3fb`).
- **Compact Size**: Keep the SVG structure simple and concise to prevent token limit overflow and keep PDF rendering fast.
- **Fallback**: If an SVG cannot be rendered cleanly by the target PDF engine, fall back to a clean ASCII diagram inside a fenced code block.


### 6.4 Ways to get a PDF (pick whatever exists)

| Tool | How |
|---|---|
| Pandoc | `pandoc STUDY.md -o out.pdf --pdf-engine=xelatex -V CJKmainfont="Noto Sans CJK KR"` (plain look), or convert to HTML with `--css style.css` and print |
| Browser | Convert to HTML (any Markdown viewer), apply the CSS above, "Print → Save as PDF" |
| VS Code / Typora / Obsidian | "Export as PDF" with a custom CSS |
| Word / Google Docs | Paste or `pandoc -o out.docx`, then export to PDF |
| A project script | If the project already has a renderer, use it instead of re-inventing |

After rendering, **look at the pages**: first page, a dense page, a self-test page, and the last page. Check for broken bold (`**` visible), wrapped table headers, code turned into symbols, boxes cut across pages, and text running off the page. Fix the Markdown or CSS and rebuild.

---

## 7. Final checklist

- [ ] Every page or section of A is covered, in source order, with `(p.N)` references
- [ ] Scope note present if the student gave a scope; excluded parts titled `[시험범위 아님]`
- [ ] Every example result or number was observed or recomputed, or is marked `(미검증)`
- [ ] Source mistakes are flagged with `> **주의**:`
- [ ] The student's own questions (C) appear where they belong, with the date or source
- [ ] Self-test cards are appropriate to the unit’s length and learning goals. Each card has a prediction box, a one-line hint, and its answer in the same card.
- [ ] 복습 질문 table has answers next to the questions
- [ ] Open assignments are explained but not solved
- [ ] Bold is sparse and has no underline or highlighter
- [ ] PDF named correctly, footer shows unit and page numbers, pages were looked at
- [ ] Temporary files removed; the student's own files untouched
- [ ] Report: paths, page count, sources used (and which did not exist), corrections, omissions

---

## 8. Quick start prompt (copy and fill in)

```
Use the course-study-material recipe.

Course: <name>            Unit: <week / chapter number and topic>
Lecture material (required): <files or pasted text>
Practice material (optional): <files, or "none">
My earlier questions / notes (optional): <paste, or "none">
Assignment or exam info (optional): <guide, exam scope such as "pages 1-41 only", or "none">
Language: <Korean / English>
Output: STUDY.md and a PDF named "<N>주차_공부자료(<Topic>).pdf"
Do not solve my open assignment. Mark anything outside the exam scope as [시험범위 아님].
```
