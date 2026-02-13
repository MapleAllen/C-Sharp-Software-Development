# 委托与事件 (Delegates and Events)

委托和事件是 C# 中实现事件驱动编程的核心机制,在 WPF 和工业软件开发中广泛使用。

## 什么是委托? (What are Delegates?)

委托是类型安全的函数指针,可以引用一个或多个具有相同签名的方法。

### 声明和使用委托

```csharp
// 1. 声明委托类型
public delegate void ProcessImageDelegate(string imagePath);

// 2. 创建方法匹配委托签名
public void ProcessWithFilter(string imagePath) {
    Console.WriteLine($"Processing {imagePath} with filter");
}

public void ProcessWithAI(string imagePath) {
    Console.WriteLine($"Processing {imagePath} with AI");
}

// 3. 使用委托
ProcessImageDelegate processor = ProcessWithFilter;
processor("image001.jpg"); // 调用 ProcessWithFilter

// 更换委托引用的方法
processor = ProcessWithAI;
processor("image002.jpg"); // 调用 ProcessWithAI
```

### 多播委托 (Multicast Delegates)
一个委托可以引用多个方法。

```csharp
ProcessImageDelegate processor = ProcessWithFilter;
processor += ProcessWithAI; // 添加第二个方法

processor("image003.jpg");
// 依次调用:
// 1. ProcessWithFilter
// 2. ProcessWithAI
```

---

## 内置委托类型 (Built-in Delegates)

C# 提供了通用的内置委托类型,避免每次都声明自定义委托。

### Action\<T>
无返回值的委托。

```csharp
// Action - 无参数
Action logAction = () => Console.WriteLine("Logging...");
logAction();

// Action<T> - 一个参数
Action<string> logMessage = (msg) => Console.WriteLine(msg);
logMessage("System started");

// Action<T1, T2> - 两个参数
Action<string, int> logWithCode = (msg, code) => {
   Console.WriteLine($"[{code}] {msg}");
};
logWithCode("Error occurred", 500);
```

### Func\<T, TResult>
有返回值的委托,最后一个类型参数是返回值类型。

```csharp
// Func<TResult> - 无参数,有返回值
Func<int> getRandomNumber = () => new Random().Next(100);
int number = getRandomNumber();

// Func<T, TResult> - 一个参数,有返回值
Func<double, double> square = (x) => x * x;
double result = square(5.0); // 25.0

// Func<T1, T2, TResult> - 两个参数,有返回值
Func<int, int, int> add = (a, b) => a + b;
int sum = add(10, 20); // 30
```

### Predicate\<T>
返回 bool 的委托,常用于过滤。

```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4, 5, 6 };

// Predicate<T>
Predicate<int> isEven = (n) => n % 2 == 0;
List<int> evenNumbers = numbers.FindAll(isEven); // [2, 4, 6]
```

---

## 事件 (Events)

事件是基于委托的发布-订阅模式实现,用于对象间的松耦合通信。

### 基本事件定义和使用

```csharp
// 发布者 (Publisher)
public class Camera {
    // 1. 声明事件
    public event EventHandler ImageCaptured;

    public void CaptureImage() {
        Console.WriteLine("Capturing image...");

        // 2. 触发事件
        ImageCaptured?.Invoke(this, EventArgs.Empty);
    }
}

// 订阅者 (Subscriber)
public class ImageProcessor {
    public void OnImageCaptured(object sender, EventArgs e) {
        Console.WriteLine("Processing captured image");
    }
}

// 使用
Camera camera = new Camera();
ImageProcessor processor = new ImageProcessor();

// 3. 订阅事件
camera.ImageCaptured += processor.OnImageCaptured;

// 触发事件
camera.CaptureImage();
// 输出:
// Capturing image...
// Processing captured image
```

### 使用自定义 EventArgs

```csharp
// 1. 定义自定义事件参数
public class ImageCapturedEventArgs : EventArgs {
    public string ImagePath { get; set; }
    public DateTime CaptureTime { get; set; }
    public int FrameNumber { get; set; }
}

// 2. 声明事件,使用 EventHandler<T>
public class Camera {
    public event EventHandler<ImageCapturedEventArgs> ImageCaptured;

    public void CaptureImage(string path) {
        Console.WriteLine($"Capturing to {path}...");

        // 创建事件参数
        ImageCapturedEventArgs args = new ImageCapturedEventArgs {
            ImagePath = path,
            CaptureTime = DateTime.Now,
            FrameNumber = 1024
        };

        // 触发事件
        ImageCaptured?.Invoke(this, args);
    }
}

// 3. 订阅者访问事件数据
public class ImageProcessor {
    public void OnImageCaptured(object sender, ImageCapturedEventArgs e) {
        Console.WriteLine($"Processing: {e.ImagePath}");
        Console.WriteLine($"Captured at: {e.CaptureTime}");
        Console.WriteLine($"Frame: {e.FrameNumber}");
    }
}

// 使用
Camera camera = new Camera();
ImageProcessor processor = new ImageProcessor();
camera.ImageCaptured += processor.OnImageCaptured;
camera.CaptureImage(@"D:\Images\image001.jpg");
```

---

## 委托 vs 事件

| 特性 | 委托 | 事件 |
|------|------|------|
| 外部触发 |  可以 |  只能类内部触发 |
| 外部赋值 |  可以 |  只能 += 和 -= |
| 封装性 | ⚠️ 较弱 |  更强 |
| 使用场景 | 回调函数 | 对象间通信 |

**最佳实践:** 对外公开的通知机制使用事件,内部回调使用委托。

---

## 基于C#的工业软件 中的应用

### 1. 相机连接状态事件

```csharp
public class Camera {
    // 连接状态变化事件
    public event EventHandler<ConnectionStatusEventArgs> ConnectionStatusChanged;

    private bool _isConnected;

    public bool IsConnected {
        get => _isConnected;
        private set {
            if (_isConnected != value) {
                _isConnected = value;
                OnConnectionStatusChanged(value);
            }
        }
    }

    protected virtual void OnConnectionStatusChanged(bool isConnected) {
        ConnectionStatusChanged?.Invoke(this, new ConnectionStatusEventArgs {
            IsConnected = isConnected,
            Timestamp = DateTime.Now
        });
    }

    public void Connect() {
        // 连接逻辑...
        IsConnected = true;
    }

    public void Disconnect() {
        // 断开逻辑...
        IsConnected = false;
    }
}

public class ConnectionStatusEventArgs : EventArgs {
    public bool IsConnected { get; set; }
    public DateTime Timestamp { get; set; }
}

// UI 层订阅
Camera camera = new Camera();
camera.ConnectionStatusChanged += (sender, e) => {
    string status = e.IsConnected ? "已连接" : "已断开";
    Console.WriteLine($"[{e.Timestamp}] 相机状态: {status}");
    UpdateStatusIndicator(e.IsConnected); // 更新 UI 显示
};
```

### 2. 检测结果回调

```csharp
public class DefectDetector {
    // 使用 Action 作为回调
    public void DetectAsync(string imagePath, Action<List<Defect>> onCompleted) {
        Task.Run(() => {
            // 执行检测...
            List<Defect> defects = PerformDetection(imagePath);

            // 调用回调
            onCompleted?.Invoke(defects);
        });
    }
}

// 使用
DefectDetector detector = new DefectDetector();
detector.DetectAsync("image001.jpg", (defects) => {
    Console.WriteLine($"Found {defects.Count} defects");
    foreach (var defect in defects) {
        Console.WriteLine($"- {defect.Type}: {defect.Confidence:P0}");
    }
});
```

### 3. UI 按钮事件处理 (WPF)

```csharp
// XAML
// <Button x:Name="CaptureButton" Click="CaptureButton_Click">Capture</Button>

// Code-behind
private void CaptureButton_Click(object sender, RoutedEventArgs e) {
    try {
        camera.CaptureImage();
        StatusText.Text = "Image captured successfully";
    } catch (Exception ex) {
        MessageBox.Show($"Capture failed: {ex.Message}", "Error");
    }
}
```

### 4. 多个订阅者模式 (观察者模式)

```csharp
public class ProductionLine {
    public event EventHandler<DefectDetectedEventArgs> DefectDetected;

    public void InspectProduct(string productId) {
        // 检测逻辑...
        bool hasDefect = CheckForDefects(productId);

        if (hasDefect) {
            DefectDetectedEventArgs args = new DefectDetectedEventArgs {
                ProductID = productId,
                DefectType = "Scratch",
                Severity = "High"
            };
            DefectDetected?.Invoke(this, args);
        }
    }
}

// 多个订阅者
ProductionLine line = new ProductionLine();

// 订阅者 1: 记录日志
line.DefectDetected += (sender, e) => {
    Log.Warning($"Defect in {e.ProductID}: {e.DefectType}");
};

// 订阅者 2: 发送警报
line.DefectDetected += (sender, e) => {
    if (e.Severity == "High") {
        SendAlert($"High severity defect detected: {e.ProductID}");
    }
};

// 订阅者 3: 更新统计
line.DefectDetected +=  (sender, e) => {
    defectStatistics.Increment(e.DefectType);
};
```

---

## 最佳实践 (Best Practices)

### 1. 安全触发事件
始终检查事件是否为 null。

```csharp
//  不安全 - 如果没有订阅者会抛出 NullReferenceException
ImageCaptured.Invoke(this, EventArgs.Empty);

//  安全方式 1 - 使用 ?. 运算符
ImageCaptured?.Invoke(this, EventArgs.Empty);

//  安全方式 2 - 先复制引用
EventHandler<ImageCapturedEventArgs> handler = ImageCaptured;
handler?.Invoke(this, args);
```

### 2. 取消事件订阅
避免内存泄漏,不再需要时取消订阅。

```csharp
public class MainWindow {
    private Camera _camera;

    public MainWindow() {
        _camera = new Camera();
        _camera.ImageCaptured += OnImageCaptured;
    }

    // 清理资源
    protected override void OnClosed(EventArgs e) {
        _camera.ImageCaptured -= OnImageCaptured;
        base.OnClosed(e);
    }

    private void OnImageCaptured(object sender, EventArgs e) {
        // 处理事件
    }
}
```

### 3. 使用lambda 表达式简化订阅
```csharp
// 简短事件处理可以使用 lambda
camera.ImageCaptured += (sender, e) => {
    Console.WriteLine("Image captured");
};

// 但要注意:lambda 无法取消订阅,除非保存引用
EventHandler<EventArgs> handler = (sender, e) => Console.WriteLine("Captured");
camera.ImageCaptured += handler;
// ...later
camera.ImageCaptured -= handler;
```

---

## 相关链接
- [C# 基础语法](./basic-syntax.md)
- [面向对象编程 (OOP)](./object-oriented-programming.md)
- [异步编程 (Async/Await)](./async-await-basics.md)
- [方法与参数](./methods-and-parameters.md)
