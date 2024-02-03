# Representation

This document describes the process of designing constructors for 

When Khi is used for configuration or markup, 

A Khi structure corresponds to a data structure of some type. This type determines


Here we discuss conventions about how common data structures are encoded.

Instead of looking at implementation details, such as whether a data structure
is implemented as a struct or enum or dictionary, consider instead what information
could fully specify an instance of said data structure. Even complex data structures
with several subcomponents can often be specified by a few pieces of information.
Such a combination of information pieces is known as a *constructor*.

Designed encodings are concise and expressive, in contrast to serial encoding. However,
this comes at the cost of having to manually implement constructors.

## Constructor

A *constructor* refers to a combination of data required to instantiate a value of
some type.

| Constructor |                     |
|-------------|---------------------|
| Nil         | The data structure  |
| Text        |                     |
| Dictionary  |                     |
| Table       |                     |
| Tuple       |                     |
| List        |                     |
| Composition |                     |
| Pattern     |                     |

## Simple structures

Consider first if 

## Complex structures

Some structures require multiple pieces of information to specify. Multiple pieces
of information should be supplied with tuple entries and tag arguments.

For tags, there are infinitely many possibilities. Just make sure that you name the
constructors appropriately.

Example: Consider a struct `Date { date, events }`. Customarily, such a struct should
be encoded as a dictionary: `{date: 2023-Dec-01; events: [Dinner]}`. Directives can
be used to define an alternate encoding. For example, `<Date>:2023-Dec-01:[Dinner]`.

## Trivial examples

Some structures are simple enough to have obvious and trivial constructors.

## Example: Events in time

In this example, we want a format for recording events. Events either happen at a
point or over a period in time.

| Structure                        | Example                             | Meaning                     |
|----------------------------------|-------------------------------------|-----------------------------|
| `Text(Date)`                     | `2023-Dec-12`                       | A date with no events.      |
| `\[Text(Date)\|Text(Event)\]`    | `[2023-Dec-12\|Report]`             | A date with an event.       |
| `\[Text(Date)\|List(of Events)]` | `[2023-Dec-12\|[Report; Fix bugs]]` | A date with multiple events |
| `Directive`                      | `<Week>:2023:40:{Deliveries}`       | Alternate time periods.     |

| Example | Meaning |
|---------|---------|
| <Week>  |         |
|         |         |

## Example: LaTeX-like markup

Markup is encoded as an expression containing an arbitrary number of text and directive expression arguments.
Directives that produce semantic text and which do not represent any action could be encoded as tags, but this is not
required. Other types of macros, which may represent actions, is encoded in command notation.

**Example:** The preprocessor examples above demonstrate markup encoding.

Markup is encoded as an expression, and each component.

- A text component is simply interpreted as text.
- An expression component is interpreted as markup (recursively).
- A directive represents a markup command.
- A table represents a tabulation.
- A dictionary component represents a key-value sequence, which is often used
  in command options.

## Example: XML-like markup

# Validity

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
