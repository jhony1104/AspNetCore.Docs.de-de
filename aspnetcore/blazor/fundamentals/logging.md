---
title: ASP.NET Core Blazor-Protokollierung
author: guardrex
description: Erfahren Sie mehr über die Protokollierung in Blazor-Apps, einschließlich der Konfiguration auf Protokollebene und des Schreibens von Protokollmeldungen von Razor-Komponenten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
ms.openlocfilehash: b0448d5f6e5e16a726eb2274dcacb4dfa8314b5d
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103248"
---
# <a name="aspnet-core-blazor-logging"></a>ASP.NET Core Blazor-Protokollierung

## <a name="blazor-webassembly"></a>Blazor WebAssembly

Konfigurieren Sie die Protokollierung in Blazor WebAssembly-Apps mit der `WebAssemblyHostBuilder.Logging`-Eigenschaft in `Program.Main`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

Die `Logging`-Eigenschaft ist vom Typ <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, deshalb sind alle auf <xref:Microsoft.Extensions.Logging.ILoggingBuilder> verfügbaren Erweiterungsmethoden auch auf `Logging` verfügbar.

Die Protokollierungskonfiguration kann aus App-Einstellungsdateien geladen werden. Weitere Informationen finden Sie unter <xref:blazor/fundamentals/logging>.

## <a name="blazor-server"></a>Blazor Server

Einen allgemeinen Leitfaden zur Protokollierung in ASP.NET Core finden Sie unter <xref:fundamentals/logging/index>.

## <a name="blazor-webassembly-signalr-net-client-logging"></a>SignalR-.NET-Clientprotokollierung in Blazor WebAssembly

Fügen Sie eine <xref:Microsoft.Extensions.Logging.ILoggerProvider>-Instanz ein, um `WebAssemblyConsoleLogger` zu den an <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> weitergegebenen Protokollierungsanbietern hinzuzufügen. Anders als bei einem herkömmlichen <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> handelt es sich bei `WebAssemblyConsoleLogger` um einen Wrapper um browserspezifische Protokollierungs-APIs (z. B. `console.log`). Die Verwendung von `WebAssemblyConsoleLogger` ermöglicht die Protokollierung in Mono in einem Browserkontext.

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a>Protokoll in Razor-Komponenten

Protokollierungen beachten die Startkonfiguration der App.

Die `using`-Direktive für <xref:Microsoft.Extensions.Logging> ist erforderlich, um die IntelliSense-Vervollständigungen für APIs zu unterstützen, zum Beispiel <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> und <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.

Im folgenden Beispiel wird die Protokollierung mit <xref:Microsoft.Extensions.Logging.ILogger> in Razor-Komponenten veranschaulicht:

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

Im folgenden Beispiel wird die Protokollierung mit <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor-Komponenten veranschaulicht:

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/logging/index>
