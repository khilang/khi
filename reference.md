# Reference

This document describes the syntax and semantics of the **Khi** data format.

## Overview

1. [Document](#document) - A string, file, stream, etc. corresponding to a data structure.
2. [Structure](#structure) - A value corresponding to a data structure
   1. [Nil](#nil)
   2. [Text](#text)
   3. [Dictionary](#dictionary)
   4. [Table](#table)
   5. [Composition](#composition)
   6. [Tuple](#tuple)
   7. [Pattern](#pattern)
3. [Expression](#expression) - A textual representation of a data structure
4. [String](#string) - A textual representation of a string
   1. [Word](#word)
   2. [Transcription](#quotation)
   3. [Text block](#text-block)
5. [Whitespace](#whitespace)
6. [Character](#character)
   1. [Whitespace character](#whitespace-character)
   2. [Ignored character](#ignored-character)
   3. [Reserved character](#reserved-character)
   4. [Escape sequence](#character-escape-sequence)
7. [Comment](#comment)

## Document

A *document* is a text file, string, stream, etc. conforming to either an [expression](#expression),
a [dictionary](#dictionary) or a [table](#table). It represents a [structure](#structure),
[dictionary](#dictionary) or a [table](#table) respectively. A blank document represents
[nil](#nil), an empty [dictionary](#dictionary) or an empty [table](#table).

## Structure

A *structure*, also referred to as a *value*, corresponds to a real data structure,
such as a string, number, date, dictionary, table, tuple, list and markup. A structure
is the result of parsing a [document](#document), [expression](#expression),
[term](#expression) or [argument](#pattern). There are seven *structure variants*: [nil](#nil),
[text](#text), [dictionary](#dictionary), [table](#table), [composition](#composition), tuple
and [pattern](#pattern).

| Structure                   | Corresponds to                                        | Examples                                                                    |
|-----------------------------|-------------------------------------------------------|-----------------------------------------------------------------------------|
| [Nil](#nil)                 | An empty or null data structure.                      | Empty value, null, empty markup                                             |
| [Text](#text)               | A scalar or irreducible data structure.               | String, number, boolean, date, markup text                                  |
| [Dictionary](#dictionary)   | A collection of data structures organized by keys.    | Dictionary, object, struct, configuration                                   |
| [Table](#table)             | A collection of data structures organized by indices. | Table, list, set, array, matrix, markup tabulation                          |
| [Composition](#composition) | A textual composition of data structures.             | Markup                                                                      |
| [Tuple](#tuple)             | A parameterization of a data structure.               | Tuple, arguments, parameters, components, substructures                     |
| [Pattern](#pattern)         | An identifier of a specific variant.                  | Enum variant, placeholder, function name, parameterization name, markup tag |

**Categorization:**
- Nil and text represent scalar values
- Dictionary, table, composition are scalable collections
- Tuple represents structures composed of multiple values
- Tag is an identifier attached to a structure

### Nil

*Nil* is a [structure](#structure) corresponding to an empty, null or default data
structure.

### Text

*Text* is a [structure](#structure) corresponding to a scalar or irreducible
data structure. It consists of a string, which is a textual representation of the
corresponding data structure.

### Dictionary

A *dictionary* is a [structure](#structure) corresponding to a collection of data
structures organized by keys. It consists of a sequence of key-value pairs known as
entries. An *entry* assigns a [structure](#structure) *value* to a string *key*. Entries
with identical keys are allowed, and the order of the entries are preserved.

An entry is represented by a key, followed by a colon `:`, followed by a value. A
*key* is a string represented by a [word](#word), [transcription](#transcription) or [text block](#text-block).
There cannot be any [whitespace](#whitespace) between a key and colon. A *value* is
a [structure](#structure) represented by an [expression](#expression). A nonempty
dictionary is represented by either of two notations: [flow notation](#flow-dictionary-notation)
or [bullet notation](#bullet-dictionary-notation). Flow notation is intended for inline
entries and compact representations, while bullet notation is intended for singleline
and multiline entries.

#### Bracketed dictionary

A *bracketed dictionary* is a dictionary enclosed in a pair of curly brackets `{`,
`}`. It can be used as a [term](#expression) or an [argument](#pattern). `{}` represents
an empty dictionary.

#### Flow dictionary notation

In *flow notation*, entries are delimited by semicolons `;`. A *trailing semicolon*,
a semicolon following the last entry, is allowed.

###### Examples

- **Flow dictionary with 3 entries:** `k1: 1; "key 2": Hello world!; k3: [1; 2; 3]`
- **Flow dictionary & trailing semicolon:**
  ```
  k1: v1; k2: v2; k3: v3;
  k4: v4; k5: v5; k6: v6;
  ```

#### Bullet dictionary notation

In *bullet notation*, each entry starts with a right angle `>`.

###### Examples
- **Bullet dictionary:**
  ```
  > k1: v1
  > k2: v2
  > k3: [
    a; b;
    c; d;
  ]
  > k4: v4
  ```

### Table

A *table* is a [structure](#structure) corresponding to a collection of data structures
organized by rows and columns, or equivalently, indices. It consists of rows, which
consist of entries. All rows in a table must have the same number of entries. A table
that has only one column is also known as a *list*, and a table that has only one
row is also known as a *tuple*. An *entry* is a structure in the table.

An entry is represented by an [expression](#expression). A nonempty table is represented
by either of three notations: [flow notation](#flow-table-notation), [grid notation](#grid-table-notation)
or [bullet notation](#bullet-table-notation). Flow notation is intended for inline
rows and compact representations, grid notation is intended for singleline rows and
bullet notation is intended for multiline rows.

#### Bracketed table

A *bracketed table* is a table enclosed in a pair of square brackets `[`, `]`. It
can be used as a [term](#expression) or an [argument](#pattern). `[]` represents an
empty dictionary.

#### Flow table notation

In *flow notation*, rows are delimited by semicolons `;` and columns are delimited by
bars `|`. A trailing semicolon is permitted.

###### Examples

- **Flow list with 8 entries:**\
  `0; 1; 1; 2; 3; 5; 8; 13`
- **Flow tuple with 3 entries:**\
  `1|0|0`
- **Flow table with 3 rows and 3 columns:**\
  `1|0|0; 0|1|0; 0|0|1`
- **Flow table with 8 rows and 2 columns & trailing semicolon:**
  ```
  2|-1; -1|1; -1|3; 2|3;
  -1|-4; 1|4; 2|6; 0|3;
  ```

#### Grid table notation

In *grid notation*, bars `|` delimit rows and columns. A bar which is not preceded by
an entry opens a row. An entry followed by a bar appends the entry to the current
row.

###### Examples

- **Grid table with 2 rows and 3 columns:**
  ```
  | a a a | b | c c c |
  |   d d | e |     f |
  ```
- **Grid table:**
  ```
  |1|0|1|1|
  |0|1|0|0|
  |1|0|1|0|
  |1|0|0|1|
  ```

#### Bullet table notation

In *bullet notation*, each row starts with a right angle `>`. Columns are delimited
by bars `|`.

###### Examples

- **Bullet list with 4 entries:**
  ```
  > Hydrogen
  > Helium
  > Nitrogen
  > Oxygen
  ```
- **Bullet table:**
  ```
  > Northwest
  | North
  | Northeast
  > West
  | Centre
  | East
  > Southwest
  | South
  | Southeast
  ```

### Composition

A *composition* is a [structure](#structure) corresponding to a textual composition
of data structures. It consists of a sequence of elements. An *element* is either
a [structure](#structure) or a space indicator. Markup is the prime example of a data
structure represented by a composition.

### Tuple

Some data structures consist of multiple substructures. Tuples and patterns are
used to specify such structures. *Constructor notation* is intended to make structures
encoded as tuples and patterns easier to read and write. Constructor notation can
be used anywhere an expression is expected.

- Tuples can be expressed without square brackets.
- Text expressions can easily be delimited.

In constructor notation, expressions are delimited by diamonds `<>`. If the first
expression is a pattern with zero arguments, the trailing expressions become the arguments
of that pattern. Otherwise, the expressions form a tuple.

It is recommended, for sakes of readability, to only use constructor notation to separate
shorter values on a single line. The last value may span multiple lines.

TODO: Write

A tuple is a parameterization of a data structure.

Tuples can be used to specify a data structure consisting of an arbitrary number of
components. It is recommended to only use tuples for obvious/well documented cases.
Complex structures should use a dictionary.

Tuples with 1 component are automatically unwrapped. Thus, `<>:a` will always be
unwrapped to `a`. (Unless `a` is a tuple, in which case the tuples become nested).

`<>:a:b:c:d` is a tuple with 4 parameters.

A tuple expression: `a :: b :: c` is an expression representing a tuple with 3 parameters.

#### Component

An *argument* is a textual representation of a [structure](#structure) that can be
used in a [pattern](#pattern).

A *nil argument*, *text argument*, *dictionary argument*, *table argument*, *composition
argument* or *pattern argument* is an argument evaluating to [nil](#nil), [text](#text),
a [dictionary](#dictionary), a [table](#table), a [composition](#composition), a tuple or a
[pattern](#pattern) respectively.

The following textual representations can be used as components:

| Component                                     | Represents                                           |
|-----------------------------------------------|------------------------------------------------------|
| [Word](#word)                                 | [Text](#text)                                        | 
| [Transcription](#transcription)               | [Text](#text)                                        |
| [Text block](#text-block)                     | [Text](#text)                                        |
| [Bracketed dictionary](#bracketed-dictionary) | [Dictionary](#dictionary)                            |
| [Bracketed table](#bracketed-table)           | [Table](#table)                                      |
| Empty tuple `<>`                              | Empty tuple                                          |
| Tag `<Tag>`                                   | Tag                                                  |
| [Bracketed expression](#bracketed-expression) | [Value](#structure) of the [expression](#expression) |

###### Examples

- **List of stock changes & tuple constructor:**
  ```
  > 2023-Nov-10 :: -200 Crates
  > 2023-Nov-11 :: +500 Crates
  > 2023-Nov-12 :: +500 Crates
  > 2023-Nov-13 :: [+500 Crates; -250 Crates]
  > 2023-Nov-14 :: -650 Crates
  > 2023-Nov-15
  > 2023-Nov-16 :: -250 Crates
  > 2023-Nov-17 :: -350 Crates
  ```
- **List of words & pattern constructor:**
  ```
    > <Verb> :: clear :: {
      > regularity: <Regular>
      > transitivity: <Transitive>
      > conjugation: [ <to> clear | cleared | cleared | clearing ]
      > definitions: [
          > To empty the contents of.
          > To remove obstructions from.
          > To make transparent.
      ]
    }
    > <Verb> :: burn <down> :: {
      > regularity: <Irregular>
      > transitivity: <Transitive>
      > conjugation: [
          > <to> burn <down>
          | burnt <down>
          | burnt <down>
          | burning <down>
      ]
      > definition: To burn completely.
    }
    > <Noun> :: firewood :: {
      > countability: <Uncountable>
      > declension: [ firewood | firewood ]
      > definition: Wood burned to fuel a fire.
    }
  ```
- **Patterns within pattern constructor:**\
  `<Pattern> :: arg arg arg :: <P>:arg:arg :: <P> :: arg` represents a pattern with 4 parameters.


### Pattern

A *pattern* is a [structure](#structure) corresponding to a specific parameterized
data structure or command. A pattern consists of a name, attributes and parameters.
A *name* is a string which identifies the pattern. An *attribute* configures the pattern
and is either *valued* or *empty*. An attribute is identified by a string, and a valued
attribute has a string value. Duplicate attributes are not allowed. A *parameter* is
a [structure](#structure) that is used to instantiate the data structure or command.
A pattern name cannot start with a hash sign `#`, as this is reserved for [text block tags](#text-block).

A pattern is represented by a tag, which contains the name and attributes, followed
by a sequence of arguments which represent the parameters. A *tag* is a left angle
bracket `<`, followed by a word which represents the name, followed by a sequence
of attributes, followed by a right angle bracket `>`. An empty attribute is represented
by a word, and a valued attribute is represented by a word, followed by a colon `:`,
followed by a [word](#word), [transcription](#transcription) or [text block](#text-block)
which represents the string attribute value. A parameter is represented by an appended
colon `:`, followed by the corresponding argument. There cannot be whitespace before
or between the colon `:` and argument. A pattern with zero parameters is represented
by a plain tag.

The arguments form a tuple. The possible arguments are those that can be applied to
a tuple.

###### Examples

- `<b w:600>` is a pattern with name `b`, attribute `w` with value `600` and no parameters.
- **Pattern with 6 parameters:** `<sum>:1:2:3:4:5:6`
- **Pattern with no parameters:** `<br>`
- `<weight>:600:{This is bold text}` is the pattern `weight` applied to
  2 text arguments.
- `<p id:opening class:fancy>` is the pattern `p` with attributes `id:opening`
  and `class:fancy`.
- `<input type:checkbox checked>` has two attributes:
  `type` with value `checkbox` and `checked` with no value.
- In `<cmd0>:arg1:arg2:<cmd3>:arg4:arg5`, `<cmd0>` is a pattern applied
  to 5 arguments. `<cmd3>` is the third argument to `<cmd0>`, and is itself a pattern
  with zero arguments.
- `<name attr1 attr2:val2 attr3:val3 attr4>`
- In `<sender> sent <amount> to <recipient>.`, patterns are used to represent placeholders.
- `<set>:x:100` is a pattern representing the specific command `set` which sets the
  variable `x` to `100`.

#### Pattern composition

Whitespace between two arguments is used to compose patterns. It applies the pattern
on the right-hand side as an argument to the pattern on the left-hand side.

###### Examples

- `<bold>: <italic>:text` is equivalent to `<bold>:{ <italic>:text }`.
- `<a>: <b>: <c>:d` is equivalent to `<a>:{ <b>:{ <c>:d } }`.

## Expression

An *expression* is a textual representation of a [structure](#structure). It is a
nonblank sequence of terms and tilde operators `~` which may have whitespace in between
them. Every [structure](#structure) can be represented by an expression.

Tilde `~` is an operator which discards adjacent whitespace. *Discarded whitespace*
is ignored in compound expressions and text terms. A tilde can be placed before or
after all the terms, but will have no effect. A tilde can stand in for an empty expression,
since an expression cannot be blank.

An *empty expression* is an expression with no terms. It represents [nil](#nil).

A *simple expression* is an expression with a single term. It represents the [value](#structure)
the term represents.

A *compound expression* is an expression with multiple terms. It represents a [composition](#composition).
The composition contains the structures represented by the terms, ordered accordingly.
If there is non-discarded whitespace between two terms, then there is a space between
the corresponding structures in the result.

A *nil expression*, *text expression*, *dictionary expression*, *table expression*
*composition expression* or *pattern expression* is an expression that represents
[nil](#nil), [text](#text), a [dictionary](#dictionary), a [table](#table), a [composition](#composition)
or a [pattern](#pattern) respectively.

A *term* is a textual representation of a [structure](#structure) that can be used
in an [expression](#expression).

A *text term* represents [text](#text). It is a sequence of [words](#word), [transcriptions](#transcription),
[text blocks](#text-block) and tilde `~` operators which may have [whitespace](#whitespace)
in between them. When parsed, the strings represented by the [words](#word), [transcriptions](#transcription)
and [text blocks](#text-block) are concatenated. If there is non-discarded whitespace
in between two strings, then there is a space character between them in the result.
The concatenated string is the content of the represented text.

A *nil term*, *text term*, *dictionary term*, *table term*, *composition term* or
*pattern term* is a term representing [nil](#nil), [text](#text), a [dictionary](#dictionary),
a [table](#table), a [composition](#composition) or a [pattern](#pattern) respectively.

Some terms cannot be adjacent to each other, because they will merge. This is overcome
by using a [bracketed expression](#bracketed-expression) or sometimes a tilde `~` operator.

The following textual representations can be used as terms:

| Term                                          | Represents                            |
|-----------------------------------------------|---------------------------------------|
| Text term                                     | [Text](#text)                         |
| [Bracketed dictionary](#bracketed-dictionary) | [Dictionary](#dictionary)             |
| [Bracketed table](#bracketed-table)           | [Table](#table)                       |
| [Pattern](#pattern)                           | [Pattern](#pattern)                   |
| [Bracketed expression](#bracketed-expression) | [Value](#structure) of the expression |

###### Examples

- `~` is an empty expression. It contains no terms and represents [nil](#nil).
- `Hello world!` is a text expression containing a text term.
- `Text constructed from 5 words.`
- `R ~ e ~ d` is equivalent to `Red`.
- `{k1: v1; k2: v2}` is a dictionary expression containing a dictionary term.
- `{k1: v1} Text [1; 2; 3]` is a compound expression containing a dictionary term,
  text term and table term. It represents a [composition](#composition).
- `{c1} ~ {c2}` is equivalent to `{c1}{c2}`, but not `{c1} {c2}`.
- `{c1}`, `~ {c1}`, `{c1} ~`, `~ {c1} ~` are equivalent.
- **Whitespace equivalence:**
  ```
  a
  b {c}d
  [e]~
  f
  ```
  is equivalent to `a b {c}d [e]f`, and
  ```
  Text    with    whitespace
  ```
  is equivalent to `Text with whitespace`.

### Bracketed expression

A *bracketed expression* is an [expression](#expression) enclosed in a pair of curly
brackets `{`, `}`. It can be used as a [term](#expression) or an [argument](#pattern).
It represents the same [structure](#structure) as the [expression](#expression) it
encloses.

Bracketing is sometimes necessary to delimit terms in an expression.
Some [terms](#expression), like a [text term](#expression) or a [pattern term](#pattern)
could incorrectly merge with other terms when they are placed next to each other.

Bracketing could also be applied to increase readability, most commonly in multiline
contexts.

###### Examples

- An expression with the 2 terms `Purple` and `Orange` is correctly written `{Purple} {Orange}`.
  On the other hand, `Purple Orange` is an expression with a single text term.
- `{a [b; c]} d` is an expression with 2 terms. Without the curly brackets, this expression
  would have 3 terms.
- **Readability:**
  ```
  ...
  > content: {
    A paragraph...
  }
  ...
  ```

## String

### Word

A *word* is a sequence of [glyphs](#glyph), including [character escape sequences](#character-escape-sequence)
and [repeated escape sequences](#repeated-escape-sequence). It represents a string.

### Transcription

A *transcription* is a representation of a string. It is a sequence of characters enclosed
in a pair of backslashes `\ `. It cannot span multiple lines. [Character escape sequences](#character-escape-sequence)
can be used within the transcription, whose primary use case is the insertion of backslashes
marks `\ ` or linebreaks.

###### Examples

- **Example:** `\This is a quotation\ `
- **Example (quotation with reserved characters & character escape sequences):**\
  ```\Reserved: {, }, [, ], <, >, \`, :, ;, |, ~, ``\ ``` yields the text string\
  `` Reserved: {, }, [, ], <, >, \, :, ;, |, ~, ` ``.

### Text block

A *text block* is a sequence of characters enclosed in a pair of text block tags.
A text block represents a string. The *contents* of a text block is the enclosed characters,
excluding the tags.

A text block has a configuration which determines how its contents are processed.
By default, a text block is optimized for files (UNIX text files) and code.

A text block can have a [label](#labelled-text-block) which prevents content from
clashing with a closing block tag, but this is rarely needed.

#### Default text block

A text block is enclosed in a pair of text block tags `<#>`.

By default, the contents of a text block is formatted in 4 steps:
1. If a linebreak exists, delete the characters after the last linebreak if they are blank.
2. If a linebreak exists, delete the header line if it is blank.
3. Delete excess indentation from each line.

###### Examples

- **Code:**
  ```
  <#>
    def fib(n):
      if n == 0:
        return 0
      elif n == 1:
        return 1
      else:
        return fib(n - 2) + fib(n - 1)
  <#>
  ```
- **Stepwise formatting:**
  Let `·` represent a space character and `⏎` a newline character.
  ```
  ··<#>⏎
  ····def·sum(a,·b):⏎
  ······return·a·+·b⏎
  ··<#>⏎
  ```
  yields the string
  ```
  def·sum(a,·b):⏎
  ··return·a·+·b⏎
  ```
  Here, the characters after the last linebreak `··` are blank, and are deleted. The
  first line `⏎` is blank, and is deleted. The excess indentation `····` in each of
  the remaining lines is deleted.

#### Labelled text block

Text blocks may be labelled. Labels are rarely needed, but may in some cases to
prevent content from clashing with a tag.

###### Examples

- ```
  <#text>
    Text can contain <#> without
    clashing with the closing tag.
  <#text>
  ```
  is a text block with the label `text`.
- `<#khi><#a><#>...<#><#a><#khi>` yields the text `<#a><#>...<#><#a>`.

#### Configured text block

A text block can be configured. Its configuration determines how its contents are
formatted. A configuration is separated from the label by whitespace. The flags in
the configuration are applied in order.

| Flag | Function                                                                                 |
|------|------------------------------------------------------------------------------------------|
| `f`  | If a linebreak exists, delete the characters after the last linebreak if they are blank. |
| `h`  | If a linebreak exists, delete the header line if it is blank.                            |
| `x`  | Delete excess indentation from each line.                                                |
| `t`  | Delete trailing whitespace from each line.                                               |
| `l`  | Delete leading whitespace from each line.                                                |
| `n`  | Delete all linebreaks.                                                                   |
| `r`  | Clear configuration, including the defaults.                                             |

By default, a text block has the configuration `fhx`. The flag `r` resets and clears
all flags, including the default flags.

###### Examples

- `<#q n>Hello world!<#q>` is a text block with label `q`,
  and configuration `fhxn`.
- ```
  <# rtl>    public static final void main(String[] arguments) { ... }    <#>
  ```
  is a text block with configuration `tl`, and yields
  `public static final void main(String[] arguments) { ... }`.

## Whitespace

*Whitespace* is a sequence of [whitespace characters](#whitespace-character) or a [comment](#comment).

## Character

### Glyph

A *glyph* is a character that is not a [whitespace character](#whitespace-character)
nor a [reserved character](#reserved-character).

#### Character escape sequence

A *character escape sequence* is a *backtick* `` ` ``, known as the *escape character*,
followed by one of a preset of characters. It represents a [glyph](#glyph).

| Sequence   | Glyph    |
|------------|----------|
| `` {` ``   | `{`      |
| `` }` ``   | `}`      |
| `` [` ``   | `[`      |
| `` ]` ``   | `]`      |
| `` <` ``   | `<`      |
| `` >` ``   | `>`      |
| `` :` ``   | `:`      |
| `` ;` ``   | `;`      |
| `` \|` ``  | `` \| `` |
| `` ~` ``   | `~`      |
| ``` `` ``` | `` ` ``  |
| `` \` ``   | `\ `     |
| `` #` ``   | `#`      |
| `` n` ``   | Newline  |

###### Examples

-  `` Example:` This is an example `` encodes the string `Example: This is an example`.
- ``Example:` This is an example.``.

#### Repeated escape sequence

A *repeated escape sequence* is a sequence of characters that takes precedence over
the [reserved characters](#reserved-character) and represents a sequence of [glyphs](#glyph).

| Sequence | Glyphs |
|----------|--------|
| `\|\|`   | `\|\|` |
| `<<`     | `<<`   |
| `<<<`    | `<<<`  |
| `>>`     | `>>`   |
| `>>>`    | `>>>`  |

###### Examples

- `a >> b` is [text](#text), but `a > b` is invalid since a single `>` is [reserved](#reserved-character).

### Whitespace character

A *whitespace character* is one of the following:
- `U+0020 (Space)`
- `U+0009 (Horizontal tabulation)`
- `U+000A (Line feed)`

### Ignored character

An *ignored character* refers to `U+000D (Carriage return)`. It is always skipped,
even in [transcriptions](#transcription) and text blocks.

### Reserved character

A *reserved character* is a character that does not represent [text](#text) in a word,
unless it is escaped in some way. Reserved characters add structure to the document.
Thus, they cannot be used freely as [glyphs](#glyph).

| Character | Name          | Use                                       |
|-----------|---------------|-------------------------------------------|
| `:`       | Colon         | Key-value separator, argument application |
| `;`       | Semicolon     | Row separator, entry delimiter            |
| `\|`      | Bar           | Column separator                          |
| `~`       | Tilde         | Whitespace contraction                    |
| `` ` ``   | Backtick      | Escape sequence                           |
| `\ `      | Backslash     | Begin transcription, end transcription    |
| `{`       | Left bracket  | Begin expression, begin dictionary        |
| `}`       | Right bracket | End expression, end nonempty dictionary   |
| `[`       | Left square   | Begin table                               |
| `]`       | Right square  | End nonempty table                        |
| `<`       | Left angle    | Begin tag, diamond                        |
| `>`       | Right angle   | End tag, bullet                           |

A hash `#` is not reserved, but either is text or opens a [comment](#comment) depending
on the character following it.

## Comment

A *comment* is a note to the editors of a document. It is considered to be whitespace
by the parser.

A comment is opened with a hash `#` which is followed by whitespace or another hash
`#`. The comment ends at the next linebreak or EOF. The comment may contain any sequence
of characters.

If the hash `#` is followed by another character, then it is considered to be regular
text. A hash `#` cannot be followed a reserved character `{`, `}`, `[`, `]`, `<`,
`>`, `:`, `;`, `|`, `~` or `\ `.

###### Examples

- `# This is a comment` is a comment, because `#` is followed by whitespace.
- `#### Configuration ####` is a comment since the first `#` is followed
  by `#`.
- `#2`, `#0FA60F`, `A#B` and `#elements` are not comments since each `#`
  is followed by a text glyph.
