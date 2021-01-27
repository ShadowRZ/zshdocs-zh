# 第六章：Shell 语法

## 简单的命令与管道

一个简单的命令是一个由空格分开的单词组成的可选的参数赋值序列，且在其中可以使用重定向。有关参数位置的描述，请参见 %% 一节的开头。

第一个单词是要执行的命令，其余的单词（如果有）则是该命令的参数。如果给出了命令名称，则在执行命令时，对参数的赋值会改变命令的环境。一个简单命令的值是其返回值，若命令被信号终止，则是128 加上信号编号。例如：
```zsh
echo foo
```
是一个带有参数的简单命令。

管道既可以是简单命令，也可以是两个或多个简单命令的序列，其中每个命令之间用 `|` 或 `|＆` 分隔。如果命令之间用 `|` 分隔，则第一个命令的标准输出将连接到下一个命令的标准输入。 `|＆` 是 `2>＆1 |` 的简写，它将命令的标准输出和标准错误连接到下一个的标准输入。除非管道前面加有 `!`，管道的值就是最后一条命令的返回值。如果有 `!`，管道的值就是是最后一条命令的返回值的逻辑反。例如，
```zsh
echo foo | sed 's/foo/bar/'
```
是管道，第一个命令的输出（`foo` 加一个换行符）将传递给第二个命令的输入。

如果管道前面带有 `coproc`，则将其作为协进程（coprocess）执行并在它和父 shell 之间建立了一条双向管道。父 shell 可以通过 `>＆p` 和 `<＆p` 重定向操作符或通过 `print -p` 和 `read -p` 来读取或写入协进程。管道之前不能同时带有 `coproc` 和 `!`。如果启用了作业管理，则可以将协进程视为输入和输出，而不是普通的后台作业。

子列表可以是单个管道，也可以是由 `&&` 或 `||` 分隔的两个或多个管道的序列。如果两个管道用 `&&` 分隔，则仅当第一个管道成功（返回零状态）时才执行第二个管道。如果两个管道用 `||` 分隔，则仅当第一个失败（返回非零状态）时才执行第二个管道。两个运算符具有相同的优先级，并且是左结合的。子列表的值是最后执行的管道的值。例如，
```zsh
dmesg | grep panic && print yes
```
是一个由两个管道组成的子列表，第二个管道只是一个简单命令，仅当 `grep` 命令返回零状态时才会执行。若返回值不为零，则子列表的返回值为第一个的返回值，否则则为 `print` 的返回值（几乎肯定为零）。

列表是由以 `;`、`＆`、`＆|`、`＆!` 或换行符结尾的零个或多个子列表的序列。若列表作为出现在圆括号或花括号之间的复杂命令，最后一个子列表的终止符可以省略。当子列表以分号或换行符终止时，shell 将在执行下一个子列表之前等待其完成。如果子列表以 `＆`，`＆|` 或 `＆!` 终止，则 shell 将在后台执行它的最后一个管道，并且不等待其完成（请注意 zsh 与其他 shell 的区别在于 zsh 会在后台执行整个子列表）。在这种情况下，后台管道返回零状态。

更一般地，列表可以看作是任何 shell 命令的集合，且包含以下复杂命令。请注意下文中出现“列表”一词时，都隐含这一种情况。例如，shell 函数中的命令形成了一种特殊的列表。


## 前置命令修饰符

一个简单命令之前可能会带有前置命令修饰符，该修饰符将会更改命令的解释方式。这些修饰符是 shell 内置命令，但 `nocorrect` 除外，后者是保留字。

`-`
:   该命令按照 `argv[0]` 字符串前面的追加一个 `-` 执行。

`builtin`
:   这个命令被视为内建命令执行，而不是通常的会考虑 shell 函数和外部命令。

`command [ -pvV ]`
:   这个命令被视为外部命令执行，而不会考虑 shell 函数或者内建命令。如果设置了 `POSIX_BUILTINS` 选项，内建命令也会被考虑执行，但是某些特性会被抑制。`-p` 选项可以指示默认的搜索路径，以取代 `$path` 数组。使用 `-v` 选项的话，`command` 的行为类似于 `whence`；使用 `-V` 选项，效果等同于 `whence -v`。

`exec [ -cl ] [ -a argv0 ]`
:   以下命令以及所有参数将代替当前进程运行，而不是作为子进程运行。shell 不 fork 而被更换。该 shell 不调用 `TRAPEXIT`，也不加载（`source`）`zlogout`。提供这些选项是为了与其他 shell 兼容。   
{>>译注：即和 POSIX syscall `exec()` 的表现相似。<<}   
`-c` 选项会清理环境。   
`-l` 选项效果与 `-` 前置命令修饰符类似，将替代运行的命令作为登录 shell (login shell) 对待，并在其 `argv[0]` 前附加 `-`。该选项与 `-a` 选项一同使用无效。   
`-a` 选项用于显式指定替换命令要使用的 `argv[0]` 字符串（命令所看到的自身的名称），并且直接等效于设置 `ARGV0` 环境变量。

`nocorrect`
:   不会对任何单词进行拼写修正。它必须出现在任何其他前置命令修饰符之前，因为它会在任何解析之前立即被解释。在非交互式（non-interactive） shell 中无效。

`noglob`
:   不会对任何单词执行文件名生成（globbing）。


## Complex Commands 复杂命令

zsh 中的复杂命令是以下之一：

`if` list `then` list [ `elif` list `then` list ] ... [ `else` list ] `fi`
:   执行 `if` 后的列表，如果返回值为零，则执行 `then` 后的列表。否则，将执行 `elif` 后的列表，并且如果 `elif` 后的列表返回值为零，则执行 `then` 后的列表。如果每个 `elif` 后的列表都返回非零值，`else` 后的列表将被执行。

`for` name ... [ `in` word ... ] term `do` list `done`
:   展开单词列表，然后依次将每个单词设置参数 name ，每次执行列表。如果省略了 `in` word，请使用位置参数代替单词。   
term 包含一个或多个换行符或分号作为终止符，并且在省略 `in` word 时是可选的。   
单词列表之前可以出现多个参数 name。如果给出了 N 个参数，则在每次执行循环时，会将下 N 个字分配给相应的参数。如果参数多于其余单词，则将其余参数分别设置为空字符串。当没有剩余的单词分配给名字时，循环的执行结束。`in` 只能作为参数列表中的第一个 name 出现，否则将被视为标记参数列表的结尾。

`for` `(( [expr1] ; [expr2] ; [expr3] ))` `do` list `done`
:   首先对算术表达式 expr1 求值（请参阅 %%）。反复对算术表达式 expr2 求值，直到其求值为零，并且当算术表达式 expr2 不为零时，执行 list 并求值算术表达式 expr3。如果省略任何表达式，则其行为就像它的值为 1。

`while` list `do` list `done`
:   只要 `while` 后的列表返回值为零，就一直执行 `do` 后的列表。

`until` list `do` list `done`
:   只要 `until` 后的列表返回值为非零，就一直执行 `do` 后的列表。

`repeat` word `do` list `done`
:   扩展 word 并将其视为算术表达式，该表达式的计算结果必须为数字 n。然后执行 n 次列表。   
当 zsh 在模拟另一个 shell 的模式下启动时，默认情况下将禁用重复语法。可以使用命令 `enable -r repeat` 启用它。

`case` word `in` `[ [(]` pattern `[ |` pattern `] ... )` list `(;;|;&|;|) ]` ... `esac`
:   执行与第一个与 word 匹配的 pattern 相关联的列表（如果有）。pattern 的形式与用于 glob 的形式相同。请参阅 %%。   
还需要注意的是，尽管括号和竖线周围可能会出现空白，并且在这些点处将从图案中去除空白，除非设置了 `SH_GLOB` 选项，带有替代选项(`|`)的整个 pattern 被 shell 视为等同于括号内的一组 pattern。   
空白可能会出现在图案的其他地方；这不会被去除。如果设置了 `SH_GLOB` 选项，则可以将开括号清楚地视为语法的一部分，该表达式将解析为单独的词，并将它们视为严格的替代词（与其他shell一样）。
如果执行的列表以 `;＆` 而不是 `;;` 终止，则还将执行下面的列表。除非到达esac，否则服从列表的终止符规则 `;;`、`;＆`或 `;|` 适用。   
如果执行的列表以 `;|` 终止，shell 继续扫描 pattern 以寻找下一个匹配项，执行相应的列表，并将规则应用于相应的终止符 `;;`、`;＆` 或 `;|`。请注意，word 不会重新展开；所有适用的 pattern 均使用相同的 word 进行测试。

`select` name [ `in` word ... term ] `do` list `done`
:   其中term是一个或多个换行符或 `;` 来终止。打印一组单词，每个单词后面都有一个数字。如果省略 `in`，请使用位置参数。如果 shell 是交互式（interactive）的且处于活动状态，或者是标准输入，则将打印 `PROMPT3` 提示符，并从行编辑器中读取一行。如果此行由列出的单词之一的数字组成，则将参数 name 设置为与该数字相对应的单词。如果该行为空，则再次打印选择列表。否则，参数名称的值将设置为 `null`。从标准输入读取的行的内容保存在参数 `REPLY` 中。对每个选择执行列表，直到遇到中断或文件结束。
    where term is one or more newline or ; to terminate the words. Print the set of words, each preceded by a number. If the in word is omitted, use the positional parameters. The PROMPT3 prompt is printed and a line is read from the line editor if the shell is interactive and that is active, or else standard input. If this line consists of the number of one of the listed words, then the parameter name is set to the word corresponding to this number. If this line is empty, the selection list is printed again. Otherwise, the value of the parameter name is set to null. The contents of the line read from standard input is saved in the parameter REPLY. list is executed for each selection until a break or end-of-file is encountered.
`(` list `)`
:   在子 shell (subshell)中执行列表。内置 trap 设置的 trap 在执行列表时会重置为其默认值。

`{` list `}`
:   执行列表

`{` try-list `}` `always` `{` always-list `}`
:   首先执行 try-list 。不管错误，还是 try-list 中遇到的中断或继续命令，都执行 always-list。然后，从 try-list 的执行结果继续执行；否则，将继续执行。换句话说，任何错误，中断或继续命令都将以正常方式处理，就像 alwayslist 不存在一样。这两个代码块分别称为“try block”和“always block”。   
可选的换行符或分号可能会出现在 `always` 后面；但是请注意，它们可能不会出现在前面的右大括号和 `always` 之间。   
在这种情况下，“错误”是指诸如语法错误之类的情况，它会导致 shell 中止当前函数，脚本或列表的执行。Shell 解析代码时遇到语法错误，always-list 将不会被执行。例如，错误构造的 try-list 中的 if 块会导致 shell 在解析过程中中止，从而将不会执行 always-list ，而诸如 `${*foo*}` 之类的错误替换会导致运行时错误，之后将执行 always-list。   
可以使用特殊的整数变量 `TRY_BLOCK_ERROR` 测试并重置错误条件。在 always-list 之外，该值无关紧要，但将其初始化为 -1。在 always-list 内，如果 try-list 中发生错误，则值为 1，否则为 0。如果在 always-list 期间将 `TRY_BLOCK_ERROR` 设置为 0，则将重置由 try-list 引起的错误条件，并在 always-list 之后继续执行 shell。在 try-list 期间更改值没有用（除非这构成封闭的 always block 的一部分）。   
无论 `TRY_BLOCK_ERROR` 如何，always-list 末尾调用 `$?` 是从 try-list 返回的值。即使发生错误，即使 `TRY_BLOCK_ERROR` 设置为零，该值也不为零。   
下面的代码执行给定的代码，而忽略其引起的任何错误。这是通过在子 shell 中执行代码来保护代码的常规约定的替代方法。
```zsh
{
    # 可能导致错误的代码
  } always {
    # 不管错误如何，都将执行此代码。
    (( TRY_BLOCK_ERROR = 0 ))
}
# 错误条件已被重置。
```
当 try block 出现在任何函数之外时，try-list 中遇到的返回或退出不会导致 always-list 的执行。相反，在执行任何 EXIT trap 后，shell 会立即退出。否则，在 try-list 中遇到的 return 命令将导致 always-list 的执行，就像中断和继续一样。

`function` word ... `[ () ] [ term ] {` list `}`
word ... `() [ term ] {` list `}`
word ... `() [ term ]` command
:   其中term是一个或多个换行符或 `;`。定义一个单词可以引用的函数。通常，只提供一个单词；多个单词仅对设置 trap 有用。函数的主体是 `{` 和 `}` 之间的列表。请参阅 %%。   
如果将选项 `SH_GLOB` 设置为与其他 shell 兼容，则当一个单词时，左右括号之间可能会出现空格；否则，在这种情况下，括号将被视为形成球形图案。   
在上述任何形式中，重定向都可能出现在函数主体外部，例如：
```
func() { ... } 2>&1
```
重定向与函数一起存储，并在执行函数时应用。重定向中的所有变量都会在执行函数时扩展，但会超出函数范围。

`time` [ pipeline ]
:   执行管道，并以TIMEFMT参数指定的形式在标准错误报告时序统计信息。如果省略管道，则打印有关 shell 进程及其子进程的统计信息。

`[[` exp `]]`
:   计算条件表达式 exp，如果为 true ，则返回零。有关 exp 的描述，请参见 %%。
