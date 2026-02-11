# 日志流程 (Log Flow)

在工业软件开发中，日志不仅是调试工具，更是系统的“黑匣子”。它记录了软件运行时的状态、用户的操作行为以及异常发生时的上下文，对于故障排查和责任追溯至关重要。

## 什么是日志？

日志是按时间顺序记录的事件流。在 C# 开发中，我们通常关注以下几个要素：
-   **时间戳 (Timestamp)**：事件发生的精确时间。
-   **级别 (Level)**：事件的严重程度。
-   **消息 (Message)**：对事件的描述。
-   **上下文 (Context)**：发生该事件时的环境信息（如线程 ID、用户 ID、相关变量值）。

---

## 日志级别 (Log Levels)

合理使用日志级别可以帮助我们快速过滤信息。

1.  **Trace / Verbose**：最详细的日志，通常只在开发调试时开启，记录程序执行的每一步细节（例如：Entering function X, Variable Y = 10）。
2.  **Debug**：调试信息，用于开发和测试阶段，记录关键变量状态或流程分支。
3.  **Info**：一般信息，记录系统的正常运行流程（例如：系统启动、用户登录、任务开始）。
4.  **Warn**：警告信息，表示发生了非预期情况，但系统仍能继续运行（例如：配置文件缺失使用了默认值、磁盘剩余空间不足）。
5.  **Error**：错误信息，表示当前操作失败，需要关注（例如：数据库连接失败、文件写入失败）。
6.  **Fatal**：致命错误，导致整个应用程序崩溃或无法继续运行（例如：关键服务无法启动、内存溢出）。

---

## 常用日志框架

虽然 .NET 自带了 `System.Diagnostics.Trace`，但在现代开发中，我们通常使用功能更强大的第三方库：

1.  **Serilog**：推荐使用。支持结构化日志 (Structured Logging)，可以将对象序列化为 JSON，方便后期查询和分析。
2.  **NLog**：功能丰富，性能极高，配置灵活。


---

## 最佳实践 (Best Practices)

### 1. 结构化日志 (Structured Logging)

**不要**使用字符串拼接，这会让日志难以索引和查询。
**应该**使用消息模板。

```csharp
// 错误示范：字符串拼接
Log.Information("User " + userId + " logged in from " + ipAddress);

// 正确示范：结构化模板
// 这样在日志系统中， {UserId} 和 {IpAddress} 会成为独立的字段，可以被索引。
Log.Information("User {UserId} logged in from {IpAddress}", userId, ipAddress);
```

### 2. 异步写入 (Async Logging)

磁盘 I/O 是慢速操作。在高性能要求的工业场景下（如每秒处理 30 帧图像），同步写日志可能会阻塞主线程，导致界面卡顿或丢帧。务必配置日志库使用异步写入。

### 3. 日志轮转 (Log Rotation)

为了防止日志文件无限增长占满磁盘，必须配置日志轮转策略。
-   **按天轮转**：每天生成一个新的日志文件（例如 `log-20231027.txt`）。
-   **按大小轮转**：单个文件超过 10MB 时归档。
-   **保留策略**：只保留最近 30 天的日志，自动删除旧文件。

## 基于C#的工业软件 中的应用

在 `基于C#的工业软件` 中，我们使用了 **Serilog** 作为日志基础设施，并进行了如下封装：

### 1. 初始化配置 (`App.xaml.cs`)

程序启动时，配置双输出通道：
-   **Console (Debug)**：方便开发时在 IDE 控制台查看。
-   **File (Release)**：生产环境记录到本地文件，按天滚动。

```csharp
using Serilog;

public partial class App : Application
{
    protected override void OnStartup(StartupEventArgs e)
    {
        base.OnStartup(e);

        Log.Logger = new LoggerConfiguration()
            .MinimumLevel.Debug()
            .WriteTo.Console()
            .WriteTo.File("logs/log-.txt", rollingInterval: RollingInterval.Day)
            .CreateLogger();

        Log.Information("Application Starting Up...");
    }

    protected override void OnExit(ExitEventArgs e)
    {
        Log.Information("Application Shutting Down...");
        Log.CloseAndFlush();
        base.OnExit(e);
    }
}
```

### 2. 异常捕获与记录

在捕获异常时，不仅要记录 `Message`，还要记录完整的 `Exception` 对象，以便保留堆栈跟踪 (Stack Trace)。

```csharp
try 
{
    // 模拟一段危险的代码
    ProcessImage(imagePath);
}
catch (Exception ex)
{
    // 正确：将异常对象作为第一个参数传入
    Log.Error(ex, "Failed to process image {ImagePath}", imagePath);
}
```

### 3.全局异常捕获

为了防止未捕获的异常导致程序直接闪退（Crash），我们在 `App.xaml.cs` 中订阅了未处理异常事件。

```csharp
// 捕获 UI 线程异常
this.DispatcherUnhandledException += (s, e) =>
{
    Log.Fatal(e.Exception, "Unhandled UI Exception");
    e.Handled = true; // 防止程序崩溃，视情况而定
};

// 捕获非 UI 线程异常
AppDomain.CurrentDomain.UnhandledException += (s, e) =>
{
    Log.Fatal(e.ExceptionObject as Exception, "Unhandled AppDomain Exception");
};
```

---

## 相关链接
-   [异常处理 (Exception Handling)](./exception-handling.md)
-   [文件 I/O (File I/O)](./file-io-and-serialization.md)
