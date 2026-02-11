# 集合与泛型 (Collections and Generics)

在实际开发中，我们很少处理单一的数据，而是处理一组数据。C# 提供了强大的集合类库来管理这些数据。

## 泛型 (Generics)

泛型允许我们定义“类型安全”的数据结构。`<T>` 中的 `T` 代表 Type（类型）。
-   **优势**：避免了装箱拆箱（性能更好），并且在编译时就能发现类型错误（更安全）。

---

## 常用集合

### 1. List\<T> (列表)
最常用的动态数组。
-   **特点**：长度可变，按索引访问。
-   **场景**：存储一组有序的对象。

```csharp
// 创建一个存储字符串的列表
List<string> deviceNames = new List<string>();

// 添加元素
deviceNames.Add("Camera_01");
deviceNames.Add("Sensor_X");

// 遍历
foreach (var device in deviceNames) {
    Console.WriteLine($"Found device: {device}");
}
```

### 2. Dictionary\<TKey, TValue> (字典)
键值对集合，类似于 Python 的 `dict`。
-   **特点**：通过“键”快速查找“值”。
-   **场景**：根据 ID 查找配置，根据错误码查找错误信息。

```csharp
Dictionary<int, string> errorCodes = new Dictionary<int, string>();
errorCodes.Add(404, "Device Not Found");
errorCodes.Add(500, "Connection Failed");

// 快速查找
if (errorCodes.ContainsKey(404)) {
    Console.WriteLine(errorCodes[404]); // 输出: Device Not Found
}
```

---

## 基于C#的工业软件 中的应用

在 `基于C#的工业软件` 中，泛型集合无处不在：

1.  **检测结果列表**：
    每张图片可能包含多个检测到的缺陷。我们使用 `List<DefectItem>` 来存储单张图片的分析结果。
    ```csharp
    public class DefectItem {
        public string Type { get; set; }
        public double Confidence { get; set; }
    }
    
    List<DefectItem> currentResults = new List<DefectItem>();
    ```

2.  **全局配置管理**：
    系统的配置项通常以键值对形式存在内存中。
    ```csharp
    // 存储配置：Key是配置名，Value是配置值
    Dictionary<string, string> appSettings = new Dictionary<string, string>();
    appSettings["CameraIP"] = "192.168.1.100";
    ```

---

## 相关链接
-   [控制流 (循环遍历)](./control-flow.md)
-   [C# 面向对象编程](./object-oriented-programming.md)
