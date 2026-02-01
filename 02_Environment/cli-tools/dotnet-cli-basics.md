.NET CLI (Command Line Interface) 是现代 .NET 开发的核心工具。它跨平台且功能强大，是 CI/CD 流水线的基础。

## 环境验证 (Diagnostics)

在开始开发前，确保环境配置正确：
-   `dotnet --info`：显示当前 .NET 环境的详细信息（版本、OS、SDK 路径）。
-   `dotnet --list-sdks`：查看已安装的 SDK 列表。
-   `dotnet --list-runtimes`：查看已安装的运行时列表。

---

## 项目创建与管理 (Project Management)

### 常用模板命令
-   `dotnet new list`：列出所有可用的项目模板。
-   `dotnet new console -n MyApp`：创建新的控制台应用。
-   `dotnet new wpf -n MyWpfApp`：创建新的 WPF 应用 (仅限 Windows)。

### 解决方案操作
-   `dotnet new sln -n MySolution`：创建解决方案文件。
-   `dotnet sln MySolution.sln add MyApp/MyApp.csproj`：将项目添加到解决方案。

---

## 核心开发工作流 (Development Workflow)

### 1. 构建与编译
-   `dotnet build`：编译项目或解决方案。
-   `dotnet clean`：清理上一次构建生成的中间文件 (`obj/`, `bin/`)。

### 2. 运行与测试
-   `dotnet run`：构建并直接运行项目。
-   `dotnet watch run`：**热重载 (Hot Reload)** 模式，代码修改后自动重新构建并运行。
-   `dotnet test`：运行所有单元测试项目。

### 3. 发布与分发
-   `dotnet publish -c Release`：发布项目以供部署。生产环境必须使用 `-c Release`。
-   `dotnet publish -r win-x64 --self-contained`：发布单文件、自包含的可执行程序（无需目标机器安装 .NET 运行时）。

---

## SDK vs Runtime (核心区别)

| 组件 | 用途 | 包含内容 |
| :--- | :--- | :--- |
| **SDK** (Software Development Kit) | **开发人员使用** | 包含编译器、CLI 工具、运行时集。 |
| **Runtime** | **生产环境部署使用** | 仅包含运行程序所需的执行环境。 |

---

## 知识提取：WpfApp 中的命令行应用

虽然 `WpfApp` 主要是通过 Visual Studio 开发的，但在某些场景下依然可以使用 CLI：
-   **脚本调用**：项目中的 Python 脚本是通过 `Process.Start` 启动的。类似地，我们可以编写 C# 脚本或批处理文件，使用 `dotnet publish` 快速打包我们的 WPF 应用程序。

---

## 相关链接
-   [返回概览](../dotnet-framework-overview.md)
-   [NuGet 包管理](../nuget-package-management.md)