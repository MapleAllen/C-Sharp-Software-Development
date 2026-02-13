# 文件 I/O 与序列化 (File I/O and Serialization)

任何实用的软件都需要读取配置、保存数据。C# 通过 `System.IO` 命名空间提供了丰富的文件操作功能。

## 文件操作基础

常用类：`File`, `Directory`, `Path`。

### 1. 读写文本文件
```csharp
string path = "data.txt";

// 写入全部文本
File.WriteAllText(path, "Hello, World!");

// 读取全部文本
if (File.Exists(path)) {
    string content = File.ReadAllText(path);
    Console.WriteLine(content);
}
```

### 2. 路径处理
**永远不要**手动拼接路径字符串（如 `"C:\\" + folder + "\\" + file`），因为不同操作系统的路径分隔符不同。请使用 `Path.Combine`。

```csharp
string folder = "AppData";
string file = "config.ini";
string fullPath = Path.Combine(folder, file); // Windows: AppData\config.ini
```

---

## 序列化 (Serialization)

序列化是将对象转换为字符串（如 JSON）以便存储或传输的过程。反之则为反序列化 (Deserialization)。
在 .NET 开发中，最流行的库是 `Newtonsoft.Json` (Json.NET) 和微软自带的 `System.Text.Json`。

以 `Newtonsoft.Json` 为例：

```csharp
using Newtonsoft.Json;

public class Config {
    public string IP { get; set; }
    public int Port { get; set; }
}

// 1. 序列化 (对象 -> JSON 字符串)
Config myConfig = new Config { IP = "127.0.0.1", Port = 8080 };
string json = JsonConvert.SerializeObject(myConfig, Formatting.Indented);
File.WriteAllText("config.json", json);

// 2. 反序列化 (JSON 字符串 -> 对象)
string loadedJson = File.ReadAllText("config.json");
Config loadedConfig = JsonConvert.DeserializeObject<Config>(loadedJson);
```

---

## 基于C#的工业软件 中的数据持久化

在 `基于C#的工业软件` 中，I/O 操作是核心功能之一：

1.  **配置文件加载**：
    程序启动时，会读取 `appsettings.json` 来获取相机 IP、检测阈值等参数。如果文件不存在，程序会自动生成一个默认配置。

2.  **检测结果存档**：
    每次业务流程完成后，程序会将输出结果序列化为 JSON，并同原始数据一起保存到当天的文件夹中，方便追溯。

    ```csharp
    // 自动按日期创建文件夹
    string dailyFolder = Path.Combine("D:\\Data", DateTime.Now.ToString("yyyy-MM-dd"));
    Directory.CreateDirectory(dailyFolder);
    ```

---

## 流式 I/O (Stream-Based I/O)

### FileStream
直接操作字节流,提供更多控制。

```csharp
// 写入二进制数据
using (FileStream fs = new FileStream("data.bin", FileMode.Create)) {
    byte[] data = { 0x01, 0x02, 0x03, 0x04 };
    fs.Write(data, 0, data.Length);
}

// 读```csharp
using (FileStream fs = new FileStream("data.bin", FileMode.Open)) {
    byte[] buffer = new byte[fs.Length];
    fs.Read(buffer, 0, buffer.Length);
}
```

### StreamReader 和 StreamWriter
用于文本文件的高效读写。

```csharp
// StreamWriter - 写入文本
using (StreamWriter writer = new StreamWriter("log.txt", append: true)) {
    writer.WriteLine($"[{DateTime.Now}] System started");
    writer.WriteLine($"[{DateTime.Now}] Camera connected");
}

// StreamReader - 逐行读取
using (StreamReader reader = new StreamReader("config.txt")) {
    string line;
    while ((line = reader.ReadLine()) != null) {
        Console.WriteLine(line);
    }
}
```

### 何时使用流 vs File 静态方法

| 场景 | 推荐方式 |
|------|----------|
| 小文件一次性读写 | `File.ReadAllText()`, `File.WriteAllText()` |
| 大文件逐行处理 | `StreamReader`, `StreamWriter` |
| 二进制数据 | `FileStream` |
| 需要控制缓冲区 | `FileStream` |

---

## 目录操作 (Directory Operations)

### 基本目录操作
```csharp
using System.IO;

// 创建目录
string dailyFolder = @"D:\Data\2023-10-27";
if (!Directory.Exists(dailyFolder)) {
    Directory.CreateDirectory(dailyFolder);
}

// 删除目录
Directory.Delete(dailyFolder, recursive: true); // recursive: true 删除所有子目录和文件

// 移动目录
Directory.Move(@"D:\Data\Old", @"D:\Data\New");
```

### 枚举文件和目录
```csharp
// 获取所有文件
string[] imageFiles = Directory.GetFiles(@"D:\Images", "*.jpg");
foreach (string file in imageFiles) {
    Console.WriteLine(file);
}

// 获取所有子目录
string[] subDirs = Directory.GetDirectories(@"D:\Data");

// 递归搜索 (包括子目录)
string[] allJpgFiles = Directory.GetFiles(@"D:\Images", "*.jpg", SearchOption.AllDirectories);

// 使用 EnumerateFiles (内存友好,惰性加载)
foreach (string file in Directory.EnumerateFiles(@"D:\Images", "*.jpg")) {
    ProcessImage(file);
}
```

### 路径操作详解
```csharp
string folder = @"D:\Data";
string subfolder = "Images";
string filename = "image001.jpg";

// 组合路径
string fullPath = Path.Combine(folder, subfolder, filename);
// 结果: D:\Data\Images\image001.jpg

// 提取信息
string directory = Path.GetDirectoryName(fullPath);   // D:\Data\Images
string fileName = Path.GetFileName(fullPath);         // image001.jpg
string fileNameNoExt = Path.GetFileNameWithoutExtension(fullPath); // image001
string extension = Path.GetExtension(fullPath);       // .jpg

// 更改扩展名
string pngPath = Path.ChangeExtension(fullPath, ".png");
// 结果: D:\Data\Images\image001.png

// 临时文件路径
string tempFile = Path.GetTempFileName();
string tempPath = Path.GetTempPath();
```

---

## XML 序列化 (XML Serialization)

### 基本 XML 序列化
```csharp
using System.Xml.Serialization;

[Serializable]
public class CameraConfig {
    public string ID { get; set; }
    public string IPAddress { get; set; }
    public int Port { get; set; }
}

// 序列化到 XML
CameraConfig config = new CameraConfig {
    ID = "Camera_01",
    IPAddress = "192.168.1.100",
    Port = 8080
};

XmlSerializer serializer = new XmlSerializer(typeof(CameraConfig));
using (FileStream fs = new FileStream("camera.xml", FileMode.Create)) {
    serializer.Serialize(fs, config);
}

// 反序列化
using (FileStream fs = new FileStream("camera.xml", FileMode.Open)) {
    CameraConfig loadedConfig = (CameraConfig)serializer.Deserialize(fs);
}
```

### XML vs JSON 选择

| 特性 | JSON | XML |
|------|------|-----|
| 可读性 | ⭐⭐⭐⭐⭐ 极佳 | ⭐⭐⭐ 较好 |
| 文件大小 |  更小 |  较大 |
| 解析速度 |  更快 |  较慢 |
| 现代化 |  Web API 标准 | ⚠️ 传统 |
| 数据验证 | 需要 JSON Schema |  内置 XSD |

**推荐:**
-  JSON: 现代应用、Web API、配置文件
- ⚠️ XML: 与旧系统集成、需要严格schema验证

---

## 工业应用最佳实践

### 1. 按日期组织文件
```csharp
public string CreateDailyFolder() {
    string baseFolder = @"D:\ProductionData";
    string dailyFolder = Path.Combine(
        baseFolder,
        DateTime.Now.ToString("yyyy-MM-dd")
    );

    Directory.CreateDirectory(dailyFolder);
    return dailyFolder;
}

// 使用
string folder = CreateDailyFolder();
string imagePath = Path.Combine(folder, $"image_{DateTime.Now:HHmmss}.jpg");
```

### 2. 安全的文件写入 (原子操作)
```csharp
public void SaveConfigSafely(Config config) {
    string configPath = "config.json";
    string tempPath = configPath + ".tmp";

    try {
        // 先写入临时文件
        string json = JsonConvert.SerializeObject(config, Formatting.Indented);
        File.WriteAllText(tempPath, json);

        // 删除旧文件并重命名
        if (File.Exists(configPath)) {
            File.Delete(configPath);
        }
        File.Move(tempPath, configPath);
    } catch (Exception ex) {
        Log.Error(ex, "Failed to save config");
        // 清理临时文件
        if (File.Exists(tempPath)) {
            File.Delete(tempPath);
        }
        throw;
    }
}
```

### 3. 大文件分块处理
```csharp
public void ProcessLargeFile(string filePath) {
    const int BufferSize = 8192; // 8KB

    using (FileStream fs = new FileStream(filePath, FileMode.Open))
    using (BufferedStream bs = new BufferedStream(fs, BufferSize)) {
        byte[] buffer = new byte[BufferSize];
        int bytesRead;

        while ((bytesRead = bs.Read(buffer, 0, buffer.Length)) > 0) {
            // 处理数据块
            ProcessChunk(buffer, bytesRead);
        }
    }
}
```

---

## 相关链接
-   [异常处理 (I/O 异常)](./exception-handling.md)
-   [集合与泛型](./collections-and-generics.md)
-   [NuGet 包管理 (Newtonsoft.Json)](../02_Environment/nuget-package-management.md)
-   [日志流程 (Log Flow)](./log-flow.md)
-   [字符串操作](./string-manipulation.md)
