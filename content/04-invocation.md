# 第四章：启动

## 启动

Shell 被执行时会识别以下参数确定 Shell 从哪里读取指令：

`-c`
:   取第一个参数作为执行的命令，而非从脚本或标准输入读取命令。若其后给了任何参数，第一个被赋值 `$0`，而非作为位置参数使用。

`-i`
:   强制交互 Shell，仍可以指定执行的脚本。

`-s`
:   强制 Shell 从标准输入读取命令。若未给 `-s` 参数且传入一个参数，第一参数作为要执行的脚本的路径。

若选项处理后仍有剩余参数，且 `-c` 和 `-s` 都未指定，第一参数作为包含 Shell 命令的脚本文件名。如设置选项 `PATH_SCRIPT`，且文件名不含目录路径（即名字中没有 `/`），先按当前目录后按变量 `PATH` 中指定的命令路径搜索脚本。如未设该选项或名字中包含 `/` 它会被直接使用。

在按上述内容处理前一两个参数后，余下的参数即作为位置参数。

更多常见于启动与内建命令 `set` 的选项，详见 [-> 选项](16-options.md)

可向 Shell 传递长选项 `--emulate` 及（以一个单独的词形式）尾随其后的一个模拟模式。模拟模式是 `emulate` 中描述的模式，详见 [-> Shell 内建命令](17-builtins.md)。`--emulate` 选项必须置于其他（可能被覆盖的）选项之前，所以可用于改变要求的模拟模式。注意相较于 Shell 内的 `emulate` 命令，使用此选项后会为平滑模拟做出额外步骤：例如 Shell 内像 `path` 这样与 POSIX 用法相违背的变量不会被定义。

选项可以用 `-o` 选项按名字指定。`-o` 像一个单字符选项，但取下一个字符串作为选项名。例如：

```zsh
zsh -x -o shwordsplit scr
```

运行脚本 `scr`，通过相应字符 `-x` 设置 `XTRACE` 选项并通过名字设置 `SH_WORD_SPLIT` 选项。可使用 `+o` 而非 `-o` _关闭_ 选项。`-o` 可以与前置的单字符选项堆叠在一起，所以举例 `-xo shwordsplit` 或 `-xoshwordsplit` 等效于 `-x -o shwordsplit`。

选项也可以通过 GNU 长选项风格 `--option-name` 按名字指定。这么做时，选项名中允许有 `-`：它们被转换成 `_`，因此被忽略。所以举例，`zsh --sh-word-split` 执行 Zsh 并启用 `SH_WORD_SPLIT` 选项。像其他选项语法一样，可以通过把首个 `-` 换成 `+` 来关闭选项；因此 `+-sh-word-split` 等效于 `--no-sh-word-split`。不像其他选项格式，GNU 式长选项不可以和其他任何选项堆在一起。因此举例 `-x-shwordsplit` 是个错误，而非像 `-x --shwordsplit` 那样处理。

特殊的 GNU 式选项 `--version` 也会被处理；它会向标准输出输出 Shell 版本信息，然后成功退出。`--help` 也会被处理；它会向标准输出输出当执行 Shell 时可用的选项。然后成功退出。

选项处理可以以两种方式结束，允许随后以 `-` 或 `+` 开头的参数作为普通参数使用。首先，单独的 `-`（或 `+`）本身作为参数使用会结束选项处理。其次是特殊选项 `--`（或 `+-`），可以单独使用（这是标准 POSIX 用法）或前置堆叠选项（所以 `-x-` 与 `-x --` 等效）`--` 后不允许堆叠选项（所以 `-x-f` 是个错误），但注意上文提及的 GNU 式选项，此时允许 `--shwordsplit` 而不结束选项处理。

除非 _sh_/_ksh_ 模拟的单字符选项有效，`-b`（或 `+b`）结束选项处理。`-b` 类似于 `--`，除了更多的单字符选项可以堆叠在 `-b` 之后且会正常产生效果。

## 兼容性

当 Zsh 被以 `sh` 或 `ksh` 运行时，会相应模拟 _sh_ 或 _ksh_；更准确说，它首先查看它被启动时的名字里的第一个字符，除去任何开头的 `r`（假定表示“限制 Shell”），如果是 `b`，`s` 或 `k` 会模拟 _sh_ 或 _ksh_。并且，若以 `su` 执行（某些系统当 Shell 被 su 执行时会这样），Shell 会试图从 `SHELL` 环境变量中取备选名字并基于此执行模拟。

在 _sh_ 和 _ksh_ 兼容模式下以下参数失去特殊性且 Shell 不载入：`ARGC`，`argv`，`cdpath`，`fignore`，`fpath`，`HISTCHARS`，`mailpath`，`MANPATH`，`manpath`，`path`，`prompt`，`PROMPT`，`PROMPT2`，`PROMPT3`，`PROMPT4`，`psvar`，`status`，`watch`。 

通常的 Zsh 启动/退出脚本不会被执行。登录 Shell 读取 `/etc/profile` 而后是 `$HOME/.profile`。若执行时设置 `ENV` 环境变量，读取登录脚本后读取 `$ENV`。`ENV` 的值会在解析为路径名时受到参数展开，命令替换，和算术展开。注意 `PRIVILEGED` 选项也会影响启动文件的执行。

若 Shell 以 `sh` 或 `ksh` 执行，以下选项会被设置：`NO_BAD_PATTERN`，`NO_BANG_HIST`，`O_BG_NICE`，`NO_EQUALS`，`NO_FUNCTION_ARGZERO`，`GLOB_SUBST`，`NO_GLOBAL_EXPORT`，`NO_HUP`，`INTERACTIVE_COMMENTS`，`KSH_ARRAYS`，`NO_MULTIOS`，`NO_NOMATCH`，`NO_NOTIFY`，`POSIX_BUILTINS`，`NO_PROMPT_PERCENT`，`RM_STAR_SILENT`，`SH_FILE_EXPANSION`，`SH_GLOB`，`SH_OPTION_LETTERS`，`SH_WORD_SPLIT`。同时若 Zsh 以 `sh` 执行还会设置选项 `BSD_ECHO` 和 `IGNORE_BRACES`。同时若 Zsh 以 `ksh` 执行会设置选项 `KSH_OPTION_PRINT`，`LOCAL_OPTIONS`，`PROMPT_BANG`，`PROMPT_SUBST` 和 `SINGLE_LINE_ZLE`。

## 限制 Shell

当 Zsh 以 `r` 开头的命令基础名执行或启动时指定 `-r`，Shell 会变成限制 Shell。模拟模式在去掉启动名前的 `r` 之后确定。限制模式中禁用以下事项：

- 用 `cd` 内建命令更改目录
- 改变或取消设置参数 `EGID`，`EUID`，`GID`，`HISTFILE`，`HISTSIZE`，`IFS`，`LD_AOUT_LIBRARY_PATH`，`LD_AOUT_PRELOAD`，`LD_LIBRARY_PATH`，`LD_PRELOAD`，`MODULE_PATH`，`module_path`，`PATH`，`path`，`SHELL`，`UID` 和 `USERNAME`。
- 指定带 `/` 的指令名
- 用 `hash` 指定命令路径名
- 重定向输出到文件
- 用 `exec` 内建命令把 Shell 替换为其他命令
- 用 `jobs -Z` 改写 Shell 进程的参数和环境空间
- 用 `ARGV0` 参数覆盖外部指令的 `argv[0]`
- 用 `set +r` 或 `unsetopt RESTRICTED` 关闭限制模式

这些限制在处理完启动文件后被强制应用。启动文件应该把 `PATH` 指向含有能安全在限制环境里运行的命令的目录。也可以通过关闭内建命令添加更多限制。

限制模式也可以在任何时候通过设置 `RESTRICTED` 选项启动。这会导致以上限制被立即应用，即使 Shell 仍未处理完启动文件。
