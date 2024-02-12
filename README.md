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

## Links

- [**Khi**](https://github.com/khilang/khilang)**:**
  - [reference](https://github.com/khilang/khilang/blob/master/reference.md)
  - [online editor & preprocessor](https://khilang.github.io/khi-editor)
- **Libraries:**
  - [khi.rs](https://github.com/khilang/khi.rs) (Rust)
  - [khi.js](https://github.com/khilang/khi.js) (JavaScript)

## A brief overview

### Document

A Khi document contains either an expression(a value), a dictionary or a table. It
is up to the programmer to decide which root element is most suitable. Possible values,
dictionaries and tables will be explained further below.

### Nil

Nil represents an empty or default value. It corresponds to other formats' null value.
A nil value is written `~`.

### Text

Text represents scalar or irreducible values like strings, numbers, booleans, dates etc. Simply
write `This is a string`, `300`, `1024.0`, `true`.
If the text contains reserved characters, you can optionally use quotes:
`"https://khilang.github.io/khi-editor/"`.

### Dictionary

A dictionary organizes values by string keys.

There are two notations for dictionaries:
- **Flow notation:**
  ```
  {name: Oak planks; price: 200}
  ```
- **Bullet notation:**
  ```
  {
    > name: Oak planks
    > price: 200
  }
  ```

It is recommended to use flow notation for inline entries and bullet notation for
multiline entries.

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
{>name: Oak planks >price: 200}
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

### Tuple

TODO: Write

A tuple is a parameterization of a data structure.

Tuples can be used to specify a data structure consisting of an arbitrary number of
components. It is recommended to only use tuples for obvious/well documented cases.
Complex structures should use a dictionary.

Tuples with 1 component are automatically unwrapped. Thus, `<>:a` will always be
unwrapped to `a`. (Unless `a` is a tuple, in which case the tuples become nested).

`<>:a:b:c:d` is a tuple with 4 parameters.

A tuple expression: `a :: b :: c` is an expression representing a tuple with 3 parameters.

### Tag / Tagged value

A tag is an identifier which can be attached to another value, or not. For example:

- **Enums:**
  `<Red>`, `<Blue>`, `<Green>`, `<Rgb>:255:127:0`. The first three are attached to
  an empty tuple, because they only have 1 value. The last is attached to a tuple with
  3 values.
- **Actions:**
  `<set>:x:10`, `<load>:std.lib`
- **LaTeX-like commands:**
  `<frac>:1:2`, `<begin>:bmatrix`
- **XML-like tagged trees**
  `<div class:m1>:{Content}` - patterns can have attributes which configure them.

### Composition

Whenever a value is expected, you can use one of the constructs above: nil, text,
dictionary, table, tuple and pattern. But you are also allowed to compose them. This is how
Khi supports markup; Markup consists of such textual compositions.

`This is a <br> composition` is a composition consisting of a text term, followed
by a pattern term, followed by a text term.

### Other features

These features have not been explained here:

- Text blocks: another way to represent text. Can be used to insert code or other
  files.
- Constructor notation
- Comments

## Examples & showcase

The following are some examples of **Khi** documents. It is shown how **Khi** supports
data structures native to configuration, markup and storage.

### Encyclopedia article example

This is an encyclopedia article example. It demonstrates that a **Khi** document can
contain both structured data, such as values, dictionaries and lists, and unstructured
data, such as markup.

Notes:
- Comments are opened by a hash `#`.
- This encyclopedia has macros. They are represented by Khi patterns: `<macro>:arg:arg:arg`.
- The `<@>` macro inserts a link. It takes two arguments: the first argument is the
  article to link to, and the second is the link label that will appear in the article.
- Compare: The `title` entry has a text value, while the `decsription` entry has a composition value.
- This is a dictionary document.

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
> stp-phase: solid
> melting-point: 933.47
> boiling-point: 2743
> density: 2.7
> electron-shells: [2; 8; 3]

# External references
> ext-refs: {
  > wikipedia: "https://en.wikipedia.org/wiki/Aluminium"
  > snl: "https://snl.no/aluminium"
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

  <p> <@>:self:Aluminium is a <@>:element:{chemical element} with <@>:chemsym:{chemical symbol}
  <chemsym> and <@>:atomnum:{atomic number} <atomnum>.

  <p> In <@>:purity:pure form, it is a highly <@>:react:reactive <@>:metal:metal~,
  but normally a thin coat of <@>:aloxide:{aluminium oxide} forms on its surface,
  keeping it highly <@>:stab:stable~.

  <p> In nature, it occurs as the <@>:ion:ion <$>:{<Al>^{3+}}. It constitutes <$>:8.2%
  of the earth's crust, making it the most common <@>:metal:metal found there.

  ...

}
```

### Khi-encoded HTML example

This is a markup example. It demonstrates that **Khi**, like **XML**/**HTML**, natively
supports named hierarchies, including text and tags with attributes. A preprocessor
could compile this document to **HTML**.

Notes:
- Here, we distinguish between HTML tags and HTML preprocessor macros. Both are
  represented by Khi patterns, but a macro ends with `!`, while a regular HTML tag is
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
    <script src:script.js>:{~} # {~} denotes an empty element.
  }
  <body>:{
    <h1 id:main-heading>:{Hello world!}
    <p>:{Hello world!}
    <img src:frontpage.jpg>
    <div class:dark-background>:<>:<p>:{
      This is a paragraph <br>
      with a line break.
      <em class:italic>:{This text is italic.}
    }
    <pre>:<>:<code>:<>:<raw!>:<#>
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
  are represented by Khi patterns, but a macro ends with `!`.
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
    <sum>_{#1}^{`[#2`:#3`]} #4
  }

  <def!>:<Log>:0:<>:<operatorname>:Log

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
