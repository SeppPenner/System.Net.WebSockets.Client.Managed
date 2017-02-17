# System.Net.WebSockets.Client.Managed
Microsoft's managed implementation of System.Net.WebSockets.ClientWebSocket tweaked for use on Windows 7 and .NET 4.5

---

From Microsoft's [ClientWebSocket documentation](https://msdn.microsoft.com/en-us/library/system.net.websockets.clientwebsocket(v=vs.110).aspx)
> Some of the classes and class elements in the System.Net.WebSockets namespace are supported on Windows 7, Windows Vista SP2, and Windows Server 2008. **However, the only public implementations of client and server WebSockets are supported on Windows 8 and Windows Server 2012.** The class elements in the System.Net.WebSockets namespace that are supported on Windows 7, Windows Vista SP2, and Windows Server 2008 are abstract class elements. This allows an application developer to inherit and extend these abstract class classes and class elements with an actual implementation of client WebSockets.

In other words: on a Windows 7 machine calling `new System.Net.WebSockets.ClientWebSocket()` throws a `PlatformNotSupportedException`. 

Thankfully Microsoft did implement that abstract class in managed code for use on non-Windows systems! But its only available for .NET 4.6+

This project is the managed System.Net.WebSockets.Client code with a few tweaks to work on .NET 4.5.

The code was taken from here (latest master branch commit 2017/02/17) https://github.com/dotnet/corefx/tree/baddea890ac483d465388acc10c5c91d6b8928bc/src/System.Net.WebSockets.Client

---

Most the tweaks required are in the added files `NET45Shims.cs` and `SR.cs`, with a few changes to the original source when extensions methods could not be leveraged (NET46-only properties and statics). 

The only actual NET 4.6+ features used were some Task helps (Task.FromException, Task.FromCanceled, Task.CompletedTask) and the Socket.ConnectAsync task. Microsoft could easily fix these and provide an official nuget package like this to support Win7 and .NET 4.5.

## Usage

`System.Net.WebSockets.SystemClientWebSocket` class has some helpers for easily creating a ClientWebSocket that will work on the current system. 

```cs
// Creates a ClientWebSocket that works for this platform. Uses System.Net.WebSockets.ClientWebSocket if supported or System.Net.WebSockets.Managed.ClientWebSocket if not.
public static WebSocket SystemClientWebSocket.CreateClientWebSocket() { ... }

// Creates and connects a ClientWebSocket that works for this platform. Uses System.Net.WebSockets.ClientWebSocket if supported or System.Net.WebSockets.Managed.ClientWebSocket if not.
public static async Task<WebSocket> ConnectAsync(Uri uri, CancellationToken cancellationToken)
```

If you know you want a managed instance than use `new System.Net.WebSockets.Managed.ClientWebSocket()` rather than `new System.Net.WebSockets.ClientWebSocket()`