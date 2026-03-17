# Copilot Instructions for pythonds

## Project Overview

This is **"Problem Solving with Algorithms and Data Structures Using Python"** (4th edition) by Miller, Ranum, and Yasinovskyy. It's an interactive textbook published on Runestone Academy. Content is authored in **PreTeXt XML** (`.ptx` files in `pretext/`).

The repository contains a legacy Runestone/Sphinx system (`_sources/`, `pavement.py`, `conf.py`) that is no longer the primary authoring path. All new content work happens in the PreTeXt system.

## Environment Setup

This project requires **Python 3.12**. Other versions may cause `pip install` failures.

```bash
# Create and activate a virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies (first time, or after requirements.txt changes)
pip install -r requirements.txt
```

If `pip install` fails, check your Python version (`python3 --version`) and ensure it is 3.12.x. Work through any errors and update `requirements.txt` to account for version constraints.

## Build Commands

```bash
# Build for web
pretext build web

# Serve locally — opens a browser on localhost to view the textbook
pretext view

# Build for Runestone Academy
pretext build runestone

# Build PDF or LaTeX
pretext build pdf
pretext build latex
```

There is no test suite or linter. PreTeXt validates XML structure during `pretext build`. A utility script `fix_xrefs.py` can validate and auto-correct cross-references.

## Architecture

- **`pretext/`** — All book content as PreTeXt XML (`.ptx`). This is the source of truth.
  - `main.ptx` — Root document, includes all chapters via `xi:include`.
  - `bookinfo.ptx` — Metadata (authors, license, edition).
  - Each chapter is a subdirectory (e.g., `BasicDS/`, `Trees/`, `Graphs/`) with a `toctree.ptx` that includes its sections.
- **`project.ptx`** — PreTeXt CLI project configuration (build targets, publication files).
- **`publication/`** — Publication profiles controlling output format and theme.
  - `publication.ptx` — Web output (oscarlevin theme, blue_red colors).
  - `publication-academy.ptx` — Runestone Academy output (salem theme, slate palette).
- **`assets/`** — Static assets (images, CSS) referenced by content.
- **`generated-assets/`** — Auto-generated files (codelens traces, datafiles). Do not edit manually.
- **`_sources/`** — Legacy RST files (deprecated, kept for reference only).

## Content Authoring Conventions

### File and ID Naming

- Each section is a separate `.ptx` file named descriptively (e.g., `ImplementingaStackinPython.ptx`).
- XML `xml:id` attributes use kebab-case: `basic-ds_implementing-a-stack-in-python`.
- Code listing IDs use `lst-` prefix: `lst-stackcode1`, `lst-binarysearchpy`.
- Figure IDs use `fig-` prefix: `fig-activecode`.

### Section Structure

Every content section follows this pattern:

```xml
<section xml:id="chapter-slug_section-slug">
  <title>Section Title</title>

  <p>Narrative prose with <c>inline code</c>, <em>emphasis</em>,
     and <xref ref="lst-example"/> cross-references.</p>

  <listing xml:id="lst-example">
    <program label="lst-example" interactive="activecode" language="python">
      <input>
# Executable Python code
      </input>
    </program>
  </listing>

  <exercises>
    <title>Self Check</title>
    <exercise label="exercise-id">
      <statement><p>Question text</p></statement>
      <choices>
        <choice correct="yes">
          <statement><p>Answer</p></statement>
          <feedback><p>Explanation</p></feedback>
        </choice>
      </choices>
    </exercise>
  </exercises>
</section>
```

### Interactive Code Types

- **`interactive="activecode"`** — Executable Python in the browser.
- **`interactive="codelens"`** — Step-through visualization (generates trace JS in `generated-assets/trace/`).
- **No `interactive` attribute** — Read-only code listing.

### Chapter Organization

Each chapter directory contains:
- `toctree.ptx` — Chapter wrapper with ordered `xi:include` of all sections.
- `Objectives.ptx` — Learning objectives (first section).
- Content sections in teaching order.
- `Summary.ptx`, `KeyTerms.ptx`, `Exercises.ptx` — Closing sections.

### Cross-References

Use `<xref ref="xml-id"/>` to reference other sections, listings, and figures. The `fix_xrefs.py` script can detect and repair broken references.
