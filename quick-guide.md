# Quick start

Here is a brief but complete overview of the Khi data format. Details may be found
in the reference.

## Document

A Khi document is either an expression, a dictionary or a table document. It is up
to the implementor to decide which is most suitable.

## Value

A *value* is a piece of information which corresponds to a real data structure or
piece of a data structure. Examples of data structures are strings, numbers, arrays,
dictionaries, tuples and structs. There are 7 kinds of values: Nil, text, dictionary,
table, compound, tuple and tagged value.

## Expression

An expression is a textual representation of a value.

## Nil

*Nil* represents an empty value. It corresponds to empty or null in other formats.
It is denoted by a tilde `~`.

## Text

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

## Dictionary

A dictionary organizes values by string keys. A key is represented by a word, transcription
or text block, and a value is represented by an expression.

There are two notations for dictionaries:
- **Delimited notation:**

  Entries are delimited by semicolons. For example:
  ```
  {name: Oak planks; price: 200}
  ```
- **Aligned notation:**

  Every entry is initiated by `>`. For example:
  ```
  {
    name: Oak planks
    price: 200
  }
  ```

It is recommended to use delimited notation for inline entries and aligned notation for
singleline or multiline entries.

<details>
<summary>Non-recommended styles</summary>
The following syntax is allowed, but it is recommended to use aligned notation:

```
{
  name: Oak planks;
  price: 200;
}
```

The following is allowed but not recommended, since it is hard to read:

```
{ name: Oak planks price: 200 }
```

</details>

## Table (replaced by list)

TODO: Remove, add list

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

## Compound

A compound is a sequence of values that have been composed together. There may be
spaces between values, or not. Compounds are used to represent markup.

`This is a <br>compound` is a compound consisting of a text term, followed
by a tag term, followed by a text term. There is space between the first two
terms, but not the two last.

## Tuple

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

## Tagged value

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

## Comment

A comment can be initiated with a hash `#` followed by another hash or whitespace. They
last until the end of the line. For example:
`# This is a comment!`
