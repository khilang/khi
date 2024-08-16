# Design

This document describes the background, goals and design decisions of the Khi data
format.

## Overview

1. [Background](#background)
2. [Goals](#goals)
3. [Candidates](#candidates)
4. [Design](#design)

## Background

Several textual data formats exist that are used for configuration, markup, storage
and serialization, including JSON, YAML, XML, TOML, CSV and LaTeX.

Each of these formats natively support particular data structures: most support strings
and numbers; JSON, YAML and TOML support objects, dictionaries and arrays; XML supports
tagged trees; CSV supports values in a table; and LaTeX supports markup.

There are applications where one needs to encode a combination of data structures not directly
supported by any of these formats. This is not really a problem if all one needs is
to serialize data, since many of the formats are versatile enough to systematically
encode any data in some way.

However, if the format is intended to be a source format, which means that it will
be read, understood and edited by a user, there are sets of structures that do not
have an acceptable representation in any of these formats. In other words, for some
combinations of data structures, none of these formats are acceptable as a source
format.

The goal is to find or design a textual format that is versatile enough to natively
express all universal data structures found in programming languages and other formats
while being easy to read and edit, so that it is a good source format.

The universal data structures that must be supported include scalars (strings, numbers,
booleans, dates), lists, tuples, sets, tables, matrices, dictionaries, maps, objects,
arrays, structs, enums and markup (such as text with commands or tags). These structures
are sufficient for markup, configuration and storage.

**Example:** An encyclopedia may need to store in its articles both structured data,
such as values stored in lists, tables and dictionaries, and unstructured data, such
as text and markup. It is certainly possible to serialize an article to JSON or XML,
but this encoding is not very user-friendly; it is hard to read, understand and edit.
In other words, these formats are not suitable as source documents for such articles.

**Example:** XML is versatile enough to encode any data structure, but some structures
must be encoded in a way that is excessively verbose. For example, in XML, a list
is encoded like `<list><li>1</li><li>2</li><li>3</li></list>`, while in JSON, this
list is encoded as `[1, 2, 3]`. Clearly, XML is usable as a serialization format,
but as a source format for expressing lists, it is unbearable.

## Goals

The goal is to design a data format that fulfills the criteria:

1. *Versatile*: The format
   1. natively supports representations of the principal data structures in modern
      programming languages and formats, including JSON, XML, CSV, YAML, LaTeX:
      null, nothing, strings, numbers, booleans, dates, objects, enums, dictionaries,
      maps, tables, lists, tuples, sets, markup, textual compositions, commands and
      tagged trees.
   2. can be used for configuration, markup and storage.
2. *Source format*: As a handwritten document, the format
   1. is easy and intuitive to read, write and edit and understandable at a glance.
   2. is aesthetic.
   3. is resistant to errors.
   4. has style recommendations.
   5. has comments.
   6. is not verbose.
   7. has a balanced level of syntax noise.
3. *Simple*: The format
   1. is not complex or complicated.
   2. is easy to parse.

## Candidates

Here, candidate formats are compared and evaluated according to the criteria.

<table>
 <tr><th></th><th>JSON</th><th>XML</th><th>YAML</th><th>TOML</th><th>DSV/CSV</th><th>LaTeX</th></tr>
 <tr>
  <td><b>1</b></td>
  <td>
   ❌Natively only supports scalars, objects and arrays.
   ❌Not practical for markup.
   ✔️For configuration and storage.
  </td>
  <td>
   ➖️Natively only supports scalars and tagged trees. However, tagged trees are flexible, and it is obvious how to encode other data structures with them.
   ✔️For configuration, markup and storage.
  </td>
  <td>
   ❌Natively only supports scalars, objects and arrays.
   ❌Not practical for markup.
   ✔️For configuration and storage.
  </td>
  <td>
   ❌Natively only supports scalars, objects and arrays.
   ❌Not practical for markup nor storage.
   ✔️For configuration.
  </td>
  <td>
   ❌Natively only supports scalars in a table.
   ❌Not practical for markup nor configuration.
   ✔️For storage.
  </td>
  <td>
   ❌Natively only supports text and commands.
   ❌Not practical for configuration or storage.
   ✔️For markup.
  </td>
 </tr>
 <tr>
  <td><b>2</b></td>
  <td>
   ✔️Easy to read, write and edit.
   ✔️Resistant to errors.
   ❌Comments.
  </td>
  <td>
   ❌️Tedious to read, write and edit.
   ✔️Resistant to errors.
   ✔️Comments.
  </td>
  <td>
   ➖️Initially easy to read, write and edit. However, it is easy to get lost in longer and deeper nested documents because of the significant indentation.
   ❌️Susceptible to indentation errors.
   ✔️Comments.
  </td>
  <td>
   ✔️Easy to read, write and edit.
   ✔️Resistant to errors.
   ✔️Comments.
  </td>
  <td>
   ✔️Easy to read, write and edit.
   ✔️Resistant to errors.
   ❌Comments.
  </td>
  <td>
   ➖️Can be tedious to read, write and edit due to terseness, significant blank lines, likeness of text and commands and number of brackets needed.
   ✔️Comments.
  </td>
 </tr>
 <tr>
  <td><b>3</b></td>
  <td>✔️Simple.</td>
  <td>✔️Simple.</td>
  <td>❌Complex.</td>
  <td>✔️Simple.</td>
  <td>✔️Simple.</td>
  <td>❌Complex.</td>
 </tr>
 <tr>
  <td><b>4</b></td>
  <td>✔️Concise. ❌Severe syntax noise around keys.</td>
  <td>❌Excessively verbose. ➖️️Some noise around attributes.</td>
  <td>✔️Concise. ✔️Low syntax noise.</td>
  <td>✔️Concise. ✔️Low syntax noise.</td>
  <td>✔️Concise. ✔️Low syntax noise.</td>
  <td>✔️Concise. ✔️Low syntax noise.</td>
 </tr>
</table>

Since no format is satisfactory, a new format will be designed.

## Design

Here are some of the decisions made during the design process. The reasons behind
these decisions may be subjective.

#### Whitespace equivalence

Whitespace equivalence gives users the flexibility to format a document however they
like. This is also advantageous since not all use cases can be predicted, and neither
can their optimal layouts.

#### Insignificant indentation

Significant indentation, for purposes of scope, is alright for shorter sections. Once
a section is large or deeply nested, it is too easy to get lost. Therefore, brackets
are preferred to control scoping.

#### Structure variants

The selected structure variants, nil, text, dictionaries, tables, compositions and
patterns, cover the corresponding considered data structures. Furthermore, each have
a satisfactory textual representation.

#### Patterns

A pattern represents a specific parameterized structure. It is left a little vague,
but this allows it to cover many important data structures. By *specific*: they can
represent any of an unbounded number of commands, enum variants, placeholders and
other name-identified data structures. By *parameterized*: these commands and enums
can take arguments and values. Attributes allow them to represent XML-tags or to be
configured, which is sometimes useful.

#### Argument application

Appending arguments by colon allows short word arguments, which are common, to be
given without using brackets. This reduces the number of required brackets, and increases
readability.

#### Escape sequences

#### Reserved characters

#### Metaformat

The XML approach is taken, where the semantics (such as the types of contained values)
of a document must be defined externally. An implementor must define semantics by
using a schema, writing documentation or implementing serialization/deserialization
procedures in a program. This approach is taken simply because it gives implementors
a lot of flexibility.

Furthermore, normally a document is not read blindly. A user or a program already
has expectations about the types of the values. Thus, it is not necessary to add syntax
typed values either.
