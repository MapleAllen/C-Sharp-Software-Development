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

## 相关链接
-   [异常处理 (I/O 异常)](./exception-handling.md)
-   [集合与泛型](./collections-and-generics.md)
-   [NuGet 包管理 (Newtonsoft.Json)](../02_Environment/nuget-package-management.md)
-   [日志流程 (Log Flow)](./log-flow.md)
