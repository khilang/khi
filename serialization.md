# Serialization

Serialization and deserialization is the systematic encoding and decoding of instances
of data types in modern programming languages.

Most programming languages model data with structs, objects, enums, dictionaries,
lists, tuples, and primitives like strings and numbers. By defining a default encoding
for these notions, any data structure can be systematically encoded. The drawback
is that such an encoding may be far more verbose than necessary.

## Summary

| Construct       | Encoding                                       |
|-----------------|------------------------------------------------|
| String          | Text                                           |
| Number          | Text                                           |
| Boolean         | Text                                           |
| Date            | Text                                           |
| Dictionary, map | Dictionary                                     |
| Table, matrix   | Table                                          |
| List, set       | List                                           |
| Tuple           | Tuple                                          |
| Field struct    | Dictionary or pattern with dictionary argument |
| Tuple struct    | Tuple or pattern with tuple argument           |
| Unit struct     | Null or pattern with no arguments              |
| Field enum      | Pattern with dictionary argument               |
| Tuple enum      | Pattern with tuple argument                    |
| Unit enum       | Pattern with no arguments                      |
| Option          | ``<?>`` if empty or `<?>:x` if present         |
| Null, default   | `~`                                            |

## Scalar

Primitive values are trivially encoded as text.

- Strings are encoded as text.
- Numbers, including booleans, are encoded as text. Valid encodings are further determined
  by number type.

Plain text is trivially encoded as text. If the text tend to contain reserved characters,
it may be a good idea to use quotes.

By default, bytes are encoded in hexadecimal (base 16). Arbitrary whitespace is allowed.

**Example:** `A1B2C3D4 E5F60000` represents 8 bytes.

## Dictionary

A dictionary is trivially represented by a dictionary structure.

`{k: v; k: v} # A dictionary.`

## List, tuple, table

A table is trivially represented by a table structure. A list, where all entries
are of the same type, is represented by a table with one column. A tuple, where entries
may be of different types, is represented by a table with one row.

`[1; 2; 3] # A list.` `[1|A|<Blue>] # A tuple.`

## Struct

Structs that have no fields are encoded as an empty expression. Structs that have
fields are either encoded as a dictionary or a tuple, depending on if they are named
or positional. Optionally, the struct type could be included.

| Variant           | Example                                                  |
|-------------------|----------------------------------------------------------|
| Named fields      | `{x: 10; y: 30; z: 5}` or `<Point>:{x: 10; y: 30; z: 5}` |
| Positional fields | `[127\|127\|0]` or `<Rgb>:[127\|127\|0]`                 |
| No fields         | `[]` or `<Unit>`                                         |

## Enum

Enums are encoded as patterns with zero or one argument. The pattern name
specifies the enum variant. If the enum has no fields, it does not have an argument.
Otherwise, the argument is either a tuple or a dictionary, depending on if
the enum has named or positional fields.

| Variant           | Example                      |
|-------------------|------------------------------|
| Named fields      | `<Binomial>:{n: 50; p: 10%}` |
| Positional fields | `<Uniform>:[0\|10]`          |
| No fields         | `<StandardNormal>`           |
