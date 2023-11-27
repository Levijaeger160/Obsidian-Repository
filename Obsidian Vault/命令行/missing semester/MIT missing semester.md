# The Shell

## shell 是什么？

为了充分利用计算机的能力，我们不得不回到最根本的方式，使用文字接口：Shell.几乎所有您能够接触到的平台都支持某种形式的 shell，有些甚至还提供了多种 shell 供您选择。虽然它们之间有些细节上的差异，但是其核心功能都是一样的：**它允许你执行程序，输入并获取某种半结构化的输出**。

本节课我们会使用 Bourne Again SHell, 简称 **“bash”** 。 这是被最广泛使用的一种 shell，它的语法和其他的 shell 都是类似的。打开shell 提示符（您输入指令的地方），您首先需要打开终端 。您的设备通常都已经内置了终端，或者您也可以安装一个，非常简单。

## 使用 shell

当您打开终端时，您会看到一个提示符，它看起来一般是这个样子的：
``` bash
missing:~$ 
```

这是 shell 最主要的文本接口。它告诉你，你的主机名是 `missing` 并且您当前的工作目录（”current working directory”）或者说您当前所在的位置是 `~` (表示 “home”)。 `$` 符号表示您现在的身份不是 root 用户（稍后会介绍）。在这个提示符中，您可以输入命令 ，命令最终会被 shell 解析。最简单的命令是执行一个程序：
```bash
missing:~$ date  
Fri 10 Jan 2020 11:49:31 AM EST  
missing:~$ 
```
这里，我们执行了 `date` 这个程序，不出意料地，它打印出了当前的日期和时间。然后，shell 等待我们输入其他命令。我们可以在执行命令的同时向程序传递参数 ：

```bash
missing:~$ echo hello  
hello
```
上例中，我们让 shell 执行 `echo` ，同时指定参数 `hello`。`echo` 程序将该参数打印出来。 **shell 基于空格分割命令并进行解析，然后执行第一个单词代表的程序，并将后续的单词作为程序可以访问的参数**。如果您希望传递的参数中包含空格（例如一个名为 My Photos 的文件夹），您要么用使用单引号，双引号将其包裹起来，要么使用转义符号 `\` 进行处理（`My\ Photos`）。

但是，shell 是如何知道去哪里寻找 `date` 或 `echo` 的呢？其实，**类似于 Python 或 Ruby，shell 是一个编程环境**，所以它具备变量、条件、循环和函数（下一课进行讲解）。当你在 shell 中执行命令时，您实际上是在**执行一段 shell 可以解释执行的简短代码**。如果你要求 shell 执行某个指令，但是该指令并不是 shell 所了解的编程关键字，那么它会去咨询**环境变量** `$PATH`，它会列出当 shell 接到某条指令时，进行程序搜索的路径：
``` bash
missing:~$ echo $PATH  
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin  
missing:~$ which echo  
/bin/echo  
missing:~$ /bin/echo $PATH  
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

当我们执行 `echo` 命令时，shell 了解到需要执行 `echo` 这个程序，随后它便会在 `$PATH` 中搜索由 `:` 所分割的一系列目录，基于名字搜索该程序。当找到该程序时便执行（假定该文件是 可执行程序，后续课程将详细讲解）。**确定某个程序名代表的是哪个具体的程序，可以使用 `which` 程序。** 我们也可以绕过 `$PATH`，通过直接指定需要执行的程序的路径来执行该程序
``` bash
cd - #回到上一个  
cd ~ #回到home
```
## 在shell中导航

shell 中的路径是一组被分割的目录，在 Linux 和 macOS 上使用 `/` 分割，而在Windows上是 `\`。路径 `/` 代表的是系统的根目录，所有的文件夹都包括在这个路径之下，在Windows上每个盘都有一个根目录（例如： `C:\`）。 我们假设您在学习本课程时使用的是 Linux 文件系统。如果某个路径以 `/` 开头，那么它是一个 **绝对路径**，其他的都是 **相对路径** 。相对路径是指相对于当前工作目录的路径，当前工作目录可以使用 `pwd` 命令来获取。此外，切换目录需要使用 `cd` 命令。在路径中，`.` 表示的是当前目录，而 `..` 表示上级目录：
``` bash
missing:~$ pwd  
/home/missing  
missing:~$ cd /home  
missing:/home$ pwd  
/home  
missing:~$ ../../bin/echo hello  
hello
```

注意，shell 会实时显示当前的路径信息。您可以通过配置 shell 提示符来显示各种有用的信息，这一内容我们会在后面的课程中进行讨论。
一般来说，当我们运行一个程序时，**如果我们没有指定路径，则该程序会在当前目录下执行**。例如，我们常常会搜索文件，并在需要时创建文件。
为了查看指定目录下包含哪些文件，我们使用 `ls` 命令：
```bash
missing:/home$ cd ..  
missing:/$ ls  
bin  
boot  
dev  
etc  
home  
...
```

**除非我们利用第一个参数指定目录，否则 `ls` 会打印当前目录下的文件**。 大多数的命令接受标记和选项（带有值的标记），它们以 `-` 开头，并可以改变程序的行为。通常，在执行程序时使用 `-h` 或 `--help` 标记可以打印帮助信息，以便了解有哪些可用的标记或选项。例如，`ls --help` 的输出如下：
``` bash
-l    # use a long listing format  
missing:~$ ls -l /home  
drwxr-xr-x 1 missing  users  4096 Jun 15  2019 missing
```

这个参数可以更加详细地列出目录下文件或文件夹的信息。首先，**本行第一个字符 `d` 表示 `missing` 是一个目录。然后接下来的九个字符，每三个字符构成一组。 （`rwx`）. 它们分别代表了文件所有者（`missing`），用户组（`users`） 以及其他所有人具有的权限。** 其中 `-` 表示该用户不具备相应的权限。
1. 从上面的信息来看，只有文件所有者可以修改（`w`），`missing` 文件夹 （例如，添加或删除文件夹中的文件）。
2. 为了进入某个文件夹，用户需要具备该文件夹以及其父文件夹的“搜索”权限（以“可执行”：`x`）权限表示。
3. 为了列出它的包含的内容，用户必须对该文件夹具备读权限（`r`）。对于文件来说，权限的意义也是类似的。 注意，`/bin` 目录下的程序在最后一组，即表示所有人的用户组中，均包含 `x` 权限，也就是说任何人都可以执行这些程序。

在这个阶段，还有几个趁手的命令是您需要掌握的，例如 `mv`（用于重命名或移动文件）、 `cp`（拷贝文件）以及 `mkdir`（新建文件夹）。

如果您想要知道关于程序参数、输入输出的信息，亦或是想要了解它们的工作方式，请试试 `man` 这个程序。它会接受一个程序名作为参数，然后将它的文档（用户手册）展现给您。注意，使用 `q` 可以退出该程序。
```bash
missing:~$ man ls
```

`Ctrl+L` 清空界面

## 在程序间创建连接

**在 shell 中，程序有两个主要的“流”：它们的输入流和输出流。** 当程序尝试读取信息时，它们会从输入流中进行读取，当程序打印信息时，它们会将信息输出到输出流中。 通常，一个程序的输入输出流都是您的终端。也就是，您的键盘作为输入，显示器作为输出。 但是，我们也可以**重定向**这些流！

最简单的重定向是 `< file` 和 `> file`。这两个命令可以将程序的输入输出流分别**重定向到文件**：
``` bash
missing:~$ echo hello > hello.txt  
missing:~$ cat hello.txt  
hello  
missing:~$ cat < hello.txt  
hello  
missing:~$ cat < hello.txt > hello2.txt  
missing:~$ cat hello2.txt  
hello
```

您还可以使用 `>>` 来向一个文件**追加内容**。使用管道（ pipes ），我们能够更好的利用文件重定向。 `|` **操作符允许我们将一个程序的输出和另外一个程序的输入连接起来**：
``` bash
missing:~$ ls -l / | tail -n1  
drwxr-xr-x 1 root  root  4096 Jun 20  2019 var  
missing:~$ curl --head --silent google.com | grep --ignore-case content-length | cut --delimiter=' ' -f2  
#curl访问网站, grep提取内容, cut裁剪文本  
219
```

`cat`显示文件内容
``` bash
cat test.txt  
cat < test.txt #重定向  
cat > test.txt < hello.txt #重定向
```
## 一个功能全面又强大的工具

对于大多数的类 Unix 系统，有一类用户是非常特殊的，那就是：根用户（root user）。 您应该已经注意到了，在上面的输出结果中，根用户几乎不受任何限制，他可以创建、读取、更新和删除系统中的任何文件。 通常在我们并不会以根用户的身份直接登录系统，因为这样可能会因为某些错误的操作而破坏系统。 取而代之的是我们会在需要的时候使用 `sudo` 命令。顾名思义，它的作用是让您可以以 su（super user 或 root 的简写）的身份执行一些操作。 当您遇到拒绝访问（permission denied）的错误时，通常是因为此时您必须是根用户才能操作。然而，请再次确认您是真的要执行此操作。

有一件事情是您必须作为根用户才能做的，那就是向 `sysfs` 文件写入内容。**系统被挂载在 `/sys` 下，`sysfs` 文件则暴露了一些内核（kernel）参数。** 因此，您不需要借助任何专用的工具，就可以轻松地在运行期间配置系统内核。
例如，您笔记本电脑的屏幕亮度写在 `brightness` 文件中，它位于
``` bash
/sys/class/backlight
```

通过将数值写入该文件，我们可以改变屏幕的亮度。现在，蹦到您脑袋里的第一个想法可能是：
``` bash
$ sudo find -L /sys/class/backlight -maxdepth 2 -name '*brightness*'  
/sys/class/backlight/thinkpad_screen/brightness  
$ cd /sys/class/backlight/thinkpad_screen  
$ sudo echo 3 > brightness  
An error occurred while redirecting file 'brightness' 
open: Permission denied
```

出乎意料的是，我们还是得到了一个错误信息。毕竟，我们已经使用了 `sudo` 命令！关于 shell，有件事我们必须要知道。`|`、`>`、和 `<` 是通过 shell 执行的，而不是被各个程序单独执行。 `echo` 等程序并不知道 `|` 的存在，它们只知道从自己的输入输出流中进行读写。 对于上面这种情况， **shell (权限为您的当前用户) 在设置 `sudo echo` 前尝试打开 brightness 文件并写入，但是系统拒绝了 shell 的操作因为此时 shell 不是根用户**。
明白这一点后，我们可以这样操作：
``` bash
$ echo 3 | sudo tee brightness   # tee
```

**因为打开 `/sys` 文件的是 `tee` 这个程序，并且该程序以 `root` 权限在运行，因此操作可以进行。** 这样您就可以在 `/sys` 中愉快地玩耍了，例如修改系统中各种LED的状态（路径可能会有所不同）：
``` bash
$ echo 1 | sudo tee /sys/class/leds/input6::scrolllock/brightness
```

或者使用
``` bash
$ sudo su  # echo ... #进入root后开头从$变成#
```

[[MIT missing semester - homework#课程概览与 shell]]
# Shell 工具和脚本

## Shell 脚本

大多数shell都有自己的一套脚本语言，包括变量、控制流和自己的语法。shell脚本与其他脚本语言不同之处在于，**shell 脚本针对 shell 所从事的相关工作进行来优化**。因此，创建命令流程（pipelines）、将结果保存到文件、从标准输入中读取输入，这些都是 shell 脚本中的原生操作，这让它比通用的脚本语言更易用。本节中，我们会专注于 **bash 脚本**，因为它最流行，应用更为广泛。

在bash中为**变量赋值**的语法是`foo=bar`，访问变量中存储的数值，其语法为 `$foo`。 需要注意的是，`foo = bar` **使用空格隔开是不能正确工作的**，因为解释器会调用程序`foo` 并将 `=` 和 `bar`作为参数。 总的来说，在shell脚本中使用空格会起到分割参数的作用，有时候可能会造成混淆，请务必多加检查。

Bash中的字符串通过`'` 和 `"`分隔符来定义，但是它们的含义并不相同。**以`'`定义的字符串为原义字符串，其中的变量不会被转义，而 `"`定义的字符串会将变量值进行替换。**
```bash
foo=bar  
echo "$foo"  
# 打印 bar  
echo '$foo'  
# 打印 $foo
```

和其他大多数的编程语言一样，`bash`也支持`if`, `case`, `while` 和 `for` 这些控制流关键字。同样地， `bash` 也支持函数，它可以接受参数并基于参数进行操作。下面这个函数是一个例子，它会创建一个文件夹并使用`cd`进入该文件夹。
``` bash
mcd () {  
    mkdir -p "$1"  
    cd "$1"  
}
```
**这里 `$1` 是脚本的第一个参数。**与其他脚本语言不同的是，bash使用了很多特殊的变量来表示参数、错误代码和相关变量。下面是列举来其中一些变量，更完整的列表可以参考[这里](https://www.tldp.org/LDP/abs/html/special-chars.html)。

使用脚本前定义其存在
``` bash
source mcd.sh
```

- `$0` - 脚本名
- `$1` 到 `$9` - 脚本的参数。 `$1` 是第一个参数，依此类推。
- `$@` - 所有参数
- `$#` - 参数个数
- `$?` - 前一个命令的返回值
- `$$` - 当前脚本的进程识别码
- `!!` - 完整的上一条命令，包括参数。常见应用：当你因为权限不足执行命令失败时，可以使用 `sudo !!`再尝试一次。
- `$_` - 上一条命令的最后一个参数。如果你正在使用的是交互式 shell，你可以通过按下 `Esc` 之后键入 . 来获取这个值。

**命令通常使用 `STDOUT`来返回输出值，使用`STDERR` 来返回错误及错误码，便于脚本以更加友好的方式报告错误。** 返回码或退出状态是脚本/命令之间交流执行状态的方式。返回值0表示正常执行，其他所有非0的返回值都表示有错误发生。

**退出码可以搭配 `&&`（与操作符）和 `||`（或操作符）使用，用来进行条件判断，决定是否执行其他程序。** 它们都属于短路[运算符](https://en.wikipedia.org/wiki/Short-circuit_evaluation)（short-circuiting） **同一行的多个命令可以用`;`分隔。** 程序 `true` 的返回码永远是`0`，`false` 的返回码永远是`1`。让我们看几个例子
``` bash
false || echo "Oops, fail"  
# Oops, fail  
  
true || echo "Will not be printed"  
#  
  
true && echo "Things went well"  
# Things went well  
  
false && echo "Will not be printed"  
#  
  
false ; echo "This will always run" #先运行false,再是echo  
# This will always run
```

另一个常见的模式是**以变量的形式获取一个命令的输出**，这可以通过命令替换（command substitution）实现。
当您通过 `$( CMD )` 这样的方式来执行`CMD` 这个命令时，它的输出结果会替换掉 `$( CMD )` 。例如，如果执行 `for file in $(ls)` ，shell首先将调用`ls` ，然后遍历得到的这些返回值。
``` bash
echo "We are in $(pwd)"  
# We are in \mnt\c  
  
cat <(ls) <(ls ..)
```

还有一个冷门的类似特性是**进程替换（process substitution）**， `<( CMD )` 会执行 `CMD` 并将结果输出到一个临时文件中，并将 `<( CMD )` 替换成临时文件名。这在我们希望返回值通过文件而不是STDIN传递时很有用。例如， `diff <(ls foo) <(ls bar)` 会显示文件夹 `foo` 和 `bar` 中文件的区别。

说了很多，现在该看例子了，下面这个例子展示了一部分上面提到的特性。这段脚本会遍历我们提供的参数，使用`grep` 搜索字符串 `foobar`，如果没有找到，则将其作为注释追加到文件中。
``` bash
#!/bin/bash  
​  
echo "Starting program at $(date)" # date会被替换成日期和时间  
​  
echo "Running program $0 with $# arguments with pid $$" 
``` 
​  
``` bash
for file in "$@"; do  
    grep foobar "$file" > /dev/null 2> /dev/null  
    # 如果模式没有找到，则grep退出状态为 1  
    # 我们将标准输出流和标准错误流重定向到Null，因为我们并不关心这些信息  
    if [[ $? -ne 0 ]]; then  
        echo "File $file does not have any foobar, adding one"  
        echo "# foobar" >> "$file"  
    fi  
done
```

在条件语句中，我们比较 `$?` 是否等于0。 Bash实现了许多类似的比较操作，您可以查看 [`test 手册`](https://man7.org/linux/man-pages/man1/test.1.html)。 **在bash中进行比较时，尽量使用双方括号 `[[ ]]` 而不是单方括号 `[ ]`，这样会降低犯错的几率，尽管这样并不能兼容 `sh`。** 更详细的说明参见[这里](http://mywiki.wooledge.org/BashFAQ/031)。

当执行脚本时，我们经常需要提供形式类似的参数。bash使我们可以轻松的实现这一操作，它可以**基于文件扩展名展开表达式**。这一技术被称为shell的**通配**（globbing）
- **通配符** - 当你想要利用通配符进行匹配时，你可以分别使用 `?` 和 `*` 来匹配一个或任意个字符。例如，对于文件`foo`, `foo1`, `foo2`, `foo10` 和 `bar`, `rm foo?`这条命令会删除`foo1` 和 `foo2` ，而`rm foo*` 则会删除除了`bar`之外的所有文件。
- **花括号**`{}` - 当你有一系列的指令，其中包含一段公共子串时，可以用花括号来自动展开这些命令。这在批量移动或转换文件时非常方便。

``` bash

convert image.{png,jpg}  
# 会展开为  
# convert image.png image.jpg  
  
cp /path/to/project/{foo,bar,baz}.sh /newpath  
# 会展开为  
cp /path/to/project/foo.sh /path/to/project/bar.sh /path/to/project/baz.sh /newpath  
  
# 也可以结合通配使用  
mv *{.py,.sh} folder  
# 会移动所有 *.py 和 *.sh 文件  
  
mkdir foo bar  
# 下面命令会创建foo/a, foo/b, ... foo/h, bar/a, bar/b, ... bar/h这些文件  
touch {foo,bar}/{a..h}  
  
touch foo/x bar/y  
# 比较文件夹 foo 和 bar 中包含文件的不同  
diff <(ls foo) <(ls bar)  
# 输出  
# < x  
# ---  
# > y
```
编写 `bash` 脚本有时候会很别扭和反直觉。**例如 [shellcheck](https://github.com/koalaman/shellcheck) 这样的工具可以帮助你定位sh/bash脚本中的错误。**

注意，脚本并不一定只有用 bash 写才能在终端里调用。比如说，这是一段 Python 脚本，作用是将输入的参数倒序输出：
``` bash
#!/usr/local/bin/python  
import sys  
for arg in reversed(sys.argv[1:]):  
    print(arg)
```
**内核知道去用 python 解释器而不是 shell 命令来运行这段脚本，是因为脚本的开头第一行的 [shebang](https://en.wikipedia.org/wiki/Shebang_(Unix))。**
**在 `shebang` 行中使用 [`env`](https://man7.org/linux/man-pages/man1/env.1.html) 命令是一种好的实践，它会利用环境变量中的程序来解析该脚本，这样就提高来您的脚本的可移植性。**`env` 会利用我们第一节讲座中介绍过的`PATH` 环境变量来进行定位。 例如，使用了`env`的shebang看上去时这样的`#!/usr/bin/env python`。

shell函数和脚本有如下一些不同点：
- 函数只能与shell使用相同的语言，**脚本可以使用任意语言**。因此在脚本中包含 `shebang` 是很重要的。
- **函数仅在定义时被加载，脚本会在每次被执行时加载。**这让函数的加载比脚本略快一些，但每次修改函数定义，都要重新加载一次。
- **函数会在当前的shell环境中执行，脚本会在单独的进程中执行。因此，函数可以对环境变量进行更改，比如改变当前工作目录，脚本则不行。**脚本需要使用 [`export`](https://man7.org/linux/man-pages/man1/export.1p.html) 将环境变量导出，并将值传递给环境变量。
- 与其他程序语言一样，函数可以提高代码模块性、代码复用性并创建清晰性的结构。shell脚本中往往也会包含它们自己的函数定义。

## Shell 工具

### 查看命令如何使用
看到这里，您可能会有疑问，我们应该如何为特定的命令找到合适的标记呢？例如 `ls -l`, `mv -i` 和 `mkdir -p`。更普遍的是，给您一个命令行，您应该怎样了解如何使用这个命令行并找出它的不同的选项呢？

在上一节中我们介绍过，最常用的方法是为对应的命令行添加`-h` 或 `--help` 标记。另外一个更详细的方法则是使用`man` 命令。**[`man`](https://man7.org/linux/man-pages/man1/man.1.html) 命令是手册（manual）的缩写，它提供了命令的用户手册。**即使是您安装的第三方命令，前提是开发者编写了手册并将其包含在了安装包中。在交互式的、基于字符处理的终端窗口中，一般也可以通过 `:help` 命令或键入 `?` 来获取帮助。

有时候手册内容太过详实，让我们难以在其中查找哪些最常用的标记和语法。 **[TLDR pages](https://tldr.sh/) 是一个很不错的替代品，它提供了一些案例，可以帮助您快速找到正确的选项。**

### 查找文件
程序员们面对的最常见的重复任务就是查找文件或目录。所有的类UNIX系统都包含一个名为 [`find`](https://man7.org/linux/man-pages/man1/find.1.html) 的工具，它是 shell 上用于查找文件的绝佳工具。**`find`命令会递归地搜索符合条件的文件**，例如：
``` bash
# 查找所有名称为src的文件夹  
find . -name src -type d  
# 查找所有文件夹路径中包含test的python文件  
find . -path '*/test/*.py' -type f  
# 查找前一天修改的所有文件  
find . -mtime -1  
# 查找所有大小在500k至10M的tar.gz文件  
find . -size +500k -size -10M -name '*.tar.gz'
```

除了列出所寻找的文件之外，**find 还能对所有查找到的文件进行操作**。这能极大地简化一些单调的任务。
``` bash
# 删除全部扩展名为.tmp 的文件  
find . -name '*.tmp' -exec rm {} \;  
# 查找全部的 PNG 文件并将其转换为 JPG  
find . -name '*.png' -exec convert {} {}.jpg \;
```

尽管 `find` 用途广泛，它的语法却比较难以记忆。例如，**为了查找满足模式 `PATTERN` 的文件，您需要执行 `find -name '*PATTERN*'` (如果您希望模式匹配时是不区分大小写，可以使用`-iname`选项）**

您当然可以使用 **alias 设置别名来简化上述操作**，但 shell 的哲学之一便是寻找（更好用的）替代方案。 记住，shell 最好的特性就是您只是在调用程序，因此您只要找到合适的替代程序即可（甚至自己编写）。
例如，[`fd`](https://github.com/sharkdp/fd) 就是一个更简单、更快速、更友好的程序，它可以用来作为`find`的替代品。它有很多不错的默认设置，例如输出着色、默认支持正则匹配、支持unicode并且我认为它的语法更符合直觉。以模式`PATTERN` 搜索的语法是 `fd PATTERN`。

大多数人都认为 `find` 和 `fd` 已经很好用了，但是有的人可能想知道，我们是不是可以有更高效的方法，例如不要每次都搜索文件而是通过编译索引或建立数据库的方式来实现更加快速地搜索。
这就要靠 [`locate`](https://man7.org/linux/man-pages/man1/locate.1.html) 了。 **`locate` 使用一个由 [`updatedb`](https://man7.org/linux/man-pages/man1/updatedb.1.html)负责更新的数据库，在大多数系统中 `updatedb` 都会通过 [`cron`](https://man7.org/linux/man-pages/man8/cron.8.html) 每日更新。这便需要我们在速度和时效性之间作出权衡。**而且，`find` 和类似的工具可以通过别的属性比如文件大小、修改时间或是权限来查找文件，`locate`则只能通过文件名。 [这里](https://unix.stackexchange.com/questions/60205/locate-vs-find-usage-pros-and-cons-of-each-other)有一个更详细的对比。

### 查找代码

查找文件是很有用的技能，但是很多时候您的目标其实是查看文件的内容。一个最常见的场景是您希望查找具有某种模式的全部文件，并找它们的位置。
**为了实现这一点，很多类UNIX的系统都提供了[`grep`](https://man7.org/linux/man-pages/man1/grep.1.html)命令**，它是用于对输入文本进行匹配的通用工具。它是一个非常重要的shell工具，我们会在后续的数据清理课程中深入的探讨它。

`grep` 有很多选项，这也使它成为一个非常全能的工具。其中我经常使用的有 `-C` ：获取查找结果的上下文（Context）；`-v` 将对结果进行反选（Invert），也就是输出不匹配的结果。举例来说， `grep -C 5` 会输出匹配结果前后五行。当需要搜索大量文件的时候，使用 `-R` 会递归地进入子目录并搜索所有的文本文件。
但是，我们有很多办法可以对 `grep -R` 进行改进，例如使其忽略`.git` 文件夹，使用多CPU等等。

因此也出现了很多它的替代品，包括 [ack](https://beyondgrep.com/), [ag](https://github.com/ggreer/the_silver_searcher) 和 [rg](https://github.com/BurntSushi/ripgrep)。它们都特别好用，但是功能也都差不多，我比较常用的是 ripgrep (`rg`) ，因为它速度快，而且用法非常符合直觉。例子如下：
``` bash
# 查找所有使用了 requests 库的文件  
rg -t py 'import requests'  
# 查找所有没有写 shebang 的文件（包含隐藏文件）  
rg -u --files-without-match "^#!"  
# 查找所有的foo字符串，并打印其之后的5行  
rg foo -A 5  
# 打印匹配的统计信息（匹配的行和文件的数量）  
rg --stats PATTERN
```

与 `find`/`fd` 一样，重要的是你要知道有些问题使用合适的工具就会迎刃而解，而具体选择哪个工具则不是那么重要。

### 查找 shell 命令

目前为止，我们已经学习了如何查找文件和代码，但随着你使用shell的时间越来越久，您可能想要找到之前输入过的某条命令。首先，按向上的方向键会显示你使用过的上一条命令，继续按上键则会遍历整个历史记录。

**`history` 命令允许您以程序员的方式来访问shell中输入的历史命令。这个命令会在标准输出中打印shell中的里面命令。**如果我们要搜索历史记录，则可以利用管道将输出结果传递给 `grep` 进行模式搜索。 `history | grep find` 会打印包含find子串的命令。

**对于大多数的shell来说，您可以使用 `Ctrl+R` 对命令历史记录进行回溯搜索。敲 `Ctrl+R` 后您可以输入子串来进行匹配，查找历史命令行。**
反复按下就会在所有搜索结果中循环。在 [zsh](https://github.com/zsh-users/zsh-history-substring-search) 中，使用方向键上或下也可以完成这项工作。
`Ctrl+R` 可以配合 [fzf](https://github.com/junegunn/fzf/wiki/Configuring-shell-key-bindings#ctrl-r) 使用。`fzf` **是一个通用对模糊查找工具，它可以和很多命令一起使用。**这里我们可以对历史命令进行模糊查找并将结果以赏心悦目的格式输出。

另外一个和历史命令相关的技巧我喜欢称之为**基于历史的自动补全**。 这一特性最初是由 [fish](https://fishshell.com/) shell 创建的，它可以根据您最近使用过的开头相同的命令，动态地对当前对shell命令进行补全。这一功能在 [zsh](https://github.com/zsh-users/zsh-autosuggestions) 中也可以使用，它可以极大的提高用户体验。

你可以修改 shell history 的行为，例如，如果在命令的开头加上一个空格，它就不会被加进shell记录中。当你输入包含密码或是其他敏感信息的命令时会用到这一特性。 为此你需要在`.bashrc`中添加`HISTCONTROL=ignorespace`或者向`.zshrc` 添加 `setopt HIST_IGNORE_SPACE`。 如果你不小心忘了在前面加空格，可以通过编辑。`bash_history`或 `.zhistory` 来手动地从历史记录中移除那一项。

### 文件夹导航

之前对所有操作我们都默认一个前提，即您已经位于想要执行命令的目录下，但是如何才能高效地在目录 间随意切换呢？有很多简便的方法可以做到，比如设置alias，使用 [ln -s](https://man7.org/linux/man-pages/man1/ln.1.html) 创建符号连接等。而开发者们已经想到了很多更为精妙的解决方案。

由于本课程的目的是尽可能对你的日常习惯进行优化。因此，**我们可以使用[`fasd`](https://github.com/clvv/fasd)和 [autojump](https://github.com/wting/autojump) 这两个工具来查找最常用或最近使用的文件和目录。**
Fasd 基于 [frecency](https://developer.mozilla.org/en-US/docs/Mozilla/Tech/Places/Frecency_algorithm) 对文件和文件排序，也就是说它会同时针对频率（frequency）和时效（recency）进行排序。默认情况下，`fasd`使用命令 `z` 帮助我们快速切换到最常访问的目录。例如， 如果您经常访问`/home/user/files/cool_project` 目录，那么可以直接使用 `z cool` 跳转到该目录。对于 autojump，则使用`j cool`代替即可。

**还有一些更复杂的工具可以用来概览目录结构，例如 [`tree`](https://linux.die.net/man/1/tree), [`broot`](https://github.com/Canop/broot) 或更加完整的文件管理器，例如 [`nnn`](https://github.com/jarun/nnn) 或 [`ranger`](https://github.com/ranger/ranger)。**

# 编辑器 (Vim)

## Vim 的哲学
所以，Vim 是一个多模态编辑器：它对于插入文字和操纵文字有不同的模式。Vim 是可编程的（可以使用 Vimscript 或者像 Python 一样的其他程序语言），Vim 的接口本身也是一个程序语言：键入操作（以及其助记名） 是命令，这些命令也是可组合的。Vim 避免了使用鼠标，因为那样太慢了。
这样的设计哲学使得 Vim 成为了一个能跟上你思维速度的编辑器。

## 编辑模式

Vim 的设计以大多数时间都花在阅读、浏览和进行少量编辑改动为基础，因此它具有多种操作模式：
- **正常模式**：在文件中四处移动光标进行修改
- **插入模式**：插入文本
- **替换模式**：替换文本
- **可视化模式**（一般，行，块）：选中文本块  
- **命令模式**：用于执行命令

在不同的操作模式下，键盘敲击的含义也不同。比如，`x` 在插入模式会插入字母 `x`，但是在正常模式 会删除当前光标所在的字母，在可视模式下则会删除选中文块。
在默认设置下，Vim 会在左下角显示当前的模式。Vim 启动时的默认模式是正常模式。通常你会把大部分 时间花在正常模式和插入模式。

你可以按下 `<ESC>`（退出键）从任何其他模式返回正常模式。在正常模式，键入 `i` 进入插入 模式，`R` 进入替换模式，`v` 进入可视（一般）模式，`V` 进入可视（行）模式，`<C-v>` （Ctrl-V, 有时也写作 `^V`）进入可视（块）模式，`:` 进入命令模式。

## Vim基本操作

### 插入文本
在正常模式，键入 `i` 进入插入模式。现在 Vim 跟很多其他的编辑器一样，直到你键入 `<ESC>` 返回正常模式。你只需要掌握这一点和上面介绍的所有基础知识就可以使用 Vim 来编辑文件了 （虽然如果你一直停留在插入模式内不一定高效）。

### 缓存， 标签页， 窗口
**Vim 会维护一系列打开的文件，称为“缓存”。** 一个 Vim 会话包含一系列标签页，每个标签页包含 一系列窗口（分隔面板）。每个窗口显示一个缓存。跟网页浏览器等其他你熟悉的程序不一样的是， 缓存和窗口不是一一对应的关系；窗口只是视角。一个缓存可以在_多个_窗口打开，甚至在同一个标签页内的多个窗口打开。这个功能其实很好用，比如在查看同一个文件的不同部分的时候。
Vim 默认打开一个标签页，这个标签也包含一个窗口。

### 命令行
在正常模式下键入 `:` 进入命令行模式。 在键入 `:` 后，你的光标会立即跳到屏幕下方的命令行。 这个模式有很多功能，包括打开，保存，关闭文件，以及 [退出 Vim](https://twitter.com/iamdevloper/status/435555976687923200)。
- `:q` 退出（关闭窗口）
- `:w` 保存（写） 
- `:wq` 保存然后退出 
- `:e {文件名}` 打开要编辑的文件 
- `:ls` 显示打开的缓存 
- `:help {标题}`打开帮助文档
    - `:help :w` 打开 `:w` 命令的帮助文档
    - `:help w` 打开 `w` 移动的帮助文档

## Vim 的接口其实是一种编程语言

Vim 最重要的设计思想是 Vim 的界面本身是一个程序语言。键入操作（以及他们的助记名） 本身是命令，这些命令可以组合使用。这使得移动和编辑更加高效，特别是一旦形成肌肉记忆。
### 移动
多数时候你会在正常模式下，使用移动命令在缓存中导航。在 Vim 里面移动也被称为 “名词”， 因为它们指向文字块。
- 基本移动: `hjkl` （左， 下， 上， 右）
- 词： `w` （下一个词）， `b` （词初）， `e` （词尾）
- 行： `0` （行初）， `^` （第一个非空格字符）， `$` （行尾）
- 屏幕： `H` （屏幕首行）， `M` （屏幕中间）， `L` （屏幕底部） 
- 翻页： `Ctrl-u` （上翻）， `Ctrl-d` （下翻）  
- 文件： `gg` （文件头）， `G` （文件尾）
- 行数： `:{行数}<CR>` 或者 `{行数}G` ({行数}为行数)
- 杂项： `%` （找到配对，比如括号或者 /* */ 之类的注释对）
- 查找：`f{字符}`， `t{字符}`， `F{字符}`， `T{字符}`
    - 查找/到 向前/向后 在本行的{字符} 
    - `,` / `;` 用于导航匹配
- 搜索: `/{正则表达式}`, `n` / `N` 用于导航匹配

### 选择
可视化模式:
- 可视化：`v`
- 可视化行： `V`
- 可视化块：`Ctrl+v`
可以用移动命令来选中。

### 编辑
**所有你需要用鼠标做的事， 你现在都可以用键盘：采用编辑命令和移动命令的组合来完成。** 这就是 Vim 的界面开始看起来像一个程序语言的时候。Vim 的编辑命令也被称为 “动词”， 因为动词可以施动于名词。
- `i`进入插入模式
    - 但是对于操纵/编辑文本，不单想用退格键完成
- `O` / `o` 在之上/之下插入行
- `d{移动命令}`删除 {移动命令}
    - 例如，`dw` 删除词, `d$` 删除到行尾, `d0` 删除到行头。
- `c{移动命令}`改变 {移动命令}
    - 例如，`cw` 改变词 
    - 比如 `d{移动命令}` 再 `i` 
- `x` 删除字符（等同于 `dl`）
- `s` 替换字符（等同于 `xi`）
- 可视化模式 + 操作
    - 选中文字, `d` 删除 或者 `c` 改变
- `u` 撤销, `<C-r>` 重做
- `y` 复制 / “yank” （其他一些命令比如 `d` 也会复制）
- `p` 粘贴
- 更多值得学习的: 比如 `~` 改变字符的大小写

### 计数
你可以用一个计数来结合“名词”和“动词”，这会执行指定操作若干次。
- `3w` 向前移动三个词
- `5j` 向下移动5行 
- `7dw` 删除7个词

### 修饰语
你可以用修饰语改变“名词”的意义。修饰语有 `i`，表示“内部”或者“在内”，和 `a`， 表示“周围”。
- `ci(` 改变当前括号内的内容
- `ci[` 改变当前方括号内的内容  
- `da'` 删除一个单引号字符串， 包括周围的单引号 

## 自定义 Vim
Vim 由一个位于 `~/.vimrc` 的文本配置文件（包含 Vim 脚本命令）。你可能会启用很多基本设置。
我们提供一个文档详细的基本设置，你可以用它当作你的初始设置。我们推荐使用这个设置因为 它修复了一些 Vim 默认设置奇怪行为。 在[这儿](https://missing-semester-cn.github.io/2020/files/vimrc)下载我们的设置，然后将它保存成 `~/.vimrc`.
Vim 能够被重度自定义，花时间探索自定义选项是值得的。你可以参考其他人的在 GitHub 上共享的设置文件，比如，你的授课人的 Vim 设置 ([Anish](https://github.com/anishathalye/dotfiles/blob/master/vimrc), [Jon](https://github.com/jonhoo/configs/blob/master/editor/.config/nvim/init.vim) (uses [neovim](https://neovim.io/)), [Jose](https://github.com/JJGO/dotfiles/blob/master/vim/.vimrc))。 有很多好的博客文章也聊到了这个话题。尽量不要复制粘贴别人的整个设置文件， 而是阅读和理解它，然后采用对你有用的部分。

## 扩展 Vim
Vim 有很多扩展插件。跟很多互联网上已经过时的建议相反，你不需要在 Vim 使用一个插件 管理器（从 Vim 8.0 开始）。你可以使用内置的插件管理系统。只需要创建一个 `~/.vim/pack/vendor/start/` 的文件夹，然后把插件放到这里（比如通过 `git clone`）。

以下是一些我们最爱的插件：
- [ctrlp.vim](https://github.com/ctrlpvim/ctrlp.vim): 模糊文件查找
- [ack.vim](https://github.com/mileszs/ack.vim): 代码搜索
- [nerdtree](https://github.com/scrooloose/nerdtree): 文件浏览器
- [vim-easymotion](https://github.com/easymotion/vim-easymotion): 魔术操作 

我们尽量避免在这里提供一份冗长的插件列表。你可以查看讲师们的开源的配置文件 ([Anish](https://github.com/anishathalye/dotfiles), [Jon](https://github.com/jonhoo/configs), [Jose](https://github.com/JJGO/dotfiles)) 来看看我们使用的其他插件。 浏览 [Vim Awesome](https://vimawesome.com/) 来了解一些很棒的插件。 这个话题也有很多博客文章：搜索 “best Vim plugins”。

## 其他程序的 Vim 模式
很多工具提供了 Vim 模式。这些 Vim 模式的质量参差不齐；取决于具体工具，有的提供了很多酷炫的 Vim 功能，但是大多数对基本功能支持的很好。
### Shell
如果你是一个 Bash 用户，用 `set -o vi`。如果你用 Zsh：`bindkey -v`。Fish 用 `fish_vi_key_bindings`。另外，不管利用什么 shell，你可以 `export EDITOR=vim`。 这是一个用来决定当一个程序需要启动编辑时启动哪个的环境变量。 例如，`git` 会使用这个编辑器来编辑 commit 信息。
### Readline
很多程序使用 [GNU Readline](https://tiswww.case.edu/php/chet/readline/rltop.html) 库来作为 它们的命令控制行界面。Readline 也支持基本的 Vim 模式， 可以通过在 `~/.inputrc` 添加如下行开启：
``` bash
set editing-mode vi
```
比如，在这个设置下，Python REPL 会支持 Vim 快捷键。
### 其他
甚至有 Vim 的网页浏览快捷键 [browsers](http://vim.wikia.com/wiki/Vim_key_bindings_for_web_browsers), 受欢迎的有 用于 Google Chrome 的 [Vimium](https://chrome.google.com/webstore/detail/vimium/dbepggeogbaibhgnhhndojpepiihcmeb?hl=en) 和用于 Firefox 的 [Tridactyl](https://github.com/tridactyl/tridactyl)。 你甚至可以在 [Jupyter notebooks](https://github.com/lambdalisue/jupyter-vim-binding) 中用 Vim 快捷键。 [这个列表](https://reversed.top/2016-08-13/big-list-of-vim-like-software) 中列举了支持类 vim 键位绑定的软件。

## Vim 进阶
这里我们提供了一些展示这个编辑器能力的例子。我们无法把所有的这样的事情都教给你，但是你可以在使用中学习。一个好的对策是: 当你在使用你的编辑器的时候感觉 “一定有更好的方法来做这个”， 那么很可能真的有：上网搜寻一下。

### 搜索和替换
`:s` （替换）命令（[文档](http://vim.wikia.com/wiki/Search_and_replace)）。
- `%s/foo/bar/g`
    - 在整个文件中将 foo 全局替换成 bar
- `%s/\[.*\](\(.*\))/\1/g`
    - 将有命名的 Markdown 链接替换成简单 URLs

### 多窗口
- 用 `:sp` / `:vsp` 来分割窗口
- 同一个缓存可以在多个窗口中显示。

### 宏
- `q{字符}` 来开始在寄存器`{字符}`中录制宏
- `q`停止录制
- `@{字符}` 重放宏
- 宏的执行遇错误会停止
- `{计数}@{字符}`执行一个宏{计数}次
- 宏可以递归
    - 首先用`q{字符}q`清除宏 
    - 录制该宏，用 `@{字符}` 来递归调用该宏 （在录制完成之前不会有任何操作）
- 例子：将 xml 转成 json (file)
    - 一个有 “name” / “email” 键对象的数组 
    - 用一个 Python 程序？
    - 用 sed / 正则表达式
        - `g/people/d`
        - `%s/<person>/{/g`
        - `%s/<name>\(.*\)<\/name>/"name": "\1",/g` 
    - Vim 命令 / 宏
        - `Gdd`, `ggdd` 删除第一行和最后一行
        - 格式化最后一个元素的宏 （寄存器`e`） 
            - 跳转到有 `<name>` 的行 
            - `qe^r"f>s": "<ESC>f<C"<ESC>q`
        - 格式化一个的宏
            - 跳转到有 `<person>` 的行
            - `qpS{<ESC>j@eA,<ESC>j@ejS},<ESC>q`
        - 格式化一个标签然后转到另外一个的宏
            - 跳转到有 `<person>` 的行
            - `qq@pjq`
        - 执行宏到文件尾
            - `999@q`
        - 手动移除最后的 `,` 然后加上 `[` 和 `]` 分隔符

# 数据整理

可以这么说，每当使用管道运算符的时候，其实就是在进行某种形式的**数据整理**。

例如这样一条命令 `journalctl | grep -i intel`，它会找到所有包含intel(不区分大小写)的系统日志。您可能并不认为这是数据整理，但是它确实将某种形式的数据（全部系统日志）转换成了另外一种形式的数据（仅包含intel的日志）。大多数情况下，数据整理需要您能够明确哪些工具可以被用来达成特定数据整理的目的，并且明白如何组合使用这些工具。

让我们从头讲起。既然是学习数据整理，那有两样东西自然是必不可少的：**用来整理的数据以及相关的应用场景。** 日志处理通常是一个比较典型的使用场景，因为我们经常需要在日志中查找某些信息，这种情况下通读日志是不现实的。现在，让我们研究一下系统日志，看看哪些用户曾经尝试过登录我们的服务器：
``` bash
ssh myserver journalctl
```

内容太多了。现在让我们把涉及 sshd 的信息过滤出来：
``` bash
ssh myserver journalctl | grep sshd
```

注意，这里我们**使用管道将一个远程服务器上的文件传递给本机的 `grep` 程序**！ `ssh` 太牛了，下一节课我们会讲授命令行环境，届时我们会详细讨论 `ssh` 的相关内容。此时我们打印出的内容，仍然比我们需要的要多得多，读起来也非常费劲。我们来改进一下：
``` bash
ssh myserver 'journalctl | grep sshd | grep "Disconnected from"' | less
```

**多出来的引号**是什么作用呢？这么说吧，我们的日志是一个非常大的文件，把这么大的文件流直接传输到我们本地的电脑上再进行过滤是对流量的一种浪费。**因此我们采取另外一种方式，我们先在远端机器上过滤文本内容，然后再将结果传输到本机。** `less` 为我们创建来一个文件分页器，使我们可以通过翻页的方式浏览较长的文本。为了进一步节省流量，我们甚至可以将当前过滤出的日志保存到文件中，这样后续就不需要再次通过网络访问该文件了：
``` bash
$ ssh myserver 'journalctl | grep sshd | grep "Disconnected from"' > ssh.log  
$ less ssh.log
```

过滤结果中仍然包含不少没用的数据。我们有很多办法可以删除这些无用的数据，但是让我们先研究一下 `sed` 这个非常强大的工具。
**`sed` 是一个基于文本编辑器`ed`构建的”流编辑器” 。在 `sed` 中，您基本上是利用一些简短的命令来修改文件，而不是直接操作文件的内容（尽管您也可以选择这样做）。** 相关的命令行非常多，但是最常用的是 `s`，即**替换命令**，例如我们可以这样写：
``` bash
ssh myserver journalctl  
 | grep sshd  
 | grep "Disconnected from"  
 | sed 's/.*Disconnected from //'
```

上面这段命令中，我们使用了一段简单的**正则表达式**。正则表达式是一种非常强大的工具，可以让我们基于某种模式来对字符串进行匹配。`s` 命令的语法如下：`s/REGEX/SUBSTITUTION/`, 其中 `REGEX` 部分是我们需要使用的正则表达式，而 `SUBSTITUTION` 是用于**替换匹配结果的文本**。

## 正则表达式
正则表达式非常常见也非常有用，值得您花些时间去理解它。让我们从这一句正则表达式开始学习： `/.*Disconnected from /`。正则表达式通常以（尽管并不总是） `/`开始和结束。大多数的 ASCII 字符都表示它们本来的含义，**但是有一些字符确实具有表示匹配行为的“特殊”含义。** 不同字符所表示的含义，根据正则表达式的实现方式不同，也会有所变化，这一点确实令人沮丧。常见的模式有：
- `.` **除换行符之外的”任意单个字符”**
- `*` **匹配前面字符零次或多次**
- `+` 匹配前面字符一次或多次
- `[abc]` 匹配 `a`, `b` 和 `c` 中的任意一个
- `(RX1|RX2)` 任何能够匹配`RX1` 或 `RX2`的结果
- `^` 行首
- `$` 行尾

`sed` 的正则表达式有些时候是比较奇怪的，它需要你在这些模式前添加`\`才能使其具有特殊含义。或者，您也可以添加`-E`选项来支持这些匹配。
``` bash
| echo 'bba' | sed 's/[ab]//'  
# ba  
# 替换a或b一次  
| echo 'bba' | sed 's/[ab]//g'  
#   
# 反复替换a或b  
  
| echo 'bcbzac' | sed 's/[ab]//g'  
# czc  
  
| echo 'abcaba' | sed  -E 's/(ab)*//g'  
#ca  
# 严格匹配ab字符串，要加-E  
| echo 'abcaba' | sed  -E 's/(ab|bc)*//g'  
#cc  
# 严格匹配ab或bc字符串
```

回过头我们再看`/.*Disconnected from /`，我们会发现这个正则表达式可以匹配任何以若干任意字符开头，并接着包含”Disconnected from “的字符串。这也正式我们所希望的。但是请注意，正则表达式并不容易写对。如果有人将 “Disconnected from” 作为自己的用户名会怎样呢？
``` bash
Jan 17 03:13:00 thesquareplanet.com sshd[2631]: Disconnected from invalid user Disconnected from 46.97.239.16 port 55920 [preauth]
```

正则表达式会如何匹配？**`*` 和 `+` 在默认情况下是贪婪模式，也就是说，它们会尽可能多的匹配文本。** 因此对上述字符串的匹配结果如下：
``` bash
46.97.239.16 port 55920 [preauth]
```

这可不是我们想要的结果。对于某些正则表达式的实现来说，**您可以给 `*` 或 `+` 增加一个`?` 后缀使其变成非贪婪模式，但是很可惜 `sed` 并不支持该后缀。** 不过，我们可以切换到 perl 的命令行模式，该模式支持编写这样的正则表达式：
``` bash
perl -pe 's/.*?Disconnected from //'
```

让我们回到 `sed` 命令并使用它完成后续的任务，毕竟对于这一类任务，`sed`是最常见的工具。`sed` 还可以非常方便的做一些事情，例如打印匹配后的内容，一次调用中进行多次替换搜索等。但是这些内容我们并不会在此进行介绍。`sed` 本身是一个非常全能的工具，但是在具体功能上往往能找到更好的工具作为替代品。

好的，我们还需要去掉用户名后面的后缀，应该如何操作呢？想要匹配用户名后面的文本，尤其是当这里的用户名可以包含空格时，这个问题变得非常棘手！这里我们需要做的是匹配一整行：
``` bash
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user .* [^ ]+ port [0-9]+( \[preauth\])?$//'
```

让我们借助**正则表达式在线调试工具[regex debugger](https://regex101.com/r/qqbZqh/2)**  来理解这段表达式。OK，开始的部分和以前是一样的，随后，我们匹配两种类型的“user”（在日志中基于两种前缀区分）。再然后我们匹配属于用户名的所有字符。接着，再匹配任意一个单词（`[^ ]+` 会匹配任意非空且不包含空格的序列）。紧接着后面匹配单“port”和它后面的一串数字，以及可能存在的后缀`[preauth]`，最后再匹配行尾。

注意，这样做的话，即使用户名是“Disconnected from”，对匹配结果也不会有任何影响，您知道这是为什么吗？
问题还没有完全解决，日志的内容全部被替换成了空字符串，整个日志的内容因此都被删除了。我们实际上希望能够将用户名保留下来。对此，我们可以使用“捕获组（capture groups）”来完成。被圆括号内的正则表达式匹配到的文本，都会被存入一系列以编号区分的捕获组中。捕获组的内容可以在替换字符串时使用（有些正则表达式的引擎甚至支持替换表达式本身），例如`\1`、 `\2`、`\3`等等，因此可以使用如下命令：
``` bash
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
```

想必您已经意识到了，为了完成某种匹配，我们最终可能会写出非常复杂的正则表达式。正则表达式是出了名的难以写对，但是它仍然会是您强大的常备工具之一。

## 回到数据整理

OK，现在我们有如下表达式：
``` bash
ssh myserver journalctl  
 | grep sshd  
 | grep "Disconnected from"  
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
```

`sed` 还可以做很多各种各样有趣的事情，例如文本注入：(使用 `i` 命令)，打印特定的行 (使用 `p`命令)，基于索引选择特定行等等。详情请见`man sed`!
现在，我们已经得到了一个包含用户名的列表，列表中的用户都曾经尝试过登录我们的系统。但这还不够，让我们过滤出那些最常出现的用户：
``` bash
ssh myserver journalctl  
 | grep sshd  
 | grep "Disconnected from"  
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'  
 | sort | uniq -c
```

**`sort` 会对其输入数据进行排序。`uniq -c` 会把连续出现的行折叠为一行并使用出现次数作为前缀。**我们希望按照出现次数排序，过滤出最常出现的用户名：
``` bash
ssh myserver journalctl  
 | grep sshd  
 | grep "Disconnected from"  
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'  
 | sort | uniq -c  
 | sort -nk1,1 | tail -n10
```

`sort -n` 会按照**数字顺序对输入进行排序**（默认情况下是按照字典序排序 `-k1,1` 则表示“仅基于以空格分割的第一列进行排序”。`,n` 部分表示“仅排序到第n个部分”，默认情况是到行尾。就本例来说，针对整个行进行排序也没有任何问题，我们这里主要是为了学习这一用法！
如果我们希望得到登录次数最少的用户，我们可以使用 `head` 来代替`tail`。或者使用`sort -r`来进行倒序排序。
相当不错。但我们只想获取用户名，而且不要一行一个地显示。

``` bash
ssh myserver journalctl  
 | grep sshd  
 | grep "Disconnected from"  
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'  
 | sort | uniq -c  
 | sort -nk1,1 | tail -n10  
 | awk '{print $2}' | paste -sd,
```

我们可以利用 `paste`命令来合并行(`-s`)，并指定一个分隔符进行分割 (`-d`)，那`awk`的作用又是什么呢？

## awk – 另外一种编辑器

**`awk` 其实是一种编程语言，只不过它碰巧非常善于处理文本。** 关于 `awk` 可以介绍的内容太多了，限于篇幅，这里我们仅介绍一些基础知识。

首先， `{print $2}` 的作用是什么？ **`awk` 程序接受一个模式串（可选），以及一个代码块，指定当模式匹配时应该做何种操作。** 默认当模式串即匹配所有行（上面命令中当用法）。 在代码块中，`$0` 表示整行的内容，`$1` 到 `$n` 为一行中的 n 个区域，区域的分割基于 `awk` 的域分隔符（默认是空格，可以通过`-F`来修改）。在这个例子中，我们的代码意思是：对于每一行文本，打印其第二个部分，也就是用户名。

让我们看看，还有什么炫酷的操作可以做。让我们统计一下所有以`c` 开头，以 `e` 结尾，并且仅尝试过一次登录的用户。
``` bash
 | awk '$1 == 1 && $2 ~ /^c[^ ]*e$/ { print $2 }' | wc -l
```

让我们好好分析一下。首先，注意这次我们为 `awk`指定了一个匹配模式串（也就是`{...}`前面的那部分内容）。该匹配要求文本的第一部分需要等于1（这部分刚好是`uniq -c`得到的计数值），然后其第二部分必须满足给定的一个正则表达式。代码块中的内容则表示打印用户名。然后我们使用 `wc -l` 统计输出结果的行数。
不过，既然 `awk` 是一种编程语言，那么则可以这样：
``` bash
BEGIN { rows = 0 }  
$1 == 1 && $2 ~ /^c[^ ]*e$/ { rows += $1 }  
END { print rows }
```

`BEGIN` 也是一种模式，它会匹配输入的开头（ `END` 则匹配结尾）。然后，对每一行第一个部分进行累加，最后将结果输出。**事实上，我们完全可以抛弃 `grep` 和 `sed` ，因为 `awk` 就可以[解决所有问题](https://backreference.org/2010/02/10/idiomatic-awk)。** 至于怎么做，就留给读者们做课后练习吧。

## 分析数据

`bc`指令，想做数学计算也是可以的！例如这样，您可以将每行的数字加起来：
``` bash
 | paste -sd+ | bc -l
```

下面这种更加复杂的表达式也可以：
``` bash
echo "2*($(data | paste -sd+))" | bc -l
```

您可以通过多种方式获取统计数据。如果已经安装了R语言，[`st`](https://github.com/nferraz/st)是个不错的选择：
``` bash
ssh myserver journalctl  
 | grep sshd  
 | grep "Disconnected from"  
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'  
 | sort | uniq -c  
 | awk '{print $1}' | R --slave -e 'x <- scan(file="stdin", quiet=TRUE); summary(x)'
```
**R 也是一种编程语言，它非常适合被用来进行数据分析和[绘制图表](https://ggplot2.tidyverse.org/)。** 这里我们不会讲的特别详细， 您只需要知道`summary` 可以打印某个向量的统计结果。我们将输入的一系列数据存放在一个向量后，利用R语言就可以得到我们想要的统计数据。

如果您希望绘制一些简单的图表， `gnuplot` 可以帮助到您：
``` bash
ssh myserver journalctl  
 | grep sshd  
 | grep "Disconnected from"  
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'  
 | sort | uniq -c  
 | sort -nk1,1 | tail -n10  
 | gnuplot -p -e 'set boxwidth 0.5; plot "-" using 1:xtic(2) with boxes'

```
## 利用数据整理来确定参数

有时候您要利用数据整理技术**从一长串列表里找出你所需要安装或移除的东西**。我们之前讨论的相关技术配合 `xargs` 即可实现：
``` bash
rustup toolchain list | grep nightly | grep -vE "nightly-x86" | sed 's/-x86.*//' | xargs rustup toolchain uninstall
```

## 整理二进制数据

虽然到目前为止我们的讨论都是基于文本数据，但对于**二进制文件**其实同样有用。例如我们可以用 ffmpeg 从相机中捕获一张图片，将其转换成灰度图后通过SSH将压缩后的文件发送到远端服务器，并在那里解压、存档并显示。
``` bash
ffmpeg -loglevel panic -i /dev/video0 -frames 1 -f image2 -  
 | convert - -colorspace gray -  
 | gzip  
 | ssh mymachine 'gzip -d | tee copy.jpg | env DISPLAY=:0 feh -'
```
# 命令行环境

### 结束进程
您的 shell 会使用 UNIX 提供的信号机制执行进程间通信。当一个进程接收到信号时，它会停止执行、处理该信号并基于信号传递的信息来改变其执行。就这一点而言，信号是一种`软件中断`。
在上面的例子中，当我们输入 `Ctrl-C` 时，shell 会发送一个`SIGINT` 信号到进程。

查看信号内容: `man signal`

下面这个 Python 程序向您展示了捕获信号`SIGINT` 并忽略它的基本操作，它并不会让程序停止。为了停止这个程序，我们需要使用`SIGQUIT` 信号，通过输入`Ctrl-\`可以发送该信号。
``` python
#!/usr/bin/env python
import signal, time

def handler(signum, time):
    print("\nI got a SIGINT, but I am not stopping")

signal.signal(signal.SIGINT, handler)
i = 0
while True:
    time.sleep(.1)
    print("\r{}".format(i), end="")
    i += 1
```

如果我们向这个程序发送两次 `SIGINT` ，然后再发送一次 `SIGQUIT`，程序会有什么反应？注意 `^` 是我们在终端输入`Ctrl` 时的表示形式：
``` bash
$ python sigint.py
24^C
I got a SIGINT, but I am not stopping
26^C
I got a SIGINT, but I am not stopping
30^\[1]    39913 quit       python sigint.pyƒ
```
尽管 `SIGINT` 和 `SIGQUIT` 都常常用来发出和终止程序相关的请求。**`SIGTERM` 则是一个更加通用的、也更加优雅地退出信号。为了发出这个信号我们需要使用 [`kill`](https://www.man7.org/linux/man-pages/man1/kill.1.html) 命令, 它的语法是： `kill -TERM <PID>`。** `kill -STOP %1`

### 暂停和后台执行进程

信号可以让进程做其他的事情，而不仅仅是终止它们。例如，`SIGSTOP` 会让进程暂停。在终端中，键入 `Ctrl-Z` 会让 shell 发送 `SIGTSTP` 信号，`SIGTSTP`是 Terminal Stop 的缩写（即`terminal`版本的SIGSTOP）。
**我们可以使用 [`fg`](https://www.man7.org/linux/man-pages/man1/fg.1p.html) 或 [`bg`](http://man7.org/linux/man-pages/man1/bg.1p.html) 命令恢复暂停的工作。它们分别表示在前台继续或在后台继续。**

**[`jobs`](http://man7.org/linux/man-pages/man1/jobs.1p.html) 命令会列出当前终端会话中尚未完成的全部任务。您可以使用 pid 引用这些任务**（也可以用 [`pgrep`](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) 找出 pid）。更加符合直觉的操作是您可以使用**百分号 + 任务编号**（`jobs` 会打印任务编号）来选取该任务。如果要选择最近的一个任务，可以使用 `$!` 这一特殊参数。

**还有一件事情需要掌握，那就是命令中的 `&` 后缀可以让命令在直接在后台运行**，这使得您可以直接在 shell 中继续做其他操作，不过它此时还是会使用 shell 的标准输出，这一点有时会比较恼人（这种情况可以使用 shell 重定向处理）。

**让已经在运行的进程转到后台运行，您可以键入`Ctrl-Z` ，然后紧接着再输入`bg`。** 注意，后台的进程仍然是您的终端进程的子进程，一旦您关闭终端（会发送另外一个信号`SIGHUP`），这些后台的进程也会终止。**为了防止这种情况发生，您可以使用 [`nohup`](https://www.man7.org/linux/man-pages/man1/nohup.1.html) (一个用来忽略 `SIGHUP` 的封装) 来运行程序。** **针对已经运行的程序，可以使用`disown` 。** 除此之外，您可以使用终端多路复用器来实现，下一章节我们会进行详细地探讨。

下面这个简单的会话中展示来了些概念的应用。
``` bash
$ sleep 1000
^Z
[1]  + 18653 suspended  sleep 1000

$ nohup sleep 2000 &
[2] 18745
appending output to nohup.out

$ jobs
[1]  + suspended  sleep 1000
[2]  - running    nohup sleep 2000

$ bg %1
[1]  - 18653 continued  sleep 1000

$ jobs
[1]  - running    sleep 1000
[2]  + running    nohup sleep 2000

$ kill -STOP %1
[1]  + 18653 suspended (signal)  sleep 1000

$ jobs
[1]  + suspended (signal)  sleep 1000
[2]  - running    nohup sleep 2000

$ kill -SIGHUP %1
[1]  + 18653 hangup     sleep 1000

$ jobs
[2]  + running    nohup sleep 2000

$ kill -SIGHUP %2

$ jobs
[2]  + running    nohup sleep 2000

$ kill %2
[2]  + 18745 terminated  nohup sleep 2000

$ jobs

```

**`SIGKILL` 是一个特殊的信号，它不能被进程捕获并且它会马上结束该进程。不过这样做会有一些副作用，例如留下孤儿进程。**
您可以在 [这里](https://en.wikipedia.org/wiki/Signal_(IPC)) 或输入 [`man signal`](https://www.man7.org/linux/man-pages/man7/signal.7.html) 或使用 `kill -l` 来获取更多关于信号的信息。

## 终端多路复用

当您在使用命令行时，您通常会希望同时执行多个任务。举例来说，您可以想要同时运行您的编辑器，并在终端的另外一侧执行程序。尽管再打开一个新的终端窗口也能达到目的，使用终端多路复用器则是一种更好的办法。
像 [`tmux`](https://www.man7.org/linux/man-pages/man1/tmux.1.html) 这类的**终端多路复用器** 可以允许我们基于面板和标签分割出多个终端窗口，这样您便可以同时与多个 shell 会话进行交互。

不仅如此，终端多路复用使我们可以分离当前终端会话并在将来重新连接。这让您操作远端设备时的工作流大大改善，避免了 `nohup` 和其他类似技巧的使用。
**现在最流行的终端多路器是 [`tmux`](https://www.man7.org/linux/man-pages/man1/tmux.1.html)。`tmux` 是一个高度可定制的工具，您可以使用相关快捷键创建多个标签页并在它们间导航。**

`tmux` 的快捷键需要我们掌握，它们都是类似 `<C-b> x` 这样的组合，即需要先按下`Ctrl+b`，松开后再按下 `x`。`tmux` 中对象的继承结构如下：

- **会话** - 每个会话都是一个独立的工作区，其中包含一个或多个窗口
    - `tmux` 开始一个新的会话
    - `tmux new -s NAME` 以指定名称开始一个新的会话
    - `tmux ls` 列出当前所有会话
    - 在 `tmux` 中输入 `<C-b> d` ，将当前会话分离
    - `tmux a` 重新连接最后一个会话。您也可以通过 `-t` 来指定具体的会话
- **窗口** - 相当于编辑器或是浏览器中的标签页，从视觉上将一个会话分割为多个部分
    - `<C-b> c` 创建一个新的窗口，使用 `<C-d>`关闭
    - `<C-b> N` 跳转到第 _N_ 个窗口，注意每个窗口都是有编号的
    - `<C-b> p` 切换到前一个窗口
    - `<C-b> n` 切换到下一个窗口
    - `<C-b> ,` 重命名当前窗口
    - `<C-b> w` 列出当前所有窗口
- **面板** - 像 vim 中的分屏一样，面板使我们可以在一个屏幕里显示多个 shell
    - `<C-b> "` 水平分割
    - `<C-b> %` 垂直分割
    - `<C-b> <方向>` 切换到指定方向的面板，<方向> 指的是键盘上的方向键
    - `<C-b> z` 切换当前面板的缩放
    - `<C-b> [` 开始往回卷动屏幕。您可以按下空格键来开始选择，回车键复制选中的部分
    - `<C-b> <空格>` 在不同的面板排布间切换
扩展阅读： [这里](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) 是一份 `tmux` 快速入门教程， [而这一篇](http://linuxcommand.org/lc3_adv_termmux.php) 文章则更加详细，它包含了 `screen` 命令。您也许想要掌握 [`screen`](https://www.man7.org/linux/man-pages/man1/screen.1.html) 命令，因为在大多数 UNIX 系统中都默认安装有该程序。

## 别名

输入一长串包含许多选项的命令会非常麻烦。因此，大多数 shell 都支持设置别名。**shell 的别名相当于一个长命令的缩写，shell 会自动将其替换成原本的命令。** 例如，bash 中的别名语法如下：
``` bash
alias alias_name="command_to_alias arg1 arg2"
```

注意， `=`两边是没有空格的，因为 [`alias`](https://www.man7.org/linux/man-pages/man1/alias.1p.html) 是一个 shell 命令，它只接受一个参数。
别名有许多很方便的特性:
``` bash
# 创建常用命令的缩写
alias ll="ls -lh"

# 能够少输入很多
alias gs="git status"
alias gc="git commit"
alias v="vim"

# 手误打错命令也没关系
alias sl=ls

# 重新定义一些命令行的默认行为
alias mv="mv -i"           # -i prompts before overwrite
alias mkdir="mkdir -p"     # -p make parent dirs as needed
alias df="df -h"           # -h prints human readable format

# 别名可以组合使用
alias la="ls -A"
alias lla="la -l"

# 在忽略某个别名
\ls
# 或者禁用别名
unalias la

# 获取别名的定义
alias ll
# 会打印 ll='ls -lh'
```
值得注意的是，在默认情况下 shell 并不会保存别名。为了让别名持续生效，您需要将配置放进 shell 的启动文件里，像是`.bashrc` 或 `.zshrc`，下一节我们就会讲到。

## 配置文件（Dotfiles）

很多程序的配置都是通过纯文本格式的被称作`_点文件_`的配置文件来完成的（之所以称为点文件，是因为它们的文件名以 `.` 开头，例如 `~/.vimrc`。也正因为此，它们默认是隐藏文件，`ls`并不会显示它们）。

shell 的配置也是通过这类文件完成的。在启动时，您的 shell 程序会读取很多文件以加载其配置项。根据 shell 本身的不同，您从登录开始还是以交互的方式完成这一过程可能会有很大的不同。关于这一话题，[这里](https://blog.flowblok.id.au/2013-02/shell-startup-scripts.html) 有非常好的资源

**对于 `bash`来说，在大多数系统下，您可以通过编辑 `.bashrc` 或 `.bash_profile` 来进行配置。** 在文件中您可以添加需要在启动时执行的命令，例如上文我们讲到过的别名，或者是您的环境变量。
实际上，很多程序都要求您在 shell 的配置文件中包含一行类似 `export PATH="$PATH:/path/to/program/bin"` 的命令，这样才能确保这些程序能够被 shell 找到。

还有一些其他的工具也可以通过_点文件_进行配置：
- `bash` - `~/.bashrc`, `~/.bash_profile`
- `git` - `~/.gitconfig`
- `vim` - `~/.vimrc` 和 `~/.vim` 目录
- `ssh` - `~/.ssh/config`
- `tmux` - `~/.tmux.conf`

我们应该如何管理这些配置文件呢，它们应该在它们的文件夹下，并使用版本控制系统进行管理，然后通过脚本将其 **符号链接** `ln -s`到需要的地方。这么做有如下好处：(即home下的配位文件链接到其他文件夹的对应文件)
- **安装简单**: 如果您登录了一台新的设备，在这台设备上应用您的配置只需要几分钟的时间；
- **可移植性**: 您的工具在任何地方都以相同的配置工作
- **同步**: 在一处更新配置文件，可以同步到其他所有地方
- **变更追踪**: 您可能要在整个程序员生涯中持续维护这些配置文件，而对于长期项目而言，版本历史是非常重要的

配置文件中需要放些什么？您可以通过在线文档和[帮助手册](https://en.wikipedia.org/wiki/Man_page)了解所使用工具的设置项。另一个方法是在网上搜索有关特定程序的文章，作者们在文章中会分享他们的配置。**还有一种方法就是直接浏览其他人的配置文件**：您可以在这里找到无数的[dotfiles 仓库](https://github.com/search?o=desc&q=dotfiles&s=stars&type=Repositories) —— 其中最受欢迎的那些可以在[这里](https://github.com/mathiasbynens/dotfiles)找到（我们建议您不要直接复制别人的配置）。[这里](https://dotfiles.github.io/) 也有一些非常有用的资源。

### 可移植性

配置文件的一个常见的痛点是它可能并不能在多种设备上生效。例如，如果您在不同设备上使用的操作系统或者 shell 是不同的，则配置文件是无法生效的。或者，有时您仅希望特定的配置只在某些设备上生效。
有一些技巧可以轻松达成这些目的。如果配置文件 if 语句，则您可以借助它针对不同的设备编写不同的配置。例如，您的 shell 可以这样做：

```
if [[ "$(uname)" == "Linux" ]]; then {do_something}; fi

# 使用和 shell 相关的配置时先检查当前 shell 类型
if [[ "$SHELL" == "zsh" ]]; then {do_something}; fi

# 您也可以针对特定的设备进行配置
if [[ "$(hostname)" == "myServer" ]]; then {do_something}; fi
```

如果配置文件支持 include 功能，您也可以多加利用。例如：`~/.gitconfig` 可以这样编写：
```
[include]
    path = ~/.gitconfig_local
```

然后我们可以在日常使用的设备上创建配置文件 `~/.gitconfig_local` 来包含与该设备相关的特定配置。您甚至应该创建一个单独的代码仓库来管理这些与设备相关的配置。

如果您希望在不同的程序之间共享某些配置，该方法也适用。例如，如果您想要在 `bash` 和 `zsh` 中同时启用一些别名，您可以把它们写在 `.aliases` 里，然后在这两个 shell 里应用：
```
# Test if ~/.aliases exists and source it
if [ -f ~/.aliases ]; then
    source ~/.aliases
fi
```

## 远端设备
对于程序员来说，在他们的日常工作中使用远程服务器已经非常普遍了。如果您需要使用远程服务器来部署后端软件或您需要一些计算能力强大的服务器，您就会用到安全 shell（SSH）。和其他工具一样，SSH 也是可以高度定制的，也值得我们花时间学习它。

通过如下命令，您可以使用 `ssh` 连接到其他服务器：
```
ssh foo@bar.mit.edu
```
`Ctrl-Z`是暂停进程, 可以通过`jobs`查看, 

### 执行命令
`ssh` 的一个经常被忽视的特性是它**可以直接远程执行命令**。 `ssh foobar@server ls` 可以直接在用foobar的命令下执行 `ls` 命令。 想要配合管道来使用也可以， `ssh foobar@server ls | grep PATTERN` 会在本地查询远端 `ls` 的输出而 `ls | ssh foobar@server grep PATTERN` 会在远端对本地 `ls` 输出的结果进行查询。

### SSH 密钥
基于密钥的验证机制使用了密码学中的公钥，我们只需要向服务器证明客户端持有对应的私钥，而不需要公开其私钥。这样您就可以避免每次登录都输入密码的麻烦了秘密就可以登录。不过，私钥(通常是 `~/.ssh/id_rsa` 或者 `~/.ssh/id_ed25519`) 等效于您的密码，所以一定要好好保存它。

#### 密钥生成
使用 [`ssh-keygen`](http://man7.org/linux/man-pages/man1/ssh-keygen.1.html) 命令可以生成一对密钥：

```
ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/id_ed25519
```

您可以为密钥设置密码，防止有人持有您的私钥并使用它访问您的服务器。您可以使用 [`ssh-agent`](https://www.man7.org/linux/man-pages/man1/ssh-agent.1.html) 或 [`gpg-agent`](https://linux.die.net/man/1/gpg-agent) ，这样就不需要每次都输入该密码了。

如果您曾经配置过使用 SSH 密钥推送到 GitHub，那么可能您已经完成了[这里](https://help.github.com/articles/connecting-to-github-with-ssh/) 介绍的这些步骤，并且已经有了一个可用的密钥对。要检查您是否持有密码并验证它，您可以运行 `ssh-keygen -y -f /path/to/key`.

#### 基于密钥的认证机制
`ssh` 会查询 `.ssh/authorized_keys` 来确认那些用户可以被允许登录。您可以通过下面的命令将一个公钥拷贝到这里：
```
cat .ssh/id_ed25519 | ssh foobar@remote 'cat >> ~/.ssh/authorized_keys'
```

如果支持 `ssh-copy-id` 的话，可以使用下面这种更简单的解决方案：
```
ssh-copy-id -i .ssh/id_ed25519.pub foobar@remote
```

#### 通过 SSH 复制文件

使用 ssh 复制文件有很多方法：
- `ssh+tee`, 最简单的方法是执行 `ssh` 命令，然后通过这样的方法利用标准输入实现 `cat localfile | ssh remote_server tee serverfile`。回忆一下，[`tee`](https://www.man7.org/linux/man-pages/man1/tee.1.html) 命令会将标准输出写入到一个文件；
- [`scp`](https://www.man7.org/linux/man-pages/man1/scp.1.html) ：当需要拷贝大量的文件或目录时，使用`scp` 命令则更加方便，因为它可以方便的遍历相关路径。语法如下：`scp path/to/local_file remote_host:path/to/remote_file`；
- [`rsync`](https://www.man7.org/linux/man-pages/man1/rsync.1.html) 对 `scp` 进行了改进，它可以检测本地和远端的文件以防止重复拷贝。它还可以提供一些诸如符号连接、权限管理等精心打磨的功能。甚至还可以基于 `--partial`标记实现断点续传。`rsync` 的语法和`scp`类似；

## 端口转发

很多情况下我们都会遇到软件需要监听特定设备的端口。如果是在您的本机，可以使用 `localhost:PORT` 或 `127.0.0.1:PORT`。但是如果需要监听远程服务器的端口该如何操作呢？这种情况下远端的端口并不会直接通过网络暴露给您。

此时就需要进行 _端口转发_。端口转发有两种，一种是本地端口转发和远程端口转发（参见下图，该图片引用自这篇[StackOverflow 文章](https://unix.stackexchange.com/questions/115897/whats-ssh-port-forwarding-and-whats-the-difference-between-ssh-local-and-remot)）中的图片。

**本地端口转发**![Local Port Forwarding](https://i.stack.imgur.com/a28N8.png%C2%A0 "本地端口转发")

**远程端口转发**![Remote Port Forwarding](https://i.stack.imgur.com/4iK3b.png%C2%A0 "远程端口转发")

常见的情景是使用本地端口转发，即远端设备上的服务监听一个端口，而您希望在本地设备上的一个端口建立连接并转发到远程端口上。例如，我们在远端服务器上运行 Jupyter notebook 并监听 `8888` 端口。 然后，建立从本地端口 `9999` 的转发，使用 `ssh -L 9999:localhost:8888 foobar@remote_server` 。这样只需要访问本地的 `localhost:9999` 即可。

### SSH 配置

我们已经介绍了很多参数。为它们创建一个别名是个好想法，我们可以这样做：
```
alias my_server="ssh -i ~/.id_ed25519 --port 2222 -L 9999:localhost:8888 foobar@remote_server
```

**不过，更好的方法是使用 `~/.ssh/config`.**
```
Host vm
    User foobar
    HostName 172.16.174.141
    Port 2222
    IdentityFile ~/.ssh/id_ed25519
    LocalForward 9999 localhost:8888

# 在配置文件中也可以使用通配符
Host *.mit.edu
    User foobaz
```

这么做的好处是，使用 `~/.ssh/config` 文件来创建别名，类似 `scp`、`rsync`和`mosh`的这些命令都可以读取这个配置并将设置转换为对应的命令行选项。

注意，`~/.ssh/config` 文件也可以被当作配置文件，而且一般情况下也是可以被导入其他配置文件的。不过，如果您将其公开到互联网上，那么其他人都将会看到您的服务器地址、用户名、开放端口等等。这些信息可能会帮助到那些企图攻击您系统的黑客，所以请务必三思。

服务器侧的配置通常放在 `/etc/ssh/sshd_config`。您可以在这里配置免密认证、修改 ssh 端口、开启 X11 转发等等。 您也可以为每个用户单独指定配置。

### 杂项

连接远程服务器的一个常见痛点是遇到由关机、休眠或网络环境变化导致的掉线。如果连接的延迟很高也很让人讨厌。**[Mosh](https://mosh.org/)（即 mobile shell ）对 ssh 进行了改进，它允许连接漫游、间歇连接及智能本地回显。** 
有时将一个远端文件夹挂载到本地会比较方便， **[sshfs](https://github.com/libfuse/sshfs) 可以将远端服务器上的一个文件夹挂载到本地，然后您就可以使用本地的编辑器了。** 

## Shell & 框架

在 shell 工具和脚本那节课中我们已经介绍了 `bash` shell，因为它是目前最通用的 shell，大多数的系统都将其作为默认 shell。但是，它并不是唯一的选项。
例如，`zsh` shell 是 `bash` 的超集并提供了一些方便的功能：

- 智能替换, `**`
- 行内替换/通配符扩展
- 拼写纠错
- 更好的 tab 补全和选择
- 路径展开 (`cd /u/lo/b` 会被展开为 `/usr/local/bin`)

**框架** 也可以改进您的 shell。比较流行的通用框架包括[prezto](https://github.com/sorin-ionescu/prezto) 或 [oh-my-zsh](https://ohmyz.sh/)。还有一些更精简的框架，它们往往专注于某一个特定功能，例如[zsh 语法高亮](https://github.com/zsh-users/zsh-syntax-highlighting) 或 [zsh 历史子串查询](https://github.com/zsh-users/zsh-history-substring-search)。 像 [fish](https://fishshell.com/) 这样的 shell 包含了很多用户友好的功能，其中一些特性包括：
- 向右对齐
- 命令语法高亮
- 历史子串查询
- 基于手册页面的选项补全
- 更智能的自动补全
- 提示符主题

需要注意的是，使用这些框架可能会降低您 shell 的性能，尤其是如果这些框架的代码没有优化或者代码过多。您随时可以测试其性能或禁用某些不常用的功能来实现速度与功能的平衡。

## 终端模拟器
和自定义 shell 一样，花点时间选择适合您的 **终端模拟器**并进行设置是很有必要的。有许多终端模拟器可供您选择（这里有一些关于它们之间[比较](https://anarc.at/blog/2018-04-12-terminal-emulators-1/)的信息）

您会花上很多时间在使用终端上，因此研究一下终端的设置是很有必要的，您可以从下面这些方面来配置您的终端：
- 字体选择
- 彩色主题
- 快捷键
- 标签页/面板支持
- 回退配置
- 性能（像 [Alacritty](https://github.com/jwilm/alacritty) 或者 [kitty](https://sw.kovidgoyal.net/kitty/) 这种比较新的终端，它们支持GPU加速）。

# 版本控制(Git)

**版本控制系统 (VCSs) 是一类用于追踪源代码（或其他文件、文件夹）改动的工具。** 顾名思义，这些工具可以帮助我们管理代码的修改历史；不仅如此，它还可以让协作编码变得更方便。**VCS通过一系列的快照将某个文件夹及其内容保存了起来，每个快照都包含了文件或文件夹的完整状态。** 同时它还维护了快照创建者的信息以及每个快照的相关信息等等。

现代的版本控制系统可以帮助您轻松地（甚至自动地）回答以下问题：
- 当前模块是谁编写的？
- 这个文件的这一行是什么时候被编辑的？是谁作出的修改？修改原因是什么呢？
- 最近的1000个版本中，何时/为什么导致了单元测试失败？

## Git 的数据模型

进行版本控制的方法很多。Git 拥有一个经过精心设计的模型，这使其能够支持版本控制所需的所有特性，例如维护历史记录、支持分支和促进协作。

### 快照
Git 将顶级目录中的文件和文件夹作为**集合**，并通过一系列快照来管理其历史记录。在Git的术语里，文件被称作Blob对象（数据对象），也就是一组数据。目录则被称之为“树”，它将名字与 Blob 对象或树对象进行映射（使得目录中可以包含其他目录）。**快照则是被追踪的最顶层的树。** 例如，一个树看起来可能是这样的：
``` bash
<root> (tree)
|
+- foo (tree)
|  |
|  + bar.txt (blob, contents = "hello world")
|
+- baz.txt (blob, contents = "git is wonderful")
```

这个顶层的树包含了两个元素，一个名为 “foo” 的树（它本身包含了一个blob对象 “bar.txt”），以及一个 blob 对象 “baz.txt”。

### 历史记录建模：关联快照

版本控制系统和快照有什么关系呢？线性历史记录是一种最简单的模型，它包含了一组按照时间顺序线性排列的快照。不过处于种种原因，Git 并没有采用这样的模型。
在 Git 中，历史记录是一个由快照组成的有向无环图。这代表 Git 中的每个快照都有一系列的“父辈”，也就是其之前的一系列快照。注意，快照具有多个“父辈”而非一个，因为某个快照可能由多个父辈而来。例如，经过合并后的两条分支。

**在 Git 中，这些快照被称为“提交”。** 通过可视化的方式来表示这些历史提交记录时，看起来差不多是这样的：

```
o <-- o <-- o <-- o
            ^  
             \
              --- o <-- o
```

上面是一个 ASCII 码构成的简图，其中的 `o` 表示一次提交（快照）。（指向的是前一个版本）
箭头指向了当前提交的父辈（这是一种“在…之前”，而不是“在…之后”的关系）。在第三次提交之后，历史记录分岔成了两条独立的分支。这可能因为此时需要同时开发两个不同的特性，它们之间是相互独立的。开发完成后，这些分支可能会被合并并创建一个新的提交，这个新的提交会同时包含这些特性。新的提交会创建一个新的历史记录，看上去像这样（最新的合并提交用粗体标记）：
```

o <-- o <-- o <-- o <---- o
            ^            /
             \          v
              --- o <-- o
```

**Git 中的提交是不可改变的。但这并不代表错误不能被修改，只不过这种“修改”实际上是创建了一个全新的提交记录。** 而引用（参见下文）则被更新为指向这些新的提交。

### 数据模型及其伪代码表示

以伪代码的形式来学习 Git 的数据模型，可能更加清晰：
``` 
// 文件就是一组数据
type blob = array<byte>

// 一个包含文件和目录的目录
type tree = map<string, tree | blob>

// 每个提交都包含一个父辈，元数据和顶层树
type commit = struct {
    parent: array<commit>
    author: string
    message: string
    snapshot: tree
}
```
这是一种简洁的历史模型。

### 对象和内存寻址

Git 中的对象可以是 blob、树或提交：
```
type object = blob | tree | commit
```

Git 在储存数据时，所有的对象都会基于它们的 [SHA-1 哈希](https://en.wikipedia.org/wiki/SHA-1) 进行寻址。
```
objects = map<string, object>

def store(object):
    id = sha1(object)
    objects[id] = object

def load(id):
    return objects[id]
```

Blobs、树和提交都一样，它们都是对象。**当它们引用其他对象时，它们并没有真正的在硬盘上保存这些对象，而是仅仅保存了它们的哈希值作为引用。**
例如，[上面](https://missing-semester-cn.github.io/2020/version-control/#snapshots)例子中的树（可以通过 `git cat-file -p 698281bc680d1995c5f4caaf3359721a5a58d48d` 来进行可视化），看上去是这样的：
```
100644 blob 4448adbf7ecd394f42ae135bbeed9676e894af85    baz.txt
040000 tree c68d233a33c5c06e0340e4c224f0afca87c8ce87    foo
```

树本身会包含一些指向其他内容的指针，例如 `baz.txt` (blob) 和 `foo` (树)。如果我们用 `git cat-file -p 4448adbf7ecd394f42ae135bbeed9676e894af85`，即通过哈希值查看 baz.txt 的内容，会得到以下信息：
```
git is wonderful
```

### 引用

现在，所有的快照都可以通过它们的 SHA-1 哈希值来标记了。但这也太不方便了，谁也记不住一串 40 位的十六进制字符。
针对这一问题，**Git 的解决方法是给这些哈希值赋予人类可读的名字，也就是引用（references）。引用是指向提交的指针。** 与对象不同的是，它是可变的（引用可以被更新，指向新的提交）。例如，`master` 引用通常会指向主分支的最新一次提交。
```
references = map<string, string>

def update_reference(name, id):
    references[name] = id

def read_reference(name):
    return references[name]

def load_reference(name_or_id):
    if name_or_id in references:
        return load(references[name_or_id])
    else:
        return load(name_or_id)
```

这样，Git 就可以使用诸如 “master” 这样人类可读的名称来表示历史记录中某个特定的提交，而不需要在使用一长串十六进制字符了。
有一个细节需要我们注意， 通常情况下，我们会想要知道“我们当前所在位置”，并将其标记下来。这样当我们创建新的快照的时候，我们就可以知道它的相对位置（如何设置它的“父辈”）。**在 Git 中，我们当前的位置有一个特殊的索引，它就是 “HEAD”。**

### 仓库

最后，我们可以粗略地给出 Git 仓库的定义了：`对象` 和 `引用`。
**在硬盘上，Git 仅存储对象和引用：因为其数据模型仅包含这些东西。所有的 `git` 命令都对应着对提交树的操作，例如增加对象，增加或删除引用。**

当您输入某个指令时，请思考一下这条命令是如何对底层的图数据结构进行操作的。另一方面，如果您希望修改提交树，例如“丢弃未提交的修改和将 ‘master’ 引用指向提交 `5d83f9e` 时，有什么命令可以完成该操作（针对这个具体问题，您可以使用 `git checkout master; git reset --hard 5d83f9e`）

## 暂存区

Git 中还包括一个和数据模型完全不相关的概念，但它确是创建提交的接口的一部分。
就上面介绍的快照系统来说，您也许会期望它的实现里包括一个 “创建快照” 的命令，该命令能够基于当前工作目录的当前状态创建一个全新的快照。有些版本控制系统确实是这样工作的，但 Git 不是。我们希望简洁的快照，而且每次从当前状态创建快照可能效果并不理想。例如，考虑如下场景，您开发了两个独立的特性，然后您希望创建两个独立的提交，其中第一个提交仅包含第一个特性，而第二个提交仅包含第二个特性。或者，假设您在调试代码时添加了很多打印语句，然后您仅仅希望提交和修复 bug 相关的代码而丢弃所有的打印语句。
Git 处理这些场景的方法是使用一种叫做 “**暂存区**（staging area）”的机制，它允许您指定下次快照中要包括那些改动。

## Git 的命令行接口

为了避免重复信息，我们将不会详细解释以下命令行。强烈推荐您阅读 [Pro Git 中文版](https://git-scm.com/book/zh/v2)或可以观看本讲座的视频来学习。
### 基础

- `git help <command>`: 获取 git 命令的帮助信息
- `git init`: 创建一个新的 git 仓库，其数据会存放在一个名为 `.git` 的目录下
- `git status`: 显示当前的仓库状态
- `git add <filename>`: 添加文件到暂存区
- `git commit`: 创建一个新的提交
    - 如何编写 [良好的提交信息](https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)!
    - 为何要 [编写良好的提交信息](https://chris.beams.io/posts/git-commit/)
- `git log`: 显示历史日志
- `git log --all --graph --decorate`: 可视化历史记录（有向无环图）
- `git diff <filename>`: 显示与暂存区文件的差异
- `git diff <revision> <filename>`: 显示某个文件两个版本之间的差异
- `git checkout <revision>`: 更新 HEAD 和目前的分支

### 分支和合并
- `git branch`: 显示分支
- `git branch <name>`: 创建分支
- `git checkout -b <name>`: 创建分支并切换到该分支
    - 相当于 `git branch <name>; git checkout <name>`
- `git merge <revision>`: 合并到当前分支
- `git mergetool`: 使用工具来处理合并冲突
- `git rebase`: 将一系列补丁变基（rebase）为新的基线

### 远端操作
- `git remote`: 列出远端
- `git remote add <name> <url>`: 添加一个远端
- `git push <remote> <local branch>:<remote branch>`: 将对象传送至远端并更新远端引用
- `git branch --set-upstream-to=<remote>/<remote branch>`: 创建本地和远端分支的关联关系
- `git fetch`: 从远端获取对象/索引
- `git pull`: 相当于 `git fetch; git merge`
- `git clone`: 从远端下载仓库

### 撤销
- `git commit --amend`: 编辑提交的内容或信息
- `git reset HEAD <file>`: 恢复暂存的文件
- `git checkout -- <file>`: 丢弃修改
- `git restore`: git2.32版本后取代git reset 进行许多撤销操作

## Git 高级操作
- `git config`: Git 是一个 [高度可定制的](https://git-scm.com/docs/git-config) 工具
- `git clone --depth=1`: 浅克隆（shallow clone），不包括完整的版本历史信息
- `git add -p`: 交互式暂存
- `git rebase -i`: 交互式变基
- `git blame`: 查看最后修改某行的人
- `git stash`: 暂时移除工作目录下的修改内容
- `git bisect`: 通过二分查找搜索历史记录
- `.gitignore`: [指定](https://git-scm.com/docs/gitignore) 故意不追踪的文件

## 杂项
- **图形用户界面**: Git 的 [图形用户界面客户端](https://git-scm.com/downloads/guis) 有很多，但是我们自己并不使用这些图形用户界面的客户端，我们选择使用命令行接口
- **Shell 集成**: 将 Git 状态集成到您的 shell 中会非常方便。([zsh](https://github.com/olivierverdier/zsh-git-prompt), [bash](https://github.com/magicmonty/bash-git-prompt))。[Oh My Zsh](https://github.com/ohmyzsh/ohmyzsh)这样的框架中一般以及集成了这一功能
- **编辑器集成**: 和上面一条类似，将 Git 集成到编辑器中好处多多。[fugitive.vim](https://github.com/tpope/vim-fugitive) 是 Vim 中集成 GIt 的常用插件
- **工作流**: 我们已经讲解了数据模型与一些基础命令，但还没讨论到进行大型项目时的一些惯例 ( 有[很多](https://nvie.com/posts/a-successful-git-branching-model/) [不同的](https://www.endoflineblog.com/gitflow-considered-harmful) [处理方法](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow))
- **GitHub**: Git 并不等同于 GitHub。 在 GitHub 中您需要使用一个被称作[拉取请求（pull request）](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)的方法来向其他项目贡献代码
- **其他 Git 提供商**: GitHub 并不是唯一的。还有像 [GitLab](https://about.gitlab.com/) 和 [BitBucket](https://bitbucket.org/) 这样的平台。

## 资源

- [Pro Git](https://git-scm.com/book/en/v2) ，**强烈推荐**！学习前五章的内容可以教会您流畅使用 Git 的绝大多数技巧，因为您已经理解了 Git 的数据模型。后面的章节提供了很多有趣的高级主题。（[Pro Git 中文版](https://git-scm.com/book/zh/v2)）；
- [Oh Shit, Git!?!](https://ohshitgit.com/) ，简短的介绍了如何从 Git 错误中恢复；
- [Git for Computer Scientists](https://eagain.net/articles/git-for-computer-scientists/) ，简短的介绍了 Git 的数据模型，与本文相比包含较少量的伪代码以及大量的精美图片；
- [Git from the Bottom Up](https://jwiegley.github.io/git-from-the-bottom-up/)详细的介绍了 Git 的实现细节，而不仅仅局限于数据模型。好奇的同学可以看看；
- [How to explain git in simple words](https://smusamashah.github.io/blog/2017/10/14/explain-git-in-simple-words)；
- [Learn Git Branching](https://learngitbranching.js.org/) 通过基于浏览器的游戏来学习 Git ；

# 调试及性能分析
## 调试代码

### 打印调试法与日志

“最有效的 debug 工具就是细致的分析，配合恰当位置的打印语句” — Brian Kernighan, Unix 新手入门。
调试代码的第一种方法往往是在您发现问题的地方**添加一些打印语句**，然后不断重复此过程直到您获取了足够的信息并找到问题的根本原因。

另外一个方法是使用**日志**，而不是临时添加打印语句。日志较普通的打印语句有如下的一些优势：
- 您可以**将日志写入文件、socket** 或者甚至是发送到远端服务器而不仅仅是标准输出；
- 日志可以支持**严重等级**（例如 INFO, DEBUG, WARN, ERROR等)，这使您可以根据需要过滤日志；
- 对于新发现的问题，很可能您的日志中已经包含了可以帮助您**定位问题的足够的信息**。
[这里](https://missing-semester-cn.github.io/static/files/logger.py) 是一个包含日志的例程序：
``` bash
$ python logger.py
# Raw output as with just prints
$ python logger.py log
# Log formatted output
$ python logger.py log ERROR
# Print only ERROR levels and above
$ python logger.py color
# Color formatted output
```

有很多技巧可以使日志的可读性变得更好，我最喜欢的一个是技巧是对其进行着色。到目前为止，您应该已经知道，以**彩色文本** 显示终端信息时可读性更好。但是应该如何设置呢？
`ls` 和 `grep` 这样的程序会使用 [ANSI escape codes](https://en.wikipedia.org/wiki/ANSI_escape_code)，它是一系列的特殊字符，可以使您的 shell 改变输出结果的颜色。例如，执行 `echo -e "\e[38;2;255;0;0mThis is red\e[0m"` 会打印红色的字符串：`This is red` 。只要您的终端支持[真彩色](https://gist.github.com/XVilka/8346728#terminals--true-color)。如果您的终端不支持真彩色（例如 MacOS 的 Terminal.app），您可以使用支持更加广泛的 16 色，例如：”\e[31;1mThis is red\e[0m”。

下面这个脚本向您展示了如何在终端中打印多种颜色（只要您的终端支持真彩色）
``` bash
#!/usr/bin/env bash
for R in $(seq 0 20 255); do
    for G in $(seq 0 20 255); do
        for B in $(seq 0 20 255); do
            printf "\e[38;2;${R};${G};${B}m█\e[0m";
        done
    done
done
```

### 第三方日志系统

如果您正在构建大型软件系统，您很可能会使用到一些依赖，有些依赖会作为程序单独运行。如 Web 服务器、数据库或消息代理都是此类常见的**第三方依赖**。
和这些系统交互的时候，**阅读它们的日志是非常必要的**，因为仅靠客户端侧的错误信息可能并不足以定位问题。

幸运的是，大多数的程序都会将日志保存在您的系统中的某个地方。对于 UNIX 系统来说，程序的日志通常存放在 `/var/log`。例如， [NGINX](https://www.nginx.com/) web 服务器就将其日志存放于`/var/log/nginx`。
目前，系统开始使用 **system log**，您所有的日志都会保存在这里。**大多数（但不是全部的）Linux 系统都会使用 `systemd`，这是一个系统守护进程，它会控制您系统中的很多东西，例如哪些服务应该启动并运行**。`systemd` 会将日志以某种特殊格式存放于`/var/log/journal`，您可以使用 [`journalctl`](http://man7.org/linux/man-pages/man1/journalctl.1.html) 命令显示这些消息。

类似地，在 macOS 系统中是 `/var/log/system.log`，但是有更多的工具会使用系统日志，它的内容可以使用 [`log show`](https://www.manpagez.com/man/1/log/) 显示。
对于大多数的 UNIX 系统，您也可以使用[`dmesg`](http://man7.org/linux/man-pages/man1/dmesg.1.html) 命令来读取内核的日志。
如果您希望将日志加入到系统日志中，您可以使用 [`logger`](http://man7.org/linux/man-pages/man1/logger.1.html) 这个 shell 程序。下面这个例子显示了如何使用 `logger`并且如何找到能够将其存入系统日志的条目。
不仅如此，大多数的编程语言都支持向系统日志中写日志。

``` bash
logger "Hello Logs"
# On macOS
log show --last 1m | grep Hello
# On Linux
journalctl --since "1m ago" | grep Hello
```

正如我们在数据整理那节课上看到的那样，日志的内容可以非常的多，我们需要对其进行处理和过滤才能得到我们想要的信息。
如果您发现您需要对 `journalctl` 和 `log show` 的结果进行大量的过滤，那么此时可以考虑使用它们自带的选项**对其结果先过滤一遍再输出**。还有一些像 [`lnav`](http://lnav.org/) 这样的工具，它为日志文件提供了更好的展现和浏览方式。

### 调试器

当通过打印已经不能满足您的调试需求时，您应该使用**调试器**。调试器是一种**可以允许我们和正在执行的程序进行交互的程序**，它可以做到：
- 当到达某一行时将程序暂停；
- 一次一条指令地逐步执行程序；
- 程序崩溃后查看变量的值；
- 满足特定条件时暂停程序；
- 其他高级功能。

很多编程语言都有自己的调试器。**Python 的调试器是[`pdb`](https://docs.python.org/3/library/pdb.html).**
下面对`pdb` 支持的命令进行简单的介绍：
- **l**(ist) - 显示当前行附近的11行或继续执行之前的显示；
- **s**(tep) - 执行当前行，并在第一个可能的地方停止；
- **n**(ext) - 继续执行直到当前函数的下一条语句或者 return 语句；
- **b**(reak) - 设置断点（基于传入的参数）；
- **p**(rint) - 在当前上下文对表达式求值并打印结果。还有一个命令是**pp** ，它使用 [`pprint`](https://docs.python.org/3/library/pprint.html) 打印；
- **r**(eturn) - 继续执行直到当前函数返回；
- **q**(uit) - 退出调试器。

让我们使用`pdb` 来修复下面的 Python 代码（参考讲座视频）`python -m ipdb bubble.py`
``` python
def bubble_sort(arr):
    n = len(arr)
    for i in range(n):
        for j in range(n):
            if arr[j] > arr[j+1]:
                arr[j] = arr[j+1]
                arr[j+1] = arr[j]
    return arr

print(bubble_sort([4, 2, 1, 8, 7, 6]))
```

注意，因为 Python 是一种解释型语言，所以我们可以通过 `pdb` shell 执行命令。 [`ipdb`](https://pypi.org/project/ipdb/) 是一种增强型的 `pdb` ，它使用[`IPython`](https://ipython.org/) 作为 REPL并开启了 tab 补全、语法高亮、更好的回溯和更好的内省，同时还保留了`pdb` 模块相同的接口。
**对于更底层的编程语言，您可能需要了解一下 [`gdb`](https://www.gnu.org/software/gdb/) ( 以及它的改进版 [`pwndbg`](https://github.com/pwndbg/pwndbg)) 和 [`lldb`](https://lldb.llvm.org/)。**
它们都对类 C 语言的调试进行了优化，它允许您探索任意进程及其机器状态：寄存器、堆栈、程序计数器等。

### 专门工具
即使您需要调试的程序是一个二进制的黑盒程序，仍然有一些工具可以帮助到您。**当您的程序需要执行一些只有操作系统内核才能完成的操作时，它需要使用 [系统调用](https://en.wikipedia.org/wiki/System_call)。有一些命令可以帮助您追踪您的程序执行的系统调用。在 Linux 中可以使用[`strace`](http://man7.org/linux/man-pages/man1/strace.1.html) ，在 macOS 和 BSD 中可以使用 [`dtrace`](http://dtrace.org/blogs/about/)。**`dtrace` 用起来可能有些别扭，因为它使用的是它自有的 `D` 语言，但是我们可以使用一个叫做 [`dtruss`](https://www.manpagez.com/man/1/dtruss/) 的封装使其具有和 `strace` (更多信息参考 [这里](https://8thlight.com/blog/colin-jones/2015/11/06/dtrace-even-better-than-strace-for-osx.html))类似的接口
下面的例子展现来如何使用 `strace` 或 `dtruss` 来显示`ls` 执行时，对[`stat`](http://man7.org/linux/man-pages/man2/stat.2.html) 系统调用进行追踪对结果。若需要深入了解 `strace`，[这篇文章](https://blogs.oracle.com/linux/strace-the-sysadmins-microscope-v2) 值得一读。
``` bash
# On Linux
sudo strace -e lstat ls -l > /dev/null
4
# On macOS
sudo dtruss -t lstat64_extended ls -l > /dev/null
```

有些情况下，我们需要**查看网络数据包**才能定位问题。像 [`tcpdump`](http://man7.org/linux/man-pages/man1/tcpdump.1.html) 和 [Wireshark](https://www.wireshark.org/) 这样的网络数据包分析工具可以帮助您获取网络数据包的内容并基于不同的条件进行过滤。

**对于 web 开发， Chrome/Firefox 的开发者工具**非常方便，功能也很强大：
- 源码 -查看任意站点的 HTML/CSS/JS 源码；
- 实时地修改 HTML, CSS, JS 代码 - 修改网站的内容、样式和行为用于测试（从这一点您也能看出来，网页截图是不可靠的）；
- Javascript shell - 在 JS REPL中执行命令；
- 网络 - 分析请求的时间线；
- 存储 - 查看 Cookies 和本地应用存储。

### 静态分析

**有些问题是您不需要执行代码就能发现的。** 例如，仔细观察一段代码，您就能发现某个循环变量覆盖了某个已经存在的变量或函数名；或是有个变量在被读取之前并没有被定义。 这种情况下 [静态分析](https://en.wikipedia.org/wiki/Static_program_analysis) 工具就可以帮我们找到问题。**静态分析会将程序的源码作为输入然后基于编码规则对其进行分析并对代码的正确性进行推理。**

下面这段 Python 代码中存在几个问题。 首先，我们的循环变量`foo` 覆盖了之前定义的函数`foo`。最后一行，我们还把 `bar` 错写成了`baz`，因此当程序完成`sleep` (一分钟)后，执行到这一行的时候便会崩溃。
``` python
import time

def foo():
    return 42

for foo in range(5):
    print(foo)
bar = 1
bar *= 0.2
time.sleep(60)
print(baz)
```

静态分析工具可以发现此类的问题。**当我们使用[`pyflakes`](https://pypi.org/project/pyflakes) 分析代码的时候，我们会得到与这两处 bug 相关的错误信息。[`mypy`](http://mypy-lang.org/) 则是另外一个工具，它可以对代码进行类型检查。** 这里，`mypy` 会经过我们`bar` 起初是一个 `int` ，然后变成了 `float`。这些问题都可以在不运行代码的情况下被发现。
``` bash
$ pyflakes foobar.py
foobar.py:6: redefinition of unused 'foo' from line 3
foobar.py:11: undefined name 'baz'

$ mypy foobar.py
foobar.py:6: error: Incompatible types in assignment (expression has type "int", variable has type "Callable[[], Any]")
foobar.py:9: error: Incompatible types in assignment (expression has type "float", variable has type "int")
foobar.py:11: error: Name 'baz' is not defined
Found 3 errors in 1 file (checked 1 source file)
```

**在 shell 工具那一节课的时候，我们介绍了 [`shellcheck`](https://www.shellcheck.net/)，这是一个类似的工具，但它是应用于 shell 脚本的。**

大多数的编辑器和 IDE 都支持在编辑界面显示这些工具的分析结果、高亮有警告和错误的位置。 这个过程通常称为 **code linting** 。风格检查或安全检查的结果同样也可以进行相应的显示。

**在 vim 中，有 [`ale`](https://vimawesome.com/plugin/ale) 或 [`syntastic`](https://vimawesome.com/plugin/syntastic) 可以帮助您做同样的事情。 在 Python 中， [`pylint`](https://www.pylint.org/) 和 [`pep8`](https://pypi.org/project/pep8/) 是两种用于进行风格检查的工具，而 [`bandit`](https://pypi.org/project/bandit/) 工具则用于检查安全相关的问题。**

对于其他语言的开发者来说，静态分析工具可以参考这个列表：[Awesome Static Analysis](https://github.com/mre/awesome-static-analysis) (您也许会对 _Writing_ 一节感兴趣) 。对于 linters 则可以参考这个列表： [Awesome Linters](https://github.com/caramelomartins/awesome-linters)。

**对于风格检查和代码格式化，还有以下一些工具可以作为补充**：用于 Python 的 [`black`](https://github.com/psf/black)、用于 Go 语言的 `gofmt`、用于 Rust 的 `rustfmt` 或是用于 JavaScript, HTML 和 CSS 的 [`prettier`](https://prettier.io/) 。这些工具可以自动格式化您的代码，这样代码风格就可以与常见的风格保持一致。 尽管您可能并不想对代码进行风格控制，标准的代码风格有助于方便别人阅读您的代码，也可以方便您阅读它的代码。

## 性能分析

即使您的代码能够像您期望的一样运行，但是如果它消耗了您全部的 CPU 和内存，那么它显然也不是个好程序。算法课上我们通常会介绍大O标记法，但却没教给我们如何找到程序中的热点。 鉴于 [过早的优化是万恶之源](http://wiki.c2.com/?PrematureOptimization)，您需要学习性能分析和监控工具，它们会帮助您找到程序中最耗时、最耗资源的部分，这样您就可以有针对性的进行性能优化。

### 计时
和调试代码类似，大多数情况下我们只需要打**印两处代码之间的时间即可发现问题**。下面这个例子中，我们使用了 Python 的 [`time`](https://docs.python.org/3/library/time.html)模块。
``` python
import time, random
n = random.randint(1, 10) * 100

# 获取当前时间 
start = time.time()

# 执行一些操作
print("Sleeping for {} ms".format(n))
time.sleep(n/1000)

# 比较当前时间和起始时间
print(time.time() - start)

# Output
# Sleeping for 500 ms
# 0.5713930130004883
```

不过，执行时间（wall clock time）也可能会误导您，因为您的电脑可能也在同时运行其他进程，也可能在此期间发生了等待。 对于工具来说，需要区分真实时间、用户时间和系统时间。**通常来说，用户时间+系统时间代表了您的进程所消耗的实际 CPU （更详细的解释可以参照[这篇文章](https://stackoverflow.com/questions/556405/what-do-real-user-and-sys-mean-in-the-output-of-time1)）。**
- 真实时间 - 从程序开始到结束流失掉的真实时间，包括其他进程的执行时间以及阻塞消耗的时间（例如等待 I/O或网络）；
- User - CPU 执行用户代码所花费的时间；
- Sys - CPU 执行系统内核代码所花费的时间。

例如，试着执行一个用于发起 HTTP 请求的命令并在其前面添加 [`time`](http://man7.org/linux/man-pages/man1/time.1.html) 前缀。网络不好的情况下您可能会看到下面的输出结果。请求花费了 2s 才完成，但是进程仅花费了 15ms 的 CPU 用户时间和 12ms 的 CPU 内核时间。
``` bash
$ time curl https://missing.csail.mit.edu &> /dev/null
real    0m2.561s
user    0m0.015s
sys     0m0.012s
```

### 性能分析工具（profilers）
#### CPU
**大多数情况下，当人们提及性能分析工具的时候，通常指的是 CPU 性能分析工具。 CPU 性能分析工具有两种： 追踪分析器（_tracing_）及采样分析器（_sampling_）。** 追踪分析器 会记录程序的每一次函数调用，而采样分析器则只会周期性的监测（通常为每毫秒）您的程序并记录程序堆栈。它们使用这些记录来生成统计信息，显示程序在哪些事情上花费了最多的时间。如果您希望了解更多相关信息，可以参考[这篇](https://jvns.ca/blog/2017/12/17/how-do-ruby---python-profilers-work-) 介绍性的文章。

大多数的编程语言都有一些基于命令行的分析器，我们可以使用它们来分析代码。它们通常可以集成在 IDE 中，但是本节课我们会专注于这些命令行工具本身。
在 Python 中，我们使用 `cProfile` 模块来分析每次函数调用所消耗的时间。 在下面的例子中，我们实现了一个基础的 grep 命令：
``` python
#!/usr/bin/env python

import sys, re

def grep(pattern, file):
    with open(file, 'r') as f:
        print(file)
        for i, line in enumerate(f.readlines()):
            pattern = re.compile(pattern)
            match = pattern.search(line)
            if match is not None:
                print("{}: {}".format(i, line), end="")

if __name__ == '__main__':
    times = int(sys.argv[1])
    pattern = sys.argv[2]
    for i in range(times):
        for file in sys.argv[3:]:
            grep(pattern, file)
```

我们可以使用下面的命令来对这段代码进行分析。通过它的输出我们可以知道，IO 消耗了大量的时间，编译正则表达式也比较耗费时间。因为正则表达式只需要编译一次，我们可以将其移动到 for 循环外面来改进性能。
``` bash
$ python -m cProfile -s tottime grep.py 1000 '^(import|\s*def)[^,]*$' *.py

[omitted program output]

 ncalls  tottime  percall  cumtime  percall filename:lineno(function)
   8000    0.266    0.000    0.292    0.000 {built-in method io.open}
   8000    0.153    0.000    0.894    0.000 grep.py:5(grep)
  17000    0.101    0.000    0.101    0.000 {built-in method builtins.print}
   8000    0.100    0.000    0.129    0.000 {method 'readlines' of '_io._IOBase' objects}
  93000    0.097    0.000    0.111    0.000 re.py:286(_compile)
  93000    0.069    0.000    0.069    0.000 {method 'search' of '_sre.SRE_Pattern' objects}
  93000    0.030    0.000    0.141    0.000 re.py:231(compile)
  17000    0.019    0.000    0.029    0.000 codecs.py:318(decode)
      1    0.017    0.017    0.911    0.911 grep.py:3(<module>)

[omitted lines]
```

关于 Python 的 `cProfile` 分析器（以及其他一些类似的分析器），需要注意的是它显示的是每次函数调用的时间。看上去可能快到反直觉，尤其是如果您在代码里面使用了第三方的函数库，因为内部函数调用也会被看作函数调用。

**更加符合直觉的显示分析信息的方式是包括每行代码的执行时间，这也是_行分析器_的工作。** 例如，下面这段 Python 代码会向本课程的网站发起一个请求，然后解析响应返回的页面中的全部 URL：
``` python
#!/usr/bin/env python
import requests
from bs4 import BeautifulSoup

# 这个装饰器会告诉行分析器 
# 我们想要分析这个函数
@profile
def get_urls():
    response = requests.get('https://missing.csail.mit.edu')
    s = BeautifulSoup(response.content, 'lxml')
    urls = []
    for url in s.find_all('a'):
        urls.append(url['href'])

if __name__ == '__main__':
    get_urls()
```

如果我们使用 Python 的 `cProfile` 分析器，我们会得到超过2500行的输出结果，即使对其进行排序，我仍然搞不懂时间到底都花在哪了。如果我们使用 [`line_profiler`](https://github.com/pyutils/line_profiler)，它会基于行来显示时间：
``` bash
$ kernprof -l -v a.py
Wrote profile results to urls.py.lprof
Timer unit: 1e-06 s

Total time: 0.636188 s
File: a.py
Function: get_urls at line 5

Line #  Hits         Time  Per Hit   % Time  Line Contents
==============================================================
 5                                           @profile
 6                                           def get_urls():
 7         1     613909.0 613909.0     96.5      response = requests.get('https://missing.csail.mit.edu')
 8         1      21559.0  21559.0      3.4      s = BeautifulSoup(response.content, 'lxml')
 9         1          2.0      2.0      0.0      urls = []
10        25        685.0     27.4      0.1      for url in s.find_all('a'):
11        24         33.0      1.4      0.0          urls.append(url['href'])
```

#### 内存

像 C 或者 C++ 这样的语言，内存泄漏会导致您的程序在使用完内存后不去释放它。为了应对内存类的 Bug，我们可以使用类似 [Valgrind](https://valgrind.org/) 这样的工具来检查内存泄漏问题。
对于 Python 这类具有垃圾回收机制的语言，内存分析器也是很有用的，因为对于某个对象来说，只要有指针还指向它，那它就不会被回收。

**下面这个例子及其输出，展示了 [memory-profiler](https://pypi.org/project/memory-profiler/) 是如何工作的（注意装饰器和 `line-profiler` 类似）。**
``` python
@profile
def my_func():
    a = [1] * (10 ** 6)
    b = [2] * (2 * 10 ** 7)
    del b
    return a

if __name__ == '__main__':
    my_func()
```

```
$ python -m memory_profiler example.py
Line #    Mem usage  Increment   Line Contents
==============================================
     3                           @profile
     4      5.97 MB    0.00 MB   def my_func():
     5     13.61 MB    7.64 MB       a = [1] * (10 ** 6)
     6    166.20 MB  152.59 MB       b = [2] * (2 * 10 ** 7)
     7     13.61 MB -152.59 MB       del b
     8     13.61 MB    0.00 MB       return a
```

#### 事件分析
在我们使用`strace`调试代码的时候，您可能会希望忽略一些特殊的代码并希望在分析时将其当作黑盒处理。**[`perf`](http://man7.org/linux/man-pages/man1/perf.1.html) 命令将 CPU 的区别进行了抽象，它不会报告时间和内存的消耗，而是报告与您的程序相关的系统事件。**

例如，`perf` 可以报告不佳的缓存局部性（poor cache locality）、大量的页错误（page faults）或活锁（livelocks）。下面是关于常见命令的简介：
- `perf list` - 列出可以被 pref 追踪的事件；
- `perf stat COMMAND ARG1 ARG2` - 收集与某个进程或指令相关的事件；
- `perf record COMMAND ARG1 ARG2` - 记录命令执行的采样信息并将统计数据储存在`perf.data`中；
- `perf report` - 格式化并打印 `perf.data` 中的数据。

#### 可视化
使用分析器来分析真实的程序时，由于软件的复杂性，其输出结果中将包含大量的信息。人类是一种视觉动物，非常不善于阅读大量的文字。因此很多工具都提供了**可视化分析器**输出结果的功能。

**对于采样分析器来说，常见的显示 CPU 分析数据的形式是 [火焰图](http://www.brendangregg.com/flamegraphs.html)，火焰图会在 Y 轴显示函数调用关系，并在 X 轴显示其耗时的比例。** 火焰图同时还是可交互的，您可以深入程序的某一具体部分，并查看其栈追踪.

**调用图和控制流图可以显示子程序之间的关系，它将函数作为节点并把函数调用作为边。** 将它们和分析器的信息（例如调用次数、耗时等）放在一起使用时，调用图会变得非常有用，它可以帮助我们分析程序的流程。 在 Python 中您可以使用 [`pycallgraph`](http://pycallgraph.slowchop.com/en/master/) 来生成这些图片。

### 资源监控
有时候，分析程序性能的第一步是搞清楚它所消耗的资源。程序变慢通常是因为它所需要的资源不够了。例如，没有足够的内存或者网络连接变慢的时候。
**有很多很多的工具可以被用来显示不同的系统资源，例如 CPU 占用、内存使用、网络、磁盘使用等。**
- **通用监控** - 最流行的工具要数 [`htop`](https://htop.dev/),了，它是 [`top`](http://man7.org/linux/man-pages/man1/top.1.html)的改进版。`htop` 可以显示当前运行进程的多种统计信息。`htop` 有很多选项和快捷键，常见的有：`<F6>` 进程排序、 `t` 显示树状结构和 `h` 打开或折叠线程。 还可以留意一下 [`glances`](https://nicolargo.github.io/glances/) ，它的实现类似但是用户界面更好。如果需要合并测量全部的进程， [`dstat`](http://dag.wiee.rs/home-made/dstat/) 是也是一个非常好用的工具，它可以实时地计算不同子系统资源的度量数据，例如 I/O、网络、 CPU 利用率、上下文切换等等；
- **I/O 操作** - [`iotop`](http://man7.org/linux/man-pages/man8/iotop.8.html) 可以显示实时 I/O 占用信息而且可以非常方便地检查某个进程是否正在执行大量的磁盘读写操作；
- **磁盘使用** - [`df`](http://man7.org/linux/man-pages/man1/df.1.html) 可以显示每个分区的信息，而 [`du`](http://man7.org/linux/man-pages/man1/du.1.html) 则可以显示当前目录下每个文件的磁盘使用情况（ **d**isk **u**sage）。`-h` 选项可以使命令以对人类（**h**uman）更加友好的格式显示数据；[`ncdu`](https://dev.yorhel.nl/ncdu)是一个交互性更好的 `du` ，它可以让您在不同目录下导航、删除文件和文件夹；
- **内存使用** - [`free`](http://man7.org/linux/man-pages/man1/free.1.html) 可以显示系统当前空闲的内存。内存也可以使用 `htop` 这样的工具来显示；
- **打开文件** - [`lsof`](http://man7.org/linux/man-pages/man8/lsof.8.html) 可以列出被进程打开的文件信息。 当我们需要查看某个文件是被哪个进程打开的时候，这个命令非常有用；
- **网络连接和配置** - [`ss`](http://man7.org/linux/man-pages/man8/ss.8.html) 能帮助我们监控网络包的收发情况以及网络接口的显示信息。`ss` 常见的一个使用场景是找到端口被进程占用的信息。如果要显示路由、网络设备和接口信息，您可以使用 [`ip`](http://man7.org/linux/man-pages/man8/ip.8.html) 命令。注意，`netstat` 和 `ifconfig` 这两个命令已经被前面那些工具所代替了。
- **网络使用** - [`nethogs`](https://github.com/raboof/nethogs) 和 [`iftop`](http://www.ex-parrot.com/pdw/iftop/) 是非常好的用于对网络占用进行监控的交互式命令行工具。
如果您希望测试一下这些工具，您可以使用 [`stress`](https://linux.die.net/man/1/stress) 命令来为系统人为地增加负载。

#### 专用工具
有时候，您只需要对黑盒程序进行基准测试，并依此对软件选择进行评估。 类似 [`hyperfine`](https://github.com/sharkdp/hyperfine) 这样的命令行可以帮您快速进行基准测试。例如，我们在 shell 工具和脚本那一节课中我们推荐使用 `fd` 来代替 `find`。我们这里可以用`hyperfine`来比较一下它们。
例如，下面的例子中，我们可以看到`fd` 比 `find` 要快20倍。
``` bash
$ hyperfine --warmup 3 'fd -e jpg' 'find . -iname "*.jpg"'
Benchmark #1: fd -e jpg
  Time (mean ± σ):      51.4 ms ±   2.9 ms    [User: 121.0 ms, System: 160.5 ms]
  Range (min … max):    44.2 ms …  60.1 ms    56 runs

Benchmark #2: find . -iname "*.jpg"
  Time (mean ± σ):      1.126 s ±  0.101 s    [User: 141.1 ms, System: 956.1 ms]
  Range (min … max):    0.975 s …  1.287 s    10 runs

Summary
  'fd -e jpg' ran
   21.89 ± 2.33 times faster than 'find . -iname "*.jpg"'
```

和 debug 一样，浏览器也包含了很多不错的性能分析工具，可以用来分析页面加载，让我们可以搞清楚时间都消耗在什么地方（加载、渲染、脚本等等）。 更多关于 [Firefox](https://developer.mozilla.org/en-US/docs/Mozilla/Performance/Profiling_with_the_Built-in_Profiler) 和 [Chrome](https://developers.google.com/web/tools/chrome-devtools/rendering-tools)的信息可以点击链接。

# 元编程

因为我们今天要讲的东西，更多是关于流程 ，而不是写代码或更高效的工作。本节课我们会学习**构建系统、代码测试以及依赖管理**。在您还是学生的时候，这些东西看上去似乎对您来说没那么重要，不过当您开始实习或走进社会的时候，您将会接触到大型的代码库，本节课讲授的这些东西也会变得随处可见。必须要指出的是，“元编程” 也有[用于操作程序的程序](https://en.wikipedia.org/wiki/Metaprogramming)” 之含义，这和我们今天讲座所介绍的概念是完全不同的。

## 构建系统
如果您使用 LaTeX 来编写论文，您需要执行哪些命令才能编译出您想要的论文呢？执行基准测试、绘制图表然后将其插入论文的命令又有哪些？或者，如何编译本课程提供的代码并执行测试呢？
**对于大多数系统来说，不论其是否包含代码，都会包含一个“构建过程”。** 有时，您需要执行一系列操作。通常，这一过程包含了很多步骤，很多分支。执行一些命令来生成图表，然后执行另外的一些命令生成结果，然后再执行其他的命令来生成最终的论文。有很多事情需要我们完成，您并不是第一个因此感到苦恼的人，幸运的是，有很多工具可以帮助我们完成这些操作。

这些工具通常被称为 “构建系统”，而且这些工具还不少。如何选择工具完全取决于您当前手头上要完成的任务以及项目的规模。**从本质上讲，这些工具都是非常类似的。您需要定义依赖、目标和规则。您必须告诉构建系统您具体的构建目标，系统的任务则是找到构建这些目标所需要的依赖，并根据规则构建所需的中间产物，直到最终目标被构建出来。** 理想的情况下，如果目标的依赖没有发生改动，并且我们可以从之前的构建中复用这些依赖，那么与其相关的构建规则并不会被执行。

`make` 是最常用的构建系统之一，您会发现它通常被安装到了几乎所有基于UNIX的系统中。`make`并不完美，但是对于中小型项目来说，它已经足够好了。**当您执行 `make` 时，它会去参考当前目录下名为 `Makefile` 的文件。所有构建目标、相关依赖和规则都需要在该文件中定义，它看上去是这样的**

``` make
paper.pdf: paper.tex plot-data.png
	pdflatex paper.tex

plot-%.png: %.dat plot.py
	./plot.py -i $*.dat -o $@
```

**这个文件中的指令，即如何使用右侧文件构建左侧文件的规则。或者，换句话说，冒号左侧的是构建目标，冒号右侧的是构建它所需的依赖。缩进的部分是从依赖构建目标时需要用到的一段程序。** 在 `make` 中，第一条指令还指明了构建的目的，如果您使用不带参数的 `make`，这便是我们最终的构建结果。或者，您可以使用这样的命令来构建其他目标：`make plot-data.png`。

**规则中的 `%` 是一种模式，它会匹配其左右两侧相同的字符串。** 例如，如果目标是 `plot-foo.png`， `make` 会去寻找 `foo.dat` 和 `plot.py` 作为依赖。现在，让我们看看如果在一个空的源码目录中执行`make` 会发生什么？
``` make
$ make
make: *** No rule to make target 'paper.tex', needed by 'paper.pdf'.  Stop.
```

`make` 会告诉我们，为了构建出`paper.pdf`，它需要 `paper.tex`，但是并没有一条规则能够告诉它如何构建该文件。让我们构建它吧！
``` make
$ touch paper.tex
$ make
make: *** No rule to make target 'plot-data.png', needed by 'paper.pdf'.  Stop.
```

有意思，我们是**有**构建 `plot-data.png` 的规则的，但是这是一条模式规则。因为源文件`data.dat` 并不存在，因此 `make` 就会告诉您它不能构建 `plot-data.png`，让我们创建这些文件：
``` latex
$ cat paper.tex
\documentclass{article}
\usepackage{graphicx}
\begin{document}
\includegraphics[scale=0.65]{plot-data.png}
\end{document}
$ cat plot.py
#!/usr/bin/env python
import matplotlib
import matplotlib.pyplot as plt
import numpy as np
import argparse

parser = argparse.ArgumentParser()
parser.add_argument('-i', type=argparse.FileType('r'))
parser.add_argument('-o')
args = parser.parse_args()

data = np.loadtxt(args.i)
plt.plot(data[:, 0], data[:, 1])
plt.savefig(args.o)
$ cat data.dat
1 1
2 2
3 3
4 4
5 8
```

当我们执行 `make` 时会发生什么？
```
$ make
./plot.py -i data.dat -o plot-data.png
pdflatex paper.tex
... lots of output ...
```

看！PDF ！
如果再次执行 `make` 会怎样？
``` make
$ make
make: 'paper.pdf' is up to date.
```
什么事情都没做！为什么？好吧，因为它什么都不需要做。make回去检查之前的构建是因其依赖改变而需要被更新。让我们试试修改 `paper.tex` 在重新执行 `make`
``` make
$ vim paper.tex
$ make
pdflatex paper.tex
...
```
注意 `make` 并**没有**重新构建 `plot.py`，因为没必要；`plot-data.png` 的所有依赖都没有发生改变。

## 依赖管理

就您的项目来说，它的依赖可能本身也是其他的项目。您也许会依赖某些程序(例如 `python`)、系统包 (例如 `openssl`)或相关编程语言的库(例如 `matplotlib`)。 现在，大多数的依赖可以通过某些**软件仓库**来获取，这些仓库会在一个地方托管大量的依赖，我们则可以通过一套非常简单的机制来安装依赖。例如 Ubuntu 系统下面有Ubuntu软件包仓库，您可以通过`apt` 这个工具来访问， RubyGems 则包含了 Ruby 的相关库，PyPi 包含了 Python 库， Arch Linux 用户贡献的库则可以在 Arch User Repository 中找到。

由于每个仓库、每种工具的运行机制都不太一样，因此我们并不会在本节课深入讲解具体的细节。我们会介绍一些通用的术语，例如_版本控制_。**大多数被其他项目所依赖的项目都会在每次发布新版本时创建一个版本号**。通常看上去像 8.1.3 或 64.1.20192004。版本号一般是数字构成的，但也并不绝对。**版本号有很多用途，其中最重要的作用是保证软件能够运行。** 试想一下，假如我的库要发布一个新版本，在这个版本里面我重命名了某个函数。如果有人在我的库升级版本后，仍希望基于它构建新的软件，那么很可能构建会失败，因为它希望调用的函数已经不复存在了。有了版本控制就可以很好的解决这个问题，我们可以指定当前项目需要基于某个版本，甚至某个范围内的版本，或是某些项目来构建。这么做的话，即使某个被依赖的库发生了变化，依赖它的软件可以基于其之前的版本进行构建。

这样还并不理想！如果我们发布了一项和安全相关的升级，它并没有影响到任何公开接口（API），但是处于安全的考虑，依赖它的项目都应该立即升级，那应该怎么做呢？这也是版本号包含多个部分的原因。**不同项目所用的版本号其具体含义并不完全相同，但是一个相对比较常用的标准是[语义版本号](https://semver.org/)，这种版本号具有不同的语义，它的格式是这样的：主版本号.次版本号.补丁号。相关规则有**：
- 如果新的版本没有改变 API，请将补丁号递增；
- 如果您添加了 API 并且该改动是向后兼容的，请将次版本号递增；
- 如果您修改了 API 但是它并不向后兼容，请将主版本号递增。

**这么做有很多好处。现在如果我们的项目是基于您的项目构建的，那么只要最新版本的主版本号只要没变就是安全的 ，次版本号不低于之前我们使用的版本即可。** 换句话说，如果我依赖的版本是`1.3.7`，那么使用`1.3.8`、`1.6.1`，甚至是`1.3.0`都是可以的。如果版本号是 `2.2.4` 就不一定能用了，因为它的主版本号增加了。我们可以将 Python 的版本号作为语义版本号的一个实例。您应该知道，Python 2 和 Python 3 的代码是不兼容的，这也是为什么 Python 的主版本号改变的原因。类似的，使用 Python 3.5 编写的代码在 3.7 上可以运行，但是在 3.4 上可能会不行。

使用依赖管理系统的时候，您可能会遇到锁文件（_lock files_）这一概念。**锁文件列出了您当前每个依赖所对应的具体版本号。通常，您需要执行升级程序才能更新依赖的版本。** 这么做的原因有很多，例如避免不必要的重新编译、创建可复现的软件版本或禁止自动升级到最新版本（可能会包含 bug）。还有一种极端的依赖锁定叫做 _vendoring_，它会把您的依赖中的所有代码直接拷贝到您的项目中，这样您就能够完全掌控代码的任何修改，同时您也可以将自己的修改添加进去，不过这也意味着如果该依赖的维护者更新了某些代码，您也必须要自己去拉取这些更新。

## 持续集成系统

随着您接触到的项目规模越来越大，您会发现修改代码之后还有很多额外的工作要做。**您可能需要上传一份新版本的文档、上传编译后的文件到某处、发布代码到 pypi，执行测试套件等等。** 或许您希望每次有人提交代码到 GitHub 的时候，他们的代码风格被检查过并执行过某些基准测试？如果您有这方面的需求，那么请花些时间了解一下持续集成。
持续集成，或者叫做 CI 是一种雨伞术语（umbrella term，涵盖了一组术语的术语），它指的是那些“**当您的代码变动时，自动运行的东西**”，市场上有很多提供各式各样 CI 工具的公司，这些工具大部分都是免费或开源的。比较大的有 Travis CI、Azure Pipelines 和 GitHub Actions。它们的工作原理都是类似的：**您需要在代码仓库中添加一个文件，描述当前仓库发生任何修改时，应该如何应对。** 目前为止，最常见的规则是：如果有人提交代码，执行测试套件。当这个事件被触发时，**CI 提供方会启动一个（或多个）虚拟机，执行您制定的规则，并且通常会记录下相关的执行结果。** 您可以进行某些设置，这样当测试套件失败时您能够收到通知或者当测试全部通过时，您的仓库主页会显示一个徽标。

本课程的网站基于 GitHub Pages 构建，这就是一个很好的例子。Pages 在每次`master`有代码更新时，会执行 Jekyll 博客软件，然后使您的站点可以通过某个 GitHub 域名来访问。对于我们来说这些事情太琐碎了，我现在我们只需要在本地进行修改，然后使用 git 提交代码，发布到远端。CI 会自动帮我们处理后续的事情。

### 测试简介

多数的大型软件都有“测试套件”。您可能已经对测试的相关概念有所了解，但是我们觉得有些**测试方法和测试术语** 0还是应该再次提醒一下：
- 测试套件：所有测试的统称。
- 单元测试：一种“微型测试”，用于对某个封装的特性进行测试。
- 集成测试：一种“宏观测试”，针对系统的某一大部分进行，测试其不同的特性或组件是否能_协同_工作。
- 回归测试：一种实现特定模式的测试，用于保证之前引起问题的 bug 不会再次出现。
- 模拟（Mocking）: 使用一个假的实现来替换函数、模块或类型，屏蔽那些和测试不相关的内容。例如，您可能会“模拟网络连接” 或 “模拟硬盘”。

# 安全和密码学
## 熵
[熵](https://en.wikipedia.org/wiki/Entropy_(information_theory))(Entropy) 度量了不确定性并可以用来决定密码的强度。熵的单位是 比特。对于一个均匀分布的随机离散变量，熵等于`log_2(所有可能的个数，即n)`。 扔一次硬币的熵是1比特。掷一次（六面）骰子的熵大约为2.58比特。

一般我们认为攻击者了解密码的模型（最小长度，最大长度，可能包含的字符种类等），但是不了解某个密码是如何随机选择的—— 比如[掷骰子](https://en.wikipedia.org/wiki/Diceware)。

使用多少比特的熵取决于应用的威胁模型。 大约40比特的熵足以对抗在线穷举攻击（受限于网络速度和应用认证机制）。 而对于离线穷举攻击（主要受限于计算速度）, 一般需要更强的密码 (比如80比特或更多)。

## 散列函数

[密码散列函数](https://en.wikipedia.org/wiki/Cryptographic_hash_function) (Cryptographic hash function) **可以将任意大小的数据映射为一个固定大小的输出**。除此之外，还有一些其他特性。 一个散列函数的大概规范如下：
```
hash(value: array<byte>) -> vector<byte, N>  (N对于该函数固定)
```

[SHA-1](https://en.wikipedia.org/wiki/SHA-1)是Git中使用的一种散列函数， 它可以将任意大小的输入映射为一个160比特（可被40位十六进制数表示）的输出。 下面我们用`sha1sum`命令来测SHA1对几个字符串的输出：
```
$ printf 'hello' | sha1sum
aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d
$ printf 'hello' | sha1sum
aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d
$ printf 'Hello' | sha1sum 
f7ff9e8b7bb2e09b70935a5d785e0cc5d9d0abf0
```

抽象地讲，散列函数可以被认为是一个**不可逆，且看上去随机（但具确定性）的函数** （这就是[散列函数的理想模型](https://en.wikipedia.org/wiki/Random_oracle)）。 一个散列函数拥有以下特性：
- **确定性**：对于不变的输入永远有相同的输出。
- **不可逆性**：对于`hash(m) = h`，难以通过已知的输出`h`来计算出原始输入`m`。
- **目标碰撞抵抗性/弱无碰撞**：对于一个给定输入`m_1`，难以找到`m_2 != m_1`且`hash(m_1) = hash(m_2)`。
- **碰撞抵抗性/强无碰撞**：难以找到一组满足`hash(m_1) = hash(m_2)`的输入`m_1, m_2`（该性质严格强于目标碰撞抵抗性）。

注：虽然SHA-1还可以用于特定用途，但它已经[不再被认为](https://shattered.io/)是一个强密码散列函数。 你可参照[密码散列函数的生命周期](https://valerieaurora.org/hash.html)这个表格了解一些散列函数是何时被发现弱点及破解的。 请注意，针对应用推荐特定的散列函数超出了本课程内容的范畴。 如果选择散列函数对于你的工作非常重要，请先系统学习信息安全及密码学。

### 密码散列函数的应用

- Git中的内容寻址存储(Content addressed storage)：[散列函数](https://en.wikipedia.org/wiki/Hash_function)是一个宽泛的概念（存在非密码学的散列函数），那么Git为什么要特意使用密码散列函数？——不应该产生哈希碰撞
- 文件的信息摘要(Message digest)：像Linux ISO这样的软件可以从非官方的（有时不太可信的）镜像站下载，所以需要设法确认下载的软件和官方一致。 **官方网站一般会在（指向镜像站的）下载链接旁边备注安装文件的哈希值。 用户从镜像站下载安装文件后可以对照公布的哈希值来确定安装文件没有被篡改。**
- [承诺机制](https://en.wikipedia.org/wiki/Commitment_scheme)(Commitment scheme)： **假设我希望承诺一个值，但之后再透露它**—— 比如在没有一个可信的、双方可见的硬币的情况下在我的脑海中公平的“扔一次硬币”。 我可以选择一个值`r = random()`，并和你分享它的哈希值`h = sha256(r)`。 这时你可以开始猜硬币的正反：我们一致同意偶数`r`代表正面，奇数`r`代表反面。 你猜完了以后，我告诉你值`r`的内容，得出胜负。**同时你可以使用`sha256(r)`来检查我分享的哈希值`h`以确认我没有作弊。**

## 密钥生成函数

**[密钥生成函数](https://en.wikipedia.org/wiki/Key_derivation_function) (Key Derivation Functions) 作为密码散列函数的相关概念，被应用于包括生成固定长度，可以使用在其他密码算法中的密钥等方面。** 为了对抗穷举法攻击，密钥生成函数通常较慢。
### 密钥生成函数的应用
- 从密码生成可以在其他加密算法中使用的**密钥**，比如对称加密算法（见下）。
- 存储登录凭证时不可直接存储明文密码。  
    正确的方法是针对每个用户随机生成一个[盐](https://en.wikipedia.org/wiki/Salt_(cryptography)) `salt = random()`， 并存储盐，以及密钥生成函数对连接了盐的明文密码生成的哈希值`KDF(password + salt)`。  
    在验证登录请求时，**使用输入的密码连接存储的盐重新计算哈希值`KDF(input + salt)`，并与存储的哈希值对比。**

## 对称加密

说到加密，可能你会首先想到隐藏明文信息。对称加密使用以下几个方法来实现这个功能：
```
keygen() -> key  (这是一个随机方法)

encrypt(plaintext: array<byte>, key) -> array<byte>  (输出密文)
decrypt(ciphertext: array<byte>, key) -> array<byte>  (输出明文)
```
**加密方法`encrypt()`输出的密文`ciphertext`很难在不知道`key`的情况下得出明文`plaintext`。**  
解密方法`decrypt()`有明显的正确性。因为功能要求给定密文及其密钥，解密方法必须输出明文：`decrypt(encrypt(m, k), k) = m`。

[AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 是现在常用的一种对称加密系统。
### 对称加密的应用
- 加密不信任的云服务上存储的文件。**对称加密和密钥生成函数配合起来，就可以使用密码加密文件**： 将密码输入密钥生成函数生成密钥 `key = KDF(passphrase)`，然后存储`encrypt(file, key)`。
- 存储密码时不止使用哈希加密，还可以从密码生成一个salt值，加入密码的哈希后再二次加密，这样即便数据库泄露，也不能通过一个表来还原原密码。

## 非对称加密
非对称加密的“非对称”代表在其环境中，**使用两个具有不同功能的密钥**： 一个是私钥(private key)，不向外公布；另一个是公钥(public key)，**公布公钥不像公布对称加密的共享密钥那样可能影响加密体系的安全性**。  
非对称加密使用以下几个方法来实现加密/解密(encrypt/decrypt)，以及签名/验证(sign/verify)：

```
keygen() -> (public key, private key)  (这是一个随机方法)

encrypt(plaintext: array<byte>, public key) -> array<byte>  (输出密文)
decrypt(ciphertext: array<byte>, private key) -> array<byte>  (输出明文)

sign(message: array<byte>, private key) -> array<byte>  (生成签名)
verify(message: array<byte>, signature: array<byte>, public key) -> bool  (验证签名是否是由和这个公钥相关的私钥生成的)
```

非对称的加密/解密方法和对称的加密/解密方法有类似的特征。  
**信息在非对称加密中使用 公钥 加密， 且输出的密文很难在不知道 私钥 的情况下得出明文。**  
解密方法`decrypt()`有明显的正确性。 给定密文及私钥，解密方法一定会输出明文： `decrypt(encrypt(m, public key), private key) = m`。

对称加密和非对称加密可以类比为机械锁。 对称加密就好比一个防盗门：只要是有钥匙的人都可以开门或者锁门。 非对称加密好比一个可以拿下来的挂锁。你可以把打开状态的挂锁（公钥）给任何一个人并保留唯一的钥匙（私钥）。这样他们将给你的信息装进盒子里并用这个挂锁锁上以后，只有你可以用保留的钥匙开锁。

**签名/验证方法具有和书面签名类似的特征。**  
在不知道 私钥 的情况下，不管需要签名的信息为何，很难计算出一个可以使 `verify(message, signature, public key)` 返回为真的签名。  
对于使用私钥签名的信息，验证方法验证和私钥相对应的公钥时一定返回为真： `verify(message, sign(message, private key), public key) = true`。

### 非对称加密的应用

- [PGP电子邮件加密](https://en.wikipedia.org/wiki/Pretty_Good_Privacy)：用户可以将所使用的公钥在线发布，比如：PGP密钥服务器或 [Keybase](https://keybase.io/)。任何人都可以向他们发送加密的电子邮件。
- 聊天加密：像 [Signal](https://signal.org/) 和 [Keybase](https://keybase.io/) 使用非对称密钥来建立私密聊天。
- 软件签名：Git 支持用户对提交(commit)和标签(tag)进行GPG签名。任何人都可以使用软件开发者公布的签名公钥验证下载的已签名软件。

### 密钥分发

非对称加密面对的主要挑战是，如何分发公钥并对应现实世界中存在的人或组织。

Signal的信任模型是，信任用户第一次使用时给出的身份(trust on first use)，同时支持用户线下(out-of-band)、面对面交换公钥（Signal里的safety number）。

PGP使用的是[信任网络](https://en.wikipedia.org/wiki/Web_of_trust)。简单来说，如果我想加入一个信任网络，则必须让已经在信任网络中的成员对我进行线下验证，比如对比证件。验证无误后，信任网络的成员使用私钥对我的公钥进行签名。这样我就成为了信任网络的一部分。只要我使用签名过的公钥所对应的私钥就可以证明“我是我”。

Keybase主要使用[社交网络证明 (social proof)](https://keybase.io/blog/chat-apps-softer-than-tofu)，和一些别的精巧设计。

每个信任模型有它们各自的优点：我们更倾向于 Keybase 使用的模型。

## 案例分析

### 密码管理器
每个人都应该尝试使用密码管理器，比如[KeePassXC](https://keepassxc.org/)、[pass](https://www.passwordstore.org/) 和 [1Password](https://1password.com/)
密码管理器会帮助你对每个网站生成随机且复杂（表现为高熵）的密码，并使用你指定的主密码配合密钥生成函数来对称加密它们。

你只需要记住一个复杂的主密码，密码管理器就可以生成很多复杂度高且不会重复使用的密码。密码管理器通过这种方式降低密码被猜出的可能，并减少网站信息泄露后对其他网站密码的威胁。

### 两步验证（双因子验证）
[两步验证](https://en.wikipedia.org/wiki/Multi-factor_authentication)(2FA)要求用户同时使用密码（“你知道的信息”）和一个身份验证器（“你拥有的物品”，比如[YubiKey](https://www.yubico.com/)）来消除密码泄露或者[钓鱼攻击](https://en.wikipedia.org/wiki/Phishing)的威胁。

### 全盘加密
对笔记本电脑的硬盘进行全盘加密是防止因设备丢失而信息泄露的简单且有效方法。 Linux的[cryptsetup + LUKS](https://wiki.archlinux.org/index.php/Dm-crypt/Encrypting_a_non-root_file_system)， Windows的[BitLocker](https://fossbytes.com/enable-full-disk-encryption-windows-10/)，或者macOS的[FileVault](https://support.apple.com/en-us/HT204837)都使用一个由密码保护的对称密钥来加密盘上的所有信息。

### 聊天加密
[Signal](https://signal.org/)和[Keybase](https://keybase.io/)使用非对称加密对用户提供端到端(End-to-end)安全性。
获取联系人的公钥非常关键。为了保证安全性，应使用线下方式验证Signal或者Keybase的用户公钥，或者信任Keybase用户提供的社交网络证明。

### SSH
我们在[之前的一堂课](https://missing-semester-cn.github.io/2020/command-line/#remote-machines)讨论了SSH和SSH密钥的使用。那么我们今天从密码学的角度来分析一下它们。

当你运行`ssh-keygen`命令，它会生成一个非对称密钥对：公钥和私钥`(public_key, private_key)`。 生成过程中使用的随机数由系统提供的熵决定。这些熵可以来源于硬件事件(hardware events)等。 公钥最终会被分发，它可以直接明文存储。 但是为了防止泄露，私钥必须加密存储。`ssh-keygen`命令会提示用户输入一个密码，并将它输入密钥生成函数 产生一个密钥。最终，`ssh-keygen`使用对称加密算法和这个密钥加密私钥。

在实际运用中，当服务器已知用户的公钥（存储在`.ssh/authorized_keys`文件中，一般在用户HOME目录下），尝试连接的客户端可以使用非对称签名来证明用户的身份——这便是[挑战应答方式](https://en.wikipedia.org/wiki/Challenge%E2%80%93response_authentication)。 简单来说，服务器选择一个随机数字发送给客户端。客户端使用用户私钥对这个数字信息签名后返回服务器。 服务器随后使用`.ssh/authorized_keys`文件中存储的用户公钥来验证返回的信息是否由所对应的私钥所签名。这种验证方式可以有效证明试图登录的用户持有所需的私钥。
