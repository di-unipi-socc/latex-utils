# Paper utilities

LaTeX utilities for papers, maintained by Jacopo Massa. Copy one of the files
below next to your main `.tex` file and rename it `paper-utils.sty`.

| Variant | Includes |
| --- | --- |
| [Light](packages/paper-utils-light.sty) | Configured biblatex, siunitx and cleveref; Inconsolata; code environments; `\cd`, `\verbcomment`, `\fncd`, `\pptext`, `\diam`, `\ie`, `\eg` |
| [Full](packages/paper-utils-full.sty) | Everything in light, plus theorem environments, `\framedbox`, changes and todonotes |

Both declare the same package name. To switch variants, replace `paper-utils.sty`
and keep `\usepackage{paper-utils}` unchanged. Load only one variant per document.

```latex
\documentclass{article}
\usepackage{paper-utils}
\addbibresource{references.bib}

\begin{document}
\cd{hello}: \SI{10}{\metre}.
\printbibliography
\end{document}
```

## Package options

Pass options in the optional argument of `\usepackage`:

```latex
\usepackage[
  language=british,
  minted=true,
  gobble=4,
  tabsize=4
]{paper-utils}
```

| Option | Default | Available in | Purpose |
| --- | --- | --- | --- |
| `language` | `english` | Both | Language loaded by babel |
| `verbatim` | `true` | Both | Enable plain code environments through fvextra |
| `minted` | `false` | Both | Enable highlighted Prolog environments; overrides verbatim |
| `gobble` | `0` | Both | Number of leading characters removed from each code line |
| `tabsize` | `4` | Both | Display width of tabs in code |
| `box-border` | `black` | Full only | Border colour for `\framedbox` |
| `box-background` | `white` | Full only | Background colour for `\framedbox` |

### `language`

Use a babel language name, such as `english`, `british` or `italian`.
The package loads babel after biblatex. This controls language-dependent behaviour
such as hyphenation, automatic headings and bibliography strings; it does not
translate your text.

```latex
\usepackage[language=italian]{paper-utils}
```

Omitting the option loads `english`. An empty value is not a switch to disable
babel. Avoid loading babel separately with different options; if your document
class already loads it, keep the language settings consistent.

### `verbatim` and `minted`

Both accept `true` or `false`. They select the implementation of `code` and
`codenum`, which display Prolog snippets without and with line numbers.
The verbatim implementation prints plain text; minted adds syntax highlighting.

| `verbatim` | `minted` | Result |
| --- | --- | --- |
| `true` | `false` | Plain code (default) |
| `false` | `false` | Neither code environment is defined |
| `true` | `true` | Minted |
| `false` | `true` | Minted |

`minted=true` wins regardless of option order, so it is enough to select minted.
To disable both, use `verbatim=false` and leave minted at its default.

Minted requires its external tools and the shell-escape configuration appropriate
to your TeX distribution. With minted 2, install Pygments and compile with
`-shell-escape`. To pass options to a single block across minted 2 and 3, use the
starred form, for example `\begin{codenum*}{firstnumber=1}`. With verbatim, use
`\begin{codenum}[firstnumber=1]`.

### `gobble`

Set the number of characters to remove from the start of every code line.
The default, `0`, preserves the input. Use `4` when each line has four extra
leading spaces, or `1` for one leading tab. This removes indentation added to
align code within the LaTeX source; indentation beyond that prefix remains.

```latex
\usepackage[gobble=4]{paper-utils}
% ...
\begin{code}
    application(AppId, [Functions], [Services]).
\end{code}
```

The four spaces before `application` are removed. Gobble counts characters,
not indentation levels: it can remove code if a line has less indentation than
expected. Use a non-negative integer supported by the selected backend.
The setting applies to both `code` and `codenum`, with either backend.

### `tabsize`

Set a positive integer for the display width of tabs, measured in character
spaces. The default is `4`. This changes how remaining tabs are displayed;
it does not remove indentation or change ordinary spaces.

```latex
\usepackage[gobble=1,tabsize=4]{paper-utils}
```

For input with one extra leading tab, this removes that tab and displays
remaining tabs with width four. Like `gobble`, the setting applies to both code
environments and both backends. Neither option affects inline `\verb` or `\texttt`.

### `box-border` and `box-background` (full only)

These options set the two colours passed to `\fcolorbox`: border first,
background second. Defaults are `black` and `white`. Set either independently;
there is no automatic tint, and the text colour is unchanged.

```latex
\usepackage[
  box-border=babyblueeyes,
  box-background=aliceblue
]{paper-utils}
\definecolor{babyblueeyes}{HTML}{A1CAF1}
\definecolor{aliceblue}{HTML}{F0F8FF}
% ...
\framedbox{A definition or a short explanation.}
```

This uses the same colours as `\fcolorbox{babyblueeyes}{aliceblue}`.
Values accept xcolor colour names or expressions such as `blue!60!black`.
Custom names must be defined before the first `\framedbox`, as above.
Both options affect every framed box. The light variant defines neither
these options nor `\framedbox`. These replace the former `box-color` option.

## Fonts, bibliography and editing

With XeLaTeX or LuaLaTeX, the package selects Inconsolata through fontspec.
With pdfLaTeX, it loads inconsolata. Install the Inconsolata fonts in your TeX
distribution. Document layout and the main text font remain your choice.

The bibliography uses Biber with the IEEE bibliography style and compact numeric
citations. Set your bibliography file with `\addbibresource` and print it with
`\printbibliography`. Load paper-utils after other packages that must precede
cleveref.

The full variant defines `definition`, `lemma`, `theorem`, `proposition` and
`corollary`, sharing a counter that resets in each section. Do not redefine these
environments in your document.

For notes, use `\todo{Note}`, `\todo[inline]{Note}` and `\listoftodos`.
For revisions, use changes commands and define authors with `\definechangesauthor`.
Options for these dependencies must be passed before loading paper-utils:

```latex
\PassOptionsToPackage{final}{changes}     % Accept revisions in the output
\PassOptionsToPackage{disable}{todonotes} % Hide notes
\usepackage{paper-utils}                 % Full variant
```

## Examples

Each file in `examples/` is a separate document:

| Example | Covers | Variant |
| --- | --- | --- |
| [biblatex](examples/biblatex.tex) | Citations and bibliography | Either |
| [commands](examples/commands.tex) | Commands, units, references, theorems and framed text | Full |
| [changes](examples/changes.tex) | Additions, deletions, replacements and comments | Full |
| [todo-notes](examples/todo-notes.tex) | Margin notes, inline notes and a list of todos | Full |
| [verbatim](examples/verbatim.tex) | Plain code and numbered lines | Either |
| [minted](examples/minted.tex) | Highlighted Prolog code | Either |

`examples/paper-utils.sty` links to the full variant, so changes to that package
apply to the examples immediately. If your checkout does not preserve symlinks,
copy `packages/paper-utils-full.sty` to `examples/paper-utils.sty` instead.
Compile from `examples/`, for example with XeLaTeX:

```sh
cd examples
latexmk -xelatex commands.tex
latexmk -xelatex biblatex.tex
```

Latexmk runs Biber when the bibliography needs updating. For a manual build of
`biblatex.tex`, run XeLaTeX, `biber biblatex`, then XeLaTeX twice. Use `latexmk -pdf`
for pdfLaTeX or `latexmk -lualatex` for LuaLaTeX. The minted example also needs the
external tools described above. Both code examples use `gobble=4,tabsize=4`.

## Contact

For questions or suggestions, contact the maintainer: [Jacopo Massa](https://pages.di.unipi.it/massa).
