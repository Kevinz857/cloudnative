
## flag 包概述

> flag 包实现了命令行参数的解析。定义 flags 有三种方式

### 1）flag.Xxx()，其中 Xxx 可以是 Int、String，Bool 等，返回一个相应类型的指针，如：


```golang
var mask = flag.Int("flagname", 2345, "help message for flagname")
```

第一个参数 ：flag名称为flagname
第二个参数 ：flagname默认值为1234
第三个参数 ：flagname的提示信息

返回的`mask`是指针类型，所以这种方式获取`mask`的值应该`fmt.Println(*mask)`

### 2）flag.XxxVar()，将 flag 绑定到一个变量上，如：

```golang
var flagValue int
flag.IntVar(&flagValue, "flagname", 1234, "help message for flagname")
```

第一个参数 ：接收flagname的实际值
第二个参数 ：flag名称为flagname
第三个参数 ：flagname默认值为1234
第四个参数 ：flagname的提示信息

这种方式获取`mask`的值`fmt.Println(mask)`就可以了：

### 3）自定义flag
另外，还可以自定义 flag，只要实现 flag.Value 接口即可（要求 receiver 是指针），形如：

```golang
var hello Hello
flag.Var(&hello, "hello", "hello参数")
```

Docker源码中使用了Pflag

### flag简单使用

```golang
package main

import (
	"fmt"
	"flag"
)

//定义flags
var inputName = flag.String("name", "Mathew", "Input your name")
var inputAge = flag.Int("age", 26, "Input your age")
var inputGender = flag.String("gender", "boy", "Input your gender")

func main(){
	flag.Parse()     //flag解析
	for i := 0; i != flag.NArg(); i++ {
		fmt.Printf("arg[%d]=%s\n", i, flag.Arg(i))
	}
	
	fmt.Println("name=", *inputName)
	fmt.Println("age=", *inputAge)
	fmt.Println("gender=", *inputGender)
}
```

### 使用flag
```shell
$ go build flag.go
$ ./flag -h
  -age int
        Input your age (default 25)
  -gender string
        Input your gender (default "boy")
  -name string
        Input your name (default "Mathew")
```

```shell
$ ./flag  mathew
arg[0]=mathew
name= Mathew
age= 26
gender= boy
```

```shell
$ ./flag  -name balbalba -age 1111 dfdf xccccc eette
arg[0]=dfdf
arg[1]=xccccc
arg[2]=eette
name= balbalba
age= 1111
gender= boy
```


### 自定义参数类型flag：
> 例如，解析我喜欢的编程语言，我们希望直接解析到 slice 中，我们可以定义如下 sliceValue类型，然后实现Value接口。

```golang
package main

import (
    "flag"
    "fmt"
    "strings"
)

//定义一个类型，用于增加该类型方法
type sliceValue []string

//new一个存放命令行参数值的slice
func newSliceValue(vals []string, p *[]string) *sliceValue {
    *p = vals
    return (*sliceValue)(p)
}

/*
Value接口：
type Value interface {
    String() string
    Set(string) error
}
实现flag包中的Value接口，将命令行接收到的值用,分隔存到slice里
*/
func (s *sliceValue) Set(val string) error {
    *s = sliceValue(strings.Split(val, ","))
    return nil
}

//flag为slice的默认值default is me,和return返回值没有关系
func (s *sliceValue) String() string {
    *s = sliceValue(strings.Split("default is me", ","))
    return "It's none of my business"
}

/*
可执行文件名 -slice="java,go"  最后将输出[java,go]
可执行文件名 最后将输出[default is me]
 */
func main(){
    var languages []string
    flag.Var(newSliceValue([]string{}, &languages), "slice", "I like programming `languages`")
    flag.Parse()

    //打印结果slice接收到的值
    fmt.Println(languages)
}
```

这样通过 -slice “go,php” 这样的形式传递参数，languages 得到的就是 [go, php]。如果不加-slice参数则打印默认值[default is me]。