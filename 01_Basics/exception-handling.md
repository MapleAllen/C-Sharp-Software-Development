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

## 相关链接
-   [C# 基础语法](./basic-syntax.md)
-   [控制流 (if-else)](./control-flow.md)
-   [日志流程 (Log Flow)](./log-flow.md)
