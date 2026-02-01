# 项目结构基础 (Project Structure)

在 .NET 体系中，代码的组织遵循一套标准化的层级结构。

## 组织层级

### 1. 解决方案 (Solution, .sln)
-   **定义**：解决方案是顶级容器，用于组织一个或多个相关的项目。
-   **文件格式**：`.sln` 是一个文本文件，记录了项目中包含的所有 `.csproj` 文件路径。
-   **实战**：`WpfApp.sln` 位于仓库根目录，统一管理所有组件。

### 2. 项目 (Project, .csproj)
-   **定义**：项目是逻辑和物理的边界。一个项目通常编译成一个二进制文件（`.exe` 或 `.dll`）。
-   **文件格式**：`.csproj` 是基于 XML 的文件，定义了：
    -   目标框架（如 `<TargetFramework>net472</TargetFramework>`）。
    -   项目依赖。
    -   包含的文件和资源。

---

## 构建输出目录 (Build Artifacts)

当你点击 "Build" 或运行 `dotnet build` 时，系统会生成两个关键文件夹：

### 1. `obj` 文件夹 (Intermediate)
-   **作用**：存放编译过程中的**中间文件**（如缓存、生成的代码）。
-   **注意**：这个文件夹通常很大，但不需要提交到 Git。

### 2. `bin` 文件夹 (Binary)
-   **作用**：存放最终编译出的**二进制可执行文件**。
-   **常用子目录**：
    -   `bin/Debug`：包含调试信息，适合开发阶段。
    -   `bin/Release`：经过代码优化，不含调试信息，适合分发。

---

## 程序集 (Assembly)

.NET 编译的最终产物被称为程序集：
-   **.dll (Dynamic Link Library)**：类库，无法直接运行，只能被引用。
-   **.exe (Executable)**：包含入口点 (Main 方法)，可以直接运行的程序。

> [!TIP]
> 在 WPF 开发中，主项目通常生成 `.exe`，而复杂的业务逻辑服务通常建议放在单独的类库项目中（生成 `.dll`），以便复用。

---

## 知识提取：WpfApp 的目录规范

参考 `WpfApp` 的 `README.md`，该项目采用了典型的企业级结构：
-   `src/`：存放所有源代码项目。
-   `packages/`：存放旧版 .NET Framework 的 NuGet 依赖包。
-   `output/`：项目自定义的运行时输出，区别于 `bin/`。

---

## 相关链接
-   [返回概览](./dotnet-framework-overview.md)
-   [NuGet 包管理](./nuget-package-management.md)
