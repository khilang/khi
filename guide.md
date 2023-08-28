# Khi syntax and semantics

### Expressions and components

An *expression* represents a data structure. It consists of a sequence of components.
A *component* is a piece of information provided to an expression, which the expression
uses to encode data.

There are 5 kinds of components: expression, text, table, dictionary and directive.
Indeed, an expression can be a component of another expression.

**Example:** `{c1} {c2} {c3}` is an expression consisting of 3 components.

An expression with no components is known as an empty expression, an expression with a
single component is known as a unary expression, and an expression with two or more components
is known as a compound expression.

Each component variant encodes data in a unique way. Here is a summary of the interpretations
of each variant.

| Component  | Use                                                     |
|------------|---------------------------------------------------------|
| Expression | Encodes a data structure in a canonical or default way. |
| Text       | Encodes a primitive value.                              |
| Table      | Encodes multiple data structures.                       |
| Dictionary | Encodes multiple data structures identified by a name.  |
| Directive  | Encodes a data structure in a specific way.             |

### Brackets and grouping

Brackets `{` `}` are used to group and delimit components.

#### Grouping

Grouping components is done to form expression.

Grouping is done to compose an arbitrary number of components into a expression.

**Example:** `{}` is a grouping of zero components. It represents an empty expression
component.

**Example:** `{ {c1} {c2} {c3} }` is a grouping of 3 components. It represents a compound
expression.

Groupings of exactly one component are automatically unwrapped and substituted for
the component itself. As a consequence, it is not possible to determine from syntax
whether a component is a unary expression or not.

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

### Text

A *text* component represents a primitive piece of information, such as text or a
number. It is represented as either a sequence of words or as text enclosed in quotes.

**Example:** `This is a sentence.` and `"This is a sentence.` are two equivalent text
components.

**Example:** `"Text component 1" Text component 2 {Text component 3} {Text component
4}` is an expression consisting of 4 text components.

**Example:** `"Quotes allow insertion of arbitrary whitespace and reserved characters,
such as : or ]"`.

Unquoted text cannot contain reserved characters, unless they are escaped with backslash
`\ `.

**Example:** `Some reserved characters\: \:, \;, \<, \}`.

Colons `:` and semicolons `;` can be inserted into unquoted text by writing `::` or
`;;`.

**Example:** `Some text:: More text` represents the text `Some text: More text`.

Furthermore, any whitespace sequence in unquoted text is reduced to a single space
character. Khi is a whitespace-equivalent format, where all whitespace is equal
to a space character, unless it is escaped or within a quote.

### Dictionary

A *dictionary* is a sequence of key-value entries delimited by semicolons `;`. The
key and value in an entry is separated by a colon `:`. A key is given by a word or
a quote; it cannot be given as multiple words. A value is an expression. A dictionary
component is enclosed within curly brackets `{` `}`.

**Example:** `{ k1: 1; "key 2": Some text; k3: "Hello" }` is a dictionary component with
3 entries.

An empty dictionary component is written as `{#}` to distinguish it from an empty
expression.

A key followed by a semicolon `;` indicates that its value is an empty expression.

**Example:** `{k1; k2: v2; k3;}` contains the keys `k1` and `k3` which are followed by semicolons `;`. This means that
their values are empty expressions.

A trailing semicolon is allowed.

**Example:** `{k1: v1; k2: v2;}` and `{k1: v1; k2: v2}` are equal.

### Table

A *table* component is an arrangement of values into rows and columns. It represents
an organized collection of expressions.

All rows must have the same number of columns.

A table that has only one column is also known as a *list*, and its entries are known
as *elements*.

An *empty table* is a table with no values. An empty table component is written as
`[#]`.

There are two notations for tables: tabular notation and sequential notation.

#### Tabular notation

Tabular notation is intended for writing tables over several lines.

Tabular notation uses bars `|` to delimit rows and columns. This notation is intended
to have one table row per line.

A bar `|` preceded only by whitespace begins a new row, and a value followed by a
bar `val |` appends the value to the current row.

**Example:**
```
[
  |a|b|c|
  |d|e|f|
]
```
is a table argument with 2 rows and 3 columns.

There must be at least 1 column.

#### Sequential notation

In sequential notation, columns are separated bars `|` and rows are separated
by semicolons `;`.

A *sequence* argument is a sequence of expressions delimited by semicolons `;` enclosed in square brackets `[` `]`.

**Example:** `[1; 2; 3; 4]` is a list of 4 elements.

**Example:** `[1|0|0; 0|1|0; 0|0|1]` is a table with 3 rows and 3 columns.

Entries are expressions, thus empty entries can be represented with a question mark
`?`.

**Example:** `[1|?|?; ?|1|?; ?|?|1]` is a table with 6 empty entries.

A trailing semicolon is allowed.

**Example:** `[expr1; expr2;]` and `[expr1; expr2]` are equal.

### Directive

A *directive* consists of a header followed by an arbitrary number of attributes.
The header is a word that identifies the directive, and attributes tune specific behaviours.

Directives are applied to arguments.

There are two notations that produce directives: command notation and tag
notation.

#### Command notation

In command notation, a *directive expression* is encoded as a *directive* enclosed in angular brackets, followed by
arguments applied to it which are appended with colons `:` where there is no surrounding whitespace.

**Example:** `<sum>:1:2:3:4:5:6` is a directive applied to 6 arguments.

**Example:** `<br>` is a directive applied to 0 arguments.

**Example:** `<weight>:600:{This is bold text}` is the directive `weight` applied
to 2 text arguments.

The directive, which is the part enclosed in angular brackets, consists of a label followed by attributes. The label is
given by a word or a quote. Following the label, it is possible to insert attributes. An attribute is a
key-value pair. The key and value is delimited by a colon `:`.

**Example:** `<p id:opening class:fancy>` encodes the directive `p` with attributes `id:opening` and `class:fancy`.

An attribute key not followed by a colon is allowed. The value of such an attribute is considered to be an empty
argument.

**Example:** `<input type:checkbox checked>` has the label `input`. It has two attributes: `type` with value `checkbox`
and `checked` with value `{}`.

Directives can be inserted as arguments into a directive expression. There they are interpreted as directive expressions
that have zero arguments.

**Example:** In `<cmd0>:arg1:arg2:<cmd3>:arg4:arg5`, `<cmd3>` is a directive expression with zero arguments. `<cmd0>`
is a directive expression with 5 arguments.

The *composition operator* `<>` is a special operator that can be used in directive
expressions. It applies the directive expression on the right-hand side as an argument
to the directive expression on the left-hand side.

**Example:** `<bold>:<>:<italic>:text` is equivalent to `<bold>:{ <italic>:text }`.

If the last argument in a command expression is a word, then a question mark `?` can
be used to end the command expression. Consequently, word arguments may not contain
a question mark. This should be used when there must not be whitespace between the
command and the next component.

**Example:** In `You have <b>:300?.`, a question mark ensures that there is
no whitespace between `300` and `.`.

#### Tag notation

In tag notation, tags are used to produce directive expressions. An opening tag is
opened with a `+` while a closing tag is opened with a `-`.

**Example:** `<+tag>content<-tag>`.

The content enclosed by the tags is an expression that is appended as the last argument onto the directive expression
represented by the tags.

**Example:** `<+math>1 + 2 + 3 + <dots><-math>` is equivalent to `<math>:{1 + 2 + 3 + <dots>}`.

Arguments may still be appended onto the opening tag, by using colons. A question
mark can be used to end the arguments.

**Example:** `<+Sum>:k:1:n?3k^2-2k</Sum>` is equivalent to `<Sum>:k:1:n:{3k^2 - 2k}`.

It is optional whether to include the directive name in the closing tag or not. In some cases this is useful, but in
others this is too verbose.

**Example:** `<+tag>arg<-tag>` is equivalent to `<+tag>arg<->`.

Tag notation could in some cases increase the readability of a document.

**Example:** In long scopes spanning several lines, it could be difficult to see which brackets belong to which
directive, and where each scope ends. Tag notation can display the name of the scope in the closing tag, solving this
problem.
`<+html> Many lines and lots of stuff... <-html>`.

**Example:** Sometimes, brackets are placed too tersely, and it is difficult to distinguish them. Tag notation makes it
easier to distinguish scopes by introducing verbosity.
`<bold>:{Bold <italic>:{italic <underline>:{underlined <strikethrough>:{strikethrough text}}}}` is easier to read as
`<+bold>Bold <+italic>italic <+underline>underlined <+strikethrough>strikethrough text<-><-><-><->`.

#### Directive semantics

There are two dimensions to a directive:

- First, a directive represents a specific encoding of a data structure.
- Secondly, a directive describes how input encodes an action (An action is a side effect or stateful change or query to
  the environment). A directive is pure if it does not depend on the environment, and impure otherwise.

Given this, a directive expression represents either an encoded data structure, an encoded action, or a mix of both. A
directive could be seen as a generalization of **XML**-tags, **LaTeX**-commands/macros and text placeholders/tokens.

**Example:** In **XML**-like markup, tags are used to mark up and add semantics to text. Tags do not encode any action.
In **Khi**, tags can be encoded as directives, which when applied to text, encodes semantic text. For example, **HTML**
`<span class="italic">text</span>` corresponds to **Khi** `<+span class:italic>text<->`.

**Example:** In `<sender> sent <amount> to <recipient>.`, directives are used to represent
tokens or placeholders.

**Example:** In **LaTeX**-like markup, commands/macros are used to perform substitutions, computations and stateful
actions (for example incrementing a section count, or including a package). In **Khi**, commands can be trivially
encoded as directives. For example, **LaTeX** `\frac{2a}{b}` corresponds to **Khi** `<frac>:2a:b`.

**Example:** `<set>:x:100` encodes an action which sets the variable `x` to `100`. It encodes an empty data structure,
since this is purely a command.

### Root node

The root node of a Khi document is either an expression, table or dictionary.

### Reserved sequences

Reserved sequences are character sequences that cannot be used in regular text, unless
they are escaped or used within quotation marks. They represent tokens, which are
used to structure a Khi document.

| Sequence            | Label                | Usages                                    |
|---------------------|----------------------|-------------------------------------------|
| `:`                 | Colon                | Key-value separator, argument application |
| `;`                 | Semicolon            | Row separator, entry delimiter            |
| <code>&#x7c;</code> | Bar                  | Column separator                          |
| `` ` ``             | Backtick             | Escape sequence                           |
| `<>`                | Diamond              | Compose directives                        |
| `{`                 | Left bracket         | Begin expression, begin dictionary        |
| `}`                 | Right bracket        | End expression, end dictionary            |
| `#}`                | Number right bracket | End dictionary                            |
| `[`                 | Left square          | Begin table                               |
| `]`                 | Right square         | End table                                 |
| `#]`                | Number right square  | End table                                 |
| `<+`                | Left angle plus      | Begin opening tag                         |
| `<-`                | Left angle minus     | Begin closing tag                         |
| `<`                 | Left angle           | Begin directive                           |
| `>`                 | Right angle          | End directive                             |
| `"`                 | Quote                | Begin quote, end quote                    |

### Special sequences

Special sequences are character sequences that can usually be used in regular text,
but which have special meanings in some contexts.

#### Question mark: end directives & empty value

The question mark is usually interpreted as text, except in some circumstances.

- is used to end directive arguments.
- denotes an empty value in tables and dictionaries.

Within tables, a single standalone question mark denotes an empty entry.

A question mark

Thus, question marks cannot be used within a directive word argument, and a standalone
question mark cannot be

#### Hash: Comments & end of dictionaries and tables

A hash is used to open comments, and they are used to end tables and dictionaries.

A hash `#` that is followed by a text glyph is a text glyph.

**Example:** `#1`, `#2D353F` and `Operator#Plus` are text sequences.

A hash that is followed by whitespace or another `#`, opens a line comment.

A `#` cannot be followed by `{`, `[`, `<`, `>`, `"`, `:`, `;`, `|`.
Within a directive, where a `?` ends the directive, the sequence `#?` cannot be used.
However, within an expression, where `?` is treated as text, `#?` can be used and
is treated as text.

### Escape character

Backtick `` ` `` is the *escape character*. The character following it specifies the
character inserted into the document.

| Sequence             | Text                |
|----------------------|---------------------|
| `` `{ ``             | `{`                 |
| `` `} ``             | `}`                 |
| `` `[ ``             | `[`                 |
| `` `] ``             | `]`                 |
| `` `< ``             | `<`                 |
| `` `> ``             | `>`                 |
| `` `: ``             | `:`                 |
| `` `; ``             | `;`                 |
| <code>`&#x7c;</code> | <code>&#x7c;</code> |
| ``` `` ```           | `` ` ``             |
| `` `# ``             | `#`                 |
| `` `? ``             | `?`                 |
| `` `" ``             | `"`                 |
| `` `n ``             | Newline             |

**Example:** `` `: `` represents the text `:`.

### Escape sequences

Escape sequences are character sequences that take precedence over the reserved sequences,
and which represents regular text rather than a token.

| Seq   | Text  |
|-------|-------|
| `::`  | `::`  |
| `:::` | `:::` |
| ...   | ...   |
| `;;`  | `;;`  |
| `;;;` | `;;;` |
| ...   | ...   |
| `<<`  | `<<`  |
| `<<<` | `<<<` |
| ...   | ...   |
| `>>`  | `>>`  |
| `>>>` | `>>>` |
| ...   | ...   |

**Example:** `Price:: 300€` represents the text `Price: 300€`.

### Comments

A number sign `#` at the beginning of a word may open a comment, depending on which character follows it. If it is
followed by whitespace or another `#`, then a comment opens that ends at the next newline. Otherwise, if it is followed
by a text glyph, the word is parsed as text as normal.

**Example:** `# This is a comment` is a comment, because `#` is followed by whitespace.

**Example:** `#### Configuration ####` is a comment since the first `#` is followed
by `#`.

**Example:** `#2`, `#0FA60F` and `#elements` are not comments since `#` is followed
by a text glyph.

**Example:** A comment is not opened in `This is text# Is this a comment?` since `#`
is not at the beginning of a word.

### Question mark

<price>:wood:<?>

### Whitespace equivalence and significance

Every sequence of whitespace is equal to a single space character, unless the whitespace
is escaped or within a quote. Whitespace between arguments in an expression is significant,
but whitespace at the beginning or the end of an expression is insignificant.

**Example:** `arg1 {arg2}` is not equal to `arg1{arg2}`, because there is a difference
in significant whitespace.

**Example:** `arg1{ arg2 }` is equal to `arg1{arg2}`, because there is no difference
in significant whitespace.

## Encoding

### Validity

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

Although there are no definite rules regarding how a data structure should be encoded, there are some best practices
when it comes to what expressions and variants represent. Following these practices while implementing an encoding
makes **Khi**-based formats more uniform, which makes them more easily understood. Below, the best practices regarding
encodings of expressions and arguments are described.

Given syntax and semantics, it is now considered how the universal data structures
are encoded. When defining complex structures, one must split the data structure into multiple
arguments, and encode each part using the variant that best fits.

### Primitive value encoding

Primitive values are trivially encoded as text.

- Strings are encoded as text.
- Numbers, including booleans, are encoded as text. Valid encodings are further determined
  by number type.

### Markup encoding

Markup is encoded as an expression containing an arbitrary number of text and directive expression arguments.
Directives that produce semantic text and which do not represent any action could be encoded as tags, but this is not
required. Other types of macros, which may represent actions, is encoded in command notation.

**Example:** The preprocessor examples above demonstrate markup encoding.

### Text encoding



### Number encoding

Numbers may be encoded with an arbitrary number of whitespace.

### Bytes encoding

By default, bytes are encoded in hexadecimal (base 16). Arbitrary whitespace is allowed.

**Example:** `A1B2C3 D4E5F6` represents 6 bytes.

### Struct encoding

Structs that have no fields are encoded as an empty expression. Structs that have fields are either encoded as a
dictionary or a sequence, depending on if they are named or positional. Optionally, the struct type could be included.

| Variant           | Example                                                    |
|-------------------|------------------------------------------------------------|
| Named fields      | `{ x: 10; y: 30; z: 5 }` or `Point { x: 10; y: 30; z: 5 }` |
| Positional fields | `[127; 127; 0]` or `Point [127; 127; 0]`                   |
| No fields         | `{}` or `Empty`                                            |

### Enum encoding

Enums are encoded as 1 or 2 arguments. The first argument is a text argument that specifies the enum variant. If the
enum has no fields, it does not have a second argument. Otherwise, the second argument is either a sequence or a
dictionary, depending on if the enum has named or positional fields. Optionally, the enum type could be included, and
one of several ways to encode this together with the variant is shown.

| Variant           | Example                                                                       |
|-------------------|-------------------------------------------------------------------------------|
| Named fields      | `<Binomial>:{ n: 50; p: 10% }` or `Distribution <Binomial>:{ n: 50; p: 10% }` |
| Positional fields | `<Uniform>:[0; 10]` or `Distribution <Uniform>:[0; 10]`                       |
| No fields         | `<StandardNormal>` or `Distribution <StandardNormal>`                         |
| Newtype variant   | `<Metres>:50` or `Distance <Metres>:50`                                       |
