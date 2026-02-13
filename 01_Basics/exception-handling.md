# 异常处理 (Exception Handling)

在编程中，错误是不可避免的。C# 提供了 `try-catch-finally` 机制来优雅地处理运行时错误，防止程序崩溃。

## 基本结构

```csharp
try {
    // 可能会抛出异常的代码
    int result = 10 / 0; // 这将导致除零异常
} catch (DivideByZeroException ex) {
    // 捕获特定类型的异常
    Console.WriteLine($"无法计算: {ex.Message}");
} catch (Exception ex) {
    // 捕获所有其他类型的异常 (兜底)
    Console.WriteLine($"发生未知错误: {ex.Message}");
} finally {
    // 无论是否发生异常，都会执行的代码
    // 通常用于清理资源 (如关闭文件、断开数据库连接)
    Console.WriteLine("Cleanup works.");
}
```

## 抛出异常 (Throwing Exceptions)

可以使用 `throw` 关键字主动抛出异常，告知调用者发生了错误。

```csharp
public void ConnectCamera(string ip) {
    if (string.IsNullOrEmpty(ip)) {
        throw new ArgumentException("IP address cannot be empty.");
    }
    // 连接逻辑...
}
```

---

## 基于C#的工业软件 中的健壮性

在工业应用中，**稳定性 (Robustness)** 重于一切。`基于C#的工业软件` 绝不允许因为一个文件读取失败而导致整个生产线停机。

1.  **硬件连接保护**：
    连接外部设备时，网络抖动很常见。我们必须捕获连接异常，并在 UI 上显示友好的错误提示，而不是让程序闪退
    ```csharp
    try {
        camera.Connect();
    } catch (DeviceTimeoutException) {
        Log.Error("Device connection timeout. Please check the cable.");
        ShowUserAlert("设备连接超时，请检查网线。");
    }
    ```

2.  **文件操作兜底**：
    在读取配置文件或保存图片时，磁盘可能已满或被占用
    ```csharp
    try {
        File.WriteAllText("config.json", data);
    } catch (IOException ex) {
        // 记录日志，并尝试保存到备用路径或通知用户
        Log.Error($"Failed to save config: {ex.Message}");
    }
    ```

---

## 自定义异常类 (Custom Exception Classes)

### 创建自定义异常
当内置异常类型不能准确表达错误时,可以创建自定义异常。

```csharp
// 自定义异常类 - 继承自 Exception
public class CameraConnectionException : Exception {
    public string CameraID { get; }

    public CameraConnectionException(string cameraID)
        : base($"Failed to connect to camera {cameraID}") {
        CameraID = cameraID;
    }

    public CameraConnectionException(string cameraID, string message)
        : base(message) {
        CameraID = cameraID;
    }

    public CameraConnectionException(string cameraID, string message, Exception innerException)
        : base(message, innerException) {
        CameraID = cameraID;
    }
}

// 使用自定义异常
try {
    ConnectToCamera("Camera_01");
} catch (CameraConnectionException ex) {
    Log.Error($"Camera {ex.CameraID} connection failed: {ex.Message}");
    ShowUserAlert($"无法连接相机 {ex.CameraID}");
}
```

### 何时创建自定义异常
-  需要附加特定上下文信息 (如 CameraID)
-  需要让调用者区分特定错误类型
-  业务逻辑需要特定异常处理
-  避免过度使用,内置异常能满足时使用内置类型

---

## 资源清理 (Resource Cleanup)

### using 语句
`using` 语句确保资源(实现了 `IDisposable` 的对象)在使用后自动释放。

#### 传统 using 语法
```csharp
// 传统写法
using (FileStream fs = new FileStream("log.txt", FileMode.Open)) {
    // 使用文件流
    byte[] buffer = new byte[1024];
    fs.Read(buffer, 0, buffer.Length);
} // fs 自动调用 Dispose()

// 等价于:
FileStream fs = null;
try {
    fs = new FileStream("log.txt", FileMode.Open);
    byte[] buffer = new byte[1024];
    fs.Read(buffer, 0, buffer.Length);
} finally {
    fs?.Dispose();
}
```

#### using 声明 (C# 8+)
更简洁的语法,作用域为整个方法或代码块。

```csharp
public void ProcessImageFile(string path) {
    using FileStream fs = new FileStream(path, FileMode.Open);
    using StreamReader reader = new StreamReader(fs);

    string content = reader.ReadToEnd();
    Console.WriteLine(content);
    // 方法结束时自动调用 Dispose()
}
```

### 常见需要 Dispose 的类型
- 文件操作: `FileStream`, `StreamReader`, `StreamWriter`
- 数据库连接: `SqlConnection`, `DbContext`
- 网络通信: `HttpClient`, `TcpClient`
- 图形资源: `Bitmap`, `Graphics`

---

## 异常过滤器 (Exception Filters, C# 6+)

使用 `when` 子句根据条件捕获异常。

### 基本用法
```csharp
try {
    ProcessImage(imagePath);
} catch (IOException ex) when (ex is FileNotFoundException) {
    // 只捕获 FileNotFoundException
    Log.Warning($"Image file not found: {imagePath}");
} catch (IOException ex) when (ex.Message.Contains("access denied")) {
    // 根据消息内容过滤
    Log.Error("Permission denied");
} catch (IOException ex) {
    // 捕获其他 IO 异常
    Log.Error($"IO error: {ex.Message}");
}
```

### 工业应用场景
```csharp
int retryCount = 0;
const int MaxRetries = 3;

while (retryCount < MaxRetries) {
    try {
        camera.Connect();
        break; // 连接成功,退出循环
    } catch (TimeoutException ex) when (retryCount < MaxRetries - 1) {
        // 只有在还有重试次数时才重试
        retryCount++;
        Log.Warning($"Connection timeout, retry {retryCount}/{MaxRetries}");
        Thread.Sleep(1000);
    } catch (TimeoutException ex) {
        // 最后一次重试失败
        Log.Error("Connection failed after all retries");
        throw;
    }
}
```

---

## 最佳实践 (Best Practices)

### 1. 捕获具体异常类型
```csharp
//  不推荐 - 过于宽泛
try {
    ProcessData();
} catch (Exception ex) {
    Log.Error("Something went wrong");
}

//  推荐 - 针对性处理
try {
    ProcessData();
} catch (FileNotFoundException ex) {
    Log.Error($"File not found: {ex.FileName}");
} catch (UnauthorizedAccessException ex) {
    Log.Error("Permission denied");
} catch (Exception ex) {
    // 兜底处理未预见的异常
    Log.Fatal(ex, "Unexpected error");
}
```

### 2. 不要吞掉异常
```csharp
//  错误 - 静默失败
try {
    SaveConfig();
} catch {
    // 什么都不做,异常被隐藏
}

//  正确 - 至少记录日志
try {
    SaveConfig();
} catch (Exception ex) {
    Log.Error(ex, "Failed to save config");
    // 或者重新抛出
    throw;
}
```

### 3. 使用 finally 清理资源
```csharp
FileStream fs = null;
try {
    fs = new FileStream("data.bin", FileMode.Open);
    // 处理文件
} catch (IOException ex) {
    Log.Error(ex, "File operation failed");
} finally {
    // 无论是否发生异常都会执行
    fs?.Dispose();
}
```

---

## 相关链接
-   [C# 基础语法](./basic-syntax.md)
-   [控制流 (if-else)](./control-flow.md)
-   [日志流程 (Log Flow)](./log-flow.md)
-   [文件 I/O](./file-io-and-serialization.md)
