# Using LaTeX in Markdown on GitHub

GitHub supports mathematical expressions written in **LaTeX** syntax. This guide explains how to write inline and block math equations in GitHub-flavored Markdown (README files, issues, pull requests, discussions, and wiki pages).

---

## Prerequisites

LaTeX math rendering requires:
- GitHub.com (or GitHub Enterprise with math rendering enabled)
- Your repository must have math rendering enabled (default on most repos)
- Use either `$...$` (inline) or `$$...$$` (display/block) delimiters

---

## 1) Inline math

Wrap LaTeX code in single `$` delimiters to render math inline within text.

```markdown
The equation $E = mc^2$ shows the relationship between energy and mass.

The solution to $ax + b = 0$ is $x = -\frac{b}{a}$.
```

**Rendered:**
- The equation $E = mc^2$ shows...
- The solution to $ax + b = 0$ is $x = -\frac{b}{a}$.

---

## 2) Display (block) math

Wrap LaTeX code in double `$$` delimiters (on separate lines) to render math as a centered block.

```markdown
$$
E = mc^2
$$

The quadratic formula is:

$$
x = \frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
$$
```

---

## 3) Greek letters

Use backslash followed by letter name.

```markdown
$\alpha, \beta, \gamma, \delta, \epsilon, \zeta, \eta, \theta$

$\iota, \kappa, \lambda, \mu, \nu, \xi, \pi, \rho, \sigma, \tau$

$\phi, \chi, \psi, \omega$

Capital: $\Gamma, \Delta, \Theta, \Lambda, \Sigma, \Phi, \Psi, \Omega$
```

---

## 4) Fractions

Use `\frac{numerator}{denominator}`.

```markdown
$\frac{1}{2}$

$\frac{x^2 + 1}{x - 1}$

$$
\frac{-b \pm \sqrt{b^2 - 4ac}}{2a}
$$
```

---

## 5) Superscripts and subscripts

- Superscript: `x^2`, `e^{i\pi}`
- Subscript: `x_1`, `a_{n+1}`

```markdown
$x^2$ and $x_2$

$e^{i\pi} + 1 = 0$

$a_n = \frac{1}{n^2}$
```

---

## 6) Square roots and radicals

Use `\sqrt{x}` or `\sqrt[n]{x}` for nth root.

```markdown
$\sqrt{2}$

$\sqrt[3]{8} = 2$

$\sqrt{x^2 + y^2}$
```

---

## 7) Summation, product, integral

- Sum: `\sum`
- Product: `\prod`
- Integral: `\int`
- Limits: `_{lower}^{upper}`

```markdown
$\sum_{i=1}^{n} i = \frac{n(n+1)}{2}$

$\prod_{i=1}^{n} x_i$

$$
\int_{0}^{\infty} e^{-x^2} dx = \frac{\sqrt{\pi}}{2}
$$
```

---

## 8) Operators and symbols

Common mathematical operators:

```markdown
$+$, $-$, $\times$, $\div$, $=$

$<$, $>$, $\leq$, $\geq$, $\neq$

$\pm$, $\mp$

$\approx$, $\cong$, $\propto$, $\sim$

$\infty$, $\partial$, $\nabla$, $\forall$, $\exists$
```

---

## 9) Matrices

Use `\begin{matrix}...\end{matrix}` or variants for different bracket styles.

```markdown
$$
\begin{matrix}
a & b \\
c & d
\end{matrix}
$$

$$
\begin{pmatrix}
a & b \\
c & d
\end{pmatrix}
$$

$$
\begin{bmatrix}
a & b \\
c & d
\end{bmatrix}
$$

$$
\begin{Bmatrix}
a & b \\
c & d
\end{Bmatrix}
$$

$$
\begin{vmatrix}
a & b \\
c & d
\end{vmatrix}
$$
```

Matrix types:
- `matrix` — no brackets
- `pmatrix` — round parentheses ()
- `bmatrix` — square brackets []
- `Bmatrix` — curly braces {}
- `vmatrix` — vertical bars ||

---

## 10) Systems of equations

Use `\begin{cases}...\end{cases}`.

```markdown
$$
\begin{cases}
x + y = 5 \\
2x - y = 4
\end{cases}
$$
```

---

## 11) Aligned multi-line equations

Use `\begin{align}...\end{align}` or `\begin{align*}...\end{align*}` (no numbering).

```markdown
$$
\begin{align}
y &= mx + b \\
0 &= mx + b - y \\
y - b &= mx
\end{align}
$$

$$
\begin{align*}
(a + b)^2 &= a^2 + 2ab + b^2 \\
&= a^2 + ab + ab + b^2
\end{align*}
$$
```

Align with `&` at the position you want lines to line up.

---

## 12) Fractions in display mode

Use `\dfrac` for display-size fractions (larger).

```markdown
$$
\frac{1}{2} \quad \text{vs} \quad \dfrac{1}{2}
$$
```

---

## 13) Trigonometric and logarithmic functions

Use operators like `\sin`, `\cos`, `\log`, `\ln` for proper formatting.

```markdown
$\sin(x)$, $\cos(x)$, $\tan(x)$

$\log(x)$, $\ln(x)$, $\log_2(x)$

$\exp(x)$
```

---

## 14) Absolute value, norms, and brackets

```markdown
$|x|$ (absolute value)

$\|v\|$ (norm)

$\lfloor x \rfloor$ (floor)

$\lceil x \rceil$ (ceiling)

$\left( expression \right)$ (auto-sized brackets)
```

---

## 15) Derivatives and limits

```markdown
$\frac{df}{dx}$ or $f'(x)$

$\frac{\partial f}{\partial x}$ (partial derivative)

$\lim_{x \to 0} \frac{\sin(x)}{x} = 1$
```

---

## 16) Common mathematical expressions

**Pythagorean theorem:**
```markdown
$$a^2 + b^2 = c^2$$
```

**Euler's identity:**
```markdown
$$e^{i\pi} + 1 = 0$$
```

**Binomial coefficient:**
```markdown
$$\binom{n}{k} = \frac{n!}{k!(n-k)!}$$
```

**Variance:**
```markdown
$$\text{Var}(X) = E[X^2] - (E[X])^2$$
```

**Normal distribution:**
```markdown
$$f(x) = \frac{1}{\sigma\sqrt{2\pi}} e^{-\frac{(x-\mu)^2}{2\sigma^2}}$$
```

---

## 17) Text in equations

Use `\text{...}` to add regular text inside math mode.

```markdown
$$
\text{distance} = \sqrt{(x_2 - x_1)^2 + (y_2 - y_1)^2}
$$
```

---

## 18) Troubleshooting

- **Math not rendering**: Ensure you're using `$...$` or `$$...$$` delimiters (not backticks).
- **Escaping `$`**: If you need a literal `$` in text, escape it: `\$`.
- **Line breaks**: Use `\\` inside display math for line breaks.
- **Alignment issues**: Ensure `&` is placed correctly in `align` environments.
- **Special characters**: Some symbols may not render; consult LaTeX documentation for alternatives.

---

## 19) Limitations

- GitHub LaTeX rendering uses a subset of LaTeX (via MathJax); not all LaTeX packages are supported.
- Macros and custom commands are not available.
- Some advanced features (e.g., TikZ diagrams) won't work.
- Rendering requires JavaScript enabled in your browser.

---

## 20) Examples in context

**README example:**

````markdown
# Physics Formula Sheet

## Kinematics
$$v = v_0 + at$$
$$x = x_0 + v_0 t + \frac{1}{2}at^2$$

## Energy
The kinetic energy is given by $KE = \frac{1}{2}mv^2$.

## Thermodynamics
The first law of thermodynamics:
$$dU = \delta Q - \delta W$$
````

**Issue example:**

````markdown
## Bug Report: Calculation Error

The formula $y = mx + b$ should compute to $y = 2(5) + 3 = 13$, but we get 15.

Matrix representation:
$$
\begin{bmatrix}
x \\
y
\end{bmatrix}
= \begin{bmatrix}
\cos\theta & -\sin\theta \\
\sin\theta & \cos\theta
\end{bmatrix}
\begin{bmatrix}
x' \\
y'
\end{bmatrix}
$$
````

---

## 21) Resources

- LaTeX math symbols: https://www.ctan.org/pkg/comprehensive
- MathJax documentation: https://docs.mathjax.org/
- GitHub Markdown math: https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/writing-mathematical-expressions
- Overleaf LaTeX tutorial: https://www.overleaf.com/learn

---

**Pro tip**: Use online LaTeX editors (Overleaf, Detexify) to test complex equations before adding them to GitHub. Copy the LaTeX code directly into your Markdown.