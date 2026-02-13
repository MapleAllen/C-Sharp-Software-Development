# 值类型 vs 引用类型 (Value vs Reference Types)

理解值类型和引用类型的区别是掌握 C# 内存模型的关键,影响性能、行为和设计决策。

## 内存架构 (Memory Architecture)

### 栈 (Stack) vs 堆 (Heap)

```
┌─────────────────────────────────────┐
│  Stack (栈)                          │
│  ┌─────────────────┐                 │
│  │ int count = 10  │ (值类型)         │
│  ├─────────────────┤                 │
│  │ double x = 3.14 │ (值类型)         │
│  ├─────────────────┤                 │
│  │ ref → 0x1000    │ (引用)          │
│  └─────────────────┘                 │
└─────────────────────────────────────┘

┌─────────────────────────────────────┐
│  Heap (堆)                           │
│  ┌─────────────────┐                 │
│  │ 0x1000:         │                 │
│  │ { Name: "Cam",  │ (对象)          │
│  │   ID: 1 }       │                 │
│  └─────────────────┘                 │
└─────────────────────────────────────┘
```

**栈 (Stack):**
- 快速分配和释放
- 自动管理 (LIFO - 后进先出)
- 存储值类型和引用 (指向堆的地址)
- 大小有限 (~1MB)

**堆 (Heap):**
- 动态分配,生命周期灵活
- 由垃圾回收器 (GC) 管理
- 存储引用类型对象
- 大小较大,按需增长

---

## 值类型 (Value Types)

### 定义
直接包含数据,存储在栈上 (作为局部变量时)。

### 内置值类型
```csharp
// 数值类型
int count = 10;
double temperature = 36.5;
decimal price = 19.99m;
byte data = 255;

// 其他值类型
bool isActive = true;
char symbol = 'A';
DateTime now = DateTime.Now;

// 结构体 (struct)
Point position = new Point(10, 20);
```

### 值类型的行为 - 复制

```csharp
int a = 10;
int b = a; // 复制值

b = 20;

Console.WriteLine(a); // 10 (a 不受影响)
Console.WriteLine(b); // 20
```

### 自定义值类型 - struct

```csharp
// 定义结构体
public struct Point {
    public int X { get; set; }
    public int Y { get; set; }

    public Point(int x, int y) {
        X = x;
        Y = y;
    }

    public double DistanceFromOrigin() {
        return Math.Sqrt(X * X + Y * Y);
    }
}

// 使用
Point p1 = new Point(10, 20);
Point p2 = p1; // 复制整个结构体

p2.X = 100;

Console.WriteLine(p1.X); // 10 (独立副本)
Console.WriteLine(p2.X); // 100
```

---

## 引用类型 (Reference Types)

### 定义
包含对堆上数据的引用,变量存储的是地址。

### 内置引用类型
```csharp
// 类 (class)
string text = "Hello";
object obj = new object();
StringBuilder sb = new StringBuilder();

// 数组
int[] numbers = { 1, 2, 3 };

// 委托
Action action = () => Console.WriteLine("Action");
```

### 引用类型的行为 - 共享引用

```csharp
class Camera {
    public string ID { get; set; }
}

Camera cam1 = new Camera { ID = "Camera_01" };
Camera cam2 = cam1; // 复制引用,指向同一对象

cam2.ID = "Camera_02";

Console.WriteLine(cam1.ID); // "Camera_02" (两个变量指向同一对象!)
Console.WriteLine(cam2.ID); // "Camera_02"
```

### Null 引用
引用类型可以为 null (不指向任何对象)。

```csharp
Camera cam = null; // 合法

// cam.Connect(); // NullReferenceException!

// 安全检查
if (cam != null) {
    cam.Connect();
}
```

---

## 装箱和拆箱 (Boxing and Unboxing)

### 装箱 (Boxing)
将值类型转换为引用类型 (object)。

```csharp
int value = 10; // 栈上

object boxed = value; // 装箱:在堆上创建副本

// 内存布局:
// Stack: value = 10
// Stack: boxed = ref → 堆
// Heap:  10 (装箱后的副本)
```

### 拆箱 (Unboxing)
将装箱的对象转换回值类型。

```csharp
object boxed = 10; // 装箱

int unboxed = (int)boxed; // 拆箱,需要显式转换

// 错误的类型会抛异常
// double wrong = (double)boxed; // InvalidCastException
```

### 装箱/拆箱的性能影响

```csharp
//  低效 - 每次迭代都装箱
ArrayList list = new ArrayList(); // 非泛型集合
for (int i = 0; i < 1000; i++) {
    list.Add(i); // 装箱!
}

//  高效 - 使用泛型,避免装箱
List<int> list = new List<int>();
for (int i = 0; i < 1000; i++) {
    list.Add(i); // 无装箱
}
```

---

## 参数传递 (Parameter Passing)

### 默认:按值传递 (By Value)

#### 值类型

```csharp
void IncrementValue(int number) {
    number++; // 修改局部副本
}

int count = 10;
IncrementValue(count);
Console.WriteLine(count); // 10 (原值不变)
```

#### 引用类型

```csharp
void ModifyCamera(Camera cam) {
    cam.ID = "Modified"; // 修改同一对象
}

Camera camera = new Camera { ID = "Original" };
ModifyCamera(camera);
Console.WriteLine(camera.ID); // "Modified" (对象被修改)

void ReplaceCamera(Camera cam) {
    cam = new Camera { ID = "New" }; // 只改变局部引用
}

Camera camera2 = new Camera { ID = "Original" };
ReplaceCamera(camera2);
Console.WriteLine(camera2.ID); // "Original" (原引用不变)
```

### ref 关键字 - 按引用传递

```csharp
// 值类型
void IncrementByRef(ref int number) {
    number++; // 修改原始值
}

int count = 10;
IncrementByRef(ref count);
Console.WriteLine(count); // 11 (原值被修改)

// 引用类型
void ReplaceCameraByRef(ref Camera cam) {
    cam = new Camera { ID = "Replaced" }; // 替换引用本身
}

Camera camera = new Camera { ID = "Original" };
ReplaceCameraByRef(ref camera);
Console.WriteLine(camera.ID); // "Replaced" (引用被替换)
```

### out 关键字 - 输出参数

```csharp
// 方法必须为 out 参数赋值
bool TryParseThreshold(string input, out double threshold) {
    if (double.TryParse(input, out threshold)) {
        return true;
    }
    threshold = 0.85; // 默认值
    return false;
}

// 使用
if (TryParseThreshold("0.95", out double result)) {
    Console.WriteLine($"Threshold: {result}"); // 0.95
}
```

### in 关键字 (C# 7.2+) - 只读引用

```csharp
// 避免复制大型 struct,但不允许修改
void PrintPoint(in Point point) {
    Console.WriteLine($"({point.X}, {point.Y})");
    // point.X = 10; // 编译错误:不能修改
}

Point p = new Point(5, 10);
PrintPoint(in p);
```

---

## Struct vs Class 选择

### Struct (值类型)
```csharp
public struct Point {
    public int X { get; set; }
    public int Y { get; set; }
}
```

**何时使用 struct:**
-  小型数据 (< 16 字节)
-  不可变 (Immutable) 数据
-  逻辑上表示单一值 (如坐标、颜色)
-  不需要继承

**特点:**
- 栈分配 (作为局部变量时)
- 赋值时复制整个内容
- 不能被继承
- 不能有无参构造函数 (C# 10- )

### Class (引用类型)
```csharp
public class Camera {
    public string ID { get; set; }
    public string IPAddress { get; set; }
}
```

**何时使用 class:**
-  复杂对象
-  需要继承
-  需要引用语义 (共享修改)
-  大型数据

**特点:**
- 堆分配
- 赋值时复制引用
- 支持继承和多态
- 可以为 null

### 工业软件实例对比

```csharp
//  Struct - 图像坐标 (小型、不可变、单一值)
public readonly struct ImageCoordinate {
    public int X { get; }
    public int Y { get; }

    public ImageCoordinate(int x, int y) {
        X = x;
        Y = y;
    }
}

//  Class - 相机配置 (复杂、可变、业务对象)
public class CameraConfiguration {
    public string ID { get; set; }
    public string IPAddress { get; set; }
    public NetworkSettings Network { get; set; }

    public void Connect() {
        // 业务逻辑
    }
}
```

---

## 性能考虑 (Performance Considerations)

### 1. 小型频繁分配:优先 struct

```csharp
//  Class - 大量小对象,GC 压力大
public class Point3D {
    public double X, Y, Z;
}

// 图像处理:数百万个点
for (int i = 0; i < 1_000_000; i++) {
    Point3D p = new Point3D(); // 堆分配,需 GC
}

//  Struct - 栈分配,无 GC 压力
public struct Point3D {
    public double X, Y, Z;
}

for (int i = 0; i < 1_000_000; i++) {
    Point3D p = new Point3D(); // 栈分配,极快
}
```

### 2. 大型 struct 会导致复制开销

```csharp
//  大型 struct - 每次传递都复制 1KB
public struct HugeData {
    public byte[] Buffer; // 1KB
}

void Process(HugeData data) { /* 复制 1KB */ }

//  使用 class 或 in 参数
void Process(in HugeData data) { /* 传递引用,无复制 */ }
```

---

## 基于C#的工业软件 中的应用

### 1. 图像处理中的坐标

```csharp
// Struct - 图像中的像素坐标
public readonly struct PixelCoordinate {
    public int Row { get; }
    public int Column { get; }

    public PixelCoordinate(int row, int column) {
        Row = row;
        Column = column;
    }
}

// 高效处理大量坐标
List<PixelCoordinate> defectPositions = new List<PixelCoordinate>();
for (int row = 0; row < imageHeight; row++) {
    for (int col = 0; col < imageWidth; col++) {
        if (IsDefect(row, col)) {
            defectPositions.Add(new PixelCoordinate(row, col));
        }
    }
}
```

### 2. 缺陷检测结果

```csharp
// Class - 复杂业务对象
public class DefectDetectionResult {
    public string ImagePath { get; set; }
    public DateTime DetectionTime { get; set; }
    public List<Defect> Defects { get; set; }
    public byte[] OriginalImage { get; set; }

    public string GenerateReport() {
        // 复杂业务逻辑
    }
}

// 引用语义:共享和修改同一对象
DefectDetectionResult result = RunDetection(imagePath);
SaveToDatabase(result); // 传递引用,不复制整个对象
SendToServer(result);   // 同一对象
```

### 3. 配置参数

```csharp
// Struct - 轻量级配置
public readonly struct ImageProcessingParams {
    public double BrightnessThreshold { get; }
    public int BlurKernelSize { get; }

    public ImageProcessingParams(double brightness, int kernelSize) {
        BrightnessThreshold = brightness;
        BlurKernelSize = kernelSize;
    }
}

// 使用:按值传递,每个处理器都有独立副本
void ProcessImage(byte[] image, ImageProcessingParams params) {
    // params 是副本,修改不影响调用者
}
```

---

## 常见陷阱 (Common Pitfalls)

### 1. Struct 的可变性问题

```csharp
public struct Counter {
    public int Value { get; set; }

    public void Increment() {
        Value++;
    }
}

Counter c = new Counter();
c.Increment();
Console.WriteLine(c.Value); // 1

//  陷阱:属性返回副本
public Counter CurrentCounter { get; set; }

CurrentCounter.Increment(); // 修改的是副本!
Console.WriteLine(CurrentCounter.Value); // 仍然是 0

//  解决:使用 readonly struct 或改用 class
```

### 2. 数组中的 struct

```csharp
struct Point {
    public int X, Y;
}

Point[] points = new Point[10];
points[0].X = 5; // OK: 修改数组中的元素

// 属性返回的 struct 数组
public Point[] Points { get; set; }
```

---

## 相关链接
- [C# 基础语法](./basic-syntax.md)
- [面向对象编程 (OOP)](./object-oriented-programming.md)
- [空值处理 (Null Handling)](./null-handling.md)
- [集合与泛型](./collections-and-generics.md)
