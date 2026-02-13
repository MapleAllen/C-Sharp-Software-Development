# C# 基础语法 (Basic Syntax)

C# 是一种强类型、面向对象的编程语言。

## 变量与常量 (Variables and Constants)

### 变量 (Variables)
```csharp
int age = 25;
double price = 19.99;
string name = "Antigravity";
bool isActive = true;

// 使用 var 进行类型推断
var message = "Hello, C#!";
```

### 常量 (Constants)
```csharp
const double Pi = 3.14159;
const string AppName = "DefectDetectionSystem";
```

---

## 数据类型 (Data Types)

- **值类型 (Value Types)**: `int`, `float`, `double`, `char`, `bool`, `struct`, `enum`
- **引用类型 (Reference Types)**: `string`, `class`, `interface`, `delegate`, `object`

---

## 运算符 (Operators)

- **算术运算符**: `+`, `-`, `*`, `/`, `%`
- **关系运算符**: `==`, `!=`, `>`, `<`, `>=`, `<=`
- **逻辑运算符**: `&&`, `||`, `!`

---

## 字符串处理 (String Handling)

### 字符串插值 (String Interpolation)
字符串插值是创建格式化字符串的推荐方式。

```csharp
string cameraId = "Camera_01";
int frameCount = 1024;

// 字符串插值 (推荐)
string message = $"Camera {cameraId} has captured {frameCount} frames";

// 传统方式 (不推荐)
string oldWay = "Camera " + cameraId + " has captured " + frameCount + " frames";
```

### 逐字字符串 (Verbatim Strings)
使用 `@` 符号可以避免转义字符的问题,特别适合文件路径。

```csharp
// 使用 @ 符号,不需要转义反斜杠
string imagePath = @"D:\Data\Images\20231027\image001.jpg";

// 不使用 @ 需要双反斜杠
string samePath = "D:\\Data\\Images\\20231027\\image001.jpg";
```

### 常用字符串方法
```csharp
string deviceName = "  Camera_03  ";

// 常用方法
deviceName.Trim();              // "Camera_03" (去除首尾空格)
deviceName.ToUpper();           // "  CAMERA_03  "
deviceName.Replace("03", "05"); // "  Camera_05  "
deviceName.Contains("Camera");  // true
deviceName.StartsWith("  C");   // true

// 字符串分割
string csv = "100,200,300";
string[] values = csv.Split(','); // ["100", "200", "300"]
```

---

## 可空类型 (Nullable Types)

### 可空值类型 (Nullable Value Types)
值类型默认不能为 `null`,但可以使用 `?` 使其可空。

```csharp
int? optionalPort = null; // 可空整数
double? threshold = 0.75;

// 检查是否有值
if (threshold.HasValue) {
    Console.WriteLine($"Threshold: {threshold.Value}");
}
```

### 空值合并运算符 (Null-Coalescing Operator)
`??` 运算符提供默认值,当左侧为 null 时使用右侧值。

```csharp
int? configPort = null;
int actualPort = configPort ?? 8080; // 如果 configPort 为 null,使用 8080

// C# 8+ 空值赋值运算符
configPort ??= 8080; // 仅当 configPort 为 null 时赋值
```

### 空值条件运算符 (Null-Conditional Operator)
`?.` 运算符安全地访问可能为 null 的对象成员。

```csharp
string? deviceName = GetDeviceName(); // 可能返回 null

// 安全访问,如果 deviceName 为 null,整个表达式返回 null
int? length = deviceName?.Length;

// 链式调用
var camera = GetCamera();
string? ip = camera?.Config?.IPAddress; // 任何一级为 null 都不会抛异常
```

---

## 类型转换 (Type Conversion)

### 隐式转换 (Implicit Casting)
小范围类型自动转换为大范围类型。

```csharp
int count = 100;
double result = count; // int -> double 自动转换
```

### 显式转换 (Explicit Casting)
大范围类型转换为小范围类型需要显式转换,可能丢失精度。

```csharp
double threshold = 0.85;
int percent = (int)(threshold * 100); // 85 (小数部分被截断)
```

### Parse 和 TryParse
从字符串转换为数值类型。

```csharp
string input = "1024";

// Parse: 转换失败会抛出异常
int value1 = int.Parse(input);

// TryParse: 转换失败返回 false (更安全)
if (int.TryParse(input, out int value2)) {
    Console.WriteLine($"Parsed: {value2}");
} else {
    Console.WriteLine("Invalid number");
}
```

### Convert 类
用于多种类型之间的转换。

```csharp
string text = "123";
int number = Convert.ToInt32(text);

bool isActive = Convert.ToBoolean(1); // true
string hex = Convert.ToString(255, 16); // "ff"
```

---

## 命名空间 (Namespaces and Using Directives)

### Using 指令
在文件顶部引入命名空间,避免每次都写完整路径。

```csharp
using System;
using System.Collections.Generic;
using System.IO;

// 现在可以直接使用 File 类,而不是 System.IO.File
File.WriteAllText("log.txt", "System started");
```

### Using Static
可以直接使用静态成员,无需类名前缀。

```csharp
using static System.Math;
using static System.Console;

double result = Sqrt(16); // 直接使用 Math.Sqrt
WriteLine(result);        // 直接使用 Console.WriteLine
```

### 文件作用域命名空间 (File-Scoped Namespace, C# 10+)
简化命名空间声明,减少缩进。

```csharp
// 传统方式
namespace InspectionSystem.Camera {
    public class CameraController {
        // ...
    }
}

// 文件作用域命名空间 (C# 10+)
namespace InspectionSystem.Camera;

public class CameraController {
    // ...
}
```

---

## 注释 (Comments)

```csharp
// 单行注释

/*
   多行注释
   用于解释复杂逻辑
*/

/// <summary>
/// XML 文档注释,用于描述类或方法
/// 可以被 IDE 和文档生成工具识别
/// </summary>
/// <param name="threshold">检测阈值,范围 0.0-1.0</param>
/// <returns>检测到的缺陷列表</returns>
public List<Defect> DetectDefects(double threshold) {
    // 方法实现...
}
```

---

## 相关链接
- [控制流 (Control Flow)](./control-flow.md)
- [面向对象编程 (OOP)](./object-oriented-programming.md)
- [字符串操作 (String Manipulation)](./string-manipulation.md)
- [空值处理 (Null Handling)](./null-handling.md)
