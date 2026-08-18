# Khi grammar

Space ` ` or asterisk `*` is optional whitespace. Underscore `_` is required
whitespace. Characters within quotes and `[word]`, `[closed-transcription]`,
`[open-transcription]` and `[text-block]` are tokens.

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
        | <barred-value>
        | <tagged-value>
```

```
<value-bracket> → "{" <value> "}"
```

## Text

```
<text> → <words>
       | [closed-transcription]
       | [open-transcription]
       | [text-block]
```

```
<words>  → [word]
         | [word] <words>
```

```
<confined-text> → [word]
                | [closed-transcription]
                | [open-transcription]
                | [text-block]
```

## Tuple

```
<barred-value>   → <catenation> <barred-value'>
                 | <barred-value'>
<barred-value'>  → "|" <catenation>
                 | "|" <catenation> <barred-value'>
```

```
<tagged-value> → <tag>":"_<value>
```

```
<confined-tuple> → <tag>
                 | <tag><confined-values>
```

```
<confined-values> → ":"<confined-catenation>
                  | ":"<confined-catenation><confined-values>
```
`<confined-catenation>` takes precedence over `<confined-values>` and `<term>` on ambiguity.

```
<confined-catenation> → <confined-value>
                      | [word]<confined-value>
```

```
<confined-value> → <confined-text>
                 | <value-bracket>
                 | <dictionary-bracket>
                 | <list-bracket>
                 | <confined-tuple>
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
            | <key>"="<confined-text>
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
                        | <section>_<sectioned-dictionary'>
```

```
<section> → <value-header>_<value>
          | <dictionary-header>
          | <dictionary-header>_<nonsectioned-dictionary>
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
             | <key>"[""]"
             | <key>_">"_<header-key>
```

```
<nonsectioned-dictionary> → <delimited-dictionary>
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
       | <tagged-list>
       | <tabular-list>
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
<tagged-list> → <tagged-tuple>
              | <tagged-tuple>_<tagged-list>
```

```
<tabular-list> → <barred-value'> "|"
               | <barred-value'> "|"_<tabular-list>
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
       | <confined-tuple>
```

## Key

```
<key> → [word]
      | [closed-transcription]
```
