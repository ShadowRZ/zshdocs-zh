# 第五章：文件

## 启动 / 退出文件

命令会首先从 `/etc/zshenv` 读取，这一点不可以被覆盖。随后的行为会被选项 `RCS` 和 `GLOBAL_RCS` 修改；前者影响所有启动文件，后者只影响全局启动文件（即此处所述文件中以 `/` 开头的文件）。若任意时刻取消设定其中一个选项，任何对应类型的启动文件随后都不会被读取。`$ZDOTDIR` 中的一个文件重新启用 `$GLOBAL_RCS` 亦可。`RCS` 及 `GLOBAL_RCS` 默认都被设定。

随后命令会从 `$ZDOTDIR/.zshenv` 读取，若 Shell 是登录 Shell，命令会先后从 `/etc/zprofile` 及 `$ZDOTDIR/.zprofile` 读取。然后若 Shell 是交互的，命令会先后从 `/etc/zshrc` 及 `$ZDOTDIR/.zshrc` 读取。最后如果 Shell 是登录 Shell，读取 `/etc/zlogin` 和 `$ZDOTDIR/.zlogin`。

登录 Shell 退出的时候，会读取 `$ZDOTDIR/.zlogout` 和 `/etc/zlogout`。这会在通过 `exit` 或 `logout` 命令显式退出或者终端读取文件末尾标识符的隐式退出。然而若 Shell 因 `exec` 到其他程序退出，将不会读取登出文件。这也会受选项 `RCS` 和 `GLOBAL_RCS` 的影响。注意 `RCS` 选项影响历史文件的保存，即若 Shell 退出时未设定 `RCS`，就不会保存历史文件。

若 `ZDOTDIR` 未设定，会使用 `HOME` 代替。取决于安装，上述位于 `/etc` 的文件也可能在另一目录。

由于 `/etc/zshenv` 会被所有 Zsh 实例运行，保持它尽可能小很重要，把不需要每个 Shell 都运行的代码置于形如 `if [[ -o rcs ]]; then ...` 的测试代码后也是好主意，这样 Zsh 带 `-f` 选项启动时就不会执行它们。

## 文件列表

`$ZDOTDIR/.zshenv`  
`$ZDOTDIR/.zprofile`  
`$ZDOTDIR/.zshrc`  
`$ZDOTDIR/.zlogin`  
`$ZDOTDIR/.zlogout`  
`${TMPPREFIX}*` （默认 /tmp/zsh*）  
`/etc/zshenv`  
`/etc/zprofile`  
`/etc/zshrc`  
`/etc/zlogin`  
`/etc/zlogout`（取决于安装 - 默认 `/etc/`）

上述任何文件都可以用内置命令 `zcompile` 预编译（详见 [-> Shell 内建命令](17-builtins.md)）。若存在编译文件（以原文件名加 `.zwc` 扩展名形式命名）且比原文件新，会使用编译文件。
