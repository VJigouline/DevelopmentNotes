# Convert Markdown to HTML with Cross-References

This document describes practical workflows to convert Markdown to HTML while preserving and enabling cross-references between files, figures, tables and equations. It includes copy-ready PowerShell commands and short examples for three approaches:

- Pandoc + `pandoc-crossref` (best for academic-style refs: figures, tables, equations)
- MkDocs (site generator with page-to-page links; great for docs sites)
- Node + `markdown-it` (custom pipelines with plugins)

Choose the approach that fits your needs.

---

## Concepts: what "cross-references" means

- Page-to-page links: linking from one Markdown file to a heading in another file (relative `./other.md#heading`).
- Labeled references: labeled figures/tables/equations that you can reference by name (`@fig:label`, `@eq:label`).
- Anchors: stable HTML ids on headings or elements so links don't break.

For simple page linking, standard Markdown relative links are sufficient. For labeled references (Figure 1, Eq. 2), use a converter that supports cross-reference syntax (Pandoc + pandoc-crossref) or a documentation generator with a plugin.

---

## 1) Pandoc + pandoc-crossref (recommended for figures/tables/equations)

Pandoc is a powerful document converter. With `pandoc-crossref` you can reference figures, tables and equations by label.

Install (Windows):

- Install `pandoc` (download from https://pandoc.org/ or use Chocolatey: `choco install pandoc`).
- Install `pandoc-crossref` (download from the project releases or use conda: `conda install -c conda-forge pandoc-crossref`). See: https://github.com/lierdakil/pandoc-crossref

Example Markdown (labels + references):

```markdown
![System architecture](images/arch.png){#fig:arch}

See Figure @fig:arch for the architecture.

$$
E = mc^2
$$ {#eq:emc}

Refer to equation @eq:emc.
```

Combine multiple Markdown files and convert to a single HTML file with a table of contents:

```powershell
# convert all markdown files in order to a single HTML with crossrefs
pandoc -s --filter pandoc-crossref --toc -o site.html chapter1.md chapter2.md appendix.md
```

Or convert one file to a standalone HTML page:

```powershell
pandoc -s --filter pandoc-crossref -o chapter1.html chapter1.md
```

Notes:
- Use `{#fig:label}` for figures and `@fig:label` to reference them.
- For equations use `{#eq:label}` and `@eq:label`.
- Pandoc supports metadata and templates to style the output.

Troubleshooting:
- If `pandoc-crossref` isn't found, ensure it's installed and on PATH. You can also pass the full path to the filter: `--filter C:/path/to/pandoc-crossref.exe`.
- For multiple files, maintain a consistent order (pass files to `pandoc` in the order you want them concatenated).

---

## 2) MkDocs / MkDocs Material (recommended for documentation sites)

MkDocs creates a website from a `docs/` folder. Cross-page links are just relative links, and headings get stable anchors.

Install & quick start (PowerShell):

```powershell
pip install mkdocs mkdocs-material
mkdocs new mysite
cd mysite
# put your .md files inside the docs/ folder, then edit mkdocs.yml to set navigation order
mkdocs serve  # runs a local dev server at http://127.0.0.1:8000
```

Linking between pages:

- Use relative links: `[See installation](setup.md#installation)`
- To link to a specific heading on another page: `[Read more](otherpage.md#heading-name)`

MkDocs automatically generates HTML anchors for headings using a deterministic slug policy.

Advantages:
- Automatic navigation (sidebar) and full-site build.
- Built-in search (with mkdocs-material) and plugins.
- Easy deployment to GitHub Pages or any static host: `mkdocs build` creates the `site/` folder.

Limitations:
- MkDocs does not provide numbered figure/table/equation references like pandoc-crossref.

---

## 3) Node + markdown-it (custom pipeline)

If you need full control or want to embed the conversion into a JavaScript toolchain, use `markdown-it` with plugins for anchors, TOC, and custom reference handling.

Quick example (PowerShell commands, then `build.js`):

```powershell
mkdir mdsite; cd mdsite
npm init -y
npm install markdown-it markdown-it-anchor markdown-it-table-of-contents fs-extra
```

`build.js` (simple example to convert a single file and add anchors):

```javascript
const fs = require('fs-extra');
const MarkdownIt = require('markdown-it');
const anchor = require('markdown-it-anchor');
const toc = require('markdown-it-table-of-contents');

const md = new MarkdownIt({ html: true })
  .use(anchor, { permalink: true, permalinkClass: 'anchor', permalinkSymbol: '¶' })
  .use(toc, { includeLevel: [2,3] });

async function build(input, output) {
  const src = await fs.readFile(input, 'utf8');
  const html = md.render(src);
  await fs.writeFile(output, `<!doctype html><meta charset="utf-8"><link rel="stylesheet" href="styles.css">${html}`);
}

build('README.md', 'index.html');
```

Run:

```powershell
node build.js
```

To implement labeled cross-references (e.g., `@fig:label`), you would add a plugin or pre-process step that collects labeled elements and replaces `@fig:label` with the appropriate HTML. There are third-party `markdown-it` plugins or you can write a small transform.

---

## 4) Page-to-page linking (relative links and anchors)

For most documentation needs, standard Markdown links are enough:

```markdown
[Open the installation guide](docs/Installation.md)
[Anchor to a section](docs/Installation.md#linux-installation)
```

Tips for reliable anchors:
- Keep heading text stable, or create explicit anchors: `<h2 id="my-section">My Section</h2>` or use header suffix: `## My Section {#my-section}` (supported by some converters like Pandoc and some Markdown flavors).
- Use relative paths in your site (avoid absolute file system paths).

---

## 5) Example: multi-file site with Pandoc crossrefs

File: `chapter1.md`

```markdown
# Chapter 1 {#ch1}

![System diagram](images/arch.png){#fig:arch}

See Figure @fig:arch and continue in [Chapter 2](chapter2.md#ch2).
```

File: `chapter2.md`

```markdown
# Chapter 2 {#ch2}

This continues the narrative and references Equation @eq:energy.

$$
E = mc^2
$$ {#eq:energy}
```

Build command (PowerShell):

```powershell
pandoc -s --filter pandoc-crossref --toc -o book.html chapter1.md chapter2.md
```

`book.html` will contain resolved references and internal anchors.

---

## 6) Deployment notes

- When hosting on GitHub Pages or another static host, ensure links are relative (no `file://` paths) and base paths are correct. Pandoc can set a `--metadata=site-url:value` or use templates to include correct base tags.
- For single-page outputs, all anchors are internal and work out-of-the-box.
- For multi-page sites (MkDocs, Docusaurus), the site generator handles navigation and routing; verify rewrite rules for SPAs.

---

## 7) Troubleshooting checklist

- Broken link -> check relative path and anchor slug.
- `pandoc-crossref` not found -> ensure the filter is installed and on PATH or pass absolute path to `--filter`.
- Images not included -> ensure image paths are correct relative to the Markdown file, or copy assets to the output folder.
- Encoding issues -> ensure files are UTF-8 without BOM.

---

## 8) Quick decision guide

- Need numbered figures/equations/tables? → Use **Pandoc + pandoc-crossref**.
- Want a full documentation site with search and navigation? → Use **MkDocs** (or Docusaurus) and standard relative links.
- Need a custom or JS-integrated pipeline? → Use **markdown-it** + plugins and write a small build script.

---

If you'd like, I can:
- Scaffold a small example project in this repo that demonstrates `pandoc` conversion with crossrefs, or
- Create an `mkdocs` example site with `docs/` and `mkdocs.yml` so you can run `mkdocs serve` locally.

Which example would you like me to add to the repo?
