即.NET SDK (dotnet CLI), 全称 Software Development Kit，以下是常用指令及示例:

## 安装与验证
1. dotnet --info: 检查已安装的.NET版本
	一般来说.NET版本影响的是兼容性，构建速度以及版本特性

2. dotnet --list-sdks: 列出所有已安装的SDK
	SDK是开发工具包，在安装多个SDK时，默认使用最新版本

3. dotnet --list-runtimes: 检查运行时
	运行时(Runtime)是.NET应用程序运行所需的**执行环境**，如：Microsoft.AspNetCore.App 8.0.0，Microsoft.NETCore.App 8.0.0，Microsoft.WindowsDesktop.App 6.0.20

## 项目创建
1. dotnet new list: 查看所有可用模版
![[Pasted image 20260118201609.png]]

### 创建流程实例  ***个人财务管理应用 "FinanceTracker"***
**首先创建项目框架**
- dotnet new sln -n FinanceTracker
	创建解决方案，-n(-name)是用来指定名称的，如果不使用此参数，dotnet new sln将创建名为"Solution1.sln"的解决方案文件（每次名称递增）
- dotnet new classlib -n FinanceTracker.Core
- dotnet sln add  FinanceTracker.Core/FinanceTracker.Core.csproj
	创建核心领域项目，classlib是 class library的缩写，用于创建**类库项目**；
	将指定的项目文件（.csproj）添加到解决方案文件（.sln）中，建立项目与解决方案的关联关系
**重复以上步骤，直至创建完Core, Data, Api, Cli, Tests这五个类**
**然后添加项目引用:**
![[Pasted image 20260118211533.png]]
项目引用(Project Reference)的作用是：在项目之间建立依赖关系，使一个项目能够使用另一个项目的代码和功能
**构建与编译**
- dotnet build: 在解决方案根目录执行
- dotnet build FinanceTracker.sln: 明确指定解决方案文件
**以上指令会找到解决方案中所有项目，分析项目依赖图，按照依赖顺序逐个编译**
**以下是手动逐个编译的命令示例（不推荐）**
- dotnet build FinanceTracker.Core.csproj
	编译类库项目，生成.dll文件（动态链接库），而不是.exe（可执行文件）
**以下是其他编译命令:**
![[Pasted image 20260118212617.png]]