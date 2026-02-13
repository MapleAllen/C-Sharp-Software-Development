# C# 面向对象编程 (Object-Oriented Programming)

C# 是一门纯粹的面向对象语言。面向对象编程 (OOP) 是一种以"对象"为中心的编程范式,它将数据和行为封装在一起。

## 类与对象 (Classes and Objects)

### 基本类定义
```csharp
public class Camera {
    // 字段 (Field) - 通常为 private
    private string _id;

    // 属性 (Property) - 公开接口
    public string ID { get; set; }
    public string IPAddress { get; set; }
    public bool IsConnected { get; set; }

    // 方法 (Method)
    public void Connect() {
        Console.WriteLine($"Connecting to camera at {IPAddress}...");
        IsConnected = true;
    }
}

// 实例化对象
Camera camera = new Camera {
    ID = "Camera_01",
    IPAddress = "192.168.1.100"
};
camera.Connect();
```

---

## 构造函数 (Constructors)

### 默认构造函数 (Default Constructor)
如果不定义任何构造函数,C# 会提供一个默认无参构造函数。

```csharp
public class Camera {
    public string ID { get; set; }

    // 编译器自动提供默认构造函数
}

Camera cam = new Camera(); // 使用默认构造函数
```

### 参数化构造函数 (Parameterized Constructor)
```csharp
public class Camera {
    public string ID { get; set; }
    public string IPAddress { get; set; }

    // 参数化构造函数
    public Camera(string id, string ipAddress) {
        ID = id;
        IPAddress = ipAddress;
        Console.WriteLine($"Camera {ID} created");
    }
}

Camera cam = new Camera("Camera_01", "192.168.1.100");
```

### 构造函数链 (Constructor Chaining)
使用 `this` 关键字调用其他构造函数,避免代码重复。

```csharp
public class Camera {
    public string ID { get; set; }
    public string IPAddress { get; set; }
    public int Port { get; set; }

    // 完整构造函数
    public Camera(string id, string ipAddress, int port) {
        ID = id;
        IPAddress = ipAddress;
        Port = port;
    }

    // 通过 this 调用上面的构造函数
    public Camera(string id, string ipAddress) : this(id, ipAddress, 8080) {
        // Port 使用默认值 8080
    }
}
```

### 静态构造函数 (Static Constructor)
类级别的初始化,只执行一次,在第一次使用类之前自动调用。

```csharp
public class CameraConfig {
    public static string DefaultIP;

    static CameraConfig() {
        DefaultIP = "192.168.1.1";
        Console.WriteLine("CameraConfig static constructor called");
    }
}
```

---

## 属性 (Properties)

### 自动实现属性 (Auto-Implemented Properties)
最常用的属性形式,编译器自动生成后备字段。

```csharp
public class DefectItem {
    public string Type { get; set; }
    public double Confidence { get; set; }
    public DateTime DetectedTime { get; set; }
}
```

### 带验证的属性 (Properties with Validation)
可以在 getter 和 setter 中添加逻辑。

```csharp
public class Camera {
    private int _port;

    public int Port {
        get { return _port; }
        set {
            if (value < 1 || value > 65535) {
                throw new ArgumentException("Port must be between 1 and 65535");
            }
            _port = value;
        }
    }
}
```

### 只读属性 (Read-Only Property)
只有 getter,没有 setter。

```csharp
public class DefectReport {
    public DateTime CreatedTime { get; }

    public DefectReport() {
        CreatedTime = DateTime.Now; // 只能在构造函数中赋值
    }
}
```

### Init-Only 属性 (C# 9+)
使用 `init` 代替 `set`,只能在对象初始化时赋值。

```csharp
public class Camera {
    public string ID { get; init; }
    public string Model { get; init; }
}

Camera cam = new Camera {
    ID = "Camera_01",
    Model = "BaslerAce"
};

// cam.ID = "Camera_02"; // 错误!不能修改 init-only 属性
```

### 表达式体属性 (Expression-Bodied Properties)
简洁的单行属性。

```csharp
public class DefectItem {
    public string Type { get; set; }
    public double Confidence { get; set; }

    // 计算属性
    public string Grade => Confidence >= 0.9 ? "High" : "Low";

    // 只读属性
    public string Description => $"{Type} ({Confidence:P0})";
}
```

---

## 字段 vs 属性 (Fields vs Properties)

### 何时使用字段
- Private 后备存储
- 不需要验证或逻辑的内部数据

### 何时使用属性
-  公共接口 (推荐)
-  需要验证或计算
-  支持数据绑定 (WPF/MVVM)

```csharp
public class Camera {
    // 字段 (私有)
    private string _serialNumber;

    // 属性 (公开)
    public string SerialNumber {
        get => _serialNumber;
        set => _serialNumber = value?.Trim(); // 自动去除空格
    }
}
```

---

## 三大特性 (Three Pillars of OOP)

### 1. 封装 (Encapsulation)
通过访问修饰符控制成员的可见性。

**访问修饰符:**
- `public`: 任何代码都可以访问
- `private`: 只有类内部可以访问 (默认)
- `protected`: 类内部和派生类可以访问
- `internal`: 同一程序集内可以访问
- `protected internal`: 同一程序集或派生类可以访问

```csharp
public class Camera {
    private string _password;           // 只有类内部可访问
    protected int _retryCount;          // 派生类可访问
    public string ID { get; set; }      // 任何代码可访问
    internal string InternalID { get; } // 同一程序集内可访问

    private void ResetConnection() {
        // 内部实现细节,不对外暴露
    }

    public void Connect() {
        // 公开接口
        ResetConnection();
    }
}
```

### 2. 继承 (Inheritance)
子类继承父类的成员,实现代码复用。

```csharp
// 基类
public class Device {
    public string ID { get; set; }
    public bool IsConnected { get; set; }

    public virtual void Connect() {
        Console.WriteLine($"Device {ID} connecting...");
        IsConnected = true;
    }
}

// 派生类
public class Camera : Device {
    public string IPAddress { get; set; }

    public override void Connect() {
        Console.WriteLine($"Camera {ID} connecting to {IPAddress}...");
        IsConnected = true;
    }

    public void CaptureImage() {
        Console.WriteLine("Image captured");
    }
}

Camera cam = new Camera { ID = "Camera_01", IPAddress = "192.168.1.100" };
cam.Connect();       // 使用重写的方法
cam.CaptureImage();  // Camera 特有的方法
```

### 3. 多态 (Polymorphism)

#### 编译时多态 - 方法重载 (Overloading)
同名方法,不同参数。

```csharp
public class ImageProcessor {
    // 重载方法 - 参数类型不同
    public void Process(string filePath) {
        Console.WriteLine($"Processing file: {filePath}");
    }

    public void Process(byte[] imageData) {
        Console.WriteLine($"Processing byte array: {imageData.Length} bytes");
    }

    // 重载方法 - 参数数量不同
    public void Process(string filePath, double threshold) {
        Console.WriteLine($"Processing {filePath} with threshold {threshold}");
    }
}
```

#### 运行时多态 - 方法重写 (Overriding)
使用 `virtual` 和 `override` 关键字。

```csharp
public class Device {
    public virtual string GetStatus() {
        return "Device ready";
    }
}

public class Camera : Device {
    public override string GetStatus() {
        return "Camera ready, live preview available";
    }
}

// 运行时多态
Device device = new Camera();
Console.WriteLine(device.GetStatus()); // 输出: "Camera ready, live preview available"
```

---

## 抽象类 (Abstract Classes)

抽象类不能被实例化,用于定义派生类的共同基础。

```csharp
public abstract class ImageSource {
    public string Name { get; set; }

    // 抽象方法 - 必须由派生类实现
    public abstract byte[] CaptureImage();

    // 具体方法 - 可以有实现
    public void SaveImage(byte[] data, string path) {
        File.WriteAllBytes(path, data);
        Console.WriteLine($"Image saved to {path}");
    }
}

public class Camera : ImageSource {
    public override byte[] CaptureImage() {
        Console.WriteLine("Capturing from camera...");
        return new byte[1024]; // 模拟图像数据
    }
}

public class FileSource : ImageSource {
    public override byte[] CaptureImage() {
        Console.WriteLine("Loading from file...");
        return File.ReadAllBytes("test.jpg");
    }
}

// ImageSource source = new ImageSource(); // 错误!不能实例化抽象类
ImageSource camera = new Camera();
byte[] data = camera.CaptureImage();
```

### 抽象类 vs 接口
| 特性 | 抽象类 | 接口 |
|------|--------|------|
| 实例化 |  不能 |  不能 |
| 成员实现 |  可以有 | ⚠️ C# 8+ 可以有默认实现 |
| 多继承 |  单继承 |  可以实现多个接口 |
| 构造函数 |  可以有 |  不能有 |
| 访问修饰符 |  可以有 |  成员默认public |

**使用建议:**
- 抽象类: "是一个" (is-a) 关系,共享实现代码
- 接口: "能做什么" (can-do) 关系,定义能力契约

---

## 接口 (Interfaces)

定义了一组行为契约,类必须实现接口中的所有成员。

```csharp
public interface IImageCapture {
    byte[] CaptureImage();
    void StartLiveView();
    void StopLiveView();
}

public class Camera : IImageCapture {
    public byte[] CaptureImage() {
        return new byte[1024];
    }

    public void StartLiveView() {
        Console.WriteLine("Live view started");
    }

    public void StopLiveView() {
        Console.WriteLine("Live view stopped");
    }
}

// 使用接口类型
IImageCapture capture = new Camera();
capture.CaptureImage();
```

---

## 静态成员 (Static Members)

### 静态方法和属性
属于类本身,不属于任何实例。

```csharp
public class MathHelper {
    // 静态方法
    public static double CalculateArea(double width, double height) {
        return width * height;
    }

    // 静态属性
    public static double Pi => 3.14159;
}

// 直接通过类名调用
double area = MathHelper.CalculateArea(10, 20);
double pi = MathHelper.Pi;
```

### 静态类
不能被实例化,所有成员必须是static。

```csharp
public static class Logger {
    public static void Log(string message) {
        Console.WriteLine($"[{DateTime.Now}] {message}");
    }

    public static void Error(string message) {
        Console.WriteLine($"[ERROR] {message}");
    }
}

// 不能实例化静态类
// Logger log = new Logger(); // 错误!

// 直接调用
Logger.Log("System started");
Logger.Error("Connection failed");
```

### 扩展方法 (Extension Methods)
为已有类型添加新方法,无需修改原始代码。

```csharp
public static class StringExtensions {
    // 扩展方法 - 第一个参数使用 this
    public static bool IsValidIP(this string ip) {
        return System.Net.IPAddress.TryParse(ip, out _);
    }
}

// 使用扩展方法
string address = "192.168.1.100";
if (address.IsValidIP()) {
    Console.WriteLine("Valid IP address");
}
```

---

## Sealed 关键字

### Sealed 类
防止类被继承。

```csharp
public sealed class SecurityManager {
    public void Authenticate() {
        // 关键安全逻辑,不允许被重写
    }
}

// public class CustomSecurity : SecurityManager { } // 错误!不能继承 sealed 类
```

### Sealed 方法
防止派生类进一步重写已重写的方法。

```csharp
public class Device {
    public virtual void Connect() {
        Console.WriteLine("Device connecting...");
    }
}

public class Camera : Device {
    public sealed override void Connect() {
        Console.WriteLine("Camera connecting...");
    }
}

public class AdvancedCamera : Camera {
    // public override void Connect() { } // 错误!不能重写 sealed 方法
}
```

---

## 基于C#的工业软件 中的应用

### 1. 设备抽象层
```csharp
public abstract class IndustrialDevice {
    public string ID { get; set; }
    public abstract void Initialize();
    public abstract void Shutdown();

    public void LogStatus() {
        Console.WriteLine($"Device {ID} status logged");
    }
}

public class Camera : IndustrialDevice {
    public override void Initialize() {
        Console.WriteLine("Camera initializing: configuring exposure, gain...");
    }

    public override void Shutdown() {
        Console.WriteLine("Camera shutting down: releasing resources...");
    }
}
```

### 2. 配置管理

```csharp
public class AppConfig {
    // Init-only 属性确保配置在初始化后不可变
    public string CameraIP { get; init; }
    public int Port { get; init; }
    public double DetectionThreshold { get; init; }

    // 计算属性
    public string ConnectionString =>
        $"{CameraIP}:{Port}";
}

AppConfig config = new AppConfig {
    CameraIP = "192.168.1.100",
    Port = 8080,
    DetectionThreshold = 0.85
};
```

---

## 相关链接
- [C# 基础语法](./basic-syntax.md)
- [集合与泛型 (Collections and Generics)](./collections-and-generics.md)
- [方法与参数 (Methods and Parameters)](./methods-and-parameters.md)
- [值类型 vs 引用类型](./value-vs-reference-types.md)
