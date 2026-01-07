# Khi grammar

Space ` ` or asterisk `*` is optional whitespace. Underscore `_` is required
whitespace. Character within quotes or `<word>`, `<transcription>` or
`<text-block>` is a token.

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
        | <delimited-tuple>
        | <prefixed-tuple>
        | <prefixed-tagged-value>
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

## Tuple

```
<delimited-tuple>  → <catenation> <delimited-tuple'>
                   | <delimited-tuple'>
<delimited-tuple'> → "|" <tuple-element>
                   | "|" <tuple-element> <delimited-tuple'>
```

```
<prefixed-tuple> → <unit>":"_<value>
```

```
<tuple-element> → <catenation>
                | ":"<key> <catenation>
```

```
<unit> → "<"">"
```

## Tagged value

```
<prefixed-tagged-value> → <tag>":"_<value>
```

```
<compact-tagged-value> → <tag>
                       | <tag><arguments>
```

```
<arguments> → ":"<argument>
            | ":"<argument><arguments>
            | ":"<named-argument>
            | ":"<named-argument><arguments>
```
`<argument>` takes precedence over `<arguments>` on ambiguity with
`<tagged-arguments>`.

```
<named-argument> → <key>"="<argument>
```

```
<argument> → <string>
           | <value-bracket>
           | <dictionary-bracket>
           | <list-bracket>
           | <compact-tagged-value>
```

```
<tag> → "<"<key>">"
      | "<"<key>_<attributes> ">"
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
<section> → <value-header>_<value>
          | <dictionary-header>
          | <dictionary-header>_<regular-dictionary>
          | <list-header>
          | <list-header>_<list>
```

```
<value-header> → "{"<header-key>"}"":"
```

```
<dictionary-header> → "{"<header-key>"}"":"
```

```
<list-header> → "["<header-key>"]"":"
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
       | <tag-list>
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
<tabulated-list> → <delimited-tuple'> "|"
                 | <delimited-tuple'> "|"_<tabulated-list>
```

```
<tag-list> → <tagged-value>
           | <tagged-value>_<tag-list>
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
       | <compact-tagged-value>
       | <unit>
```

## Key

```
<key> → <word>
      | <closed-transcription>
```
