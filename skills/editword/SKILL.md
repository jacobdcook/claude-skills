# /editword — Edit Specific Paragraphs in a .docx File

## Trigger

Use when the user says `/editword`, "edit the word doc", "update paragraph N in the docx", "fix paragraph N", or provides a `.docx` path and paragraph index with new text.

## What This Skill Does

Replaces the text of one or more paragraphs in a `.docx` file by index, preserving the original run formatting (font, bold, italic, size). Overwrites the file in place unless told otherwise.

## How to Use

1. **Get the file path** from the user (or infer from context).
2. **Map the paragraphs** — run the index probe below to print all paragraph indices and their current text so the user can identify which numbers to target.
3. **Get the new text** — either from the user directly, or generate it yourself (e.g. after an audit).
4. **Run the rewrite script** with the rewrites dict filled in.

## Step 1 — Probe paragraph indices

```python
from docx import Document
doc = Document('/path/to/file.docx')
for i, p in enumerate(doc.paragraphs):
    if p.text.strip():
        print(f'{i:3d}  {p.text[:100]}')
```

## Step 2 — Rewrite script

```python
from docx import Document
from docx.oxml.ns import qn
from lxml import etree
import copy

def replace_para_text(para, new_text):
    """Replace paragraph text. Use \\n for line break within a paragraph."""
    p = para._element
    first_rpr = None
    for r_elem in p.findall(qn('w:r')):
        rpr = r_elem.find(qn('w:rPr'))
        if rpr is not None:
            first_rpr = copy.deepcopy(rpr)
        break
    for r_elem in list(p.findall(qn('w:r'))):
        p.remove(r_elem)
    r = etree.SubElement(p, qn('w:r'))
    if first_rpr is not None:
        r.insert(0, first_rpr)
    parts = new_text.split('\n')
    for i, part in enumerate(parts):
        if i > 0:
            etree.SubElement(r, qn('w:br'))
        if part:
            t = etree.SubElement(r, qn('w:t'))
            t.text = part
            t.set('{http://www.w3.org/XML/1998/namespace}space', 'preserve')

# ── EDIT ONLY THIS DICT ──────────────────────────────────────────────
rewrites = {
    # paragraph_index: "New text here.",
    # 61: "New HIPAA paragraph.",
    # 59: "New PCI DSS paragraph.",
}
# ────────────────────────────────────────────────────────────────────

INPUT  = '/path/to/file.docx'
OUTPUT = INPUT  # overwrites in place; change if you want a copy

doc = Document(INPUT)
for idx, text in rewrites.items():
    replace_para_text(doc.paragraphs[idx], text)
doc.save(OUTPUT)
print(f"Done. {len(rewrites)} paragraph(s) updated.")
```

## Notes

- `\n` inside a value inserts a Word line break (`w:br`) within the same paragraph — use for multi-line paragraphs that should stay visually together.
- To insert a blank line between logical blocks inside one paragraph slot, use `\n\n` — this adds two `w:br` elements.
- Paragraph indices shift if paragraphs are added or deleted. Always re-probe after structural changes.
- Tables, headers, footers, and text boxes are **not** in `doc.paragraphs` — they need separate traversal.
- This skill requires `python-docx` and `lxml`: `pip install python-docx lxml`.

## Example invocation

User: `/editword — fix paragraph 61 in the D482 doc`

Claude: runs the probe, identifies paragraph 61, generates new text, fills in the rewrites dict, runs the script, confirms with a snippet of the saved text.
