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