# C# 控制流 (Control Flow)

控制流决定了代码的执行顺序。C# 提供了丰富的控制流语句,包括条件判断和循环结构。

## 条件语句 (Conditional Statements)

### if-else
```csharp
int score = 85;
if (score >= 90) {
    Console.WriteLine("A");
} else if (score >= 80) {
    Console.WriteLine("B");
} else {
    Console.WriteLine("C");
}
```

### 三元运算符 (Ternary Operator)
简洁的条件表达式,适合简单的二选一场景。

```csharp
int defectCount = 5;
string status = defectCount > 0 ? "NG" : "OK";  // NG (不良品)

// 等价于:
string status2;
if (defectCount > 0) {
    status2 = "NG";
} else {
    status2 = "OK";
}
```

**何时使用三元运算符:**
-  简单的值选择
-  避免嵌套使用 (难以阅读)

### switch 语句 (传统)
```csharp
int day = 3;
switch (day) {
    case 1:
        Console.WriteLine("Monday");
        break;
    case 2:
        Console.WriteLine("Tuesday");
        break;
    default:
        Console.WriteLine("Another day");
        break;
}
```

---

## 现代 Switch 表达式 (Modern Switch Expressions, C# 8+)

### 基本 Switch 表达式
更简洁,直接返回值。

```csharp
int dayOfWeek = 3;
string dayName = dayOfWeek switch {
    1 => "Monday",
    2 => "Tuesday",
    3 => "Wednesday",
    4 => "Thursday",
    5 => "Friday",
    6 => "Saturday",
    7 => "Sunday",
    _ => "Invalid"  // _ 表示默认情况
};
```

### 类型模式 (Type Pattern)
根据对象类型进行分支。

```csharp
object data = GetData();
string message = data switch {
    int number => $"Integer: {number}",
    string text => $"String: {text}",
    double value => $"Double: {value}",
    null => "No data",
    _ => "Unknown type"
};
```

### 关系模式 (Relational Pattern, C# 9+)
使用比较运算符。

```csharp
double confidence = 0.87;
string grade = confidence switch {
    >= 0.9 => "Excellent",
    >= 0.7 => "Good",
    >= 0.5 => "Fair",
    _ => "Poor"
};
```

### 属性模式 (Property Pattern)
基于对象属性匹配。

```csharp
public class DefectItem {
    public string Type { get; set; }
    public double Confidence { get; set; }
}

DefectItem defect = new DefectItem { Type = "Scratch", Confidence = 0.95 };

string action = defect switch {
    { Type: "Scratch", Confidence: >= 0.9 } => "Reject immediately",
    { Type: "Scratch", Confidence: >= 0.7 } => "Manual inspection",
    { Type: "Dent" } => "Measure depth",
    _ => "Pass"
};
```

---

## 循环语句 (Loops)

### for 循环
适合已知循环次数的场景。

```csharp
// 处理图像的每一行
for (int row = 0; row < imageHeight; row++) {
    Console.WriteLine($"Processing row {row}");
}
```

### foreach 循环
用于遍历集合,语法简洁。

```csharp
string[] cameraIds = { "Camera_01", "Camera_02", "Camera_03" };
foreach (string id in cameraIds) {
    Console.WriteLine($"Connecting to {id}");
}

// 遍历字典
Dictionary<string, int> defectCounts = new Dictionary<string, int> {
    { "Scratch", 5 },
    { "Dent", 2 }
};

foreach (var pair in defectCounts) {
    Console.WriteLine($"{pair.Key}: {pair.Value} defects");
}
```

### while 循环
条件在循环前检查。

```csharp
int retryCount = 0;
while (retryCount < 3 && !camera.IsConnected) {
    camera.TryConnect();
    retryCount++;
}
```

### do-while 循环
至少执行一次,条件在循环后检查。

```csharp
string input;
do {
    Console.Write("Enter command (or 'exit' to quit): ");
    input = Console.ReadLine();
    ProcessCommand(input);
} while (input != "exit");
```

---

## 控制流关键字 (Control Flow Keywords)

### break
立即退出当前循环。

```csharp
// 找到第一个高置信度缺陷后停止
foreach (var defect in defects) {
    if (defect.Confidence > 0.95) {
        Console.WriteLine($"Critical defect found: {defect.Type}");
        break; // 退出循环
    }
}
```

### continue
跳过当前迭代,继续下一次循环。

```csharp
// 只处理高置信度的检测结果
foreach (var result in results) {
    if (result.Confidence < 0.7) {
        continue; // 跳过低置信度结果
    }
    ProcessResult(result);
}
```

### return
从方法中返回。

```csharp
public bool ValidateConfiguration(Config config) {
    if (config == null) {
        return false; // 提前返回
    }

    if (string.IsNullOrEmpty(config.CameraIP)) {
        return false;
    }

    return true; // 验证通过
}
```

### goto (不推荐)
跳转到标签位置。**注意:** 在现代 C# 中很少使用,会降低代码可读性。

```csharp
// 不推荐的写法
for (int i = 0; i < 10; i++) {
    if (i == 5) {
        goto Found;
    }
}
Console.WriteLine("Not found");
return;

Found:
Console.WriteLine("Found at 5");

// 推荐: 使用 break 和条件判断代替
```

---

## 基于C#的工业软件 中的应用

### 1. 检测结果分级处理
```csharp
public string ClassifyDefect(DefectItem defect) {
    return defect switch {
        { Type: "Scratch", Confidence: >= 0.90 } => "Critical - Reject",
        { Type: "Scratch", Confidence: >= 0.70 } => "Warning - Review",
        { Type: "Dent", Confidence: >= 0.85 } => "Critical - Measure",
        { Confidence: < 0.50 } => "Uncertain - Retest",
        _ => "Pass"
    };
}
```

### 2. 批量处理带超时控制
```csharp
for (int i = 0; i < imagePaths.Length; i++) {
    if (processingTime > maxTimeout) {
        Console.WriteLine("Timeout exceeded. Stopping batch.");
        break;
    }

    try {
        ProcessImage(imagePaths[i]);
    } catch (Exception ex) {
        Log.Warning($"Failed to process {imagePaths[i]}: {ex.Message}");
        continue; // 跳过失败的图片,继续处理下一张
    }
}
```

---

## 相关链接
- [C# 基础语法](./basic-syntax.md)
- [面向对象编程 (OOP)](./object-oriented-programming.md)
- [集合与泛型 (Collections and Generics)](./collections-and-generics.md)
- [异常处理 (Exception Handling)](./exception-handling.md)
