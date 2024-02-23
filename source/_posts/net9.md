---
title: ".NET9发布了,了解一下更新了什么？"
date: 2024-02-21 10:00:05
tags:
    - "C#"
    - ".NET9"
categories:
    - "编程教程"
excerpt: "来关注一下2024刚发行的.net9预览版有哪些好玩的新内容"
---

## 版本信息
版本号信息如下：
- Full version
- 9.0.100-preview.1.24101.2
- Visual Studio support
- Visual Studio 2022 (v17.10 Preview 1)
- 官方下载地址:[Download .NET 9.0 ](https://dotnet.microsoft.com/en-us/download/dotnet/9.0)
## 升级步骤
- 安装官方的sdk和runtime两个包
- 升级本地的visual studio，我自己vs的版本是2022，17.8.7，要连续更新，只能升级到17.9.0
- 所以还得重新下载10的安装工具，下载地址:[visualstudio17.10.0](https://learn.microsoft.com/en-us/visualstudio/releases/2022/release-notes-preview)
- <img src="/images/cutimg1.png" width = "500" height = "200"/>
- 修改项目的global.json如下
``` json
{
    "sdk": {
      "version": "9.0.100"
    }
}

```
- 然后打开vs在各项目的属性中，选择.net9.0即可完成升级

>以下新特性文档，摘自微软官方
## 序列化

在 System.Text.Json 中，.NET 9 具有用于序列化 JSON 的新选项和新的单一实例，可以更轻松地使用 Web 默认值进行序列化。

## 缩进选项

JsonSerializerOptions包含一些新属性，可用于自定义写入 JSON 的缩进字符和缩进大小。


```c#
var options = new JsonSerializerOptions
{
    WriteIndented = true,
    IndentCharacter = '\t',
    IndentSize = 2,
};

string json = JsonSerializer.Serialize(
    new { Value = 1 },
    options
    );
Console.WriteLine(json);
//{
//                "Value": 1
//}
```


## 默认 Web 选项

如果要使用 ASP.NET Core 用于 Web 应用的默认选项进行序列化，请使用新的 JsonSerializerOptions.Web 单一实例。

```c#
string webJson = JsonSerializer.Serialize(
    new { SomeValue = 42 },
    JsonSerializerOptions.Web // Defaults to camelCase naming policy.
    );
Console.WriteLine(webJson);
// {"someValue":42}
```

## LINQ的

引入了新方法CountBy和AggregateBy这些方法可以按键聚合状态，而无需通过 GroupBy

CountBy 可让您快速计算每个键的频率。下面的示例查找文本字符串中最常出现的单词。

```C#
string sourceText = """
    Lorem ipsum dolor sit amet, consectetur adipiscing elit.
    Sed non risus. Suspendisse lectus tortor, dignissim sit amet, 
    adipiscing nec, ultricies sed, dolor. Cras elementum ultrices amet diam.
""";

// Find the most frequent word in the text.
KeyValuePair<string, int> mostFrequentWord = sourceText
    .Split(new char[] { ' ', '.', ',' }, StringSplitOptions.RemoveEmptyEntries)
    .Select(word => word.ToLowerInvariant())
    .CountBy(word => word)
    .MaxBy(pair => pair.Value);

Console.WriteLine(mostFrequentWord.Key); // amet
```

AggregateBy允许您实现更多通用工作流。以下示例演示如何计算与给定键关联的分数。

```C#
(string id, int score)[] data =
    [
        ("0", 42),
        ("1", 5),
        ("2", 4),
        ("1", 10),
        ("0", 25),
    ];

var aggregatedData =
    data.AggregateBy(
        keySelector: entry => entry.id,
        seed: 0,
        (totalScore, curr) => totalScore + curr.score
        );

foreach (var item in aggregatedData)
{
    Console.WriteLine(item);
}
//(0, 67)
//(1, 15)
//(2, 4)

///Index<TSource>（IEnumerable<TSource>）可以快速提取可枚举的隐式索引。现在，您可以编写如下代码片段，以自动为集合中的项编制索引。

```c#
IEnumerable<string> lines2 = File.ReadAllLines("output.txt");
foreach ((int index, string line) in lines2.Index())
{
    Console.WriteLine($"Line number: {index + 1}, Line: {line}");
}
```

## 密码学

对于加密，.NET 9 在 CryptographicOperations类型上添加了新的一次性哈希方法。它还添加了使用 KMAC 算法的新类。

### CryptographicOperations.HashData（） 方法

.NET 包括哈希函数和相关函数的多个静态“一次性”实现。这些 API 包括 SHA256。HashData 和 HMACSHA256。哈希数据。最好使用一次性 API，因为它们可以提供最佳性能并减少或消除分配。

如果开发人员想要提供支持哈希的 API，其中调用方定义要使用的哈希算法，则通常通过接受 HashAlgorithmName 参数来完成。但是，将该模式与一次性 API 一起使用需要切换每个可能的 HashAlgorithmName，然后使用适当的方法。为了解决此问题，.NET 9 引入了 CryptographicOperations.HashData API。此 API 允许您一次性生成输入的哈希或 HMAC，其中使用的算法由 HashAlgorithmName 确定。


```c#
static void HashAndProcessData(HashAlgorithmName hashAlgorithmName, byte[] data)
{
    byte[] hash = CryptographicOperations.HashData(hashAlgorithmName, data);
    ProcessHash(hash);
}
```


### KMAC算法

.NET 9 提供 NIST SP-800-185指定的 KMAC 算法。KECCAK 消息验证码 （KMAC） 是一个基于 KECCAK 的伪随机函数和键控哈希函数。

以下新类使用 KMAC 算法。使用实例累积数据以生成 MAC，或使用静态方法对单个输入进行一次性处理。`HashData`

- [KMAC128型](https://learn.microsoft.com/en-us/dotnet/api/system.security.cryptography.kmac128)
- [KMAC256型](https://learn.microsoft.com/en-us/dotnet/api/system.security.cryptography.kmac256)
- [KmacXof128](https://learn.microsoft.com/en-us/dotnet/api/system.security.cryptography.kmacxof128)
- [KmacXof256](https://learn.microsoft.com/en-us/dotnet/api/system.security.cryptography.kmacxof256)

KMAC 在具有 OpenSSL 3.0 或更高版本的 Linux 以及 Windows 11 Build 26016 或更高版本上可用。可以使用 static 属性来确定平台是否支持所需的算法。`IsSupported`


```c#
if (Kmac128.IsSupported)
{
    byte[] key = GetKmacKey();
    byte[] input = GetInputToMac();
    byte[] mac = Kmac128.HashData(key, input, outputLength: 32);
}
else
{
    // Handle scenario where KMAC isn't available.
}
```

## 反射

在 .NET Core 版本和 .NET 5-8 中，对生成程序集和为动态创建的类型发出反射元数据的支持仅限于可运行的 AssemblyBuilder。缺乏对_保存_程序集的支持通常是客户从 .NET Framework 迁移到 .NET 的障碍。.NET 9 将公共 API 添加到 AssemblyBuilder以保存发出的程序集。

新的持久化 AssemblyBuilder

```c#
public void CreateAndSaveAssembly(string assemblyPath)
{
    AssemblyBuilder ab = AssemblyBuilder.DefinePersistedAssembly(
        new AssemblyName("MyAssembly"),
        typeof(object).Assembly
        );
    TypeBuilder tb = ab.DefineDynamicModule("MyModule")
        .DefineType("MyType", TypeAttributes.Public | TypeAttributes.Class);

    MethodBuilder mb = tb.DefineMethod(
        "SumMethod",
        MethodAttributes.Public | MethodAttributes.Static,
        typeof(int), [typeof(int), typeof(int)]
        );
    ILGenerator il = mb.GetILGenerator();
    il.Emit(OpCodes.Ldarg_0);
    il.Emit(OpCodes.Ldarg_1);
    il.Emit(OpCodes.Add);
    il.Emit(OpCodes.Ret);

    tb.CreateType();
    ab.Save(assemblyPath); // or could save to a Stream
}

public void UseAssembly(string assemblyPath)
{
    Assembly assembly = Assembly.LoadFrom(assemblyPath);
    Type type = assembly.GetType("MyType");
    MethodInfo method = type.GetMethod("SumMethod");
    Console.WriteLine(method.Invoke(null, [5, 10]));
}
```