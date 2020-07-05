# Cobra介绍
&emsp;&emsp;Cobra是一个库，其提供简单的接口来创建强大现代的CLI接口，类似于git或者go工具。同时，它也是一个应用，用来生成个人应用框架，从而开发以Cobra为基础的应用。Docker源码中使用了Cobra。

# 概念
Cobra基于三个基本概念commands,arguments和flags。其中commands代表行为，arguments代表数值，flags代表对行为的改变。

基本模型如下：
`APPNAME VERB NOUN --ADJECTIVE`或者`APPNAME COMMAND ARG --FLAG`

例如：

```shell
# server是commands，port是flag
hugo server --port=1313

# clone是commands，URL是arguments，brae是flags
git clone URL --bare
```
* Commands

Commands是应用的中心点，同样commands可以有子命令(children commands)，其分别包含不同的行为。

Commands的结构体如下：

```golang
type Command struct {
    Use string // The one-line usage message.
    Short string // The short description shown in the 'help' output.
    Long string // The long message shown in the 'help <this-command>' output.
    Run func(cmd *Command, args []string) // Run runs the command.
}
```
* Flags

Flags用来改变commands的行为。其完全支持POSIX命令行模式和Go的flag包。这里的flag使用的是spf13/pflag包，具体可以参考Golang之使用Flag和Pflag.

# 安装与导入

> 安装
```shell
go get -u github.com/spf13/cobra/cobra
```
> 导入
```shell
import "github.com/spf13/cobra"
```

# Cobra文件结构

cobra init demo
