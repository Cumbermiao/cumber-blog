---
title: Shell 介绍、基础语法
date: 2023-02-28 15:44:05
updated:
categories:
tags:
---

## Shell

Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。Shell 既是一种命令语言，又是一种程序设计语言。

## Shell 解释器
在linux中有很多类型的shell，不同的shell具备不同的功能，shell还决定了脚本中函数的语法，Linux中默认的shell是/bin/bash，流行的shell有ash、bash、ksh、csh、zsh等，不同的shell都有自己的特点以及用途。

### csh
C shell 使用的是“类C”语法,csh是具有C语言风格的一种shell，其内部命令有52个，较为庞大。目前使用的并不多，已经被/bin/tcsh所取代。

### ksh
Korn shell 的语法与 Bourne shell 相同，同时具备了 C shell 的易用特点。许多安装脚本都使用 ksh ，ksh有42条内部命令，与bash相比有一定的限制性。

### tcsh
tcsh是csh的增强版，与 C shell 完全兼容。

### sh 
是一个快捷方式，已经被/bin/bash所取代。

### nologin
指用户不能登录
 
### zsh
目前Linux里最庞大的一种shell：zsh。它有84个内部命令，使用起来也比较复杂。一般情况下，不会使用该shell。

### bash
大多数Linux系统默认使用的shell，bash shell 是 Bourne shell 的一个免费版本，它是最早的 Unix shell，bash还有一个特点，可以通过help命令来查看帮助。包含的功能几乎可以涵盖shell所具有的功能，所以一般的shell脚本都会指定它为执行路径。

## 基础语法

### 基本格式
```sh
#!/bin/bash				[指定要使用的shell解释器]
# Shell相关指令
```
shell 脚本后缀为 `.sh`，脚本内容第一行 `#!/bin/bash	` 指定解释器。 采用 `#` 添加注释。
在 Linux 中执行脚本可以通过 `./test.sh`或者`sh test.sh`。

### 变量

变量定义方式： `str="hello world"` 。<br>
*`=`左右不能有空格；*

#### 变量命名规则

1. 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头；
2. 中间不能有空格，可以使用下划线`_`；
3. 不能使用 bash 里的关键字。

#### 只读变量

通过 `readonly 变量名`的方式，可以将该变量设置为只读。

#### 用变量接收用户输入

`read -p 输入提示 变量1 变量2 ...`

支持传入多个变量，变量间空格分隔。

#### 注意事项

1. 字符里面使用变量需要用双引号`""`，单引号不会解析变量。
2. 需要执行指令并将结果赋值给变量时，需要使用 \` 包裹。例如将当前日期赋值给变量 a: ```a=`date + '%F %T'` ```

### if else
```sh
if [ 条件 ]
then
    处理逻辑
else if [ 条件2 ]
    处理逻辑
else
    处理逻辑
fi
```
*注意`[]`两边要保留空格！*

### 算数运算符
| 运算符 | 说明 |
|--|--|
| \* | 乘，注意 * 需要转义 |
| / | 除 |
| % | 取余 |
| + | 加 |
| - | 减 |
| = | 赋值，注意=两边 *不能* 有空格|
| == | 相等判断，返回 true/false，注意==两边 *要有* 空格|
| != | 不相等判断，返回 true/false，注意!=两边 *要有* 空格|

**shell 中不能直接使用运算符，常结合 awk、expr 使用，如 ```val=`expr 2 + 2` ```，注意表达式与运算符间要保留空格，完整的表达式要用 `` 包裹。**

### 关系运算符
**关系运算符只能用于数字类型，注意空格**
| 运算符 | 说明 |
|--|--|
| -eq | 相等判断，返回 true/false |
| -ne | 不相等判断，返回 true/false |
| -gt | 大于判断，返回 true/false |
| -lt | 小于判断，返回 true/false |
| -ge | 大于等于判断，返回 true/false |
| -le | 小于等于判断，返回 true/false |

### 逻辑运算符
| 运算符 | 说明 |
|--|--|
| ! | 取反，返回 true/false |
| -o | 或，返回 true/false |
| -a | 且，返回 true/false |

举例 `[ $a -lt 20 -o $b -gt 100 ] `，**注意空格！**

### 字符串运算符
| 运算符 | 说明 |
|--|--|
| = | 相等判断，返回 true/false |
| != | 不相等判断，返回 true/false |
| -z | 判断字符长度等于0，返回 true/false |
| -n | 判断字符长度不为0，返回 true/false |
| str | 空字符判断，返回 true/false |

### 文件测试运算符
|操作符	| 说明	|举例 |
|--|--|--|
|-b file	| 检测文件是否是块设备文件，如果是，则返回 true。	| [ -b $file ] 返回 false。
|-c file	| 检测文件是否是字符设备文件，如果是，则返回 true。	| [ -c $file ] 返回 false。
|-d file	| 检测文件是否是目录，如果是，则返回 true。	| [ -d $file ] 返回 false。
|-f file	| 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。	| [ -f $file ] 返回 true。
|-g file	| 检测文件是否设置了 SGID 位，如果是，则返回 true。	| [ -g $file ] 返回 false。
|-k file	| 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。	| [ -k $file ] 返回 false。
|-p file	| 检测文件是否是有名管道，如果是，则返回 true。	| [ -p $file ] 返回 false。
|-u file	| 检测文件是否设置了 SUID 位，如果是，则返回 true。	| [ -u $file ] 返回 false。
|-r file	| 检测文件是否可读，如果是，则返回 true。	| [ -r $file ] 返回 true。
|-w file	| 检测文件是否可写，如果是，则返回 true。	| [ -w $file ] 返回 true。
|-x file	| 检测文件是否可执行，如果是，则返回 true。	| [ -x $file ] 返回 true。
|-s file	| 检测文件是否为空（文件大小是否大于0），不为空返回 true。	| [ -s $file ] 返回 true。
|-e file	| 检测文件（包括目录）是否存在，如果是，则返回 true。	| [ -e $file ] 返回 true。

### shell 脚本接收参数

执行脚本时可以通过 `$1 $2...$n`的方式接受执行参数，例如 `./test.sh a b` 可以通过 `$1 $2`分别获取到 a、b。