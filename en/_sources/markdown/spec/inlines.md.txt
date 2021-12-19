# Inlines

Inlines are parsed sequentially from the beginning of the character
stream to the end (left to right, in left-to-right languages).
Thus, for{panels}, in

````````````````````````````````{panels}
`hi`lo`
.
<p><code>hi</code>lo`</p>
````````````````````````````````

`hi` is parsed as code, leaving the backtick at the end as a literal
backtick.



## Code spans

A [backtick string](#@)
is a string of one or more backtick characters (`` ` ``) that is neither
preceded nor followed by a backtick.

A [code span](#@) begins with a backtick string and ends with
a backtick string of equal length.  The contents of the code span are
the characters between these two backtick strings, normalized in the
following ways:

- First, [line endings] are converted to [spaces].
- If the resulting string both begins *and* ends with a [space]
  character, but does not consist entirely of [space]
  characters, a single [space] character is removed from the
  front and back.  This allows you to include code that begins
  or ends with backtick characters, which must be separated by
  whitespace from the opening or closing backtick strings.

This is a simple code span:

````````````````````````````````{panels}
`foo`
.
<p><code>foo</code></p>
````````````````````````````````


Here two backticks are used, because the code contains a backtick.
This{panels} also illustrates stripping of a single leading and
trailing space:

````````````````````````````````{panels}
`` foo ` bar ``
.
<p><code>foo ` bar</code></p>
````````````````````````````````


This{panels} shows the motivation for stripping leading and trailing
spaces:

````````````````````````````````{panels}
` `` `
.
<p><code>``</code></p>
````````````````````````````````

Note that only *one* space is stripped:

````````````````````````````````{panels}
`  ``  `
.
<p><code> `` </code></p>
````````````````````````````````

The stripping only happens if the space is on both
sides of the string:

````````````````````````````````{panels}
` a`
.
<p><code> a</code></p>
````````````````````````````````

Only [spaces], and not [unicode whitespace] in general, are
stripped in this way:

````````````````````````````````{panels}
` b `
.
<p><code> b </code></p>
````````````````````````````````

No stripping occurs if the code span contains only spaces:

````````````````````````````````{panels}
` `
`  `
.
<p><code> </code>
<code>  </code></p>
````````````````````````````````


[Line endings] are treated like spaces:

````````````````````````````````{panels}
``
foo
bar  
baz
``
.
<p><code>foo bar   baz</code></p>
````````````````````````````````

````````````````````````````````{panels}
``
foo 
``
.
<p><code>foo </code></p>
````````````````````````````````


Interior spaces are not collapsed:

````````````````````````````````{panels}
`foo   bar 
baz`
.
<p><code>foo   bar  baz</code></p>
````````````````````````````````

Note that browsers will typically collapse consecutive spaces
when rendering `<code>` elements, so it is recommended that
the following CSS be used:

    code{white-space: pre-wrap;}


Note that backslash escapes do not work in code spans. All backslashes
are treated literally:

````````````````````````````````{panels}
`foo\`bar`
.
<p><code>foo\</code>bar`</p>
````````````````````````````````


Backslash escapes are never needed, because one can always choose a
string of *n* backtick characters as delimiters, where the code does
not contain any strings of exactly *n* backtick characters.

````````````````````````````````{panels}
``foo`bar``
.
<p><code>foo`bar</code></p>
````````````````````````````````

````````````````````````````````{panels}
` foo `` bar `
.
<p><code>foo `` bar</code></p>
````````````````````````````````


Code span backticks have higher precedence than any other inline
constructs except HTML tags and autolinks.  Thus, for{panels}, this is
not parsed as emphasized text, since the second `*` is part of a code
span:

````````````````````````````````{panels}
*foo`*`
.
<p>*foo<code>*</code></p>
````````````````````````````````


And this is not parsed as a link:

````````````````````````````````{panels}
[not a `link](/foo`)
.
<p>[not a <code>link](/foo</code>)</p>
````````````````````````````````


Code spans, HTML tags, and autolinks have the same precedence.
Thus, this is code:

````````````````````````````````{panels}
`<a href="`">`
.
<p><code>&lt;a href=&quot;</code>&quot;&gt;`</p>
````````````````````````````````


But this is an HTML tag:

````````````````````````````````{panels}
<a href="`">`
.
<p><a href="`">`</p>
````````````````````````````````


And this is code:

````````````````````````````````{panels}
`<http://foo.bar.`baz>`
.
<p><code>&lt;http://foo.bar.</code>baz&gt;`</p>
````````````````````````````````


But this is an autolink:

````````````````````````````````{panels}
<http://foo.bar.`baz>`
.
<p><a href="http://foo.bar.%60baz">http://foo.bar.`baz</a>`</p>
````````````````````````````````


When a backtick string is not closed by a matching backtick string,
we just have literal backticks:

````````````````````````````````{panels}
```foo``
.
<p>```foo``</p>
````````````````````````````````


````````````````````````````````{panels}
`foo
.
<p>`foo</p>
````````````````````````````````

The following case also illustrates the need for opening and
closing backtick strings to be equal in length:

````````````````````````````````{panels}
`foo``bar``
.
<p>`foo<code>bar</code></p>
````````````````````````````````


## Emphasis and strong emphasis

John Gruber's original [Markdown syntax
description](http://daringfireball.net/projects/markdown/syntax#em) says:

> Markdown treats asterisks (`*`) and underscores (`_`) as indicators of
> emphasis. Text wrapped with one `*` or `_` will be wrapped with an HTML
> `<em>` tag; double `*`'s or `_`'s will be wrapped with an HTML `<strong>`
> tag.

This is enough for most users, but these rules leave much undecided,
especially when it comes to nested emphasis.  The original
`Markdown.pl` test suite makes it clear that triple `***` and
`___` delimiters can be used for strong emphasis, and most
implementations have also allowed the following patterns:

``` markdown
***strong emph***
***strong** in emph*
***emph* in strong**
**in strong *emph***
*in emph **strong***
```

The following patterns are less widely supported, but the intent
is clear and they are useful (especially in contexts like bibliography
entries):

``` markdown
*emph *with emph* in it*
**strong **with strong** in it**
```

Many implementations have also restricted intraword emphasis to
the `*` forms, to avoid unwanted emphasis in words containing
internal underscores.  (It is best practice to put these in code
spans, but users often do not.)

``` markdown
internal emphasis: foo*bar*baz
no emphasis: foo_bar_baz
```

The rules given below capture all of these patterns, while allowing
for efficient parsing strategies that do not backtrack.

First, some definitions.  A [delimiter run](#@) is either
a sequence of one or more `*` characters that is not preceded or
followed by a non-backslash-escaped `*` character, or a sequence
of one or more `_` characters that is not preceded or followed by
a non-backslash-escaped `_` character.

A [left-flanking delimiter run](#@) is
a [delimiter run] that is (1) not followed by [Unicode whitespace],
and either (2a) not followed by a [Unicode punctuation character], or
(2b) followed by a [Unicode punctuation character] and
preceded by [Unicode whitespace] or a [Unicode punctuation character].
For purposes of this definition, the beginning and the end of
the line count as Unicode whitespace.

A [right-flanking delimiter run](#@) is
a [delimiter run] that is (1) not preceded by [Unicode whitespace],
and either (2a) not preceded by a [Unicode punctuation character], or
(2b) preceded by a [Unicode punctuation character] and
followed by [Unicode whitespace] or a [Unicode punctuation character].
For purposes of this definition, the beginning and the end of
the line count as Unicode whitespace.

Here are some{panels}s of delimiter runs.

  - left-flanking but not right-flanking:

    ```
    ***abc
      _abc
    **"abc"
     _"abc"
    ```

  - right-flanking but not left-flanking:

    ```
     abc***
     abc_
    "abc"**
    "abc"_
    ```

  - Both left and right-flanking:

    ```
     abc***def
    "abc"_"def"
    ```

  - Neither left nor right-flanking:

    ```
    abc *** def
    a _ b
    ```

(The idea of distinguishing left-flanking and right-flanking
delimiter runs based on the character before and the character
after comes from Roopesh Chander's
[vfmd](http://www.vfmd.org/vfmd-spec/specification/#procedure-for-identifying-emphasis-tags).
vfmd uses the terminology "emphasis indicator string" instead of "delimiter
run," and its rules for distinguishing left- and right-flanking runs
are a bit more complex than the ones given here.)

The following rules define emphasis and strong emphasis:

1.  A single `*` character [can open emphasis](#@)
    iff (if and only if) it is part of a [left-flanking delimiter run].

2.  A single `_` character [can open emphasis] iff
    it is part of a [left-flanking delimiter run]
    and either (a) not part of a [right-flanking delimiter run]
    or (b) part of a [right-flanking delimiter run]
    preceded by a [Unicode punctuation character].

3.  A single `*` character [can close emphasis](#@)
    iff it is part of a [right-flanking delimiter run].

4.  A single `_` character [can close emphasis] iff
    it is part of a [right-flanking delimiter run]
    and either (a) not part of a [left-flanking delimiter run]
    or (b) part of a [left-flanking delimiter run]
    followed by a [Unicode punctuation character].

5.  A double `**` [can open strong emphasis](#@)
    iff it is part of a [left-flanking delimiter run].

6.  A double `__` [can open strong emphasis] iff
    it is part of a [left-flanking delimiter run]
    and either (a) not part of a [right-flanking delimiter run]
    or (b) part of a [right-flanking delimiter run]
    preceded by a [Unicode punctuation character].

7.  A double `**` [can close strong emphasis](#@)
    iff it is part of a [right-flanking delimiter run].

8.  A double `__` [can close strong emphasis] iff
    it is part of a [right-flanking delimiter run]
    and either (a) not part of a [left-flanking delimiter run]
    or (b) part of a [left-flanking delimiter run]
    followed by a [Unicode punctuation character].

9.  Emphasis begins with a delimiter that [can open emphasis] and ends
    with a delimiter that [can close emphasis], and that uses the same
    character (`_` or `*`) as the opening delimiter.  The
    opening and closing delimiters must belong to separate
    [delimiter runs].  If one of the delimiters can both
    open and close emphasis, then the sum of the lengths of the
    delimiter runs containing the opening and closing delimiters
    must not be a multiple of 3 unless both lengths are
    multiples of 3.

10. Strong emphasis begins with a delimiter that
    [can open strong emphasis] and ends with a delimiter that
    [can close strong emphasis], and that uses the same character
    (`_` or `*`) as the opening delimiter.  The
    opening and closing delimiters must belong to separate
    [delimiter runs].  If one of the delimiters can both open
    and close strong emphasis, then the sum of the lengths of
    the delimiter runs containing the opening and closing
    delimiters must not be a multiple of 3 unless both lengths
    are multiples of 3.

11. A literal `*` character cannot occur at the beginning or end of
    `*`-delimited emphasis or `**`-delimited strong emphasis, unless it
    is backslash-escaped.

12. A literal `_` character cannot occur at the beginning or end of
    `_`-delimited emphasis or `__`-delimited strong emphasis, unless it
    is backslash-escaped.

Where rules 1--12 above are compatible with multiple parsings,
the following principles resolve ambiguity:

13. The number of nestings should be minimized. Thus, for{panels},
    an interpretation `<strong>...</strong>` is always preferred to
    `<em><em>...</em></em>`.

14. An interpretation `<em><strong>...</strong></em>` is always
    preferred to `<strong><em>...</em></strong>`.

15. When two potential emphasis or strong emphasis spans overlap,
    so that the second begins before the first ends and ends after
    the first ends, the first takes precedence. Thus, for{panels},
    `*foo _bar* baz_` is parsed as `<em>foo _bar</em> baz_` rather
    than `*foo <em>bar* baz</em>`.

16. When there are two potential emphasis or strong emphasis spans
    with the same closing delimiter, the shorter one (the one that
    opens later) takes precedence. Thus, for{panels},
    `**foo **bar baz**` is parsed as `**foo <strong>bar baz</strong>`
    rather than `<strong>foo **bar baz</strong>`.

17. Inline code spans, links, images, and HTML tags group more tightly
    than emphasis.  So, when there is a choice between an interpretation
    that contains one of these elements and one that does not, the
    former always wins.  Thus, for{panels}, `*[foo*](bar)` is
    parsed as `*<a href="bar">foo*</a>` rather than as
    `<em>[foo</em>](bar)`.

These rules can be illustrated through a series of{panels}s.

Rule 1:

````````````````````````````````{panels}
*foo bar*
.
<p><em>foo bar</em></p>
````````````````````````````````


This is not emphasis, because the opening `*` is followed by
whitespace, and hence not part of a [left-flanking delimiter run]:

````````````````````````````````{panels}
a * foo bar*
.
<p>a * foo bar*</p>
````````````````````````````````


This is not emphasis, because the opening `*` is preceded
by an alphanumeric and followed by punctuation, and hence
not part of a [left-flanking delimiter run]:

````````````````````````````````{panels}
a*"foo"*
.
<p>a*&quot;foo&quot;*</p>
````````````````````````````````


Unicode nonbreaking spaces count as whitespace, too:

````````````````````````````````{panels}
* a *
.
<p>* a *</p>
````````````````````````````````


Intraword emphasis with `*` is permitted:

````````````````````````````````{panels}
foo*bar*
.
<p>foo<em>bar</em></p>
````````````````````````````````


````````````````````````````````{panels}
5*6*78
.
<p>5<em>6</em>78</p>
````````````````````````````````


Rule 2:

````````````````````````````````{panels}
_foo bar_
.
<p><em>foo bar</em></p>
````````````````````````````````


This is not emphasis, because the opening `_` is followed by
whitespace:

````````````````````````````````{panels}
_ foo bar_
.
<p>_ foo bar_</p>
````````````````````````````````


This is not emphasis, because the opening `_` is preceded
by an alphanumeric and followed by punctuation:

````````````````````````````````{panels}
a_"foo"_
.
<p>a_&quot;foo&quot;_</p>
````````````````````````````````


Emphasis with `_` is not allowed inside words:

````````````````````````````````{panels}
foo_bar_
.
<p>foo_bar_</p>
````````````````````````````````


````````````````````````````````{panels}
5_6_78
.
<p>5_6_78</p>
````````````````````````````````


````````````````````````````````{panels}
пристаням_стремятся_
.
<p>пристаням_стремятся_</p>
````````````````````````````````


Here `_` does not generate emphasis, because the first delimiter run
is right-flanking and the second left-flanking:

````````````````````````````````{panels}
aa_"bb"_cc
.
<p>aa_&quot;bb&quot;_cc</p>
````````````````````````````````


This is emphasis, even though the opening delimiter is
both left- and right-flanking, because it is preceded by
punctuation:

````````````````````````````````{panels}
foo-_(bar)_
.
<p>foo-<em>(bar)</em></p>
````````````````````````````````


Rule 3:

This is not emphasis, because the closing delimiter does
not match the opening delimiter:

````````````````````````````````{panels}
_foo*
.
<p>_foo*</p>
````````````````````````````````


This is not emphasis, because the closing `*` is preceded by
whitespace:

````````````````````````````````{panels}
*foo bar *
.
<p>*foo bar *</p>
````````````````````````````````


A line ending also counts as whitespace:

````````````````````````````````{panels}
*foo bar
*
.
<p>*foo bar
*</p>
````````````````````````````````


This is not emphasis, because the second `*` is
preceded by punctuation and followed by an alphanumeric
(hence it is not part of a [right-flanking delimiter run]:

````````````````````````````````{panels}
*(*foo)
.
<p>*(*foo)</p>
````````````````````````````````


The point of this restriction is more easily appreciated
with this{panels}:

````````````````````````````````{panels}
*(*foo*)*
.
<p><em>(<em>foo</em>)</em></p>
````````````````````````````````


Intraword emphasis with `*` is allowed:

````````````````````````````````{panels}
*foo*bar
.
<p><em>foo</em>bar</p>
````````````````````````````````



Rule 4:

This is not emphasis, because the closing `_` is preceded by
whitespace:

````````````````````````````````{panels}
_foo bar _
.
<p>_foo bar _</p>
````````````````````````````````


This is not emphasis, because the second `_` is
preceded by punctuation and followed by an alphanumeric:

````````````````````````````````{panels}
_(_foo)
.
<p>_(_foo)</p>
````````````````````````````````


This is emphasis within emphasis:

````````````````````````````````{panels}
_(_foo_)_
.
<p><em>(<em>foo</em>)</em></p>
````````````````````````````````


Intraword emphasis is disallowed for `_`:

````````````````````````````````{panels}
_foo_bar
.
<p>_foo_bar</p>
````````````````````````````````


````````````````````````````````{panels}
_пристаням_стремятся
.
<p>_пристаням_стремятся</p>
````````````````````````````````


````````````````````````````````{panels}
_foo_bar_baz_
.
<p><em>foo_bar_baz</em></p>
````````````````````````````````


This is emphasis, even though the closing delimiter is
both left- and right-flanking, because it is followed by
punctuation:

````````````````````````````````{panels}
_(bar)_.
.
<p><em>(bar)</em>.</p>
````````````````````````````````


Rule 5:

````````````````````````````````{panels}
**foo bar**
.
<p><strong>foo bar</strong></p>
````````````````````````````````


This is not strong emphasis, because the opening delimiter is
followed by whitespace:

````````````````````````````````{panels}
** foo bar**
.
<p>** foo bar**</p>
````````````````````````````````


This is not strong emphasis, because the opening `**` is preceded
by an alphanumeric and followed by punctuation, and hence
not part of a [left-flanking delimiter run]:

````````````````````````````````{panels}
a**"foo"**
.
<p>a**&quot;foo&quot;**</p>
````````````````````````````````


Intraword strong emphasis with `**` is permitted:

````````````````````````````````{panels}
foo**bar**
.
<p>foo<strong>bar</strong></p>
````````````````````````````````


Rule 6:

````````````````````````````````{panels}
__foo bar__
.
<p><strong>foo bar</strong></p>
````````````````````````````````


This is not strong emphasis, because the opening delimiter is
followed by whitespace:

````````````````````````````````{panels}
__ foo bar__
.
<p>__ foo bar__</p>
````````````````````````````````


A line ending counts as whitespace:
````````````````````````````````{panels}
__
foo bar__
.
<p>__
foo bar__</p>
````````````````````````````````


This is not strong emphasis, because the opening `__` is preceded
by an alphanumeric and followed by punctuation:

````````````````````````````````{panels}
a__"foo"__
.
<p>a__&quot;foo&quot;__</p>
````````````````````````````````


Intraword strong emphasis is forbidden with `__`:

````````````````````````````````{panels}
foo__bar__
.
<p>foo__bar__</p>
````````````````````````````````


````````````````````````````````{panels}
5__6__78
.
<p>5__6__78</p>
````````````````````````````````


````````````````````````````````{panels}
пристаням__стремятся__
.
<p>пристаням__стремятся__</p>
````````````````````````````````


````````````````````````````````{panels}
__foo, __bar__, baz__
.
<p><strong>foo, <strong>bar</strong>, baz</strong></p>
````````````````````````````````


This is strong emphasis, even though the opening delimiter is
both left- and right-flanking, because it is preceded by
punctuation:

````````````````````````````````{panels}
foo-__(bar)__
.
<p>foo-<strong>(bar)</strong></p>
````````````````````````````````



Rule 7:

This is not strong emphasis, because the closing delimiter is preceded
by whitespace:

````````````````````````````````{panels}
**foo bar **
.
<p>**foo bar **</p>
````````````````````````````````


(Nor can it be interpreted as an emphasized `*foo bar *`, because of
Rule 11.)

This is not strong emphasis, because the second `**` is
preceded by punctuation and followed by an alphanumeric:

````````````````````````````````{panels}
**(**foo)
.
<p>**(**foo)</p>
````````````````````````````````


The point of this restriction is more easily appreciated
with these{panels}s:

````````````````````````````````{panels}
*(**foo**)*
.
<p><em>(<strong>foo</strong>)</em></p>
````````````````````````````````


````````````````````````````````{panels}
**Gomphocarpus (*Gomphocarpus physocarpus*, syn.
*Asclepias physocarpa*)**
.
<p><strong>Gomphocarpus (<em>Gomphocarpus physocarpus</em>, syn.
<em>Asclepias physocarpa</em>)</strong></p>
````````````````````````````````


````````````````````````````````{panels}
**foo "*bar*" foo**
.
<p><strong>foo &quot;<em>bar</em>&quot; foo</strong></p>
````````````````````````````````


Intraword emphasis:

````````````````````````````````{panels}
**foo**bar
.
<p><strong>foo</strong>bar</p>
````````````````````````````````


Rule 8:

This is not strong emphasis, because the closing delimiter is
preceded by whitespace:

````````````````````````````````{panels}
__foo bar __
.
<p>__foo bar __</p>
````````````````````````````````


This is not strong emphasis, because the second `__` is
preceded by punctuation and followed by an alphanumeric:

````````````````````````````````{panels}
__(__foo)
.
<p>__(__foo)</p>
````````````````````````````````


The point of this restriction is more easily appreciated
with this{panels}:

````````````````````````````````{panels}
_(__foo__)_
.
<p><em>(<strong>foo</strong>)</em></p>
````````````````````````````````


Intraword strong emphasis is forbidden with `__`:

````````````````````````````````{panels}
__foo__bar
.
<p>__foo__bar</p>
````````````````````````````````


````````````````````````````````{panels}
__пристаням__стремятся
.
<p>__пристаням__стремятся</p>
````````````````````````````````


````````````````````````````````{panels}
__foo__bar__baz__
.
<p><strong>foo__bar__baz</strong></p>
````````````````````````````````


This is strong emphasis, even though the closing delimiter is
both left- and right-flanking, because it is followed by
punctuation:

````````````````````````````````{panels}
__(bar)__.
.
<p><strong>(bar)</strong>.</p>
````````````````````````````````


Rule 9:

Any nonempty sequence of inline elements can be the contents of an
emphasized span.

````````````````````````````````{panels}
*foo [bar](/url)*
.
<p><em>foo <a href="/url">bar</a></em></p>
````````````````````````````````


````````````````````````````````{panels}
*foo
bar*
.
<p><em>foo
bar</em></p>
````````````````````````````````


In particular, emphasis and strong emphasis can be nested
inside emphasis:

````````````````````````````````{panels}
_foo __bar__ baz_
.
<p><em>foo <strong>bar</strong> baz</em></p>
````````````````````````````````


````````````````````````````````{panels}
_foo _bar_ baz_
.
<p><em>foo <em>bar</em> baz</em></p>
````````````````````````````````


````````````````````````````````{panels}
__foo_ bar_
.
<p><em><em>foo</em> bar</em></p>
````````````````````````````````


````````````````````````````````{panels}
*foo *bar**
.
<p><em>foo <em>bar</em></em></p>
````````````````````````````````


````````````````````````````````{panels}
*foo **bar** baz*
.
<p><em>foo <strong>bar</strong> baz</em></p>
````````````````````````````````

````````````````````````````````{panels}
*foo**bar**baz*
.
<p><em>foo<strong>bar</strong>baz</em></p>
````````````````````````````````

Note that in the preceding case, the interpretation

``` markdown
<p><em>foo</em><em>bar<em></em>baz</em></p>
```


is precluded by the condition that a delimiter that
can both open and close (like the `*` after `foo`)
cannot form emphasis if the sum of the lengths of
the delimiter runs containing the opening and
closing delimiters is a multiple of 3 unless
both lengths are multiples of 3.


For the same reason, we don't get two consecutive
emphasis sections in this{panels}:

````````````````````````````````{panels}
*foo**bar*
.
<p><em>foo**bar</em></p>
````````````````````````````````


The same condition ensures that the following
cases are all strong emphasis nested inside
emphasis, even when the interior whitespace is
omitted:


````````````````````````````````{panels}
***foo** bar*
.
<p><em><strong>foo</strong> bar</em></p>
````````````````````````````````


````````````````````````````````{panels}
*foo **bar***
.
<p><em>foo <strong>bar</strong></em></p>
````````````````````````````````


````````````````````````````````{panels}
*foo**bar***
.
<p><em>foo<strong>bar</strong></em></p>
````````````````````````````````


When the lengths of the interior closing and opening
delimiter runs are *both* multiples of 3, though,
they can match to create emphasis:

````````````````````````````````{panels}
foo***bar***baz
.
<p>foo<em><strong>bar</strong></em>baz</p>
````````````````````````````````

````````````````````````````````{panels}
foo******bar*********baz
.
<p>foo<strong><strong><strong>bar</strong></strong></strong>***baz</p>
````````````````````````````````


Indefinite levels of nesting are possible:

````````````````````````````````{panels}
*foo **bar *baz* bim** bop*
.
<p><em>foo <strong>bar <em>baz</em> bim</strong> bop</em></p>
````````````````````````````````


````````````````````````````````{panels}
*foo [*bar*](/url)*
.
<p><em>foo <a href="/url"><em>bar</em></a></em></p>
````````````````````````````````


There can be no empty emphasis or strong emphasis:

````````````````````````````````{panels}
** is not an empty emphasis
.
<p>** is not an empty emphasis</p>
````````````````````````````````


````````````````````````````````{panels}
**** is not an empty strong emphasis
.
<p>**** is not an empty strong emphasis</p>
````````````````````````````````



Rule 10:

Any nonempty sequence of inline elements can be the contents of an
strongly emphasized span.

````````````````````````````````{panels}
**foo [bar](/url)**
.
<p><strong>foo <a href="/url">bar</a></strong></p>
````````````````````````````````


````````````````````````````````{panels}
**foo
bar**
.
<p><strong>foo
bar</strong></p>
````````````````````````````````


In particular, emphasis and strong emphasis can be nested
inside strong emphasis:

````````````````````````````````{panels}
__foo _bar_ baz__
.
<p><strong>foo <em>bar</em> baz</strong></p>
````````````````````````````````


````````````````````````````````{panels}
__foo __bar__ baz__
.
<p><strong>foo <strong>bar</strong> baz</strong></p>
````````````````````````````````


````````````````````````````````{panels}
____foo__ bar__
.
<p><strong><strong>foo</strong> bar</strong></p>
````````````````````````````````


````````````````````````````````{panels}
**foo **bar****
.
<p><strong>foo <strong>bar</strong></strong></p>
````````````````````````````````


````````````````````````````````{panels}
**foo *bar* baz**
.
<p><strong>foo <em>bar</em> baz</strong></p>
````````````````````````````````


````````````````````````````````{panels}
**foo*bar*baz**
.
<p><strong>foo<em>bar</em>baz</strong></p>
````````````````````````````````


````````````````````````````````{panels}
***foo* bar**
.
<p><strong><em>foo</em> bar</strong></p>
````````````````````````````````


````````````````````````````````{panels}
**foo *bar***
.
<p><strong>foo <em>bar</em></strong></p>
````````````````````````````````


Indefinite levels of nesting are possible:

````````````````````````````````{panels}
**foo *bar **baz**
bim* bop**
.
<p><strong>foo <em>bar <strong>baz</strong>
bim</em> bop</strong></p>
````````````````````````````````


````````````````````````````````{panels}
**foo [*bar*](/url)**
.
<p><strong>foo <a href="/url"><em>bar</em></a></strong></p>
````````````````````````````````


There can be no empty emphasis or strong emphasis:

````````````````````````````````{panels}
__ is not an empty emphasis
.
<p>__ is not an empty emphasis</p>
````````````````````````````````


````````````````````````````````{panels}
____ is not an empty strong emphasis
.
<p>____ is not an empty strong emphasis</p>
````````````````````````````````



Rule 11:

````````````````````````````````{panels}
foo ***
.
<p>foo ***</p>
````````````````````````````````


````````````````````````````````{panels}
foo *\**
.
<p>foo <em>*</em></p>
````````````````````````````````


````````````````````````````````{panels}
foo *_*
.
<p>foo <em>_</em></p>
````````````````````````````````


````````````````````````````````{panels}
foo *****
.
<p>foo *****</p>
````````````````````````````````


````````````````````````````````{panels}
foo **\***
.
<p>foo <strong>*</strong></p>
````````````````````````````````


````````````````````````````````{panels}
foo **_**
.
<p>foo <strong>_</strong></p>
````````````````````````````````


Note that when delimiters do not match evenly, Rule 11 determines
that the excess literal `*` characters will appear outside of the
emphasis, rather than inside it:

````````````````````````````````{panels}
**foo*
.
<p>*<em>foo</em></p>
````````````````````````````````


````````````````````````````````{panels}
*foo**
.
<p><em>foo</em>*</p>
````````````````````````````````


````````````````````````````````{panels}
***foo**
.
<p>*<strong>foo</strong></p>
````````````````````````````````


````````````````````````````````{panels}
****foo*
.
<p>***<em>foo</em></p>
````````````````````````````````


````````````````````````````````{panels}
**foo***
.
<p><strong>foo</strong>*</p>
````````````````````````````````


````````````````````````````````{panels}
*foo****
.
<p><em>foo</em>***</p>
````````````````````````````````



Rule 12:

````````````````````````````````{panels}
foo ___
.
<p>foo ___</p>
````````````````````````````````


````````````````````````````````{panels}
foo _\__
.
<p>foo <em>_</em></p>
````````````````````````````````


````````````````````````````````{panels}
foo _*_
.
<p>foo <em>*</em></p>
````````````````````````````````


````````````````````````````````{panels}
foo _____
.
<p>foo _____</p>
````````````````````````````````


````````````````````````````````{panels}
foo __\___
.
<p>foo <strong>_</strong></p>
````````````````````````````````


````````````````````````````````{panels}
foo __*__
.
<p>foo <strong>*</strong></p>
````````````````````````````````


````````````````````````````````{panels}
__foo_
.
<p>_<em>foo</em></p>
````````````````````````````````


Note that when delimiters do not match evenly, Rule 12 determines
that the excess literal `_` characters will appear outside of the
emphasis, rather than inside it:

````````````````````````````````{panels}
_foo__
.
<p><em>foo</em>_</p>
````````````````````````````````


````````````````````````````````{panels}
___foo__
.
<p>_<strong>foo</strong></p>
````````````````````````````````


````````````````````````````````{panels}
____foo_
.
<p>___<em>foo</em></p>
````````````````````````````````


````````````````````````````````{panels}
__foo___
.
<p><strong>foo</strong>_</p>
````````````````````````````````


````````````````````````````````{panels}
_foo____
.
<p><em>foo</em>___</p>
````````````````````````````````


Rule 13 implies that if you want emphasis nested directly inside
emphasis, you must use different delimiters:

````````````````````````````````{panels}
**foo**
.
<p><strong>foo</strong></p>
````````````````````````````````


````````````````````````````````{panels}
*_foo_*
.
<p><em><em>foo</em></em></p>
````````````````````````````````


````````````````````````````````{panels}
__foo__
.
<p><strong>foo</strong></p>
````````````````````````````````


````````````````````````````````{panels}
_*foo*_
.
<p><em><em>foo</em></em></p>
````````````````````````````````


However, strong emphasis within strong emphasis is possible without
switching delimiters:

````````````````````````````````{panels}
****foo****
.
<p><strong><strong>foo</strong></strong></p>
````````````````````````````````


````````````````````````````````{panels}
____foo____
.
<p><strong><strong>foo</strong></strong></p>
````````````````````````````````



Rule 13 can be applied to arbitrarily long sequences of
delimiters:

````````````````````````````````{panels}
******foo******
.
<p><strong><strong><strong>foo</strong></strong></strong></p>
````````````````````````````````


Rule 14:

````````````````````````````````{panels}
***foo***
.
<p><em><strong>foo</strong></em></p>
````````````````````````````````


````````````````````````````````{panels}
_____foo_____
.
<p><em><strong><strong>foo</strong></strong></em></p>
````````````````````````````````


Rule 15:

````````````````````````````````{panels}
*foo _bar* baz_
.
<p><em>foo _bar</em> baz_</p>
````````````````````````````````


````````````````````````````````{panels}
*foo __bar *baz bim__ bam*
.
<p><em>foo <strong>bar *baz bim</strong> bam</em></p>
````````````````````````````````


Rule 16:

````````````````````````````````{panels}
**foo **bar baz**
.
<p>**foo <strong>bar baz</strong></p>
````````````````````````````````


````````````````````````````````{panels}
*foo *bar baz*
.
<p>*foo <em>bar baz</em></p>
````````````````````````````````


Rule 17:

````````````````````````````````{panels}
*[bar*](/url)
.
<p>*<a href="/url">bar*</a></p>
````````````````````````````````


````````````````````````````````{panels}
_foo [bar_](/url)
.
<p>_foo <a href="/url">bar_</a></p>
````````````````````````````````


````````````````````````````````{panels}
*<img src="foo" title="*"/>
.
<p>*<img src="foo" title="*"/></p>
````````````````````````````````


````````````````````````````````{panels}
**<a href="**">
.
<p>**<a href="**"></p>
````````````````````````````````


````````````````````````````````{panels}
__<a href="__">
.
<p>__<a href="__"></p>
````````````````````````````````


````````````````````````````````{panels}
*a `*`*
.
<p><em>a <code>*</code></em></p>
````````````````````````````````


````````````````````````````````{panels}
_a `_`_
.
<p><em>a <code>_</code></em></p>
````````````````````````````````


````````````````````````````````{panels}
**a<http://foo.bar/?q=**>
.
<p>**a<a href="http://foo.bar/?q=**">http://foo.bar/?q=**</a></p>
````````````````````````````````


````````````````````````````````{panels}
__a<http://foo.bar/?q=__>
.
<p>__a<a href="http://foo.bar/?q=__">http://foo.bar/?q=__</a></p>
````````````````````````````````



## Links

A link contains [link text] (the visible text), a [link destination]
(the URI that is the link destination), and optionally a [link title].
There are two basic kinds of links in Markdown.  In [inline links] the
destination and title are given immediately after the link text.  In
[reference links] the destination and title are defined elsewhere in
the document.

A [link text](#@) consists of a sequence of zero or more
inline elements enclosed by square brackets (`[` and `]`).  The
following rules apply:

- Links may not contain other links, at any level of nesting. If
  multiple otherwise valid link definitions appear nested inside each
  other, the inner-most definition is used.

- Brackets are allowed in the [link text] only if (a) they
  are backslash-escaped or (b) they appear as a matched pair of brackets,
  with an open bracket `[`, a sequence of zero or more inlines, and
  a close bracket `]`.

- Backtick [code spans], [autolinks], and raw [HTML tags] bind more tightly
  than the brackets in link text.  Thus, for{panels},
  `` [foo`]` `` could not be a link text, since the second `]`
  is part of a code span.

- The brackets in link text bind more tightly than markers for
  [emphasis and strong emphasis]. Thus, for{panels}, `*[foo*](url)` is a link.

A [link destination](#@) consists of either

- a sequence of zero or more characters between an opening `<` and a
  closing `>` that contains no line endings or unescaped
  `<` or `>` characters, or

- a nonempty sequence of characters that does not start with `<`,
  does not include [ASCII control characters][ASCII control character]
  or [space] character, and includes parentheses only if (a) they are
  backslash-escaped or (b) they are part of a balanced pair of
  unescaped parentheses.
  (Implementations may impose limits on parentheses nesting to
  avoid performance issues, but at least three levels of nesting
  should be supported.)

A [link title](#@)  consists of either

- a sequence of zero or more characters between straight double-quote
  characters (`"`), including a `"` character only if it is
  backslash-escaped, or

- a sequence of zero or more characters between straight single-quote
  characters (`'`), including a `'` character only if it is
  backslash-escaped, or

- a sequence of zero or more characters between matching parentheses
  (`(...)`), including a `(` or `)` character only if it is
  backslash-escaped.

Although [link titles] may span multiple lines, they may not contain
a [blank line].

An [inline link](#@) consists of a [link text] followed immediately
by a left parenthesis `(`, an optional [link destination], an optional
[link title], and a right parenthesis `)`.
These four components may be separated by spaces, tabs, and up to one line
ending.
If both [link destination] and [link title] are present, they *must* be
separated by spaces, tabs, and up to one line ending.

The link's text consists of the inlines contained
in the [link text] (excluding the enclosing square brackets).
The link's URI consists of the link destination, excluding enclosing
`<...>` if present, with backslash-escapes in effect as described
above.  The link's title consists of the link title, excluding its
enclosing delimiters, with backslash-escapes in effect as described
above.

Here is a simple inline link:

````````````````````````````````{panels}
[link](/uri "title")
.
<p><a href="/uri" title="title">link</a></p>
````````````````````````````````


The title, the link text and even 
the destination may be omitted:

````````````````````````````````{panels}
[link](/uri)
.
<p><a href="/uri">link</a></p>
````````````````````````````````

````````````````````````````````{panels}
[](./target.md)
.
<p><a href="./target.md"></a></p>
````````````````````````````````


````````````````````````````````{panels}
[link]()
.
<p><a href="">link</a></p>
````````````````````````````````


````````````````````````````````{panels}
[link](<>)
.
<p><a href="">link</a></p>
````````````````````````````````


````````````````````````````````{panels}
[]()
.
<p><a href=""></a></p>
````````````````````````````````

The destination can only contain spaces if it is
enclosed in pointy brackets:

````````````````````````````````{panels}
[link](/my uri)
.
<p>[link](/my uri)</p>
````````````````````````````````

````````````````````````````````{panels}
[link](</my uri>)
.
<p><a href="/my%20uri">link</a></p>
````````````````````````````````

The destination cannot contain line endings,
even if enclosed in pointy brackets:

````````````````````````````````{panels}
[link](foo
bar)
.
<p>[link](foo
bar)</p>
````````````````````````````````

````````````````````````````````{panels}
[link](<foo
bar>)
.
<p>[link](<foo
bar>)</p>
````````````````````````````````

The destination can contain `)` if it is enclosed
in pointy brackets:

````````````````````````````````{panels}
[a](<b)c>)
.
<p><a href="b)c">a</a></p>
````````````````````````````````

Pointy brackets that enclose links must be unescaped:

````````````````````````````````{panels}
[link](<foo\>)
.
<p>[link](&lt;foo&gt;)</p>
````````````````````````````````

These are not links, because the opening pointy bracket
is not matched properly:

````````````````````````````````{panels}
[a](<b)c
[a](<b)c>
[a](<b>c)
.
<p>[a](&lt;b)c
[a](&lt;b)c&gt;
[a](<b>c)</p>
````````````````````````````````

Parentheses inside the link destination may be escaped:

````````````````````````````````{panels}
[link](\(foo\))
.
<p><a href="(foo)">link</a></p>
````````````````````````````````

Any number of parentheses are allowed without escaping, as long as they are
balanced:

````````````````````````````````{panels}
[link](foo(and(bar)))
.
<p><a href="foo(and(bar))">link</a></p>
````````````````````````````````

However, if you have unbalanced parentheses, you need to escape or use the
`<...>` form:

````````````````````````````````{panels}
[link](foo(and(bar))
.
<p>[link](foo(and(bar))</p>
````````````````````````````````


````````````````````````````````{panels}
[link](foo\(and\(bar\))
.
<p><a href="foo(and(bar)">link</a></p>
````````````````````````````````


````````````````````````````````{panels}
[link](<foo(and(bar)>)
.
<p><a href="foo(and(bar)">link</a></p>
````````````````````````````````


Parentheses and other symbols can also be escaped, as usual
in Markdown:

````````````````````````````````{panels}
[link](foo\)\:)
.
<p><a href="foo):">link</a></p>
````````````````````````````````


A link can contain fragment identifiers and queries:

````````````````````````````````{panels}
[link](#fragment)

[link](http://example.com#fragment)

[link](http://example.com?foo=3#frag)
.
<p><a href="#fragment">link</a></p>
<p><a href="http://example.com#fragment">link</a></p>
<p><a href="http://example.com?foo=3#frag">link</a></p>
````````````````````````````````


Note that a backslash before a non-escapable character is
just a backslash:

````````````````````````````````{panels}
[link](foo\bar)
.
<p><a href="foo%5Cbar">link</a></p>
````````````````````````````````


URL-escaping should be left alone inside the destination, as all
URL-escaped characters are also valid URL characters. Entity and
numerical character references in the destination will be parsed
into the corresponding Unicode code points, as usual.  These may
be optionally URL-escaped when written as HTML, but this spec
does not enforce any particular policy for rendering URLs in
HTML or other formats.  Renderers may make different decisions
about how to escape or normalize URLs in the output.

````````````````````````````````{panels}
[link](foo%20b&auml;)
.
<p><a href="foo%20b%C3%A4">link</a></p>
````````````````````````````````


Note that, because titles can often be parsed as destinations,
if you try to omit the destination and keep the title, you'll
get unexpected results:

````````````````````````````````{panels}
[link]("title")
.
<p><a href="%22title%22">link</a></p>
````````````````````````````````


Titles may be in single quotes, double quotes, or parentheses:

````````````````````````````````{panels}
[link](/url "title")
[link](/url 'title')
[link](/url (title))
.
<p><a href="/url" title="title">link</a>
<a href="/url" title="title">link</a>
<a href="/url" title="title">link</a></p>
````````````````````````````````


Backslash escapes and entity and numeric character references
may be used in titles:

````````````````````````````````{panels}
[link](/url "title \"&quot;")
.
<p><a href="/url" title="title &quot;&quot;">link</a></p>
````````````````````````````````


Titles must be separated from the link using spaces, tabs, and up to one line
ending.
Other [Unicode whitespace] like non-breaking space doesn't work.

````````````````````````````````{panels}
[link](/url "title")
.
<p><a href="/url%C2%A0%22title%22">link</a></p>
````````````````````````````````


Nested balanced quotes are not allowed without escaping:

````````````````````````````````{panels}
[link](/url "title "and" title")
.
<p>[link](/url &quot;title &quot;and&quot; title&quot;)</p>
````````````````````````````````


But it is easy to work around this by using a different quote type:

````````````````````````````````{panels}
[link](/url 'title "and" title')
.
<p><a href="/url" title="title &quot;and&quot; title">link</a></p>
````````````````````````````````


(Note:  `Markdown.pl` did allow double quotes inside a double-quoted
title, and its test suite included a test demonstrating this.
But it is hard to see a good rationale for the extra complexity this
brings, since there are already many ways---backslash escaping,
entity and numeric character references, or using a different
quote type for the enclosing title---to write titles containing
double quotes.  `Markdown.pl`'s handling of titles has a number
of other strange features.  For{panels}, it allows single-quoted
titles in inline links, but not reference links.  And, in
reference links but not inline links, it allows a title to begin
with `"` and end with `)`.  `Markdown.pl` 1.0.1 even allows
titles with no closing quotation mark, though 1.0.2b8 does not.
It seems preferable to adopt a simple, rational rule that works
the same way in inline links and link reference definitions.)

Spaces, tabs, and up to one line ending is allowed around the destination and
title:

````````````````````````````````{panels}
[link](   /uri
  "title"  )
.
<p><a href="/uri" title="title">link</a></p>
````````````````````````````````


But it is not allowed between the link text and the
following parenthesis:

````````````````````````````````{panels}
[link] (/uri)
.
<p>[link] (/uri)</p>
````````````````````````````````


The link text may contain balanced brackets, but not unbalanced ones,
unless they are escaped:

````````````````````````````````{panels}
[link [foo [bar]]](/uri)
.
<p><a href="/uri">link [foo [bar]]</a></p>
````````````````````````````````


````````````````````````````````{panels}
[link] bar](/uri)
.
<p>[link] bar](/uri)</p>
````````````````````````````````


````````````````````````````````{panels}
[link [bar](/uri)
.
<p>[link <a href="/uri">bar</a></p>
````````````````````````````````


````````````````````````````````{panels}
[link \[bar](/uri)
.
<p><a href="/uri">link [bar</a></p>
````````````````````````````````


The link text may contain inline content:

````````````````````````````````{panels}
[link *foo **bar** `#`*](/uri)
.
<p><a href="/uri">link <em>foo <strong>bar</strong> <code>#</code></em></a></p>
````````````````````````````````


````````````````````````````````{panels}
[![moon](moon.jpg)](/uri)
.
<p><a href="/uri"><img src="moon.jpg" alt="moon" /></a></p>
````````````````````````````````


However, links may not contain other links, at any level of nesting.

````````````````````````````````{panels}
[foo [bar](/uri)](/uri)
.
<p>[foo <a href="/uri">bar</a>](/uri)</p>
````````````````````````````````


````````````````````````````````{panels}
[foo *[bar [baz](/uri)](/uri)*](/uri)
.
<p>[foo <em>[bar <a href="/uri">baz</a>](/uri)</em>](/uri)</p>
````````````````````````````````


````````````````````````````````{panels}
![[[foo](uri1)](uri2)](uri3)
.
<p><img src="uri3" alt="[foo](uri2)" /></p>
````````````````````````````````


These cases illustrate the precedence of link text grouping over
emphasis grouping:

````````````````````````````````{panels}
*[foo*](/uri)
.
<p>*<a href="/uri">foo*</a></p>
````````````````````````````````


````````````````````````````````{panels}
[foo *bar](baz*)
.
<p><a href="baz*">foo *bar</a></p>
````````````````````````````````


Note that brackets that *aren't* part of links do not take
precedence:

````````````````````````````````{panels}
*foo [bar* baz]
.
<p><em>foo [bar</em> baz]</p>
````````````````````````````````


These cases illustrate the precedence of HTML tags, code spans,
and autolinks over link grouping:

````````````````````````````````{panels}
[foo <bar attr="](baz)">
.
<p>[foo <bar attr="](baz)"></p>
````````````````````````````````


````````````````````````````````{panels}
[foo`](/uri)`
.
<p>[foo<code>](/uri)</code></p>
````````````````````````````````


````````````````````````````````{panels}
[foo<http://example.com/?search=](uri)>
.
<p>[foo<a href="http://example.com/?search=%5D(uri)">http://example.com/?search=](uri)</a></p>
````````````````````````````````


There are three kinds of [reference link](#@)s:
[full](#full-reference-link), [collapsed](#collapsed-reference-link),
and [shortcut](#shortcut-reference-link).

A [full reference link](#@)
consists of a [link text] immediately followed by a [link label]
that [matches] a [link reference definition] elsewhere in the document.

A [link label](#@)  begins with a left bracket (`[`) and ends
with the first right bracket (`]`) that is not backslash-escaped.
Between these brackets there must be at least one character that is not a space,
tab, or line ending.
Unescaped square bracket characters are not allowed inside the
opening and closing square brackets of [link labels].  A link
label can have at most 999 characters inside the square
brackets.

One label [matches](#@)
another just in case their normalized forms are equal.  To normalize a
label, strip off the opening and closing brackets,
perform the *Unicode case fold*, strip leading and trailing
spaces, tabs, and line endings, and collapse consecutive internal
spaces, tabs, and line endings to a single space.  If there are multiple
matching reference link definitions, the one that comes first in the
document is used.  (It is desirable in such cases to emit a warning.)

The link's URI and title are provided by the matching [link
reference definition].

Here is a simple{panels}:

````````````````````````````````{panels}
[foo][bar]

[bar]: /url "title"
.
<p><a href="/url" title="title">foo</a></p>
````````````````````````````````


The rules for the [link text] are the same as with
[inline links].  Thus:

The link text may contain balanced brackets, but not unbalanced ones,
unless they are escaped:

````````````````````````````````{panels}
[link [foo [bar]]][ref]

[ref]: /uri
.
<p><a href="/uri">link [foo [bar]]</a></p>
````````````````````````````````


````````````````````````````````{panels}
[link \[bar][ref]

[ref]: /uri
.
<p><a href="/uri">link [bar</a></p>
````````````````````````````````


The link text may contain inline content:

````````````````````````````````{panels}
[link *foo **bar** `#`*][ref]

[ref]: /uri
.
<p><a href="/uri">link <em>foo <strong>bar</strong> <code>#</code></em></a></p>
````````````````````````````````


````````````````````````````````{panels}
[![moon](moon.jpg)][ref]

[ref]: /uri
.
<p><a href="/uri"><img src="moon.jpg" alt="moon" /></a></p>
````````````````````````````````


However, links may not contain other links, at any level of nesting.

````````````````````````````````{panels}
[foo [bar](/uri)][ref]

[ref]: /uri
.
<p>[foo <a href="/uri">bar</a>]<a href="/uri">ref</a></p>
````````````````````````````````


````````````````````````````````{panels}
[foo *bar [baz][ref]*][ref]

[ref]: /uri
.
<p>[foo <em>bar <a href="/uri">baz</a></em>]<a href="/uri">ref</a></p>
````````````````````````````````


(In the{panels}s above, we have two [shortcut reference links]
instead of one [full reference link].)

The following cases illustrate the precedence of link text grouping over
emphasis grouping:

````````````````````````````````{panels}
*[foo*][ref]

[ref]: /uri
.
<p>*<a href="/uri">foo*</a></p>
````````````````````````````````


````````````````````````````````{panels}
[foo *bar][ref]*

[ref]: /uri
.
<p><a href="/uri">foo *bar</a>*</p>
````````````````````````````````


These cases illustrate the precedence of HTML tags, code spans,
and autolinks over link grouping:

````````````````````````````````{panels}
[foo <bar attr="][ref]">

[ref]: /uri
.
<p>[foo <bar attr="][ref]"></p>
````````````````````````````````


````````````````````````````````{panels}
[foo`][ref]`

[ref]: /uri
.
<p>[foo<code>][ref]</code></p>
````````````````````````````````


````````````````````````````````{panels}
[foo<http://example.com/?search=][ref]>

[ref]: /uri
.
<p>[foo<a href="http://example.com/?search=%5D%5Bref%5D">http://example.com/?search=][ref]</a></p>
````````````````````````````````


Matching is case-insensitive:

````````````````````````````````{panels}
[foo][BaR]

[bar]: /url "title"
.
<p><a href="/url" title="title">foo</a></p>
````````````````````````````````


Unicode case fold is used:

````````````````````````````````{panels}
[ẞ]

[SS]: /url
.
<p><a href="/url">ẞ</a></p>
````````````````````````````````


Consecutive internal spaces, tabs, and line endings are treated as one space for
purposes of determining matching:

````````````````````````````````{panels}
[Foo
  bar]: /url

[Baz][Foo bar]
.
<p><a href="/url">Baz</a></p>
````````````````````````````````


No spaces, tabs, or line endings are allowed between the [link text] and the
[link label]:

````````````````````````````````{panels}
[foo] [bar]

[bar]: /url "title"
.
<p>[foo] <a href="/url" title="title">bar</a></p>
````````````````````````````````


````````````````````````````````{panels}
[foo]
[bar]

[bar]: /url "title"
.
<p>[foo]
<a href="/url" title="title">bar</a></p>
````````````````````````````````


This is a departure from John Gruber's original Markdown syntax
description, which explicitly allows whitespace between the link
text and the link label.  It brings reference links in line with
[inline links], which (according to both original Markdown and
this spec) cannot have whitespace after the link text.  More
importantly, it prevents inadvertent capture of consecutive
[shortcut reference links]. If whitespace is allowed between the
link text and the link label, then in the following we will have
a single reference link, not two shortcut reference links, as
intended:

``` markdown
[foo]
[bar]

[foo]: /url1
[bar]: /url2
```

(Note that [shortcut reference links] were introduced by Gruber
himself in a beta version of `Markdown.pl`, but never included
in the official syntax description.  Without shortcut reference
links, it is harmless to allow space between the link text and
link label; but once shortcut references are introduced, it is
too dangerous to allow this, as it frequently leads to
unintended results.)

When there are multiple matching [link reference definitions],
the first is used:

````````````````````````````````{panels}
[foo]: /url1

[foo]: /url2

[bar][foo]
.
<p><a href="/url1">bar</a></p>
````````````````````````````````


Note that matching is performed on normalized strings, not parsed
inline content.  So the following does not match, even though the
labels define equivalent inline content:

````````````````````````````````{panels}
[bar][foo\!]

[foo!]: /url
.
<p>[bar][foo!]</p>
````````````````````````````````


[Link labels] cannot contain brackets, unless they are
backslash-escaped:

````````````````````````````````{panels}
[foo][ref[]

[ref[]: /uri
.
<p>[foo][ref[]</p>
<p>[ref[]: /uri</p>
````````````````````````````````


````````````````````````````````{panels}
[foo][ref[bar]]

[ref[bar]]: /uri
.
<p>[foo][ref[bar]]</p>
<p>[ref[bar]]: /uri</p>
````````````````````````````````


````````````````````````````````{panels}
[[[foo]]]

[[[foo]]]: /url
.
<p>[[[foo]]]</p>
<p>[[[foo]]]: /url</p>
````````````````````````````````


````````````````````````````````{panels}
[foo][ref\[]

[ref\[]: /uri
.
<p><a href="/uri">foo</a></p>
````````````````````````````````


Note that in this{panels} `]` is not backslash-escaped:

````````````````````````````````{panels}
[bar\\]: /uri

[bar\\]
.
<p><a href="/uri">bar\</a></p>
````````````````````````````````


A [link label] must contain at least one character that is not a space, tab, or
line ending:

````````````````````````````````{panels}
[]

[]: /uri
.
<p>[]</p>
<p>[]: /uri</p>
````````````````````````````````


````````````````````````````````{panels}
[
 ]

[
 ]: /uri
.
<p>[
]</p>
<p>[
]: /uri</p>
````````````````````````````````


A [collapsed reference link](#@)
consists of a [link label] that [matches] a
[link reference definition] elsewhere in the
document, followed by the string `[]`.
The contents of the first link label are parsed as inlines,
which are used as the link's text.  The link's URI and title are
provided by the matching reference link definition.  Thus,
`[foo][]` is equivalent to `[foo][foo]`.

````````````````````````````````{panels}
[foo][]

[foo]: /url "title"
.
<p><a href="/url" title="title">foo</a></p>
````````````````````````````````


````````````````````````````````{panels}
[*foo* bar][]

[*foo* bar]: /url "title"
.
<p><a href="/url" title="title"><em>foo</em> bar</a></p>
````````````````````````````````


The link labels are case-insensitive:

````````````````````````````````{panels}
[Foo][]

[foo]: /url "title"
.
<p><a href="/url" title="title">Foo</a></p>
````````````````````````````````



As with full reference links, spaces, tabs, or line endings are not
allowed between the two sets of brackets:

````````````````````````````````{panels}
[foo] 
[]

[foo]: /url "title"
.
<p><a href="/url" title="title">foo</a>
[]</p>
````````````````````````````````


A [shortcut reference link](#@)
consists of a [link label] that [matches] a
[link reference definition] elsewhere in the
document and is not followed by `[]` or a link label.
The contents of the first link label are parsed as inlines,
which are used as the link's text.  The link's URI and title
are provided by the matching link reference definition.
Thus, `[foo]` is equivalent to `[foo][]`.

````````````````````````````````{panels}
[foo]

[foo]: /url "title"
.
<p><a href="/url" title="title">foo</a></p>
````````````````````````````````


````````````````````````````````{panels}
[*foo* bar]

[*foo* bar]: /url "title"
.
<p><a href="/url" title="title"><em>foo</em> bar</a></p>
````````````````````````````````


````````````````````````````````{panels}
[[*foo* bar]]

[*foo* bar]: /url "title"
.
<p>[<a href="/url" title="title"><em>foo</em> bar</a>]</p>
````````````````````````````````


````````````````````````````````{panels}
[[bar [foo]

[foo]: /url
.
<p>[[bar <a href="/url">foo</a></p>
````````````````````````````````


The link labels are case-insensitive:

````````````````````````````````{panels}
[Foo]

[foo]: /url "title"
.
<p><a href="/url" title="title">Foo</a></p>
````````````````````````````````


A space after the link text should be preserved:

````````````````````````````````{panels}
[foo] bar

[foo]: /url
.
<p><a href="/url">foo</a> bar</p>
````````````````````````````````


If you just want bracketed text, you can backslash-escape the
opening bracket to avoid links:

````````````````````````````````{panels}
\[foo]

[foo]: /url "title"
.
<p>[foo]</p>
````````````````````````````````


Note that this is a link, because a link label ends with the first
following closing bracket:

````````````````````````````````{panels}
[foo*]: /url

*[foo*]
.
<p>*<a href="/url">foo*</a></p>
````````````````````````````````


Full and compact references take precedence over shortcut
references:

````````````````````````````````{panels}
[foo][bar]

[foo]: /url1
[bar]: /url2
.
<p><a href="/url2">foo</a></p>
````````````````````````````````

````````````````````````````````{panels}
[foo][]

[foo]: /url1
.
<p><a href="/url1">foo</a></p>
````````````````````````````````

Inline links also take precedence:

````````````````````````````````{panels}
[foo]()

[foo]: /url1
.
<p><a href="">foo</a></p>
````````````````````````````````

````````````````````````````````{panels}
[foo](not a link)

[foo]: /url1
.
<p><a href="/url1">foo</a>(not a link)</p>
````````````````````````````````

In the following case `[bar][baz]` is parsed as a reference,
`[foo]` as normal text:

````````````````````````````````{panels}
[foo][bar][baz]

[baz]: /url
.
<p>[foo]<a href="/url">bar</a></p>
````````````````````````````````


Here, though, `[foo][bar]` is parsed as a reference, since
`[bar]` is defined:

````````````````````````````````{panels}
[foo][bar][baz]

[baz]: /url1
[bar]: /url2
.
<p><a href="/url2">foo</a><a href="/url1">baz</a></p>
````````````````````````````````


Here `[foo]` is not parsed as a shortcut reference, because it
is followed by a link label (even though `[bar]` is not defined):

````````````````````````````````{panels}
[foo][bar][baz]

[baz]: /url1
[foo]: /url2
.
<p>[foo]<a href="/url1">bar</a></p>
````````````````````````````````



## Images

Syntax for images is like the syntax for links, with one
difference. Instead of [link text], we have an
[image description](#@).  The rules for this are the
same as for [link text], except that (a) an
image description starts with `![` rather than `[`, and
(b) an image description may contain links.
An image description has inline elements
as its contents.  When an image is rendered to HTML,
this is standardly used as the image's `alt` attribute.

````````````````````````````````{panels}
```
![foo](/url "title")
```
---
```
<p><img src="/url" alt="foo" title="title" /></p>
```
````````````````````````````````


````````````````````````````````{panels}
```
![foo *bar*]

[foo *bar*]: train.jpg "train & tracks"
```
---
```
<p><img src="train.jpg" alt="foo bar" title="train &amp; tracks" /></p>
```
````````````````````````````````


````````````````````````````````{panels}
```
![foo ![bar](/url)](/url2)
```
---
```
<p><img src="/url2" alt="foo bar" /></p>
```
````````````````````````````````


````````````````````````````````{panels}
```
![foo [bar](/url)](/url2)
```
---
```
<p><img src="/url2" alt="foo bar" /></p>
```
````````````````````````````````


Though this spec is concerned with parsing, not rendering, it is
recommended that in rendering to HTML, only the plain string content
of the [image description] be used.  Note that in
the above{panels}, the alt attribute's value is `foo bar`, not `foo
[bar](/url)` or `foo <a href="/url">bar</a>`.  Only the plain string
content is rendered, without formatting.

````````````````````````````````{panels}
```
![foo *bar*][]

[foo *bar*]: train.jpg "train & tracks"
```
---
```
<p><img src="train.jpg" alt="foo bar" title="train &amp; tracks" /></p>
```
````````````````````````````````


````````````````````````````````{panels}
```
![foo *bar*][foobar]

[FOOBAR]: train.jpg "train & tracks"
```
---
```
<p><img src="train.jpg" alt="foo bar" title="train &amp; tracks" /></p>
```
````````````````````````````````


````````````````````````````````{panels}
```
![foo](train.jpg)
```
---
```
<p><img src="train.jpg" alt="foo" /></p>
```
````````````````````````````````


````````````````````````````````{panels}
```
My ![foo bar](/path/to/train.jpg  "title"   )
```
---
```
<p>My <img src="/path/to/train.jpg" alt="foo bar" title="title" /></p>
```
````````````````````````````````


````````````````````````````````{panels}
```
![foo](<url>)
```
---
```
<p><img src="url" alt="foo" /></p>
```
````````````````````````````````


````````````````````````````````{panels}
```
![](/url)
```
---
```
<p><img src="/url" alt="" /></p>
```
````````````````````````````````


Reference-style:

````````````````````````````````{panels}
```````
![foo][bar]

[bar]: /url
```````
---
```````
<p><img src="/url" alt="foo" /></p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
![foo][bar]

[BAR]: /url
```````
---
```````
<p><img src="/url" alt="foo" /></p>
```````
````````````````````````````````


Collapsed:

````````````````````````````````{panels}
```````
![foo][]

[foo]: /url "title"
```````
---
```````
<p><img src="/url" alt="foo" title="title" /></p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
![*foo* bar][]

[*foo* bar]: /url "title"
```````
---
```````
<p><img src="/url" alt="foo bar" title="title" /></p>
```````
````````````````````````````````


The labels are case-insensitive:

````````````````````````````````{panels}
```````
![Foo][]

[foo]: /url "title"
```````
---
```````
<p><img src="/url" alt="Foo" title="title" /></p>
```````
````````````````````````````````


As with reference links, spaces, tabs, and line endings, are not allowed
between the two sets of brackets:

````````````````````````````````{panels}
```````
![foo] 
[]

[foo]: /url "title"
```````
---
```````
<p><img src="/url" alt="foo" title="title" />
[]</p>
```````
````````````````````````````````


Shortcut:

````````````````````````````````{panels}
```````
![foo]

[foo]: /url "title"
```````
---
```````
<p><img src="/url" alt="foo" title="title" /></p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
![*foo* bar]

[*foo* bar]: /url "title"
```````
---
```````
<p><img src="/url" alt="foo bar" title="title" /></p>
```````
````````````````````````````````


Note that link labels cannot contain unescaped brackets:

````````````````````````````````{panels}
```````
![[foo]]

[[foo]]: /url "title"
```````
---
```````
<p>![[foo]]</p>
<p>[[foo]]: /url &quot;title&quot;</p>
```````
````````````````````````````````


The link labels are case-insensitive:

````````````````````````````````{panels}
```````
![Foo]

[foo]: /url "title"
```````
---
```````
<p><img src="/url" alt="Foo" title="title" /></p>
```````
````````````````````````````````


If you just want a literal `!` followed by bracketed text, you can
backslash-escape the opening `[`:

````````````````````````````````{panels}
```````
!\[foo]

[foo]: /url "title"
```````
---
```````
<p>![foo]</p>
```````
````````````````````````````````


If you want a link after a literal `!`, backslash-escape the
`!`:

````````````````````````````````{panels}
```````
\![foo]

[foo]: /url "title"
```````
---
```````
<p>!<a href="/url" title="title">foo</a></p>
```````
````````````````````````````````


## Autolinks

[Autolink](#@)s are absolute URIs and email addresses inside
`<` and `>`. They are parsed as links, with the URL or email address
as the link label.

A [URI autolink](#@) consists of `<`, followed by an
[absolute URI] followed by `>`.  It is parsed as
a link to the URI, with the URI as the link's label.

An [absolute URI](#@),
for these purposes, consists of a [scheme] followed by a colon (`:`)
followed by zero or more characters other than [ASCII control
characters][ASCII control character], [space], `<`, and `>`.
If the URI includes these characters, they must be percent-encoded
(e.g. `%20` for a space).

For purposes of this spec, a [scheme](#@) is any sequence
of 2--32 characters beginning with an ASCII letter and followed
by any combination of ASCII letters, digits, or the symbols plus
("+"), period ("."), or hyphen ("-").

Here are some valid autolinks:

````````````````````````````````{panels}
```````
<http://foo.bar.baz>
```````
---
```````
<p><a href="http://foo.bar.baz">http://foo.bar.baz</a></p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
<http://foo.bar.baz/test?q=hello&id=22&boolean>
```````
---
```````
<p><a href="http://foo.bar.baz/test?q=hello&amp;id=22&amp;boolean">http://foo.bar.baz/test?q=hello&amp;id=22&amp;boolean</a></p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
<irc://foo.bar:2233/baz>
```````
---
```````
<p><a href="irc://foo.bar:2233/baz">irc://foo.bar:2233/baz</a></p>
```````
````````````````````````````````


Uppercase is also fine:

````````````````````````````````{panels}
```````
<MAILTO:FOO@BAR.BAZ>
```````
---
```````
<p><a href="MAILTO:FOO@BAR.BAZ">MAILTO:FOO@BAR.BAZ</a></p>
```````
````````````````````````````````


Note that many strings that count as [absolute URIs] for
purposes of this spec are not valid URIs, because their
schemes are not registered or because of other problems
with their syntax:

````````````````````````````````{panels}
```````
<a+b+c:d>
```````
---
```````
<p><a href="a+b+c:d">a+b+c:d</a></p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
<made-up-scheme://foo,bar>
```````
---
```````
<p><a href="made-up-scheme://foo,bar">made-up-scheme://foo,bar</a></p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
<http://../>
```````
---
```````
<p><a href="http://../">http://../</a></p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
<localhost:5001/foo>
```````
---
```````
<p><a href="localhost:5001/foo">localhost:5001/foo</a></p>
```````
````````````````````````````````


Spaces are not allowed in autolinks:

````````````````````````````````{panels}
```````
<http://foo.bar/baz bim>
```````
---
```````
<p>&lt;http://foo.bar/baz bim&gt;</p>
```````
````````````````````````````````


Backslash-escapes do not work inside autolinks:

````````````````````````````````{panels}
```````
<http://example.com/\[\>
```````
---
```````
<p><a href="http://example.com/%5C%5B%5C">http://example.com/\[\</a></p>
```````
````````````````````````````````


An [email autolink](#@)
consists of `<`, followed by an [email address],
followed by `>`.  The link's label is the email address,
and the URL is `mailto:` followed by the email address.

An [email address](#@),
for these purposes, is anything that matches
the [non-normative regex from the HTML5
spec](https://html.spec.whatwg.org/multipage/forms.html#e-mail-state-(type=email)):

    /^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?
    (?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$/

Examples of email autolinks:

````````````````````````````````{panels}
```````
<foo@bar.example.com>
```````
---
```````
<p><a href="mailto:foo@bar.example.com">foo@bar.example.com</a></p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
<foo+special@Bar.baz-bar0.com>
```````
---
```````
<p><a href="mailto:foo+special@Bar.baz-bar0.com">foo+special@Bar.baz-bar0.com</a></p>
```````
````````````````````````````````


Backslash-escapes do not work inside email autolinks:

````````````````````````````````{panels}
```````
<foo\+@bar.example.com>
```````
---
```````
<p>&lt;foo+@bar.example.com&gt;</p>
```````
````````````````````````````````


These are not autolinks:

````````````````````````````````{panels}
```````
<>
```````
---
```````
<p>&lt;&gt;</p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
< http://foo.bar >
```````
---
```````
<p>&lt; http://foo.bar &gt;</p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
<m:abc>
```````
---
```````
<p>&lt;m:abc&gt;</p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
<foo.bar.baz>
```````
---
```````
<p>&lt;foo.bar.baz&gt;</p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
http://example.com
```````
---
```````
<p>http://example.com</p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
foo@bar.example.com
```````
---
```````
<p>foo@bar.example.com</p>
```````
````````````````````````````````


## Raw HTML

Text between `<` and `>` that looks like an HTML tag is parsed as a
raw HTML tag and will be rendered in HTML without escaping.
Tag and attribute names are not limited to current HTML tags,
so custom tags (and even, say, DocBook tags) may be used.

Here is the grammar for tags:

A [tag name](#@) consists of an ASCII letter
followed by zero or more ASCII letters, digits, or
hyphens (`-`).

An [attribute](#@) consists of spaces, tabs, and up to one line ending,
an [attribute name], and an optional
[attribute value specification].

An [attribute name](#@)
consists of an ASCII letter, `_`, or `:`, followed by zero or more ASCII
letters, digits, `_`, `.`, `:`, or `-`.  (Note:  This is the XML
specification restricted to ASCII.  HTML5 is laxer.)

An [attribute value specification](#@)
consists of optional spaces, tabs, and up to one line ending,
a `=` character, optional spaces, tabs, and up to one line ending,
and an [attribute value].

An [attribute value](#@)
consists of an [unquoted attribute value],
a [single-quoted attribute value], or a [double-quoted attribute value].

An [unquoted attribute value](#@)
is a nonempty string of characters not
including spaces, tabs, line endings, `"`, `'`, `=`, `<`, `>`, or `` ` ``.

A [single-quoted attribute value](#@)
consists of `'`, zero or more
characters not including `'`, and a final `'`.

A [double-quoted attribute value](#@)
consists of `"`, zero or more
characters not including `"`, and a final `"`.

An [open tag](#@) consists of a `<` character, a [tag name],
zero or more [attributes], optional spaces, tabs, and up to one line ending,
an optional `/` character, and a `>` character.

A [closing tag](#@) consists of the string `</`, a
[tag name], optional spaces, tabs, and up to one line ending, and the character
`>`.

An [HTML comment](#@) consists of `<!--` + *text* + `-->`,
where *text* does not start with `>` or `->`, does not end with `-`,
and does not contain `--`.  (See the
[HTML5 spec](http://www.w3.org/TR/html5/syntax.html#comments).)

A [processing instruction](#@)
consists of the string `<?`, a string
of characters not including the string `?>`, and the string
`?>`.

A [declaration](#@) consists of the string `<!`, an ASCII letter, zero or more
characters not including the character `>`, and the character `>`.

A [CDATA section](#@) consists of
the string `<![CDATA[`, a string of characters not including the string
`]]>`, and the string `]]>`.

An [HTML tag](#@) consists of an [open tag], a [closing tag],
an [HTML comment], a [processing instruction], a [declaration],
or a [CDATA section].

Here are some simple open tags:

````````````````````````````````{panels}
```````
<a><bab><c2c>
```````
---
```````
<p><a><bab><c2c></p>
```````
````````````````````````````````


Empty elements:

````````````````````````````````{panels}
```````
<a/><b2/>
```````
---
```````
<p><a/><b2/></p>
```````
````````````````````````````````


Whitespace is allowed:

````````````````````````````````{panels}
```````
<a  /><b2
data="foo" >
```````
---
```````
<p><a  /><b2
data="foo" ></p>
```````
````````````````````````````````


With attributes:

````````````````````````````````{panels}
```````
<a foo="bar" bam = 'baz <em>"</em>'
_boolean zoop:33=zoop:33 />
```````
---
```````
<p><a foo="bar" bam = 'baz <em>"</em>'
_boolean zoop:33=zoop:33 /></p>
```````
````````````````````````````````


Custom tag names can be used:

````````````````````````````````{panels}
```````
Foo <responsive-image src="foo.jpg" />
```````
---
```````
<p>Foo <responsive-image src="foo.jpg" /></p>
```````
````````````````````````````````


Illegal tag names, not parsed as HTML:

````````````````````````````````{panels}
```````
<33> <__>
```````
---
```````
<p>&lt;33&gt; &lt;__&gt;</p>
```````
````````````````````````````````


Illegal attribute names:

````````````````````````````````{panels}
```````
<a h*#ref="hi">
```````
---
```````
<p>&lt;a h*#ref=&quot;hi&quot;&gt;</p>
```````
````````````````````````````````


Illegal attribute values:

````````````````````````````````{panels}
```````
<a href="hi'> <a href=hi'>
```````
---
```````
<p>&lt;a href=&quot;hi'&gt; &lt;a href=hi'&gt;</p>
```````
````````````````````````````````


Illegal whitespace:

````````````````````````````````{panels}
```````
< a><
foo><bar/ >
<foo bar=baz
bim!bop />
```````
---
```````
<p>&lt; a&gt;&lt;
foo&gt;&lt;bar/ &gt;
&lt;foo bar=baz
bim!bop /&gt;</p>
```````
````````````````````````````````


Missing whitespace:

````````````````````````````````{panels}
```````
<a href='bar'title=title>
```````
---
```````
<p>&lt;a href='bar'title=title&gt;</p>
```````
````````````````````````````````


Closing tags:

````````````````````````````````{panels}
```````
</a></foo >
```````
---
```````
<p></a></foo ></p>
```````
````````````````````````````````


Illegal attributes in closing tag:

````````````````````````````````{panels}
```````
</a href="foo">
---
```````
<p>&lt;/a href=&quot;foo&quot;&gt;</p>
```````
````````````````````````````````


Comments:

````````````````````````````````{panels}
```````
foo <!-- this is a
comment - with hyphen -->
```````
---
```````
<p>foo <!-- this is a
comment - with hyphen --></p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
foo <!-- not a comment -- two hyphens -->
```````
---
```````
<p>foo &lt;!-- not a comment -- two hyphens --&gt;</p>
````````````````````````````````


Not comments:

````````````````````````````````{panels}
```````
foo <!--> foo -->

foo <!-- foo--->
```````
---
```````
<p>foo &lt;!--&gt; foo --&gt;</p>
<p>foo &lt;!-- foo---&gt;</p>
```````
````````````````````````````````


Processing instructions:

````````````````````````````````{panels}
```````
foo <?php echo $a; ?>
```````
---
```````
<p>foo <?php echo $a; ?></p>
```````
````````````````````````````````


Declarations:

````````````````````````````````{panels}
```````
foo <!ELEMENT br EMPTY>
```````
---
```````
<p>foo <!ELEMENT br EMPTY></p>
```````
````````````````````````````````


CDATA sections:

````````````````````````````````{panels}
```````
foo <![CDATA[>&<]]>
```````
---
```````
<p>foo <![CDATA[>&<]]></p>
```````
````````````````````````````````


Entity and numeric character references are preserved in HTML
attributes:

````````````````````````````````{panels}
```````
foo <a href="&ouml;">
```````
---
```````
<p>foo <a href="&ouml;"></p>
```````
````````````````````````````````


Backslash escapes do not work in HTML attributes:

````````````````````````````````{panels}
```````
foo <a href="\*">
```````
---
```````
<p>foo <a href="\*"></p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
<a href="\"">
```````
---
```````
<p>&lt;a href=&quot;&quot;&quot;&gt;</p>
```````
````````````````````````````````


## Hard line breaks

A line ending (not in a code span or HTML tag) that is preceded
by two or more spaces and does not occur at the end of a block
is parsed as a [hard line break](#@) (rendered
in HTML as a `<br />` tag):

````````````````````````````````{panels}
```````
foo  
baz
```````
---
```````
<p>foo<br />
baz</p>
```````
````````````````````````````````


For a more visible alternative, a backslash before the
[line ending] may be used instead of two or more spaces:

````````````````````````````````{panels}
```````
foo\
baz
```````
---
```````
<p>foo<br />
baz</p>
```````
````````````````````````````````


More than two spaces can be used:

````````````````````````````````{panels}
```````
foo       
baz
```````
---
```````
<p>foo<br />
baz</p>
```````
````````````````````````````````


Leading spaces at the beginning of the next line are ignored:

````````````````````````````````{panels}
```````
foo  
     bar
```````
---
```````
<p>foo<br />
bar</p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
foo\
     bar
```````
---
```````
<p>foo<br />
bar</p>
```````
````````````````````````````````


Hard line breaks can occur inside emphasis, links, and other constructs
that allow inline content:

````````````````````````````````{panels}
```````
*foo  
bar*
```````
---
```````
<p><em>foo<br />
bar</em></p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
*foo\
bar*
```````
---
```````
<p><em>foo<br />
bar</em></p>
```````
````````````````````````````````


Hard line breaks do not occur inside code spans

````````````````````````````````{panels}
```````
`code  
span`
```````
---
```````
<p><code>code   span</code></p>
````````````````````````````````


````````````````````````````````{panels}
```````
`code\
span`
```````
---
```````
<p><code>code\ span</code></p>
```````
````````````````````````````````


or HTML tags:

````````````````````````````````{panels}
```````
<a href="foo  
bar">
```````
---
```````
<p><a href="foo  
bar"></p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
<a href="foo\
bar">
```````
---
```````
<p><a href="foo\
bar"></p>
```````
````````````````````````````````


Hard line breaks are for separating inline content within a block.
Neither syntax for hard line breaks works at the end of a paragraph or
other block element:

````````````````````````````````{panels}
```````
foo\
```````
---
```````
<p>foo\</p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
foo  
```````
---
```````
<p>foo</p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
### foo\
```````
---
```````
<h3>foo\</h3>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
### foo  
```````
---
```````
<h3>foo</h3>
```````
````````````````````````````````


## Soft line breaks

A regular line ending (not in a code span or HTML tag) that is not
preceded by two or more spaces or a backslash is parsed as a
[softbreak](#@).  (A soft line break may be rendered in HTML either as a
[line ending] or as a space. The result will be the same in
browsers. In the{panels}s here, a [line ending] will be used.)

````````````````````````````````{panels}
```````
foo
baz
```````
---
```````
<p>foo
baz</p>
```````
````````````````````````````````


Spaces at the end of the line and beginning of the next line are
removed:

````````````````````````````````{panels}
```````
foo 
 baz
```````
---
```````
<p>foo
baz</p>
```````
````````````````````````````````


A conforming parser may render a soft line break in HTML either as a
line ending or as a space.

A renderer may also provide an option to render soft line breaks
as hard line breaks.

## Textual content

Any characters not given an interpretation by the above rules will
be parsed as plain textual content.

````````````````````````````````{panels}
```````
hello $.;'there
```````
---
```````
<p>hello $.;'there</p>
```````
````````````````````````````````


````````````````````````````````{panels}
```````
Foo χρῆν
```````
---
```````
<p>Foo χρῆν</p>
```````
````````````````````````````````


Internal spaces are preserved verbatim:

````````````````````````````````{panels}
```````
Multiple     spaces
```````
---
```````
<p>Multiple     spaces</p>
```````
````````````````````````````````


<!-- END TESTS -->
