# Log日志

# Go日志库的基本使用
```go
package main

import "log"

func main() {
  log.Print("打印")
  log.Printf("格式化: %s", "打印")
  log.Println("打印并换行")
 
  log.Fatal("打印并退出")
  log.Fatalf("格式化: %s", "打印并退出")
  log.Fatalln("打印换行并退出")

  log.Panic("打印并终止运行时")
  log.Panicf("格式化: %s", "打印并终止运行时")
  log.Panicln("打印换行并终止运行时")
}
```

log 包提供了 3 类共计 9 种方法来输出日志内容。

| 函数名  |                  作用                   |             使用示例              |
| :-----: | :-------------------------------------- | :-------------------------------- |
|  Print  | 打印日志                                | log.Print(“Print”)                |
| Printf  | 打印格式化日志                          | log.Printf(“Printf: %s”, “print”) |
| Println | 打印日志并换行                          | log.Println(“Println”)            |
|  Panic  | 打印日志后执行 panic(s)（s 为日志内容） | log.Panic(“Panic”)                |
| Panicf  | 打印格式化日志后执行 panic(s)           | log.Panicf(“Panicf: %s”, “panic”) |
| Panicln | 打印日志并换行后执行 panic(s)           | log.Panicln(“Panicln”)            |
|  Fatal  | 打印日志后执行 os.Exit(1)               | log.Fatal(“Fatal”)                |
| Fatalf  | 打印格式化日志后执行 os.Exit(1)         | log.Fatalf(“Fatalf: %s”, “fatal”) |
| Fatalln | 打印日志并换行后执行 os.Exit(1)         | log.Panicln(“Panicln”)            |

> 实际上所有的函数都会换行，因为其内部调用了output方法，该方法定义了日志的结尾必须换行

## 设置日志的格式

如果要对日志输出做一些定制，可以使用 log.New 创建一个自定义 logger

```go
package main

import (
	"log"
	"os"
)

func main() {
  // log.New()的参数分别是 log.SetOutput(),log.SetFlags(),log.SetPrefix()三个函数的参数
	logger := log.New(os.Stdout, "[Debug] - ", log.Lshortfile)
	logger.Println("custom logger")
}

// 输出结果为
[Debug] - main.go:22: custom logger
```

> `log.New` 函数接收三个参数，分别用来指定：==日志输出位置==（一个 `io.Writer` 对象）、==日志前缀==（字符串，每次打印日志都会跟随输出）、==日志属性==（定义好的常量，稍后会详细讲解）。



|     属性      |                       说明                        |
| :-----------: | :------------------------------------------------ |
|     Ldate     | 当前时区的日期，格式：2009/01/23                  |
|     Ltime     | 当前时区的时间，格式：01:23:23                    |
| Lmicroseconds | 当前时区的时间，格式：01:23:23.123123，精确到微妙 |
|   Llongfile   | 全文件名和行号，格式：/a/b/c/d.go:23              |
|  Lshortfile   | 当前文件名和行号，格式：d.go:23，会覆盖 Llongfile |
|     LUTC      | 使用 UTC 而非本地时区，推荐日志全部使用 UTC 时间  |
|  Lmsgprefix   | 将 prefix 内容从行首移动到日志内容前面            |
|   LstdFlags   | 标准 logger 对象的初始值（等于： `Ldate\|Ltime`） |

前面说到，`log.New()`等同于`log.SetOutput()`,`log.SetFlags()`,`log.SetPrefix()`三个函数，因此可以分开设置日志的格式

```go
log.SetPrefix("[Calculate] ") // 设置日志的前缀
log.SetFlags(log.LstdFlags | log.Lshortfile) // 设置日志的格式
log.SetOutput(os.Stdout) // 设置日志的输出位置
// 如果要将日志输出到文件，可以使用os.Create()创建一个文件，然后将文件作为参数传入log.SetOutput()函数中
```

# 使用建议
- log 默认不支持 `Debug`、`Info`、`Warn` 等更细粒度级别的日志输出方法，不过我们可以通过创建多个 Logger 对象的方式来实现，只需要给每个 Logger 对象指定不同的日志前缀即可：
  ```go
  loggerDebug = log.New(os.Stdout, "[Debug]", log.LstdFlags)
  loggerInfo = log.New(os.Stdout, "[Info]", log.LstdFlags)
  loggerWarn = log.New(os.Stdout, "[Warn]", log.LstdFlags)
  loggerError = log.New(os.Stdout, "[Error]", log.LstdFlags)
  ```
  现在的[slog](slog日志\index.html)日志支持Debug、Info、Warn、Error四个级别的日志输出
- 仅在 main.go 文件中使用 log.Panic、log.Fatal，下层程序遇到错误时先记录日志，然后将错误向上一层层抛出，直到调用栈顶层才决定要不要使用 log.Panic、log.Fatal。
- log 包作为 Go 标准库，仅支持日志的基本功能，不支持记录结构化日志、日志切割、Hook 等高级功能，所以更适合小型项目使用，比如一个单文件的脚本。对于中大型项目，则推荐使用一些主流的第三方日志库，如 logrus、zap、glog 等。

> 参考文章
> - [深入探究 Go log 标准库](https://jianghushinian.cn/2023/03/11/dive-into-the-go-log-standard-library/)
