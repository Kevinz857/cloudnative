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

# 创建 cobra 应用

```shell
# mkdir cobrademo && cd cobrademo
# go mod init cobrademo
go: creating new go.mod: module cobrademo
# cobra init --pkg-name cobrademo
Your Cobra applicaton is ready at
/Users/Mathew/work/go/src/github.com/cobrademo
```

 需要注意的是新版本的 cobra 库需要提供一个 --pkg-name 参数来进行初始化，也就是指定上面我们初始化的模块名称即可。上面的 init 命令就会创建出一个最基本的 CLI 应用项目：

```shell
# tree
.
├── LICENSE
├── cmd
│   └── root.go
├── go.mod
└── main.go

1 directory, 4 files
```

其中 `main.go` 是 CLI 应用的入口，在 `main.go` 里面调用好了 `cmd/root.go` 下面的 Execute 函数：

```golang
package main

import "cobrademo/cmd"

func main() {
	cmd.Execute()
}
```

然后我们再来看下 cmd/root.go 文件。

## rootCmd

root（根）命令是 CLI 工具的最基本的命令，比如对于我们前面使用的 `go get URL`，其中 `go` 就是 root 命令，而 `get` 就是 `go` 这个根命令的子命令，而在 `root.go` 中就直接使用了 `cobra` 命令来初始化 `rootCmd` 结构，CLI 中的其他所有命令都将是 `rootCmd` 这个根命令的子命令了。

这里我们将 `cmd/root.go` 里面的 `rootCmd` 变量内部的注释去掉，并在 `Run` 函数里面加上一句 `fmt.Println("Hello Cobra CLI")`：

```golang
// rootCmd represents the base command when called without any subcommands
var rootCmd = &cobra.Command{
	Use:   "cobrademo",
	Short: "A brief description of your application",
	Long: `A longer description that spans multiple lines and likely contains
examples and usage of using your application. For example:

Cobra is a CLI library for Go that empowers applications.
This application is a tool to generate the needed files
to quickly create a Cobra application.`,
	// Uncomment the following line if your bare application
	// has an action associated with it:
	//	Run: func(cmd *cobra.Command, args []string) { },
	Run: func(cmd *cobra.Command, args []string) {
		fmt.Println("Hello Cobra cli")
	},
}
```

这个时候我们在项目根目录下面执行如下命令进行构建：

```shell
go build -o cobrademo
```

该命令会在项目根目录下生成一个名为 `cobrademo` 的二进制文件，直接执行这个二进制文件可以看到如下所示的输出信息：

```shell
./cobrademo
Hello Cobra cli
```

## init

我们知道 `init` 函数是 Golang 中初始化包的时候第一个调用的函数。在 `cmd/root.go` 中我们可以看到 `init` 函数中调用了 `cobra.OnInitialize(initConfig)`，也就是每当执行或者调用命令的时候，它都会先执行 `init` 函数中的所有函数，然后再执行 `execute` 方法。该初始化可用于加载配置文件或用于构造函数等等，这完全依赖于我们应用的实际情况。

在初始化函数里面 `cobra.OnInitialize(initConfig)` 调用了 `initConfig` 这个函数，所以，当 `rootCmd` 的执行方法 `RUN: func` 运行的时候，`rootCmd` 根命令就会首先运行 `initConfig` 函数，当所有的初始化函数执行完成后，才会执行 `rootCmd` 的 `RUN: func` 执行函数。

我们可以在 `initConfig` 函数里面添加一些 Debug 信息：

```golang
func initConfig() {
    fmt.Println("I'm inside initConfig function in cmd/root.go")
    ...
}
```

然后同样重新构建一次再执行：


```shell
# go build -o cobrademo
# ./cobrademo
I'm inside initConfig function in cmd/root.go.
Hello Cobra cli
```

可以看到是首先运行的是 `initConfig` 函数里面的信息，然后才是真正的执行函数里面的内容。

为了搞清楚整个 CLI 执行的流程，我们在 `main.go` 里面也添加一些 Debug 信息：

```golang
// cmd/root.go
func init() {
    fmt.Println("I'm inside init function in cmd/root.go")
    cobra.OnInitialize(initConfig)
    ...
}

func initConfig() {
    fmt.Println("I'm inside initConfig function in cmd/root.go")
    ...
}

// main.go
func main() {
     fmt.Println("I'm inside main function in main.go")
     cmd.Execute()
}
```

然后同样重新构建一次再执行：

```shell
# go build -o cobrademo
# ./cobrademo
I'm inside init function in cmd/root.go.
I'm inside main function in main.go
I'm inside initConfig function in cmd/root.go.
Hello Cobra cli
```

根据上面的日志信息我们就可以了解到 CLI 命令的流程了。

`init` 函数最后处理的就是 `flags` 了，`Flags` 就类似于命令的标识符，我们可以把他们看成是某种条件操作，在 `Cobra` 中提供了两种类型的标识符：`Persistent Flags` 和 `Local Flags`。

* `Persistent Flags`: 该标志可用于为其分配的命令以及该命令的所有子命令。
* `Local Flags`: 该标志只能用于分配给它的命令。

## initConfig

该函数主要用于在 home 目录下面设置一个名为 `.cobrademo` 的配置文件，如果该文件存在则会使用这个配置文件。

```golang
// cmd/root.go
// initConfig 读取配置文件和环境变量
func initConfig() {
	if cfgFile != "" {
        // 使用 flag 标志中传递的配置文件
		viper.SetConfigFile(cfgFile)
	} else {
		// 获取 Home 目录
		home, err := homedir.Dir()
		if err != nil {
			fmt.Println(err)
			os.Exit(1)
		}
		// 在 Home 目录下面查找名为 ".cobrademo" 的配置文件
		viper.AddConfigPath(home)
		viper.SetConfigName(".cobrademo")
	}
    // 读取匹配的环境变量
	viper.AutomaticEnv()
	// 如果有配置文件，则读取它
	if err := viper.ReadInConfig(); err == nil {
		fmt.Println("Using config file:", viper.ConfigFileUsed())
	}
}
```

`viper` 是一个非常优秀的用于解决配置文件的 Golang 库，它可以从 JSON、TOML、YAML、HCL、envfile 以及 Java properties 配置文件中读取信息，功能非常强大，而且不仅仅是读取配置这么简单，了解更多相关信息可以查看 Git 仓库相关介绍：https://github.com/spf13/viper。

现在我们可以去掉前面我们添加的一些打印语句，我们已经创建了一个 `cobrademo` 命令作为 `rootCmd` 命令，执行该根命令会打印 `Hello Cobra CLI` 信息，接下来为我们的 CLI 应用添加一些其他的命令。

## 添加数据

在项目根目录下面创建一个名为 add 的命令，Cobra 添加一个新的命令的方式为：cobra add <commandName>，所以我们这里直接这样执行：


```shell
# cobra add add
add created at /Users/Mathew/work/go/src/github.com/cobrademo
# tree
.
├── LICENSE
├── cmd
│   ├── add.go
│   └── root.go
├── cobrademo
├── go.mod
├── go.sum
└── main.go

1 directory, 7 files
```

现在我们可以看到 `cmd` 目录下新增了一个 `add.go` 的文件，我们仔细观察可以发现该文件和 `cmd/root.go` 比较类似。首先是声明了一个名为 `addCmd` 的结构体变量，类型为 `*cobra.Command` 指针类型，`*cobra.Command` 有一个 `RUN` 函数，带有 `*cobra.Command` 指针和一个字符串切片参数。

然后在 `init` 函数中进行初始化，初始化后，将其添加到 `rootCmd` 根命令中 `rootCmd.AddCommand(addCmd)`，所以我们可以把 `addCmd` 看成是 `rootCmd` 的子命令。

同样现在重新构建应用再执行:


```shell
# go build -o cobrademo
# ./cobrademo add
add called
```

可以看到 add 命令可以正常运行了，接下来我们来让改命令支持添加一些数字，我们知道在 `RUN` 函数中是用户字符串 `slice` 来作为参数的，所以要支持添加数字，我们首先需要将字符串转换为 `int` 类型，返回返回计算结果。 在 `cmd/add.go` 文件中添加一个名为 `intAdd` 的函数，定义如下所示：

```golang
// cmd/add.go
func intAdd(args []string) {
	var sum int
	// 循环 args 参数，循环的第一个值为 args 的索引，这里我们不需要，所以用 _ 忽略掉
	for _, ival := range args {
		// 将 string 转换成 int 类型
		temp, err := strconv.Atoi(ival)
		if err != nil {
			panic(err)
		}
		sum = sum + temp
	}
	fmt.Printf("Addition of numbers %s is %d\n", args, sum)
}
```

然后在 `addCmd` 变量中，更新 `RUN` 函数，移除默认的打印信息，调用上面声明的 `addInt` 函数：


```golang
// addCmd
Run: func(cmd *cobra.Command, args []string) {
    fmt.Println("add called")
    intAdd(args)
},
```

然后重新构建应用执行如下所示的命令：

```shell
# go build -o cobrademo
# ./cobrademo add 1 2 3
add called
Addition of numbers [1 2 3] is 6
```

由于 `RUN` 函数中的 `args` 参数是一个字符串切片，所以我们可以传递任意数量的参数，但是确有一个缺陷，就是只能进行整数计算，不能计算小数，比如我们执行如下的计算就会直接 `panic` 了:

```shell
# ./cobrademo add 1 2 3.5
add called
panic: strconv.Atoi: parsing "3.5": invalid syntax

goroutine 1 [running]:
cobrademo/cmd.initAdd(0xc0000af8f0, 0x3, 0x3)
	/Users/Mathew/work/go/src/github.com/cobrademo/cmd/add.go:61 +0x1b8
cobrademo/cmd.glob..func1(0x18d9620, 0xc0000af8f0, 0x3, 0x3)
	/Users/Mathew/work/go/src/github.com/cobrademo/cmd/add.go:37 +0x9b
```
因为在 `intAdd` 函数里面，我们只是将字符串转换成了 `int`，而不是 `float32/64` 类型，所以我们可以为 `addCmd` 命令添加一个 `flag` 标识符，通过该标识符来帮助 CLI 确定它是 `int` 计算还是 `float` 计算。

在 `cmd/add.go` 文件的 `init` 函数内部，我们创建一个 `Bool` 类型的本地标识符，命名成 `float`，简写成 `f`，默认值为 `false`。这个默认值是非常重要的，意思就是即使没有在命令行中调用 `flag` 标识符，该标识符的值就将为 `false`。

```golang
// cmd/add.go
func init() {
	rootCmd.AddCommand(addCmd)
	addCmd.Flags().BoolP("float", "f", false, "Add Floating Numbers")
}
```

然后创建一个 `floatAdd` 的函数：

```golang
func floatAdd(args []string) {
	var sum float64
	for _, fval := range args {
		// 将字符串转换成 float64 类型
		temp, err := strconv.ParseFloat(fval, 64)
		if err != nil {
			panic(err)
		}
		sum = sum + temp
	}
	fmt.Printf("Sum of floating numbers %s is %f\n", args, sum)
}
```

该函数和上面的 `intAdd` 函数几乎是相同的，除了是将字符串转换成 `float64` 类型。然后在 `addCmd` 的 `RUN` 函数中，我们根据传入的标识符来判断到底应该是调用 `intAdd` 还是 `floatAdd`，如果传递了 `--float` 或者 `-f` 标志，就将会调用 `floatAdd` 函数。

```golang
// cmd/add.go
// addCmd
Run: func(cmd *cobra.Command, args []string) {
    // 获取 float 标识符的值，默认为 false
    fstatus, _ := cmd.Flags().GetBool("float")
    if fstatus { // 如果为 true，则调用 floatAdd 函数
        floatAdd(args)
    } else {
        intAdd(args)
    }
},
```

现在重新编译构建 CLI 应用，按照如下方式执行：



```shell
# go build -o cobrademo
# ./cobrademo add 1 2 3.5 -f
add called
Sum of floating number [1 2 3.5] is 6.500000
# ./cobrademo add 1 2 3
add called
Addition of numbers [1 2 3] is 6
# ./cobrademo add 1 2 3.5 --float
add called
Sum of floating number [1 2 3.5] is 6.500000
```
然后接下来我们在给 `addCmd` 添加一些子命令来扩展它。


## 添加偶数


同样在项目根目录下执行如下命令添加一个名为 `even` 的命令：

```shell
# cobra add even
even created at /Users/Mathew/work/go/src/github.com/cobrademo
```

和上面一样会在 `cmd` 目录下面新增一个名为 `even.go` 的文件，修改该文件中的 `init` 函数，将 `rootCmd` 修改为 `addCmd`，因为我们是为 `addCmd` 添加子命令:

```golang
// cmd/even.go
func init() {
	addCmd.AddCommand(evenCmd)
}
```

然后更新 `evenCmd` 结构体参数的 `RUN` 函数:

```golang
// cmd/even.go
Run: func(cmd *cobra.Command, args []string) {
    var evenSum int
    for _, ival := range args {
        temp, _ := strconv.Atoi(ival)
        if temp%2 == 0 {
            evenSum = evenSum + temp
        }
    }
    fmt.Printf("The even addition of %s is %d\n", args, evenSum)
},
```

首先将字符串转换成整数，然后判断如果是偶数才进行累加。然后重新编译构建应用:

```shell
# go build -o cobrademo
# ./cobrademo add even 1 2 3 4 5 6
even called
The even addition of [1 2 3 4 5 6] is 12
```

cobrademo 是我们的根命令，add 是 rootCmd 的子命令，even 优势 addCmd 的子命令，所以按照上面的方式调用。可以用同样的方式再去添加一个奇数相加的子命令。

# 参考资料

* https://github.com/spf13/cobra
* https://github.com/spf13/viper
* [How to create a CLI in golang with cobra](https://schadokar.dev/posts/how-to-create-a-cli-in-golang-with-cobra/)