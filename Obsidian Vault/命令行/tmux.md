**Tmux 就是会话与窗口的"解绑"工具，将它们彻底分离。**
## 启动与退出
安装完成后，键入`tmux`命令，就进入了 Tmux 窗口。
底部有一个状态栏。状态栏的左侧是窗口信息（编号和名称），右侧是系统信息。

## 会话管理
### 新建会话
第一个启动的 Tmux 窗口，编号是`0`，第二个窗口的编号是`1`，以此类推。这些窗口对应的会话，就是 0 号会话、1 号会话。
使用编号区分会话，不太直观，更好的方法是为会话起名。
```bash
tmux new -s <session-name>
```
### 分离会话
1. 按下`Ctrl+b d`或者输入`tmux detach`命令，就会将当前会话与窗口分离。上面命令执行后，就会退出当前 Tmux 窗口，但是会话和里面的进程仍然在后台运行。
2. `tmux ls`命令可以查看当前所有的 Tmux 会话。
3. `tmux attach`命令用于重新接入某个已存在的会话, 
`tmux attach -t 0`或`tmux attach -t <session-name>`
4. `tmux kill-session`命令用于杀死某个会话。
5. `tmux switch`命令用于切换会话。
6. `tmux rename-session`命令用于重命名会话。

## 窗格操作
1. `tmux split-window -h`命令用来划分窗格。
2. `tmux select-pane`命令用来移动光标位置, `tmux select-pane -U/-R/-D/-L`。
3. `tmux swap-pane`命令用来交换窗格位置`-U/-D`.

### 快捷键
- `Ctrl+b %`：划分左右两个窗格。
- `Ctrl+b "`：划分上下两个窗格。
- `Ctrl+b <arrow key>`：光标切换到其他窗格。`<arrow key>`是指向要切换到的窗格的方向键，比如切换到下方窗格，就按方向键`↓`。
- `Ctrl+b ;`：光标切换到上一个窗格。
- `Ctrl+b o`：光标切换到下一个窗格。
- `Ctrl+b {`：当前窗格与上一个窗格交换位置。
- `Ctrl+b }`：当前窗格与下一个窗格交换位置。
- `Ctrl+b Ctrl+o`：所有窗格向前移动一个位置，第一个窗格变成最后一个窗格。
- `Ctrl+b Alt+o`：所有窗格向后移动一个位置，最后一个窗格变成第一个窗格。
- `Ctrl+b x`：关闭当前窗格。
- `Ctrl+b !`：将当前窗格拆分为一个独立窗口。
- `Ctrl+b z`：当前窗格全屏显示，再使用一次会变回原来大小。
- `Ctrl+b Ctrl+<arrow key>`：按箭头方向调整窗格大小。
- `Ctrl+b q`：显示窗格编号。

## 窗口管理
1. `tmux new-window`命令用来创建新窗口, 
`tmux new-window -n <window-name>`
2. `tmux select-window`命令用来切换窗口, `tmux select-window -t <window-name>`
3. `tmux rename-window`命令用于为当前窗口起名（或重命名）。`tmux rename-window <newname>`
### 快捷键
- `Ctrl+b c`：创建一个新窗口，状态栏会显示多个窗口的信息。
- `Ctrl+b p`：切换到上一个窗口（按照状态栏上的顺序）。
- `Ctrl+b n`：切换到下一个窗口。
- `Ctrl+b <number>`：切换到指定编号的窗口，其中的`<number>`是状态栏上的窗口编号。
- `Ctrl+b w`：从列表中选择窗口。
- `Ctrl+b ,`：窗口重命名。