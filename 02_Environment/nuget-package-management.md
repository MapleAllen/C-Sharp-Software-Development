# NuGet 包管理 (Package Management)

NuGet 是 .NET 生态系统的官方映射包管理器，类似于 Python 的 `pip` 或 Node.js 的 `npm`。

## 什么是 NuGet？

NuGet 允许开发者共享和使用第三方类库（DLLs）。
-   **NuGet Gallery (nuget.org)**：全球最大的 .NET 包仓库。
-   **依赖管理**：自动处理包的相互依赖关系。

---

## 依赖声明方式

随着 .NET 的演进，声明依赖的方式主要有两种：

### 1. PackageReference (现代方式)
-   **位置**：直接写在 `.csproj` 文件中。
-   **优势**：管理简洁，支持传递依赖。
-   **示例**：
    ```xml
    <ItemGroup>
      <PackageReference Include="Newtonsoft.Json" Version="13.0.1" />
    </ItemGroup>
    ```

### 2. packages.config (旧版方式)
-   **位置**：一个独立的 `packages.config` XML 文件。
-   **实战**：在 `WpfApp` 这样的 .NET Framework 4.7.2 项目中，通常能看到这个文件。它将包下载到解决方案根目录的 `packages/` 文件夹中。

---

## 常用操作

### 1. 使用 Visual Studio (GUI)
-   右键点击项目 -> **Manage NuGet Packages**。
-   搜索、安装、卸载和更新包。

### 2. 使用命令行 (CLI)
-   **添加包**：`dotnet add package Newtonsoft.Json`
-   **移除包**：`dotnet remove package Newtonsoft.Json`
-   **还原包**：`dotnet restore` (通常 `build` 时会自动还原)

---

## 知识提取：WpfApp 中的依赖

在 `基于C#的工业软件` 中，关键依赖包括：
-   **Newtonsoft.Json**：用于解析项目配置文件（`.json`）。
-   **PDFsharp**：用于生成检测报告 PDF。
-   **Npgsql**：如果启用了数据库入库功能，用于连接 PostgreSQL。

---

## 相关链接
-   [返回概览](./dotnet-framework-overview.md)
-   [CLI 工具参考](./cli-tools/dotnet-cli-basics.md)
