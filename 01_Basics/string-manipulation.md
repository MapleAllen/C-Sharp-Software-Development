# 字符串操作 (String Manipulation)

字符串是编程中最常用的数据类型之一。C# 提供了丰富的字符串操作方法。

## 字符串基础 (String Basics)

### 字符串不可变性 (Immutability)
C# 中的字符串是**不可变的** (Immutable),每次修改都会创建新字符串。

```csharp
string original = "Hello";
string modified = original.ToUpper(); // 创建新字符串 "HELLO"

Console.WriteLine(original);  // "Hello" (原字符串未改变)
Console.WriteLine(modified);  // "HELLO"
```

### 字符串驻留 (String Interning)
相同的字符串字面量共享同一内存地址。

```csharp
string a = "Camera_01";
string b = "Camera_01";

bool sameReference = object.ReferenceEquals(a, b); // true (指向同一对象)
```

---

## 常用字符串操作 (Common String Operations)

### 1. 字符串拼接 (Concatenation)

```csharp
string first = "Hello";
string second = "World";

// 方式 1: + 运算符 (少量拼接)
string result1 = first + " " + second; // "Hello World"

// 方式 2: string.Concat
string result2 = string.Concat(first, " ", second);

// 方式 3: 字符串插值 (推荐)
string cameraId = "Camera_01";
string status = "Connected";
string message = $"{cameraId} is {status}"; // "Camera_01 is Connected"

// 方式 4: string.Join (数组拼接)
string[] parts = { "Camera", "01", "OK" };
string joined = string.Join("_", parts); // "Camera_01_OK"
```

### 2. 查找和检查 (Search and Check)

```csharp
string text = "DefectDetectionSystem";

// 包含
bool contains = text.Contains("Detection"); // true

// 开头/结尾
bool startsWith = text.StartsWith("Defect"); // true
bool endsWith = text.EndsWith("System");     // true

// 查找位置
int index = text.IndexOf("Detection"); // 6
int lastIndex = text.LastIndexOf("e"); // 14

// 区分大小写
bool containsIgnoreCase = text.IndexOf("detection", StringComparison.OrdinalIgnoreCase) >= 0;
```

### 3. 提取子字符串 (Substring)

```csharp
string filePath = @"D:\Data\2023-10-27\image001.jpg";

// Substring(startIndex)
string fromMiddle = filePath.Substring(8); // "2023-10-27\image001.jpg"

// Substring(startIndex, length)
string year = filePath.Substring(8, 4); // "2023"

// 提取文件名 (推荐使用 Path 类)
string fileName = Path.GetFileName(filePath); // "image001.jpg"
```

### 4. 替换 (Replace)

```csharp
string text = "Camera_01_Image_001";

// Replace(oldValue, newValue)
string updated = text.Replace("001", "002"); // "Camera_01_Image_002"

// 替换多个字符
string cleaned = text.Replace("_", "-"); // "Camera-01-Image-001"
```

### 5. 分割和 合并 (Split and Join)

```csharp
// Split - 分割字符串
string csv = "Scratch,0.95,100,200";
string[] parts = csv.Split(','); // ["Scratch", "0.95", "100", "200"]

// 分割后解析
string defectType = parts[0];
double confidence = double.Parse(parts[1]);

//  Join - 合并数组
string[] devices = { "Camera_01", "Camera_02", "Camera_03" };
string allDevices = string.Join(", ", devices); // "Camera_01, Camera_02, Camera_03"
```

### 6. 修剪 (Trim)

```csharp
string input = "  Camera_01  ";

string trimmed = input.Trim();       // "Camera_01" (去除首尾空格)
string trimStart = input.TrimStart(); // "Camera_01  "
string trimEnd = input.TrimEnd();     // "  Camera_01"

// 修剪指定字符
string path = "/Data/Images/";
string cleaned = path.Trim('/'); // "Data/Images"
```

### 7. 大小写转换 (Case Conversion)

```csharp
string text = "DefectDetectionSystem";

string upper = text.ToUpper();       // "DEFECTDETECTIONSYSTEM"
string lower = text.ToLower();       // "defectdetectionsystem"
string title = "hello world".ToUpper(); // 首字母大写需要手动实现

// 文化不敏感转换 (更快)
string upperInvariant = text.ToUpperInvariant();
```

### 8. 判空检查 (Null/Empty Checks)

```csharp
string text1 = null;
string text2 = "";
string text3 = "   ";

// IsNullOrEmpty
bool isEmpty1 = string.IsNullOrEmpty(text1); // true
bool isEmpty2 = string.IsNullOrEmpty(text2); // true

// IsNullOrWhiteSpace (包含空格)
bool isWhitespace = string.IsNullOrWhiteSpace(text3); // true

// 推荐使用
if (string.IsNullOrWhiteSpace(userInput)) {
    Console.WriteLine("Input is required");
}
```

---

## 字符串格式化 (String Formatting)

### 1. 复合格式化 (Composite Formatting)

```csharp
string name = "Camera_01";
int frameCount = 1024;
double confidence = 0.8567;

// string.Format
string result = string.Format("Camera {0} captured {1} frames", name, frameCount);

// 格式说明符
string formatted = string.Format(
    "Confidence: {0:P} ({1:F2})",
    confidence,   // 0:P  -> 85.67% (百分比)
    confidence    // 1:F2 -> 0.86 (保留 2 位小数)
);
```

### 2. 字符串插值 (String Interpolation) - 推荐

```csharp
string cameraId = "Camera_01";
int count = 1024;
double value = 0.8567;

// 基本插值
string msg1 = $"Camera {cameraId} captured {count} frames";

// 格式说明符
string msg2 = $"Confidence: {value:P} ({value:F2})";

// 表达式
string msg3 = $"Status: {count > 1000 ? "OK" : "Low"}";

// 对齐和宽度
string aligned = $"|{cameraId,-15}|{count,10}|";
// |Camera_01      |      1024|
```

### 3. 常用格式说明符

| 说明符 | 用途 | 示例输入 | 示例输出 |
|--------|------|----------|----------|
| `D` | 整数(零填充) | `{123:D5}` | `00123` |
| `F` | 定点数 | `{3.14159:F2}` | `3.14` |
| `P` | 百分比 | `{0.856:P}` | `85.60%` |
| `C` | 货币 | `{1234.5:C}` | `¥1,234.50` |
| `E` | 科学计数法 | `{1234:E2}` | `1.23E+003` |
| `X` | 十六进制 | `{255:X}` | `FF` |

---

## StringBuilder - 高效字符串构建

当需要大量拼接字符串时,使用 `StringBuilder` 避免性能问题。

### 性能对比

```csharp
//  低效 - 每次拼接创建新字符串
string result = "";
for (int i = 0; i < 1000; i++) {
    result += i.ToString(); // 创建 1000 个临时字符串
}

//  高效 - 使用 StringBuilder
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.Append(i);
}
string result = sb.ToString();
```

### StringBuilder 常用方法

```csharp
StringBuilder sb = new StringBuilder();

// Append - 追加
sb.Append("Camera_");
sb.Append(1);
sb.Append(" is ");
sb.Append("Connected");

// AppendLine - 追加并换行
sb.AppendLine("First line");
sb.AppendLine("Second line");

// AppendFormat - 格式化追加
sb.AppendFormat("Confidence: {0:P}", 0.95);

// Insert - 插入
sb.Insert(0, "Prefix: ");

// Replace - 替换
sb.Replace("Connected", "Disconnected");

// Remove - 删除
sb.Remove(0, 8); // 删除前 8 个字符

// 转换为字符串
string final = sb.ToString();
```

### 何时使用 StringBuilder?

-  循环中大量拼接
-  构建大型字符串 (如 HTML, JSON)
-  少量拼接 (3-5 次) - 使用字符串插值更简洁

---

## 正则表达式基础 (Regular Expressions Basics)

正则表达式用于复杂的字符串匹配和提取。

### 基本匹配

```csharp
using System.Text.RegularExpressions;

string text = "Camera_01 captured image at 192.168.1.100";

// IsMatch - 是否匹配
bool hasIP = Regex.IsMatch(text, @"\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}");
Console.WriteLine(hasIP); // true
```

### 提取匹配内容

```csharp
string logLine = "[2023-10-27 14:32:15] Camera_01: Image captured";

// Match - 获取第一个匹配
Match match = Regex.Match(logLine, @"\d{4}-\d{2}-\d{2} \d{2}:\d{2}:\d{2}");
if (match.Success) {
    Console.WriteLine($"Timestamp: {match.Value}");
    // Timestamp: 2023-10-27 14:32:15
}

// Matches - 获取所有匹配
string data = "Camera_01, Camera_02, Camera_03";
MatchCollection matches = Regex.Matches(data, @"Camera_\d{2}");
foreach (Match m in matches) {
    Console.WriteLine(m.Value); // Camera_01, Camera_02, Camera_03
}
```

### 分组提取

```csharp
string fileName = "image_2023-10-27_143215.jpg";

// 使用命名分组
Match match = Regex.Match(fileName,
    @"image_(?<date>\d{4}-\d{2}-\d{2})_(?<time>\d{6})\.jpg"
);

if (match.Success) {
    string date = match.Groups["date"].Value;   // 2023-10-27
    string time = match.Groups["time"].Value;   // 143215
    Console.WriteLine($"Date: {date}, Time: {time}");
}
```

### 常用正则模式

| 模式 | 含义 | 示例 |
|------|------|------|
| `\d` | 数字 | `\d{4}` 匹配 4 位数字 |
| `\w` | 字母、数字、下划线 | `\w+` 匹配单词 |
| `\s` | 空白字符 | `\s+` 匹配空格 |
| `.` | 任意字符 | `.*` 匹配任意内容 |
| `^` |  字符串开头 | `^Camera` |
| `$` | 字符串结尾 | `\.jpg$` |
| `[...]` | 字符集 | `[a-z]` 匹配小写字母 |
| `{n,m}` | 重复次数 | `\d{2,4}` 2-4 位数字 |

---

## 基于C#的工业软件 中的应用

### 1. 解析日志文件名

```csharp
public DateTime? ParseLogFileName(string fileName) {
    // 格式: log-2023-10-27.txt
    var match = Regex.Match(fileName, @"log-(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})\.txt");

    if (match.Success) {
        int year = int.Parse(match.Groups["year"].Value);
        int month = int.Parse(match.Groups["month"].Value);
        int day = int.Parse(match.Groups["day"].Value);
        return new DateTime(year, month, day);
    }
    return null;
}
```

### 2. 格式化缺陷报告

```csharp
public string FormatDefectReport(List<DefectItem> defects) {
    StringBuilder report = new StringBuilder();

    report.AppendLine("=== Defect Detection Report ===");
    report.AppendLine($"Generated: {DateTime.Now:yyyy-MM-dd HH:mm:ss}");
    report.AppendLine($"Total Defects: {defects.Count}");
    report.AppendLine();

    foreach (var defect in defects) {
        report.AppendFormat(
            "{0,-15} | Confidence: {1,6:P1} | Position: ({2}, {3})",
            defect.Type,
            defect.Confidence,
            defect.X,
            defect.Y
        );
        report.AppendLine();
    }

    return report.ToString();
}
```

### 3. 验证IP地址

```csharp
public bool IsValidIPAddress(string ip) {
    if (string.IsNullOrWhiteSpace(ip)) return false;

    // 方式 1: 正则表达式
    string pattern = @"^((25[0-5]|2[0-4]\d|[01]?\d\d?)\.){3}(25[0-5]|2[0-4]\d|[01]?\d\d?)$";
    bool regexValid = Regex.IsMatch(ip, pattern);

    // 方式 2: 使用内置方法 (推荐)
    bool parseValid = System.Net.IPAddress.TryParse(ip, out _);

    return parseValid;
}
```

### 4. 清理用户输入

```csharp
public string SanitizeInput(string input) {
    if (string.IsNullOrWhiteSpace(input)) return "";

    // 1. 修剪空格
    string cleaned = input.Trim();

    // 2. 移除特殊字符
    cleaned = Regex.Replace(cleaned, @"[^\w\s-]", "");

    // 3. 压缩多个空格为一个
    cleaned = Regex.Replace(cleaned, @"\s+", " ");

    return cleaned;
}
```

---

## 性能提示 (Performance Tips)

### 1. 预编译正则表达式

```csharp
//  低效 - 每次调用都编译正则表达式
public bool IsValidFormat(string input) {
    return Regex.IsMatch(input, @"^\d{4}-\d{2}-\d{2}$");
}

//  高效 - 预编译,复用
private static readonly Regex DateRegex = new Regex(
    @"^\d{4}-\d{2}-\d{2}$",
    RegexOptions.Compiled
);

public bool IsValidFormat(string input) {
    return DateRegex.IsMatch(input);
}
```

### 2. String vs StringBuilder 选择

```csharp
// 少量拼接 (< 5 次): 使用字符串插值
string msg = $"{camera} captured {count} images at {time}";

// 大量拼接 (循环): 使用 StringBuilder
StringBuilder html = new StringBuilder();
html.AppendLine("<table>");
foreach (var item in items) {
    html.AppendLine($"<tr><td>{item.Name}</td><td>{item.Value}</td></tr>");
}
html.AppendLine("</table>");
```

---

## 相关链接
- [C# 基础语法](./basic-syntax.md)
- [文件 I/O](./file-io-and-serialization.md)
- [LINQ 基础](./linq-basics.md)
- [异常处理](./exception-handling.md)
