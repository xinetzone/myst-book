# 简介

Markdown 是一种用于编写结构化文档的纯文本格式，基于在电子邮件和 usenet 帖子中指示格式的约定。它由 John Gruber（在 Aaron Swartz 的帮助下）开发，并于 2004 年以 [语法描述](http://daringfireball.net/projects/markdown/syntax) 和 Perl 脚本（`Markdown.pl`）的形式发布，用于将 Markdown 转换为 HTML。在接下来的十年里，用多种语言开发了几十种实现。有些人使用脚注、表和其他文档元素的约定扩展了原始的 Markdown 语法。有些允许 Markdown 文档以 HTML 以外的格式呈现。像 Reddit, StackOverflow 和 GitHub 这样的网站有数百万人在使用 Markdown。Markdown 开始在网络之外使用，写书、文章、幻灯片、信件和课堂笔记。

Markdown 与许多其他轻量级标记语法（它们通常更容易编写）的区别在于它的可读性。正如 Gruber 写道：

> Markdown 格式语法的首要设计目标是使其可读性尽可能高。其思想是，markdown 格式的文档应该是可以按原样发布的，作为纯文本，而不是看起来像用标记或格式化指令标记的文档。（http://daringfireball.net/projects/markdown/）

这一点可以通过比较一个样例的 AsciiDoc 和一个等效的样例 Markdown 来说明：

````{panels}
:column: col-12
:card: border-2

AsciiDoc
^^^
```
1. List item one.
+
List item one continued with a second paragraph followed by an
Indented block.
+
.................
$ ls *.sh
$ mv *.sh ~/tmp
.................
+
List item continued with a third paragraph.

2. List item two continued with an open block.
+
--
This paragraph is part of the preceding list item.

a. This list is nested and does not require explicit item
continuation.
+
This paragraph is part of the preceding list item.

b. List item b.

This paragraph belongs to item two of the outer list.
--
```
---
Markdown
^^^
```
1.  List item one.

    List item one continued with a second paragraph followed by an
    Indented block.

        $ ls *.sh
        $ mv *.sh ~/tmp

    List item continued with a third paragraph.

2.  List item two continued with an open block.

    This paragraph is part of the preceding list item.

    1. This list is nested and does not require explicit item continuation.

       This paragraph is part of the preceding list item.

    2. List item b.

    This paragraph belongs to item two of the outer list.
```
````

可以说，AsciiDoc 版本更容易编写。你不需要担心缩进。但 Markdown 版本更容易阅读。列表项的嵌套在源代码中很明显，而不只是在处理过的文档中。

## 为什么需要一个规范？

John Gruber 对 Markdown 语法的规范描述并没有明确地说明语法。以下是一些它无法回答的问题：

1. 一个子列表需要多少缩进？规范说，连续段落需要缩进四个空格，但对于子列表没有完全明确。人们很自然地认为它们也必须缩进四个空格，但 `Markdown.pl` 不要求这样做。这并不是一个“极端情况”，在这个问题上的实现之间的分歧常常会让真实文档中的用户感到惊讶。（参见[ John Gruber 的评论](http://article.gmane.org/gmane.text.markdown.general/1997)。）
2. 在一个块引号或标题之前需要一个空白行吗？大多数实现不需要空行。然而，这可能会在硬包装文本中导致意想不到的结果，并在解析中产生歧义（注意，一些实现将标题放在 blockquote 中，而另一些没有）。（约翰·格鲁伯(John Gruber) 也表示支持[要求填写空白行](http://article.gmane.org/gmane.text.markdown.general/2146)。）
3. 在缩进代码块之前需要一个空行吗?（`Markdown.pl` 需要它，但文档中没有提到，而且一些实现不需要它。）

    ```
    paragraph
        code?
    ```

4. 确定列表项在标记中包装的确切规则是什么？一个列表可以部分“松”部分“紧”吗？我们应该怎么处理这样一个列表？

    ```
    1. one

    2. two
    3. three
    ```

    或者

    ```
    1.  one
        - a

        - b
    2.  two
    ```

    （这里有一些[约翰·格鲁伯的相关评论](http://article.gmane.org/gmane.text.markdown.general/2554)。）

5. 列表标记可以缩进吗？有序列表标记可以右对齐吗？

    ```
     8. item 1
     9. item 2
    10. item 2a
    ```

6. 这是一个在第二个项目中带有主题中断的列表，还是两个列表之间由一个主题中断分开？

    ```
    * a
    * * * * *
    * b
    ```

7. 当列表标记从数字变为项目符号时，我们有两个列表还是一个列表？（Markdown 语法描述建议有两个，但 perl 脚本和许多其他实现会产生一个。）

    ```
    1. fee
    2. fie
    -  foe
    -  fum
    ```

8. 内联结构标记的优先级规则是什么？例如，下面的链接是有效的吗？还是代码范围优先？

    ```
    [a backtick (`)](/url) and [another backtick (`)](/url).
    ```

9. emphasis 标记和 strong emphasis 标记的优先规则是什么？例如，应该如何解析以下内容？

    ```
    *foo *bar* baz*
    ```

10. 块级和内联级结构之间的优先规则是什么？例如，应该如何解析以下内容？

    ```
    - `a long code span can contain a hyphen like this
    - and it can screw things up`
    ```

11. 列表项目可以包括 section 标题吗？（`Markdown.pl` 不允许这样做，但允许块引号包含标题。）

    ```
    - # Heading
    ```

12. 列表项目可以是空的吗？

    ```
    * a
    *
    * b
    ```

13. 链接引用可以在块引号或列表项中定义吗？

    ```
    > Blockquote [foo].
    >
    > [foo]: /url
    ```

14. 如果同一个引用有多个定义，哪个优先？

    ```
    [foo]: /url1
    [foo]: /url2

    [foo][]
    ```

在没有规范的情况下，早期的实现人员参考 `Markdown.pl` 来解决这些歧义。但是 `Markdown.pl` 有很多 bug，在很多情况下会给出很糟糕的结果，所以它并不是规范的满意替代品。

因为没有明确的规范，实现有很大的差异。因此，用户经常会惊讶地发现，在一个系统上以一种方式呈现的文档（例如，GitHub wiki）在另一个系统上呈现的方式不同（例如，使用 pandoc 转换为 docbook）。更糟糕的是，由于 Markdown 中没有什么可以算作“语法错误”，这种差异通常不会马上被发现。

## 关于此文档

本文试图明确地指定 Markdown 语法。它包含了许多使用并排 Markdown 和 HTML 的示例。这些测试的目的是作为一致性测试。附带的脚本 [`spec_tests.py`](https://github.com/commonmark/commonmark-spec/blob/master/test/spec_tests.py) 可用于对任何 Markdown 程序运行测试：

```sh
python test/spec_tests.py --spec spec.txt --program PROGRAM
```

由于本文档描述了如何将 Markdown 解析为抽象语法树，所以使用语法树的抽象表示而不是 HTML 是有意义的。但是 HTML 能够表示我们需要做出的结构上的区别，并且选择 HTML 作为测试，使得在不编写抽象语法树渲染器的情况下运行针对实现的测试成为可能。

注意，规范并不是强制要求 HTML 示例的每一个特性，例如，规范规定了什么算作链接目的地，但它没有强制要求 URL 中的非 ascii 字符采用百分比编码。要使用自动测试，需要提供一个符合规范示例（url 中百分比编码的非 ascii 字符）的呈现程序。但是符合标准的实现可以使用不同的渲染器，并且可以选择不对 url 中的非 ascii 字符进行百分比编码。

该文档是从一个文本文件 `spec.txt` 生成的，该文件用 Markdown 编写，带有一个用于并排测试的小扩展名。脚本工具 `tools/makespec.py` 可以用来将 `spec.txt` 转换为 HTML 或 CommonMark（然后可以转换为其他格式）。

在示例中，→ 字符用于表示制表符。
