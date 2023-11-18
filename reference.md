# Khi reference

A reference describing the syntax and semantics of the Khi data format.

## Expressions and components

An *expression* represents an encoded data structure. It consists of a sequence of
components. A *component* is a piece of information provided to an expression, which
the expression uses to encode data.

There are 5 kinds of components: *expression*, *text*, *dictionary*, *table* and *directive*.
To elaborate, an expression can be a component of another expression. Component kind
is selected based on what fits the encoded data the best.

**Example:** `{key: value} Text [1; 0; 0]` is an expression consisting of a dictionary
component, a text component and a table component.

An expression with zero components is known as an empty expression, an expression
with a single component is known as a unary expression, and an expression with two
or more components is known as a compound expression.

Each component variant encodes data in a unique way. Here is a summary of the interpretations
of each variant.

| Component  | Use                                                                         |
|------------|-----------------------------------------------------------------------------|
| Expression | Encodes a data structure in a canonical or default way.                     |
| Text       | Encodes a primitive value.                                                  |
| Dictionary | Encodes multiple data structures, where each entry is identified by a name. |
| Table      | Encodes multiple data structures.                                           |
| Directive  | Encodes a data structure in a specific way.                                 |

### Brackets: Grouping and delimitation

Brackets `{` `}` are used to group and delimit components.

#### Grouping

Expressions are formed by grouping components together. This can be done by enclosing
components within brackets `{`, `}`.

**Example:** `{ {1} {2} {3} }` is a grouping of 3 text components. It represents a
compound expression.

**Example:** `{~}` is a grouping of zero components. It represents an empty expression
component. It cannot be written `{}`; this denotes an empty dictionary.

Groupings of exactly one component are automatically unwrapped and substituted for
the component itself. As a consequence, it is not possible to determine from syntax
whether a component is a unary expression or not.

**Example:** `{Text} {[1; 0]}` is an expression containing a text component
and a table component. The brackets contain only 1 component, thus they do not form
an expression.

**Example:** `{Text}` is a grouping of 1 text component. It is not possible to determine
from syntax if this is a text component a unary expression component containing a single
text component.

Whether a component shall be interpreted as a unary expression or not must be determined
by the semantics of the document.

#### Delimitation

Delimitation is sometimes done to increase readability, but sometimes it is necessary
to prevent components from merging into one.

**Example:** `{Text 1} {Text 2}` is an expression with 2 text components. Brackets are
used to separate the text components, preventing them from merging into one text component.

### Component separator

Tilde `~` is the component separator. It can be used within an expression to separate
two components.

**Example:** `Hello world! ~ 340` is an expression with text components `Hello world!`
and `340`.

Separated components have no whitespace between them.

**Example:** `A ~ B~C` is equivalent to `{A}{B}{C}`.

## Text

A *text* component represents a primitive piece of information, like plain text or
a number. It can be inserted as either a sequence of words or as text enclosed in
quotes.

**Example:** `Hello world!` and `"Hello world!"` are two equivalent text
components.

**Example:** `"Text component 1" Text component 2 {Text component 3} Text component
4` is an expression consisting of 4 text components.

Reserved characters, such as `{`, `}`, and `:`, can be freely inserted into quoted
text.

**Example:** `"Received: {items}"`.

Unquoted text cannot contain reserved characters, unless they are escaped with a backtick
`` ` ``.

**Example:** `` `{key`: value`} `` represents the text `{key: value}`.

Furthermore, any whitespace sequence in unquoted text is reduced to a single space
character. Khi is a whitespace-equivalent format, where all sequences of whitespace
equal a space character.

## Dictionary

A *dictionary* is a sequence of key-value entries delimited by semicolons `;`. The
key and value in an entry is separated by a colon `:`. A key is given by a word or
a quote; it cannot be given as multiple words. A value is an expression. A dictionary
component is enclosed within curly brackets `{` `}`.

**Example:** `{k1: 1; "key 2": Some text; k3: "Hello"}` is a dictionary component with
3 entries.

An empty dictionary component is written as `{}`.

An empty value can be denoted by `~`.

**Example:** `{k1: ~; k2: v2}` contains an entry with key `k1` and empty expression value.

A trailing semicolon is allowed.

**Example:** `{k1: v1; k2: v2;}` and `{k1: v1; k2: v2}` are equal.

## Table

A *table* component is an arrangement of values into rows and columns. It represents
an organized collection of expressions.

All rows must have the same number of columns.

A table that has only one column is also known as a *list*, and a table that has one
row is known as a *tuple*. 

An *empty table* is a table with no values. An empty table component is written as
`[]`.

There are two notations for tables: tabular notation and sequential notation.

### Tabular notation

Tabular notation is intended for writing tables over several lines.

Tabular notation uses bars `|` to delimit rows and columns. This notation is intended
to have one table row per line.

A bar `|` preceded only by whitespace begins a new row, and a value followed by a
bar `val |` appends the value to the current row.

**Example:**
```
[
  | a | b | c |
  | d | e | f |
]
```

is a table argument with 2 rows and 3 columns.

Empty entries can be denoted by `~`.

**Example:** 
```
[
  | 2.5 | 1 |
  |   ~ | 1 |
  | 3.0 | 0 |
  |   ~ | 1 |
]
```
is a table with two empty entries.

There must be at least 1 column.

### Sequential notation

In sequential notation, columns are separated bars `|` and rows are separated
by semicolons `;`.

A *sequence* argument is a sequence of expressions delimited by semicolons `;` enclosed in square brackets `[` `]`.

**Example:** `[1; 2; 3; 4]` is a list of 4 elements.

**Example:** `[1|0|0; 0|1|0; 0|0|1]` is a table with 3 rows and 3 columns.

Empty entries are denoted with a tilde `~`.

**Example:** `[1|~|~; ~|1|~; ~|~|1]` is a table with 6 empty entries.

A trailing semicolon is allowed.

**Example:** `[expr1; expr2;]` and `[expr1; expr2]` are equal.

## Directive

A *directive* consists of a header followed by an arbitrary number of attributes.
The header is a word that identifies the directive, and attributes tune specific behaviours.

Directives are applied to arguments.

A *directive expression* is encoded as a *directive* enclosed
in angular brackets, followed by arguments applied to it which are appended with colons
`:` where there is no surrounding whitespace.

**Example:** `<sum>:1:2:3:4:5:6` is a directive applied to 6 arguments.

**Example:** `<br>` is a directive applied to 0 arguments.

**Example:** `<weight>:600:{This is bold text}` is the directive `weight` applied
to 2 text arguments.

The directive, which is the part enclosed in angular brackets, consists of a label
followed by attributes. The label is given by a word or a quote. Following the label,
it is possible to insert attributes. An attribute is a key-value pair. The key and
value is delimited by a colon `:`.

**Example:** `<p id:opening class:fancy>` encodes the directive `p` with attributes
`id:opening` and `class:fancy`.

An attribute key not followed by a colon is allowed. The value of such an attribute
is considered to be an empty argument.

**Example:** `<input type:checkbox checked>` has two attributes:
`type` with value `checkbox` and `checked` with value `~` (empty expression).

Directives can be inserted as arguments into a directive expression. There, they are
interpreted as directive expressions that have zero arguments.

**Example:** In `<cmd0>:arg1:arg2:<cmd3>:arg4:arg5`, `<cmd0>` is a directive applied
to 5 arguments. `<cmd3>` is the third argument to `<cmd0>`, and is a directive expression
with zero arguments.

The *composition operator* `<>` is a special operator that can be used in directive
expressions. It applies the directive expression on the right-hand side as an argument
to the directive expression on the left-hand side.

**Example:** `<bold>:<>:<italic>:text` is equivalent to `<bold>:{ <italic>:text }`.

### Directive semantics

There are two dimensions to a directive:

- First, a directive represents a specific encoding of a data structure.
- Secondly, a directive describes how input encodes an action (An action is a side
  effect or stateful change or query to the environment). A directive is pure if it
  does not depend on the environment, and impure otherwise.

Given this, a directive expression represents either an encoded data structure, an
encoded action, or a mix of both. A directive could be seen as a generalization of
**XML**-tags, **LaTeX**-commands/macros and text placeholders/tokens.

**Example:** In **XML**-like markup, tags are used to mark up and add semantics to
text. Tags do not encode any action. In **Khi**, tags can be encoded as directives,
which when applied to text, encodes semantic text. For example, **HTML** `<span class="italic">text</span>`
corresponds to **Khi** `<span class:italic>:text`.

**Example:** In `<sender> sent <amount> to <recipient>.`, directives are used to represent
tokens or placeholders.

**Example:** In **LaTeX**-like markup, commands are used to perform substitutions,
computations and stateful actions (for example incrementing a section count or including
a package). In **Khi**, commands are encoded as directives. For example, **LaTeX**
`\frac{2a}{b}` corresponds to **Khi** `<frac>:2a:b`.

**Example:** `<set>:x:100` encodes an action which sets the variable `x` to `100`.
It encodes no data structure, since this is purely a command.

## Root node

The root node of a Khi document is either an expression, table or dictionary. It is
up to the designer of a format to choose which is the most practical for the application
in question.

## Reserved sequences

Reserved sequences are character sequences that cannot be used in regular text, unless
they are escaped or used within quotation marks. They represent tokens, which are
used to structure a Khi document.

| Sequence | Label           | Usages                                                       |
|----------|-----------------|--------------------------------------------------------------|
| `:`      | Colon           | Key-value separator, argument application, empty table entry |
| `;`      | Semicolon       | Row separator, entry delimiter                               |
| `\|`     | Bar             | Column separator                                             |
| `~`      | Tilde           | Component separator                                          |
| `` ` ``  | Backtick        | Escape sequence                                              |
| `<>`     | Diamond         | Compose directives                                           |
| `{`      | Left bracket    | Begin expression, begin dictionary                           |
| `}`      | Right bracket   | End expression, end nonempty dictionary                      |
| `[`      | Left square     | Begin table                                                  |
| `]`      | Right square    | End nonempty table                                           |
| `<`      | Left angle      | Begin command                                                |
| `<#`     | Left angle hash | Begin multiline quote, end multiline quote                   |
| `>`      | Right angle     | End directive                                                |
| `"`      | Quote           | Begin quote, end quote                                       |

## Comments

A hash `#` may open a comment, depending on which character follows it. If it is followed
by whitespace or another `#`, then a comment opens that ends at the next newline.
Otherwise, if it is followed by a text glyph, the word is parsed as text as normal.

**Example:** `# This is a comment` is a comment, because `#` is followed by whitespace.

**Example:** `#### Configuration ####` is a comment since the first `#` is followed
by `#`.

**Example:** `#2`, `#0FA60F`, `A#B` and `#elements` are not comments since each `#`
is followed by a text glyph.

## Hash glyph

A hash `#` that is followed by whitespace or another `#`, opens a line comment. A
hash `#` that is followed by a text glyph is a text glyph.

**Example:** `#1`, `#2D353F` and `A#12` are text sequences.

A `#` cannot be followed by `{`, `}`, `[`, `]`, `<`, `>`, `"`, `:`, `;`, `|` or `~`.

## Escape sequences, escape character

Backtick `` ` `` is the *escape character*. The character following it specifies the
character inserted into the document.

| Sequence   | Text     |
|------------|----------|
| `` `{ ``   | `{`      |
| `` `} ``   | `}`      |
| `` `[ ``   | `[`      |
| `` `] ``   | `]`      |
| `` `< ``   | `<`      |
| `` `> ``   | `>`      |
| `` `: ``   | `:`      |
| `` `; ``   | `;`      |
| `` `\| ``  | `` \| `` |
| `` `~ ``   | `~`      |
| ``` `` ``` | `` ` ``  |
| `` `# ``   | `#`      |
| `` `" ``   | `"`      |
| `` `n ``   | Newline  |

**Example:** `` `: `` represents the text `:`.

## Repeated escape sequences

Repeated escape sequences are character sequences that take precedence over the reserved
sequences and represent regular text rather than a token.

| Seq      | Text     |
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

**Example:** `a >> b` is text, but `a > b` is invalid since a single `>` is reserved.

## Whitespace equivalence and significance

Every sequence of whitespace is equal to a single space character, unless the whitespace
is escaped or within a quote. Whitespace between arguments in an expression is significant,
but whitespace at the beginning or the end of an expression is insignificant.

**Example:** `arg1 {arg2}` is not equal to `arg1{arg2}`, because there is a difference
in significant whitespace.

**Example:** `arg1{ arg2 }` is equal to `arg1{arg2}`, because there is no difference
in significant whitespace.

# Encoding

Here we discuss conventions about how common data structures are encoded.

## Validity

Khi dictates the syntax of expressions and components, but it does not dictate
how data structures are encoded. The validity of directive expressions, dictionary
keys and structure composition, are determined when a Khi-based format is defined.
This is similar to how **XML** and **JSON** are metalanguages. On their own, they
only determine if a document is syntactically well-formed, but leave questions of
validity to a format implementor.

A set of data structures could be encoded in Khi in many arbitrary ways. Thus,
a format implementor must select a specific encoding for each of them. An implementor
must also define whether the document root is an expression, a sequence or dictionary.
This can be done by writing documentation, using a schema, or preferably by implementing
deserialization procedures in a program. Once this is done, one has a format with
well-defined syntax, semantics and validity.

Although there are no definite rules regarding how a data structure should be encoded,
there are some best practices when it comes to what expressions and variants represent.
Following these practices while implementing an encoding makes **Khi**-based formats
more uniform, which makes them more easily understood. Below, the best practices regarding
encodings of expressions and arguments are described.

Given syntax and semantics, it is now considered how the universal data structures
are encoded. When defining complex structures, one must split the data structure into multiple
arguments, and encode each part using the variant that best fits.

## Primitive value encoding

Primitive values are trivially encoded as text.

- Strings are encoded as text.
- Numbers, including booleans, are encoded as text. Valid encodings are further determined
  by number type.

## Markup encoding

Markup is encoded as an expression containing an arbitrary number of text and directive expression arguments.
Directives that produce semantic text and which do not represent any action could be encoded as tags, but this is not
required. Other types of macros, which may represent actions, is encoded in command notation.

**Example:** The preprocessor examples above demonstrate markup encoding.

## Text encoding

Plain text is trivially encoded as text. If the text tend to contain reserved characters,
it may be a good idea to use quotes.

## Number encoding

Numbers may be encoded with an arbitrary number of whitespace.

## Bytes encoding

By default, bytes are encoded in hexadecimal (base 16). Arbitrary whitespace is allowed.

**Example:** `A1B2C3D4 E5F60000` represents 8 bytes.

## Struct encoding

Structs that have no fields are encoded as an empty expression. Structs that have
fields are either encoded as a dictionary or a tuple, depending on if they are named
or positional. Optionally, the struct type could be included.

| Variant           | Example                                                      |
|-------------------|--------------------------------------------------------------|
| Named fields      | `{ x: 10; y: 30; z: 5 }` or `<Point>:{ x: 10; y: 30; z: 5 }` |
| Positional fields | `[127\|127\|0]` or `<Rgb>:[127\|127\|0]`                     |
| No fields         | `~`, `{~}` or `<Unit>`                                       |

## Enum encoding

Enums are encoded as directive expressions with zero or one argument. The directive
specifies the enum variant. If the enum has no fields, it does not have a second argument.
Otherwise, the second argument is either a tuple or a dictionary, depending on if
the enum has named or positional fields.

| Variant           | Example                        |
|-------------------|--------------------------------|
| Named fields      | `<Binomial>:{ n: 50; p: 10% }` |
| Positional fields | `<Uniform>:[0\|10]`            |
| No fields         | `<StandardNormal>`             |
