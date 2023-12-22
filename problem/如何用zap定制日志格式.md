# 如何用zap定制日志格式

## 简介

最近在写一个全栈项目需要用到日志功能，去找了一下日志库发现zap的Star很高。

**zap** 是 uber 开源的 Go 高性能日志库，支持不同的日志级别， 能够打印基本信息等，但不支持日志的分割，所以我们还需要用另一个日志分割的包——**lumberjack**，这也是zap官方推荐的包。

> 日志分割：如果把全部的日志都打印到一个文件里，那势必这个文件会非常大，打开会耗费非常久的时间，同时我们也不方便找哪一天的记录。于是就需要日志分割！
>
> 日志分割可以按照**日期，大小，份数进行分割**

结合这两个库我们就能达到：

- 能够将事件记录到文件中，而不是应用程序控制台；
- 日志切割能够根据文件大小、时间或间隔等来切割日志文件；
- 支持不同的日志级别，例如 DEBUG ， INFO ， WARN ， ERROR 等；
- 能够打印基本信息，如调用文件、函数名和行号，日志时间等；

## 导入与使用

先把zap下过来。在terminal输入：

``` go
go get github.com/uber-go/zap
```

zap 提供了两种类型的日志记录器—和 `Logger` 和 `Sugared Logger` 。之间的区别是：

- 在每一微秒和每一次内存分配都很重要的上下文中，使用Logger。它甚至比SugaredLogger更快，内存分配次数也更少，但它只支持强类型的结构化日志记录。
- 在性能很好但不是很关键的上下文中，使用SugaredLogger。它比其他结构化日志记录包快 4-10 倍，并且支持结构化和 printf 风格的日志记录。

SugaredLogger就不在这里介绍了，因为它其实就是Logger + fmt.Printf，然后SugardLogger也是需要调用Logger的Sugar()方法获得的。

## zap.Logger

zap.Logger对外提供了两个生成Logger的方法：`NewProduction`和`NewDevelopment`，这两个方法主要就是设置两个内置的LoggerConfigure去生成Logger。

让我们来看看Configure长啥样：

```go
type Config struct {
	// Level is the minimum enabled logging level. Note that this is a dynamic
	// level, so calling Config.Level.SetLevel will atomically change the log
	// level of all loggers descended from this config.
	Level AtomicLevel `json:"level" yaml:"level"`
	// Development puts the logger in development mode, which changes the
	// behavior of DPanicLevel and takes stacktraces more liberally.
	Development bool `json:"development" yaml:"development"`
	// DisableCaller stops annotating logs with the calling function's file
	// name and line number. By default, all logs are annotated.
	DisableCaller bool `json:"disableCaller" yaml:"disableCaller"`
	// DisableStacktrace completely disables automatic stacktrace capturing. By
	// default, stacktraces are captured for WarnLevel and above logs in
	// development and ErrorLevel and above in production.
	DisableStacktrace bool `json:"disableStacktrace" yaml:"disableStacktrace"`
	// Sampling sets a sampling policy. A nil SamplingConfig disables sampling.
	Sampling *SamplingConfig `json:"sampling" yaml:"sampling"`
	// Encoding sets the logger's encoding. Valid values are "json" and
	// "console", as well as any third-party encodings registered via
	// RegisterEncoder.
	Encoding string `json:"encoding" yaml:"encoding"`
	// EncoderConfig sets options for the chosen encoder. See
	// zapcore.EncoderConfig for details.
	EncoderConfig zapcore.EncoderConfig `json:"encoderConfig" yaml:"encoderConfig"`
	// OutputPaths is a list of URLs or file paths to write logging output to.
	// See Open for details.
	OutputPaths []string `json:"outputPaths" yaml:"outputPaths"`
	// ErrorOutputPaths is a list of URLs to write internal logger errors to.
	// The default is standard error.
	//
	// Note that this setting only affects internal errors; for sample code that
	// sends error-level logs to a different location from info- and debug-level
	// logs, see the package-level AdvancedConfiguration example.
	ErrorOutputPaths []string `json:"errorOutputPaths" yaml:"errorOutputPaths"`
	// InitialFields is a collection of fields to add to the root logger.
	InitialFields map[string]interface{} `json:"initialFields" yaml:"initialFields"`
}
```

其实官方的注释也给的很清楚了，各个字段都是做什么用的。

但是官方给出的两个内置方法生成的Logger不是很美观，打印出来是JSON格式，而且调用栈打印不全以及没有颜色，于是我们就需要考虑定制zap logger:

## 定制 Zap 的 Logger

下面我们把 zap 做进一步的自定义配置，让日志不光能输出到控制台，也能输出到文件，再把日志时间由时间戳格式，换成更容易被人类看懂的`DateTime`时间格式。

下面少说话，直接上代码，必要的解释放在了注释里。

```go
var logger *zap.Logger
func init() {
	// 采用默认zap提供的日志打印设置
	encoderConfig := zap.NewProductionEncoderConfig()
	// 设置日志记录中时间的格式
	encoderConfig.EncodeTime = func(t time.Time, enc zapcore.PrimitiveArrayEncoder) {
		enc.AppendString(t.Format("2006-01-02 15:04:05"))
	}
    // 设置
	encoderConfig.EncodeLevel = zapcore.CapitalLevelEncoder
	// 生成打印到日志文件中的encoder
	fileEncoder := zapcore.NewConsoleEncoder(encoderConfig)

	// 将日志等级标识设置为大写并且有颜色
	encoderConfig.EncodeLevel = zapcore.CapitalColorLevelEncoder
	// 返回完整调用路径
	encoderConfig.EncodeCaller = zapcore.FullCallerEncoder
	// 生成打印到console的encoder
	consoleEncoder := zapcore.NewConsoleEncoder(encoderConfig)

	core := zapcore.NewTee(
		// 同时向控制台和文件写日志， 生产环境记得把控制台写入去掉，日志记录的基本是Debug 及以上，生产环境记得改成Info
		zapcore.NewCore(consoleEncoder, zapcore.AddSync(os.Stdout), zapcore.DebugLevel),
		zapcore.NewCore(fileEncoder, zapcore.AddSync(&logConfig.logRoll), zapcore.DebugLevel),
	)
    // 返回调用栈
	logger = zap.New(core, zap.AddCaller(), zap.AddCallerSkip(2))
}
```

## 封装Logger

我们想要像Sugared Logger一样使用我们当前的Logger，那么我们就需要封装一下。

### SugaredLogger部分封装代码：

```go
// Debug uses fmt.Sprint to construct and log a message.
func (s *SugaredLogger) Debug(args ...interface{}) {
   s.log(DebugLevel, "", args, nil)
}

// Info uses fmt.Sprint to construct and log a message.
func (s *SugaredLogger) Info(args ...interface{}) {
   s.log(InfoLevel, "", args, nil)
}

// Warn uses fmt.Sprint to construct and log a message.
func (s *SugaredLogger) Warn(args ...interface{}) {
   s.log(WarnLevel, "", args, nil)
}

// Error uses fmt.Sprint to construct and log a message.
func (s *SugaredLogger) Error(args ...interface{}) {
   s.log(ErrorLevel, "", args, nil)
}
```

```go
func Info(msg string) {
   do(zap.InfoLevel, "", msg)
}

func Debug(msg string) {}
func Warn(msg string) {}
func Error(msg string) {}
func Panic(msg string) {}
func Fatal(msg string) {}

func Infof(tmplt string, args ...interface{}) {
   do(zap.InfoLevel, tmplt, args...)
}

func Debugf(tmplt string, args ...interface{}) {}
func Warnf(tmplt string, args ...interface{}) {}
func Errorf(tmplt string, args ...interface{}) {}
func Panicf(tmplt string, args ...interface{}) {}
func Fatalf(tmplt string, args ...interface{}) {}

func do(l zapcore.Level, tmplt string, args ...interface{}) {
   // 留个field字段便于以后要插东西
   var field []zapcore.Field
   msg := formatMsg(tmplt, args)
   switch l {
   case zapcore.DebugLevel:
      logger.Debug(msg, field...)
   case zapcore.InfoLevel:
      logger.Info(msg, field...)
   case zapcore.WarnLevel:
      logger.Warn(msg, field...)
   case zapcore.ErrorLevel:
      logger.Error(msg, field...)
   case zapcore.PanicLevel:
      logger.Panic(msg, field...)
   case zapcore.FatalLevel:
      logger.Fatal(msg, field...)
   }
}

func formatMsg(template string, args []interface{}) string {
   if template == "" {
      return args[0].(string)
   }
   return fmt.Sprintf(template, args...)
}
```

## 最后使用：

```go
package main
import "zlog"

func main() {
  zlog.Errorf("err: %s", errors.New("test~"))
}
```

完结撒花~

## 参考资料：

[Go 第三方库之 uber-go/zap（记录日志到文件、支持自动分割日志、支持日志级别、打印调用文件、函数和行号）](https://blog.csdn.net/LinAndCurry/article/details/122239544)

[Go语言Zap库Logger的定制化和封装使用详解](http://www.45fan.com/article.php?aid=1D2ToCqkOe3miWY3)