---
title: Protokollieren in .NET Core und ASP.NET Core
author: rick-anderson
description: In diesem Artikel wird das Verwenden des Protokollierungsframeworks erläutert, das vom Microsoft.Extensions.Logging-NuGet-Paket bereitgestellt wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/04/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 49d598330948c5f4a137c534094e14ed5e01e27c
ms.sourcegitcommit: f4cd3828e26e6d549ba8d0c36a17be35ad9e5a51
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74825490"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>Protokollieren in .NET Core und ASP.NET Core

Von [Tom Dykstra](https://github.com/tdykstra) und [Steve Smith](https://ardalis.com/)

.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet. Dieser Artikel zeigt, wie Sie die Protokollierungs-API mit integrierten Anbietern verwenden können.

::: moniker range=">= aspnetcore-3.0"

Die meisten der Codebeispiele in diesem Artikel stammen aus ASP.NET Core-Apps. Die auf die Protokollierung bezogenen Teile dieser Codeausschnitte gelten für jede .NET Core-App, die den [generischen Host](xref:fundamentals/host/generic-host) verwendet. Weitere Informationen zum Verwenden des generischen Hosts in Nicht-Webkonsolen-Apps finden Sie unter [Gehostete Dienste](xref:fundamentals/host/hosted-services).

Das Protokollieren von Code für Apps ohne generischen Host weicht in Bezug auf das [Hinzufügen von Anbietern](#add-providers) und das [Erstellen von Protokollierungen](#create-logs) ab. Codebeispiele ohne Host finden sich in diesen Abschnitten des Artikels.

::: moniker-end

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="add-providers"></a>Hinzufügen von Anbietern

Ein Protokollierungsanbieter zeigt Protokolle an oder speichert diese. Beispielsweise zeigt der Konsolenanbieter Protokolle in der Konsole an, und der Azure Application Insights-Anbieter speichert sie in Azure Application Insights. Protokolle können durch das Hinzufügen mehrerer Anbieter an mehrere Ziele gesendet werden.

::: moniker range=">= aspnetcore-3.0"

Um einen Anbieter in einer App hinzuzufügen, die den generischen Host verwendet, rufen Sie die `Add{provider name}`-Erweiterungsmethode des Anbieters in *Program.cs* auf:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

Rufen Sie in einer Konsolen-App ohne Host die `Add{provider name}`-Erweiterungsmethode des Anbieters auf, während Sie eine `LoggerFactory` erstellen:

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory` und `AddConsole` erfordern eine `using`-Anweisung für `Microsoft.Extensions.Logging`.

Die ASP.NET-Standardprojektvorlagen rufen die Methode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> auf, die die folgenden Protokollierungsanbieter hinzufügt:

* [Konsole](#console-provider)
* [Debuggen](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider) (nur bei Ausführung unter Windows)

Sie können die Standardanbieter durch Ihre eigene Auswahl ersetzen. Rufen Sie <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> auf, und fügen Sie die gewünschten Anbieter hinzu.

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

Um einen Anbieter hinzuzufügen, rufen Sie die `Add{provider name}`-Erweiterungsmethode des Anbieters in *Program.cs* auf:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

Der vorstehende Code erfordert Verweise auf `Microsoft.Extensions.Logging` und `Microsoft.Extensions.Configuration`.

Die Standardprojektvorlage ruft die Methode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> auf, die die folgenden Protokollierungsanbieter hinzufügt:

* Konsole
* Debug
* EventSource (beginnend mit ASP.NET Core 2.2)

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

Bei Verwendung von `CreateDefaultBuilder` können Sie die Standardanbieter durch Ihre eigene Auswahl ersetzen. Rufen Sie <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> auf, und fügen Sie die gewünschten Anbieter hinzu.

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

Informationen zu den [integrierten Protokollierungsanbietern](#built-in-logging-providers) sowie zu [Protokollierungsanbietern von Drittanbietern](#third-party-logging-providers) finden Sie weiter unten in diesem Artikel.

## <a name="create-logs"></a>Erstellen von Protokollen

Verwenden Sie zum Erstellen von Protokollen ein <xref:Microsoft.Extensions.Logging.ILogger%601>-Objekt. Rufen Sie in einer Web-App oder einem gehosteten Dienst einen `ILogger` aus der Abhängigkeitsinjektion ab. Verwenden Sie in Konsolen-Apps ohne Host `LoggerFactory`, um einen `ILogger` zu erstellen.

Im folgenden ASP.NET Core-Beispiel wird eine Protokollierung mit `TodoApiSample.Pages.AboutModel` als Kategorie erstellt. Die Protokoll*kategorie* ist eine Zeichenfolge, die jedem Protokoll zugeordnet ist. Die von der Abhängigkeitsinjektion bereitgestellte `ILogger<T>`-Instanz erstellt Protokolle, die den vollqualifizierten Namen vom Typ `T` als Kategorie aufweisen. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

Im folgenden Beispiel einer Konsolen-App ohne Host wird eine Protokollierung mit `LoggingConsoleApp.Program` als Kategorie erstellt.

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

In den folgenden Beispielen zu ASP.NET Core und einer Konsolen-App werden von der Protokollierung Protokolle mit dem Protokolliergrad `Information` erstellt. Der Protokollierungs*grad* gibt den Schweregrad des protokollierten Ereignisses an. 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

[Grade](#log-level) und [Kategorien](#log-category) werden weiter unten in diesem Artikel ausführlicher erläutert. 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a>Erstellen von Protokollen in der Program-Klasse

Wenn Protokolle in die `Program`-Klasse einer ASP.NET Core-App geschrieben werden sollen, rufen Sie nach dem Erstellen des Hosts eine `ILogger`-Instanz aus der Abhängigkeitsinjektion ab:

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

Die Protokollierung während der Hosterstellung wird nicht direkt unterstützt. Es kann jedoch eine separate Protokollierung verwendet werden. Im folgenden Beispiel wird eine [Serilog](https://serilog.net/)-Protokollierung zum Anmelden von `CreateHostBuilder` verwendet. `AddSerilog` verwendet die in `Log.Logger` angegebene statische Konfiguration:

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a>Erstellen von Protokollen in der Startup-Klasse

Wenn Protokolle in die `Startup.Configure`-Methode einer ASP.NET Core-App geschrieben werden sollen, schließen Sie einen `ILogger`-Parameter in die Methodensignatur ein:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

Das Schreiben von Protokollen vor Abschluss der Einrichtung des Abhängigkeitsinjektion-Containers in der `Startup.ConfigureServices`-Methode wird nicht unterstützt:

* Die Protokollierungsinjektion in den `Startup`-Konstruktor wird nicht unterstützt.
* Die Protokollierungsinjektion in die `Startup.ConfigureServices`-Methodensignatur wird nicht unterstützt.

Dies Einschränkung ist darauf zurückzuführen, dass die Protokollierung von der Abhängigkeitsinjektion und der Konfiguration abhängt (die wiederum von der Abhängigkeitsinjektion abhängt). Der Abhängigkeitsinjektion-Container wird erst nach Fertigstellung von `ConfigureServices` eingerichtet.

Die Konstruktorinjektion einer Protokollierung in `Startup` funktioniert in früheren Versionen von ASP.NET Core, da für den Webhost ein separater Abhängigkeitsinjektion-Container erstellt wird. Weitere Informationen dazu, warum nur ein Container für den generischen Host erstellt wird, erhalten Sie in der [Breaking Change-Ankündigung](https://github.com/aspnet/Announcements/issues/353).

Wenn Sie einen Dienst konfigurieren müssen, der von `ILogger<T>` abhängt, können Sie dies immer noch mithilfe der Konstruktorinjektion oder durch Bereitstellen einer Factorymethode ausführen. Die Nutzung einer Factorymethode wird nur empfohlen, wenn Sie keine andere Wahl haben. Angenommen, Sie müssen eine Eigenschaft mit einem Dienst aus der Abhängigkeitsinjektion füllen:

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

Der hervorgehobene Code oben ist eine `Func`, die ausgeführt wird, wenn der Abhängigkeitsinjektion-Container eine Instanz von `MyService` erstellen muss. Auf diese Weise können Sie auf alle registrierten Dienste zugreifen.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a>Erstellen von Protokollen in der Startklasse

Zum Schreiben von Protokollen in der `Startup`-Klasse schließen Sie einen `ILogger`-Parameter in die Konstruktorsignatur ein:

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>Erstellen von Protokollen in der Program-Klasse

Zum Schreiben von Protokollen in der `Program`-Klasse rufen Sie eine `ILogger`-Instanz aus DI ab:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

Die Protokollierung während der Hosterstellung wird nicht direkt unterstützt. Es kann jedoch eine separate Protokollierung verwendet werden. Im folgenden Beispiel wird eine [Serilog](https://serilog.net/)-Protokollierung zum Anmelden von `CreateWebHostBuilder` verwendet. `AddSerilog` verwendet die in `Log.Logger` angegebene statische Konfiguration:

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a>Keine asynchronen Protokollierungsmethoden

Die Protokollierung sollte so schnell erfolgen, dass sich die Leistungskosten von asynchronem Code nicht lohnen. Wenn Ihr Protokollierungsdatenspeicher langsam ist, schreiben Sie nicht direkt in ihn. Erwägen Sie, die Protokollnachrichten zunächst in einen schnellen Speicher zu schreiben und sie dann später in den langsamen Speicher zu verschieben. Wenn Sie beispielsweise in SQL Server protokollieren, möchten Sie dies nicht direkt in einer `Log`-Methode tun, da die `Log`-Methoden synchron sind. Fügen Sie stattdessen die Protokollmeldungen synchron zu einer Warteschlange im Arbeitsspeicher hinzu, und rufen Sie mithilfe eines Hintergrundworkers die Nachrichten aus der Warteschlange ab, um sie dann asynchron per Pushvorgang an SQL Server zu übertragen. Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/aspnet/AspNetCore.Docs/issues/11801).

## <a name="configuration"></a>Konfiguration

Die Konfiguration von Protokollierungsanbietern erfolgt durch mindestens einen Konfigurationsanbieter:

* Dateiformate (INI, JSON und XML)
* Befehlszeilenargumenten
* Umgebungsvariablen.
* Speicherinterne .NET-Objekte
* Der unverschlüsselte Speicher von [Secret Manager](xref:security/app-secrets).
* Ein unverschlüsselter Benutzerspeicher, z.B. [Azure Key Vault](xref:security/key-vault-configuration).
* Benutzerdefinierte Anbieter (installiert oder erstellt).

Die Konfiguration der Protokollierung wird meist vom `Logging`-Abschnitt von Anwendungseinstellungsdateien angegeben. Im folgenden Beispiel werden die Inhalte einer herkömmlichen *appsettings.Development.json*-Datei veranschaulicht:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

Die `Logging`-Eigenschaft kann den `LogLevel` und Protokollanbietereigenschaften beinhalten (Konsole wird angezeigt).

Die `LogLevel`-Eigenschaft unter `Logging` gibt den Mindest[grad](#log-level) an, der für ausgewählte Kategorien protokolliert werden soll. Im Beispiel protokollieren die Kategorien `System` und `Microsoft` mit dem Grad `Information` und alle anderen Kategorien mit dem Grad `Debug`.

Weitere Eigenschaften unter `Logging` geben Protokollierungsanbieter an. Das Beispiel bezieht sich auf den Konsolenanbieter. Wenn ein Anbieter [Protokollbereiche](#log-scopes) unterstützt, gibt `IncludeScopes` an, ob sie aktiviert sind. Eine Anbietereigenschaft (z.B. `Console` im Beispiel) kann auch eine `LogLevel`-Eigenschaft angeben. `LogLevel` unter einem Anbieter gibt die Grade an, die für diesen Anbieter protokolliert werden sollen.

Wenn Grade in `Logging.{providername}.LogLevel` angegeben werden, überschreiben sie alle Angaben in `Logging.LogLevel`.

Die Protokollierungs-API umfasst kein Szenario zum Ändern der Protokollebene, während eine App ausgeführt wird. Einige Konfigurationsanbieter können jedoch die Konfiguration erneut laden, was sich unmittelbar auf die Protokollierungskonfiguration auswirkt. Beispielsweise lädt der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider), der durch `CreateDefaultBuilder` zum Lesen von Einstellungsdateien hinzugefügt wird, die Protokollierungskonfiguration standardmäßig erneut. Wenn die Konfiguration im Code geändert wird, während eine App ausgeführt wird, kann die App [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) aufrufen, um ihre Protokollierungskonfiguration zu aktualisieren.

Informationen zur Implementierung von Konfigurationsanbieter finden Sie hier: <xref:fundamentals/configuration/index>.

## <a name="sample-logging-output"></a>Beispiel einer Protokollierungsausgabe

Mit dem im vorherigen Abschnitt gezeigten Beispielcode werden Protokolle in der Konsole angezeigt, wenn die Anwendung über die Befehlszeile ausgeführt wird. Hier ein Beispiel für eine Konsolenausgabe:

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

::: moniker-end

Die vorherigen Protokolle wurden generiert, indem eine HTTP Get-Anforderung an die Beispiel-App unter `http://localhost:5000/api/todo/0` vorgenommen wurde.

Nachfolgend sehen Sie, wie die gleichen Protokolle im Debugfenster angezeigt werden, wenn Sie die Beispiel-App in Visual Studio ausführen:

::: moniker range=">= aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

Die über die `ILogger`-Aufrufe aus dem vorherigen Abschnitt erstellten Protokolle beginnen mit „TodoApiSample“. Protokolle, die mit Microsoft-Kategorien beginnen, stammen aus ASP.NET Core-Frameworkcode. ASP.NET Core und Anwendungscode verwenden dieselbe Protokollierungs-API und dieselben Protokollierungsanbieter.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

Die über die `ILogger`-Aufrufe aus dem vorherigen Abschnitt erstellten Protokolle beginnen mit „TodoApi“. Protokolle, die mit Microsoft-Kategorien beginnen, stammen aus ASP.NET Core-Frameworkcode. ASP.NET Core und Anwendungscode verwenden dieselbe Protokollierungs-API und dieselben Protokollierungsanbieter.

::: moniker-end

In den übrigen Abschnitten dieses Artikels werden einige Details und Optionen für die Protokollierung erläutert.

## <a name="nuget-packages"></a>NuGet-Pakete

Die Schnittstellen `ILogger` und `ILoggerFactory` befinden sich in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), ihre Standardimplementierungen sind in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) enthalten.

## <a name="log-category"></a>Protokollkategorie

Wenn ein `ILogger`-Objekt erstellt wird, wird eine *Kategorie* für dieses Objekt angegeben. Diese Kategorie ist in jeder Protokollmeldung enthalten, die von dieser Instanz von `ILogger` erstellt wird. Die Kategorie kann eine beliebige Zeichenfolge sein, aber die Konvention besteht in der Verwendung des Klassennamens, z.B. „TodoApi.Controllers.TodoController“.

Verwenden Sie `ILogger<T>` zum Abrufen einer `ILogger`-Instanz, die den vollqualifizierten Typnamen von `T` als Kategorie verwendet:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

Um die Kategorie explizit anzugeben, rufen Sie `ILoggerFactory.CreateLogger` auf:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>` entspricht dem Aufruf von `CreateLogger` mit dem vollqualifizierten Typnamen `T`.

## <a name="log-level"></a>Protokolliergrad

Jedes Protokoll gibt einen <xref:Microsoft.Extensions.Logging.LogLevel>-Wert an. Der Protokolliergrad gibt den Schweregrad oder die Wichtigkeit an. Sie können beispielsweise ein `Information`-Protokoll schreiben, wenn eine Methode normal endet, und ein `Warning`-Protokoll, wenn eine Methode den Statuscode *404 Not Found* zurückgibt.

Der folgende Code erstellt `Information`- und `Warning`-Protokolle:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

Im Code oben ist der erste Parameter die [Protokollereignis-ID](#log-event-id). Der zweite Parameter ist eine Meldungsvorlage mit Platzhaltern für Argumentwerte, die von den verbleibenden Methodenparametern bereitgestellt werden. Die Methodenparameter werden im [Abschnitt „Meldungsvorlage“](#log-message-template) später in diesem Artikel erläutert.

Protokollmethoden, die den Protokolliergrad im Methodennamen enthalten (z.B. `LogInformation` und `LogWarning`), sind [Erweiterungsmethoden für ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions). Diese Methoden rufen eine `Log`-Methode auf, die einen `LogLevel`-Parameter annimmt. Sie können anstelle eines Aufrufs dieser Erweiterungsmethoden die `Log`-Methode direkt aufrufen, aber die Syntax ist relativ kompliziert. Weitere Informationen finden Sie unter <xref:Microsoft.Extensions.Logging.ILogger> und im [Quellcode für Protokollierungserweiterungen](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).

ASP.NET Core definiert die folgenden Protokolliergrade. Die Reihenfolge reicht vom geringsten bis zum höchsten Schweregrad.

* Trace = 0

  Protokolliert Informationen, die in der Regel nur für das Debuggen nützlich sind. Diese Meldungen können sensible Anwendungsdaten enthalten und sollten daher nicht in einer Produktionsumgebung aktiviert werden. *Standardmäßig deaktiviert.*

* Debug = 1

  Protokolliert Informationen, die bei der Entwicklung und beim Debuggen hilfreich sein können. Beispiel: `Entering method Configure with flag set to true.` Aktivieren Sie Protokolle mit dem Protokolliergrad `Debug` aufgrund des hohen Protokollaufkommens nur zur Problembehandlung in der Produktion.

* Information = 2

  Zur Nachverfolgung des allgemeinen Ablaufs der App. Diese Protokolle haben typischerweise einen langfristigen Nutzen. Ein Beispiel: `Request received for path /api/todo`

* Warning = 3

  Für ungewöhnliche oder unerwartete Ereignisse im App-Verlauf. Dazu können Fehler oder andere Bedingungen gehören, die zwar nicht zum Beenden der App führen, aber eventuell untersucht werden müssen. Behandelte Ausnahmen sind eine typische Verwendung für den Protokolliergrad `Warning`. Ein Beispiel: `FileNotFoundException for file quotes.txt.`

* Error = 4

  Für Fehler und Ausnahmen, die nicht behandelt werden können. Diese Meldungen weisen auf einen Fehler in der aktuellen Aktivität oder im aktuellen Vorgang (z.B. in der aktuellen HTTP-Anforderung) und nicht auf einen anwendungsweiten Fehler hin. Beispielprotokollmeldung: `Cannot insert record due to duplicate key violation.`

* Critical = 5

  Für Fehler, die sofortige Aufmerksamkeit erfordern. Beispiel: Szenarios mit Datenverlust, Speichermangel.

Verwenden Sie den Protokolliergrad, um die Menge an Protokollausgabedaten zu steuern, die in ein bestimmtes Speichermedium geschrieben oder an ein Anzeigefenster ausgegeben werden. Beispiel:

* In einer Produktionsumgebung
  * Das Protokollieren von `Trace` über die `Information`-Ebenen verursacht viele detaillierte Protokollmeldungen. Protokollieren Sie `Trace` über Nachrichten auf `Information`-Ebene in einem kostengünstigen Speicher mit hohem Volumen, um die Kosten zu überwachen und die Grenzwerte des Datenspeichers nicht zu überschreiten.
  * Das Protokollieren von `Warning` über die `Critical`-Ebenen verursacht weniger und kürzere Protokollmeldungen. Daher müssen Sie sich über Kosten- und Speichergrenzwerte keine Sorgen machen und sind bei der Auswahl des Datenspeichers flexibler.
* Entwicklungsphase:
  * Protokollieren Sie `Warning` über `Critical`-Meldungen an der Konsole.
  * Fügen Sie bei der Fehlerbehebung `Trace` über `Information`-Meldungen hinzu.

Im Abschnitt [Protokollfilterung](#log-filtering) weiter unten in diesem Artikel wird erläutert, wie Sie steuern, welche Protokolliergrade ein Anbieter verarbeitet.

ASP.NET Core schreibt Protokolle für Frameworkereignisse. Die zuvor in diesem Artikel gezeigten Protokollbeispiele enthielten Protokolle unterhalb des Protokolliergrads `Information`, deshalb wurden keine Protokolle des Protokolliergrads `Debug` oder `Trace` erstellt. Hier ist ein Beispiel für Konsolenprotokolle, die durch Ausführen der Beispiel-App generiert werden, die so konfiguriert ist, dass sie `Debug`-Protokolle anzeigt:

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

::: moniker-end

## <a name="log-event-id"></a>Protokollereignis-ID

Jedes Protokoll kann eine *Ereignis-ID* angeben. Die Beispiel-App verwendet hierzu eine lokal definierte `LoggingEvents`-Klasse:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

Eine Ereignis-ID ordnet eine Gruppe von Ereignissen zu. Beispielsweise können alle Protokolle, die sich auf die Anzeige einer Liste von Elementen auf einer Seite beziehen, 1001 sein.

Der Protokollierungsanbieter kann die Ereignis-ID in einem ID-Feld, in der Protokollierungsmeldung oder gar nicht speichern. Der Debuganbieter zeigt keine Ereignis-IDs an. Der Konsolenanbieter zeigt Ereignis-IDs in Klammern hinter der Kategorie an:

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>Protokollmeldungsvorlage

Jedes Protokoll gibt eine Meldungsvorlage an. Die Meldungsvorlage kann Platzhalter enthalten, für die Argumente bereitgestellt werden. Verwenden Sie Namen für die Platzhalter, keine Zahlen.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

Die Reihenfolge der Platzhalter – nicht ihre Namen – bestimmt, welche Parameter verwendet werden, um ihre Werte zur Verfügung zu stellen. Beachten Sie im folgenden Code, dass die Parameternamen in der Meldungsvorlage nicht in der richtigen Reihenfolge vorhanden sind:

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

Dieser Code erstellt eine Protokollierungsmeldung mit den Parameterwerten in der richtigen Reihenfolge:

```text
Parameter values: parm1, parm2
```

Das Protokollierungsframework funktioniert auf diese Weise, damit Protokollierungsanbieter [semantische Protokollierung, die auch als strukturierte Protokollierung bezeichnet wird](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging), implementieren können. Die Argumente selbst (nicht nur die formatierte Meldungsvorlage) werden an das Protokollierungssystem übergeben. Diese Informationen ermöglichen es Protokollierungsanbietern, die Parameterwerte als Felder zu speichern. Nehmen wir zum Beispiel an, dass Aufrufe von Protokollierungsmethoden folgendermaßen aussehen:

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

Wenn Sie die Protokolle an Azure Table Storage senden, kann jede Azure Table-Entität die Eigenschaften `ID` und `RequestTime` aufweisen. Dies vereinfacht die Abfrage von Protokolldaten. Eine Abfrage kann alle Protokolle in einem bestimmten `RequestTime`-Bereich finden, ohne die Uhrzeit aus den Textmeldungen zu analysieren.

## <a name="logging-exceptions"></a>Protokollieren von Ausnahmen

Die Protokollierungsmethoden umfassen Überladungen, die das Übergeben von Ausnahmen ermöglichen, wie im folgenden Beispiel gezeigt:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

Die verschiedenen Anbieter verarbeiten die Ausnahmeinformationen auf unterschiedliche Weise. Hier sehen Sie ein Beispiel einer Debuganbieterausgabe aus dem obigen Codebeispiel.

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>Protokollfilterung

Sie können einen Mindestprotokolliergrad für einen bestimmten Anbieter und eine bestimmte Kategorie oder für alle Anbieter oder alle Kategorien festlegen. Alle Protokolle unter dem Mindestgrad werden nicht an diesen Anbieter weitergeleitet, sodass sie nicht angezeigt oder gespeichert werden.

Wenn Sie alle Protokolle unterdrücken möchten, geben Sie `LogLevel.None` als Mindestprotokolliergrad an. Der ganzzahlige Wert von `LogLevel.None` lautet 6 und liegt über `LogLevel.Critical` (5).

### <a name="create-filter-rules-in-configuration"></a>Erstellen von Filterregeln in der Konfiguration

Der Projektvorlagencode ruft `CreateDefaultBuilder` auf, um die Protokollierung für die Konsolen-, Debug- und EventSource-Anbieter (ASP.NET Core 2.2 oder höher) einzurichten. Die `CreateDefaultBuilder`-Methode richtet die Protokollierung ein, um nach der Konfiguration in einem `Logging`-Abschnitt zu suchen; dies wird [weiter oben in diesem Artikel](#configuration) erläutert.

Die Konfigurationsdaten geben die Mindestprotokolliergrade nach Anbieter und Kategorie an, wie im folgenden Beispiel gezeigt:

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

Diese JSON-Konfiguration erstellt sechs Filterregeln: eine für den Debuganbieter, vier für den Konsolenanbieter und eine für alle Anbieter. Eine einzelne Regel wird für jeden Anbieter ausgewählt, wenn ein `ILogger`-Objekt erstellt wird.

### <a name="filter-rules-in-code"></a>Filterregeln im Code

Das folgende Beispiel zeigt, wie Filterregeln im Code registriert werden:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

Das zweite `AddFilter` gibt den Debuganbieter durch Verwendung des zugehörigen Typnamens an. Das erste `AddFilter` gilt für alle Anbieter, weil kein Anbietertyp angegeben wird.

### <a name="how-filtering-rules-are-applied"></a>Anwendung von Filterregeln

Die Konfigurationsdaten und der in den vorangegangenen Beispielen gezeigte `AddFilter`-Code erzeugen die in der folgenden Tabelle gezeigten Regeln. Die ersten sechs Regeln stammen aus dem Konfigurationsbeispiel, die letzten zwei Filter stammen aus dem Codebeispiel.

| Anzahl | Anbieter      | Kategorien beginnend mit...          | Mindestprotokolliergrad |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | Debug         | Alle Kategorien                          | Information       |
| 2      | Konsole       | Microsoft.AspNetCore.Mvc.Razor.Internal | Warnung           |
| 3      | Konsole       | Microsoft.AspNetCore.Mvc.Razor.Razor    | Debug             |
| 4      | Konsole       | Microsoft.AspNetCore.Mvc.Razor          | Fehler             |
| 5      | Konsole       | Alle Kategorien                          | Information       |
| 6      | Alle Anbieter | Alle Kategorien                          | Debug             |
| 7      | Alle Anbieter | System                                  | Debug             |
| 8      | Debug         | Microsoft                               | Ablaufverfolgung             |

Wenn ein `ILogger`-Objekt erstellt wird, wählt das `ILoggerFactory`-Objekt eine einzige Regel pro Anbieter aus, die auf diese Protokollierung angewendet wird. Alle von einer `ILogger`-Instanz geschriebenen Meldungen werden auf Grundlage der ausgewählten Regeln gefiltert. Aus den verfügbaren Regeln wird die für jeden Anbieter und jedes Kategoriepaar spezifischste Regel ausgewählt.

Beim Erstellen von `ILogger` für eine vorgegebene Kategorie wird für jeden Anbieter der folgende Algorithmus verwendet:

* Es werden alle Regeln ausgewählt, die dem Anbieter oder dem zugehörigen Alias entsprechen. Wenn keine Übereinstimmung gefunden wird, werden alle Regeln mit einem leeren Anbieter ausgewählt.
* Aus dem Ergebnis des vorherigen Schritts werden die Regeln mit dem längsten übereinstimmenden Kategoriepräfix ausgewählt. Wenn keine Übereinstimmung gefunden wird, werden alle Regeln ohne Angabe einer Kategorie ausgewählt.
* Wenn mehrere Regeln ausgewählt sind, wird die **letzte** Regel verwendet.
* Wenn keine Regel ausgewählt ist, wird `MinimumLevel` verwendet.

Angenommen, Sie erstellen mit der oben aufgeführten Liste mit Regeln ein `ILogger`-Objekt für die Kategorie „Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine“:

* Für den Debuganbieter gelten die Regeln 1, 6 und 8. Regel 8 ist die spezifischste Regel, deshalb wird diese Regel ausgewählt.
* Für den Konsolenanbieter gelten die Regeln 3, 4, 5 und 6. Regel 3 ist die spezifischste Regel.

Die sich ergebende `ILogger`-Instanz sendet Protokolle mit dem Protokolliergrad `Trace` und höher an den Debuganbieter. Protokolle mit dem Protokolliergrad `Debug` und höher werden an den Konsolenanbieter gesendet.

### <a name="provider-aliases"></a>Anbieteraliase

Jeder Anbieter definiert einen *Alias*, der in der Konfiguration anstelle des vollqualifizierten Typnamens verwendet werden kann.  Verwenden Sie für die integrierten Anbieter die folgenden Aliase:

* Konsole
* Debug
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>Standardmindestprotokolliergrad

Es gibt eine Einstellung für den Mindestprotokolliergrad, die nur dann wirksam wird, wenn für einen bestimmten Anbieter und eine bestimmte Kategorie keine Regeln aus Konfiguration oder Code gelten. Im folgenden Beispiel wird das Festlegen des Mindestprotokolliergrads veranschaulicht:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

Wenn Sie den Mindestprotokolliergrad nicht explizit festlegen, lautet der Standardwert `Information`, d.h. Protokolle der Ebene `Trace` und `Debug` werden ignoriert.

### <a name="filter-functions"></a>Filterfunktionen

Eine Filterfunktion wird für alle Anbieter und Kategorien aufgerufen, denen keine Regeln durch Konfiguration oder Code zugewiesen sind. Code in der Funktion verfügt über Zugriff auf den Anbietertyp, die Kategorie und den Protokolliergrad. Beispiel:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a>Systemkategorien und -protokolliergrade

Dies sind einige Kategorien, die vom ASP.NET Core und Entity Framework Core verwendet werden, mit Hinweisen darauf, welche Protokolle von ihnen zu erwarten sind:

| Kategorie                            | Hinweise |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | Allgemeine ASP.NET Core-Diagnose. |
| Microsoft.AspNetCore.DataProtection | Gibt an, welche Schlüssel in Betracht gezogen, gefunden und verwendet wurden. |
| Microsoft.AspNetCore.HostFiltering  | Hosts sind zulässig. |
| Microsoft.AspNetCore.Hosting        | Gibt an, wie lange es bis zum Abschluss von HTTP-Anforderungen gedauert hat und zu welcher Zeit sie gestartet wurden. Gibt an, welche Hostingstartassemblys geladen wurden. |
| Microsoft.AspNetCore.Mvc            | MVC- und Razor-Diagnose. Modellbindung, Filterausführung, Ansichtskompilierung, Aktionsauswahl. |
| Microsoft.AspNetCore.Routing        | Gibt Routenabgleichsinformationen an. |
| Microsoft.AspNetCore.Server         | Start-, Beendigungs- und Keep-Alive-Antworten der Verbindung. HTTPS-Zertifikatinformationen. |
| Microsoft.AspNetCore.StaticFiles    | Die bereitgestellten Dateien. |
| Microsoft.EntityFrameworkCore       | Allgemeine Entity Framework Core-Diagnose. Datenbankaktivität und -konfiguration, Änderungserkennung, Migrationen. |

## <a name="log-scopes"></a>Protokollbereiche

 Ein *Bereich* kann einen Satz logischer Vorgänge gruppieren. Diese Gruppierung kann verwendet werden, um an jedes Protokoll, das als Teil einer Gruppe erstellt wird, die gleichen Daten anzufügen. So kann beispielsweise jedes Protokoll, das im Rahmen der Verarbeitung einer Transaktion erstellt wird, die Transaktions-ID enthalten.

Ein Bereich ist ein `IDisposable`-Typ, der von der <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>-Methode zurückgegeben und so lange beibehalten wird, bis er verworfen wird. Verwenden Sie einen Bereich, indem Sie Protokollierungsaufrufe mit einem `using`-Block umschließen, der als Wrapper verwendet wird:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

Der folgende Code aktiviert Bereiche für den Konsolenanbieter:

*Program.cs*:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> Um die bereichsbasierte Protokollierung zu aktivieren, muss die Konsolenprotokollierungsoption `IncludeScopes` konfiguriert werden.
>
> Weitere Informationen zur Konfiguration finden Sie im Abschnitt [Konfiguration](#configuration).

Jede Protokollmeldung enthält die bereichsbezogenen Informationen:

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>Integrierte Protokollierungsanbieter

ASP.NET Core wird mit den folgenden Anbietern bereitgestellt:

* [Konsole](#console-provider)
* [Debuggen](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

Weitere Informationen zu „stdout“ und zur Debugprotokollierung mit dem ASP.NET Core-Modul finden Sie unter <xref:test/troubleshoot-azure-iis> und <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

### <a name="console-provider"></a>Der Konsolenanbieter

Das Anbieterpaket [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sendet eine Protokollausgabe an die Konsole. 

```csharp
logging.AddConsole();
```

Um die Ausgabe der Konsolenprotokollierung anzuzeigen, öffnen Sie eine Eingabeaufforderung im Projektordner und führen den folgenden Befehl aus:

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>Der Debuganbieter

Beim Anbieterpaket [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) erfolgt die Protokollausgabe unter Verwendung der Klasse [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine`-Methodenaufrufe).

Unter Linux werden Protokolle dieses Anbieters in */var/log/message* geschrieben.

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>Ereignisquellenanbieter

Das [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource)-Anbieterpaket schreibt in eine plattformübergreifende Ereignisquelle mit dem Namen `Microsoft-Extensions-Logging`. Unter Windows verwendet der Anbieter die [Ereignisablaufverfolgung für Windows (ETW)](https://msdn.microsoft.com/library/windows/desktop/bb968803).

```csharp
logging.AddEventSourceLogger();
```

Der Ereignisquellenanbieter wird automatisch hinzugefügt, wenn `CreateDefaultBuilder` zum Erstellen des Hosts aufgerufen wird.

::: moniker range=">= aspnetcore-3.0"

#### <a name="dotnet-trace-tooling"></a>dotnet-trace-Tool

Das Tool [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) ist ein plattformübergreifendes globales Befehlszeilenschnittstellentool zum Sammeln von .NET Core-Ablaufverfolgungen eines ausgeführten Prozesses. Das Tool sammelt <xref:Microsoft.Extensions.Logging.EventSource>-Anbieterdaten mithilfe einer <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.

Installieren Sie das dotnet-trace-Tool mit dem folgenden Befehl:

```dotnetcli
dotnet tool install --global dotnet-trace
```

Verwenden Sie das dotnet-trace-Tool, um die Ablaufverfolgung aus einer App zu erfassen:

1. Wenn die App den Host nicht mit `CreateDefaultBuilder` erstellt, fügen Sie den [Ereignisquellenanbieter](#event-source-provider) zur Protokollierungskonfiguration der App hinzu.

1. Führen Sie die App mit dem Befehl `dotnet run` aus.

1. Bestimmen Sie den Prozessbezeichner der .NET Core-App:

   * Unter Windows können Sie einen der folgenden Ansätze verwenden:
     * Task-Manager (STRG+ALT+ENTF)
     * [tasklist-Befehl](/windows-server/administration/windows-commands/tasklist)
     * [PowerShell-Befehl „Get-Process“](/powershell/module/microsoft.powershell.management/get-process)
   * Verwenden Sie unter Linux den [pidof-Befehl](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).

   Suchen Sie den Prozessbezeichner für den Prozess, der den gleichen Namen wie die App-Assembly hat.

1. Führen Sie den `dotnet trace`-Befehl aus.

   Allgemeine Befehlssyntax:

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   Wenn Sie eine PowerShell-Befehlsshell verwenden, schließen Sie den `--providers`-Wert in einfache Anführungszeichen (`'`) ein:

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   Auf Plattformen, die nicht unter Windows ausgeführt werden, fügen Sie die `-f speedscope`-Option hinzu, um das Format der Ablaufverfolgungsdatei der Ausgabe in `speedscope` zu ändern.

   | Stichwort | BESCHREIBUNG |
   | :-----: | ----------- |
   | 1       | Protokolliert Metaereignisse über die `LoggingEventSource`. Es werden keine Ereignisse von `ILogger`) protokolliert. |
   | 2       | Aktiviert das `Message`-Ereignis, wenn `ILogger.Log()` aufgerufen wird. Die Informationen werden in einer programmgesteuerten (nicht formatierten) Weise ausgegeben. |
   | 4       | Aktiviert das `FormatMessage`-Ereignis, wenn `ILogger.Log()` aufgerufen wird. Gibt die formatierte Zeichenfolgeversion der Informationen an. |
   | 8       | Aktiviert das `MessageJson`-Ereignis, wenn `ILogger.Log()` aufgerufen wird. Stellt eine JSON-Darstellung der Argumente bereit. |

   | Ereignisgrad | BESCHREIBUNG     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs`-Einträge für `{Logger Category}` und `{Event Level}` stellen zusätzliche Protokollfilterbedingungen dar. Trennen Sie die `FilterSpecs`-Einträge durch ein Semikolon (`;`).

   Beispiel mit einer Windows-Befehlsshell (**keine** einfachen Anführungszeichen um den `--providers`-Wert):

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   Mit dem vorangestellten Komma wird Folgendes aktiviert:

   * Die Ereignisquellenprotokollierung zur Erzeugung von formatierten Zeichenfolgen (`4`) für Fehler (`2`)
   * `Microsoft.AspNetCore.Hosting`-Protokollierung auf `Informational`-Protokollierungsebene (`4`)

1. Halten Sie das dotnet-trace-Tool an, indem Sie die EINGABETASTE oder STRG+C drücken.

   Die Ablaufverfolgung wird mit dem Namen *trace.nettrace* in dem Ordner gespeichert, in dem der `dotnet trace`-Befehl ausgeführt wird.

1. Öffnen Sie die Ablaufverfolgung mit [Perfview](#perfview). Öffnen Sie die Datei *trace.nettrace*, und untersuchen Sie die Ablaufverfolgungsereignisse.

Weitere Informationen finden Sie unter:

* [Ablaufverfolgung für das Hilfsprogramm zur Leistungsanalyse (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core-Dokumentation)
* [Ablaufverfolgung für das Hilfsprogramm zur Leistungsanalyse (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (GitHub-Repository-Dokumentation zu dotnet/diagnostics)
* [LoggingEventSource-Klasse](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API-Browser)
* <xref:System.Diagnostics.Tracing.EventLevel>
* [LoggingEventSource-Verweisquelle (3.0)](https://github.com/aspnet/Extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Ändern Sie für das Abrufen der Verweisquelle für eine andere Version den Branch in `release/{Version}`, wobei `{Version}` die Version der gewünschten ASP.NET Core-Version darstellt.
* [Perfview](#perfview) &ndash; Nützlich zum Anzeigen von Ablaufverfolgung für Ereignisquellen.

#### <a name="perfview"></a>PerfView

::: moniker-end

Verwenden Sie das [PerfView-Hilfsprogramm](https://github.com/Microsoft/perfview) zum Sammeln und Anzeigen von Protokollen. Es gibt andere Tools zur Anzeige von ETW-Protokollen, aber PerfView bietet die besten Ergebnisse bei der Arbeit mit ETW-Ereignissen, die von ASP.NET Core ausgegeben werden.

Um PerfView für das Erfassen von Ereignissen zu konfigurieren, die von diesem Anbieter protokolliert wurden, fügen Sie die Zeichenfolge `*Microsoft-Extensions-Logging` zur Liste **Zusätzliche Anbieter** hinzu. (Vergessen Sie nicht das Sternchen am Anfang der Zeichenfolge.)

![Zusätzliche PerfView-Anbieter](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Der Windows-EventLog-Anbieter

Das Anbieterpaket [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sendet eine Protokollausgabe in das Windows-Ereignisprotokoll.

```csharp
logging.AddEventLog();
```

Mithilfe von [AddEventLog-Überladungen](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) können Sie <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> übergeben. Wenn `null` oder nicht angegeben, werden die folgenden Standardeinstellungen verwendet:

* `LogName` &ndash; „Anwendung“
* `SourceName` &ndash; „.NET Runtime“
* `MachineName` &ndash; Lokaler Computer

### <a name="tracesource-provider"></a>Der TraceSource-Anbieter

Das Anbieterpaket [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) verwendet die <xref:System.Diagnostics.TraceSource>-Bibliotheken und -Anbieter.

```csharp
logging.AddTraceSource(sourceSwitchName);
```

Mithilfe von [AddTraceSource-Überladungen](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) können Sie eine Quelloption und einen Listener für die Ablaufverfolgung übergeben.

Um diesen Anbieter zu verwenden, muss eine App unter .NET Framework (anstelle von .NET Core) ausgeführt werden. Der Anbieter kann Nachrichten an eine Vielzahl von [Listenern](/dotnet/framework/debug-trace-profile/trace-listeners) weiterleiten, z.B. an den in der Beispiel-App verwendeten <xref:System.Diagnostics.TextWriterTraceListener>.

### <a name="azure-app-service-provider"></a>Der Azure App Service-Anbieter

Das Anbieterpaket [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) schreibt Protokolle in Textdateien in das Dateisystem einer Azure App Service-App und in [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in einem Azure Storage-Konto.

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

Das Anbieterpaket ist nicht im freigegebenen Framework enthalten. Zum Verwenden des Anbieters müssen Sie das Anbieterpaket dem Projekt hinzufügen.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dieses Anbieterpaket ist nicht im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten. Wenn Sie Anwendungen für .NET Framework entwickeln oder auf das `Microsoft.AspNetCore.App`-Metapaket verweisen, fügen Sie das Anbieterpaket dem Projekt hinzu. 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Verwenden Sie <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> und <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> wie im folgenden Beispiel, um die Anbietereinstellungen zu konfigurieren:

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Verwenden Sie <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> und <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> wie im folgenden Beispiel, um die Anbietereinstellungen zu konfigurieren:

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Eine <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>-Überladung ermöglicht das Übergeben von <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>. Das settings-Objekt kann Standardeinstellungen überschreiben, z.B. die Protokollierungsausgabevorlage, den Blobnamen und die maximale Dateigröße. (Die *Ausgabevorlage* ist eine Nachrichtenvorlage, die auf alle Protokolle zusätzlich zu dem angewendet wird, was mit einem `ILogger`-Methodenaufruf bereitgestellt wird.)

::: moniker-end

Wenn Sie eine Bereitstellung in einer App Service-App durchführen, berücksichtigt die Anwendung die Einstellungen im Abschnitt [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) (App Service-Protokolle) auf der Seite **App Services** im Azure-Portal. Bei einem Update der folgenden Einstellungen werden die Änderungen sofort wirksam, ohne dass ein Neustart oder eine erneute Bereitstellung der App notwendig ist.

* **Anwendungsprotokoll (Dateisystem)**
* **Anwendungsprotokoll (Blob)**

Der Standardspeicherort für Protokolldateien ist der Ordner *D:\\home\\LogFiles\\Application*, und der standardmäßige Dateiname lautet *diagnostics-yyyymmdd.txt*. Die Dateigröße ist standardmäßig auf 10 MB beschränkt, und die maximal zulässige Anzahl beibehaltener Dateien lautet 2. Der Standardblobname lautet *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.

Der Anbieter funktioniert nur, wenn das Projekt in der Azure-Umgebung ausgeführt wird. Bei einer lokalen Ausführung zeigt er keine Auswirkungen. Der Anbieter schreibt keine Protokolle in lokale Dateien oder den lokalen Entwicklungsspeicher für BLOBs.

#### <a name="azure-log-streaming"></a>Azure-Protokollstreaming

Azure-Protokollstreaming ermöglicht Ihnen eine Echtzeitanzeige der Protokollaktivität für:

* App-Server
* Webserver
* Ablaufverfolgung für Anforderungsfehler

So konfigurieren Sie das Azure-Protokollstreaming

* Navigieren Sie von der Portalseite Ihrer App zur Seite **App Service-Protokolle**.
* Legen Sie **Anwendungsprotokollierung (Dateisystem)** auf **Ein** fest.
* Wählen Sie die **Protokollierungsebene**. Diese Einstellung gilt nur für das Azure-Protokollstreaming, nicht für andere Protokollierungsanbieter in der App.

Navigieren Sie zur Seite **Log Stream** (Protokollstream), um App-Meldungen anzuzeigen. Diese werden von der App über die `ILogger`-Schnittstelle protokolliert.

### <a name="azure-application-insights-trace-logging"></a>Ablaufverfolgungsprotokollierung für Azure Application Insights

Das [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)-Anbieterpaket schreibt Protokolle in Azure Application Insights. Application Insights ist ein Dienst, der eine Web-App überwacht und Tools für Abfragen und Analysen von Telemetriedaten zur Verfügung stellt. Wenn Sie diesen Anbieter verwenden, können Sie Ihre Protokolle mithilfe der Application Insights-Tools abfragen und analysieren.

Der Protokollierungsanbieter ist als Abhängigkeit von [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) enthalten, d.h. als das Paket, das alle verfügbaren Telemetriedaten für ASP.NET Core bereitstellt. Wenn Sie dieses Paket verwenden, ist nicht erforderlich, das Anbieterpaket zu installieren.

Verwenden Sie nicht das [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)-Paket, das für ASP.NET 4.x bestimmt ist.

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Application Insights-Übersicht](/azure/application-insights/app-insights-overview)
* [Anwendungseinblicke für ASP.NET Core-Anwendungen](/azure/azure-monitor/app/asp-net-core): Beginnen Sie hier, wenn Sie die gesamte Bandbreite der Application Insights-Telemetrie zusammen mit der Protokollierung implementieren möchten.
* [ApplicationInsightsLoggerProvider für .NET Core ILogger-Protokolle](/azure/azure-monitor/app/ilogger): Beginnen Sie hier, wenn Sie den Protokollierungsanbieter ohne die übrige Application Insights-Telemetrie implementieren möchten.
* [Application Insights logging adapters (Adapter zur Protokollierung für Application Insights)](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).
* [Installieren, Konfigurieren und Initialisieren des Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): interaktive Tutorials auf der Microsoft Learn-Website.

## <a name="third-party-logging-providers"></a>Protokollierungsanbieter von Drittanbietern

Protokollierungsframeworks von Drittanbietern aufgeführt, die mit ASP.NET Core funktionieren:

* [elmah.io](https://elmah.io/) ([GitHub-Repository](https://github.com/elmahio/Elmah.Io.Extensions.Logging))
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub-Repository](https://github.com/mattwcole/gelf-extensions-logging))
* [JSNLog](https://jsnlog.com/) ([GitHub-Repository](https://github.com/mperdeck/jsnlog))
* [KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))
* [Log4Net](https://logging.apache.org/log4net/) ([GitHub-Repository](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))
* [Loggr](https://loggr.net/) ([GitHub-Repository](https://github.com/imobile3/Loggr.Extensions.Logging))
* [NLog](https://nlog-project.org/) ([GitHub-Repository](https://github.com/NLog/NLog.Extensions.Logging))
* [Sentry](https://sentry.io/welcome/) ([GitHub-Repository](https://github.com/getsentry/sentry-dotnet))
* [Serilog](https://serilog.net/) ([GitHub-Repository](https://github.com/serilog/serilog-aspnetcore))
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub-Repository](https://github.com/googleapis/google-cloud-dotnet))

Einige Drittanbieterframeworks können eine [semantische Protokollierung (auch als strukturierte Protokollierung bezeichnet)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) ausführen.

Die Verwendung eines Frameworks von Drittanbietern ist ähnlich wie die Verwendung eines integrierten Anbieters:

1. Fügen Sie Ihrem Paket ein NuGet-Paket hinzu.
1. Rufen Sie eine `ILoggerFactory`-Erweiterungsmethode auf, die vom Protokollierungsframework bereitgestellt wird.

Weitere Informationen finden Sie in der Dokumentation zum jeweiligen Anbieter. Protokollierungsanbieter von Drittanbietern werden von Microsoft nicht unterstützt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/logging/loggermessage>
