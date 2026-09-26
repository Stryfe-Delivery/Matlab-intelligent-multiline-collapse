# MATLAB Multiline Collapse Reviewer

A browser-based tool for reviewing and collapsing MATLAB multiline continuation blocks (using `...`) into single lines. Paste code copied from a Live Script, review each continuation block with a diff-style interface, and copy the approved result back into MATLAB.

![No build step](https://img.shields.io/badge/build-none-brightgreen)
![Single file](https://img.shields.io/badge/single--file-HTML-blue)
![platform](https://img.shields.io/badge/platform-linux%20%7C%20macOS%20%7C%20windows-lightgrey)

## Overview

When copying code out of a MATLAB, (or when using MATLAB's copiliot tools or other ai,) continuation lines often get spread across multiple lines using the `...` operator. This tool detects those blocks, proposes a collapsed single-line version for each one, and lets you accept or reject every change individually before producing the final paste-ready output.

Tool is privacy forward. All processing happens entirely in the browser — no server, no dependencies, no data leaves your machine.

## Features

- **Automatic detection** of MATLAB continuation blocks (`...` at end of line)
- **Line-by-line diff view** showing the original multiline block vs. the proposed collapsed result
- **Per-change review** with Accept / Reject buttons
- **Progress tracking** — see how many blocks are found, accepted, kept, and still pending
- **Smart parsing** that ignores `...` inside strings and comments, and correctly handles MATLAB string quotes, escape sequences, and the transpose operator (`'`)
- **Final output view** with copy-to-clipboard and download-as-`.m` options
- **Warning banner** for any unreviewed blocks (which remain in their original form)
- **Fully client-side** — works offline, no installation required

## Usage

1. Open the HTML file in any modern browser (Chrome, Firefox, Edge, Safari).
2. Paste your MATLAB code into the **Source code** panel on the left. Detection happens automatically on input.
3. The **Change review** panel on the right shows one continuation block at a time:
   - **Original lines** (red) show the multiline form as pasted.
   - **Proposed change** (green) shows the collapsed single-line form.
4. Click:
   - **✓ Accept collapse** — apply the collapsed version to the final output.
   - **✕ Keep multiline** — preserve the original multiline form.
5. Use **← Previous** / **Next →** to navigate, or **Reset review** to clear all decisions.
6. Switch to the **Final output** tab to:
   - **Copy result** to the clipboard, or
   - **Download .m** as a file.
7. Paste the result back into MATLAB.

> Unreviewed blocks remain exactly as pasted, so you can safely copy the output at any point.

## Example

**Input:**

```matlab
terrainResultsFile = ...
"TerrainResults.csv";

shelteringResultsFile = ...
"ShelteringResults.csv";
```

**Collapsed output (after accepting):**

```matlab
terrainResultsFile = "TerrainResults.csv";

shelteringResultsFile = "ShelteringResults.csv";
```

## How the collapsing works

For each detected continuation block, the tool:

1. Strips the trailing `...` from every line in the block.
2. Trims each fragment.
3. Joins the fragments with a single space.
4. Preserves the indentation of the first line in the block.

The parser tracks string literals and comments so that `...` appearing inside a string (e.g. `"wait..."`) or after a `%` comment is not treated as a continuation.

## Technical notes

- **Single HTML file** — HTML, CSS, and JavaScript are all self-contained.
- **No external dependencies** — no frameworks, no CDN links.
- **Parser behavior:**
  - `%` outside of a string starts a comment; anything after it is ignored.
  - Double-quoted strings handle `""` as an escaped quote.
  - Single-quoted strings handle `''` as an escaped quote.
  - A single quote preceded by an identifier, `)`, or `]` is treated as the transpose operator, not a string delimiter.
- **Output construction** — only accepted blocks are collapsed; rejected and unreviewed blocks are preserved verbatim.

## Browser support

Works in any modern browser that supports ES2021 features (`String.prototype.replaceAll`, optional chaining, etc.):

- Chrome / Edge 85+
- Firefox 77+
- Safari 13.1+

## Limitations

- Only detects `...` continuations that appear at the end of a line (optionally followed by whitespace or a `%` comment).
- Assumes standard MATLAB syntax; unusual constructs (e.g. command-form function calls) may not parse perfectly.
- Does not reformat code beyond collapsing continuations — it is a reviewer, not a formatter.
- Any section breaks, plaintext, or images in the source .mlx are not transferred back and forth. you will need to reinsert section breaks after
