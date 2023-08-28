# Khi design document

## Introduction

There exist several textual data formats that are used for configuration, markup,
storage and interchange, including JSON, YAML, XML, TOML, CSV and LaTeX. Each of these
formats natively support particular data structures: JSON specializes in dictionaries
and arrays, XML specializes in trees, CSV specializes in tables and LaTeX specializes
in markup.

These formats work well for many purposes. however, there are applications where one
needs to encode a set of data structures not directly supported by any of these formats.
This is not really a problem if all one needs is to serialize data, since many of
the formats are versatile enough to encode any data is some way.

However, if the format is intended to be a source format, which means that it will
be read, understood and edited by a user, there are sets of structures that do not
have an acceptable representation in any of these formats. In other words, for some
data structures, none of these formats are acceptable as a source format.

The goal of Khi is to be a textual format that is versatile enough to natively express
all universal data structures found in programming languages and other formats while
having great readability and writability, so that it is a good source format. Khi
must be easy and non-tedious to read, understand and edit.

The universal data structures supported include primitives (text and numbers), sequences
(lists, tuples), tables (including matrices), dictionaries and markup (such as text
with commands or tags), structs and enums. These structures can be used to define
different types of formats, such as configuration, data storage and interchange and
markup.

**Example:** An encyclopedia may need to store in its articles both structured data,
such as values stored in lists, tables and dictionaries, and unstructured data, such
as text and markup. It is certainly possible to serialize an article to JSON or XML,
but this encoding is not very user-friendly; it is hard to read, understand and edit.
In other words, these formats are not suitable as source documents for such articles.

**Example:** XML is versatile enough to encode any data structure, but some structures
must be encoded in a way that is excessively verbose. For example, in XML, a list
is encoded like `<list><li>1</li><li>2</li><li>3</li></list>`, while in Khi, this
list is encoded as `[1;2;3]`. Clearly, XML is usable as a serialization format, but
perhaps not as a source format.

## Goals

The goal is to design a textual format that satisfy the requirements below. It is also considered how other formats that
already exist satisfy these requirements. The most important requirements are **1**, **2**, **6**, **7**, **8** and
**10**, while **9** is of lesser importance. The primary reason for designing this new format is indeed the lack of a
format satisfying requirements **6** and **10**. Keep in mind that some requirements may be subjective.

<table>
 <tr><th>Goal</th><th>JSON</th><th>XML&HTML</th><th>YAML</th><th>TOML</th></tr>
 <tr>
  <td>
   <b>1</b> The format is human-readable. Assuming that best formatting practices are followed, the format should be easy to read
   and understand.
  </td>
  <td>✔️</td>
  <td>✔️</td>
  <td>✔️</td>
  <td>✔️</td>
 </tr>
 <tr>
  <td><b>2</b> The format is human-writable. Here, ease of writing or convenience is not taken into account.</td>
  <td>✔️️</td>
  <td>✔️</td>
  <td>✔️</td>
  <td>✔️</td>
 </tr>
 <tr>
  <td>
   <b>3</b> The format is simple. There are few special cases. An advantage of a simpler format is that it is easier to
   parse.
  </td>
  <td>✔️</td>
  <td>✔️ There is sometimes minor confusion about whether to encode data as tags or as attributes.</td>
  <td>❌ YAML is complex. There are many special cases and values may yield surprising results.</td>
  <td>✔️</td>
 </tr>
 <tr>
  <td><b>4</b> The format is concise and contains minimal syntax noise.</td>
  <td>➖ JSON is concise, but does not minimize syntax noise. It requires quotes around keys even when there is no ambiguity.</td>
  <td>❌ XML does not minimize syntax noise. It is extremely verbose.</td>
  <td>✔️</td>
  <td>✔️</td>
 </tr>
 <tr>
  <td><b>5</b> The format has comments.️</td>
  <td>❌</td>
  <td>✔️</td>
  <td>✔️</td>
  <td>✔️</td>
 </tr>
 <tr>
  <td><b>6</b> The format can natively express both structured and unstructured data, such as:
    <ul>
      <li>numbers, text, structs, enums, sequences and dictionaries.</li>
      <li>markup consisting of text and tags with attributes and content, like HTML.</li>
      <li>markup consisting of text, groupings and macro commands, like TeX.</li>
    </ul>
  </td>
  <td>❌ JSON does not support markup, and it is not entirely clear how to represent sum types.</td>
  <td>➖️ XML can represent these structures thanks to its flexibility, but it has no native support for sequences and dictionaries. Yet, it is obvious how to model them.</td>
  <td>❌ YAML does not support markup, and it is not entirely clear how to represent sum types.</td>
  <td>❌ TOML does not support markup, and it is not entirely clear how to represent sum types.</td>
 </tr>
 <tr>
  <td><b>7</b> The format is suitable for markup.</td>
  <td>❌</td>
  <td>✔️ </td>
  <td>❌</td>
  <td>❌</td>
 </tr>
 <tr>
  <td><b>8</b> The format is suitable for configuration.</td>
  <td>➖️ JSON can be used for configuration, but it lacks comments, which is a big downside.</td>
  <td>
   ➖️ XML can be used for configuration, but its verbosity makes it inconvenient as a universal configuration format.
  </td>
  <td>✔️ </td>
  <td>✔️ </td>
 </tr>
 <tr>
  <td><b>9</b> The format is viable for serialization, data storage and data interchange.</td>
  <td>✔️ </td>
  <td>✔️ </td>
  <td>➖️ YAML can be used for serialization, but is not optimal.</td>
  <td>❌ TOML is not intended for serialization.</td>
 </tr>
 <tr>
  <td>
   <b>10</b> The format is suitable for hand-coding. It lends itself well as a source format. It can conveniently encode structured data and markup.
  </td>
  <td>➖️️ JSON can be hand-coded easily, but its lack of comments makes it impractical as a source format.</td>
  <td>❌️ XML is not suitable as a source format because of its verbosity.</td>
  <td>✔️ YAML is easy to hand-code in most cases, but when YAML documents get large or complex, they may get hard to
      manage, especially given the whitespace indentation.</td>
  <td>✔️ </td>
 </tr>
</table>

## Design

Here are some of the decisions made during the design process. The reasons behind these decisions may be subjective.

#### Whitespace equivalence

Whitespace equivalence gives users the flexibility to format a document however they like. For simple expressions, this
flexibility is not needed, but for complex expressions that span multiple lines, it is appreciated.

#### Whitespace indentation

Whitespace indentation is simple and works great when expressions span one line. In many whitespace-indented formats and
languages, this is the case most of the time. However, when an expression has to span multiple lines, whitespace
indentation requires complex rules that feel like special cases to the user. Keeping track of whitespace and indentation
level also adds complexity to the parser. Thus, it was decided to stick with bracket delimited scopes.

#### Argument variants

Looking at modern programming languages and ubiquitous formats such as **JSON**, **XML** and **LaTeX**, the following
structures are universally used: numbers, text, structs/product types, enums/sum types, dictionaries, sequences and
markup consisting of text and commands/tags.

The implemented argument variants are able to natively support these structures with concise and convenient syntax.

#### Metaformat

The XML approach is taken, where the semantics (such as the types of contained expressions) of a document must be
defined externally. A user must define semantics by using a schema, writing documentation or implementing
serialization/deserialization procedures in a program.

This approach is taken simply because it gives format implementers a lot of flexibility. Furthermore, normally a
document is not read blindly. A user or a program already has expectations about the types of encoded expressions. Thus,
it is not necessary to add syntax typed expression either.
