# 预备知识

## 字符和行

任何字符序列都是有效的 CommonMark 文档。

字符
:   一个 {term}`字符` 是一个 Unicode {term}`码点`。尽管一些 {term}`码点` （例如，组合重音）在直观意义上与字符不对应，但在本规范中，所有 {term}`码点` 都被视为字符。

    本规范没有指定编码方式；它认为 {dfn}`行` 是是由 **字符** 而不是字节组成的。符合标准的解析器可能被限制为特定的编码。

行
:   **行** 是一个由 0 个或多个字符组成的序列（而不是换行（`U+000A`）或回车（`U+000D`）），后面跟着 **行结束符** 或文件结束符。

行结束符
:   **行结束符**（line ending） 是换行符（`U+000A`），回车符（`U+000D`）后面没有换行符，或者回车符后面有换行符。

空行
:   不包含字符或只包含空格（`U+0020`）或制表符（`U+0009`）的行称为 **空行**（blank line）。

本规范将使用以下字符类的定义：

Unicode 空白字符
:   Unicode 空白字符（Unicode whitespace character）是 Unicode `Zs` 一般类别中的任何 {term}`码点`，或者制表符（`U+0009`）、换行符（`U+000A`）、换行符（`U+000C`）或回车符（`U+000D`）。

Unicode 空白符
:   Unicode 空白符（Unicode whitespace）是一个或多个 Unicode 空白字符的序列。

`tab`
:   `tab` 是 `U+0009`。

`space`
:   space 是 `U+0020`。

ASCII 控制字符
:   ASCII 控制字符（ASCII control character）是介于 `U+0000–1F` （包括）或 `U+007F` 之间的字符。

ASCII 标点符号
:   ASCII 标点符号（ASCII punctuation character） 是 `!`, `"`, `#`, `$`, `%`, `&`, `'`, `(`, `)`, `*`, `+`, `,`, `-`, `.`, `/` (U+0021–2F), `:`, `;`, `<`, `=`, `>`, `?`, `@` (`U+003A–0040`), `[`, `\`, `]`, `^`, `_`, `` ` `` (`U+005B–0060`),  `{`, `|`, `}`, 或者 `~` (`U+007B–007E`)。

Unicode 标点符号
:   Unicode 标点符号（Unicode punctuation character） 是一个 ASCII 标点字符或一般 Unicode 类别 `Pc`、`Pd`、`Pe`、`Pf`、`Pi`、`Po` 或 `Ps` 中的任何字符。

## 制表符

行中的制表符不扩展为空格。然而，在空格有助于定义块结构的上下文中，制表符的行为就好像它们被带有 4 个字符的制表符的空格替换了一样。

因此，例如，可以在缩进代码块中使用一个制表符而不是四个空格。（但是请注意，内部制表符是作为字面制表符传递的，而不是扩展为空格。）

````{panels}
markdown
^^^
```
→foo→baz→→bim
```
----
html
^^^
```
<pre><code>foo→baz→→bim
</code></pre>
```
````

````{panels}
markdown
^^^
```
  →foo→baz→→bim
```
----
html
^^^
```
<pre><code>foo→baz→→bim
</code></pre>
```
````

````{panels}
markdown
^^^
```
    a→a
    ὐ→a
```
----
html
^^^
```
<pre><code>a→a
ὐ→a
</code></pre>
```
````

在下面的例子中，列表项的延续段落使用 tab 缩进；这与有四个空格的缩进的效果完全相同：

````{panels}
markdown
^^^
```
  - foo

→bar
```
----
html
^^^
```
<ul>
<li>
<p>foo</p>
<p>bar</p>
</li>
</ul>
```
````

````{panels}
markdown
^^^
```
- foo

→→bar
```
----
html
^^^
```
<ul>
<li>
<p>foo</p>
<pre><code>  bar
</code></pre>
</li>
</ul>
```
````

通常，以块引用开头的 `>` 后面可以有一个空格，这个空格不被认为是内容的一部分。在下面的情况中，`>` 后面跟着一个制表符，它被视为扩展为三个空格。由于其中一个空格被认为是分隔符的一部分，`foo` 被认为在 块引用 上下文中缩进了 6 个空格，因此我们得到了一个以两个空格开始的缩进代码块。

````{panels}
markdown
^^^
```
>→→foo
```
----
html
^^^
```
<blockquote>
<pre><code>  foo
</code></pre>
</blockquote>
```
````

````{panels}
markdown
^^^
```
-→→foo
```
----
html
^^^
```
<ul>
<li>
<pre><code>  foo
</code></pre>
</li>
</ul>
```
````

````{panels}
markdown
^^^
```
    foo
→bar
```
----
html
^^^
```
<pre><code>foo
bar
</code></pre>
```
````

````{panels}
markdown
^^^
```
 - foo
   - bar
→ - baz
```
----
html
^^^
```
<ul>
<li>foo
<ul>
<li>bar
<ul>
<li>baz</li>
</ul>
</li>
</ul>
</li>
</ul>
```
````

````{panels}
markdown
^^^
```
#→Foo
```
----
html
^^^
```
<h1>Foo</h1>
```
````

````{panels}
markdown
^^^
```
*→*→*→
```
----
html
^^^
```
<hr />
```
````

## 不安全的字符

出于安全原因，Unicode 字符 `U+0000` 必须被替换为替换字符（`U+FFFD`）。

## 反斜杠转义

任何 ASCII 标点字符都可以被反斜杠转义：

````{panels}
markdown
^^^
```
\!\"\#\$\%\&\'\(\)\*\+\,\-\.\/\:\;\<\=\>\?\@\[\\\]\^\_\`\{\|\}\~
```
----
html
^^^
```
<p>!&quot;#$%&amp;'()*+,-./:;&lt;=&gt;?@[\]^_`{|}~</p>
```
````

其他字符前的反斜杠被视为字面反斜杠：

````{panels}
markdown
^^^
```
\→\A\a\ \3\φ\«
```
----
html
^^^
```
<p>\→\A\a\ \3\φ\«</p>
```
````

转义字符被视为普通字符，没有通常的 Markdown 含义：

````{panels}
markdown
^^^
```
\*not emphasized*
\<br/> not a tag
\[not a link](/foo)
\`not code`
1\. not a list
\* not a list
\# not a heading
\[foo]: /url "not a reference"
\&ouml; not a character entity
```
----
html
^^^
```
<p>*not emphasized*
&lt;br/&gt; not a tag
[not a link](/foo)
`not code`
1. not a list
* not a list
# not a heading
[foo]: /url &quot;not a reference&quot;
&amp;ouml; not a character entity</p>
```
````

如果反斜杠本身被转义，则以下字符不被转义：

````{panels}
markdown
^^^
```
\\*emphasis*
```
----
html
^^^
```
<p>\<em>emphasis</em></p>
```
````

行尾的反斜杠是硬换行符：


````{panels}
markdown
^^^
```
foo\
bar
```
----
html
^^^
```
<p>foo<br />
bar</p>
```
````

反斜杠转义在代码块、代码 spans、自动链接或原始 HTML 中不起作用：


````{panels}
markdown
^^^
```
`` \[\` ``
```
----
html
^^^
```
<p><code>\[\`</code></p>
```
````

````{panels}
markdown
^^^
```
    \[\]
```
----
html
^^^
```
<pre><code>\[\]
</code></pre>
```
````

````{panels}
markdown
^^^
```
~~~
\[\]
~~~
```
----
html
^^^
```
<pre><code>\[\]
</code></pre>
```
````

````{panels}
markdown
^^^
```
<http://example.com?find=\*>
```
----
html
^^^
```
<p><a href="http://example.com?find=%5C*">http://example.com?find=\*</a></p>
```
````

````{panels}
markdown
^^^
```
<a href="/bar\/)">
```
----
html
^^^
```
<a href="/bar\/)">
```
````

但它们在所有其他上下文中都可以工作，包括 url 和链接标题、链接引用和 fenced 代码块中的 info 字符串：

````{panels}
markdown
^^^
```
[foo](/bar\* "ti\*tle")
```
----
html
^^^
```
<p><a href="/bar*" title="ti*tle">foo</a></p>
```
````

``````{panels}
```
[foo]

[foo]: /bar\* "ti\*tle"
```
---
```
<p><a href="/bar*" title="ti*tle">foo</a></p>
```
``````


````````````````````````````````{panels}
````
``` foo\+bar
foo
```
````
---
```
<pre><code class="language-foo+bar">foo
</code></pre>
```
````````````````````````````````

## 实体和数字字符引用

有效的 HTML 实体引用和数字字符引用可以用来替代相应的 Unicode 字符，但有以下例外：

- 在代码块和代码段中无法识别实体和字符引用。

- 实体和字符引用不能代替在 CommonMark 中定义结构元素的特殊字符。虽然 `&#42;` 可以代替 `*` 字符，但 `&#42;` 不能代替强调分隔符、项目符号列表标记或主题中断中的 `*`。

统一的 CommonMark 解析器不需要存储关于源文件中是否使用 Unicode 字符或实体引用表示特定字符的信息。

实体引用由 `&#` + 任何有效的 HTML5 实体名称 + `;` 组成。文档 <https://html.spec.whatwg.org/entities.json> 被用作有效实体引用及其相应 {term}`码点` 的权威源代码。

````````````````````````````````{panels}
```
&nbsp; &amp; &copy; &AElig; &Dcaron;
&frac34; &HilbertSpace; &DifferentialD;
&ClockwiseContourIntegral; &ngE;
```
---
```
<p>  &amp; © Æ Ď
¾ ℋ ⅆ
∲ ≧̸</p>
```
````````````````````````````````

十进制数字字符引用由 `&#` + 1-7 个阿拉伯数字的字符串 + `;` 组成。数字字符引用被解析为对应的 Unicode 字符。无效的 Unicode 码位将被替换为替换字符（`U+FFFD`）。出于安全原因，{term}`码点`  `U+0000` 也将被 `U+FFFD` 取代。

````````````````````````````````{panels}
```
&#35; &#1234; &#992; &#0;
```
---
```
<p># Ӓ Ϡ �</p>
```
````````````````````````````````

十六进制数字字符引用由 `&#` + `X` 或 `x` + 1-6 个十六进制数字的字符串 + `;` 组成。它们也被解析为相应的 Unicode 字符（这一次用十六进制数字而不是十进制数字指定）。

````````````````````````````````{panels}
```
&#X22; &#XD06; &#xcab;
```
---
```
<p>&quot; ആ ಫ</p>
```
````````````````````````````````

这里有一些无足轻重的东西：

````````````````````````````````{panels}
```
&nbsp &x; &#; &#x;
&#87654321;
&#abcdef0;
&ThisIsNotDefined; &hi?;
```
---
```
<p>&amp;nbsp &amp;x; &amp;#; &amp;#x;
&amp;#87654321;
&amp;#abcdef0;
&amp;ThisIsNotDefined; &amp;hi?;</p>
```
````````````````````````````````

虽然 HTML5 确实接受一些不带尾随分号的实体引用（如 `&copy`），但这里不能识别这些引用，因为这会使语法太模糊：

````````````````````````````````{panels}
```
&copy
```
---
```
<p>&amp;copy</p>
```
````````````````````````````````

不在 HTML5 命名实体列表中的字符串也不会被识别为实体引用：

````````````````````````````````{panels}
```
&MadeUpEntity;
```
---
```
<p>&amp;MadeUpEntity;</p>
```
````````````````````````````````

实体和数字字符引用在代码段或代码块以外的任何上下文中都可以识别，包括 url、链接标题和 fenced 代码块、info 字符串：

````````````````````````````````{panels}
```
<a href="&ouml;&ouml;.html">
```
---
```
<a href="&ouml;&ouml;.html">
```
````````````````````````````````


````````````````````````````````{panels}
```
[foo](/f&ouml;&ouml; "f&ouml;&ouml;")
```
---
```
<p><a href="/f%C3%B6%C3%B6" title="föö">foo</a></p>
```
````````````````````````````````


````````````````````````````````{panels}
```
[foo]

[foo]: /f&ouml;&ouml; "f&ouml;&ouml;"
```
---
```
<p><a href="/f%C3%B6%C3%B6" title="föö">foo</a></p>
```
````````````````````````````````


````````````````````````````````{panels}
````
``` f&ouml;&ouml;
foo
```
````
---
```
<pre><code class="language-föö">foo
</code></pre>
```
````````````````````````````````

实体和数字字符引用在代码段和代码块中被视为文本：

````````````````````````````````{panels}
```
`f&ouml;&ouml;`
```
---
```
<p><code>f&amp;ouml;&amp;ouml;</code></p>
```
````````````````````````````````


````````````````````````````````{panels}
```
    f&ouml;f&ouml;
```
---
```
<pre><code>f&amp;ouml;f&amp;ouml;
</code></pre>
```
````````````````````````````````

实体和数字字符引用不能用于代替 CommonMark 文档中表示结构的符号。

````````````````````````````````{panels}
```
&#42;foo&#42;
*foo*
```
---
```
<p>*foo*
<em>foo</em></p>
```
````````````````````````````````

````````````````````````````````{panels}
```
&#42; foo

* foo
```
---
```
<p>* foo</p>
<ul>
<li>foo</li>
</ul>
```
````````````````````````````````

````````````````````````````````{panels}
```
foo&#10;&#10;bar
```
---
```
<p>foo

bar</p>
```
````````````````````````````````

````````````````````````````````{panels}
```
&#9;foo
```
---
```
<p>→foo</p>
```
````````````````````````````````


````````````````````````````````{panels}
```
[a](url &quot;tit&quot;)
```
---
```
<p>[a](url &quot;tit&quot;)</p>
```
````````````````````````````````
