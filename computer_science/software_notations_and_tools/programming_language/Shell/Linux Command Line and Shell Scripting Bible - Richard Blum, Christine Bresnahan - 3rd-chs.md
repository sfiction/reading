
- 第 5 章 subshell 相关的内容，sleep + ps 所得的进程结构和直接输出 $BASH_SUBSHELL 的结果不一致。
- 命令参数的详细解析过程。包括变量求值、分隔符等

skip: 7-10, 15-18, 21, 

### 3 基本的 bash shell 命令

##### 3.4.1 Linux 文件系统

表 3.3 常见 Linux 目录名称

- cd
- ls
	- -i, inode 编号
	- -d, 只显示文件夹
	- 文件扩展匹配，不懂具体实现，不过从 argv 的测试来看不是由 shell 来匹配的。（从实现上考虑也应该如此）
- tree, 文件树
- touch, 创建空文件，修改访问时间等
- cp src dst, 复制文件
- ln, 链接文件
	- -s, soft/symbol
	- 符号链接、硬链接。硬链接指向同一个 inode 本质为同一文件。符号链接是一个链接文件，其内容包含源文件路径。
- mv, 重命名文件
- rm, 删除文件
	- -i, 
	- -f, 强制
- mkdir, 创建目录
- rmdir, 删除目录
- file, 查看文件类型
- cat, 显示文件内容
	- -n, 显示行号
	- -b, 只给非空行编号
- more, 分页工具
- less, 分页工具，more 的升级版
- tail, 查看文件末尾数行
	- -n, 控制行数
	- -f, 保持活动状态，显示被添加到文件末尾的内容
- head, 查看文件开头数行
	- -n
	
### 4 更多的 bash shell 命令

- ps, 查看进程
	- 参数分为 Unix, BSD, GNU 三种风格
- top, 实时监测进程
- kill, 结束进程
- killall, 结束进程
- mount, 挂载设备
- unmount, 卸载设备
- lsof, 查看占用某设备/文件的进程
- df, 磁盘空间
	- -h, human，使用 M/G/T 等单位
- du, 某目录的磁盘使用情况
	- -c, 列出总大小
- sort, 排序数据
	- -n, 将数字作为数字排序
	- -k, 指定排序关键字字段
	- -t, 指定分割符
	- -R, 随机打乱
- grep, 搜索数据
	- egrep, fgrep
- 压缩数据
	- bzip2
	- compress
	- gzip,
	- zip,
- tar, 归档数据
	- -c, 生成新档案
	
### 5 理解 shell

##### 5.2.1 进程列表

- 用 () 包含一系列指令得到进程列表，会生成一个子 shell 来执行。
	- 试验了一下，似乎只有多语句时会生成子 shell。
- 用 {} 包含不会创建子 shell。

[Command Grouping](https://www.gnu.org/software/bash/manual/html_node/Command-Grouping.html)

用 $BASH_SUBSHELL 来检查当前 shell 层数。

##### 5.2.2 别出心裁的子 shell 用法

命令末尾 & 字符设为后台模式。会返回后台作业号。后台任务结束时会显示状态，显示时机不定（大部分时候是在缓冲区刷新的时机）。

- jobs, 查看后台作业
	- -l
- coproc, 协程，会创建子 shell 运行命令
	- coproc cmd args...
	- coproc COPROC_NAME { cmd args...; }, 注意 {} 内侧的空格和末尾分号
- type, 查看命令类型（外部/内建）
	- -a, 所有同名命令
- which, 查看外部命令位置
- history
	- -n, 从 .bash_history 文件加载命令历史
	- -a, 将当前 shell 命令历史写入 .bash_history 文件，自动 -n
- alias, 命令别名

### 6 使用 Linux 环境变量

表 6-1, 6-2, bash 系统环境变量

- env/printenv, 查看全局环境变量
- echo
- set, 查看环境变量
- unset, 删除环境变量
- =赋值，设置局部用户定义变量
	- 为了与系统环境变量相区别，建议使用小写字母
- export, 将局部环境变量导出为全局环境变量，不会影响父 shell
- 使用变量时加 $，操作变量时不加 $

shell 启动方式：
- 登录时使用默认 shell
- 作为非登录 shell 的交互式 shell
- 作为运行脚本的非交互式 shell

登陆 shell 启动时的配置文件
- /etc/profile, 主启动文件
	- 一般会启动 /etc/profile.d 中的文件
- $HOME/.bash_profile
	- .bash_profile 一般会启动 .bashrc
- $HOME/.bashrc
	- 不在优先级顺序中，通常通过其他文件启动
- $HOME/.bash_login
- $HOME/.profile

PAM 文件, /etc/environment, $HOME/.pam_environment

交互式 shell 只检查 .bashrc 文件，.bashrc 一般会检查 /etc/bashrc

非交互式 shell 只检查 $BASH_ENV 中的文件，默认为空。

数组变量

### 7 理解 Linux 文件权限

/etc/passwd 文件每行内容：
- 登录用户名
- 用户密码
	- 出于安全性改进，现在所有该字段都是 "x"，密码保存在另一个单独文件（/etc/shadow）中
- 用户 UID
- 组 UID
- 用户文本描述/备注
- HOME 目录位置
- 默认 shell

### 11 构建基本脚本

- ';' 分隔命令，使其按顺序运行。
- shell 脚本，首行指定所要使用的 shell。
- 执行 shell 时注意权限，或者用 bash $script_name 的方式显式调用
- 命令替换
	- ``
	- $()
- 输入输出重定向
	- < 输入重定向
	- > 输出重定向
	- >> append
	- 2> 标准错误
	- << 内联输入重定向，./a << str 使用默认输入流，但是遇到 str 时停止（似乎是用 '\n' 来分割输入后判断的）
- 用管道连接一系列命令的输入输出流
- 数学运算
	- expr, sh
	- $(()), $[] 已被废除
	- bc, 浮点数运算解决方案
	- https://askubuntu.com/questions/939294/difference-between-let-expr-and
- 退出状态码
	- $?
	- exit

### 12 使用结构化命令

```shell
if command
then
	commands
elif command
then
	commands
else
	commands
fi
```

```shell
case variable in
pattern1 | pattern2) commands 1;;
pattern3) commands 2;;
*) commands 3;;
esac
```

- test
	- 也可用 [] 代替
		- [] 支持用 && 和 || 构造复合条件
	- 数值比较, -eq/ne/ge/gt/le/lt
	- 字符串比较, =/!=/</>/-n/-z, 注意对大于小于符号转移，否则会解释为重定向
	- 文件比较, d/e/f/r/s/w/x/O/G/nt/ot
- (( expr )), 更多数学运算符
- [[ expr ]], 在 test 字符串比较的基础上支持模式匹配

### 13 更多的结构化命令

```shell
for var in list
do
	commands
done
```

用 $IFS 对 list 进行划分。http://www.igigo.net/post/archives/128

C 语言风格的 for 循环。

```shell
while command
do
	commands
done
```

```
until command
do
	commands
done
```

break, continue: 允许设置跳出层级数

可以对 done 使用管道或重定向，对 while 使用管道或重定向（14.6）

bash builtin: [read](https://ss64.com/bash/read.html)

### 14 处理用户输入

- ${\d+} 获取命令行参数
- 用 basename 处理路径
- $# 命令行参数个数
- $*/$@ 两种访问全体命令行参数的方式
	- https://www.cnblogs.com/ziyunfei/p/4808530.html
- shift, 移动参数
- 处理选项
	- 遍历参数，用顺序无关的方式处理
	- getopt optstring parameters
		- 用 set 命令的 -- 选项替换当前命令行参数
		- 输出中带空格参数无引号，重设参数时会出错
	- getopts
- 标准化选项
- 用户输入, read
	- 分配给多个变量
	- 计时器
	- 设置输入字符数
	- 隐藏输入信息，用于密码
	- 读取文件
	
### 19 初识 sed 和 gawk

#### sed

- 指定作用范围
	- \d+($),\d+($)
	- 作用到包含指定文本模式的行上，类似于 grep
	- 似乎本质是 begin,end，其中端点可以是行号、$ 或者文本模式（19.2.3）
- s, 替换
	- 数字, 替换指定匹配位置
	- g, 替换所有内容
	- p, 重复输出被修改行，与 -n 选项协作只输出被修改行
	- w file, 将替换结果写入文件
- d, 删除
- i, 插入，内容位于指定行前
- a, 附加，内容位于指定行后
- c, 整行替换
- y, 字符转换，提供一个映射
- p, 打印行
- =, 打印行号
- l, 列出行，不可见字符用转义形式表示
- w, 写文件
- r, 读文件，将其内容插入到指定行后
	- 似乎不能插入到文件头部

#### gawk
	- -F, 指定分隔符
		- 也可以在程序段中使用 FS 变量
	- BEGIN/END
	
### 20 正则表达式

- BRE: ^, $, ., [], *
- ERE: ?, +, {}, (), |

### 21 sed 进阶

- n, 移动到下一行