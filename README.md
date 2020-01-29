ProcessX
===
[![CircleCI](https://circleci.com/gh/Cysharp/ProcessX.svg?style=svg)](https://circleci.com/gh/Cysharp/ProcessX)

ProcessX simplify call an external process with the aync streams in C# 8.0. You can receive standard output results by `await foreach`, it is completely asynchronous and realtime.

```csharp
using Cysharp.Diagnostics; // using namespace

await foreach (var item in ProcessX.StartAsync("dotnet build --help"))
{
    Console.WriteLine(item);
}
```

Getting Started
---
Install library from NuGet that support from `.NET Standard 2.0`.

> PM> Install-Package [ProcessX](https://www.nuget.org/packages/ProcessX)

Main API is only `Cysharp.Diagnostics.ProcessX.Start` and throws `ProcessErrorException` when error detected.

* **Simple**, only write single string command like the shell script.
* **Asynchronous**, by C# 8.0 async streams.
* **Manage Error**, handling exitcode and stderror.

```csharp
// async iterate.
await foreach (string item in ProcessX.StartAsync("dotnet --info"))
{
    Console.WriteLine(item);
}

// receive buffered result(similar as WaitForExit).
string[] result = await ProcessX.StartAsync("dotnet --info").AsTask();

// when ExitCode is not 0 or ErrorOutput is exists, throws ProcessErrorException
try
{
    await foreach (var item in ProcessX.StartAsync("dotnet --foo --bar")) { }
}
catch (ProcessErrorException ex)
{
    // int .ExitCode
    // string[] .ErrorOutput
    Console.WriteLine(ex.ToString());
}
```

Reference
---
to Cancel, you can use `WithCancellationToken`.

```csharp
await foreach (var item in ProcessX.StartAsync("dotnet --info").WithCancellation(cancellationToken))
{
    Console.WriteLine(item);
}
```

timeout, you can use `CancellationTokenSource(delay)`.

```csharp
using (var tcs = new CancellationTokenSource(TimeSpan.FromSeconds(1)))
{
    await foreach (var item in ProcessX.StartAsync("dotnet --info").WithCancellation(tcs.Token))
    {
        Console.WriteLine(item);
    }
}
```

ProcessX.StartAsync overloads, you can set workingDirectory, environmentVariable, encoding.

```csharp
StartAsync(string command, string? workingDirectory = null, IDictionary<string, string>? environmentVariable = null, Encoding? encoding = null)
StartAsync(string fileName, string? arguments, string? workingDirectory = null, IDictionary<string, string>? environmentVariable = null, Encoding? encoding = null)
StartAsync(ProcessStartInfo processStartInfo)
```

Competitor
---
[Tyrrrz/CliWrap](https://github.com/Tyrrrz/CliWrap) - Wrapper for command line interfaces.
[jamesmanning/RunProcessAsTask](https://github.com/jamesmanning/RunProcessAsTask) - Simple wrapper around System.Diagnostics.Process to expose it as a System.Threading.Tasks.Task.

License
---
This library is under the MIT License.