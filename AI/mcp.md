# MCP

mcp客户端：cursor，cline，windsurf， claude app  就是负责发起请求与服务器通信的

## 通过cline配置server
1、配置客户端 cline使用：

![2025-03-26-21-51-44.png](./images/2025-03-26-21-51-44.png)

2、配置server环境 node.js：

https://nodejs.org/zh-cn/download


![2025-03-26-21-53-48.png](./images/2025-03-26-21-53-48.png)

3、安装mcp server
1）通过cline的mcp servers安装 (github)

![2025-03-26-22-09-43.png](./images/2025-03-26-22-09-43.png)

mac:
- ![2025-03-26-22-17-54.png](./images/2025-03-26-22-17-54.png)

window:
- ![2025-03-26-22-19-51.png](./images/2025-03-26-22-19-51.png)

![2025-03-26-22-58-39.png](./images/2025-03-26-22-58-39.png)

![2025-03-26-23-04-14.png](./images/2025-03-26-23-04-14.png)


2）通过配置
https://github.com/modelcontextprotocol/servers

直接在json文件配置然后刷新就好了。

![2025-03-26-23-52-47.png](./images/2025-03-26-23-52-47.png)

![2025-03-26-23-57-31.png](./images/2025-03-26-23-57-31.png)


本质就是“函数调用”，首先有一个客户端，客户端结合语言模型（gpt，deepseek）去理解用户输入的语言，解析成命令去查找配置中合适的server。server定义了一系列api，客户端调用对应的api获取信息或执行某个操作。

而mcp是制定了一套规范，规定了server开发暴露的接口的样式，这样可以接入客户端使用。

目前mcp server只会在本地运行，比如上面例子是通过node.js包运行server代码的。

![2025-03-27-00-12-28.png](./images/2025-03-27-00-12-28.png)

## mcp server开发 （C#）

```
# C#构建mcp server或client
dotnet add package ModelContextProtocol --preview
# 构建长时间运行的服务应用程序(如后台服务、微服务等)
dotnet add package Microsoft.Extensions.Hosting
```

Program.cs
```cs
using Microsoft.Extensions.Hosting;
using ModelContextProtocol;

// 创建一个空的应用程序构建器
var builder = Host.CreateEmptyApplicationBuilder(settings: null);

builder.Services.AddMcpServer() // 添加mcp服务
    .WithStdioServerTransport() // 配置服务器使用标准输入/输出（stdio）作为传输方式
    .WithToolsFromAssembly(); // 从当前程序集中加载工具

// 构建，初始化注册的mcp服务
var app = builder.Build();

// 启动mcp服务
await app.RunAsync();
```

编写实际操作逻辑：
```cs
[McpToolType]
public class MockCedarPackage
{
    [McpTool, Description("get rocks shape in cedar package.")]
    public static async Task<string> GetRocksShape(string color)
    {
        switch (color)
        {
            case "red":
                return "it is shape of flame";
            case "green":
                return "it is shape of grass";
            case "grey":
                return "it is normal rock";
            default:
                return "it is not rock";
        }
    }
    
}
```

到cline加上启动server的配置：
```cs
"McpServerTest": {
    "command": "dotnet",
    "args": [
    "run",
    "--project",
    "D:\\Cedar\\WorkPlace\\learning-code\\CSharp\\mcp-server-test\\mcp-server-test",
    "--no-build"
    ],
    "disabled": false,
    "autoApprove": []
}
```

![2025-03-27-22-42-16.png](./images/2025-03-27-22-42-16.png)

![2025-03-27-22-41-40.png](./images/2025-03-27-22-41-40.png)

