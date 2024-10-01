# Khi

the **Khi** data language: a source format for data.

- defines a textual data format.
- has native support (both syntactic and semantic) for the universal data
  structures found in modern programming languages and formats:
  - text, markup
  - dictionaries, structs
  - lists
  - tuples
  - tables, matrices
  - enums (like structure varieties), commands (like LaTeX commands)
  - named trees and hierarchies (like XML tags/elements)
- focuses on data representation, markup and configuration, but could be used
  for storage and serialization.
- is intuitive to read, write and edit.
- is aesthetic and convenient for handcoding.
- is simple; There are no complex rules.
- has a balanced amount of verbosity and syntax noise.

## Links

- **Khi:**
  - [repository](https://github.com/khilang/khilang), [quick guide](https://github.com/khilang/khilang/blob/master/quick-guide.md), [reference](https://github.com/khilang/khilang/blob/master/reference.md)
  - [online editor & preprocessor](https://khilang.github.io/khi-editor)
  - [reference implementation](https://github.com/khilang/khi.rs)

- **Libraries:**
  - Rust: [khi.rs](https://github.com/khilang/khi.rs) (Reference implementation)
  - JavaScript:* [khi.js](https://github.com/khilang/khi.js) (`wasm-pack` derived reference implementation)

## Status

The language seems complete, but we will wait some time before finalizing it, in
case some currently unconsidered use cases need support. However, only small
changes and additions will be made at this point.

### TODO:

Stuff to do and think about, fix or add before **Khi 1.0.0**.

- [X] **Add absolute notation:** Much better than indentation and brackets for
  longer documents.
- [ ] **Evaluate text block flags:** There could be some important use cases
  that should have support. For example, maybe a flag to replace newlines by
  spaces.
- [ ] **Specify characters and lexer details:** Specification and description of
  ASCII/Unicode characters and lexer.
- [ ] Tidy up and finish documentation and reference.

## Examples & showcase

The following are some examples of **Khi** documents. It is shown how **Khi** supports
data structures native to configuration, markup and storage.

### Encyclopedia article example

This is an encyclopedia article example. It demonstrates that a **Khi** document can
contain both structured data, such as values, dictionaries and lists, and unstructured
data, such as markup.

Notes:
- Comments are opened by a hash `#`.
- This encyclopedia has macros. They are represented by Khi tags: `<macro>:arg:arg:arg`.
- The `<@>` macro inserts a link. It takes two arguments: the first argument is the
  article to link to, and the second is the link label that will appear in the article.
- Compare: The `title` entry has a text value, while the `description` entry has a compound value.
- This is a dictionary document, written in *absolute dictionary notation*.
  This notation sections a document with headers, similar to TOML/INI.
- Backslash `\ ` opens a transcription that lasts until the next `\ ` or the end of
  the line. A transcription parses reserved characters as text.

```
{article}:
uuid: 0c5aacfe-d828-43c7-a530-12a802af1df4
type: chemical-element
key: aluminium
title: Aluminium
description: The <@element>:{chemical element} aluminium.
tags: [metal; common]

{chemical-element}:
symbol: Al
number: 13
stp-phase: <Solid>
melting-point: 933.47
boiling-point: 2743
density: 2.7
electron-shells: [2; 8; 3]

{references}:
wikipedia: \https://en.wikipedia.org/wiki/Aluminium
snl: \https://snl.no/aluminium

{links}:
element: 740097ea-10fa-4203-b086-58632f099167
chemsym: 6e2f634c-f180-407a-b9ce-2138b412b248
atomnum: 1a5e1974-a78c-4820-afeb-79bef6974814
react: ab7d8a1f-c028-4466-9bb2-41a39d153241
aloxide: c1ff08e7-a88f-42d5-83c3-6adc4835a07b
stab: b3b13474-4fe3-4556-9568-925c066916a5
purity: 40786551-85c4-461c-ba6e-4d54d5863820
ion: effd5c7a-da31-4357-a94c-91343e9a05eb
metal: 84333088-cfcc-4e78-8d3f-7307dcab144b

[content]:

<h1>: Aluminium

<p>: <@self>:Aluminium is a <@element>:{chemical element} with
<@chemsym>:{chemical symbol} <chemsym> and <@atomnum>:{atomic number} <atomnum>.

<h2>: Substance

<p>: In <@purity>:pure form, it is a highly <@react>:reactive {<@metal>:metal},
but normally a thin coat of <@aloxide>:{aluminium oxide} forms on its surface,
keeping it highly {<@stab>:stable}.

<p>: ...

<h2>: Occurrence

<p>: In nature, it occurs as the <@ion>:ion <$>:{<sym>:{Al}^{3+}}. It
constitutes <$>:8.2% of the earth's crust, making it the most common
<@metal>:metal found there.

<p>: ...
```

### Khi-encoded HTML example

This is a markup example. It demonstrates that **Khi**, like **XML**/**HTML**, natively
supports named hierarchies, including text and tags with attributes. A preprocessor
could compile this document to **HTML**.

Notes:
- Here, we distinguish between HTML tags and HTML preprocessor macros. Both are
  represented by Khi tags, but a macro ends with `!`, while a regular HTML tag is
  alphabetic.
- `<doctype!>:html` compiles to `<!doctype html>`.
- `<#>` opens and closes a text block. It is used here to embed code.
- This is a value document.

```
<doctype!>:html # Macro that inserts <!doctype html>.
<html>:{
  <head>:{
    <title>:{Hello world!}
    <script src:script.js>:<> # <> denotes a default (in this case empty) element.
  }
  <body>:{
    <h1 id:main-heading>:{Hello world!}
    <p>:{Hello world!}
    <img src:frontpage.jpg>
    <div class:dark-background>:<p>:{
      This is a paragraph <br> with
      a line break. <em class:italic>:{
        This text is italic.
      }
    }
    <pre>:<code>:<raw!>:<#>
      def fib(n):
          if n == 0:
              return 0
          elif n == 1:
              return 1
          else:
              return fib(n - 1) + fib(n - 2)
    <#>
  }
}
```

### Khi-encoded LaTeX example

This is a markup example. It demonstrates that **Khi**, like **LaTeX**, natively supports
compositions of text and commands with arguments. A preprocessor could compile this
document to **LaTeX**.

Notes:
- Some macros are implemented to make it easier to use certain LaTeX commands.
  Here the `<def!>` macro is an interface to the LaTeX `\newcommand`.
- A LaTeX optional argument is indicated by square brackets `[`, `]`. Here, an
  initial optional argument is indicated with an apostrophe `'` at the end of
  the command name. Compare `<sqrt>` and `<sqrt'>`.
- Lists/tables are compiled to LaTeX tabulations (which are delimited by `&` and
  `\\`). For example, `[1|0; 0|1]` is compiled to **LaTeX** `1&0\\0&1\\`.
- This is a value document.

```
<documentclass>:article

<usepackage>:amsmath

# Use brackets around jot to prevent it from taking "1em" as argument.
<setlength>:{<jot>}:1em # Controls math line spacing

<begin>:document

A document containing some
equations and matrices.

<section>:Equations

# The def! macro makes it easier to use LaTeX \newcommand.
<def!>:Log:0:{ <operatorname>:Log }

<begin>:equation* <begin>:split

  <sqrt>:5 <times> <sqrt>:5 = 5
  <n>
  # A trailing apostrophe indicates that the first argument is optional.
  <sqrt'>:3:4 <times> <sqrt'>:3:16 = 4

<end>:split <end>:equation*

<begin>:equation* <Log>(1 + 2 + 3) = <Log>:1 + <Log>:2 + <Log>:3 <end>:equation*

<begin>:align* [
  > 0 + 1 + 2 + <dots> + 99 + 100
  > (0 + 100) + (1 + 99) + <dots> + (49 + 51) + 50
  > 5050
] <end>:align*

<begin>:align* [
  = 0 + 1 + 2 + <dots> + (n - 1) + n;
  = n <cfrac>:n:2 + <cfrac>:n:2;
  = <cfrac>:n^2:2 + <cfrac>:n:2;
  = n <cdot> <cfrac>:{n + 1}:2;
] <end>:align*

<section>:Matrices

<begin>:math
  <mathbf>:X = <begin>:bmatrix [
    |1|0|0|
    |0|1|0|
    |0|0|1|
  ] <end>:bmatrix
  =
  # ~ denotes an empty (nil) value.
  <begin>:bmatrix [1|~|~; ~|1|~; ~|~|1] <end>:bmatrix
<end>:math

<end>:document
```

### Material configuration example

This is a configuration example. It demonstrates that **Khi**, like **JSON** and **YAML**,
natively supports objects, arrays and scalar values.

Notes:
- **Khi** is not *syntax typed*, unlike **JSON** and **YAML**. JSON and YAML determine
  the types of data during parsing. For example, quotes indicate that a value is a
  string. Using Khi, programs determine the types of values themselves.
- This is a dictionary document, written in *aligned dictionary notation*.

```
oak-planks: {
  name: Oak planks
  tags: [wood]
  price: 200
}
birch-planks: {
  name: Birch planks
  tags: [wood]
  price: 200
}
stone: {
  name: Stone
  price: 100
  tags: [heavy; stone]
}
marble: {
  name: Marble
  price: 800
  beauty: 2
  tags: [heavy; stone; rich]
}
glass: {
  name: Glass
  price: 1600
  tags: [rich]
}
```

### Element table example

This is a data storage example. It demonstrates that **Khi**, like **CSV**, natively
supports tables of values.

Notes:
- This is a list document, written in *tabular list notation*. It contains
  tuples of equal size.

```
# 1) Atomic number
# 2) Chemical symbol
# 3) Element name
# 4) Chemical group
# 5) Phase (stp)
# 6) Electron shells

|  1 |  H |   Hydrogen |  1 |   <Gas> |           [1] |
|  2 | He |     Helium | 18 |   <Gas> |           [2] |
|  3 | Li |    Lithium |  1 | <Solid> |        [2; 1] |
|  4 | Be |  Beryllium |  2 | <Solid> |        [2; 2] |
|  5 |  B |      Boron | 13 | <Solid> |        [2; 3] |
|  6 |  C |     Carbon | 14 | <Solid> |        [2; 4] |
|  7 |  N |   Nitrogen | 15 |   <Gas> |        [2; 5] |
|  8 |  O |     Oxygen | 16 |   <Gas> |        [2; 6] |
|  9 |  F |   Fluorine | 17 |   <Gas> |        [2; 7] |
| 10 | Ne |       Neon | 18 |   <Gas> |        [2; 8] |
| 11 | Na |     Sodium |  1 | <Solid> |     [2; 8; 1] |
| 12 | Mg |  Magnesium |  2 | <Solid> |     [2; 8; 2] |
| 13 | Al |  Aluminium | 13 | <Solid> |     [2; 8; 3] |
| 14 | Si |    Silicon | 14 | <Solid> |     [2; 8; 4] |
| 15 |  P | Phosphorus | 15 | <Solid> |     [2; 8; 5] |
| 16 |  S |    Sulphur | 16 | <Solid> |     [2; 8; 6] |
| 17 | Cl |   Chlorine | 17 |   <Gas> |     [2; 8; 7] |
| 18 | Ar |      Argon | 18 |   <Gas> |     [2; 8; 8] |
| 19 |  K |  Potassium |  1 | <Solid> |  [2; 8; 8; 1] |
| 20 | Ca |    Calcium |  2 | <Solid> |  [2; 8; 8; 2] |
| 21 | Sc |   Scandium |  3 | <Solid> |  [2; 8; 9; 2] |
| 22 | Ti |   Titanium |  4 | <Solid> | [2; 8; 10; 2] |
| 23 |  V |   Vanadium |  5 | <Solid> | [2; 8; 11; 2] |
| 24 | Cr |   Chromium |  6 | <Solid> | [2; 8; 13; 1] |
```

### Words & phrases example

- This is a list document, written in *tagged list notation*.

```
<Verb>: clear {
  regularity: <Regular>
  transitivity: <Transitive>
  conjugation: <to> clear | cleared | cleared | clearing
  definitions: [
    > To empty the contents of.
    > To remove obstructions from.
    > To make transparent.
  ]
}
<Verb>: burn <down> {
  regularity: <Irregular>
  transitivity: <Transitive>
  conjugation:
  | <to> burn <down>
  | burnt <down>
  | burnt <down>
  | burning <down>
  definition: To burn completely.
}
<Noun>: firewood {
  countability: <Uncountable>
  declension: firewood | firewood
  definition: Wood burned to fuel a fire.
}
```
