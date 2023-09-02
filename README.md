# Khi

- is a data format and notation for arbitrary data, also known as a [metaformat](https://www.w3.org/standards/webarch/metaformats).
- can be used for configuration, markup, serialization, storage and interchange.
- was first and foremost designed with handwritten data in mind.
- has native support for the universal data structures found in other formats and
  programming languages, including:

## Links

- [**Khi**](https://github.com/khilang/khilang)**:** [reference](https://github.com/khilang/khilang/blob/master/reference.md), [editor & preprocessors](https://khilang.github.io/khi-editor)
- **Libraries:** [Rust](https://github.com/khilang/khi.rs), [Javascript](https://github.com/khilang/khi.js)

## Examples & showcase

The following are some examples of **Khi**-based formats and documents written in
them. It is shown how structured and unstructured data can coexist and form more
complex structures, and how it can be used for markup and configuration.

### Wiki article example

This is an example of a wiki article written in a **Khi**-based wiki article
format.

Such an article consists of significantly different kinds of data structures. It consists
of both structured data, such as values, dictionaries and lists, and unstructured
data, such as markup.

The purpose of this example is to show the capabilities of **Khi** when it is used
to its full extent. In particular, a wiki article usually contains both structured
data and unstructured data. Thus, this is a good example of how **Khi** can compose
both types into more complex hierarchical structures.

Additionally, this example showcases the **Khi** syntax. The readability, conciseness
and simplicity of the format should be compared to other formats encoding the same
data.

Notes:
- Macro application looks like this: `<macro>:arg1:arg2:...:argN`. Arguments are appended
  with a colon.
- Macros can also be expressed with tags. This is a macro with 1 argument: `<+macro>arg<-macro>`.
- The `@` macro inserts a link. It takes two arguments: the first argument is the article to link to, and the second is
  the link label that will appear in the article.
- The `title` macro takes no arguments and is substituted for the article title.
- The root element is a dictionary.

```
title: Aluminium;
short-desc: The <@>:element:{chemical element} aluminium.;
uuid: 0c5aacfe-d828-43c7-a530-12a802af1df4;
type: chemical-element;
tags: [metal; common];
key: aluminium;

chemical-symbol: Al;
atomic-number: 13;
stp-phase: solid;
melting-point: 933.47;
boiling-point: 2743;
density: 2.7;
electron-shells: [2; 8; 3];

# External references
ext-refs: {
  wikipedia: "https://en.wikipedia.org/wiki/Aluminium";
  snl: "https://snl.no/aluminium";
};

# Intra-wiki references
refs: {
  element: 740097ea-10fa-4203-b086-58632f099167;
  chemsym: 6e2f634c-f180-407a-b9ce-2138b412b248;
  atomnum: 1a5e1974-a78c-4820-afeb-79bef6974814;
  react: ab7d8a1f-c028-4466-9bb2-41a39d153241;
  aloxide: c1ff08e7-a88f-42d5-83c3-6adc4835a07b;
  stab: b3b13474-4fe3-4556-9568-925c066916a5;
  purity: 40786551-85c4-461c-ba6e-4d54d5863820;
  ion: effd5c7a-da31-4357-a94c-91343e9a05eb;
  metal: 84333088-cfcc-4e78-8d3f-7307dcab144b;
};

content: {

  <p> <@>:self:Aluminium is a <@>:element:{chemical element} with <@>:chemsym:{chemical symbol}
  <chemsym> and <@>:atomnum:{atomic number} <atomnum>.

  <p> In <@>:purity:pure form, it is a highly <@>:react:reactive <@>:metal:metal?,
  but normally a thin coat of <@>:aloxide:{aluminium oxide} forms on its
  surface, keeping it highly <@>:stab:stable?.

  <p> In nature, it occurs as the <@>:ion:ion <$>:{Al^{3+}}. It constitutes 8.2% of
  the earth's crust, making it the most common <@>:metal:metal found there.

  ...

};
```

### HTML preprocessor example

This is an example of a document written in a **Khi**-based **HTML** preprocessor input format. The preprocessor can
compile this document to **HTML**.

The purpose of this example is to showcase a **Khi**-based encoding of markup and **XML**-like structures.

Compare this document to the corresponding **HTML** document. In terms of verbosity and syntax noise, **Khi** allows
short and long closing tags. Both are useful in different cases. **Khi** does not require quotes around attribute
values.

Notes:
- In this format, regular markup tags and special macros are distinguished by the `!` symbol. Macros end with `!`
  while regular tags only consist of letters.
- The `doctype!` macro substitutes itself for `<!doctype html>`.

```
<@doctype> # @doctype is a preprocessor directive which inserts <!doctype html>.
<+html> # <+tag> is an opening tag and <-tag> or <-> its closing tag.
  <+head>
    <+title>Hello world!<->
    <+script src:script.js><->
  <-head>
  <+body>
    <+h1 id:main-heading>Hello world!<->
    <+p>Hello world!<-> # These two paragraph notations are equivalent.
    <p>:{Hello world!}
    <img src:frontpage.jpg>
    <+div class:dark-background><+p>
      This is a paragraph <br>
      with a line break.
      <+em class:italic-text>This text is italic.<->
    <-><->
    <colour>:{ # Dictionaries also compile to tags.
      name: Teal;
      hex: #008080;
      description: Blue-green mix;
    }
  <-body>
<-html>

```

### TeX preprocessor example

This is an example of a document written in a Khi-based **LaTeX** preprocessor
input format. The preprocessor can compile this document to **LaTeX**.

The purpose of this example is to show how Khi could encode **LaTeX**-like markup,
which involves handling hierarchies of text and commands.

Compare this document to the corresponding **LaTeX** document. They are similar, but
one benefit of the **Khi** document is that the arguments applied to a command can
be determined from syntax alone.

This format could be used to encode **LaTeX**-math, that is later displayed by **MathJax**.

Notes:
- Preprocessor macros start with `@` and regular commands consist only of letters.
- The `@tabulate-sq` automatically tabulates a square grid, such as a matrix. It takes a number and a sequence of
  tabulated values.

```
<documentclass>:article

<usepackage>:amsmath

<addtolength>:<jot>:1em # Controls the line spacing in align*

<begin>:document

<section>:Equations

  # Define a sum-range command.
  <@def>:<SumRn>:4:{ # @def is a preprocessor directive which handles the options in \newcommand.
    <sum>_{#1}^{#2`:#3} #4
  }

  <begin>:align* [
    | <SumRn>:k:0:100:k                                |
    | = 0 + 1 + 2 + <dots> + 99 + 100                  |
    | = (0 + 100) + (1 + 99) + <dots> (49 + 51) + 50   |
    | = 5050                                           |
  ] <end>:align*

  <begin>:align* [
    | <SumRn>:k:0:n:k                    |
    | = 0 + 1 + 2 + <dots> + (n - 1) + n |
    | = n <cfrac>:n:2 + <cfrac>:n:2      |
    | = <cfrac>:n^2:2 + <cfrac>:n:2      |
    | = n <cdot> <cfrac>:{n + 1}:2       |
  ] <end>:align*

<section>:Matrices

  <begin>:math
    <mathbf>:X = <begin>:bmatrix [
      |1|0|0|
      |0|1|0|
      |0|0|1|
    ] <end>:bmatrix
    <n>
    <mathbf>:X = <begin>:bmatrix [1|?|?; ?|1|?; ?|?|1] <end>:bmatrix
  <end>:math

<end>:document
```

### Material configuration example

This is an example of a Khi-based configuration format.

The purpose of this example is to show how Khi can be used for configuration
and to compare it to the corresponding **JSON** or **YAML** document.

- Khi allows comments, JSON does not.
- In terms of syntax noise, **JSON** requires quotes around all keys and text values.
- JSON requires the root element to be wrapped in brackets.

```
oak-planks: {
  name: Oak planks;
  description: Planks made from oak wood.;
  tags: [wood];
  price: 200;
};
birch-planks: {
  name: Birch planks;
  description: Planks made from birch wood.;
  tags: [wood];
  price: 200;
};
stone: {
  name: Stone;
  description: A solid material, but does not insulate well.;
  price: 100;
  tags: [heavy; stone];
};
marble: {
  name: Marble;
  price: 800;
  beauty: 2;
  tags: [heavy; stone; rich];
};
# Glass: this material is not available yet.
glass: {
  disabled; # This is equal to `disabled: ?;` or `disabled: {};`.
  name: Glass;
  price: 250;
};
```
