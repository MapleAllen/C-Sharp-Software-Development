# .NET 运行时与类库 (Runtime & Libraries)

深入理解 .NET 的运行机制对于编写高性能、稳定的 C# 程序至关重要。

## CLR (Common Language Runtime)

CLR 是 .NET 程序的执行引擎。所有的 C# 代码最终都由 CLR 托管运行。

### 核心职责
-   **受托管代码执行 (Managed Code Execution)**：管理 IL 代码到机器码的转换。
-   **垃圾回收 (Garbage Collection, GC)**：
    -   **自动内存管理**：开发者无需手动释放内存（对比 C++）。
    -   **代数机制 (Generations)**：分为 Gen 0 (短生命周期), Gen 1, Gen 2 (长生命周期)，优化回收效率。
-   **异常处理**：提供统一的 `try-catch` 机制。
-   **类型安全**：确保对象不会非法访问内存。

### 托管代码 (Managed) vs 非托管代码 (Unmanaged)
-   **Managed Code**：编写在 .NET 框架内的代码，由 CLR 管理生命周期。
-   **Unmanaged Code**：直接调用系统底层 API 或 C++ DLL（通过 P/Invoke）。开发者需要负责这类代码的资源释放。

---

## BCL (Base Class Library)

BCL 是 .NET 提供的庞大类库集合，作为开发的基础。

### 常用命名空间 (Namespaces)
-   `System`：核心基础类（`String`, `DateTime`, `Console`）。
-   `System.Collections.Generic`：泛型集合（`List<T>`, `Dictionary<K, V>`）。
-   `System.IO`：文件读写与流处理。
-   `System.Linq`：强大的语言集成查询。
-   `System.Threading.Tasks`：异步编程核心 (`Task`, `async/await`)。

---

## .NET Standard

**.NET Standard** 是一套正式的 .NET API 规范，旨在解决不同平台（.NET Framework, .NET Core, Xamarin）之间的兼容性问题。

-   **作用**：如果你编写一个类库并目标选为 `.NET Standard 2.0`，那么这个库既可以被 `.NET Framework 4.7.2` 项目引用，也可以被 `.NET 8` 项目引用。
-   **现状**：随着 .NET 5/6+ 的统一，新项目通常直接目标定为特定的 .NET 版本，但维护跨版本类库时 .NET Standard 依然重要。

---

## 知识提取：WpfApp 中的应用

在 `WpfApp` 项目中，我们可以看到这些概念的实际体现：
-   **托管与非托管**：主程序是托管的，但调用 Python 脚本或 FFmpeg 属于跨进程调用（与非托管环境交互）。
-   **BCL 使用**：广泛使用了 `System.IO` 处理视频文件路径，使用 `System.Diagnostics` 中的 `Process` 类启动外部工具。

---

## 相关链接
-   [返回概览](./dotnet-framework-overview.md)
-   [项目结构基础](./project-structure-fundamentals.md)
