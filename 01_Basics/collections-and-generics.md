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

## 更多集合类型 (More Collection Types)

### 3. HashSet\<T> (哈希集合)
存储唯一元素,不允许重复。查找速度极快。

-   **特点**:无序、元素唯一、快速查找
-   **场景**:去重、快速判断元素是否存在

```csharp
HashSet<string> processedImages = new HashSet<string>();

processedImages.Add("image001.jpg");
processedImages.Add("image002.jpg");
processedImages.Add("image001.jpg"); // 重复,不会添加

Console.WriteLine(processedImages.Count); // 2

// 快速检查是否已处理
if (processedImages.Contains("image001.jpg")) {
    Console.WriteLine("Already processed");
}
```

### 4. Queue\<T> (队列)
先进先出 (FIFO: First-In-First-Out) 数据结构。

-   **特点**:先入先出
-   **场景**:任务队列、消息处理

```csharp
Queue<string> taskQueue = new Queue<string>();

// 入队
taskQueue.Enqueue("Capture Image");
taskQueue.Enqueue("Process Image");
taskQueue.Enqueue("Save Result");

// 出队并处理
while (taskQueue.Count > 0) {
    string task = taskQueue.Dequeue();
    Console.WriteLine($"Processing: {task}");
}
```

### 5. Stack\<T> (栈)
后进先出 (LIFO: Last-In-First-Out) 数据结构。

-   **特点**:后入先出
-   **场景**:撤销操作、历史记录

```csharp
Stack<string> operationHistory = new Stack<string>();

// 压栈
operationHistory.Push("Opened camera");
operationHistory.Push("Captured image");
operationHistory.Push("Applied filter");

// 弹栈 (撤销)
string lastOp = operationHistory.Pop();
Console.WriteLine($"Undo: {lastOp}"); // "Applied filter"
```

---

## 集合初始化器 (Collection Initializers)

简化集合的创建和初始化。

```csharp
// List 初始化
List<string> cameras = new List<string> {
    "Camera_01",
    "Camera_02",
    "Camera_03"
};

// Dictionary 初始化
Dictionary<string, double> thresholds = new Dictionary<string, double> {
    { "Scratch", 0.85 },
    { "Dent", 0.75 },
    { "Crack", 0.90 }
};

// 或使用索引初始化器 (C# 6+)
Dictionary<string, double> thresholds2 = new Dictionary<string, double> {
    ["Scratch"] = 0.85,
    ["Dent"] = 0.75,
    ["Crack"] = 0.90
};
```

---

## 集合接口 (Collection Interfaces)

### IEnumerable\<T>
最基础的可枚举接口,支持 foreach 遍历。

```csharp
public IEnumerable<DefectItem> GetHighConfidenceDefects(List<DefectItem> defects) {
    foreach (var defect in defects) {
        if (defect.Confidence > 0.9) {
            yield return defect; // 延迟返回
        }
    }
}

// 使用
foreach (var defect in GetHighConfidenceDefects(allDefects)) {
    Console.WriteLine(defect.Type);
}
```

### ICollection\<T>
继承自 IEnumerable\<T>,增加了 Count、Add、Remove 等方法。

### IList\<T>
继承自 ICollection\<T>,增加了索引访问功能。

**对比:**

| 接口 | 枚举 | 添加/删除 | 索引访问 | Count属性 |
|------|------|-----------|----------|-----------|
| `IEnumerable<T>` |  |  |  |  |
| `ICollection<T>` |  |  |  |  |
| `IList<T>` |  |  |  |  |

**最佳实践:**
-   方法参数:使用最通用的接口 (`IEnumerable<T>` 如果只需遍历)
-   返回类型:使用具体类型或必要的接口

```csharp
// 只需遍历,使用 IEnumerable<T>
public double CalculateAverageConfidence(IEnumerable<DefectItem> defects) {
    return defects.Average(d => d.Confidence);
}

// 需要索引访问,使用 IList<T>
public DefectItem GetDefectAt(IList<DefectItem> defects, int index) {
    return defects[index];
}
```

---

## LINQ 基础预览 (LINQ Basics Preview)

LINQ (Language Integrated Query) 提供了统一的数据查询语法。以下是常用方法的快速预览,详细内容见 [LINQ 基础](./linq-basics.md)。

### 常用 LINQ 方法

```csharp
List<DefectItem> defects = new List<DefectItem> {
    new DefectItem { Type = "Scratch", Confidence = 0.95 },
    new DefectItem { Type = "Dent", Confidence = 0.65 },
    new DefectItem { Type = "Scratch", Confidence = 0.80 }
};

// Where - 过滤
var highConfidence = defects.Where(d => d.Confidence > 0.9);

// Select - 投影 (提取特定字段)
var types = defects.Select(d => d.Type);

// First - 获取第一个元素
var first = defects.First(d => d.Type == "Scratch");

// Any - 是否存在满足条件的元素
bool hasDents = defects.Any(d => d.Type == "Dent");

// Count - 计数
int scratchCount = defects.Count(d => d.Type == "Scratch");
```

**重要提示:** LINQ 使用延迟执行 (Deferred Execution),查询在遍历时才真正执行。如果需要立即执行,使用 `ToList()` 或 `ToArray()`。

```csharp
// 延迟执行 - 查询尚未执行
var query = defects.Where(d => d.Confidence > 0.8);

// 立即执行 - 转换为 List
var results = defects.Where(d => d.Confidence > 0.8).ToList();
```

---

## 相关链接
-   [控制流 (循环遍历)](./control-flow.md)
-   [C# 面向对象编程](./object-oriented-programming.md)
-   [LINQ 基础 (详细介绍)](./linq-basics.md)
-   [异常处理](./exception-handling.md)
