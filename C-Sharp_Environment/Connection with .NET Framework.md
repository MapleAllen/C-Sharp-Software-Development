C# 是 .Net框架的一部分，用于编写.Net应用程序

## .NET Framework
用于编写以下应用程序：
1. Windows 应用程序 （如：Microsoft Visual Studio, QQ等），此类软件功能强大，可直接操作电脑的文件、摄像头和麦克风

2. Web 应用程序 （如：知乎，携程旅游网等），此类软件无需安装，输入网址即可使用

3. Web 服务 （如：微信支付的“后台计算器”，天气预报App的数据来源），此类软件没有页面，通过标准网络协议（主要是HTTP)来提供API接口，供其他应用程序调用

*需要注意的是，.NET Framework主要支持Windows平台的开发，.NET Core/.NET 5+ 支持跨平台开发，后者更通用*

## 集成开发环境
### 图像开发界面
1. Visual Studio
2. Visual Studio Code
3. JetBrains Rider

### 命令行编译
1. .NET SDK: 最核心的命令行编译工具，包含:
	- dotnet new
	- dotnet build
	- dotnet run
	- dotnet publish
	- dotnet test
	- dotnet add package
	- dotnet remove package
	- dotnet list package
	- dotnet restore
	- dotnet sln 
	
2. MSBuild: 传统的.NET构建系统

3. C# Interactive(CSI): 交互式C#环境，类似于在命令行输入"pytyon"以进行快速代码测试，只不过这里使用"csi"进行快速启动

4. PowerShell: 强大的脚本环境，可自动化编译流程

5. Azure DevOps Pipeline中的命令行编译: 在CI/CD流水线中使用命令行编译

## 包管理器 NuGet
.NET生态系统的包管理器

## 数据库ORM - Entity Framework Core
对象关系映射框架，简化数据库操作

## 云服务平台
微软的DevOps平台，提供完整的开发运维工具链，构建CI/CD流水线