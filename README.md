# Khi

- is a textual data format.
- has native support for the universal data structures found in modern programming
  languages and data formats like JSON, YAML, XML, CSV and LaTeX:
  - text and markup.
  - dictionaries.
  - tables, lists, tuples, matrices.
  - named hierarchies (like XML tags/elements).
  - functions, commands (like LaTeX commands).
- can be used for configuration, markup, storage and serialization.
- is intuitive to read, write and edit.
- is aesthetic and convenient for handcoding.
- is simple; There are no complex rules.
- is concise and has low syntax noise.

## Status

The language is complete, but we will wait some time before finalizing it, in case
some unknown future use cases needs support. However, only small changes and additions
will be made at this point, such as:

- Minor syntax changes.
- Small additions, such as more flags for text blocks.
- Elaboration on or changes to semantics.
- Clarifications, such as detailed specification of allowed and disallowed Unicode characters.

## Links

- [**Khi**](https://github.com/khilang/khilang)**:**
  - [reference](https://github.com/khilang/khilang/blob/master/reference.md)
  - [online editor & preprocessor](https://khilang.github.io/khi-editor)
- **Libraries:**
  - [khi.rs](https://github.com/khilang/khi.rs) (Rust)
  - [khi.js](https://github.com/khilang/khi.js) (JavaScript)

## A brief overview

Here is a brief but complete overview of the Khi data format. Details may be found
in the reference.

### Document

A Khi document is either an expression, a dictionary or a table document. It is up
to the implementor to decide which is most suitable.

### Value

A *value* is a piece of information which corresponds to a real data structure or
piece of a data structure. Examples of data structures are strings, numbers, arrays,
dictionaries, tuples and structs. There are 7 kinds of values: Nil, text, dictionary,
table, compound, tuple and tagged value.

### Expression

An expression is a textual representation of a value.

### Nil

*Nil* represents an empty value. It corresponds to empty or null in other formats.
It is denoted by a tilde `~`.

### Text

*Text* represents scalar or irreducible values like strings, numbers, booleans, dates,
etc. For example: `This is a string`, `300`, `1024.0`, `true`, `2023-Nov-12`.

A *transcription* is used to insert text that may contain reserved characters. It is
a sequence of characters initiated by a backslash `\ `, and it is closed by another
`\ ` or by the end of the line. For example: `\https://khilang.github.io/khi-editor/\`.

A *text block* is used to insert text that may contain reserved characters and which
may span multiple lines. A text block may be opened and closed by a pair of `<#>`
tags. It is very useful for including files or code. For example:
```
<#>
  def sum(a, b):
    return a + b
<#>
```

### Dictionary

A dictionary organizes values by string keys. A key is represented by a word, transcription
or text block, and a value is represented by an expression.

There are two notations for dictionaries:
- **Flow notation:**

  Entries are delimited by semicolons. For example:
  ```
  {name: Oak planks; price: 200}
  ```
- **Bullet notation:**

  Every entry is initiated by `>`. For example:
  ```
  {
    > name: Oak planks
    > price: 200
  }
  ```

It is recommended to use flow notation for inline entries and bullet notation for
singleline or multiline entries.

<details>
<summary>Non-recommended styles</summary>
The following syntax is allowed, but it is recommended to use bullet notation:

```
{
  name: Oak planks;
  price: 200;
}
```

The following is allowed, but looks bad:

```
{ > name: Oak planks > price: 200 }
```

</details>

### Table

A table organizes values by rows and columns. A table with one column is also known
as a list, and a table with one row is also known as a tuple.

There are three notations for tables:
- **Flow notation:**
  ```
  [1|0|0; 0|1|0; 0|0|1]
  ```
- **Grid notation:**
  ```
  [
    |1|~|~|
    |~|1|~|
    |~|~|1|
  ]
  ```
- **Bullet notation:**
  ```
  [
    > Northwest
    | Northeast
    > Southwest
    | Southeast
  ]
  ```

It is recommended to use flow notation for inline rows, grid notation for singleline
rows and bullet notation for multiline rows.

### Compound

A compound is a sequence of values that have been composed together. There may be
spaces between values, or not. Compounds are used to represent markup.

`This is a <br>compound` is a compound consisting of a text term, followed
by a tag term, followed by a text term. There is space between the first two
terms, but not the two last.

### Tuple

A tuple represents a data structure parameterized by zero, one or multiple elements.

Tuples are initiated by `<>`, and elements are specified by appending a colon `:`
followed by the value. For example: `<>:a:b:c` is a tuple with 3 values.

The empty tuple `<>` represents a trivial (default) value. **Note:** this is not the same
as Nil, the empty value.

Tuples with 1 element are always automatically unwrapped, unless they contain a nested
tuple. Thus, if `a` is not a tuple, `<>:a` is equivalent to `a`
itself.

In place of an expression, one can use a *tuple expression* to represent tuples and
tagged values. For example: `a & b & c` is an expression representing a tuple with
3 parameters.

### Tagged value

A tag is an identifier that is attached to a value. It can be used to identify a specific
value and parameterization.

For example:

- **Enums:**
  `<Red>`, `<Green>`, `<Blue>`, `<Rgb>:255:127:0`, `<Yellow>:127`. The first three
  are implicitly attached to an empty tuple. The fourth is attached to a tuple with
  3 elements, and the last is attached to a text value.
- **Actions:**
  `<set>:x:10`, `<load>:std.lib`
- **LaTeX-like commands:**
  `<frac>:1:2`, `<begin>:bmatrix`
- **XML-like tagged trees**
  `<div class:m1>:{Content}`. Tags can have attributes which configure them.

A tuple expression starting with a tag represents a tagged value. For example:
`<Tag>: a & b & c`.

### Comment

A comment can be initiated with a hash `#` followed by another hash or whitespace. They
last until the end of the line. For example:
`# This is a comment!`

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
- This is a dictionary document.
- Backslash `\ ` opens a transcription that lasts until the next `\ ` or the end of
  the line.

```
# Encyclopedia article about aluminium

> uuid: 0c5aacfe-d828-43c7-a530-12a802af1df4
> type: chemical-element
> key: aluminium
> title: Aluminium
> description: The <@>:element:{chemical element} aluminium.
> tags: [metal; common]

> chemical-symbol: Al
> atomic-number: 13
> stp-phase: <Solid>
> melting-point: 933.47
> boiling-point: 2743
> density: 2.7
> electron-shells: [2; 8; 3]

# External references
> ext-refs: {
  > wikipedia: \https://en.wikipedia.org/wiki/Aluminium
  > snl: \https://snl.no/aluminium
}

# Internal references
> refs: {
  > element: 740097ea-10fa-4203-b086-58632f099167
  > chemsym: 6e2f634c-f180-407a-b9ce-2138b412b248
  > atomnum: 1a5e1974-a78c-4820-afeb-79bef6974814
  > react: ab7d8a1f-c028-4466-9bb2-41a39d153241
  > aloxide: c1ff08e7-a88f-42d5-83c3-6adc4835a07b
  > stab: b3b13474-4fe3-4556-9568-925c066916a5
  > purity: 40786551-85c4-461c-ba6e-4d54d5863820
  > ion: effd5c7a-da31-4357-a94c-91343e9a05eb
  > metal: 84333088-cfcc-4e78-8d3f-7307dcab144b
}

> content: {

  <p> <@>:self:Aluminium is a <@>:element:{chemical element}
  with <@>:chemsym:{chemical symbol} <chemsym> and
  <@>:atomnum:{atomic number} <atomnum>.

  <p> In <@>:purity:pure form, it is a highly <@>:react:reactive
  <@>:metal:metal~, but normally a thin coat of
  <@>:aloxide:{aluminium oxide} forms on its surface, keeping it
  highly <@>:stab:stable~.

  <p> In nature, it occurs as the <@>:ion:ion <$>:{<Al>^{3+}}.
  It constitutes <$>:8.2% of the earth's crust, making it the
  most common <@>:metal:metal found there.

  ...

}
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
- `<#>` opens and closes a text block. We use it to embed code.
- This is an expression document.

```
# A frontpage

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
    <div class:dark-background>:&:<p>:{
      This is a paragraph <br> with a line break.
      <em class:italic>:{This text is italic.}
    }
    <pre>:&:<code>:&:<raw!>:<#>
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
- Here, we distinguish between regular LaTeX commands and preprocessor macros. Both
  are represented by Khi tags, but a macro ends with `!`.
- **Khi** does not natively support optional arguments. This is instead handled by
  ending a command name with an apostrophe.
- Tables are compiled to tabulation syntax. For example, `[1|0; 0|1]` is compiled
  to **LaTeX** `1&0\\0&1\\`.
- This is an expression document.

```
# Math equations

<documentclass>:article

<usepackage>:amsmath

<setlength>:<jot>:1em # Controls math line spacing

<begin>:document

A document containing some
equations and matrices.

<section>:Equations

  # Define a sum-range command.
  <def!>:<SumRn>:4:{ # def! is a macro that defines a LaTeX command.
    <sum>_{#1}^{`[#2::#3`]} #4
  }

  <def!>:<Log>:0:{ <operatorname>:Log }

  <begin>:equation* <begin>:split

    <sqrt>:5 <times> <sqrt>:5 = 5
    <n>
    # A trailing apostrophe indicates that the first argument is optional.
    <sqrt'>:3:4 <times> <sqrt'>:3:16 = 4

  <end>:split <end>:equation*

  <begin>:equation* <Log>(1 + 2 + 3) = <Log>:1 + <Log>:2 + <Log>:3 <end>:equation*

  <begin>:align* [
    | <SumRn>:k:0:100:k | = 0 + 1 + 2 + <dots> + 99 + 100                  |
    |                 ~ | = (0 + 100) + (1 + 99) + <dots> + (49 + 51) + 50 |
    |                 ~ | = 5050                                           |
  ] <end>:align*

  <begin>:align* [
    | <SumRn>:k:0:n:k                    |
    | = 0 + 1 + 2 + <dots> + (n - 1) + n |
    | = n <cfrac>:n:2 + <cfrac>:n:2      |
    | = <cfrac>:n^2:2 + <cfrac>:n:2      |
    | = n <cdot> <cfrac>:{n + 1}:2       |
  ] <end>:align*

<section>:Matrices

  <begin>:math
    <mathbf>:X = <begin>:bmatrix [
      |1|0|0|
      |0|1|0|
      |0|0|1|
    ] <end>:bmatrix
    =
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
- This is a dictionary document.

```
# Materials

> oak-planks: {
  > name: Oak planks
  > tags: [wood]
  > price: 200
}
> birch-planks: {
  > name: Birch planks
  > tags: [wood]
  > price: 200
}
> stone: {
  > name: Stone
  > price: 100
  > tags: [heavy; stone]
}
> marble: {
  > name: Marble
  > price: 800
  > beauty: 2
  > tags: [heavy; stone; rich]
}
> glass: {
  > name: Glass
  > price: 1600
  > tags: [rich]
}
```

### Element table example

This is a data storage example. It demonstrates that **Khi**, like **CSV**, natively
supports tables of values.

Notes:
 - This is a table document.

```
# Chemical elements

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
