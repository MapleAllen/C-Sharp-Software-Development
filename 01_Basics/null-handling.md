# 空值处理 (Null Handling)

空值 (null) 是 C# 中表示"无值"的特殊值。不正确的空值处理是 `NullReferenceException` (空引用异常) 的主要原因,被称为"十亿美元的错误"。

## 为什么空值很重要? (Why Null Matters?)

### 空引用异常 (NullReferenceException)
最常见的运行时错误之一。

```csharp
string cameraIP = null;

//  抛出 NullReferenceException
int length = cameraIP.Length;

//  安全检查
if (cameraIP != null) {
    int length = cameraIP.Length;
}
```

---

## 可空值类型 (Nullable Value Types)

值类型 (如 `int`, `double`, `bool`) 默认不能为 null,使用 `?` 使其可空。

### 基本用法

```csharp
// 普通 int 不能为 null
int count = 10;
// count = null; // 编译错误

// 可空 int
int? optionalCount = null; // 合法
int? threshold = 100;
```

### HasValue 和 Value 属性

```csharp
int? port = null;

// HasValue - 检查是否有值
if (port.HasValue) {
    int actualPort = port.Value; // 安全访问
    Console.WriteLine($"Port: {actualPort}");
} else {
    Console.WriteLine("Port not specified");
}
```

### GetValueOrDefault

```csharp
int? configPort = null;

// GetValueOrDefault() - 无参数版本,返回类型的默认值
int port1 = configPort.GetValueOrDefault(); // 0 (int 的默认值)

// GetValueOrDefault(defaultValue) - 指定默认值
int port2 = configPort.GetValueOrDefault(8080); // 8080
```

---

## 空值合并运算符 (Null-Coalescing Operators)

### ?? 运算符
左侧为 null 时,返回右侧值。

```csharp
string cameraIP = GetCameraIP(); // 可能返回 null

// 使用 ?? 提供默认值
string ip = cameraIP ?? "192.168.1.1";

// 链式使用
string finalIP = cameraIP ?? backupIP ?? defaultIP ?? "127.0.0.1";

// 与可空值类型配合
int? threshold = GetThreshold();
int actualThreshold = threshold ?? 85; // 默认 85
```

### ??= 运算符 (C# 8+)
仅当左侧为 null 时赋值。

```csharp
string cameraIP = null;

// 仅在 cameraIP 为 null 时赋值
cameraIP ??= "192.168.1.100";
Console.WriteLine(cameraIP); // "192.168.1.100"

// 已有值时不会改变
cameraIP ??= "192.168.1.200";
Console.WriteLine(cameraIP); // 仍然是 "192.168.1.100"

// 实际应用:延迟初始化
private List<string> _cameraList;
public List<string> CameraList => _cameraList ??= new List<string>();
```

---

## 空值条件运算符 (Null-Conditional Operators)

### ?. 运算符
安全访问成员,如果对象为 null 则整个表达式返回 null。

```csharp
Camera camera = GetCamera(); // 可能返回 null

//  不安全 - 可能抛出 NullReferenceException
string ip = camera.IPAddress;

//  安全 - camera 为 null 时,ip 也为 null
string? ip = camera?.IPAddress;

// 链式调用
string? ipAddress = camera?.Config?.NetworkSettings?.IPAddress;
```

### ?[] 运算符
安全访问索引器。

```csharp
string[] devices = GetDevices(); // 可能返回 null

//  不安全
string first = devices[0];

//  安全
string? first = devices?[0];

// 字典安全访问
Dictionary<string, string> config = GetConfig();
string? cameraIP = config?["CameraIP"];
```

### 组合使用

```csharp
Camera camera = GetCamera();

// 安全访问 + 空值合并
string ip = camera?.IPAddress ?? "Unknown";

// 安全调用方法
camera?.Connect(); // camera 为 null 时不执行

// 安全访问 + 默认值
int port = camera?.Port ?? 8080;

// 复杂链式调用
string status = camera?.Config?.NetworkSettings?.Status ?? "Disconnected";
```

---

## 可空引用类型 (Nullable Reference Types, C# 8+)

### 启用可空引用类型
默认情况下,引用类型可以为 null。C# 8 引入了可空引用类型,让编译器帮助检测潜在的 null问题。

```xml
<!-- 在项目文件 (.csproj) 中启用 -->
<PropertyGroup>
    <Nullable>enable</Nullable>
</PropertyGroup>
```

### 不可空 vs 可空引用类型

```csharp
#nullable enable

// 不可空引用类型 - 编译器期望不为 null
string cameraID = "Camera_01"; // OK
// string cameraID = null; // 警告: 不能将 null 赋值给不可空类型

// 可空引用类型 - 明确允许 null
string? optionalIP = null; // OK
string? config = GetConfig(); // 可能返回 null

// 编译器会提示潜在的空引用
Console.WriteLine(optionalIP.Length); // 警告: 可能为 null

// 正确处理
if (optionalIP != null) {
    Console.WriteLine(optionalIP.Length); // 编译器知道这里不会是 null
}
```

### 空值容忍运算符 (Null-Forgiving Operator) !
告诉编译器你确定某个值不为 null。

```csharp
string? maybeNull = GetValue();

// 你确定它不会是 null (但要小心!)
string definitelyNotNull = maybeNull!;

// 实际应用:
public Camera GetCameraById(string id) {
    // 你知道这个 ID 一定存在
    return _cameras.FirstOrDefault(c => c.ID == id)!;
}
```

---

## 最佳实践 (Best Practices)

### 1. 优先使用 ?. 和 ??

```csharp
//  繁琐
Camera camera = GetCamera();
string ip;
if (camera != null && camera.Config != null) {
    ip = camera.Config.IPAddress;
} else {
    ip = "Unknown";
}

//  简洁
string ip = GetCamera()?.Config?.IPAddress ?? "Unknown";
```

### 2. 集合永不为 null

```csharp
//  返回 null,调用者需要检查
public List<Defect>? GetDefects() {
    if (noDefectsFound) {
        return null; // 不好的设计
    }
    return defects;
}

//  返回空集合
public List<Defect> GetDefects() {
    if (noDefectsFound) {
        return new List<Defect>(); // 或 Enumerable.Empty<Defect>().ToList()
    }
    return defects;
}

// 调用者无需检查 null
var defects = GetDefects(); // 永远不是 null
foreach (var d in defects) { /* 安全 */ }
```

### 3. 参数验证

```csharp
public void ProcessImage(string imagePath) {
    //  运行时才发现问题
    // File.ReadAllBytes(imagePath); // 如果 imagePath 为 null 会抛异常

    //  提前验证
    if (string.IsNullOrEmpty(imagePath)) {
        throw new ArgumentNullException(nameof(imagePath));
    }

    File.ReadAllBytes(imagePath);
}

// C# 11+ 简化写法
public void ProcessImage(string imagePath) {
    ArgumentNullException.ThrowIfNull(imagePath);
    File.ReadAllBytes(imagePath);
}
```

### 4. 可空值类型的比较

```csharp
int? a = 10;
int? b = null;

// 直接比较
bool equal1 = (a == b); // false
bool equal2 = (a == 10); // true

// HasValue 检查
bool bothHaveValue = a.HasValue && b.HasValue;
```

---

## 基于C#的工业软件 中的应用

### 1. 安全访问配置

```csharp
public class CameraConfig {
    public NetworkSettings? Network { get; set; }
}

public class NetworkSettings {
    public string? IPAddress { get; set; }
    public int? Port { get; set; }
}

public string GetCameraIP(CameraConfig config) {
    // 安全访问多层嵌套属性
    return config?.Network?.IPAddress ?? "192.168.1.1";
}

public int GetPort(CameraConfig config) {
    // 可空值类型 + 默认值
    return config?.Network?.Port ?? 8080;
}
```

### 2. 可选的检测参数

```csharp
public class DetectionOptions {
    public double? ConfidenceThreshold { get; set; }
    public int? MaxDefects { get; set; }
    public bool? SaveResults { get; set; }
}

public List<Defect> RunDetection(string imagePath, DetectionOptions? options = null) {
    // 使用默认值或传入的值
    double threshold = options?.ConfidenceThreshold ?? 0.85;
    int maxDefects = options?.MaxDefects ?? 100;
    bool saveResults = options?.SaveResults ?? true;

    // 执行检测...
    var defects = PerformDetection(imagePath, threshold, maxDefects);

    if (saveResults) {
        SaveResults(defects);
    }

    return defects;
}
```

### 3. 安全的对象访问

```csharp
public class Camera {
    public string ID { get; set; }
    public ConnectionStatus? Status { get; set; }
}

public class ConnectionStatus {
    public bool IsConnected { get; set; }
    public DateTime? LastConnectedTime { get; set; }
}

public void DisplayCameraStatus(Camera? camera) {
    if (camera == null) {
        Console.WriteLine("No camera available");
        return;
    }

    Console.WriteLine($"Camera ID: {camera.ID}");

    // 安全访问嵌套属性
    bool isConnected = camera.Status?.IsConnected ?? false;
    string statusText = isConnected ? "Connected" : "Disconnected";
    Console.WriteLine($"Status: {statusText}");

    // 安全显示最后连接时间
    if (camera.Status?.LastConnectedTime.HasValue == true) {
        Console.WriteLine($"Last connected: {camera.Status.LastConnectedTime.Value}");
    }
}
```

### 4. 防御性编程

```csharp
public class DefectDetector {
    private readonly List<Defect> _defects = new List<Defect>();

    // 永远返回非 null 集合
    public List<Defect> GetAllDefects() => _defects?.ToList() ?? new List<Defect>();

    // 安全添加
    public void AddDefect(Defect? defect) {
        if (defect == null) {
            Log.Warning("Attempted to add null defect");
            return;
        }

        // 额外验证
        if (string.IsNullOrWhiteSpace(defect.Type)) {
            Log.Warning("Defect type is empty");
            return;
        }

        _defects.Add(defect);
    }
}
```

---

## 常见陷阱 (Common Pitfalls)

### 1. 可空值类型的装箱

```csharp
int? nullable = null;

// 装箱为 object,但仍是 null
object boxed = nullable; // null,而不是装箱的 Nullable<int>

// 检查
bool isNull = (boxed == null); // true
```

### 2. 字符串的特殊性

```csharp
// 空字符串不等于 null
string empty = "";
bool isNull = (empty == null); // false
bool isEmpty = string.IsNullOrEmpty(empty); // true

// 推荐使用
bool isNullOrWhite = string.IsNullOrWhiteSpace(empty); // true
```

### 3. null 传播与副作用

```csharp
int callCount = 0;

int? GetValue() {
    callCount++;
    return null;
}

// ?. 短路:GetValue() 只调用一次
int? result = GetValue()?.ToString()?.Length;
Console.WriteLine(callCount); // 1 (不是 3)
```

---

## 相关链接
- [C# 基础语法](./basic-syntax.md)
- [异常处理 (Exception Handling)](./exception-handling.md)
- [值类型 vs 引用类型](./value-vs-reference-types.md)
- [LINQ 基础](./linq-basics.md)
