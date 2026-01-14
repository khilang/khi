# Khi grammar

Space ` ` or asterisk `*` is optional whitespace. Underscore `_` is required
whitespace. Character within quotes or `<word>`, `<closed-transcription>`,
`<unclosed-transcription>`  or `<text-block>` is a token.

## Document

```
<value-document> → *
                 | *<value>*
```

```
<dictionary-document> → *
                      | *<dictionary>*
```

```
<list-document> → *
                | *<list>*
```

## Value

```
<value> → <catenation>
        | <open-tuple>
```

```
<value-bracket> → "{" <value> "}"
```

## Text

```
<text>  → <string>
        | <string> <text'>
<text'> → <string>
        | <string> <text'>
        | "~" <text'>
```

```
<string> → <word>
         | <closed-transcription>
         | <unclosed-transcription>
         | <text-block>
```

## Tagged tuple

```
<open-tuple> → <open-elements>
             | <tagged-tuple>
```

```
<open-elements>  → <open-element> <open-elements'>
                 | <open-elements'>
<open-elements'> → "|" <open-element>
                 | "|" <open-element> <open-elements'>
```

```
<open-element> → <catenation>
               | ":"<key> <catenation>
```

```
<tagged-tuple> → <tag>":"_<tagged-value>
```

```
<tagged-value> → <catenation>
               | <open-elements>
               | <tagged-tuple>
```

```
<compact-tuple> → <tag>
                | <tag><compact-elements>
```

```
<compact-elements> → ":"<compact-element>
                   | ":"<compact-element><compact-elements>
```
`<compact-element>` takes precedence over `<compact-elements>` on ambiguity.

```
<compact-element> → <compact-value>
                  | <key>"="<compact-value>
```

```
<compact-value> → <string>
                | <value-bracket>
                | <dictionary-bracket>
                | <list-bracket>
                | <compact-tuple>
```

```
<tag> → "<"<key>">"
      | "<"<key>_<attributes> ">"
      | "<"">"
```

```
<attributes> → <attribute>
             | <attribute>_<attributes>
```

```
<attribute> → <key>
            | <key>"="<string>
```

## Dictionary

```
<dictionary> → <delimited-dictionary>
             | <aligned-dictionary>
             | <sectioned-dictionary>
```

```
<delimited-dictionary> → <entry>
                       | <entry> ";"
                       | <entry> ";" <delimited-dictionary>
```

```
<aligned-dictionary> → <entry>
                     | <entry>_<aligned-dictionary>
```
Ambiguity of `<entry>` in `<delimited-dictionary>` and `<aligned-dictionary>`
is inconsequential.

```
<sectioned-dictionary>  → <sectioned-dictionary'>
                        | <regular-dictionary>_<sectioned-dictionary'>
<sectioned-dictionary'> → <section>
                        | <section>_<sectioned-dictionary>
```

```
<section> → <curly-header>":"_<value>
          | <curly-header>":"
          | <curly-header>":"_<regular-dictionary>
          | <square-header>":"
          | <square-header>":"_<list>
          | "{"<square-header>"}"":"_<value>
          | "{"<square-header>"}"":"
          | "{"<square-header>"}"":"_<regular-dictionary>
          | "["<square-header>"]"":"
          | "["<square-header>"]"":"_<list>
```

```
<curly-header> → "{"<header-key>"}"
```

```
<square-header> → "["<header-key>"]"
```

```
<header-key> → <key>
             | <key> ">" <header-key>
```

```
<regular-dictionary> → <delimited-dictionary>
                     | <aligned-dictionary>
```

```
<entry> → <key>":"_<value>
```

```
<dictionary-bracket> → "{" "}"
                     | "{" <dictionary> "}"
```

## List

```
<list> → <delimited-list>
       | <bulleted-list>
       | <tabulated-list>
       | <tagged-list>
```

```
<delimited-list> → <value>
                 | <value> ";"
                 | <value> ";" <delimited-list>
```

```
<bulleted-list> → ">"_<value>
                | ">"_<value>_<bulleted-list>
```

```
<tabulated-list> → <open-elements'> "|"
                 | <open-elements'> "|"_<tabulated-list>
```

```
<tagged-list> → <tagged-tuple>
              | <tagged-tuple>_<tagged-list>
```

```
<list-bracket> → "[" "]"
               | "[" <list> "]"
```

## Catenation

```
<catenation> → <term>
             | <term> <catenation>
             | "~"
             | "~" <catenation>
```
`<term>` takes precedence over `<catenation>` on ambiguity, which can occur
with `~` when `<term>` matches `<text>`.

```
<term> → <text>
       | <value-bracket>
       | <dictionary-bracket>
       | <list-bracket>
       | <compact-tuple>
```

## Key

```
<key> → <word>
      | <closed-transcription>
```
