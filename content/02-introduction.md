# 第二章：简介

Zsh 是一个 UNIX 命令解释器（Shell），可用于交互登录 Shell 以及作为 Shell 脚本指令处理器。在标准 Shell 中，Zsh 最接近于 _ksh_ 但附带了许多改进。在默认工作模式下它不提供与 POSIX 或其他 Shell 的兼容性：见章节 %%。

Zsh 包括了命令行编辑，内建拼写纠正，可编程命令补全，Shell 函数（包括自装入机制），历史系统，以及其他诸多功能。

## 作者

Zsh 最初由 Paul Falstad `<pf@zsh.org>` 编写。Zsh 现由 zsh-workers 邮件列表 `<zsh-workers@zsh.org>` 的成员维护。其开发现由 Peter Stephenson `<pws@zsh.org>` 协调。协调者可通过 `<coordinator@zsh.org>` 联系。但关于代码的事通常应该跑到邮件列表。

## 获取

Zsh 可从以下 HTTP 和匿名 FTP 站下载。

ftp://ftp.zsh.org/pub/  
https://www.zsh.org/pub/  

最新源码可通过 Sourceforge Git 获取。详情见 [https://sourceforge.net/projects/zsh/](https://sourceforge.net/projects/zsh/)。对此归档的操作概要可在 http://zsh.sourceforge.net/ 找到。

## 邮件列表

Zsh 有三个邮件列表：

`<zsh-announce@zsh.org>`
:   发行宣告，Shell 大变更以及月更的 Zsh FAQ。（审核）

`<zsh-users@zsh.org>`
:   用户讨论。

`<zsh-workers@zsh.org>`
:   调教，开发，Bug 报告和补丁。

如要订阅或退订，向邮件列表相应的管理地址发邮件。

`<zsh-announce-subscribe@zsh.org>`  
`<zsh-announce-subscribe@zsh.org>`  
`<zsh-users-subscribe@zsh.org>`  
`<zsh-workers-subscribe@zsh.org>`  
`<zsh-announce-unsubscribe@zsh.org>`  
`<zsh-users-unsubscribe@zsh.org>`  
`<zsh-workers-unsubscribe@zsh.org>`

**你只需要加入其中一个邮件列表，因为他们是嵌套的。**_zsh-announce_ 全部发件会自动转发到 _zsh-users_。_zsh-users_ 全部发件会自动转发到 _zsh-workers_。

如你订阅/退订时遇到任何问题，向 `<listmaster@zsh.org>` 发邮件。邮件列表由 Karsten Thygesen `<karthy@kom.auc.dk>` 维护。

邮件列表是归档的。归档可通过上方列出的管理地址访问。另有 Geoff Wing `<gcw@zsh.org>` 维护的超文本归档，可在 https://www.zsh.org/mla/ 访问。

## Zsh FAQ

Zsh 有一份由 Peter Stephenson `<pws@zsh.org>` 常见问题列表（FAQ）。它经常被发到 _comp.unix.shell_ 和 _zsh-announce_ 邮件列表。最新版可在任何 Zsh FTP 站或 http://www.zsh.org/FAQ/ 找到。与 FAQ 有关的事务的联系地址是 `<faqmaster@zsh.org>`。

## Zsh 网页

Zsh 有一个在 https://www.zsh.org/ 的网页。它由 SunSITE Denmark 的 Karsten Thygesen `<karthy@zsh.org>` 维护。与网页有关的事务的联系地址是 `<webmaster@zsh.org>`。

## Zsh 用户手册

用户手册正处于准备阶段。它主要用于手册的补充，包括对手册中神秘的部分的解说和提示。可在 http://zsh.sourceforge.net/Guide/ 以现状查看。编写此文档时，与启动文件有关的章节和内容，以及新补全系统部分已基本完成。

## 另见

手册页 `sh(1)`，手册页 `csh(1)`，手册页 `tcsh(1)`，手册页 `rc(1)`，手册页 [`bash(1)`](https://tiswww.case.edu/php/chet/bash/bash.html)，手册页 `ksh(1)`
