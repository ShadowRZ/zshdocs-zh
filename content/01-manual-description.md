# 第一章：Zsh 手册

!!! note "译者注释"
    这部分内容是描述如何生成 Zsh 原文档的，因此不适用于翻译版。

本文档是通过 Zsh 发行中，`Doc` 子目录里的 `zsh.texi` 生成的。

## 从 zsh.texi 中生成文档

Texinfo 源可转为以下格式：

Info 手册
:   Info 格式允许从诸多索引中搜索话题、命令、函数等。用 `makeinfo zsh.texi` 生成 Info 文档。

打印手册
:   `texi2dvi zsh.dvi` 命令会生成 `zsh.dvi`，它可以用 _dvips_ 和可选的 _gs_ (Ghostscript) 生成格式较好的手册。

HTML 手册
:   HTML 手册可通过以下位置在 Zsh 网站上找到：  
    [http://zsh.sourceforge.net/Doc/](http://zsh.sourceforge.net/Doc/).  
    （HTML 版使用 _text2html_ 生成，它可从 http://www.nongnu.org/texi2html/ 获取。命令是：`texi2html –output . –ifinfo –split=chapter –node-files zsh.texi`。如有必要，升级到 1.78 版本的 text2html。）

对于那些没有必要工具处理 Texinfo 的人们，预编译文档（Postscript，DVI，PDF 和 HTML 格式）可从 Zsh 归档站或其镜像获取。在文件 `zsh-doc.tar.gz` 中。（站点列表见 [-> 获取](02-introduction.md#_3)）
{>>译注：我们呢？<<}
