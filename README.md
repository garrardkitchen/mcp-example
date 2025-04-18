
TO start, enter:

```bash
dotnet new console -n Mcp.Echo  
```

Now add the following dependencies to your project:

```sh
dotnet add package ModelContextProtocol --prerelease
dotnet add package Microsoft.Extensions.Hosting
```

```csharp {filename="Program.cs"}
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using ModelContextProtocol.Server;
using System.ComponentModel;

var builder = Host.CreateApplicationBuilder(args);
builder.Logging.AddConsole(consoleLogOptions =>
{
    // Configure all logs to go to stderr
    consoleLogOptions.LogToStandardErrorThreshold = LogLevel.Trace;
});

builder.Services
    .AddMcpServer()
    .WithStdioServerTransport()
    .WithToolsFromAssembly();

await builder.Build().RunAsync();
```

Add using:

```csharp
using Microsoft.Extensions.Logging;
```

Add

```csharp {filename="EchoTool.cs"}
using System.ComponentModel;
using ModelContextProtocol.Server;

[McpServerToolType]
public static class EchoTool
{
    [McpServerTool, Description("Echoes the message back to the client.")]
    public static string Echo(string message) => $"Hello from C#: {message}";

    [McpServerTool, Description("Echoes in reverse the message sent by the client.")]
    public static string ReverseEcho(string message) => new string(message.Reverse().ToArray());
}
```

Add a file `.vscode/mcp.json'

```json
{
    "inputs": [],
    "servers": {
        "MyFirstMCP": {
            "type": "stdio",
            "command": "dotnet",
            "args": [
                "run",
                "--project",
                "/Users/garrardkitchen/source/mcp/Mcp.Echo/Mcp.Echo.csproj"
            ]
        }
    }
}
```

We're adding it to the .vscode folder as we don't want to share this with more globally.

Now build it:

```bash
dotnet build
```

Then either (a) go to your mcp.json file and restart the server or (b) click the restart button in the bottom in the Agent view

![](2025-04-18-14-50-06.png)

The MCP server is now running.

In the agent view, enter 

![](2025-04-18-14-47-25.png)


I then entered:

```sh
run #ReverseEcho Garrard
```

![](2025-04-18-14-48-14.png)

And that's it, my first MCP server in C#!

Yay me!

## References

- https://devblogs.microsoft.com/dotnet/build-a-model-context-protocol-mcp-server-in-csharp/