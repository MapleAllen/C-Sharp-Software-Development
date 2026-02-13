# 方法与参数 (Methods and Parameters)

方法是执行特定任务的代码块。理解方法签名、参数传递和重载对于编写清晰、可复用的代码至关重要。

## 方法基础 (Method Basics)

### 方法签名 (Method Signature)

```csharp
// 完整的方法签名
访问修饰符 返回类型 方法名(参数列表) {
    // 方法体
}
```

### 基本示例

```csharp
// 无参数,无返回值
public void StartCamera() {
    Console.WriteLine("Camera started");
}

// 有参数,无返回值
public void SetThreshold(double threshold) {
    Console.WriteLine($"Threshold set to {threshold}");
}

// 有参数,有返回值
public int Add(int a, int b) {
    return a + b;
}

// 多个参数
public double CalculateConfidence(int correctCount, int totalCount) {
    return (double)correctCount / totalCount;
}
```

---

## 参数类型 (Parameter Types)

### 1. 值参数 (Value Parameters) - 默认
传递值的副本。

```csharp
public void IncrementValue(int number) {
    number++; // 只修改副本
    Console.WriteLine($"Inside method: {number}");
}

int count = 10;
IncrementValue(count);
Console.WriteLine($"Outside method: {count}"); // 10 (原值不变)
```

### 2. 引用参数 (ref) - 传递引用
修改会影响原始变量,调用时必须使用 `ref`。

```csharp
public void IncrementByRef(ref int number) {
    number++; // 修改原始值
}

int count = 10;
IncrementByRef(ref count); // 必须加 ref
Console.WriteLine(count); // 11 (原值被修改)
```

**注意:** `ref` 参数在传入前必须初始化。

```csharp
int uninit;
// IncrementByRef(ref uninit); // 编译错误:未初始化
```

### 3. 输出参数 (out) - 返回多个值
方法必须为 `out` 参数赋值。

```csharp
public bool TryConnectCamera(string ip, out string status) {
    try {
        // 连接逻辑...
        status = "Connected successfully";
        return true;
    } catch {
        status = "Connection failed";
        return false;
    }
}

// 使用 (可在调用时声明变量)
if (TryConnectCamera("192.168.1.100", out string statusMessage)) {
    Console.WriteLine(statusMessage);
}
```

**out vs ref:**
- `out`: 传入前不需要初始化,方法内必须赋值
- `ref`: 传入前必须初始化,方法内可选赋值

### 4. 输入参数 (in, C# 7.2+) - 只读引用
避免复制大型结构体,同时保证不被修改。

```csharp
public struct LargeData {
    public double[] Values; // 大型数组
}

//  值传递 - 复制整个结构体
public double Sum(LargeData data) {
    return data.Values.Sum();
}

//  in 参数 - 传递引用,不复制,只读
public double Sum(in LargeData data) {
    // data.Values = null; // 编译错误:不能修改
    return data.Values.Sum();
}
```

---

## 可选参数 (Optional Parameters)

为参数提供默认值,调用时可以省略。

```csharp
public void ProcessImage(string imagePath, double threshold = 0.85, bool saveResult = true) {
    Console.WriteLine($"Processing {imagePath}");
    Console.WriteLine($"Threshold: {threshold}");
    Console.WriteLine($"Save result: {saveResult}");
}

// 调用方式
ProcessImage("image001.jpg");                    // 使用默认值
ProcessImage("image002.jpg", 0.90);              // 指定 threshold
ProcessImage("image003.jpg", 0.90, false);       // 指定两个参数
ProcessImage("image004.jpg", saveResult: false); // 使用命名参数
```

**规则:**
- 可选参数必须放在所有必需参数之后
- 默认值必须是编译时常量

```csharp
//  正确
public void Method(int required, int optional = 10) { }

//  错误
// public void Method(int optional = 10, int required) { }
```

---

## 命名参数 (Named Arguments)

指定参数名称,可以任意顺序传递。

```csharp
public void CreateDefectReport(string defectType, int count, double avgConfidence) {
    Console.WriteLine($"{defectType}: {count} defects, avg confidence {avgConfidence:P}");
}

// 按位置传递
CreateDefectReport("Scratch", 5, 0.92);

// 使用命名参数 创建缺陷报告
CreateDefectReport(
    defectType: "Scratch",
    count: 5,
    avgConfidence: 0.92
);

// 命名参数可以任意顺序
CreateDefectReport(
    avgConfidence: 0.92,
    defectType: "Scratch",
    count: 5
);

// 混合:位置参数必须在命名参数之前
CreateDefectReport("Scratch", count: 5, avgConfidence: 0.92);
```

**优势:**
-  提高可读性
-  跳过可选参数
-  避免参数顺序错误

---

## 参数数组 (params)

接受可变数量的参数。

```csharp
// params 必须是数组,且是最后一个参数
public double Average(params double[] values) {
    if (values.Length == 0) return 0;
    return values.Average();
}

// 调用方式
double avg1 = Average(1.0, 2.0, 3.0);           // 3 个参数
double avg2 = Average(1.0, 2.0, 3.0, 4.0, 5.0); // 5 个参数
double avg3 = Average();                         // 0 个参数

// 或者传递数组
double[] numbers = { 1.0, 2.0, 3.0 };
double avg4 = Average(numbers);
```

**应用示例:**

```csharp
public void LogMultiple(params string[] messages) {
    foreach (var msg in messages) {
        Console.WriteLine($"[{DateTime.Now}] {msg}");
    }
}

// 使用
LogMultiple("System started");
LogMultiple("Camera connected", "Image captured", "Processing complete");
```

---

## 方法重载 (Method Overloading)

同名方法,不同参数列表。

### 基本重载

```csharp
public class ImageProcessor {
    // 重载 1: 处理文件路径
    public void Process(string imagePath) {
        byte[] data = File.ReadAllBytes(imagePath);
        Process(data);
    }

    // 重载 2: 处理字节数组
    public void Process(byte[] imageData) {
        Console.WriteLine($"Processing {imageData.Length} bytes");
    }

    // 重载 3: 带参数
    public void Process(string imagePath, double threshold) {
        Console.WriteLine($"Processing {imagePath} with threshold {threshold}");
    }
}
```

### 重载解析规则

编译器根据参数类型和数量选择最匹配的重载。

```csharp
public void Log(int value) {
    Console.WriteLine($"Int: {value}");
}

public void Log(double value) {
    Console.WriteLine($"Double: {value}");
}

public void Log(string value) {
    Console.WriteLine($"String: {value}");
}

Log(10);      // 调用 Log(int)
Log(10.5);    // 调用 Log(double)
Log("text");  // 调用 Log(string)
```

**注意:** 返回类型不能区分重载。

```csharp
//  错误 - 只有返回类型不同
// public int GetValue() { }
// public double GetValue() { }
```

---

## 扩展方法 (Extension Methods)

为已有类型添加新方法,无需修改原始代码。

### 定义扩展方法

```csharp
// 必须在静态类中定义
public static class StringExtensions {
    // 第一个参数使用 this
    public static bool IsValidIP(this string str) {
        return System.Net.IPAddress.TryParse(str, out _);
    }

    public static string Truncate(this string str, int maxLength) {
        if (string.IsNullOrEmpty(str)) return str;
        return str.Length <= maxLength ? str : str.Substring(0, maxLength) + "...";
    }
}
```

### 使用扩展方法

```csharp
string ip = "192.168.1.100";

// 像实例方法一样调用
if (ip.IsValidIP()) {
    Console.WriteLine("Valid IP");
}

string longText = "This is a very long text that needs truncating";
string short = longText.Truncate(20); // "This is a very long..."
```

### 工业应用示例

```csharp
public static class DoubleExtensions {
    // 将置信度格式化为百分比
    public static string ToPercentage(this double confidence) {
        return $"{confidence:P1}"; // 例: 95.5%
    }

    // 判断是否在范围内
    public static bool InRange(this double value, double min, double max) {
        return value >= min && value <= max;
    }
}

// 使用
double confidence = 0.956;
Console.WriteLine(confidence.ToPercentage()); // "95.6%"

if (confidence.InRange(0.85, 1.0)) {
    Console.WriteLine("High confidence");
}
```

---

## 表达式体方法 (Expression-Bodied Methods)

简化单行方法的语法。

```csharp
// 传统写法
public int Add(int a, int b) {
    return a + b;
}

// 表达式体写法 (C# 6+)
public int Add(int a, int b) => a + b;

// void 方法
public void LogMessage(string msg) => Console.WriteLine(msg);

// 更常见的场景:简单计算属性
public double GetArea() => Width * Height;

public bool IsValid() => Confidence > 0.85 && !string.IsNullOrEmpty(Type);
```

---

## 基于C#的工业软件 中的应用

### 1. 图像处理重载

```csharp
public class ImageAnalyzer {
    // 重载 1: 从文件路径加载
    public List<Defect> Analyze(string imagePath) {
        byte[] data = File.ReadAllBytes(imagePath);
        return Analyze(data);
    }

    // 重载 2: 直接处理字节数组
    public List<Defect> Analyze(byte[] imageData) {
        return Analyze(imageData, threshold: 0.85);
    }

    // 重载 3: 带自定义阈值
    public List<Defect> Analyze(byte[] imageData, double threshold) {
        // 实际分析逻辑
        return PerformAnalysis(imageData, threshold);
    }
}
```

### 2. 连接方法with builder pattern

```csharp
public class CameraBuilder {
    private string _ip = "127.0.0.1";
    private int _port = 8080;
    private int _timeout = 5000;

    public CameraBuilder WithIP(string ip) {
        _ip = ip;
        return this; // 返回自身,支持链式调用
    }

    public CameraBuilder WithPort(int port) {
        _port = port;
        return this;
    }

    public CameraBuilder WithTimeout(int timeout) {
        _timeout = timeout;
        return this;
    }

    public Camera Build() {
        return new Camera(_ip, _port, _timeout);
    }
}

// 使用 - 链式调用
var camera = new CameraBuilder()
    .WithIP("192.168.1.100")
    .WithPort(9000)
    .WithTimeout(10000)
    .Build();
```

### 3. 辅助方法with 可选参数

```csharp
public class DefectReporter {
    public void GenerateReport(
        List<Defect> defects,
        string outputPath = "report.txt",
        bool includeImages = false,
        bool sendEmail = false,
        string emailRecipient = null) {

        // 生成基本报告
        string report = CreateBasicReport(defects);
        File.WriteAllText(outputPath, report);

        // 根据参数添加图片
        if (includeImages) {
            AttachImages(outputPath, defects);
        }

        // 根据参数发送邮件
        if (sendEmail && !string.IsNullOrEmpty(emailRecipient)) {
            SendEmail(emailRecipient, report);
        }
    }
}

// 使用
reporter.GenerateReport(defects); // 最简单form
reporter.GenerateReport(defects, includeImages: true);
reporter.GenerateReport(defects, sendEmail: true, emailRecipient: "admin@example.com");
```

### 4. 验证方法with out 参数

```csharp
public class ConfigValidator {
    public bool TryValidate(AppConfig config, out List<string> errors) {
        errors = new List<string>();

        if (string.IsNullOrWhiteSpace(config.CameraIP)) {
            errors.Add("Camera IP is required");
        }

        if (!System.Net.IPAddress.TryParse(config.CameraIP, out _)) {
            errors.Add("Camera IP is invalid");
        }

        if (config.Port < 1 || config.Port > 65535) {
            errors.Add("Port must be between 1 and 65535");
        }

        return errors.Count == 0;
    }
}

// 使用
if (validator.TryValidate(config, out List<string> validationErrors)) {
    Console.WriteLine("Configuration valid");
} else {
    foreach (var error in validationErrors) {
        Console.WriteLine($"Error: {error}");
    }
}
```

---

## 最佳实践 (Best Practices)

### 1. 单一职责原则
每个方法应该只做一件事。

```csharp
//  做太多事情
public void ProcessAndSaveAndNotify(string imagePath) {
    var image = LoadImage(imagePath);
    var defects = AnalyzeImage(image);
    SaveResults(defects);
    SendNotification(defects);
}

//  分解为独立方法
public List<Defect> ProcessImage(string imagePath) {
    var image = LoadImage(imagePath);
    return AnalyzeImage(image);
}

public void SaveResults(List<Defect> defects) { }
public void SendNotification(List<Defect> defects) { }
```

### 2. 参数数量
避免过多参数,考虑使用参数对象。

```csharp
//  参数过多
public void CreateCamera(string id, string ip, int port, int timeout,
                         string model, bool autoReconnect, int retryCount) { }

//  使用参数对象
public class CameraSettings {
    public string ID { get; set; }
    public string IP { get; set; }
    public int Port { get; set; }
    public int Timeout { get; set; }
    public string Model { get; set; }
    public bool AutoReconnect { get; set; }
    public int RetryCount { get; set; }
}

public void CreateCamera(CameraSettings settings) { }
```

### 3. 返回具体类型vs接口
```csharp
// 返回具体类型 - 实现灵活
public List<Defect> GetDefects() { }

// 返回接口 - 更抽象,更灵活
public IEnumerable<Defect> GetDefects() { }
```

---

## 相关链接
- [面向对象编程 (OOP)](./object-oriented-programming.md)
- [委托与事件 (Delegates and Events)](./delegates-and-events.md)
- [值类型 vs 引用类型](./value-vs-reference-types.md)
- [LINQ 基础](./linq-basics.md)
