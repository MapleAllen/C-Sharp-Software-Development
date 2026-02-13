# LINQ 基础 (LINQ Basics)

LINQ (Language Integrated Query,语言集成查询) 是 C# 中用于数据查询和操作的强大工具,提供统一的语法处理集合、数据库、XML 等数据源。

## 什么是 LINQ?

LINQ 提供了一致的查询语法,使数据操作更加直观和类型安全。

### 为什么使用 LINQ?
-  **可读性强**: 接近自然语言的查询表达
-  **类型安全**: 编译时类型检查
-  **简洁高效**: 减少循环和临时变量
-  **延迟执行**: 按需查询,提升性能

---

## 查询语法 vs 方法语法

LINQ 有两种写法,推荐使用方法语法。

### 查询语法 (Query Syntax)
类似 SQL 的声明式语法。

```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

// 查询语法
var evenNumbers = from n in numbers
                  where n % 2 == 0
                  select n;
```

### 方法语法 (Method Syntax) - 推荐
使用扩展方法和 lambda 表达式。

```csharp
// 方法语法 (推荐)
var evenNumbers = numbers.Where(n => n % 2 == 0);
```

**何时使用哪种?**
-  方法语法:更灵活,可以链式调用,更符合 C# 风格 (推荐)
- ⚠️ 查询语法:某些复杂查询(如多个 from)可能更清晰

---

## 常用 LINQ 操作符

### 1. Where - 过滤

```csharp
List<DefectItem> defects = new List<DefectItem> {
    new DefectItem { Type = "Scratch", Confidence = 0.95 },
    new DefectItem { Type = "Dent", Confidence = 0.65 },
    new DefectItem { Type = "Scratch", Confidence = 0.80 },
    new DefectItem { Type = "Crack", Confidence = 0.90 }
};

// 过滤高置信度缺陷
var highConfidence = defects.Where(d => d.Confidence >= 0.85);

// 多条件过滤
var criticalScratches = defects.Where(d =>
    d.Type == "Scratch" && d.Confidence > 0.9
);
```

### 2. Select - 投影 (提取特定字段)

```csharp
// 提取缺陷类型
var types = defects.Select(d => d.Type);
// 结果: ["Scratch", "Dent", "Scratch", "Crack"]

// 创建匿名对象
var summary = defects.Select(d => new {
    d.Type,
    ConfidencePercent = d.Confidence * 100
});

// 转换为字符串
var descriptions = defects.Select(d => $"{d.Type} ({d.Confidence:P0})");
// 结果: ["Scratch (95%)", "Dent (65%)", ...]
```

### 3. OrderBy / OrderByDescending - 排序

```csharp
// 按置信度升序
var sortedAsc = defects.OrderBy(d => d.Confidence);

// 按置信度降序
var sortedDesc = defects.OrderByDescending(d => d.Confidence);

// 多级排序:先按类型,再按置信度
var multiSort = defects
    .OrderBy(d => d.Type)
    .ThenByDescending(d => d.Confidence);
```

### 4. First / FirstOrDefault - 获取第一个元素

```csharp
// First - 找不到会抛异常
var firstScratch = defects.First(d => d.Type == "Scratch");

// FirstOrDefault - 找不到返回 null 或默认值 (更安全)
var firstDent = defects.FirstOrDefault(d => d.Type == "Dent");
if (firstDent != null) {
    Console.WriteLine($"Found dent with confidence: {firstDent.Confidence}");
}
```

### 5. Single / SingleOrDefault - 获取唯一元素

```csharp
// Single - 期望有且仅有一个,否则抛异常
var uniqueCrack = defects.Single(d => d.Type == "Crack");

// SingleOrDefault - 允许为空,但多个会抛异常
var maybeCrack = defects.SingleOrDefault(d => d.Type == "Crack");
```

### 6. Any / All - 存在性判断

```csharp
// Any - 是否存在满足条件的元素
bool hasDents = defects.Any(d => d.Type == "Dent");

// Any - 是否有任何元素
bool hasDefects = defects.Any();

// All - 是否所有元素都满足条件
bool allHighConfidence = defects.All(d => d.Confidence > 0.8);
```

### 7. Count - 计数

```csharp
// Count - 总数
int totalCount = defects.Count();

// Count - 满足条件的数量
int scratchCount = defects.Count(d => d.Type == "Scratch");
```

### 8. Sum / Average / Max / Min - 聚合

```csharp
List<double> confidences = new List<double> { 0.95, 0.65, 0.80, 0.90 };

double sum = confidences.Sum();           // 3.3
double avg = confidences.Average();       // 0.825
double max = confidences.Max();           // 0.95
double min = confidences.Min();           // 0.65

// 对对象集合应用
double avgConfidence = defects.Average(d => d.Confidence);
```

### 9. Distinct - 去重

```csharp
var types = defects.Select(d => d.Type).Distinct();
// 结果: ["Scratch", "Dent", "Crack"] (去掉重复的 "Scratch")
```

### 10. Skip / Take - 分页

```csharp
// Take - 取前 N 个
var top3 = defects.OrderByDescending(d => d.Confidence).Take(3);

// Skip - 跳过前 N 个
var after2 = defects.Skip(2);

// 分页示例:每页 10 条,取第 3 页
int pageSize = 10;
int pageNumber = 3;
var page = defects.Skip((pageNumber - 1) * pageSize).Take(pageSize);
```

### 11. GroupBy - 分组

```csharp
// 按类型分组
var groupedByType = defects.GroupBy(d => d.Type);

foreach (var group in groupedByType) {
    Console.WriteLine($"Type: {group.Key}");
    foreach (var defect in group) {
        Console.WriteLine($"  - Confidence: {defect.Confidence}");
    }
}

// 分组并统计
var statistics = defects
    .GroupBy(d => d.Type)
    .Select(g => new {
        DefectType = g.Key,
        Count = g.Count(),
        AvgConfidence = g.Average(d => d.Confidence)
    });
```

---

## 延迟执行 (Deferred Execution)

LINQ 查询在遍历时才真正执行,这称为延迟执行。

### 延迟执行示例

```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };

// 定义查询 - 此时还没有执行
var query = numbers.Where(n => {
    Console.WriteLine($"Checking {n}");
    return n > 3;
});

Console.WriteLine("Query defined");

// 遍历时才执行
foreach (var n in query) {
    Console.WriteLine($"Result: {n}");
}

// 输出:
// Query defined
// Checking 1
// Checking 2
// Checking 3
// Checking 4
// Result: 4
// Checking 5
// Result: 5
```

### 立即执行 - ToList() / ToArray()

```csharp
// 立即执行并缓存结果
var results = numbers.Where(n => n > 3).ToList();
// 此时筛选已完成,results 是实际的 List<int>

// 再次遍历不会重新执行查询
foreach (var n in results) {
    Console.WriteLine(n); // 不会再打印 "Checking"
}
```

**何时使用立即执行?**
-  需要多次遍历同一结果
-  数据源可能改变
-  需要获取 Count

---

## 链式调用 (Method Chaining)

LINQ 方法可以链式调用,构建复杂查询。

```csharp
var result = defects
    .Where(d => d.Confidence > 0.7)          // 1. 过滤低置信度
    .OrderByDescending(d => d.Confidence)    // 2. 按置信度降序
    .Take(5)                                  // 3. 取前 5 个
    .Select(d => d.Type)                      // 4. 只保留类型
    .Distinct()                               // 5. 去重
    .ToList();                                // 6. 立即执行并转换为 List
```

---

## 基于C#的工业软件 中的应用

### 1. 按置信度筛选缺陷

```csharp
public List<DefectItem> GetCriticalDefects(List<DefectItem> allDefects, double threshold) {
    return allDefects
        .Where(d => d.Confidence >= threshold)
        .OrderByDescending(d => d.Confidence)
        .ToList();
}

// 使用
var critical = GetCriticalDefects(detectionResults, 0.85);
```

### 2. 缺陷统计报告

```csharp
public class DefectStatistics {
    public string DefectType { get; set; }
    public int Count { get; set; }
    public double AverageConfidence { get; set; }
    public double MaxConfidence { get; set; }
}

public List<DefectStatistics> GenerateReport(List<DefectItem> defects) {
    return defects
        .GroupBy(d => d.Type)
        .Select(g => new DefectStatistics {
            DefectType = g.Key,
            Count = g.Count(),
            AverageConfidence = g.Average(d => d.Confidence),
            MaxConfidence = g.Max(d => d.Confidence)
        })
        .OrderByDescending(s => s.Count)
        .ToList();
}
```

### 3. 查找特定日期的图像文件

```csharp
public List<string> FindImagesByDate(string folderPath, DateTime date) {
    return Directory.EnumerateFiles(folderPath, "*.jpg", SearchOption.AllDirectories)
        .Where(path => File.GetCreationTime(path).Date == date.Date)
        .OrderBy(path => Path.GetFileName(path))
        .ToList();
}

// 使用
var todayImages = FindImagesByDate(@"D:\Data", DateTime.Today);
```

### 4. 配置验证

```csharp
public bool ValidateConfiguration(AppConfig config) {
    var cameras = config.Cameras;

    // 检查是否所有相机都有有效 IP
    bool allHaveValidIP = cameras.All(c =>
        !string.IsNullOrEmpty(c.IPAddress) &&
        System.Net.IPAddress.TryParse(c.IPAddress, out _)
    );

    // 检查是否有重复 ID
    bool noDuplicateIDs = cameras.Select(c => c.ID).Distinct().Count() == cameras.Count;

    return allHaveValidIP && noDuplicateIDs;
}
```

### 5. 生产数据分析

```csharp
public class ProductionSummary {
    public DateTime Date { get; set; }
    public int TotalInspected { get; set; }
    public int DefectiveCount { get; set; }
    public double DefectRate { get; set; }
}

public List<ProductionSummary> GetWeeklySummary(List<InspectionRecord> records) {
    var oneWeekAgo = DateTime.Now.AddDays(-7);

    return records
        .Where(r => r.InspectionTime >= oneWeekAgo)
        .GroupBy(r => r.InspectionTime.Date)
        .Select(g => new ProductionSummary {
            Date = g.Key,
            TotalInspected = g.Count(),
            DefectiveCount = g.Count(r => r.HasDefect),
            DefectRate = (double)g.Count(r => r.HasDefect) / g.Count()
        })
        .OrderBy(s => s.Date)
        .ToList();
}
```

---

## 性能注意事项 (Performance Tips)

### 1. 避免多次枚举
```csharp
//  错误 - 三次枚举,三次执行查询
var query = defects.Where(d => d.Confidence > 0.8);
int count = query.Count();        // 第 1 次枚举
var first = query.First();        // 第 2 次枚举
var list = query.ToList();        // 第 3次枚举

//  正确 - 只执行一次
var list = defects.Where(d => d.Confidence > 0.8).ToList();
int count = list.Count;
var first = list.First();
```

### 2. 先过滤后排序
```csharp
//  低效 - 对所有数据排序后再过滤
var result = defects.OrderBy(d => d.Confidence).Where(d => d.Type == "Scratch");

//  高效 - 先过滤减少数据量,再排序
var result = defects.Where(d => d.Type == "Scratch").OrderBy(d => d.Confidence);
```

### 3. 使用 Any 而不是 Count > 0
```csharp
//  低效 - Count 会遍历所有元素
if (defects.Count() > 0) { ... }

//  高效 - Any 找到第一个就返回
if (defects.Any()) { ... }
```

---

## 相关链接
- [集合与泛型 (Collections and Generics)](./collections-and-generics.md)
- [委托与事件 (Delegates and Events)](./delegates-and-events.md)
- [控制流 (Control Flow)](./control-flow.md)
- [异步编程 (Async/Await)](./async-await-basics.md)
