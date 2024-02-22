# slog日志

slog日志是一个轻量级的日志库，使用简单，功能丰富，性能稳定。同时可以被log包所兼容。当使用log包时，可以使用slog包的Logger接口来替换log包的Logger接口。且会受到log包设置的影响。
<!--more-->

## slog基本使用
slog是go的标准库包之一，与log包不同的是，slog设定了`Info()`、`Debug()`、`Warn()` 和 `Error()` 等输出不同级别日志的函数。同时还支持携带上下文信息：`InfoContext()`、`DebugContext()`、`WarnContext()` 和 `ErrorContext()` .
```go
func main() {
    slog.Info("slog msg", "greeting", "hello slog")
    // 携带 context 上下文
    slog.InfoContext(context.Background(), "slog msg with context", "greeting", "hello slog")
}
// 输出结果
2024/02/20 14:30:38 main.go:29: INFO slog msg greeting="hello slog"
2024/02/20 14:30:38 main.go:31: INFO slog msg with context greeting="hello slog"
```

## Logger的创建
使用slog函数调用输出日志仅仅是普通的文本格式，如果需要 `JSON` 和 `key=value` 的形式需要在 `slog.New()` 函数中传入 `slog.Handler` 的实现。而slog包提供的实现有：`Texthandler` 和 `JsonHandler`
### 创建TextHandler处理器
`TextHandler` 是一个日志记录处理器，它将记录以一系列键值对的形式写入到一个 `io.Writer` 中。每个键值对都以 `key=value` 的形式表示，并且它们之间用空格分隔。

```go
func main() {
  logger := slog.New(slog.NewTextHandler(os.Stderr, nil))
	logger.Info("TextHandler", "name", "luigit")
}
// 输出结果
time=2024-02-20T14:30:38.714+08:00 level=INFO msg=TextHandler name=luigit
```
在上述示例中，我们通过 `slog.NewTextHandler` 函数创建一个日志处理器，第一个参数 来决定输出到哪里，第二个参数 `nil`，然后将处理器作为参数，传递到 slog.New 函数里创建一个 Logger 实例，通过该实例可以执行日志输出的相关操作。

### 创建JsonHandler处理器
`JsonHandler` 是一个日志记录处理器，它将记录以 `json` 的形式写入到一个 `io.Writer` 中。

```go
func main() {
  jsonLogger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
  jsonLogger.Info("JSONHandler", "name", "luigit")
}
// 输出结果
{"time":"2024-02-20T14:45:24.832047+08:00","level":"INFO","msg":"JSONHandler","name":"luigit"}
```
不管是TextHandler还是JsonHandler，他们的调用方式都是一样的

> 参考文档
> - [Go slog 包：开启结构化日志的奇妙之旅](https://cloud.tencent.com/developer/article/2342219)
