C# 是 .NET 生态系统的核心编程语言，用于构建运行在 .NET 平台上的各种应用程序。

## .NET 演进史 (Evolution)

.NET 平台经历了从单一 Windows 平台到跨平台生态的重大转变：

1.  **.NET Framework (4.x 及更早)**：
    -   **平台**：仅限 Windows。
    -   **特点**：成熟稳定，与 Windows 系统深度集成（如 WPF, WinForms）。
    -   **现状**：处于维护模式，不再增加新特性。

2.  **.NET Core (1.0 - 3.1)**：
    -   **平台**：跨平台 (Windows, Linux, macOS)。
    -   **特点**：高性能、模块化、开源。

3.  **.NET 5+ (现代 .NET)**：
    -   **.NET 5/6/7/8/9**：统一了 .NET Framework 和 .NET Core，成为未来的统一平台。
    -   **LTS (Long Term Support)**：偶数版本（如 .NET 6, .NET 8）通常为长期支持版本。

> [!NOTE]
> 项目目前使用的是 **.NET Framework 4.7.2**，这在工业软件和旧版 WPF 开发中非常常见。

---

## .NET 架构 (Architecture)

无论哪个版本，.NET 的核心运行机制都围绕以下三个关键组件：

### 1. CLR (Common Language Runtime)
**公共语言运行时**。它是 .NET 的“发动机”，负责：
-   **内存管理**：通过 **GC (Garbage Collection)** 自动回收内存。
-   **代码执行**：管理受托管代码 (Managed Code) 的运行。
-   **安全性**：验证代码类型安全和权限。

### 2. BCL (Base Class Library)
**基类库**。预置的大量标准类，涵盖了文件 I/O、网络通信、数据结构（List, Dictionary）等基础功能。它是所有 .NET 语言（C#, VB.NET, F#）共享的底层库。

### 3. JIT (Just-In-Time Compilation)
**即时编译**。C# 代码首先被编译为 **IL (Intermediate Language)** 中间语言（存储在 .exe 或 .dll 中）。在程序运行时，JIT 编译器根据当前的硬件环境将 IL 编译为最终的**机器码 (Native Code)**。

---

## 集成开发环境 (IDE)

### 图形界面工具
1.  **Visual Studio**：功能最全的 IDE，开发 WPF (.NET Framework) 的首选。
2.  **JetBrains Rider**：基于 IntelliJ 平台的跨平台 .NET IDE，性能优秀。
3.  **Visual Studio Code**：轻量级编辑器，配合 C# Dev Kit 插件适合跨平台 .NET 开发。

### 命令行工具 (CLI)
现代 .NET 开发流程通常涉及 `dotnet` 命令行工具：
-   `dotnet new`：创建新项目。
-   `dotnet build`：构建项目。
-   `dotnet run`：运行项目。
-   `dotnet test`：运行单元测试。

---

## 生态关键组件

-   **NuGet**：.NET 生态的包管理器，用于引用第三方类库。
-   **Entity Framework (EF) Core**：主流的 **ORM** (对象关系映射) 框架，用于数据库操作。

---

## 相关链接
-   [运行时与类库详解](./runtime-and-libraries.md)
-   [项目结构基础](./project-structure-fundamentals.md)
-   [CLI 工具深入](./cli-tools/dotnet-cli-basics.md)