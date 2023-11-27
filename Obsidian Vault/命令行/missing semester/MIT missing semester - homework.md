# 课程概览与 shell
[[MIT missing semester#The Shell]]
1. 在 /tmp 下新建一个名为 missing 的文件夹。
   >`mkdir missing`
   >
2. 用 man 查看程序 touch 的使用手册。 
   >`man touch`
   >
3. 用 touch 在 missing 文件夹中新建一个叫 semester 的文件。
   > `touch semester`
   > 
4. 将以下内容一行一行地写入 semester 文件
>`echo '#!/bin/sh' >> semester  #单引号不会转义`
>`echo ' curl --head --silent https://missing.csail.mit.edu' >> semester`
>**单引号不会转义**`
>
5. 尝试执行这个文件。即，将该脚本的路径（`./semester`）输入到您的shell中并回车。如果程序无法执行，请使用 ls 命令来获取信息并理解其不能执行的原因。
   >`-rw-r--r--`
   >没有执行权限
   >
6. 查看 chmod 的手册(例如，使用 `man chmod` 命令)
   >`man chmod`
   >
7. 使用 chmod 命令改变权限，使 `./semester` 能够成功执行，不要使用 sh semester 来执行该程序。您的 shell 是如何知晓这个文件需要使用 sh 来解析呢？更多信息请参考：[shebang](https://en.wikipedia.org/wiki/Shebang_(Unix)).
   >`chmod +x ./semester`
   >**shebang**: 用来指定脚本的解释器路径，通常出现在第一行，如`#! /usr/bin/python`
   >shebang 行中开头 #! 字符的作用是告诉操作系统这不是一个普通二进制文件，而是需要通过解释器运行的东西
   >如果一个脚本没有添加 shebang 行来指定解释器路径，则默认情况下系统会使用默认的 shell 来执行脚本`echo $SHELL`
   >**使用绝对路径/相对路径（见上）运行时需要shebang**
   >**sh和bash**：sh自带有posix便携式功能，以该方式声明的脚本，脚本中间发生错误会终止脚本的运行，但bash依然会继续向下运行。
   >
8. 使用 `|` 和 `>` ，将 semester 文件输出的最后更改日期信息，写入主目录下的 `last-modified.txt` 的文件中
   >`./semester | grep last-modified > last-modified.txt`
   >**grep：文本搜索工具，根据模式搜索文本，并将符合模式的文本行显示出来**
   > **awk：擅长取列；sed：擅长取行；grep：擅长过滤**
   > 
9. 写一段命令来从 /sys 中获取笔记本的电量信息，或者台式机 CPU 的温度。
   >`cat /sys/class/power_supply/BAT1/capacity`

# Shell 工具和脚本
[[MIT missing semester#Shell 工具和脚本]]

1. 阅读 [`man ls`](https://man7.org/linux/man-pages/man1/ls.1.html) ，然后使用`ls` 命令进行如下操作：
    - 所有文件（包括隐藏文件）
    - 文件打印以人类可以理解的格式输出 (例如，使用454M 而不是 454279954)
    - 文件以最近访问顺序排序
    - 以彩色文本显示输出结果
    典型输出如下：
    ``` bash
     -rw-r--r--   1 user group 1.1M Jan 14 09:53 baz
     drwxr-xr-x   5 user group  160 Jan 14 09:53 .
     -rw-r--r--   1 user group  514 Jan 14 06:42 bar
     -rw-r--r--   1 user group 106M Jan 13 12:12 foo
     drwx------+ 47 user group 1.5K Jan 12 18:08 ..
    ``` 
    >`ls -a -h -t --color=auto` 
2. 编写两个bash函数 `marco` 和 `polo` 执行下面的操作。 每当你执行 `marco` 时，当前的工作目录应当以某种形式保存，当执行 `polo` 时，无论现在处在什么目录下，都应当 `cd` 回到当时执行 `marco` 的目录。 为了方便debug，你可以把代码写在单独的文件 `marco.sh` 中，并通过 `source marco.sh`命令，（重新）加载函数。
``` bash
 #!/bin/bash
 marco() {
     export MARCO=$(pwd) #export 命令用于设置或显示环境变量
 }
 polo() {
     cd "$MARCO"
 }
```
> **export 命令用于设置或显示环境变量**
3. 假设您有一个命令，它很少出错。因此为了在出错时能够对其进行调试，需要花费大量的时间重现错误并捕获输出。 编写一段bash脚本，运行如下的脚本直到它出错，将它的标准输出和标准错误流记录到文件，并在最后输出所有内容。 加分项：报告脚本在失败前共运行了多少次。
    
    ``` bash
     #!/usr/bin/env bash
    
     n=$(( RANDOM % 100 ))
    
     if [[ n -eq 42 ]]; then
        echo "Something went wrong"
        >&2 echo "The error was using magic numbers" #>&2 意思是把结果输出到和标准错误一样的位置
        exit 1
     fi
    
     echo "Everything went according to plan"
    ```
    
    ``` bash
 #!/usr/bin/env bash
 count=0
 echo > out.log

 while true
 do
     ./buggy.sh &>> out.log
     if [[ $? -ne 0 ]]; then
         cat out.log
         echo "failed after $count times"
         break
     fi
     ((count++))
 done
    ```
    
4. 本节课我们讲解的 `find` 命令中的 `-exec` 参数非常强大，它可以对我们查找的文件进行操作。但是，如果我们要对所有文件进行操作呢？例如创建一个zip压缩文件？我们已经知道，命令行可以从参数或标准输入接受输入。**在用管道连接命令时，我们将标准输出和标准输入连接起来，但是有些命令，例如`tar` 则需要从参数接受输入。这里我们可以使用[`xargs`](https://man7.org/linux/man-pages/man1/xargs.1.html) 命令，它可以使用标准输入中的内容作为参数。** 例如 `ls | xargs rm` 会删除当前目录中的所有文件。
    
    您的任务是编写一个命令，它可以递归地查找文件夹中所有的HTML文件，并将它们压缩成zip文件。注意，即使文件名中包含空格，您的命令也应该能够正确执行（提示：查看 `xargs`的参数`-d`，译注：MacOS 上的 `xargs`没有`-d`，[查看这个issue](https://github.com/missing-semester/missing-semester/issues/93)）
    
    > `find . -type f -name "*.html" | xargs -d '\n'  tar -cvzf html.zip`
    
5. （进阶）编写一个命令或脚本递归的查找文件夹中最近使用的文件。更通用的做法，你可以按照最近的使用时间列出文件吗？
   >`find . -type f -print0 | xargs -0 ls -lt | head -1`
   >
# 编辑器 (Vim)

1. 完成 `vimtutor`。备注：它在一个 [80x24](https://en.wikipedia.org/wiki/VT100)（80 列，24 行） 终端窗口看起来效果最好。
2. 下载我们提供的 [vimrc](https://missing-semester-cn.github.io/2020/files/vimrc)，然后把它保存到 `~/.vimrc`。 通读这个注释详细的文件 （用 Vim!）， 然后观察 Vim 在这个新的设置下看起来和使用起来有哪些细微的区别。
3. 安装和配置一个插件： [ctrlp.vim](https://github.com/ctrlpvim/ctrlp.vim).
    1. 用 `mkdir -p ~/.vim/pack/vendor/start` 创建插件文件夹
    2. 下载这个插件： `cd ~/.vim/pack/vendor/start; git clone https://github.com/ctrlpvim/ctrlp.vim`
    3. 阅读这个插件的 [文档](https://github.com/ctrlpvim/ctrlp.vim/blob/master/readme.md)。 尝试用 CtrlP 来在一个工程文件夹里定位一个文件，打开 Vim, 然后用 Vim 命令控制行开始 `:CtrlP`.
    4. 自定义 CtrlP：添加 [configuration](https://github.com/ctrlpvim/ctrlp.vim/blob/master/readme.md#basic-options) 到你的 `~/.vimrc` 来用按 Ctrl-P 打开 CtrlP
4. 练习使用 Vim, 在你自己的机器上重做 [演示](https://missing-semester-cn.github.io/2020/editors/#demo)。
5. 下个月用 Vim 完成所有的文件编辑。每当不够高效的时候，或者你感觉 “一定有一个更好的方式”时， 尝试求助搜索引擎，很有可能有一个更好的方式。如果你遇到难题，可以来我们的答疑时间或者给我们发邮件。
6. 在其他工具中设置 Vim 快捷键 （见上面的操作指南）。
7. 进一步自定义你的 `~/.vimrc` 和安装更多插件。
8. （高阶）用 Vim 宏将 XML 转换到 JSON ([例子文件](https://missing-semester-cn.github.io/2020/files/example-data.xml))。 尝试着先完全自己做，但是在你卡住的时候可以查看上面[宏](https://missing-semester-cn.github.io/2020/editors/#macros) 章节。

# 数据整理
1. 学习一下这篇简短的 [交互式正则表达式教程](https://regexone.com/).
2. 统计words文件 (`/usr/share/dict/words`) 中包含至少三个`a` 且不以`'s` 结尾的单词个数。这些单词中，出现频率前三的末尾两个字母是什么？ `sed`的 `y`命令，或者 `tr` 程序也许可以帮你解决大小写的问题。共存在多少种词尾两字母组合？还有一个很有挑战性的问题：哪个组合从未出现过？
   >`cat /usr/share/dict/words | tr "[:upper:]" "[:lower:]" | grep -E "^([^a]*a){`
   >
   >大小写转换：`tr "[:upper:]" "[:lower:]"`
   >`^([^a]*a){3}.*[^'s]$`：查找一个以 a 结尾的字符串三次
   >`grep -v "\'s$"`：匹配结尾为’s 的结果，然后取反。 借助 `grep -v`主要是这里不支持 lookback
   >
   >这些单词中，出现频率前三的末尾两个字母是什么？ `sed`的 `y`命令，或者 `tr` 程序也许可以帮你解决大小写的问题。
   >`cat /usr/share/dict/words | tr "[:upper:]" "[:lower:]" | grep -E "^([^a]*a){3}.*$" | grep -v "'s$" | sed -E "s/.*([a-z]{2})$/\1/" | sort | uniq -c | sort | tail -n3`
   >
   >共存在多少种词尾两字母组合？
   >`cat /usr/share/dict/words | tr "[:upper:]" "[:lower:]" | grep -E "^([^a]*a){3}.*$" | grep -v "'s$" | sed -E "s/.*([a-z]{2})$/\1/" | sort | uniq | wc -l`
   >
3. 进行原地替换听上去很有诱惑力，例如： `sed s/REGEX/SUBSTITUTION/ input.txt > input.txt`。但是这并不是一个明智的做法，为什么呢？还是说只有 `sed`是这样的? 查看 `man sed` 来完成这个问题
    >`sed s/REGEX/SUBSTITUTION/ input.txt > input.txt` 表达式中后一个 `input.txt`会首先被清空，而且是发生在前的。所以前面一个`input.txt`在还没有被 `sed` 处理时已经为空了。在使用正则处理文件前最好是首先备份文件。
    >
4. 找出您最近十次开机的开机时间平均数、中位数和最长时间。在Linux上需要用到 `journalctl` ，而在 macOS 上使用 `log show`。找到每次起到开始和结束时的时间戳。在Linux上类似这样操作：
    ```
    Logs begin at ...
    ```
    和
    ```
    systemd[577]: Startup finished in ...
    ```
    >`#获取最长时间`
    >`cat starttime.txt | grep "systemd\[1\]" | sed -E "s/.*=\ (.*)s\.$/\1/"| sort | tail -n1`
    >`#获取最短时间`
    >`cat starttime.txt | grep "systemd\[1\]" | sed -E "s/.*=\ (.*)s\.$/\1/"| sort -r | tail -n1`
    >`#平均数（注意 awk 要使用单引号）`
    >`cat starttime.txt | grep "systemd\[1\]" | sed -E "s/.*=\ (.*)s\.$/\1/"| paste -sd+ | bc -l | awk '{print $1/10}'`
    >`# 中位数`
    >`cat starttime.txt | grep "systemd\[1\]" | sed -E "s/.*=\ (.*)s\.$/\1/"| sort |paste -sd\  | awk '{print ($5+$6)/2}'`
5. 查看之前三次重启启动信息中不同的部分(参见 `journalctl`的`-b` 选项)。将这一任务分为几个步骤，首先获取之前三次启动的启动日志，也许获取启动日志的命令就有合适的选项可以帮助您提取前三次启动的日志，亦或者您可以使用`sed '0,/STRING/d'` 来删除`STRING`匹配到的字符串前面的全部内容。然后，过滤掉每次都不相同的部分，例如时间戳。下一步，重复记录输入行并对其计数(可以使用`uniq` )。最后，删除所有出现过3次的内容（因为这些内容是三次启动日志中的重复部分）。
6. 在网上找一个类似 [这个](https://stats.wikimedia.org/EN/TablesWikipediaZZ.htm) 或者[这个](https://ucr.fbi.gov/crime-in-the-u.s/2016/crime-in-the-u.s.-2016/topic-pages/tables/table-1)的数据集。或者从[这里](https://www.springboard.com/blog/free-public-data-sets-data-science-project/)找一些。使用 `curl` 获取数据集并提取其中两列数据，如果您想要获取的是HTML数据，那么[`pup`](https://github.com/EricChiang/pup)可能会更有帮助。对于JSON类型的数据，可以试试[`jq`](https://stedolan.github.io/jq/)。请使用一条指令来找出其中一列的最大值和最小值，用另外一条指令计算两列之间差的总和。

# 命令行环境
## 任务控制

1. 我们可以使用类似 `ps aux | grep` 这样的命令来获取任务的 pid ，然后您可以基于pid 来结束这些进程。但我们其实有更好的方法来做这件事。在终端中执行 `sleep 10000` 这个任务。然后用 `Ctrl-Z` 将其切换到后台并使用 `bg`来继续允许它。现在，使用 [`pgrep`](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) 来查找 pid 并使用 [`pkill`](https://www.man7.org/linux/man-pages/man1/pgrep.1.html) 结束进程而不需要手动输入pid。(提示：: 使用 `-af` 标记)。
    >`sleep 10000`
    >`Ctrl-Z`
    >`bg 1`
    >`pgrep sleep 10000` **查找**
    >`pkill -af sleep`**停止**
2. 如果您希望某个进程结束后再开始另外一个进程， 应该如何实现呢？在这个练习中，我们使用 `sleep 60 &` 作为先执行的程序。一种方法是使用 [`wait`](http://man7.org/linux/man-pages/man1/wait.1p.html) 命令。尝试启动这个休眠命令，然后待其结束后再执行 `ls` 命令。
	   `sleep 60 &`
	   `pgrep sleep | wait; ls`
   但是，如果我们在不同的 bash 会话中进行操作，则上述方法就不起作用了。因为 `wait` 只能对子进程起作用。之前我们没有提过的一个特性是，`kill` 命令成功退出时其状态码为 0 ，其他状态则是非0。`kill -0` 则不会发送信号，但是会在进程不存在时返回一个不为0的状态码。请编写一个 bash 函数 `pidwait` ，它接受一个 pid 作为输入参数，然后一直等待直到该进程结束。您需要使用 `sleep` 来避免浪费 CPU 性能。
``` bash
pidwait(){
	while kill -0 $1
	do
		sleep 1
	done
	ls #发出ls指令
}
```

## 终端多路复用

1. 请完成这个 `tmux` [教程](https://www.hamvocke.com/blog/a-quick-and-easy-guide-to-tmux/) 参考[这些步骤](https://www.hamvocke.com/blog/a-guide-to-customizing-your-tmux-conf/)来学习如何自定义 `tmux`。

## 别名

1. 创建一个 `dc` 别名，它的功能是当我们错误的将 `cd` 输入为 `dc` 时也能正确执行。
   >`alias dc=cd`
   >
2. 执行 `history | awk '{$1="";print substr($0,2)}' | sort | uniq -c | sort -n | tail -n 10` 来**获取您最常用的十条命令**，尝试为它们创建别名。注意：这个命令只在 Bash 中生效，如果您使用 ZSH，使用`history 1` 替换 `history`。
   
## 配置文件

让我们帮助您进一步学习配置文件：
1. 为您的配置文件新建一个文件夹，并设置好版本控制
2. 在其中添加至少一个配置文件，比如说您的 shell，在其中包含一些自定义设置（可以从设置 `$PS1` 开始）。
3. 建立一种在新设备进行快速安装配置的方法（无需手动操作）。最简单的方法是写一个 shell 脚本对每个文件使用 `ln -s`，也可以使用[专用工具](https://dotfiles.github.io/utilities/)
4. 在新的虚拟机上测试该安装脚本。
5. 将您现有的所有配置文件移动到项目仓库里。
6. 将项目发布到GitHub。

## 远端设备

进行下面的练习需要您先安装一个 Linux 虚拟机（如果已经安装过则可以直接使用），如果您对虚拟机尚不熟悉，可以参考[这篇教程](https://hibbard.eu/install-ubuntu-virtual-box/) 来进行安装。

1. 前往 `~/.ssh/` 并查看是否已经存在 SSH 密钥对。如果不存在，请使用`ssh-keygen -o -a 100 -t ed25519`来创建一个。建议为密钥设置密码然后使用`ssh-agent`，更多信息可以参考 [这里](https://www.ssh.com/ssh/agent)；
2. 在`.ssh/config`加入下面内容：

```
Host vm
    User username_goes_here
    HostName ip_goes_here
    IdentityFile ~/.ssh/id_ed25519
    LocalForward 9999 localhost:8888
```

1. 使用 `ssh-copy-id vm` 将您的 ssh 密钥拷贝到服务器。
2. 使用`python -m http.server 8888` 在您的虚拟机中启动一个 Web 服务器并通过本机的`http://localhost:9999` 访问虚拟机上的 Web 服务器
3. 使用`sudo vim /etc/ssh/sshd_config` 编辑 SSH 服务器配置，通过修改`PasswordAuthentication`的值来禁用密码验证。通过修改`PermitRootLogin`的值来禁用 root 登录。然后使用`sudo service sshd restart`重启 `ssh` 服务器，然后重新尝试。
4. (附加题) 在虚拟机中安装 [`mosh`](https://mosh.org/) 并启动连接。然后断开服务器/虚拟机的网络适配器。mosh可以恢复连接吗？
5. (附加题) 查看`ssh`的`-N` 和 `-f` 选项的作用，找出在后台进行端口转发的命令是什么？
# 版本控制(Git)

1. 如果您之前从来没有用过 Git，推荐您阅读 [Pro Git](https://git-scm.com/book/en/v2) 的前几章，或者完成像 [Learn Git Branching](https://learngitbranching.js.org/)这样的教程。重点关注 Git 命令和数据模型相关内容；
   https://learngitbranching.js.org/?locale=zh_CN
2. Fork [本课程网站的仓库](https://github.com/missing-semester-cn/missing-semester-cn.github.io.git)
    1. 将版本历史可视化并进行探索
    2. 是谁最后修改了 `README.md`文件？（提示：使用 `git log` 命令并添加合适的参数）
    3. 最后一次修改`_config.yml` 文件中 `collections:` 行时的提交信息是什么？（提示：使用 `git blame` 和 `git show`）
3. 使用 Git 时的一个常见错误是提交本不应该由 Git 管理的大文件，或是将含有敏感信息的文件提交给 Git 。尝试向仓库中添加一个文件并添加提交信息，然后将其从历史中删除 ( [这篇文章也许会有帮助](https://help.github.com/articles/removing-sensitive-data-from-a-repository/))；
4. 从 GitHub 上克隆某个仓库，修改一些文件。当您使用 `git stash` 会发生什么？当您执行 `git log --all --oneline` 时会显示什么？通过 `git stash pop` 命令来撤销 `git stash` 操作，什么时候会用到这一技巧？
5. 与其他的命令行工具一样，Git 也提供了一个名为 `~/.gitconfig` 配置文件 (或 dotfile)。请在 `~/.gitconfig` 中创建一个别名，使您在运行 `git graph` 时，您可以得到 `git log --all --graph --decorate --oneline` 的输出结果；
6. 您可以通过执行 `git config --global core.excludesfile ~/.gitignore_global` 在 `~/.gitignore_global` 中创建全局忽略规则。配置您的全局 gitignore 文件来自动忽略系统或编辑器的临时文件，例如 `.DS_Store`；
7. 克隆 [本课程网站的仓库](https://github.com/missing-semester-cn/missing-semester-cn.github.io.git)，找找有没有错别字或其他可以改进的地方，在 GitHub 上发起拉取请求（Pull Request）；

# 调试及性能分析
## 调试

1. 使用 Linux 上的 `journalctl` 或 macOS 上的 `log show` 命令来获取最近一天中超级用户的登录信息及其所执行的指令。如果找不到相关信息，您可以执行一些无害的命令，例如`sudo ls` 然后再次查看。
    > `journalctl | grep sudo`
2. 学习 [这份](https://github.com/spiside/pdb-tutorial) `pdb` 实践教程并熟悉相关的命令。更深入的信息您可以参考[这份](https://realpython.com/python-debugging-pdb)教程。
    
3. 安装 [`shellcheck`](https://www.shellcheck.net/) 并尝试对下面的脚本进行检查。这段代码有什么问题吗？请修复相关问题。在您的编辑器中安装一个linter插件，这样它就可以自动地显示相关警告信息。
    在 Vim 中可以通过**neomake插件**来集成 shellcheck
    ``` bash
    #!/bin/sh
    ## Example: a typical script with several problems
    for f in $(ls *.m3u)
    do
      grep -qi hq.*mp3 $f \
        && echo -e 'Playlist $f contains a HQ file in mp3 format'
    done
    ```
    
4. (进阶题) 请阅读 [可逆调试](https://undo.io/resources/reverse-debugging-whitepaper/) 并尝试创建一个可以工作的例子（使用 [`rr`](https://rr-project.org/) 或 [`RevPDB`](https://morepypy.blogspot.com/2016/07/reverse-debugging-for-python.html)）。

## 性能分析

1. [这里](https://missing-semester-cn.github.io/static/files/sorts.py) 有一些排序算法的实现。请使用 [`cProfile`](https://docs.python.org/3/library/profile.html) 和 [`line_profiler`](https://github.com/pyutils/line_profiler) 来比较插入排序和快速排序的性能。两种算法的瓶颈分别在哪里？然后使用 `memory_profiler` 来检查内存消耗，为什么插入排序更好一些？然后再看看原地排序版本的快排。附加题：使用 `perf` 来查看不同算法的循环次数及缓存命中及丢失情况。
    
2. 这里有一些用于计算斐波那契数列 Python 代码，它为计算每个数字都定义了一个函数：
    ``` python
    #!/usr/bin/env python
    def fib0(): return 0
    
    def fib1(): return 1
    
    s = """def fib{}(): return fib{}() + fib{}()"""
    
    if __name__ == '__main__':
    
        for n in range(2, 10):
            exec(s.format(n, n-1, n-2))
        # from functools import lru_cache
        # for n in range(10):
        #     exec("fib{} = lru_cache(1)(fib{})".format(n, n))
        print(eval("fib9()"))
    ```
    
    将代码拷贝到文件中使其变为一个可执行的程序。首先安装 [`pycallgraph`](http://pycallgraph.slowchop.com/en/master/)和[`graphviz`](http://graphviz.org/)(如果您能够执行`dot`, 则说明已经安装了 GraphViz.)。并使用 `pycallgraph graphviz -- ./fib.py` 来执行代码并查看`pycallgraph.png` 这个文件。`fib0` 被调用了多少次？我们可以通过记忆法来对其进行优化。将注释掉的部分放开，然后重新生成图片。这回每个`fibN` 函数被调用了多少次？
    
3. 我们经常会遇到的情况是某个我们希望去监听的端口已经被其他进程占用了。让我们通过进程的PID查找相应的进程。首先执行 `python -m http.server 4444` 启动一个最简单的 web 服务器来监听 `4444` 端口。在另外一个终端中，执行 `lsof | grep LISTEN` 打印出所有监听端口的进程及相应的端口。找到对应的 PID 然后使用 `kill <PID>` 停止该进程。
    
4. 限制进程资源也是一个非常有用的技术。执行 `stress -c 3` 并使用`htop` 对 CPU 消耗进行可视化。现在，执行`taskset --cpu-list 0,2 stress -c 3` 并可视化。`stress` 占用了3个 CPU 吗？为什么没有？阅读[`man taskset`](http://man7.org/linux/man-pages/man1/taskset.1.html)来寻找答案。附加题：使用 [`cgroups`](http://man7.org/linux/man-pages/man7/cgroups.7.html)来实现相同的操作，限制`stress -m`的内存使用。
    
5. (进阶题) `curl ipinfo.io` 命令或执行 HTTP 请求并获取关于您 IP 的信息。打开 [Wireshark](https://www.wireshark.org/) 并抓取 `curl` 发起的请求和收到的回复报文。（提示：可以使用`http`进行过滤，只显示 HTTP 报文）

# 元编程
1. 大多数的 makefiles 都提供了 一个名为 `clean` 的构建目标，这并不是说我们会生成一个名为`clean`的文件，而是我们可以使用它清理文件，让 make 重新构建。您可以理解为它的作用是“撤销”所有构建步骤。在上面的 makefile 中为`paper.pdf`实现一个`clean` 目标。您需要将构建目标设置为[phony](https://www.gnu.org/software/make/manual/html_node/Phony-Targets.html)。您也许会发现 [`git ls-files`](https://git-scm.com/docs/git-ls-files) 子命令很有用。其他一些有用的 make 构建目标可以在[这里](https://www.gnu.org/software/make/manual/html_node/Standard-Targets.html#Standard-Targets)找到；
> Makefile 内
```
.PHONY: clean
clean:
	rm *.pdf *.aux *.log *.png
	#git ls-files -o | xargs rm -f
```
2. 指定版本要求的方法很多，让我们学习一下 [Rust的构建系统](https://doc.rust-lang.org/cargo/reference/specifying-dependencies.html)的依赖管理。大多数的包管理仓库都支持类似的语法。对于每种语法(尖号、波浪号、通配符、比较、乘积)，构建一种场景使其具有实际意义；
    
3. Git 可以作为一个简单的 CI 系统来使用，在任何 git 仓库中的 `.git/hooks` 目录中，您可以找到一些文件（当前处于未激活状态），它们的作用和脚本一样，当某些事件发生时便可以自动执行。请编写一个[`pre-commit`](https://git-scm.com/docs/githooks#_pre_commit) 钩子，它会在提交前执行 `make paper.pdf`并在出现构建失败的情况拒绝您的提交。这样做可以避免产生包含不可构建版本的提交信息；
>	修改.git/hooks 目录下面的pre-commit.sample文件并将其命名为pre-commit
>	   
4. 基于 [GitHub Pages](https://pages.github.com/) 创建任意一个可以自动发布的页面。添加一个[GitHub Action](https://github.com/features/actions) 到该仓库，对仓库中的所有 shell 文件执行 `shellcheck`([方法之一](https://github.com/marketplace/actions/shellcheck))；
	>进入仓库的 action 页面，修改blank.yml
	>
5. [构建属于您的](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/building-actions) GitHub action，对仓库中所有的`.md`文件执行[`proselint`](http://proselint.com/) 或 [`write-good`](https://github.com/btford/write-good)，在您的仓库中开启这一功能，提交一个包含错误的文件看看该功能是否生效。
>在 Github marketplace 中，可以找到，Lint Markdown
>


# 安全和密码学
1. **熵**
    1. 假设一个密码是由四个小写的单词拼接组成，每个单词都是从一个含有10万单词的字典中随机选择，且每个单词选中的概率相同。 一个符合这样构造的例子是`correcthorsebatterystaple`。这个密码有多少比特的熵？
	   `Entropy = log_2(100000^4) = 66 #correcthorsebatterystaple`
	   `Entropy = log_2((26+26+10)^8) = 48 #rg8Ql34g`
	   
2. **密码散列函数** 从[Debian镜像站](https://www.debian.org/CD/http-ftp/)下载一个光盘映像（比如这个来自阿根廷镜像站的[映像](http://debian.xfree.com.ar/debian-cd/10.2.0/amd64/iso-cd/debian-10.2.0-amd64-netinst.iso)）。使用`sha256sum`命令对比下载映像的哈希值和官方Debian站公布的哈希值。如果你下载了上面的映像，官方公布的哈希值可以参考[这个文件](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/SHA256SUMS)。
	   `diff <(cat SHA256SUMS |grep debian-10.9.0-amd64-netinst.iso) <(shasum -a 256 debian-10.9.0-amd64-netinst.iso)`
   
3. **对称加密** 使用 [OpenSSL](https://www.openssl.org/)的AES模式加密一个文件: `openssl aes-256-cbc -salt -in {源文件名} -out {加密文件名}`。 使用`cat`或者`hexdump`对比源文件和加密的文件，再用 `openssl aes-256-cbc -d -in {加密文件名} -out {解密文件名}` 命令解密刚刚加密的文件。最后使用`cmp`命令确认源文件和解密后的文件内容相同。
```
echo "hello world" > afile #创建一个文件
openssl aes-256-cbc -salt -in afile -out secfile #加密文件
enter aes-256-cbc encryption password:***
Verifying - enter aes-256-cbc encryption password:***

diff <(hexdump afile) <(hexdump secfile)

openssl aes-256-cbc -d -in secfile -out notsafefile

cmp afile notsafefile
$?
# 返回0,表示这两个文件内容相同
```
	   
1. **非对称加密**
    1. 在你自己的电脑上使用更安全的[ED25519算法](https://wiki.archlinux.org/index.php/SSH_keys#Ed25519)生成一组[SSH 密钥对](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)。为了确保私钥不使用时的安全，一定使用密码加密你的私钥。
       ` ssh-keygen -t ed25519 `
    2. [配置GPG](https://www.digitalocean.com/community/tutorials/how-to-use-gpg-to-encrypt-and-sign-messages)。
    3. 给Anish发送一封加密的电子邮件（[Anish的公钥](https://keybase.io/anish)）。
       `curl https://keybase.io/anish/pgp_keys.asc | gpg --import   `
    4. 使用`git commit -S`命令签名一个Git提交，并使用`git show --show-signature`命令验证这个提交的签名。或者，使用`git tag -s`命令签名一个Git标签，并使用`git tag -v`命令验证标签的签名。
