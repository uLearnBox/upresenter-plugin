# Equations and formulas

uPresenter has native formulas, rendered by KaTeX. A formula is an inline part of a text, next to
ordinary words, that the viewer sees as typeset math. Any mathematical expression in a deck must be
a formula: it looks like a textbook and stays sharp at any size. Text such as `x^2`, `x²`, `1/2` or
`sqrt(x)` typed as plain characters is not math to the app, and looks amateur.

Contents: 1 How to write one, 2 Where it works, 3 When to use it, 4 Syntax, 5 Layout and length,
6 Editing text that holds formulas, 7 Mistakes

## 1. How to write one

Put the LaTeX between `[[katex:` and `]]` inside the text you pass to a tool:

```json
{"tool": "setShapeText", "args": {"slideId": "3", "shapeId": "8", "text": "Expand [[katex:(a+b)^2]] first, then simplify."}}
```

The text around the marker stays plain. A text can hold several formulas, and a formula can be the
whole text. Backslashes follow JSON rules: write `\\frac` and `\\pi` inside a JSON string.

A derivation is one line per step, each line its own paragraph (a line break in the text starts a
new paragraph):

```json
{"text": "[[katex:99^2 = (100-1)^2]]\n[[katex:= 100^2 - 2 \\cdot 100 \\cdot 1 + 1^2]]\n[[katex:= 9801]]"}
```

## 2. Where it works

Formulas are understood in these tool arguments:

- `setShapeText` and `addText` (`text`): titles, subtitles, body text, list items, template slots;
- table cells (`setTableCellsText`, `setTableCellText`);
- question answers: choice answers (`answers[].text`), sequence `items`, matching `pairs`, and the
  `sentence` of fill-blanks, drag-words and multiple-dropdown questions. The question itself is
  the text of the slide's title (`setShapeText`).

They are **not** understood in dropdown options, accepted answers of text-entry questions, chart
labels, slide names and shape names. Those stay plain text: keep math out of them or write it in
words.

## 3. When to use a formula

Use one for every mathematical expression: equations and identities, fractions, powers and roots,
subscripts, Greek letters, inequalities, sums and integrals, geometry notation (angles, segments,
parallel), sets, probability, units with exponents, and chemical formulas written with `\mathrm`.

Do not use one for ordinary numbers in a sentence ("5 seconds", "9 801 students"), currency
("$48B"), percentages in prose, or years. A `$` is just a dollar sign.

Math slides read best with one idea and one or two large formulas: the formula is the headline of
the slide, with a short sentence in words around it.

## 4. Syntax (KaTeX)

| Need | Write |
|---|---|
| Fraction | `\frac{a}{b}` |
| Power, subscript | `x^{2}`, `x_{1}`, `a_n^2` |
| Root | `\sqrt{x}`, `\sqrt[3]{x}` |
| Multiply, divide, plus-minus | `\cdot`, `\times`, `\div`, `\pm` |
| Compare | `\le`, `\ge`, `\ne`, `\approx`, `\equiv` |
| Greek | `\pi`, `\alpha`, `\beta`, `\theta`, `\Delta` |
| Sum, integral, limit | `\sum_{i=1}^{n}`, `\int_a^b f(x)\,dx`, `\lim_{x \to 0}` |
| Geometry | `\angle ABC`, `\triangle ABC`, `\overline{AB}`, `AB \parallel CD`, `AB \perp CD` |
| Sets | `\in`, `\subset`, `\cup`, `\cap`, `\varnothing`, `\mathbb{R}` |
| System of equations | `\begin{cases} x + y = 5 \\ x - y = 1 \end{cases}` |
| Words inside a formula | `\text{if } x > 0` (needed for any word, in any language) |
| Thin space in numbers | `10\,000` |
| Chemistry | `\mathrm{H_2O}`, `\mathrm{CO_2}` |

The table shows plain LaTeX: inside a JSON argument every backslash is doubled (`\\frac`,
`\\\\` for a line break in `cases`). This is KaTeX, not full LaTeX: no packages, no `\usepackage`, no `\ce`. Keep a formula to what the
slide shows: no `$`, no `\(` `\)` delimiters (they would show literally), no Markdown.

## 5. Layout and length

- A formula takes the font size of its paragraph. To make a formula the focus of a slide, put it
  alone in its own text shape and raise the size with `setTextStyle`.
- `maxChars` of a slot counts the characters you write, LaTeX source included. A formula usually
  renders shorter than its source, so stay within the budget by keeping formulas short.
- A long equation does not wrap inside a formula. Split it into one step per line (section 1) or
  move it into a wider slot.

## 6. Editing text that holds formulas

`getPresentationState` shows a text's formulas inside `text.content` as their LaTeX source, and
lists them in `text.formulas`. `setShapeText` replaces the whole text, so when you rewrite a text
that contains formulas, wrap every formula in `[[katex:` `]]` again. Otherwise it turns into plain
text with the raw LaTeX showing.

After writing math, read the shape back: if `text.formulas` is missing or short, a marker was
malformed (an unfinished `[[katex:`) and the formula shows as plain text. Fix it and write again.

## 7. Mistakes that look wrong in the deck

- Unicode math (`x²`, `√`, `½`, `×` typed in words) or ASCII math (`x^2`, `sqrt(x)`, `1/2`): use a
  formula.
- Markers inside dropdown options, chart labels or slide names: they show literally.
- `$...$`, `\(...\)` or `$$...$$` instead of `[[katex:...]]`: they show literally.
- Forgetting `\text{}` for words inside a formula: the letters render as italic variables.
- Rewriting a text with `setShapeText` and dropping the markers of its formulas.
