# Markdown Syntax Guide

This guide covers the essential Markdown syntax for writing formatted documents, README files, and documentation.

## 1) Headings

Use `#` to create headings (1–6 levels):

```markdown
# Heading 1
## Heading 2
### Heading 3
#### Heading 4
##### Heading 5
###### Heading 6
```

**Output:**
- `# Heading 1` — largest, typically used for page title
- `## Heading 2` — section heading
- `### Heading 3` — subsection
- etc.

Alternative syntax for h1 and h2 (underlines):

```markdown
Heading 1
=========

Heading 2
---------
```

## 2) Text formatting

### Bold

```markdown
**bold text**
__bold text__
```

### Italic

```markdown
*italic text*
_italic text_
```

### Bold and italic

```markdown
***bold and italic***
___bold and italic___
```

### Strikethrough

```markdown
~~strikethrough~~
```

### Inline code

```markdown
Use `const x = 5` for inline code.
```

## 3) Paragraphs and line breaks

**Paragraph**: Separate lines with a blank line.

```markdown
This is paragraph 1.

This is paragraph 2.
```

**Line break**: End a line with two spaces or `\`:

```markdown
Line 1  
Line 2

or:

Line 1\
Line 2
```

## 4) Lists

### Unordered list

```markdown
- Item 1
- Item 2
- Item 3

or with * or +:

* Item 1
+ Item 2
```

### Ordered list

```markdown
1. First item
2. Second item
3. Third item
```

### Nested lists

```markdown
- Item 1
  - Nested item 1a
  - Nested item 1b
- Item 2
  1. Ordered nested item
  2. Another ordered item
```

### Description list (some Markdown flavors)

```markdown
Term
:   Definition of the term

Another term
:   Its definition
```

## 5) Blockquotes

```markdown
> This is a blockquote.
> 
> It can span multiple lines.
> 
> > And can be nested.
```

**Output:**
> This is a blockquote.

## 6) Code blocks

### Fenced code block (with syntax highlighting)

````markdown
```javascript
const greeting = "Hello, world!";
console.log(greeting);
```

```python
def hello():
    print("Hello, world!")
```

```powershell
Write-Host "Hello, world!"
```
````

### Indented code block (4 spaces or 1 tab)

```markdown
    const x = 5;
    console.log(x);
```

## 7) Links

### Basic link

```markdown
[Link text](https://example.com)
```

### Link with title

```markdown
[Link text](https://example.com "Hover title")
```

### Reference-style link

```markdown
[Link text][ref]

[ref]: https://example.com
```

### Autolink

```markdown
<https://example.com>
<email@example.com>
```

## 8) Images

### Basic image

```markdown
![Alt text](image.png)
```

### Image with title

```markdown
![Alt text](image.png "Image title")
```

### Image with link

```markdown
[![Alt text](image.png)](https://example.com)
```

### Reference-style image

```markdown
![Alt text][img-ref]

[img-ref]: image.png
```

## 9) Horizontal rule

```markdown
---

or:

***

or:

___
```

**Output:** A horizontal line.

## 10) Tables

```markdown
| Column 1 | Column 2 | Column 3 |
|----------|----------|----------|
| Data 1   | Data 2   | Data 3   |
| Data 4   | Data 5   | Data 6   |
```

### Table alignment

```markdown
| Left align | Center align | Right align |
|:-----------|:------------:|------------:|
| L1         |      C1      |          R1 |
| L2         |      C2      |          R2 |
```

- `:---` — left align
- `:---:` — center align
- `---:` — right align

## 11) Escaping characters

Use backslash `\` to escape special Markdown characters:

```markdown
\*not italic\*
\[not a link\](url)
\# not a heading
```

## 12) Comments (HTML)

```markdown
<!-- This is a comment and won't be rendered -->
```

## 13) Task lists (GitHub and some flavors)

```markdown
- [x] Completed task
- [ ] Incomplete task
- [ ] Another incomplete task
```

## 14) Strikethrough (GitHub and some flavors)

```markdown
~~This is strikethrough~~
```

## 15) Subscript and superscript (some flavors)

### HTML approach (works in most Markdown)

```markdown
H<sub>2</sub>O
E = mc<sup>2</sup>
```

### Using HTML directly

```markdown
H₂O (Unicode character)
E = mc² (Unicode character)
```

## 16) Keyboard keys (HTML)

```markdown
Press <kbd>Ctrl</kbd> + <kbd>C</kbd> to copy.
```

## 17) Line breaks in lists and blockquotes

### Within a list

```markdown
- Item 1 with multiple lines
  This continues the item.
  
- Item 2
```

### Within a blockquote

```markdown
> Line 1
> 
> Line 2 (blank line breaks paragraphs in blockquotes)
```

## 18) Markdown flavors and extensions

Different platforms support different extensions:

| Flavor | Features |
|--------|----------|
| **GitHub Flavored Markdown (GFM)** | Tables, task lists, strikethrough, autolinks |
| **CommonMark** | Standard Markdown spec |
| **MultiMarkdown** | Footnotes, subscript, superscript, metadata |
| **Markdown Extra** | Abbreviations, footnotes, definition lists |

## 19) Best practices

✅ **Do:**
- Use consistent heading hierarchy (`# → ## → ###`, not skipping levels).
- Separate headings from content with blank lines.
- Use blank lines between list items for readability.
- Format code blocks with syntax highlighting (specify language).
- Use descriptive link text (not "click here").
- Keep line length reasonable (~80 characters) for readability in source.
- Use backticks for inline code (variable names, commands, etc.).

❌ **Don't:**
- Use `#` for styling; use it for document structure only.
- Mix list markers (`-`, `*`, `+`) inconsistently.
- Create deeply nested structures (more than 3 levels of nesting can be hard to read).
- Use images without alt text for accessibility.
- Over-use bold and italics for emphasis.

## 20) Quick reference

| Element | Syntax |
|---------|--------|
| Heading 1 | `# Heading 1` |
| Heading 2 | `## Heading 2` |
| Bold | `**bold**` |
| Italic | `*italic*` |
| Inline code | `` `code` `` |
| Unordered list | `- Item` |
| Ordered list | `1. Item` |
| Blockquote | `> Quote` |
| Code block | ` ```language ` ... ` ``` ` |
| Link | `[text](url)` |
| Image | `![alt](image.png)` |
| Horizontal rule | `---` |
| Table | ` \| Col1 \| Col2 \| ` |
| Strikethrough | `~~text~~` |
| Task list | `- [ ] Task` |

## 21) Tools and resources

- **Editor with preview**: VS Code with Markdown Preview Enhanced extension, Typora, Obsidian
- **GitHub Flavored Markdown spec**: https://github.github.com/gfm/
- **CommonMark spec**: https://spec.commonmark.org/
- **Markdown guide**: https://www.markdownguide.org/

---
**Tip:** Most Markdown renderers support HTML tags directly, so you can always fall back to HTML for features not supported by pure Markdown (e.g., `<div>`, `<span>`, `<u>` for underline).
