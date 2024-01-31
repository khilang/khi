# Reference

This document describes the syntax and semantics of the **Khi** data format.

## Overview

1. [Document](#document): A string, file, stream, etc. corresponding to a data structure.
2. [Structure](#structure) - A value corresponding to a data structure
3. [Expression](#expression) - A textual representation of a data structure
4. Structure variants:
   1. [Nil](#nil)
   2. [Text](#text)
   3. [Dictionary](#dictionary)
   4. [Table](#table)
   5. [Composition](#composition)
   6. [Pattern](#pattern)
5. String representation - A textual representation of a string
   1. [Word](#word)
   2. [Quotation](#quotation)
   3. [Text block](#text-block)
6. Character
7. [Constructor notation](#constructor-notation)
8. [Comment](#comment)

## Document

A *document* is a text file, string, stream, etc. conforming to either an [expression](#expression),
a [dictionary](#dictionary) or a [table](#table). It represents a [structure](#structure),
[dictionary](#dictionary) or a [table](#table) respectively. Evaluating a blank document
yields [nil](#nil), an empty [dictionary](#dictionary) or an empty [table](#table)
respectively.

## Structure

A *structure*, also referred to as a *value*, corresponds to a data structure. It is the
result of evaluating a [document](#document), [expression](#expression), term or argument. Examples
of data structures are strings, numbers, dates, dictionaries, tables, tuples, lists
and markup. There are six kinds of structures: [nil](#nil), [text](#text), [dictionary](#dictionary),
[table](#table), [composition](#composition) and [pattern](#pattern). Each encodes
data in a unique way.

| Structure                   | Corresponds to                                                            | Examples                                           |
|-----------------------------|---------------------------------------------------------------------------|----------------------------------------------------|
| [Nil](#nil)                 | An empty, null or default data structure.                                 | Empty value, default value, null, empty markup     |
| [Text](#text)               | A simple, scalar or irreducible data structure.                           | String, number, boolean, date, markup text         |
| [Dictionary](#dictionary)   | A collection of data structures organized by keys.                        | Dictionary, object, struct, configuration          |
| [Table](#table)             | A collection of data structures organized by rows and columns or indices. | Table, list, tuple, set, matrix, markup tabulation |
| [Composition](#composition) | A textual composition of multiple data structures.                        | Markup                                             |
| [Pattern](#pattern)         | A parameterized data structure or command.                                | Enum, placeholder, markup tag, markup command      |

## Expression

An *expression* is a nonblank sequence of terms. There may be whitespace
between the terms. When evaluated, it yields a [structure](#structure) value. Any
structure can be represented by an expression.

A *term* is a textual representation of a [structure](#structure) that can be used
in an [expression](#expression).

An *empty expression* is an expression with no term. It represents [nil](#nil).

A *simple expression* is an expression with a single term. It represents
the same [structure](#structure) as the term.

A *compound expression* is an expression with multiple terms. It represents
a [composition](#composition) of the structures represented by the terms. If there
is whitespace between two terms, there is whitespace between the structures in the
composition.

A *nil expression*, *text expression*, *dictionary expression*, *table expression*
*composition expression* or *pattern expression* is an expression that evaluates to
[nil](#nil), [text](#text), a [dictionary](#dictionary), a [table](#table), a [composition](#composition)
or a [pattern](#pattern) respectively.

In an expression, a tilde `~` is an operator which discards all
whitespace between the previous and next term or word. If
there is a tilde between two terms, then there is no whitespace between the elements
in the resulting composition.

Consecutive words and whitespace represent [text](#text).

A tilde can also be placed before or after the terms, but will have no effect.
A tilde can be placed in an expression with no terms. An expression cannot be
blank, thus, a tilde is necessary to represent an empty expression.

**Example:** `Hello world!` is an expression with a text term. It parses to text.

**Example:** `{k1: v1; k2: v2}` is an expression with a dictionary term. It parses
to a dictionary.

**Example:** `{k1: v1} Text [1; 2; 3]` is an expression containing a dictionary term,
text term and table term. It parses to a [composition](#composition).

**Example:** `~` is an expression with no terms. It is an empty expression. It
evaluates to [nil](#nil).

**Example:** `{c1} ~ {c2}` is equivalent to `{c1}{c2}`.

**Example:** `~ {c1}` is equivalent to `{c1}`.

**Example:** `A word that is <b>:bold~! {c1}` is equivalent to `{c1}`.

### Word sequence

In an expression, a *word sequence* is a sequence of [words](#word) separated by [whitespace](#whitespace) in between
them. When parsed, a sentence yields a string containing the words with space characters
separating them, according to the rule of [whitespace equivalence](#whitespace-equivalence).

In a sentence, a tilde `~` is used to discard whitespace. It can be placed between
two words. If there is a tilde between two words, there is no space character between
the words in the result.

**Example:** `This is a sentence built from 8 words.`

**Example:** `R ~ e ~ d` is equivalent to `Red`.

### Term forms

The possible forms of a [term](#expression) is:

| Term                                          | Represents                                       |
|-----------------------------------------------|--------------------------------------------------|
| [Word sequence](#word-sequence)               | [Text](#text)                                    |
| [Quotation](#quotation)                       | [Text](#text)                                    |
| [Text block](#text-block)                     | [Text](#text)                                    |
| [Bracketed dictionary](#bracketed-dictionary) | [Dictionary](#dictionary)                        |
| [Bracketed table](#bracketed-table)           | [Table](#table)                                  |
| [Pattern](#pattern)                           | [Pattern](#pattern)                              |
| [Bracketed expression](#bracketed-expression) | [Value](#structure) of [expression](#expression) |

A *nil term*, *text term*, *dictionary term*, *table term*, *composition term* or
*pattern term* is a term evaluating to [nil](#nil), [text](#text), a [dictionary](#dictionary),
a [table](#table), a [composition](#composition) or a [pattern](#pattern) respectively.

Some terms cannot be adjacent to each other. This is overcome by using a [bracketed expression](#bracketed-expression)
or sometimes a tilde `~` operator.

### Bracketed expression

A *bracketed expression* is an [expression](#expression) enclosed in a pair of brackets
`{`, `}`.

It can be used as both a [term](#expression) and an [argument](#pattern). It represents
the same [structure](#structure) as the [expression](#expression) it encloses.

#### Bracketing for delimitation

Bracketing is sometimes necessary to delimit terms in an expression.
Some [terms](#expression), like an [open text term](#word-sequence) or a [pattern term](#pattern)
could incorrectly merge with other terms when they are placed next to each other.
To prevent this, it is necessary to group them.

**Example:** An expression with the 2 terms `Purple` and `Orange` is correctly written
`{Purple} {Orange}`. On the other hand, `Purple Orange` is an expression with a single
term.

**Example:** `{{a} [b; c]} d` is an expression with 2 terms. Without bracketing, this
expression would have 3 terms.

#### Bracketing for readability

Groupings are sometimes used even when not necessary. This could be done to increase
readability, most commonly in multiline contexts.

**Example (Bracketing for readability):**
```
> paragraph: {
  A paragraph...
}
```

## Nil

*Nil* is a [structure](#structure) corresponding to an empty, null or default data
structure.

## Text

*Text* is a [structure](#structure) corresponding to a simple, scalar or irreducible
data structure. It contains a string, which is a textual representation of the data
structure.  a string, number, boolean or date.

## Dictionary

A *dictionary* is a [structure](#structure) corresponding to a collection of data
structures organized by keys. It is a sequence of key-value pairs known as entries.
An *entry* assigns a [structure](#structure) value to a string key. Duplicate keys
are allowed, and the order of the entries are preserved.

An entry is represented by a key, followed by a colon `:`, followed by a value.
A *key* is a string represented by a [word](#word), [quotation](#quotation) or [text block](#text-block).
There cannot be any [whitespace](#whitespace) between a key and colon. A *value* is
a [structure](#structure) represented by an [expression](#expression).

A nonempty dictionary is represented by either of two notations: [flow notation](#flow-dictionary-notation)
or [bullet notation](#bullet-dictionary-notation). Flow notation is intended for inline
entries and compact representations, while bullet notation is intended for singleline
and multiline entries.

### Bracketed dictionary

A dictionary enclosed in curly brackets `{`, `}` can be used as a [term](#expression) or
an [argument](#pattern).

### Flow dictionary notation

In *flow notation*, entries are delimited by semicolons `;`. A *trailing semicolon*,
a semicolon following the last entry, is allowed.

**Example (Flow dictionary with 3 entries):** `k1: 1; "key 2": Hello world!; k3: [1; 2; 3]`

**Example (Flow dictionary & trailing semicolon):**
```
k1: v1; k2: v2; k3: v3;
k4: v4; k5: v5; k6: v6;
```

### Bullet dictionary notation

In *bullet notation*, each entry starts with a right angle `>`.

**Example (Bullet dictionary):**
```
> k1: v1
> k2: v2
> k3: [
  a; b;
  c; d;
]
> k4: v4
```

## Table

A *table* is a [structure](#structure) corresponding to a collection of data structures
which are organized by rows and columns, or equivalently, indices. All rows in a table
must have the same number of columns. A table that has only one column is also known
as a *list*, and a table that has only one row is also known as a *tuple*. An *entry*
is a data structure in the table and is represented by an [expression](#expression).

A nonempty table is represented by either of three notations: [flow notation](#flow-table-notation),
[grid notation](#grid-table-notation) or [bullet notation](#bullet-table-notation).
Flow notation is intended for inline rows and compact representations, grid notation
is intended for singleline rows and bullet notation is intended for multiline rows.

### Bracketed table

A *bracketed table* is a table enclosed in square brackets `[`, `]`. It can be used
as a [term](#expression) or an [argument](#pattern).

### Flow table notation

In *flow notation*, rows are delimited by semicolons `;` and columns are delimited by
bars `|`. A trailing semicolon is permitted.

**Example (Flow list with 8 entries):**\
`0; 1; 1; 2; 3; 5; 8; 13`

**Example (Flow tuple with 3 entries):**\
`1|0|0`

**Example (Flow table with 3 rows and 3 columns):**\
`1|0|0; 0|1|0; 0|0|1`

**Example (Flow table with 8 rows and 2 columns & trailing semicolon):**
```
2|-1; -1|1; -1|3; 2|3;
-1|-4; 1|4; 2|6; 0|3;
```

### Grid table notation

In *grid notation*, bars `|` delimit rows and columns. A bar which is not preceded by
an entry opens a row. An entry followed by a bar appends the entry to the current
row.

**Example (Grid table with 2 rows and 3 columns):**
```
| a a a | b | c c c |
|   d d | e |     f |
```

**Example (Grid table):**
```
|1|0|1|1|
|0|1|0|0|
|1|0|1|0|
|1|0|0|1|
```

### Bullet table notation

In *bullet notation*, each row starts with a right angle `>`. Columns are delimited
by bars `|`.

**Example (Bullet list with 4 entries):**
```
> Hydrogen
> Helium
> Nitrogen
> Oxygen
```

**Example (Bullet table):**
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

## Composition

A *composition* is a [structure](#structure) corresponding to a textual composition
of data structures. It is a sequence of elements. An *element* is either a [structure](#structure)
or whitespace. Markup is the prime example of a data structure represented by a composition.

## Pattern

A *pattern* is a [structure](#structure) corresponding to a parameterized data structure
or command. A pattern has a name, attributes and arguments. A *name* is a string which
identifies the pattern. An *attribute* is a key-value pair that configures the pattern.
The key is a string while the value is either a string or not present. *Arguments* are
the values that instantiate the structure or command. A pattern could be seen as a
generalization of constructors, **XML**-tags, **LaTeX**-commands/macros and text placeholders/tokens.

A pattern is represented by a tag, which contains the name and attributes, followed
by a sequence of applied arguments.

A *tag* is a left angle bracket `<`, followed by a *name*, followed by a sequence
of attributes, followed by a right angle bracket `>`. The name is represented by a [word](#word)
that determines the specific data structure
or action. The [attributes](#pattern) tune the specific properties or behaviours
of the pattern. A pattern name cannot start with a hash sign `#`. This is reserved
for [text block tags](#text-block).

An *attribute* configures the pattern. An attribute is either a *flag* or take a string
value. A flag is represented by a word. An option is represented by a word, followed
by a colon `:`, followed by a value represented by a word, quotation or text block.
Duplicate attributes are not allowed.

An attribute key is a string represented by a word, and an attribute value
is a string represented by a word, quotation or text block. They are separated by
a colon. An attribute can be a flag, which means it has an empty value. Such an attribute
is represented by a single word.

An *argument* is a representation of
An *argument* is a representation of a [structure](#structure) that can be used in
a [pattern](#pattern).

A pattern is applied to an argument by appending a colon `:` followed by the argument.
There cannot be whitespace between the colon `:` and argument. A pattern with zero
arguments is simply represented by a tag.

**Example (Pattern with 6 arguments):** `<sum>:1:2:3:4:5:6`

**Example (Pattern with no arguments):** `<br>`

**Example:** `<weight>:600:{This is bold text}` is the pattern `weight` applied to
2 text arguments.

**Example:** `<p id:opening class:fancy>` is the pattern `p` with attributes `id:opening`
and `class:fancy`.

**Example:** `<input type:checkbox checked>` has two attributes:
`type` with value `checkbox` and `checked` with empty value.

**Example:** In `<cmd0>:arg1:arg2:<cmd3>:arg4:arg5`, `<cmd0>` is a pattern applied
to 5 arguments. `<cmd3>` is the third argument to `<cmd0>`, and is itself a pattern
with zero arguments.

**Example:** `<name attr1 attr2:val2 attr3:val3 attr4>`

**Example:** In **XML**-like markup, tags are used to mark up and add semantics to
text. Tags do not encode any action. In **Khi**, tags can be encoded as patterns,
which when applied to text, encodes semantic text. For example, **HTML** `<span class="italic">text</span>`
corresponds to **Khi** `<span class:italic>:text`.

**Example:**\
In `<sender> sent <amount> to <recipient>.`, patterns are used to represent placeholders.

**Example:**\
In **LaTeX**-like markup, commands are used to perform substitutions,
computations and stateful actions (for example incrementing a section count or including
a package). In **Khi**, commands are encoded as patterns. For example, **LaTeX**
`\frac{2a}{b}` corresponds to **Khi** `<frac>:2a:b`.

**Example:** `<set>:x:100` encodes an action which sets the variable `x` to `100`.

### Argument forms

The possible forms of an [argument](#pattern) is:

| Argument                                                            | Represents                                       |
|---------------------------------------------------------------------|--------------------------------------------------|
| [Word](#word)                                                       | [Text](#text)                                    | 
| [Quotation](#quotation)                                             | [Text](#text)                                    |
| [Text block](#text-block)                                           | [Text](#text)                                    |
| [Bracketed dictionary](#bracketed-dictionary)                       | [Dictionary](#dictionary)                        |
| [Bracketed table](#bracketed-table)                                 | [Table](#table)                                  |
| [Tag](#pattern) ([Pattern](#pattern) with no [arguments](#pattern)) | [Pattern](#pattern)                              |
| [Bracketed expression](#bracketed-expression)                       | [Value](#structure) of [expression](#expression) |

A *nil argument*, *text argument*, *dictionary argument*, *table argument*, *composition
argument* or *pattern argument* is an argument evaluating to [nil](#nil), [text](#text),
a [dictionary](#dictionary), a [table](#table), a [composition](#composition) or a
[pattern](#pattern) respectively.

### Pattern composition

Patterns can be composed by the *composition operator* `<>`. It applies the pattern
on the right-hand side as an argument to the pattern on the left-hand side.

**Example:** `<bold>:<>:<italic>:text` is equivalent to `<bold>:{ <italic>:text }`.

**Example:** `<a>:<>:<b>:<>:<c>:d` is equivalent to `<a>:{<b>:{<c>:d}}`.

## Word

A *word* is a sequence of glyphs, [character escape sequences](#character-escape-sequence)
and [repeated escape sequences](#repeated-escape-sequence). It represents a string.

A *glyph* is a character that is not [whitespace](#whitespace) nor a [reserved character](#reserved-character).

## Quotation

A *quotation* is a representation of a string. It is a sequence of characters enclosed
in a pair of quotation marks `"`. It cannot span multiple lines. [Character escape sequences](#character-escape-sequence)
can be used within the quotation, whose primary use case is the insertion of quotation
marks `"` or linebreaks.

**Example:** `"This is a quotation"`

**Example (quotation with reserved characters & character escape sequences):**\
```"Reserved: {, }, [, ], <, >, `", :, ;, |, ~, ``"``` returns the text string\
`` Reserved: {, }, [, ], <, >, ", :, ;, |, ~, ` ``.

## Text block

A *text block* is a sequence of characters enclosed in a pair of text block tags.
A text block represents a string. The *contents* of a text block is the enclosed characters,
excluding the tags.

A text block has a configuration which determines how its contents are processed before
being returned. By default, a text block is optimal for files and code.

A text block can have a [label](#labelled-text-block) which prevents enclosed characters
from clashing with a closing block tag.

### Default text block

A text block is enclosed in a pair of text block tags `<#>`.

By default, the contents of a text block is formatted in 4 steps:
1. If a linebreak exists, delete the characters after the last linebreak if they are blank.
2. If a linebreak exists, delete the header line if it is blank.
3. Delete excess indentation from each line.

**Example (Code):**
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

**Example (Formatting steps):**
Let `·` represent a space character and `⏎` a newline character.
```
··<#>⏎
····def·sum(a,·b):⏎
······return·a·+·b⏎
··<#>⏎
```
returns the string
```
def·sum(a,·b):⏎
··return·a·+·b⏎
```
Here, the characters after the last linebreak `··` are blank, and are deleted. The
first line `⏎` is blank, and is deleted. The excess indentation `····` in each of
the remaining lines is deleted.

### Labelled text block

Text blocks may be labelled. Labels are rarely needed, but may in some cases to
prevent content from clashing with a tag.

**Example:**
```
<#text>
  Text can contain <#> without
  clashing with the closing tag.
<#text>
```
is a text block with the label `text`.

**Example:**
`<#khi><#a><#>...<#><#a><#khi>` yields the text `<#a><#>...<#><#a>`.

### Configured text block

A text block can be configured. Its configuration that determines how its contents
are formatted. A configuration is separated from the label by whitespace. The flags
in the configuration are applied in order.

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

**Example:** `<#q n>Hello world!<#q>` is a text block with label `q`,
and configuration `fhxn`.

**Example:**
```
<# rtl>    public static final void main(String[] args) { ... }    <#>
```
is a text block with configuration `tl`.

## Reserved character

A *reserved character* is a character that does not represent [text](#text) in a word,
unless it is escaped in some way. Reserved characters add structure to the document.
Thus, they cannot be used freely within a [word](#word).

| Character | Name           | Use                                                    |
|-----------|----------------|--------------------------------------------------------|
| `:`       | Colon          | Key-value separator, argument application, constructor |
| `;`       | Semicolon      | Row separator, entry delimiter                         |
| `\|`      | Bar            | Column separator                                       |
| `~`       | Tilde          | Whitespace contraction                                 |
| `` ` ``   | Backtick       | Escape sequence                                        |
| `{`       | Left bracket   | Begin expression, begin dictionary                     |
| `}`       | Right bracket  | End expression, end nonempty dictionary                |
| `[`       | Left square    | Begin table                                            |
| `]`       | Right square   | End nonempty table                                     |
| `<`       | Left angle     | Begin tag, diamond                                     |
| `>`       | Right angle    | End tag, bullet                                        |
| `"`       | Quotation mark | Begin quotation, end quotation                         |

A hash `#` is not reserved, but either is text or opens a [comment](#comment) depending
on the character following it.

## Character escape sequence

A *character escape sequence* is a *backtick* `` ` ``, known as the *escape character*,
followed by one of several characters. It encodes a specific string.

| Sequence   | Encodes   |
|------------|-----------|
| `` `{ ``   | `{`       |
| `` `} ``   | `}`       |
| `` `[ ``   | `[`       |
| `` `] ``   | `]`       |
| `` `< ``   | `<`       |
| `` `> ``   | `>`       |
| `` `" ``   | `"`       |
| `` `: ``   | `:`       |
| `` `; ``   | `;`       |
| `` `\| ``  | `` \| ``  |
| `` `~ ``   | `~`       |
| ``` `` ``` | `` ` ``   |
| `` `# ``   | `#`       |
| `` `n ``   | Linebreak |

**Example:** `` Example`: `` encodes the string `Example:`.

## Repeated escape sequence

A *repeated escape sequence* is a sequence of characters that takes precedence over
[reserved characters](#reserved-character). It encodes a string.

| Sequence | Text     |
|----------|----------|
| `::`     | `::`     |
| `:::`    | `:::`    |
| `::::`   | `::::`   |
| ...      | ...      |
| `;;`     | `;;`     |
| `;;;`    | `;;;`    |
| ...      | ...      |
| `\|\|`   | `\|\|`   |
| `\|\|\|` | `\|\|\|` |
| ...      | ...      |
| `~~`     | `~~`     |
| `~~~`    | `~~~`    |
| ...      | ...      |
| `<<`     | `<<`     |
| `<<<`    | `<<<`    |
| ...      | ...      |
| `>>`     | `>>`     |
| `>>>`    | `>>>`    |
| ...      | ...      |

**Example:** `a >> b` is text, but `a > b` is not since a single `>` is reserved.

## Whitespace

*Whitespace* is a sequence of whitespace characters or a [comment](#comment).

### Whitespace equivalence

*Whitespace equivalence* is the principle that any whitespace is equivalent. In other
words, a sequence of whitespace is equivalent to a space character.

**Example:**
```
a
b {c}d
[e]~
f
```
is equivalent to `a b {c}d [e]f`.

**Example:**
```
Text    with    whitespace
```
is equivalent to `Text with whitespace`.

## Constructor notation

Some data structures consist of multiple substructures. Tuples and patterns are
used to specify such structures. *Constructor notation* is intended to make structures
encoded as tuples and patterns easier to read and write. Constructor notation can
be used anywhere an expression is expected.

- Tuples can be expressed without square brackets.
- Text expressions can easily be delimited.

In constructor notation, expressions are delimited by colons `:`, and there must be
whitespace before and after a colon. If the first expression is a pattern with zero
arguments, the trailing expressions become the arguments of that pattern. Otherwise,
the expressions form a tuple.

It is recommended, for sakes of readability, to only use constructor notation to separate
shorter values on a single line. The last value may span multiple lines.

**Example (List of stock changes & tuple constructor):**
```
> 2023-Nov-10 : -200 Crates
> 2023-Nov-11 : +500 Crates
> 2023-Nov-12 : +500 Crates
> 2023-Nov-13 : [+500 Crates; -250 Crates]
> 2023-Nov-14 : -650 Crates
> 2023-Nov-15
> 2023-Nov-16 : -250 Crates
> 2023-Nov-17 : -350 Crates
```

**Example (List of words & pattern constructor):**
```
> <Verb> : clear : <Regular> : <Transitive> : {
  > conjugation: <a>:to clear : cleared : cleared : clearing
  > definitions: [
    > To empty the contents of.
    > To remove obstructions from.
    > To make transparent.
  ]
}
> <Verb> : burn <p>:down : <Irregular> : <Transitive> : {
  > conjugation: <a>:to burn <p>:down : burnt <p>:down : burnt <p>:down : burning <p>:down
  > definition: To burn completely.
}
> <Noun> : firewood : <Uncountable> : {
  > declension: firewood : firewood
  > definition: Wood burned to fuel a fire.
}
```

**Example (Patterns within pattern constructor):**\
`<Pattern> : arg arg arg : <P>:arg:arg : <P> : arg` represents a pattern with 4 arguments.

## Character

A *whitespace character* is one of the following:
- `U+0020 (Space)`
- `U+0009 (Tabulation)`
- `U+000A (Line feed)`

An *ignored character* refers to `U+000D (Carriage return)`. It is always skipped,
even in quotations and text blocks.

## Comment

A *comment* is a note to the editors of a document. It is considered to be whitespace
by the parser.

A comment is opened with a hash `#` which is followed by whitespace or another hash
`#`. The comment ends at the next linebreak or EOF. The comment may contain any sequence
of characters.

If the hash `#` is followed by another character, then it is considered to be regular
text. A hash `#` cannot be followed by `{`, `}`, `[`, `]`, `<`, `>`, `"`, `:`, `;`, `|`
or `~`.

**Example:** `# This is a comment` is a comment, because `#` is followed by whitespace.

**Example:** `#### Configuration ####` is a comment since the first `#` is followed
by `#`.

**Example:** `#2`, `#0FA60F`, `A#B` and `#elements` are not comments since each `#`
is followed by a text glyph.
