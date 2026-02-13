# 异步编程基础 (Async/Await Basics)

异步编程是现代 C# 应用的核心,尤其在 UI 应用中,可以保持界面响应,避免卡顿。

## 为什么需要异步? (Why Async?)

### 同步 vs 异步

```csharp
//  同步方式 - 阻塞 UI 线程
public void LoadData_Sync() {
    StatusText.Text = "Loading..."; // UI 更新
    Thread.Sleep(5000); // 模拟耗时操作,UI 会冻结 5 秒
    StatusText.Text = "Loaded"; // UI 更新
}

//  异步方式 - UI 保持响应
public async Task LoadData_Async() {
    StatusText.Text = "Loading...";
    await Task.Delay(5000); // UI 线程被释放,可以处理其他事件
    StatusText.Text = "Loaded";
}
```

**关键区别:**
- 同步:主线程被阻塞,UI 无响应
- 异步:主线程空闲时可以处理其他任务,UI 保持流畅

---

## Task 和 Task\<T>

`Task` 表示一个异步操作。

### Task - 无返回值
```csharp
public Task SaveImageAsync(string path) {
    return Task.Run(() => {
        // 模拟文件保存
        Thread.Sleep(2000);
        File.WriteAllBytes(path, new byte[1024]);
        Console.WriteLine("Image saved");
    });
}

// 使用
await SaveImageAsync("image001.jpg");
```

### Task\<T> - 有返回值
```csharp
public Task<int> CalculateDefectCountAsync(string imagePath) {
    return Task.Run(() => {
        // 模拟图像分析
        Thread.Sleep(3000);
        return 5; // 返回检测到的缺陷数
    });
}

// 使用
int count = await CalculateDefectCountAsync("image001.jpg");
Console.WriteLine($"Found {count} defects");
```

### Task.Run - CPU 密集型工作
```csharp
public async Task ProcessImageAsync(string imagePath) {
    // 将 CPU 密集型工作转移到后台线程
    byte[] result = await Task.Run(() => {
        return ApplyHeavyFilter(imagePath); // CPU 密集型操作
    });

    // 回到 UI 线程更新界面
    DisplayResult(result);
}
```

---

## async 和 await 关键字

### async 关键字
标记方法为异步方法,允许使用 `await`。

```csharp
// async 方法签名
public async Task ConnectToCameraAsync() {
    await Task.Delay(1000);
    Console.WriteLine("Connected");
}

// async 方法可以有返回值
public async Task<string> GetCameraStatusAsync() {
    await Task.Delay(500);
    return "Ready";
}
```

### await 关键字
等待异步操作完成,期间会释放当前线程。

```csharp
public async Task PerformInspectionAsync() {
    Console.WriteLine("Step 1: Connecting to camera...");
    await ConnectToCameraAsync(); // 等待连接完成

    Console.WriteLine("Step 2: Capturing image...");
    byte[] image = await CaptureImageAsync(); // 等待图像采集

    Console.WriteLine("Step 3: Analyzing image...");
    var defects = await AnalyzeImageAsync(image); // 等待分析完成

    Console.WriteLine($"Inspection complete. Found {defects.Count} defects.");
}
```

---

## 异步方法命名约定

**约定:** 异步方法名称以 `Async` 结尾。

```csharp
//  正确命名
public async Task<string> LoadConfigAsync() { ... }
public async Task SaveDataAsync() { ... }

//  不推荐
public async Task<string> LoadConfig() { ... } // 缺少 Async 后缀
```

---

## 常见异步模式

### 1. I/O 密集型操作

```csharp
public async Task<string> ReadFileAsync(string path) {
    using (StreamReader reader = new StreamReader(path)) {
        return await reader.ReadToEndAsync(); // 异步读取
    }
}

public async Task WriteFileAsync(string path, string content) {
    using (StreamWriter writer = new StreamWriter(path)) {
        await writer.WriteAsync(content); // 异步写入
    }
}
```

### 2. 网络请求

```csharp
public async Task<string> FetchDataFromAPIAsync(string url) {
    using (HttpClient client = new HttpClient()) {
        string json = await client.GetStringAsync(url);
        return json;
    }
}
```

### 3. 并行执行多个异步任务

```csharp
public async Task ProcessMultipleCamerasAsync() {
    // 同时启动所有相机连接
    Task task1 = ConnectCameraAsync("Camera_01");
    Task task2 = ConnectCameraAsync("Camera_02");
    Task task3 = ConnectCameraAsync("Camera_03");

    // 等待所有任务完成
    await Task.WhenAll(task1, task2, task3);
    Console.WriteLine("All cameras connected");
}

// 或者获取所有结果
public async Task<int> GetTotalDefectCountAsync() {
    Task<int> count1 = CountDefectsAsync("image001.jpg");
    Task<int> count2 = CountDefectsAsync("image002.jpg");
    Task<int> count3 = CountDefectsAsync("image003.jpg");

    int[] counts = await Task.WhenAll(count1, count2, count3);
    return counts.Sum();
}
```

---

## 异步最佳实践 (Best Practices)

### 1. 避免 async void
**只有事件处理器可以使用 async void**。其他情况使用 `async Task`。

```csharp
//  错误 - 异常无法被捕获,难以测试
public async void SaveDataBadAsync() {
    await File.WriteAllTextAsync("data.txt", "content");
}

//  正确 - 返回 Task
public async Task SaveDataAsync() {
    await File.WriteAllTextAsync("data.txt", "content");
}

//  例外 - 事件处理器可以使用 async void
private async void Button_Click(object sender, RoutedEventArgs e) {
    try {
        await SaveDataAsync();
    } catch (Exception ex) {
        MessageBox.Show(ex.Message);
    }
}
```

### 2. Async All the Way
一旦使用异步,整个调用链都应该是异步的。

```csharp
//  错误 - 混合同步和异步
public void LoadData() {
    var task = LoadDataAsync();
    task.Wait(); // 阻塞调用,可能导致死锁
}

//  正确 - 全程异步
public async Task LoadDataAsync() {
    await FetchFromDatabaseAsync();
    await ProcessDataAsync();
}
```

### 3. ConfigureAwait(false) - 库代码优化
在类库代码中使用 `ConfigureAwait(false)` 提升性能。

```csharp
// 应用程序代码 - 需要回到 UI 线程
public async Task UpdateUIAsync() {
    var data = await LoadDataAsync(); // 默认回到 UI 线程
    TextBox.Text = data; // 更新 UI
}

// 类库代码 - 不需要特定上下文
public async Task<string> LoadDataAsync() {
    using (HttpClient client = new HttpClient()) {
        // 不需要回到原始上下文,提升性能
        return await client.GetStringAsync(url).ConfigureAwait(false);
    }
}
```

### 4. 异常处理

```csharp
public async Task LoadAndProcessAsync() {
    try {
        byte[] data = await LoadImageAsync();
        await ProcessImageAsync(data);
    } catch (FileNotFoundException ex) {
        Log.Error($"Image file not found: {ex.FileName}");
    } catch (HttpRequestException ex) {
        Log.Error($"Network error: {ex.Message}");
    } catch (Exception ex) {
        Log.Fatal(ex, "Unexpected error during image processing");
        throw; // 重新抛出
    }
}
```

---

## 基于C#的工业软件 中的应用

### 1. 异步加载配置

```csharp
public class AppConfigLoader {
    public async Task<AppConfig> LoadConfigAsync() {
        string path = "appsettings.json";

        if (!File.Exists(path)) {
            return GetDefaultConfig();
        }

        try {
            // 异步读取文件
            string json = await File.ReadAllTextAsync(path);

            // CPU 密集型的 JSON 解析放到后台线程
            var config = await Task.Run(() =>
                JsonConvert.DeserializeObject<AppConfig>(json)
            );

            Log.Information("Configuration loaded successfully");
            return config;
        } catch (Exception ex) {
            Log.Error(ex, "Failed to load configuration");
            return GetDefaultConfig();
        }
    }
}
```

### 2. 非阻塞图像处理

```csharp
public class ImageProcessingService {
    public async Task<List<Defect>> ProcessImageAsync(string imagePath) {
        try {
            // 1. 异步读取图像
            byte[] imageData = await File.ReadAllBytesAsync(imagePath);

            // 2. CPU 密集型分析在后台线程
            List<Defect> defects = await Task.Run(() => {
                return DefectDetectionAlgorithm.Analyze(imageData);
            });

            // 3. 异步保存结果
            await SaveResultsAsync(defects, imagePath);

            return defects;
        } catch (Exception ex) {
            Log.Error(ex, $"Failed to process image: {imagePath}");
            throw;
        }
    }

    private async Task SaveResultsAsync(List<Defect> defects, string imagePath) {
        string resultPath = Path.ChangeExtension(imagePath, ".result.json");
        string json = JsonConvert.SerializeObject(defects, Formatting.Indented);
        await File.WriteAllTextAsync(resultPath, json);
    }
}
```

### 3. 异步相机连接

```csharp
public class CameraController {
    public async Task<bool> ConnectAsync(string ipAddress, int timeout = 5000) {
        try {
            // 使用超时的异步操作
            using (var cts = new CancellationTokenSource(timeout)) {
                await Task.Run(async () => {
                    // 模拟连接握手
                    await Task.Delay(1000, cts.Token);
                    // 实际相机SDK调用...
                }, cts.Token);
            }

            Log.Information($"Camera connected: {ipAddress}");
            return true;
        } catch (OperationCanceledException) {
            Log.Warning($"Camera connection timeout: {ipAddress}");
            return false;
        } catch (Exception ex) {
            Log.Error(ex, $"Failed to connect to camera: {ipAddress}");
            return false;
        }
    }
}
```

### 4. WPF 按钮异步事件处理

```csharp
private async void StartInspectionButton_Click(object sender, RoutedEventArgs e) {
    try {
        // 禁用按钮防止重复点击
        StartInspectionButton.IsEnabled = false;
        StatusText.Text = "Inspection in progress...";

        // 异步执行检测
        var defects = await inspectionService.RunInspectionAsync();

        // 更新 UI
        ResultsListBox.ItemsSource = defects;
        StatusText.Text = $"Inspection complete. Found {defects.Count} defects.";
    } catch (Exception ex) {
        MessageBox.Show($"Inspection failed: {ex.Message}", "Error");
        StatusText.Text = "Inspection failed";
    } finally {
        // 重新启用按钮
        StartInspectionButton.IsEnabled = true;
    }
}
```

---

## 常见陷阱 (Common Pitfalls)

### 1. 死锁 (Deadlock)
```csharp
//  导致死锁
public void DeadlockExample() {
    var task = GetDataAsync();
    string result = task.Result; // 阻塞等待,可能死锁
}

//  正确方式
public async Task NoDeadlockAsync() {
    string result = await GetDataAsync();
}
```

### 2. Fire and Forget (发射后忘记)
```csharp
//  错误 - 异常会被忽略
public void BadFireAndForget() {
    _ = SaveDataAsync(); // 异常无法捕获
}

//  正确 - 明确处理
public async void GoodFireAndForget() {
    try {
        await SaveDataAsync();
    } catch (Exception ex) {
        Log.Error(ex, "Background save failed");
    }
}
```

---

## 相关链接
- [委托与事件 (Delegates and Events)](./delegates-and-events.md)
- [异常处理 (Exception Handling)](./exception-handling.md)
- [文件 I/O](./file-io-and-serialization.md)
- [LINQ 基础](./linq-basics.md)
