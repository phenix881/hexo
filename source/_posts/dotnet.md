---
title: "c#/dotnet基础命令，常见问题和使用技巧"
date: 2024-02-22 10:38:05
tags:
    - "C#"
    - ".NET"
    - "DOTNET"
categories:
    - "编程教程"
---

# 基础命令
## 检查 SDK 版本
`dotnet --list-sdks`
```
C:\Users\Administrator> dotnet --list-sdks
6.0.400 [C:\Program Files\dotnet\sdk]
8.0.200 [C:\Program Files\dotnet\sdk]
9.0.100-preview.1.24101.2 [C:\Program Files\dotnet\sdk]
```
如需删除所有list中的版本可用
`dotnet-core-uninstall list`
>查询版本的意义在于，dotnet项目根目录配置global.json版本信息时，需匹配list中的版本号，否则编译过程会报错找不到对应的sdk

## 其他命令

| 命令 | 函数 |
|:------|:-------| 
dotnet  build  | 生成 .NET 应用程序。
dotnet build-server	 | 与通过生成启动的服务器进行交互。
dotnet clean	 | 清除生成输出。
dotnet exec | 	运行 .NET 应用程序。
dotnet help	 | 显示命令更详细的在线文档。
dotnet migrate | 	将有效的预览版 2 项目迁移到 .NET Core SDK 1.0 项目。
dotnet msbuild | 	提供对 MSBuild 命令行的访问权限。
dotnet new | 	为给定的模板初始化 C# 或 F# 项目。
dotnet pack | 	创建代码的 NuGet 包。
dotnet publish | 	发布 .NET 依赖于框架或独立应用程序。
dotnet restore | 	还原给定应用程序的依赖项。
dotnet run | 	从源运行应用程序。
dotnet sdk check | 	显示已安装 SDK 和运行时版本的最新状态。
dotnet sln | 	用于添加、删除和列出解决方案文件中项目的选项。
dotnet store | 	将程序集存储到运行时包存储区。
dotnet test | 	使用测试运行程序运行测试。

## 示例
- 创建新的 .NET 控制台应用程序：
`dotnet new console`

- 生成给定目录中的项目及其依赖项：
`dotnet build`

- 运行应用程序：
`dotnet exec myapp.dll`
`dotnet myapp.dll`