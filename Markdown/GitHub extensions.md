# GitHub Flavored Markdown (GFM) Extensions

GitHub extends standard Markdown with additional features called **GitHub Flavored Markdown (GFM)**. This guide covers all the special syntax and features available when writing on GitHub (README files, issues, pull requests, discussions, comments, etc.).

---

## 1) Tables

Create structured data using pipe (`|`) delimiters and dashes (`-`).

```markdown
| Header 1 | Header 2 | Header 3 |
|----------|----------|----------|
| Cell 1   | Cell 2   | Cell 3   |
| Cell 4   | Cell 5   | Cell 6   |
```

**Alignment**:
- Left: `:---`
- Center: `:---:`
- Right: `---:`

```markdown
| Left   | Center | Right |
|:-------|:------:|------:|
| A      |   B    |     C |
```

---

## 2) Task lists

Create interactive checkboxes in issues, PRs, and discussions.

```markdown
- [x] Completed task
- [ ] Incomplete task
- [ ] Another task
```

When viewing on GitHub, you can click checkboxes to toggle their state. The Markdown updates automatically.

---

## 3) Strikethrough

Use `~~` to strike through text.

```markdown
~~This is strikethrough text~~
```

Rendered as: ~~This is strikethrough text~~

---

## 4) Autolinks

URLs and email addresses are automatically converted to clickable links.

```markdown
https://github.com
user@example.com
```

You can also use angle brackets to force linking:

```markdown
<https://github.com>
<user@example.com>
```

---

## 5) @ mentions

Reference users, teams, or organizations to notify them.

```markdown
@username
@organization/team
```

Typing `@` in an issue, PR, or comment shows an autocomplete dropdown.

---

## 6) # issue and PR references

Link to issues and pull requests by number.

```markdown
#123          (issue or PR in same repo)
repo#123      (issue or PR in same org)
user/repo#123 (issue or PR in any repo)
GH-123        (alternative syntax)
```

---

## 7) Emoji

Use `:emoji-name:` shortcodes to add emoji.

Common examples:

```markdown
:tada: :rocket: :bug: :wave: :fire: :+1: :-1:
:smile: :heart: :star: :warning: :lock:
```

Full emoji list: https://github.com/ikatyang/emoji-cheat-sheet

---

## 8) Code block syntax highlighting

Specify a language after the opening triple backticks for syntax highlighting.

````markdown
```javascript
const greeting = "Hello, World!";
console.log(greeting);
```

```python
def greet():
    print("Hello, World!")
```

```powershell
Write-Host "Hello, World!"
```

```bash
echo "Hello, World!"
```

```json
{
  "name": "example",
  "version": "1.0.0"
}
```
````

**Supported languages**: JavaScript, Python, Java, C++, C#, Ruby, Go, Rust, PHP, SQL, HTML, CSS, Bash, PowerShell, YAML, JSON, XML, and many others.

---

## 9) Diff highlighting in code blocks

Show code changes with `diff` syntax highlighting.

````markdown
```diff
- old line
+ new line
  unchanged line
```
````

Lines starting with `-` are red (removed), `+` are green (added).

---

## 10) Disabling code block rendering

Use `text` or `plaintext` to show code without highlighting:

````markdown
```text
This won't be syntax highlighted
Just plain text
```
````

---

## 11) Math expressions (LaTeX)

GitHub supports math using `$` for inline and `$$` for blocks (requires "Markdown math" to be enabled in repo settings).

```markdown
Inline math: $E = mc^2$

Block math:
$$
\frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
$$
```

---

## 12) Footnotes

Create footnote references and definitions.

```markdown
This is a statement[^1].

[^1]: This is the footnote content.
```

The footnote appears at the bottom of the rendered page.

---

## 13) Collapse sections (details/summary)

Hide long content behind a collapsible section.

```markdown
<details>
<summary>Click to expand</summary>

This content is hidden until clicked.

```python
print("Hidden code example")
```

</details>
```

---

## 14) Line highlighting in code blocks

Highlight specific lines (some contexts like GitHub gists support line numbers and ranges).

In a code fence, you can link to specific lines:
- Direct URL: `https://github.com/owner/repo/blob/main/file.js#L10`
- Range: `https://github.com/owner/repo/blob/main/file.js#L10-L20`
- Shift-click to select multiple lines

---

## 15) Mentions in code

Use backticks to escape mentions so they don't notify:

```markdown
`@username` — won't notify the user
@username — will notify
```

---

## 16) Alert boxes (callouts)

Use blockquotes with special markers to create styled alert boxes.

```markdown
> [!NOTE]
> This is a note.

> [!WARNING]
> This is a warning.

> [!IMPORTANT]
> This is important.

> [!TIP]
> This is a tip.

> [!CAUTION]
> This is a caution.
```

These render with distinctive colors and icons on GitHub.

---

## 17) HTML comments

Hide content from rendering.

```markdown
<!-- This is a comment and won't be visible -->
```

---

## 18) Image sizing

Specify image dimensions in HTML.

```markdown
![Alt text](image.png)

<img src="image.png" width="200" height="100" alt="Alt text" />
```

---

## 19) Videos and embeds

While not pure Markdown, you can embed videos and media using HTML:

```markdown
<video src="video.mp4" width="320" height="240" controls></video>
```

Or link to video platforms (YouTube embeds may not work directly, but a link will show preview).

---

## 20) GFM-specific best practices

✅ **Do:**
- Use task lists for tracking progress in issues.
- Reference issues/PRs to create connected context.
- Use tables for structured data (comparison, specs).
- Enable math rendering for technical documentation.
- Escape `@` mentions if they're not meant to notify.
- Use alert boxes (`> [!NOTE]`, etc.) for important information.

❌ **Don't:**
- Rely on HTML/CSS that GitHub filters out (for security).
- Assume all GFM features are enabled (some repos may have settings disabled).
- Use unsupported HTML tags; stick to safe subset.

---

## 21) Compatibility and limitations

- GFM features work on GitHub.com, GitHub Enterprise, and GitHub's markdown rendering.
- Not all GFM features work in other Markdown renderers (e.g., VS Code, local Markdown viewers).
- Math requires explicit opt-in on some repos.
- Some features (like `> [!NOTE]`) are newer and may not work in older GitHub versions.

---

## 22) Quick reference table

| Feature | Syntax | Example |
|---------|--------|---------|
| Table | `\| col1 \| col2 \|` | See section 1 |
| Task | `- [ ] task` | See section 2 |
| Strikethrough | `~~text~~` | See section 3 |
| Mention | `@user` | See section 5 |
| Issue ref | `#123` | See section 6 |
| Emoji | `:tada:` | See section 7 |
| Code block | ` ```lang ` | See section 8 |
| Diff | ` ```diff ` | See section 9 |
| Math | `$equation$` | See section 11 |
| Footnote | `[^1]` | See section 12 |
| Collapse | `<details>` | See section 13 |
| Alert | `> [!NOTE]` | See section 16 |

---

## 23) Resources

- GitHub Flavored Markdown spec: https://github.github.com/gfm/
- GitHub markdown guide: https://guides.github.com/features/mastering-markdown/
- GitHub help: https://docs.github.com/en/get-started/writing-on-github
- Emoji cheat sheet: https://github.com/ikatyang/emoji-cheat-sheet

---

**Pro tip**: Use GFM features liberally in README files, issues, and PRs to make your repositories more readable, organized, and interactive.