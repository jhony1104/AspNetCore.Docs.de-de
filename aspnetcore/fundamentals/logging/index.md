---
title: Protokollieren in .NET Core und ASP.NET Core
author: rick-anderson
description: In diesem Artikel wird das Verwenden des Protokollierungsframeworks erläutert, das vom Microsoft.Extensions.Logging-NuGet-Paket bereitgestellt wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/23/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 7be8cef3377132ed43efde209db67401d7bdb6dc
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110914"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="71d50-103">Protokollieren in .NET Core und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="71d50-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71d50-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="71d50-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="71d50-105">.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="71d50-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="71d50-106">Dieser Artikel zeigt, wie Sie die Protokollierungs-API mit integrierten Anbietern verwenden können.</span><span class="sxs-lookup"><span data-stu-id="71d50-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="71d50-107">Die meisten der Codebeispiele in diesem Artikel stammen aus ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="71d50-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="71d50-108">Die auf die Protokollierung bezogenen Teile dieser Codeausschnitte gelten für jede .NET Core-App, die den [generischen Host](xref:fundamentals/host/generic-host) verwendet.</span><span class="sxs-lookup"><span data-stu-id="71d50-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="71d50-109">Ein Beispiel zum Verwenden des generischen Hosts in einer nicht webbasierten Konsolen-App finden Sie in der *Program.cs*-Datei der [Beispiel-App für Hintergrundaufgaben](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="71d50-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="71d50-110">Das Protokollieren von Code für Apps ohne generischen Host weicht in Bezug auf das [Hinzufügen von Anbietern](#add-providers) und das [Erstellen von Protokollierungen](#create-logs) ab.</span><span class="sxs-lookup"><span data-stu-id="71d50-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="71d50-111">Codebeispiele ohne Host finden sich in diesen Abschnitten des Artikels.</span><span class="sxs-lookup"><span data-stu-id="71d50-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="71d50-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71d50-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="71d50-113">Hinzufügen von Anbietern</span><span class="sxs-lookup"><span data-stu-id="71d50-113">Add providers</span></span>

<span data-ttu-id="71d50-114">Ein Protokollierungsanbieter zeigt Protokolle an oder speichert diese.</span><span class="sxs-lookup"><span data-stu-id="71d50-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="71d50-115">Beispielsweise zeigt der Konsolenanbieter Protokolle in der Konsole an, und der Azure Application Insights-Anbieter speichert sie in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="71d50-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="71d50-116">Protokolle können durch das Hinzufügen mehrerer Anbieter an mehrere Ziele gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="71d50-117">Um einen Anbieter in einer App hinzuzufügen, die den generischen Host verwendet, rufen Sie die `Add{provider name}`-Erweiterungsmethode des Anbieters in *Program.cs* auf:</span><span class="sxs-lookup"><span data-stu-id="71d50-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="71d50-118">Rufen Sie in einer Konsolen-App ohne Host die `Add{provider name}`-Erweiterungsmethode des Anbieters auf, während Sie eine `LoggerFactory` erstellen:</span><span class="sxs-lookup"><span data-stu-id="71d50-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="71d50-119">`LoggerFactory` und `AddConsole` erfordern eine `using`-Anweisung für `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="71d50-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="71d50-120">Die ASP.NET-Standardprojektvorlagen rufen die Methode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> auf, die die folgenden Protokollierungsanbieter hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="71d50-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="71d50-121">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="71d50-122">Debuggen</span><span class="sxs-lookup"><span data-stu-id="71d50-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="71d50-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="71d50-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="71d50-124">[EventLog](#windows-eventlog-provider) (nur bei Ausführung unter Windows)</span><span class="sxs-lookup"><span data-stu-id="71d50-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="71d50-125">Sie können die Standardanbieter durch Ihre eigene Auswahl ersetzen.</span><span class="sxs-lookup"><span data-stu-id="71d50-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="71d50-126">Rufen Sie <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> auf, und fügen Sie die gewünschten Anbieter hinzu.</span><span class="sxs-lookup"><span data-stu-id="71d50-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="71d50-127">Informationen zu den [integrierten Protokollierungsanbietern](#built-in-logging-providers) sowie zu [Protokollierungsanbietern von Drittanbietern](#third-party-logging-providers) finden Sie weiter unten in diesem Artikel.</span><span class="sxs-lookup"><span data-stu-id="71d50-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="71d50-128">Erstellen von Protokollen</span><span class="sxs-lookup"><span data-stu-id="71d50-128">Create logs</span></span>

<span data-ttu-id="71d50-129">Verwenden Sie zum Erstellen von Protokollen ein <xref:Microsoft.Extensions.Logging.ILogger%601>-Objekt.</span><span class="sxs-lookup"><span data-stu-id="71d50-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="71d50-130">Rufen Sie in einer Web-App oder einem gehosteten Dienst einen `ILogger` aus der Abhängigkeitsinjektion ab.</span><span class="sxs-lookup"><span data-stu-id="71d50-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="71d50-131">Verwenden Sie in Konsolen-Apps ohne Host `LoggerFactory`, um einen `ILogger` zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="71d50-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="71d50-132">Im folgenden ASP.NET Core-Beispiel wird eine Protokollierung mit `TodoApiSample.Pages.AboutModel` als Kategorie erstellt.</span><span class="sxs-lookup"><span data-stu-id="71d50-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="71d50-133">Die Protokoll*kategorie* ist eine Zeichenfolge, die jedem Protokoll zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="71d50-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="71d50-134">Die von der Abhängigkeitsinjektion bereitgestellte `ILogger<T>`-Instanz erstellt Protokolle, die den vollqualifizierten Namen vom Typ `T` als Kategorie aufweisen.</span><span class="sxs-lookup"><span data-stu-id="71d50-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="71d50-135">Im folgenden Beispiel einer Konsolen-App ohne Host wird eine Protokollierung mit `LoggingConsoleApp.Program` als Kategorie erstellt.</span><span class="sxs-lookup"><span data-stu-id="71d50-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="71d50-136">In den folgenden Beispielen zu ASP.NET Core und einer Konsolen-App werden von der Protokollierung Protokolle mit dem Protokolliergrad `Information` erstellt.</span><span class="sxs-lookup"><span data-stu-id="71d50-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="71d50-137">Der Protokollierungs*grad* gibt den Schweregrad des protokollierten Ereignisses an.</span><span class="sxs-lookup"><span data-stu-id="71d50-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="71d50-138">[Grade](#log-level) und [Kategorien](#log-category) werden weiter unten in diesem Artikel ausführlicher erläutert.</span><span class="sxs-lookup"><span data-stu-id="71d50-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="71d50-139">Erstellen von Protokollen in der Program-Klasse</span><span class="sxs-lookup"><span data-stu-id="71d50-139">Create logs in the Program class</span></span>

<span data-ttu-id="71d50-140">Wenn Protokolle in die `Program`-Klasse einer ASP.NET Core-App geschrieben werden sollen, rufen Sie nach dem Erstellen des Hosts eine `ILogger`-Instanz aus der Abhängigkeitsinjektion ab:</span><span class="sxs-lookup"><span data-stu-id="71d50-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="71d50-141">Die Protokollierung während der Hosterstellung wird nicht direkt unterstützt.</span><span class="sxs-lookup"><span data-stu-id="71d50-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="71d50-142">Es kann jedoch eine separate Protokollierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-142">However, a separate logger can be used.</span></span> <span data-ttu-id="71d50-143">Im folgenden Beispiel wird eine [Serilog](https://serilog.net/)-Protokollierung zum Anmelden von `CreateHostBuilder` verwendet.</span><span class="sxs-lookup"><span data-stu-id="71d50-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="71d50-144">`AddSerilog` verwendet die in `Log.Logger` angegebene statische Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="71d50-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="71d50-145">Erstellen von Protokollen in der Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="71d50-145">Create logs in the Startup class</span></span>

<span data-ttu-id="71d50-146">Wenn Protokolle in die `Startup.Configure`-Methode einer ASP.NET Core-App geschrieben werden sollen, schließen Sie einen `ILogger`-Parameter in die Methodensignatur ein:</span><span class="sxs-lookup"><span data-stu-id="71d50-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="71d50-147">Das Schreiben von Protokollen vor Abschluss der Einrichtung des Abhängigkeitsinjektion-Containers in der `Startup.ConfigureServices`-Methode wird nicht unterstützt:</span><span class="sxs-lookup"><span data-stu-id="71d50-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="71d50-148">Die Protokollierungsinjektion in den `Startup`-Konstruktor wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="71d50-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="71d50-149">Die Protokollierungsinjektion in die `Startup.ConfigureServices`-Methodensignatur wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="71d50-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="71d50-150">Dies Einschränkung ist darauf zurückzuführen, dass die Protokollierung von der Abhängigkeitsinjektion und der Konfiguration abhängt (die wiederum von der Abhängigkeitsinjektion abhängt).</span><span class="sxs-lookup"><span data-stu-id="71d50-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="71d50-151">Der Abhängigkeitsinjektion-Container wird erst nach Fertigstellung von `ConfigureServices` eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="71d50-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="71d50-152">Die Konstruktorinjektion einer Protokollierung in `Startup` funktioniert in früheren Versionen von ASP.NET Core, da für den Webhost ein separater Abhängigkeitsinjektion-Container erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="71d50-153">Weitere Informationen dazu, warum nur ein Container für den generischen Host erstellt wird, erhalten Sie in der [Breaking Change-Ankündigung](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="71d50-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="71d50-154">Wenn Sie einen Dienst konfigurieren müssen, der von `ILogger<T>` abhängt, können Sie dies immer noch mithilfe der Konstruktorinjektion oder durch Bereitstellen einer Factorymethode ausführen.</span><span class="sxs-lookup"><span data-stu-id="71d50-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="71d50-155">Die Nutzung einer Factorymethode wird nur empfohlen, wenn Sie keine andere Wahl haben.</span><span class="sxs-lookup"><span data-stu-id="71d50-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="71d50-156">Angenommen, Sie müssen eine Eigenschaft mit einem Dienst aus der Abhängigkeitsinjektion füllen:</span><span class="sxs-lookup"><span data-stu-id="71d50-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="71d50-157">Der hervorgehobene Code oben ist eine `Func`, die ausgeführt wird, wenn der Abhängigkeitsinjektion-Container eine Instanz von `MyService` erstellen muss.</span><span class="sxs-lookup"><span data-stu-id="71d50-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="71d50-158">Auf diese Weise können Sie auf alle registrierten Dienste zugreifen.</span><span class="sxs-lookup"><span data-stu-id="71d50-158">You can access any of the registered services in this way.</span></span>

### <a name="create-logs-in-blazor"></a><span data-ttu-id="71d50-159">Erstellen von Protokollen in Blazor</span><span class="sxs-lookup"><span data-stu-id="71d50-159">Create logs in Blazor</span></span>

#### <a name="blazor-webassembly"></a><span data-ttu-id="71d50-160">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="71d50-160">Blazor WebAssembly</span></span>

<span data-ttu-id="71d50-161">Konfigurieren Sie Protokollierung in Blazor-WebAssembly-Apps mit der `WebAssemblyHostBuilder.Logging`-Eigenschaft in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="71d50-161">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="71d50-162">Die `Logging`-Eigenschaft ist vom Typ <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, deshalb sind alle auf <xref:Microsoft.Extensions.Logging.ILoggingBuilder> verfügbaren Erweiterungsmethoden auch auf `Logging` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="71d50-162">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

#### <a name="log-in-razor-components"></a><span data-ttu-id="71d50-163">Protokollieren in Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="71d50-163">Log in Razor components</span></span>

<span data-ttu-id="71d50-164">Protokollierungen beachten die Startkonfiguration der App.</span><span class="sxs-lookup"><span data-stu-id="71d50-164">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="71d50-165">Die `using`-Direktive für <xref:Microsoft.Extensions.Logging> ist erforderlich, um die IntelliSense-Vervollständigungen für APIs zu unterstützen, zum Beispiel <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> und <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span><span class="sxs-lookup"><span data-stu-id="71d50-165">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="71d50-166">Im folgenden Beispiel wird die Protokollierung mit einer <xref:Microsoft.Extensions.Logging.ILogger>-Schnittstelle in Razor-Komponenten veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="71d50-166">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

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

<span data-ttu-id="71d50-167">Im folgenden Beispiel wird die Protokollierung mit einer <xref:Microsoft.Extensions.Logging.ILoggerFactory>-Schnittstelle in Razor-Komponenten veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="71d50-167">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="71d50-168">Keine asynchronen Protokollierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="71d50-168">No asynchronous logger methods</span></span>

<span data-ttu-id="71d50-169">Die Protokollierung sollte so schnell erfolgen, dass sich die Leistungskosten von asynchronem Code nicht lohnen.</span><span class="sxs-lookup"><span data-stu-id="71d50-169">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="71d50-170">Wenn Ihr Protokollierungsdatenspeicher langsam ist, schreiben Sie nicht direkt in ihn.</span><span class="sxs-lookup"><span data-stu-id="71d50-170">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="71d50-171">Erwägen Sie, die Protokollnachrichten zunächst in einen schnellen Speicher zu schreiben und sie dann später in den langsamen Speicher zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="71d50-171">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="71d50-172">Wenn Sie beispielsweise in SQL Server protokollieren, möchten Sie dies nicht direkt in einer `Log`-Methode tun, da die `Log`-Methoden synchron sind.</span><span class="sxs-lookup"><span data-stu-id="71d50-172">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="71d50-173">Fügen Sie stattdessen die Protokollmeldungen synchron zu einer Warteschlange im Arbeitsspeicher hinzu, und rufen Sie mithilfe eines Hintergrundworkers die Nachrichten aus der Warteschlange ab, um sie dann asynchron per Pushvorgang an SQL Server zu übertragen.</span><span class="sxs-lookup"><span data-stu-id="71d50-173">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="71d50-174">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/11801).</span><span class="sxs-lookup"><span data-stu-id="71d50-174">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="71d50-175">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="71d50-175">Configuration</span></span>

<span data-ttu-id="71d50-176">Die Konfiguration von Protokollierungsanbietern erfolgt durch mindestens einen Konfigurationsanbieter:</span><span class="sxs-lookup"><span data-stu-id="71d50-176">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="71d50-177">Dateiformate (INI, JSON und XML)</span><span class="sxs-lookup"><span data-stu-id="71d50-177">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="71d50-178">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="71d50-178">Command-line arguments.</span></span>
* <span data-ttu-id="71d50-179">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="71d50-179">Environment variables.</span></span>
* <span data-ttu-id="71d50-180">Speicherinterne .NET-Objekte</span><span class="sxs-lookup"><span data-stu-id="71d50-180">In-memory .NET objects.</span></span>
* <span data-ttu-id="71d50-181">Der unverschlüsselte Speicher von [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="71d50-181">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="71d50-182">Ein unverschlüsselter Benutzerspeicher, z.B. [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="71d50-182">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="71d50-183">Benutzerdefinierte Anbieter (installiert oder erstellt).</span><span class="sxs-lookup"><span data-stu-id="71d50-183">Custom providers (installed or created).</span></span>

<span data-ttu-id="71d50-184">Die Konfiguration der Protokollierung wird meist vom `Logging`-Abschnitt von Anwendungseinstellungsdateien angegeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-184">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="71d50-185">Im folgenden Beispiel werden die Inhalte einer herkömmlichen *appsettings.Development.json*-Datei veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="71d50-185">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="71d50-186">Die `Logging`-Eigenschaft kann den `LogLevel` und Protokollanbietereigenschaften beinhalten (Konsole wird angezeigt).</span><span class="sxs-lookup"><span data-stu-id="71d50-186">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="71d50-187">Die `LogLevel`-Eigenschaft unter `Logging` gibt den Mindest[grad](#log-level) an, der für ausgewählte Kategorien protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="71d50-187">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="71d50-188">Im Beispiel protokollieren die Kategorien `System` und `Microsoft` mit dem Grad `Information` und alle anderen Kategorien mit dem Grad `Debug`.</span><span class="sxs-lookup"><span data-stu-id="71d50-188">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="71d50-189">Weitere Eigenschaften unter `Logging` geben Protokollierungsanbieter an.</span><span class="sxs-lookup"><span data-stu-id="71d50-189">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="71d50-190">Das Beispiel bezieht sich auf den Konsolenanbieter.</span><span class="sxs-lookup"><span data-stu-id="71d50-190">The example is for the Console provider.</span></span> <span data-ttu-id="71d50-191">Wenn ein Anbieter [Protokollbereiche](#log-scopes) unterstützt, gibt `IncludeScopes` an, ob sie aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="71d50-191">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="71d50-192">Eine Anbietereigenschaft (z.B. `Console` im Beispiel) kann auch eine `LogLevel`-Eigenschaft angeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-192">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="71d50-193">`LogLevel` unter einem Anbieter gibt die Grade an, die für diesen Anbieter protokolliert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="71d50-193">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="71d50-194">Wenn Grade in `Logging.{providername}.LogLevel` angegeben werden, überschreiben sie alle Angaben in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="71d50-194">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="71d50-195">Die Protokollierungs-API umfasst kein Szenario zum Ändern der Protokollebene, während eine App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-195">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="71d50-196">Einige Konfigurationsanbieter können jedoch die Konfiguration erneut laden, was sich unmittelbar auf die Protokollierungskonfiguration auswirkt.</span><span class="sxs-lookup"><span data-stu-id="71d50-196">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="71d50-197">Beispielsweise lädt der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider), der durch `CreateDefaultBuilder` zum Lesen von Einstellungsdateien hinzugefügt wird, die Protokollierungskonfiguration standardmäßig erneut.</span><span class="sxs-lookup"><span data-stu-id="71d50-197">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="71d50-198">Wenn die Konfiguration im Code geändert wird, während eine App ausgeführt wird, kann die App [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) aufrufen, um ihre Protokollierungskonfiguration zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="71d50-198">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="71d50-199">Informationen zur Implementierung von Konfigurationsanbieter finden Sie hier: <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="71d50-199">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="71d50-200">Beispiel einer Protokollierungsausgabe</span><span class="sxs-lookup"><span data-stu-id="71d50-200">Sample logging output</span></span>

<span data-ttu-id="71d50-201">Mit dem im vorherigen Abschnitt gezeigten Beispielcode werden Protokolle in der Konsole angezeigt, wenn die Anwendung über die Befehlszeile ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-201">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="71d50-202">Hier ein Beispiel für eine Konsolenausgabe:</span><span class="sxs-lookup"><span data-stu-id="71d50-202">Here's an example of console output:</span></span>

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

<span data-ttu-id="71d50-203">Die vorherigen Protokolle wurden generiert, indem eine HTTP Get-Anforderung an die Beispiel-App unter `http://localhost:5000/api/todo/0` vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="71d50-203">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="71d50-204">Nachfolgend sehen Sie, wie die gleichen Protokolle im Debugfenster angezeigt werden, wenn Sie die Beispiel-App in Visual Studio ausführen:</span><span class="sxs-lookup"><span data-stu-id="71d50-204">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="71d50-205">Die über die `ILogger`-Aufrufe aus dem vorherigen Abschnitt erstellten Protokolle beginnen mit „TodoApiSample“.</span><span class="sxs-lookup"><span data-stu-id="71d50-205">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="71d50-206">Protokolle, die mit Microsoft-Kategorien beginnen, stammen aus ASP.NET Core-Frameworkcode.</span><span class="sxs-lookup"><span data-stu-id="71d50-206">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="71d50-207">ASP.NET Core und Anwendungscode verwenden dieselbe Protokollierungs-API und dieselben Protokollierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="71d50-207">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="71d50-208">In den übrigen Abschnitten dieses Artikels werden einige Details und Optionen für die Protokollierung erläutert.</span><span class="sxs-lookup"><span data-stu-id="71d50-208">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="71d50-209">NuGet-Pakete</span><span class="sxs-lookup"><span data-stu-id="71d50-209">NuGet packages</span></span>

<span data-ttu-id="71d50-210">Die Schnittstellen `ILogger` und `ILoggerFactory` befinden sich in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), ihre Standardimplementierungen sind in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) enthalten.</span><span class="sxs-lookup"><span data-stu-id="71d50-210">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="71d50-211">Protokollkategorie</span><span class="sxs-lookup"><span data-stu-id="71d50-211">Log category</span></span>

<span data-ttu-id="71d50-212">Wenn ein `ILogger`-Objekt erstellt wird, wird eine *Kategorie* für dieses Objekt angegeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-212">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="71d50-213">Diese Kategorie ist in jeder Protokollmeldung enthalten, die von dieser Instanz von `ILogger` erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-213">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="71d50-214">Die Kategorie kann eine beliebige Zeichenfolge sein, aber die Konvention besteht in der Verwendung des Klassennamens, z.B. „TodoApi.Controllers.TodoController“.</span><span class="sxs-lookup"><span data-stu-id="71d50-214">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="71d50-215">Verwenden Sie `ILogger<T>` zum Abrufen einer `ILogger`-Instanz, die den vollqualifizierten Typnamen von `T` als Kategorie verwendet:</span><span class="sxs-lookup"><span data-stu-id="71d50-215">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="71d50-216">Um die Kategorie explizit anzugeben, rufen Sie `ILoggerFactory.CreateLogger` auf:</span><span class="sxs-lookup"><span data-stu-id="71d50-216">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="71d50-217">`ILogger<T>` entspricht dem Aufruf von `CreateLogger` mit dem vollqualifizierten Typnamen `T`.</span><span class="sxs-lookup"><span data-stu-id="71d50-217">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="71d50-218">Protokolliergrad</span><span class="sxs-lookup"><span data-stu-id="71d50-218">Log level</span></span>

<span data-ttu-id="71d50-219">Jedes Protokoll gibt einen <xref:Microsoft.Extensions.Logging.LogLevel>-Wert an.</span><span class="sxs-lookup"><span data-stu-id="71d50-219">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="71d50-220">Der Protokolliergrad gibt den Schweregrad oder die Wichtigkeit an.</span><span class="sxs-lookup"><span data-stu-id="71d50-220">The log level indicates the severity or importance.</span></span> <span data-ttu-id="71d50-221">Sie können beispielsweise ein `Information`-Protokoll schreiben, wenn eine Methode normal endet, und ein `Warning`-Protokoll, wenn eine Methode den Statuscode *404 Not Found* zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="71d50-221">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="71d50-222">Der folgende Code erstellt `Information`- und `Warning`-Protokolle:</span><span class="sxs-lookup"><span data-stu-id="71d50-222">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="71d50-223">Im Code oben ist der erste Parameter die [Protokollereignis-ID](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="71d50-223">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="71d50-224">Der zweite Parameter ist eine Meldungsvorlage mit Platzhaltern für Argumentwerte, die von den verbleibenden Methodenparametern bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-224">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="71d50-225">Die Methodenparameter werden im [Abschnitt „Meldungsvorlage“](#log-message-template) später in diesem Artikel erläutert.</span><span class="sxs-lookup"><span data-stu-id="71d50-225">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="71d50-226">Protokollmethoden, die den Protokolliergrad im Methodennamen enthalten (z.B. `LogInformation` und `LogWarning`), sind [Erweiterungsmethoden für ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="71d50-226">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="71d50-227">Diese Methoden rufen eine `Log`-Methode auf, die einen `LogLevel`-Parameter annimmt.</span><span class="sxs-lookup"><span data-stu-id="71d50-227">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="71d50-228">Sie können anstelle eines Aufrufs dieser Erweiterungsmethoden die `Log`-Methode direkt aufrufen, aber die Syntax ist relativ kompliziert.</span><span class="sxs-lookup"><span data-stu-id="71d50-228">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="71d50-229">Weitere Informationen finden Sie unter <xref:Microsoft.Extensions.Logging.ILogger> und im [Quellcode für Protokollierungserweiterungen](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="71d50-229">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="71d50-230">ASP.NET Core definiert die folgenden Protokolliergrade. Die Reihenfolge reicht vom geringsten bis zum höchsten Schweregrad.</span><span class="sxs-lookup"><span data-stu-id="71d50-230">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="71d50-231">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="71d50-231">Trace = 0</span></span>

  <span data-ttu-id="71d50-232">Protokolliert Informationen, die in der Regel nur für das Debuggen nützlich sind.</span><span class="sxs-lookup"><span data-stu-id="71d50-232">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="71d50-233">Diese Meldungen können sensible Anwendungsdaten enthalten und sollten daher nicht in einer Produktionsumgebung aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-233">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="71d50-234">*Standardmäßig deaktiviert.*</span><span class="sxs-lookup"><span data-stu-id="71d50-234">*Disabled by default.*</span></span>

* <span data-ttu-id="71d50-235">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="71d50-235">Debug = 1</span></span>

  <span data-ttu-id="71d50-236">Protokolliert Informationen, die bei der Entwicklung und beim Debuggen hilfreich sein können.</span><span class="sxs-lookup"><span data-stu-id="71d50-236">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="71d50-237">Beispiel: `Entering method Configure with flag set to true.` Aktivieren Sie Protokolle mit dem Protokolliergrad `Debug` aufgrund des hohen Protokollaufkommens nur zur Problembehandlung in der Produktion.</span><span class="sxs-lookup"><span data-stu-id="71d50-237">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="71d50-238">Information = 2</span><span class="sxs-lookup"><span data-stu-id="71d50-238">Information = 2</span></span>

  <span data-ttu-id="71d50-239">Zur Nachverfolgung des allgemeinen Ablaufs der App.</span><span class="sxs-lookup"><span data-stu-id="71d50-239">For tracking the general flow of the app.</span></span> <span data-ttu-id="71d50-240">Diese Protokolle haben typischerweise einen langfristigen Nutzen.</span><span class="sxs-lookup"><span data-stu-id="71d50-240">These logs typically have some long-term value.</span></span> <span data-ttu-id="71d50-241">Ein Beispiel: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="71d50-241">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="71d50-242">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="71d50-242">Warning = 3</span></span>

  <span data-ttu-id="71d50-243">Für ungewöhnliche oder unerwartete Ereignisse im App-Verlauf.</span><span class="sxs-lookup"><span data-stu-id="71d50-243">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="71d50-244">Dazu können Fehler oder andere Bedingungen gehören, die zwar nicht zum Beenden der App führen, aber eventuell untersucht werden müssen.</span><span class="sxs-lookup"><span data-stu-id="71d50-244">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="71d50-245">Behandelte Ausnahmen sind eine typische Verwendung für den Protokolliergrad `Warning`.</span><span class="sxs-lookup"><span data-stu-id="71d50-245">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="71d50-246">Ein Beispiel: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="71d50-246">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="71d50-247">Error = 4</span><span class="sxs-lookup"><span data-stu-id="71d50-247">Error = 4</span></span>

  <span data-ttu-id="71d50-248">Für Fehler und Ausnahmen, die nicht behandelt werden können.</span><span class="sxs-lookup"><span data-stu-id="71d50-248">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="71d50-249">Diese Meldungen weisen auf einen Fehler in der aktuellen Aktivität oder im aktuellen Vorgang (z.B. in der aktuellen HTTP-Anforderung) und nicht auf einen anwendungsweiten Fehler hin.</span><span class="sxs-lookup"><span data-stu-id="71d50-249">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="71d50-250">Beispielprotokollmeldung: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="71d50-250">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="71d50-251">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="71d50-251">Critical = 5</span></span>

  <span data-ttu-id="71d50-252">Für Fehler, die sofortige Aufmerksamkeit erfordern.</span><span class="sxs-lookup"><span data-stu-id="71d50-252">For failures that require immediate attention.</span></span> <span data-ttu-id="71d50-253">Beispiel: Szenarios mit Datenverlust, Speichermangel.</span><span class="sxs-lookup"><span data-stu-id="71d50-253">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="71d50-254">Verwenden Sie den Protokolliergrad, um die Menge an Protokollausgabedaten zu steuern, die in ein bestimmtes Speichermedium geschrieben oder an ein Anzeigefenster ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-254">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="71d50-255">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="71d50-255">For example:</span></span>

* <span data-ttu-id="71d50-256">In einer Produktionsumgebung</span><span class="sxs-lookup"><span data-stu-id="71d50-256">In production:</span></span>
  * <span data-ttu-id="71d50-257">Das Protokollieren von `Trace` über die `Information`-Ebenen verursacht viele detaillierte Protokollmeldungen.</span><span class="sxs-lookup"><span data-stu-id="71d50-257">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="71d50-258">Protokollieren Sie `Trace` über Nachrichten auf `Information`-Ebene in einem kostengünstigen Speicher mit hohem Volumen, um die Kosten zu überwachen und die Grenzwerte des Datenspeichers nicht zu überschreiten.</span><span class="sxs-lookup"><span data-stu-id="71d50-258">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="71d50-259">Das Protokollieren von `Warning` über die `Critical`-Ebenen verursacht weniger und kürzere Protokollmeldungen.</span><span class="sxs-lookup"><span data-stu-id="71d50-259">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="71d50-260">Daher müssen Sie sich über Kosten- und Speichergrenzwerte keine Sorgen machen und sind bei der Auswahl des Datenspeichers flexibler.</span><span class="sxs-lookup"><span data-stu-id="71d50-260">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="71d50-261">Entwicklungsphase:</span><span class="sxs-lookup"><span data-stu-id="71d50-261">During development:</span></span>
  * <span data-ttu-id="71d50-262">Protokollieren Sie `Warning` über `Critical`-Meldungen an der Konsole.</span><span class="sxs-lookup"><span data-stu-id="71d50-262">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="71d50-263">Fügen Sie bei der Fehlerbehebung `Trace` über `Information`-Meldungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="71d50-263">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="71d50-264">Im Abschnitt [Protokollfilterung](#log-filtering) weiter unten in diesem Artikel wird erläutert, wie Sie steuern, welche Protokolliergrade ein Anbieter verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="71d50-264">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="71d50-265">ASP.NET Core schreibt Protokolle für Frameworkereignisse.</span><span class="sxs-lookup"><span data-stu-id="71d50-265">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="71d50-266">Die zuvor in diesem Artikel gezeigten Protokollbeispiele enthielten Protokolle unterhalb des Protokolliergrads `Information`, deshalb wurden keine Protokolle des Protokolliergrads `Debug` oder `Trace` erstellt.</span><span class="sxs-lookup"><span data-stu-id="71d50-266">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="71d50-267">Hier ist ein Beispiel für Konsolenprotokolle, die durch Ausführen der Beispiel-App generiert werden, die so konfiguriert ist, dass sie `Debug`-Protokolle anzeigt:</span><span class="sxs-lookup"><span data-stu-id="71d50-267">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="71d50-268">Protokollereignis-ID</span><span class="sxs-lookup"><span data-stu-id="71d50-268">Log event ID</span></span>

<span data-ttu-id="71d50-269">Jedes Protokoll kann eine *Ereignis-ID* angeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-269">Each log can specify an *event ID*.</span></span> <span data-ttu-id="71d50-270">Die Beispiel-App verwendet hierzu eine lokal definierte `LoggingEvents`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="71d50-270">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="71d50-271">Eine Ereignis-ID ordnet eine Gruppe von Ereignissen zu.</span><span class="sxs-lookup"><span data-stu-id="71d50-271">An event ID associates a set of events.</span></span> <span data-ttu-id="71d50-272">Beispielsweise können alle Protokolle, die sich auf die Anzeige einer Liste von Elementen auf einer Seite beziehen, 1001 sein.</span><span class="sxs-lookup"><span data-stu-id="71d50-272">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="71d50-273">Der Protokollierungsanbieter kann die Ereignis-ID in einem ID-Feld, in der Protokollierungsmeldung oder gar nicht speichern.</span><span class="sxs-lookup"><span data-stu-id="71d50-273">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="71d50-274">Der Debuganbieter zeigt keine Ereignis-IDs an.</span><span class="sxs-lookup"><span data-stu-id="71d50-274">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="71d50-275">Der Konsolenanbieter zeigt Ereignis-IDs in Klammern hinter der Kategorie an:</span><span class="sxs-lookup"><span data-stu-id="71d50-275">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="71d50-276">Protokollmeldungsvorlage</span><span class="sxs-lookup"><span data-stu-id="71d50-276">Log message template</span></span>

<span data-ttu-id="71d50-277">Jedes Protokoll gibt eine Meldungsvorlage an.</span><span class="sxs-lookup"><span data-stu-id="71d50-277">Each log specifies a message template.</span></span> <span data-ttu-id="71d50-278">Die Meldungsvorlage kann Platzhalter enthalten, für die Argumente bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-278">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="71d50-279">Verwenden Sie Namen für die Platzhalter, keine Zahlen.</span><span class="sxs-lookup"><span data-stu-id="71d50-279">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="71d50-280">Die Reihenfolge der Platzhalter – nicht ihre Namen – bestimmt, welche Parameter verwendet werden, um ihre Werte zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="71d50-280">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="71d50-281">Beachten Sie im folgenden Code, dass die Parameternamen in der Meldungsvorlage nicht in der richtigen Reihenfolge vorhanden sind:</span><span class="sxs-lookup"><span data-stu-id="71d50-281">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="71d50-282">Dieser Code erstellt eine Protokollierungsmeldung mit den Parameterwerten in der richtigen Reihenfolge:</span><span class="sxs-lookup"><span data-stu-id="71d50-282">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="71d50-283">Das Protokollierungsframework funktioniert auf diese Weise, damit Protokollierungsanbieter [semantische Protokollierung, die auch als strukturierte Protokollierung bezeichnet wird](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging), implementieren können.</span><span class="sxs-lookup"><span data-stu-id="71d50-283">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="71d50-284">Die Argumente selbst (nicht nur die formatierte Meldungsvorlage) werden an das Protokollierungssystem übergeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-284">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="71d50-285">Diese Informationen ermöglichen es Protokollierungsanbietern, die Parameterwerte als Felder zu speichern.</span><span class="sxs-lookup"><span data-stu-id="71d50-285">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="71d50-286">Nehmen wir zum Beispiel an, dass Aufrufe von Protokollierungsmethoden folgendermaßen aussehen:</span><span class="sxs-lookup"><span data-stu-id="71d50-286">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="71d50-287">Wenn Sie die Protokolle an Azure Table Storage senden, kann jede Azure Table-Entität die Eigenschaften `ID` und `RequestTime` aufweisen. Dies vereinfacht die Abfrage von Protokolldaten.</span><span class="sxs-lookup"><span data-stu-id="71d50-287">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="71d50-288">Eine Abfrage kann alle Protokolle in einem bestimmten `RequestTime`-Bereich finden, ohne die Uhrzeit aus den Textmeldungen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="71d50-288">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="71d50-289">Protokollieren von Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="71d50-289">Logging exceptions</span></span>

<span data-ttu-id="71d50-290">Die Protokollierungsmethoden umfassen Überladungen, die das Übergeben von Ausnahmen ermöglichen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="71d50-290">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="71d50-291">Die verschiedenen Anbieter verarbeiten die Ausnahmeinformationen auf unterschiedliche Weise.</span><span class="sxs-lookup"><span data-stu-id="71d50-291">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="71d50-292">Hier sehen Sie ein Beispiel einer Debuganbieterausgabe aus dem obigen Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="71d50-292">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="71d50-293">Protokollfilterung</span><span class="sxs-lookup"><span data-stu-id="71d50-293">Log filtering</span></span>

<span data-ttu-id="71d50-294">Sie können einen Mindestprotokolliergrad für einen bestimmten Anbieter und eine bestimmte Kategorie oder für alle Anbieter oder alle Kategorien festlegen.</span><span class="sxs-lookup"><span data-stu-id="71d50-294">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="71d50-295">Alle Protokolle unter dem Mindestgrad werden nicht an diesen Anbieter weitergeleitet, sodass sie nicht angezeigt oder gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-295">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="71d50-296">Wenn Sie alle Protokolle unterdrücken möchten, geben Sie `LogLevel.None` als Mindestprotokolliergrad an.</span><span class="sxs-lookup"><span data-stu-id="71d50-296">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="71d50-297">Der ganzzahlige Wert von `LogLevel.None` lautet 6 und liegt über `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="71d50-297">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="71d50-298">Erstellen von Filterregeln in der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="71d50-298">Create filter rules in configuration</span></span>

<span data-ttu-id="71d50-299">Der Projektvorlagencode ruft `CreateDefaultBuilder` auf, um die Protokollierung für die Konsolen-, Debug- und EventSource-Anbieter (ASP.NET Core 2.2 oder höher) einzurichten.</span><span class="sxs-lookup"><span data-stu-id="71d50-299">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="71d50-300">Die `CreateDefaultBuilder`-Methode richtet die Protokollierung ein, um nach der Konfiguration in einem `Logging`-Abschnitt zu suchen; dies wird [weiter oben in diesem Artikel](#configuration) erläutert.</span><span class="sxs-lookup"><span data-stu-id="71d50-300">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="71d50-301">Die Konfigurationsdaten geben die Mindestprotokolliergrade nach Anbieter und Kategorie an, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="71d50-301">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="71d50-302">Diese JSON-Konfiguration erstellt sechs Filterregeln: eine für den Debuganbieter, vier für den Konsolenanbieter und eine für alle Anbieter.</span><span class="sxs-lookup"><span data-stu-id="71d50-302">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="71d50-303">Eine einzelne Regel wird für jeden Anbieter ausgewählt, wenn ein `ILogger`-Objekt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-303">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="71d50-304">Filterregeln im Code</span><span class="sxs-lookup"><span data-stu-id="71d50-304">Filter rules in code</span></span>

<span data-ttu-id="71d50-305">Das folgende Beispiel zeigt, wie Filterregeln im Code registriert werden:</span><span class="sxs-lookup"><span data-stu-id="71d50-305">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="71d50-306">Das zweite `AddFilter` gibt den Debuganbieter durch Verwendung des zugehörigen Typnamens an.</span><span class="sxs-lookup"><span data-stu-id="71d50-306">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="71d50-307">Das erste `AddFilter` gilt für alle Anbieter, weil kein Anbietertyp angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-307">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="71d50-308">Anwendung von Filterregeln</span><span class="sxs-lookup"><span data-stu-id="71d50-308">How filtering rules are applied</span></span>

<span data-ttu-id="71d50-309">Die Konfigurationsdaten und der in den vorangegangenen Beispielen gezeigte `AddFilter`-Code erzeugen die in der folgenden Tabelle gezeigten Regeln.</span><span class="sxs-lookup"><span data-stu-id="71d50-309">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="71d50-310">Die ersten sechs Regeln stammen aus dem Konfigurationsbeispiel, die letzten zwei Filter stammen aus dem Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="71d50-310">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="71d50-311">Anzahl</span><span class="sxs-lookup"><span data-stu-id="71d50-311">Number</span></span> | <span data-ttu-id="71d50-312">Anbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-312">Provider</span></span>      | <span data-ttu-id="71d50-313">Kategorien beginnend mit...</span><span class="sxs-lookup"><span data-stu-id="71d50-313">Categories that begin with ...</span></span>          | <span data-ttu-id="71d50-314">Mindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="71d50-314">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="71d50-315">1</span><span class="sxs-lookup"><span data-stu-id="71d50-315">1</span></span>      | <span data-ttu-id="71d50-316">Debug</span><span class="sxs-lookup"><span data-stu-id="71d50-316">Debug</span></span>         | <span data-ttu-id="71d50-317">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="71d50-317">All categories</span></span>                          | <span data-ttu-id="71d50-318">Information</span><span class="sxs-lookup"><span data-stu-id="71d50-318">Information</span></span>       |
| <span data-ttu-id="71d50-319">2</span><span class="sxs-lookup"><span data-stu-id="71d50-319">2</span></span>      | <span data-ttu-id="71d50-320">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-320">Console</span></span>       | <span data-ttu-id="71d50-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="71d50-321">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="71d50-322">Warnung</span><span class="sxs-lookup"><span data-stu-id="71d50-322">Warning</span></span>           |
| <span data-ttu-id="71d50-323">3</span><span class="sxs-lookup"><span data-stu-id="71d50-323">3</span></span>      | <span data-ttu-id="71d50-324">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-324">Console</span></span>       | <span data-ttu-id="71d50-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="71d50-325">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="71d50-326">Debug</span><span class="sxs-lookup"><span data-stu-id="71d50-326">Debug</span></span>             |
| <span data-ttu-id="71d50-327">4</span><span class="sxs-lookup"><span data-stu-id="71d50-327">4</span></span>      | <span data-ttu-id="71d50-328">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-328">Console</span></span>       | <span data-ttu-id="71d50-329">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="71d50-329">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="71d50-330">Fehler</span><span class="sxs-lookup"><span data-stu-id="71d50-330">Error</span></span>             |
| <span data-ttu-id="71d50-331">5</span><span class="sxs-lookup"><span data-stu-id="71d50-331">5</span></span>      | <span data-ttu-id="71d50-332">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-332">Console</span></span>       | <span data-ttu-id="71d50-333">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="71d50-333">All categories</span></span>                          | <span data-ttu-id="71d50-334">Information</span><span class="sxs-lookup"><span data-stu-id="71d50-334">Information</span></span>       |
| <span data-ttu-id="71d50-335">6</span><span class="sxs-lookup"><span data-stu-id="71d50-335">6</span></span>      | <span data-ttu-id="71d50-336">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-336">All providers</span></span> | <span data-ttu-id="71d50-337">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="71d50-337">All categories</span></span>                          | <span data-ttu-id="71d50-338">Debug</span><span class="sxs-lookup"><span data-stu-id="71d50-338">Debug</span></span>             |
| <span data-ttu-id="71d50-339">7</span><span class="sxs-lookup"><span data-stu-id="71d50-339">7</span></span>      | <span data-ttu-id="71d50-340">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-340">All providers</span></span> | <span data-ttu-id="71d50-341">System</span><span class="sxs-lookup"><span data-stu-id="71d50-341">System</span></span>                                  | <span data-ttu-id="71d50-342">Debug</span><span class="sxs-lookup"><span data-stu-id="71d50-342">Debug</span></span>             |
| <span data-ttu-id="71d50-343">8</span><span class="sxs-lookup"><span data-stu-id="71d50-343">8</span></span>      | <span data-ttu-id="71d50-344">Debug</span><span class="sxs-lookup"><span data-stu-id="71d50-344">Debug</span></span>         | <span data-ttu-id="71d50-345">Microsoft</span><span class="sxs-lookup"><span data-stu-id="71d50-345">Microsoft</span></span>                               | <span data-ttu-id="71d50-346">Ablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="71d50-346">Trace</span></span>             |

<span data-ttu-id="71d50-347">Wenn ein `ILogger`-Objekt erstellt wird, wählt das `ILoggerFactory`-Objekt eine einzige Regel pro Anbieter aus, die auf diese Protokollierung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-347">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="71d50-348">Alle von einer `ILogger`-Instanz geschriebenen Meldungen werden auf Grundlage der ausgewählten Regeln gefiltert.</span><span class="sxs-lookup"><span data-stu-id="71d50-348">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="71d50-349">Aus den verfügbaren Regeln wird die für jeden Anbieter und jedes Kategoriepaar spezifischste Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="71d50-349">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="71d50-350">Beim Erstellen von `ILogger` für eine vorgegebene Kategorie wird für jeden Anbieter der folgende Algorithmus verwendet:</span><span class="sxs-lookup"><span data-stu-id="71d50-350">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="71d50-351">Es werden alle Regeln ausgewählt, die dem Anbieter oder dem zugehörigen Alias entsprechen.</span><span class="sxs-lookup"><span data-stu-id="71d50-351">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="71d50-352">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln mit einem leeren Anbieter ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="71d50-352">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="71d50-353">Aus dem Ergebnis des vorherigen Schritts werden die Regeln mit dem längsten übereinstimmenden Kategoriepräfix ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="71d50-353">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="71d50-354">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln ohne Angabe einer Kategorie ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="71d50-354">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="71d50-355">Wenn mehrere Regeln ausgewählt sind, wird die **letzte** Regel verwendet.</span><span class="sxs-lookup"><span data-stu-id="71d50-355">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="71d50-356">Wenn keine Regel ausgewählt ist, wird `MinimumLevel` verwendet.</span><span class="sxs-lookup"><span data-stu-id="71d50-356">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="71d50-357">Angenommen, Sie erstellen mit der oben aufgeführten Liste mit Regeln ein `ILogger`-Objekt für die Kategorie „Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine“:</span><span class="sxs-lookup"><span data-stu-id="71d50-357">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="71d50-358">Für den Debuganbieter gelten die Regeln 1, 6 und 8.</span><span class="sxs-lookup"><span data-stu-id="71d50-358">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="71d50-359">Regel 8 ist die spezifischste Regel, deshalb wird diese Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="71d50-359">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="71d50-360">Für den Konsolenanbieter gelten die Regeln 3, 4, 5 und 6.</span><span class="sxs-lookup"><span data-stu-id="71d50-360">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="71d50-361">Regel 3 ist die spezifischste Regel.</span><span class="sxs-lookup"><span data-stu-id="71d50-361">Rule 3 is most specific.</span></span>

<span data-ttu-id="71d50-362">Die sich ergebende `ILogger`-Instanz sendet Protokolle mit dem Protokolliergrad `Trace` und höher an den Debuganbieter.</span><span class="sxs-lookup"><span data-stu-id="71d50-362">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="71d50-363">Protokolle mit dem Protokolliergrad `Debug` und höher werden an den Konsolenanbieter gesendet.</span><span class="sxs-lookup"><span data-stu-id="71d50-363">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="71d50-364">Anbieteraliase</span><span class="sxs-lookup"><span data-stu-id="71d50-364">Provider aliases</span></span>

<span data-ttu-id="71d50-365">Jeder Anbieter definiert einen *Alias*, der in der Konfiguration anstelle des vollqualifizierten Typnamens verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="71d50-365">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="71d50-366">Verwenden Sie für die integrierten Anbieter die folgenden Aliase:</span><span class="sxs-lookup"><span data-stu-id="71d50-366">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="71d50-367">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-367">Console</span></span>
* <span data-ttu-id="71d50-368">Debug</span><span class="sxs-lookup"><span data-stu-id="71d50-368">Debug</span></span>
* <span data-ttu-id="71d50-369">EventSource</span><span class="sxs-lookup"><span data-stu-id="71d50-369">EventSource</span></span>
* <span data-ttu-id="71d50-370">EventLog</span><span class="sxs-lookup"><span data-stu-id="71d50-370">EventLog</span></span>
* <span data-ttu-id="71d50-371">TraceSource</span><span class="sxs-lookup"><span data-stu-id="71d50-371">TraceSource</span></span>
* <span data-ttu-id="71d50-372">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="71d50-372">AzureAppServicesFile</span></span>
* <span data-ttu-id="71d50-373">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="71d50-373">AzureAppServicesBlob</span></span>
* <span data-ttu-id="71d50-374">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="71d50-374">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="71d50-375">Standardmindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="71d50-375">Default minimum level</span></span>

<span data-ttu-id="71d50-376">Es gibt eine Einstellung für den Mindestprotokolliergrad, die nur dann wirksam wird, wenn für einen bestimmten Anbieter und eine bestimmte Kategorie keine Regeln aus Konfiguration oder Code gelten.</span><span class="sxs-lookup"><span data-stu-id="71d50-376">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="71d50-377">Im folgenden Beispiel wird das Festlegen des Mindestprotokolliergrads veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="71d50-377">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="71d50-378">Wenn Sie den Mindestprotokolliergrad nicht explizit festlegen, lautet der Standardwert `Information`, d.h. Protokolle der Ebene `Trace` und `Debug` werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="71d50-378">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="71d50-379">Filterfunktionen</span><span class="sxs-lookup"><span data-stu-id="71d50-379">Filter functions</span></span>

<span data-ttu-id="71d50-380">Eine Filterfunktion wird für alle Anbieter und Kategorien aufgerufen, denen keine Regeln durch Konfiguration oder Code zugewiesen sind.</span><span class="sxs-lookup"><span data-stu-id="71d50-380">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="71d50-381">Code in der Funktion verfügt über Zugriff auf den Anbietertyp, die Kategorie und den Protokolliergrad.</span><span class="sxs-lookup"><span data-stu-id="71d50-381">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="71d50-382">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="71d50-382">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="71d50-383">Systemkategorien und -protokolliergrade</span><span class="sxs-lookup"><span data-stu-id="71d50-383">System categories and levels</span></span>

<span data-ttu-id="71d50-384">Dies sind einige Kategorien, die vom ASP.NET Core und Entity Framework Core verwendet werden, mit Hinweisen darauf, welche Protokolle von ihnen zu erwarten sind:</span><span class="sxs-lookup"><span data-stu-id="71d50-384">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="71d50-385">Kategorie</span><span class="sxs-lookup"><span data-stu-id="71d50-385">Category</span></span>                            | <span data-ttu-id="71d50-386">Hinweise</span><span class="sxs-lookup"><span data-stu-id="71d50-386">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="71d50-387">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="71d50-387">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="71d50-388">Allgemeine ASP.NET Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="71d50-388">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="71d50-389">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="71d50-389">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="71d50-390">Gibt an, welche Schlüssel in Betracht gezogen, gefunden und verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="71d50-390">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="71d50-391">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="71d50-391">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="71d50-392">Hosts sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="71d50-392">Hosts allowed.</span></span> |
| <span data-ttu-id="71d50-393">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="71d50-393">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="71d50-394">Gibt an, wie lange es bis zum Abschluss von HTTP-Anforderungen gedauert hat und zu welcher Zeit sie gestartet wurden.</span><span class="sxs-lookup"><span data-stu-id="71d50-394">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="71d50-395">Gibt an, welche Hostingstartassemblys geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="71d50-395">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="71d50-396">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="71d50-396">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="71d50-397">MVC- und Razor-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="71d50-397">MVC and Razor diagnostics.</span></span> <span data-ttu-id="71d50-398">Modellbindung, Filterausführung, Ansichtskompilierung, Aktionsauswahl.</span><span class="sxs-lookup"><span data-stu-id="71d50-398">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="71d50-399">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="71d50-399">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="71d50-400">Gibt Routenabgleichsinformationen an.</span><span class="sxs-lookup"><span data-stu-id="71d50-400">Route matching information.</span></span> |
| <span data-ttu-id="71d50-401">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="71d50-401">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="71d50-402">Start-, Beendigungs- und Keep-Alive-Antworten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="71d50-402">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="71d50-403">HTTPS-Zertifikatinformationen.</span><span class="sxs-lookup"><span data-stu-id="71d50-403">HTTPS certificate information.</span></span> |
| <span data-ttu-id="71d50-404">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="71d50-404">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="71d50-405">Die bereitgestellten Dateien.</span><span class="sxs-lookup"><span data-stu-id="71d50-405">Files served.</span></span> |
| <span data-ttu-id="71d50-406">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="71d50-406">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="71d50-407">Allgemeine Entity Framework Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="71d50-407">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="71d50-408">Datenbankaktivität und -konfiguration, Änderungserkennung, Migrationen.</span><span class="sxs-lookup"><span data-stu-id="71d50-408">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="71d50-409">Protokollbereiche</span><span class="sxs-lookup"><span data-stu-id="71d50-409">Log scopes</span></span>

 <span data-ttu-id="71d50-410">Ein *Bereich* kann einen Satz logischer Vorgänge gruppieren.</span><span class="sxs-lookup"><span data-stu-id="71d50-410">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="71d50-411">Diese Gruppierung kann verwendet werden, um an jedes Protokoll, das als Teil einer Gruppe erstellt wird, die gleichen Daten anzufügen.</span><span class="sxs-lookup"><span data-stu-id="71d50-411">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="71d50-412">So kann beispielsweise jedes Protokoll, das im Rahmen der Verarbeitung einer Transaktion erstellt wird, die Transaktions-ID enthalten.</span><span class="sxs-lookup"><span data-stu-id="71d50-412">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="71d50-413">Ein Bereich ist ein `IDisposable`-Typ, der von der <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>-Methode zurückgegeben und so lange beibehalten wird, bis er verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-413">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="71d50-414">Verwenden Sie einen Bereich, indem Sie Protokollierungsaufrufe mit einem `using`-Block umschließen, der als Wrapper verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="71d50-414">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="71d50-415">Der folgende Code aktiviert Bereiche für den Konsolenanbieter:</span><span class="sxs-lookup"><span data-stu-id="71d50-415">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="71d50-416">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="71d50-416">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="71d50-417">Um die bereichsbasierte Protokollierung zu aktivieren, muss die Konsolenprotokollierungsoption `IncludeScopes` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-417">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="71d50-418">Weitere Informationen zur Konfiguration finden Sie im Abschnitt [Konfiguration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="71d50-418">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="71d50-419">Jede Protokollmeldung enthält die bereichsbezogenen Informationen:</span><span class="sxs-lookup"><span data-stu-id="71d50-419">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="71d50-420">Integrierte Protokollierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-420">Built-in logging providers</span></span>

<span data-ttu-id="71d50-421">ASP.NET Core wird mit den folgenden Anbietern bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="71d50-421">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="71d50-422">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-422">Console</span></span>](#console-provider)
* [<span data-ttu-id="71d50-423">Debuggen</span><span class="sxs-lookup"><span data-stu-id="71d50-423">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="71d50-424">EventSource</span><span class="sxs-lookup"><span data-stu-id="71d50-424">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="71d50-425">EventLog</span><span class="sxs-lookup"><span data-stu-id="71d50-425">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="71d50-426">TraceSource</span><span class="sxs-lookup"><span data-stu-id="71d50-426">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="71d50-427">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="71d50-427">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="71d50-428">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="71d50-428">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="71d50-429">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="71d50-429">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="71d50-430">Weitere Informationen zu „stdout“ und zur Debugprotokollierung mit dem ASP.NET Core-Modul finden Sie unter <xref:test/troubleshoot-azure-iis> und <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="71d50-430">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="71d50-431">Der Konsolenanbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-431">Console provider</span></span>

<span data-ttu-id="71d50-432">Das Anbieterpaket [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sendet eine Protokollausgabe an die Konsole.</span><span class="sxs-lookup"><span data-stu-id="71d50-432">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="71d50-433">Um die Ausgabe der Konsolenprotokollierung anzuzeigen, öffnen Sie eine Eingabeaufforderung im Projektordner und führen den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="71d50-433">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="71d50-434">Der Debuganbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-434">Debug provider</span></span>

<span data-ttu-id="71d50-435">Beim Anbieterpaket [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) erfolgt die Protokollausgabe unter Verwendung der Klasse [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine`-Methodenaufrufe).</span><span class="sxs-lookup"><span data-stu-id="71d50-435">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="71d50-436">Unter Linux werden Protokolle dieses Anbieters in */var/log/message* geschrieben.</span><span class="sxs-lookup"><span data-stu-id="71d50-436">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="71d50-437">Ereignisquellenanbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-437">Event Source provider</span></span>

<span data-ttu-id="71d50-438">Das [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource)-Anbieterpaket schreibt in eine plattformübergreifende Ereignisquelle mit dem Namen `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="71d50-438">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="71d50-439">Unter Windows verwendet der Anbieter die [Ereignisablaufverfolgung für Windows (ETW)](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="71d50-439">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="71d50-440">Der Ereignisquellenanbieter wird automatisch hinzugefügt, wenn `CreateDefaultBuilder` zum Erstellen des Hosts aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-440">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="71d50-441">dotnet-trace-Tool</span><span class="sxs-lookup"><span data-stu-id="71d50-441">dotnet trace tooling</span></span>

<span data-ttu-id="71d50-442">Das Tool [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) ist ein plattformübergreifendes globales Befehlszeilenschnittstellentool zum Sammeln von .NET Core-Ablaufverfolgungen eines ausgeführten Prozesses.</span><span class="sxs-lookup"><span data-stu-id="71d50-442">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="71d50-443">Das Tool sammelt <xref:Microsoft.Extensions.Logging.EventSource>-Anbieterdaten mithilfe einer <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="71d50-443">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="71d50-444">Installieren Sie das dotnet-trace-Tool mit dem folgenden Befehl:</span><span class="sxs-lookup"><span data-stu-id="71d50-444">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="71d50-445">Verwenden Sie das dotnet-trace-Tool, um die Ablaufverfolgung aus einer App zu erfassen:</span><span class="sxs-lookup"><span data-stu-id="71d50-445">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="71d50-446">Wenn die App den Host nicht mit `CreateDefaultBuilder` erstellt, fügen Sie den [Ereignisquellenanbieter](#event-source-provider) zur Protokollierungskonfiguration der App hinzu.</span><span class="sxs-lookup"><span data-stu-id="71d50-446">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="71d50-447">Führen Sie die App mit dem Befehl `dotnet run` aus.</span><span class="sxs-lookup"><span data-stu-id="71d50-447">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="71d50-448">Bestimmen Sie den Prozessbezeichner der .NET Core-App:</span><span class="sxs-lookup"><span data-stu-id="71d50-448">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="71d50-449">Unter Windows können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="71d50-449">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="71d50-450">Task-Manager (STRG+ALT+ENTF)</span><span class="sxs-lookup"><span data-stu-id="71d50-450">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="71d50-451">tasklist-Befehl</span><span class="sxs-lookup"><span data-stu-id="71d50-451">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="71d50-452">PowerShell-Befehl „Get-Process“</span><span class="sxs-lookup"><span data-stu-id="71d50-452">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="71d50-453">Verwenden Sie unter Linux den [pidof-Befehl](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="71d50-453">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="71d50-454">Suchen Sie den Prozessbezeichner für den Prozess, der den gleichen Namen wie die App-Assembly hat.</span><span class="sxs-lookup"><span data-stu-id="71d50-454">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="71d50-455">Führen Sie den `dotnet trace`-Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="71d50-455">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="71d50-456">Allgemeine Befehlssyntax:</span><span class="sxs-lookup"><span data-stu-id="71d50-456">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="71d50-457">Wenn Sie eine PowerShell-Befehlsshell verwenden, schließen Sie den `--providers`-Wert in einfache Anführungszeichen (`'`) ein:</span><span class="sxs-lookup"><span data-stu-id="71d50-457">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="71d50-458">Auf Plattformen, die nicht unter Windows ausgeführt werden, fügen Sie die `-f speedscope`-Option hinzu, um das Format der Ablaufverfolgungsdatei der Ausgabe in `speedscope` zu ändern.</span><span class="sxs-lookup"><span data-stu-id="71d50-458">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="71d50-459">Stichwort</span><span class="sxs-lookup"><span data-stu-id="71d50-459">Keyword</span></span> | <span data-ttu-id="71d50-460">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="71d50-460">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="71d50-461">1</span><span class="sxs-lookup"><span data-stu-id="71d50-461">1</span></span>       | <span data-ttu-id="71d50-462">Protokolliert Metaereignisse über die `LoggingEventSource`.</span><span class="sxs-lookup"><span data-stu-id="71d50-462">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="71d50-463">Es werden keine Ereignisse von `ILogger`) protokolliert.</span><span class="sxs-lookup"><span data-stu-id="71d50-463">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="71d50-464">2</span><span class="sxs-lookup"><span data-stu-id="71d50-464">2</span></span>       | <span data-ttu-id="71d50-465">Aktiviert das `Message`-Ereignis, wenn `ILogger.Log()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-465">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="71d50-466">Die Informationen werden in einer programmgesteuerten (nicht formatierten) Weise ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-466">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="71d50-467">4</span><span class="sxs-lookup"><span data-stu-id="71d50-467">4</span></span>       | <span data-ttu-id="71d50-468">Aktiviert das `FormatMessage`-Ereignis, wenn `ILogger.Log()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-468">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="71d50-469">Gibt die formatierte Zeichenfolgeversion der Informationen an.</span><span class="sxs-lookup"><span data-stu-id="71d50-469">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="71d50-470">8</span><span class="sxs-lookup"><span data-stu-id="71d50-470">8</span></span>       | <span data-ttu-id="71d50-471">Aktiviert das `MessageJson`-Ereignis, wenn `ILogger.Log()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-471">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="71d50-472">Stellt eine JSON-Darstellung der Argumente bereit.</span><span class="sxs-lookup"><span data-stu-id="71d50-472">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="71d50-473">Ereignisgrad</span><span class="sxs-lookup"><span data-stu-id="71d50-473">Event Level</span></span> | <span data-ttu-id="71d50-474">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="71d50-474">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="71d50-475">0</span><span class="sxs-lookup"><span data-stu-id="71d50-475">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="71d50-476">1</span><span class="sxs-lookup"><span data-stu-id="71d50-476">1</span></span>           | `Critical`      |
   | <span data-ttu-id="71d50-477">2</span><span class="sxs-lookup"><span data-stu-id="71d50-477">2</span></span>           | `Error`         |
   | <span data-ttu-id="71d50-478">3</span><span class="sxs-lookup"><span data-stu-id="71d50-478">3</span></span>           | `Warning`       |
   | <span data-ttu-id="71d50-479">4</span><span class="sxs-lookup"><span data-stu-id="71d50-479">4</span></span>           | `Informational` |
   | <span data-ttu-id="71d50-480">5</span><span class="sxs-lookup"><span data-stu-id="71d50-480">5</span></span>           | `Verbose`       |

   <span data-ttu-id="71d50-481">`FilterSpecs`-Einträge für `{Logger Category}` und `{Event Level}` stellen zusätzliche Protokollfilterbedingungen dar.</span><span class="sxs-lookup"><span data-stu-id="71d50-481">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="71d50-482">Trennen Sie die `FilterSpecs`-Einträge durch ein Semikolon (`;`).</span><span class="sxs-lookup"><span data-stu-id="71d50-482">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="71d50-483">Beispiel mit einer Windows-Befehlsshell (**keine** einfachen Anführungszeichen um den `--providers`-Wert):</span><span class="sxs-lookup"><span data-stu-id="71d50-483">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="71d50-484">Mit dem vorangestellten Komma wird Folgendes aktiviert:</span><span class="sxs-lookup"><span data-stu-id="71d50-484">The preceding command activates:</span></span>

   * <span data-ttu-id="71d50-485">Die Ereignisquellenprotokollierung zur Erzeugung von formatierten Zeichenfolgen (`4`) für Fehler (`2`)</span><span class="sxs-lookup"><span data-stu-id="71d50-485">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="71d50-486">`Microsoft.AspNetCore.Hosting`-Protokollierung auf `Informational`-Protokollierungsebene (`4`)</span><span class="sxs-lookup"><span data-stu-id="71d50-486">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="71d50-487">Halten Sie das dotnet-trace-Tool an, indem Sie die EINGABETASTE oder STRG+C drücken.</span><span class="sxs-lookup"><span data-stu-id="71d50-487">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="71d50-488">Die Ablaufverfolgung wird mit dem Namen *trace.nettrace* in dem Ordner gespeichert, in dem der `dotnet trace`-Befehl ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-488">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="71d50-489">Öffnen Sie die Ablaufverfolgung mit [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="71d50-489">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="71d50-490">Öffnen Sie die Datei *trace.nettrace*, und untersuchen Sie die Ablaufverfolgungsereignisse.</span><span class="sxs-lookup"><span data-stu-id="71d50-490">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="71d50-491">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="71d50-491">For more information, see:</span></span>

* <span data-ttu-id="71d50-492">[Ablaufverfolgung für das Hilfsprogramm zur Leistungsanalyse (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="71d50-492">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="71d50-493">[Ablaufverfolgung für das Hilfsprogramm zur Leistungsanalyse (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (GitHub-Repository-Dokumentation zu dotnet/diagnostics)</span><span class="sxs-lookup"><span data-stu-id="71d50-493">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="71d50-494">[LoggingEventSource-Klasse](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API-Browser)</span><span class="sxs-lookup"><span data-stu-id="71d50-494">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="71d50-495">[LoggingEventSource-Verweisquelle (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs)&ndash; Ändern Sie für das Abrufen der Verweisquelle für eine andere Version den Branch in `release/{Version}`, wobei `{Version}` die Version der gewünschten ASP.NET Core-Version darstellt.</span><span class="sxs-lookup"><span data-stu-id="71d50-495">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="71d50-496">[Perfview](#perfview) ist hilfreich zum Anzeigen der Ablaufverfolgung für Ereignisquellen.</span><span class="sxs-lookup"><span data-stu-id="71d50-496">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="71d50-497">PerfView</span><span class="sxs-lookup"><span data-stu-id="71d50-497">Perfview</span></span>

<span data-ttu-id="71d50-498">Verwenden Sie das [PerfView-Hilfsprogramm](https://github.com/Microsoft/perfview) zum Sammeln und Anzeigen von Protokollen.</span><span class="sxs-lookup"><span data-stu-id="71d50-498">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="71d50-499">Es gibt andere Tools zur Anzeige von ETW-Protokollen, aber PerfView bietet die besten Ergebnisse bei der Arbeit mit ETW-Ereignissen, die von ASP.NET Core ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-499">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="71d50-500">Um PerfView für das Erfassen von Ereignissen zu konfigurieren, die von diesem Anbieter protokolliert wurden, fügen Sie die Zeichenfolge `*Microsoft-Extensions-Logging` zur Liste **Zusätzliche Anbieter** hinzu.</span><span class="sxs-lookup"><span data-stu-id="71d50-500">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="71d50-501">(Vergessen Sie nicht das Sternchen am Anfang der Zeichenfolge.)</span><span class="sxs-lookup"><span data-stu-id="71d50-501">(Don't miss the asterisk at the start of the string.)</span></span>

![Zusätzliche PerfView-Anbieter](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="71d50-503">Der Windows-EventLog-Anbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-503">Windows EventLog provider</span></span>

<span data-ttu-id="71d50-504">Das Anbieterpaket [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sendet eine Protokollausgabe in das Windows-Ereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="71d50-504">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="71d50-505">Mithilfe von [AddEventLog-Überladungen](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) können Sie <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> übergeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-505">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="71d50-506">Wenn `null` oder nicht angegeben, werden die folgenden Standardeinstellungen verwendet:</span><span class="sxs-lookup"><span data-stu-id="71d50-506">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="71d50-507">`LogName` „Anwendung“</span><span class="sxs-lookup"><span data-stu-id="71d50-507">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="71d50-508">`SourceName` „.NET Runtime“</span><span class="sxs-lookup"><span data-stu-id="71d50-508">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="71d50-509">`MachineName` Lokaler Computer</span><span class="sxs-lookup"><span data-stu-id="71d50-509">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="71d50-510">Ereignisse werden für die [Warnstufe und höher](#log-level) protokolliert.</span><span class="sxs-lookup"><span data-stu-id="71d50-510">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="71d50-511">Legen Sie die Protokollierungsebene fest, um Ereignisse, die niedriger als `Warning` sind, zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="71d50-511">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="71d50-512">Fügen Sie beispielsweise Folgendes zur *appsettings.json*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="71d50-512">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="71d50-513">Der TraceSource-Anbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-513">TraceSource provider</span></span>

<span data-ttu-id="71d50-514">Das Anbieterpaket [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) verwendet die <xref:System.Diagnostics.TraceSource>-Bibliotheken und -Anbieter.</span><span class="sxs-lookup"><span data-stu-id="71d50-514">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="71d50-515">Mithilfe von [AddTraceSource-Überladungen](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) können Sie eine Quelloption und einen Listener für die Ablaufverfolgung übergeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-515">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="71d50-516">Um diesen Anbieter zu verwenden, muss eine App unter .NET Framework (anstelle von .NET Core) ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-516">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="71d50-517">Der Anbieter kann Nachrichten an eine Vielzahl von [Listenern](/dotnet/framework/debug-trace-profile/trace-listeners) weiterleiten, z.B. an den in der Beispiel-App verwendeten <xref:System.Diagnostics.TextWriterTraceListener>.</span><span class="sxs-lookup"><span data-stu-id="71d50-517">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="71d50-518">Der Azure App Service-Anbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-518">Azure App Service provider</span></span>

<span data-ttu-id="71d50-519">Das Anbieterpaket [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) schreibt Protokolle in Textdateien in das Dateisystem einer Azure App Service-App und in [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in einem Azure Storage-Konto.</span><span class="sxs-lookup"><span data-stu-id="71d50-519">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="71d50-520">Das Anbieterpaket ist nicht im freigegebenen Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="71d50-520">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="71d50-521">Zum Verwenden des Anbieters müssen Sie das Anbieterpaket dem Projekt hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="71d50-521">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="71d50-522">Verwenden Sie <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> und <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> wie im folgenden Beispiel, um die Anbietereinstellungen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="71d50-522">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="71d50-523">Wenn Sie eine Bereitstellung in einer App Service-App durchführen, berücksichtigt die Anwendung die Einstellungen im Abschnitt [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) (App Service-Protokolle) auf der Seite **App Services** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="71d50-523">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="71d50-524">Bei einem Update der folgenden Einstellungen werden die Änderungen sofort wirksam, ohne dass ein Neustart oder eine erneute Bereitstellung der App notwendig ist.</span><span class="sxs-lookup"><span data-stu-id="71d50-524">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="71d50-525">**Anwendungsprotokoll (Dateisystem)**</span><span class="sxs-lookup"><span data-stu-id="71d50-525">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="71d50-526">**Anwendungsprotokoll (Blob)**</span><span class="sxs-lookup"><span data-stu-id="71d50-526">**Application Logging (Blob)**</span></span>

<span data-ttu-id="71d50-527">Der Standardspeicherort für Protokolldateien ist der Ordner *D:\\home\\LogFiles\\Application*, und der standardmäßige Dateiname lautet *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="71d50-527">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="71d50-528">Die Dateigröße ist standardmäßig auf 10 MB beschränkt, und die maximal zulässige Anzahl beibehaltener Dateien lautet 2.</span><span class="sxs-lookup"><span data-stu-id="71d50-528">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="71d50-529">Der Standardblobname lautet *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="71d50-529">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="71d50-530">Der Anbieter funktioniert nur, wenn das Projekt in der Azure-Umgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-530">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="71d50-531">Bei einer lokalen Ausführung zeigt er keine Auswirkungen. Der Anbieter schreibt keine Protokolle in lokale Dateien oder den lokalen Entwicklungsspeicher für BLOBs.</span><span class="sxs-lookup"><span data-stu-id="71d50-531">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="71d50-532">Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="71d50-532">Azure log streaming</span></span>

<span data-ttu-id="71d50-533">Azure-Protokollstreaming ermöglicht Ihnen eine Echtzeitanzeige der Protokollaktivität für:</span><span class="sxs-lookup"><span data-stu-id="71d50-533">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="71d50-534">App-Server</span><span class="sxs-lookup"><span data-stu-id="71d50-534">The app server</span></span>
* <span data-ttu-id="71d50-535">Webserver</span><span class="sxs-lookup"><span data-stu-id="71d50-535">The web server</span></span>
* <span data-ttu-id="71d50-536">Ablaufverfolgung für Anforderungsfehler</span><span class="sxs-lookup"><span data-stu-id="71d50-536">Failed request tracing</span></span>

<span data-ttu-id="71d50-537">So konfigurieren Sie das Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="71d50-537">To configure Azure log streaming:</span></span>

* <span data-ttu-id="71d50-538">Navigieren Sie von der Portalseite Ihrer App zur Seite **App Service-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="71d50-538">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="71d50-539">Legen Sie **Anwendungsprotokollierung (Dateisystem)** auf **Ein** fest.</span><span class="sxs-lookup"><span data-stu-id="71d50-539">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="71d50-540">Wählen Sie die **Protokollierungsebene**.</span><span class="sxs-lookup"><span data-stu-id="71d50-540">Choose the log **Level**.</span></span> <span data-ttu-id="71d50-541">Diese Einstellung gilt nur für das Azure-Protokollstreaming, nicht für andere Protokollierungsanbieter in der App.</span><span class="sxs-lookup"><span data-stu-id="71d50-541">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="71d50-542">Navigieren Sie zur Seite **Log Stream** (Protokollstream), um App-Meldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="71d50-542">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="71d50-543">Diese werden von der App über die `ILogger`-Schnittstelle protokolliert.</span><span class="sxs-lookup"><span data-stu-id="71d50-543">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="71d50-544">Ablaufverfolgungsprotokollierung für Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="71d50-544">Azure Application Insights trace logging</span></span>

<span data-ttu-id="71d50-545">Das [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)-Anbieterpaket schreibt Protokolle in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="71d50-545">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="71d50-546">Application Insights ist ein Dienst, der eine Web-App überwacht und Tools für Abfragen und Analysen von Telemetriedaten zur Verfügung stellt.</span><span class="sxs-lookup"><span data-stu-id="71d50-546">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="71d50-547">Wenn Sie diesen Anbieter verwenden, können Sie Ihre Protokolle mithilfe der Application Insights-Tools abfragen und analysieren.</span><span class="sxs-lookup"><span data-stu-id="71d50-547">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="71d50-548">Der Protokollierungsanbieter ist als Abhängigkeit von [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) enthalten, d.h. als das Paket, das alle verfügbaren Telemetriedaten für ASP.NET Core bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="71d50-548">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="71d50-549">Wenn Sie dieses Paket verwenden, ist nicht erforderlich, das Anbieterpaket zu installieren.</span><span class="sxs-lookup"><span data-stu-id="71d50-549">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="71d50-550">Verwenden Sie nicht das [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)-Paket, das für ASP.NET 4.x bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="71d50-550">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="71d50-551">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="71d50-551">For more information, see the following resources:</span></span>

* [<span data-ttu-id="71d50-552">Application Insights-Übersicht</span><span class="sxs-lookup"><span data-stu-id="71d50-552">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="71d50-553">[Anwendungseinblicke für ASP.NET Core-Anwendungen](/azure/azure-monitor/app/asp-net-core): Beginnen Sie hier, wenn Sie die gesamte Bandbreite der Application Insights-Telemetrie zusammen mit der Protokollierung implementieren möchten.</span><span class="sxs-lookup"><span data-stu-id="71d50-553">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="71d50-554">[ApplicationInsightsLoggerProvider für .NET Core ILogger-Protokolle](/azure/azure-monitor/app/ilogger): Beginnen Sie hier, wenn Sie den Protokollierungsanbieter ohne die übrige Application Insights-Telemetrie implementieren möchten.</span><span class="sxs-lookup"><span data-stu-id="71d50-554">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="71d50-555">[Application Insights logging adapters (Adapter zur Protokollierung für Application Insights)](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="71d50-555">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="71d50-556">[Installieren, Konfigurieren und Initialisieren des Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): interaktive Tutorials auf der Microsoft Learn-Website.</span><span class="sxs-lookup"><span data-stu-id="71d50-556">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="71d50-557">Protokollierungsanbieter von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="71d50-557">Third-party logging providers</span></span>

<span data-ttu-id="71d50-558">Protokollierungsframeworks von Drittanbietern aufgeführt, die mit ASP.NET Core funktionieren:</span><span class="sxs-lookup"><span data-stu-id="71d50-558">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="71d50-559">[elmah.io](https://elmah.io/) ([GitHub-Repository](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="71d50-559">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="71d50-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub-Repository](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="71d50-560">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="71d50-561">[JSNLog](https://jsnlog.com/) ([GitHub-Repository](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="71d50-561">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="71d50-562">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="71d50-562">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="71d50-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub-Repository](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="71d50-563">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="71d50-564">[Loggr](https://loggr.net/) ([GitHub-Repository](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="71d50-564">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="71d50-565">[NLog](https://nlog-project.org/) ([GitHub-Repository](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="71d50-565">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="71d50-566">[Sentry](https://sentry.io/welcome/) ([GitHub-Repository](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="71d50-566">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="71d50-567">[Serilog](https://serilog.net/) ([GitHub-Repository](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="71d50-567">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="71d50-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub-Repository](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="71d50-568">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="71d50-569">Einige Drittanbieterframeworks können eine [semantische Protokollierung (auch als strukturierte Protokollierung bezeichnet)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) ausführen.</span><span class="sxs-lookup"><span data-stu-id="71d50-569">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="71d50-570">Die Verwendung eines Frameworks von Drittanbietern ist ähnlich wie die Verwendung eines integrierten Anbieters:</span><span class="sxs-lookup"><span data-stu-id="71d50-570">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="71d50-571">Fügen Sie Ihrem Paket ein NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="71d50-571">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="71d50-572">Rufen Sie eine `ILoggerFactory`-Erweiterungsmethode auf, die vom Protokollierungsframework bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-572">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="71d50-573">Weitere Informationen finden Sie in der Dokumentation zum jeweiligen Anbieter.</span><span class="sxs-lookup"><span data-stu-id="71d50-573">For more information, see each provider's documentation.</span></span> <span data-ttu-id="71d50-574">Protokollierungsanbieter von Drittanbietern werden von Microsoft nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="71d50-574">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71d50-575">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="71d50-575">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71d50-576">Von [Tom Dykstra](https://github.com/tdykstra) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="71d50-576">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="71d50-577">.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="71d50-577">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="71d50-578">Dieser Artikel zeigt, wie Sie die Protokollierungs-API mit integrierten Anbietern verwenden können.</span><span class="sxs-lookup"><span data-stu-id="71d50-578">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="71d50-579">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="71d50-579">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="71d50-580">Hinzufügen von Anbietern</span><span class="sxs-lookup"><span data-stu-id="71d50-580">Add providers</span></span>

<span data-ttu-id="71d50-581">Ein Protokollierungsanbieter zeigt Protokolle an oder speichert diese.</span><span class="sxs-lookup"><span data-stu-id="71d50-581">A logging provider displays or stores logs.</span></span> <span data-ttu-id="71d50-582">Beispielsweise zeigt der Konsolenanbieter Protokolle in der Konsole an, und der Azure Application Insights-Anbieter speichert sie in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="71d50-582">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="71d50-583">Protokolle können durch das Hinzufügen mehrerer Anbieter an mehrere Ziele gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-583">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="71d50-584">Um einen Anbieter hinzuzufügen, rufen Sie die `Add{provider name}`-Erweiterungsmethode des Anbieters in *Program.cs* auf:</span><span class="sxs-lookup"><span data-stu-id="71d50-584">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="71d50-585">Der vorstehende Code erfordert Verweise auf `Microsoft.Extensions.Logging` und `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="71d50-585">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="71d50-586">Die Standardprojektvorlage ruft die Methode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> auf, die die folgenden Protokollierungsanbieter hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="71d50-586">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="71d50-587">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-587">Console</span></span>
* <span data-ttu-id="71d50-588">Debug</span><span class="sxs-lookup"><span data-stu-id="71d50-588">Debug</span></span>
* <span data-ttu-id="71d50-589">EventSource (beginnend mit ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="71d50-589">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="71d50-590">Bei Verwendung von `CreateDefaultBuilder` können Sie die Standardanbieter durch Ihre eigene Auswahl ersetzen.</span><span class="sxs-lookup"><span data-stu-id="71d50-590">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="71d50-591">Rufen Sie <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> auf, und fügen Sie die gewünschten Anbieter hinzu.</span><span class="sxs-lookup"><span data-stu-id="71d50-591">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="71d50-592">Informationen zu den [integrierten Protokollierungsanbietern](#built-in-logging-providers) sowie zu [Protokollierungsanbietern von Drittanbietern](#third-party-logging-providers) finden Sie weiter unten in diesem Artikel.</span><span class="sxs-lookup"><span data-stu-id="71d50-592">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="71d50-593">Erstellen von Protokollen</span><span class="sxs-lookup"><span data-stu-id="71d50-593">Create logs</span></span>

<span data-ttu-id="71d50-594">Verwenden Sie zum Erstellen von Protokollen ein <xref:Microsoft.Extensions.Logging.ILogger%601>-Objekt.</span><span class="sxs-lookup"><span data-stu-id="71d50-594">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="71d50-595">Rufen Sie in einer Web-App oder einem gehosteten Dienst einen `ILogger` aus der Abhängigkeitsinjektion ab.</span><span class="sxs-lookup"><span data-stu-id="71d50-595">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="71d50-596">Verwenden Sie in Konsolen-Apps ohne Host `LoggerFactory`, um einen `ILogger` zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="71d50-596">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="71d50-597">Im folgenden ASP.NET Core-Beispiel wird eine Protokollierung mit `TodoApiSample.Pages.AboutModel` als Kategorie erstellt.</span><span class="sxs-lookup"><span data-stu-id="71d50-597">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="71d50-598">Die Protokoll*kategorie* ist eine Zeichenfolge, die jedem Protokoll zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="71d50-598">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="71d50-599">Die von der Abhängigkeitsinjektion bereitgestellte `ILogger<T>`-Instanz erstellt Protokolle, die den vollqualifizierten Namen vom Typ `T` als Kategorie aufweisen.</span><span class="sxs-lookup"><span data-stu-id="71d50-599">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="71d50-600">In den folgenden Beispielen zu ASP.NET Core und einer Konsolen-App werden von der Protokollierung Protokolle mit dem Protokolliergrad `Information` erstellt.</span><span class="sxs-lookup"><span data-stu-id="71d50-600">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="71d50-601">Der Protokollierungs*grad* gibt den Schweregrad des protokollierten Ereignisses an.</span><span class="sxs-lookup"><span data-stu-id="71d50-601">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="71d50-602">[Grade](#log-level) und [Kategorien](#log-category) werden weiter unten in diesem Artikel ausführlicher erläutert.</span><span class="sxs-lookup"><span data-stu-id="71d50-602">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="71d50-603">Erstellen von Protokollen in der Startklasse</span><span class="sxs-lookup"><span data-stu-id="71d50-603">Create logs in Startup</span></span>

<span data-ttu-id="71d50-604">Zum Schreiben von Protokollen in der `Startup`-Klasse schließen Sie einen `ILogger`-Parameter in die Konstruktorsignatur ein:</span><span class="sxs-lookup"><span data-stu-id="71d50-604">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="71d50-605">Erstellen von Protokollen in der Program-Klasse</span><span class="sxs-lookup"><span data-stu-id="71d50-605">Create logs in the Program class</span></span>

<span data-ttu-id="71d50-606">Zum Schreiben von Protokollen in der `Program`-Klasse rufen Sie eine `ILogger`-Instanz aus DI ab:</span><span class="sxs-lookup"><span data-stu-id="71d50-606">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="71d50-607">Die Protokollierung während der Hosterstellung wird nicht direkt unterstützt.</span><span class="sxs-lookup"><span data-stu-id="71d50-607">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="71d50-608">Es kann jedoch eine separate Protokollierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-608">However, a separate logger can be used.</span></span> <span data-ttu-id="71d50-609">Im folgenden Beispiel wird eine [Serilog](https://serilog.net/)-Protokollierung zum Anmelden von `CreateWebHostBuilder` verwendet.</span><span class="sxs-lookup"><span data-stu-id="71d50-609">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="71d50-610">`AddSerilog` verwendet die in `Log.Logger` angegebene statische Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="71d50-610">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="71d50-611">Keine asynchronen Protokollierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="71d50-611">No asynchronous logger methods</span></span>

<span data-ttu-id="71d50-612">Die Protokollierung sollte so schnell erfolgen, dass sich die Leistungskosten von asynchronem Code nicht lohnen.</span><span class="sxs-lookup"><span data-stu-id="71d50-612">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="71d50-613">Wenn Ihr Protokollierungsdatenspeicher langsam ist, schreiben Sie nicht direkt in ihn.</span><span class="sxs-lookup"><span data-stu-id="71d50-613">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="71d50-614">Erwägen Sie, die Protokollnachrichten zunächst in einen schnellen Speicher zu schreiben und sie dann später in den langsamen Speicher zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="71d50-614">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="71d50-615">Wenn Sie beispielsweise in SQL Server protokollieren, möchten Sie dies nicht direkt in einer `Log`-Methode tun, da die `Log`-Methoden synchron sind.</span><span class="sxs-lookup"><span data-stu-id="71d50-615">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="71d50-616">Fügen Sie stattdessen die Protokollmeldungen synchron zu einer Warteschlange im Arbeitsspeicher hinzu, und rufen Sie mithilfe eines Hintergrundworkers die Nachrichten aus der Warteschlange ab, um sie dann asynchron per Pushvorgang an SQL Server zu übertragen.</span><span class="sxs-lookup"><span data-stu-id="71d50-616">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="71d50-617">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/11801).</span><span class="sxs-lookup"><span data-stu-id="71d50-617">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="71d50-618">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="71d50-618">Configuration</span></span>

<span data-ttu-id="71d50-619">Die Konfiguration von Protokollierungsanbietern erfolgt durch mindestens einen Konfigurationsanbieter:</span><span class="sxs-lookup"><span data-stu-id="71d50-619">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="71d50-620">Dateiformate (INI, JSON und XML)</span><span class="sxs-lookup"><span data-stu-id="71d50-620">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="71d50-621">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="71d50-621">Command-line arguments.</span></span>
* <span data-ttu-id="71d50-622">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="71d50-622">Environment variables.</span></span>
* <span data-ttu-id="71d50-623">Speicherinterne .NET-Objekte</span><span class="sxs-lookup"><span data-stu-id="71d50-623">In-memory .NET objects.</span></span>
* <span data-ttu-id="71d50-624">Der unverschlüsselte Speicher von [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="71d50-624">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="71d50-625">Ein unverschlüsselter Benutzerspeicher, z.B. [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="71d50-625">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="71d50-626">Benutzerdefinierte Anbieter (installiert oder erstellt).</span><span class="sxs-lookup"><span data-stu-id="71d50-626">Custom providers (installed or created).</span></span>

<span data-ttu-id="71d50-627">Die Konfiguration der Protokollierung wird meist vom `Logging`-Abschnitt von Anwendungseinstellungsdateien angegeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-627">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="71d50-628">Im folgenden Beispiel werden die Inhalte einer herkömmlichen *appsettings.Development.json*-Datei veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="71d50-628">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="71d50-629">Die `Logging`-Eigenschaft kann den `LogLevel` und Protokollanbietereigenschaften beinhalten (Konsole wird angezeigt).</span><span class="sxs-lookup"><span data-stu-id="71d50-629">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="71d50-630">Die `LogLevel`-Eigenschaft unter `Logging` gibt den Mindest[grad](#log-level) an, der für ausgewählte Kategorien protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="71d50-630">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="71d50-631">Im Beispiel protokollieren die Kategorien `System` und `Microsoft` mit dem Grad `Information` und alle anderen Kategorien mit dem Grad `Debug`.</span><span class="sxs-lookup"><span data-stu-id="71d50-631">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="71d50-632">Weitere Eigenschaften unter `Logging` geben Protokollierungsanbieter an.</span><span class="sxs-lookup"><span data-stu-id="71d50-632">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="71d50-633">Das Beispiel bezieht sich auf den Konsolenanbieter.</span><span class="sxs-lookup"><span data-stu-id="71d50-633">The example is for the Console provider.</span></span> <span data-ttu-id="71d50-634">Wenn ein Anbieter [Protokollbereiche](#log-scopes) unterstützt, gibt `IncludeScopes` an, ob sie aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="71d50-634">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="71d50-635">Eine Anbietereigenschaft (z.B. `Console` im Beispiel) kann auch eine `LogLevel`-Eigenschaft angeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-635">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="71d50-636">`LogLevel` unter einem Anbieter gibt die Grade an, die für diesen Anbieter protokolliert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="71d50-636">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="71d50-637">Wenn Grade in `Logging.{providername}.LogLevel` angegeben werden, überschreiben sie alle Angaben in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="71d50-637">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="71d50-638">Die Protokollierungs-API umfasst kein Szenario zum Ändern der Protokollebene, während eine App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-638">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="71d50-639">Einige Konfigurationsanbieter können jedoch die Konfiguration erneut laden, was sich unmittelbar auf die Protokollierungskonfiguration auswirkt.</span><span class="sxs-lookup"><span data-stu-id="71d50-639">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="71d50-640">Beispielsweise lädt der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider), der durch `CreateDefaultBuilder` zum Lesen von Einstellungsdateien hinzugefügt wird, die Protokollierungskonfiguration standardmäßig erneut.</span><span class="sxs-lookup"><span data-stu-id="71d50-640">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="71d50-641">Wenn die Konfiguration im Code geändert wird, während eine App ausgeführt wird, kann die App [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) aufrufen, um ihre Protokollierungskonfiguration zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="71d50-641">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="71d50-642">Informationen zur Implementierung von Konfigurationsanbieter finden Sie hier: <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="71d50-642">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="71d50-643">Beispiel einer Protokollierungsausgabe</span><span class="sxs-lookup"><span data-stu-id="71d50-643">Sample logging output</span></span>

<span data-ttu-id="71d50-644">Mit dem im vorherigen Abschnitt gezeigten Beispielcode werden Protokolle in der Konsole angezeigt, wenn die Anwendung über die Befehlszeile ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-644">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="71d50-645">Hier ein Beispiel für eine Konsolenausgabe:</span><span class="sxs-lookup"><span data-stu-id="71d50-645">Here's an example of console output:</span></span>

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

<span data-ttu-id="71d50-646">Die vorherigen Protokolle wurden generiert, indem eine HTTP Get-Anforderung an die Beispiel-App unter `http://localhost:5000/api/todo/0` vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="71d50-646">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="71d50-647">Nachfolgend sehen Sie, wie die gleichen Protokolle im Debugfenster angezeigt werden, wenn Sie die Beispiel-App in Visual Studio ausführen:</span><span class="sxs-lookup"><span data-stu-id="71d50-647">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="71d50-648">Die über die `ILogger`-Aufrufe aus dem vorherigen Abschnitt erstellten Protokolle beginnen mit „TodoApi“.</span><span class="sxs-lookup"><span data-stu-id="71d50-648">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="71d50-649">Protokolle, die mit Microsoft-Kategorien beginnen, stammen aus ASP.NET Core-Frameworkcode.</span><span class="sxs-lookup"><span data-stu-id="71d50-649">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="71d50-650">ASP.NET Core und Anwendungscode verwenden dieselbe Protokollierungs-API und dieselben Protokollierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="71d50-650">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="71d50-651">In den übrigen Abschnitten dieses Artikels werden einige Details und Optionen für die Protokollierung erläutert.</span><span class="sxs-lookup"><span data-stu-id="71d50-651">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="71d50-652">NuGet-Pakete</span><span class="sxs-lookup"><span data-stu-id="71d50-652">NuGet packages</span></span>

<span data-ttu-id="71d50-653">Die Schnittstellen `ILogger` und `ILoggerFactory` befinden sich in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), ihre Standardimplementierungen sind in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) enthalten.</span><span class="sxs-lookup"><span data-stu-id="71d50-653">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="71d50-654">Protokollkategorie</span><span class="sxs-lookup"><span data-stu-id="71d50-654">Log category</span></span>

<span data-ttu-id="71d50-655">Wenn ein `ILogger`-Objekt erstellt wird, wird eine *Kategorie* für dieses Objekt angegeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-655">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="71d50-656">Diese Kategorie ist in jeder Protokollmeldung enthalten, die von dieser Instanz von `ILogger` erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-656">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="71d50-657">Die Kategorie kann eine beliebige Zeichenfolge sein, aber die Konvention besteht in der Verwendung des Klassennamens, z.B. „TodoApi.Controllers.TodoController“.</span><span class="sxs-lookup"><span data-stu-id="71d50-657">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="71d50-658">Verwenden Sie `ILogger<T>` zum Abrufen einer `ILogger`-Instanz, die den vollqualifizierten Typnamen von `T` als Kategorie verwendet:</span><span class="sxs-lookup"><span data-stu-id="71d50-658">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="71d50-659">Um die Kategorie explizit anzugeben, rufen Sie `ILoggerFactory.CreateLogger` auf:</span><span class="sxs-lookup"><span data-stu-id="71d50-659">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="71d50-660">`ILogger<T>` entspricht dem Aufruf von `CreateLogger` mit dem vollqualifizierten Typnamen `T`.</span><span class="sxs-lookup"><span data-stu-id="71d50-660">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="71d50-661">Protokolliergrad</span><span class="sxs-lookup"><span data-stu-id="71d50-661">Log level</span></span>

<span data-ttu-id="71d50-662">Jedes Protokoll gibt einen <xref:Microsoft.Extensions.Logging.LogLevel>-Wert an.</span><span class="sxs-lookup"><span data-stu-id="71d50-662">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="71d50-663">Der Protokolliergrad gibt den Schweregrad oder die Wichtigkeit an.</span><span class="sxs-lookup"><span data-stu-id="71d50-663">The log level indicates the severity or importance.</span></span> <span data-ttu-id="71d50-664">Sie können beispielsweise ein `Information`-Protokoll schreiben, wenn eine Methode normal endet, und ein `Warning`-Protokoll, wenn eine Methode den Statuscode *404 Not Found* zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="71d50-664">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="71d50-665">Der folgende Code erstellt `Information`- und `Warning`-Protokolle:</span><span class="sxs-lookup"><span data-stu-id="71d50-665">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="71d50-666">Im Code oben ist der erste Parameter die [Protokollereignis-ID](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="71d50-666">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="71d50-667">Der zweite Parameter ist eine Meldungsvorlage mit Platzhaltern für Argumentwerte, die von den verbleibenden Methodenparametern bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-667">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="71d50-668">Die Methodenparameter werden im [Abschnitt „Meldungsvorlage“](#log-message-template) später in diesem Artikel erläutert.</span><span class="sxs-lookup"><span data-stu-id="71d50-668">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="71d50-669">Protokollmethoden, die den Protokolliergrad im Methodennamen enthalten (z.B. `LogInformation` und `LogWarning`), sind [Erweiterungsmethoden für ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="71d50-669">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="71d50-670">Diese Methoden rufen eine `Log`-Methode auf, die einen `LogLevel`-Parameter annimmt.</span><span class="sxs-lookup"><span data-stu-id="71d50-670">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="71d50-671">Sie können anstelle eines Aufrufs dieser Erweiterungsmethoden die `Log`-Methode direkt aufrufen, aber die Syntax ist relativ kompliziert.</span><span class="sxs-lookup"><span data-stu-id="71d50-671">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="71d50-672">Weitere Informationen finden Sie unter <xref:Microsoft.Extensions.Logging.ILogger> und im [Quellcode für Protokollierungserweiterungen](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="71d50-672">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="71d50-673">ASP.NET Core definiert die folgenden Protokolliergrade. Die Reihenfolge reicht vom geringsten bis zum höchsten Schweregrad.</span><span class="sxs-lookup"><span data-stu-id="71d50-673">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="71d50-674">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="71d50-674">Trace = 0</span></span>

  <span data-ttu-id="71d50-675">Protokolliert Informationen, die in der Regel nur für das Debuggen nützlich sind.</span><span class="sxs-lookup"><span data-stu-id="71d50-675">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="71d50-676">Diese Meldungen können sensible Anwendungsdaten enthalten und sollten daher nicht in einer Produktionsumgebung aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-676">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="71d50-677">*Standardmäßig deaktiviert.*</span><span class="sxs-lookup"><span data-stu-id="71d50-677">*Disabled by default.*</span></span>

* <span data-ttu-id="71d50-678">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="71d50-678">Debug = 1</span></span>

  <span data-ttu-id="71d50-679">Protokolliert Informationen, die bei der Entwicklung und beim Debuggen hilfreich sein können.</span><span class="sxs-lookup"><span data-stu-id="71d50-679">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="71d50-680">Beispiel: `Entering method Configure with flag set to true.` Aktivieren Sie Protokolle mit dem Protokolliergrad `Debug` aufgrund des hohen Protokollaufkommens nur zur Problembehandlung in der Produktion.</span><span class="sxs-lookup"><span data-stu-id="71d50-680">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="71d50-681">Information = 2</span><span class="sxs-lookup"><span data-stu-id="71d50-681">Information = 2</span></span>

  <span data-ttu-id="71d50-682">Zur Nachverfolgung des allgemeinen Ablaufs der App.</span><span class="sxs-lookup"><span data-stu-id="71d50-682">For tracking the general flow of the app.</span></span> <span data-ttu-id="71d50-683">Diese Protokolle haben typischerweise einen langfristigen Nutzen.</span><span class="sxs-lookup"><span data-stu-id="71d50-683">These logs typically have some long-term value.</span></span> <span data-ttu-id="71d50-684">Ein Beispiel: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="71d50-684">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="71d50-685">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="71d50-685">Warning = 3</span></span>

  <span data-ttu-id="71d50-686">Für ungewöhnliche oder unerwartete Ereignisse im App-Verlauf.</span><span class="sxs-lookup"><span data-stu-id="71d50-686">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="71d50-687">Dazu können Fehler oder andere Bedingungen gehören, die zwar nicht zum Beenden der App führen, aber eventuell untersucht werden müssen.</span><span class="sxs-lookup"><span data-stu-id="71d50-687">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="71d50-688">Behandelte Ausnahmen sind eine typische Verwendung für den Protokolliergrad `Warning`.</span><span class="sxs-lookup"><span data-stu-id="71d50-688">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="71d50-689">Ein Beispiel: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="71d50-689">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="71d50-690">Error = 4</span><span class="sxs-lookup"><span data-stu-id="71d50-690">Error = 4</span></span>

  <span data-ttu-id="71d50-691">Für Fehler und Ausnahmen, die nicht behandelt werden können.</span><span class="sxs-lookup"><span data-stu-id="71d50-691">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="71d50-692">Diese Meldungen weisen auf einen Fehler in der aktuellen Aktivität oder im aktuellen Vorgang (z.B. in der aktuellen HTTP-Anforderung) und nicht auf einen anwendungsweiten Fehler hin.</span><span class="sxs-lookup"><span data-stu-id="71d50-692">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="71d50-693">Beispielprotokollmeldung: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="71d50-693">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="71d50-694">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="71d50-694">Critical = 5</span></span>

  <span data-ttu-id="71d50-695">Für Fehler, die sofortige Aufmerksamkeit erfordern.</span><span class="sxs-lookup"><span data-stu-id="71d50-695">For failures that require immediate attention.</span></span> <span data-ttu-id="71d50-696">Beispiel: Szenarios mit Datenverlust, Speichermangel.</span><span class="sxs-lookup"><span data-stu-id="71d50-696">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="71d50-697">Verwenden Sie den Protokolliergrad, um die Menge an Protokollausgabedaten zu steuern, die in ein bestimmtes Speichermedium geschrieben oder an ein Anzeigefenster ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-697">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="71d50-698">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="71d50-698">For example:</span></span>

* <span data-ttu-id="71d50-699">In einer Produktionsumgebung</span><span class="sxs-lookup"><span data-stu-id="71d50-699">In production:</span></span>
  * <span data-ttu-id="71d50-700">Das Protokollieren von `Trace` über die `Information`-Ebenen verursacht viele detaillierte Protokollmeldungen.</span><span class="sxs-lookup"><span data-stu-id="71d50-700">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="71d50-701">Protokollieren Sie `Trace` über Nachrichten auf `Information`-Ebene in einem kostengünstigen Speicher mit hohem Volumen, um die Kosten zu überwachen und die Grenzwerte des Datenspeichers nicht zu überschreiten.</span><span class="sxs-lookup"><span data-stu-id="71d50-701">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="71d50-702">Das Protokollieren von `Warning` über die `Critical`-Ebenen verursacht weniger und kürzere Protokollmeldungen.</span><span class="sxs-lookup"><span data-stu-id="71d50-702">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="71d50-703">Daher müssen Sie sich über Kosten- und Speichergrenzwerte keine Sorgen machen und sind bei der Auswahl des Datenspeichers flexibler.</span><span class="sxs-lookup"><span data-stu-id="71d50-703">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="71d50-704">Entwicklungsphase:</span><span class="sxs-lookup"><span data-stu-id="71d50-704">During development:</span></span>
  * <span data-ttu-id="71d50-705">Protokollieren Sie `Warning` über `Critical`-Meldungen an der Konsole.</span><span class="sxs-lookup"><span data-stu-id="71d50-705">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="71d50-706">Fügen Sie bei der Fehlerbehebung `Trace` über `Information`-Meldungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="71d50-706">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="71d50-707">Im Abschnitt [Protokollfilterung](#log-filtering) weiter unten in diesem Artikel wird erläutert, wie Sie steuern, welche Protokolliergrade ein Anbieter verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="71d50-707">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="71d50-708">ASP.NET Core schreibt Protokolle für Frameworkereignisse.</span><span class="sxs-lookup"><span data-stu-id="71d50-708">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="71d50-709">Die zuvor in diesem Artikel gezeigten Protokollbeispiele enthielten Protokolle unterhalb des Protokolliergrads `Information`, deshalb wurden keine Protokolle des Protokolliergrads `Debug` oder `Trace` erstellt.</span><span class="sxs-lookup"><span data-stu-id="71d50-709">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="71d50-710">Hier ist ein Beispiel für Konsolenprotokolle, die durch Ausführen der Beispiel-App generiert werden, die so konfiguriert ist, dass sie `Debug`-Protokolle anzeigt:</span><span class="sxs-lookup"><span data-stu-id="71d50-710">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="71d50-711">Protokollereignis-ID</span><span class="sxs-lookup"><span data-stu-id="71d50-711">Log event ID</span></span>

<span data-ttu-id="71d50-712">Jedes Protokoll kann eine *Ereignis-ID* angeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-712">Each log can specify an *event ID*.</span></span> <span data-ttu-id="71d50-713">Die Beispiel-App verwendet hierzu eine lokal definierte `LoggingEvents`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="71d50-713">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="71d50-714">Eine Ereignis-ID ordnet eine Gruppe von Ereignissen zu.</span><span class="sxs-lookup"><span data-stu-id="71d50-714">An event ID associates a set of events.</span></span> <span data-ttu-id="71d50-715">Beispielsweise können alle Protokolle, die sich auf die Anzeige einer Liste von Elementen auf einer Seite beziehen, 1001 sein.</span><span class="sxs-lookup"><span data-stu-id="71d50-715">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="71d50-716">Der Protokollierungsanbieter kann die Ereignis-ID in einem ID-Feld, in der Protokollierungsmeldung oder gar nicht speichern.</span><span class="sxs-lookup"><span data-stu-id="71d50-716">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="71d50-717">Der Debuganbieter zeigt keine Ereignis-IDs an.</span><span class="sxs-lookup"><span data-stu-id="71d50-717">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="71d50-718">Der Konsolenanbieter zeigt Ereignis-IDs in Klammern hinter der Kategorie an:</span><span class="sxs-lookup"><span data-stu-id="71d50-718">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="71d50-719">Protokollmeldungsvorlage</span><span class="sxs-lookup"><span data-stu-id="71d50-719">Log message template</span></span>

<span data-ttu-id="71d50-720">Jedes Protokoll gibt eine Meldungsvorlage an.</span><span class="sxs-lookup"><span data-stu-id="71d50-720">Each log specifies a message template.</span></span> <span data-ttu-id="71d50-721">Die Meldungsvorlage kann Platzhalter enthalten, für die Argumente bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-721">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="71d50-722">Verwenden Sie Namen für die Platzhalter, keine Zahlen.</span><span class="sxs-lookup"><span data-stu-id="71d50-722">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="71d50-723">Die Reihenfolge der Platzhalter – nicht ihre Namen – bestimmt, welche Parameter verwendet werden, um ihre Werte zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="71d50-723">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="71d50-724">Beachten Sie im folgenden Code, dass die Parameternamen in der Meldungsvorlage nicht in der richtigen Reihenfolge vorhanden sind:</span><span class="sxs-lookup"><span data-stu-id="71d50-724">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="71d50-725">Dieser Code erstellt eine Protokollierungsmeldung mit den Parameterwerten in der richtigen Reihenfolge:</span><span class="sxs-lookup"><span data-stu-id="71d50-725">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="71d50-726">Das Protokollierungsframework funktioniert auf diese Weise, damit Protokollierungsanbieter [semantische Protokollierung, die auch als strukturierte Protokollierung bezeichnet wird](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging), implementieren können.</span><span class="sxs-lookup"><span data-stu-id="71d50-726">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="71d50-727">Die Argumente selbst (nicht nur die formatierte Meldungsvorlage) werden an das Protokollierungssystem übergeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-727">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="71d50-728">Diese Informationen ermöglichen es Protokollierungsanbietern, die Parameterwerte als Felder zu speichern.</span><span class="sxs-lookup"><span data-stu-id="71d50-728">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="71d50-729">Nehmen wir zum Beispiel an, dass Aufrufe von Protokollierungsmethoden folgendermaßen aussehen:</span><span class="sxs-lookup"><span data-stu-id="71d50-729">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="71d50-730">Wenn Sie die Protokolle an Azure Table Storage senden, kann jede Azure Table-Entität die Eigenschaften `ID` und `RequestTime` aufweisen. Dies vereinfacht die Abfrage von Protokolldaten.</span><span class="sxs-lookup"><span data-stu-id="71d50-730">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="71d50-731">Eine Abfrage kann alle Protokolle in einem bestimmten `RequestTime`-Bereich finden, ohne die Uhrzeit aus den Textmeldungen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="71d50-731">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="71d50-732">Protokollieren von Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="71d50-732">Logging exceptions</span></span>

<span data-ttu-id="71d50-733">Die Protokollierungsmethoden umfassen Überladungen, die das Übergeben von Ausnahmen ermöglichen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="71d50-733">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="71d50-734">Die verschiedenen Anbieter verarbeiten die Ausnahmeinformationen auf unterschiedliche Weise.</span><span class="sxs-lookup"><span data-stu-id="71d50-734">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="71d50-735">Hier sehen Sie ein Beispiel einer Debuganbieterausgabe aus dem obigen Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="71d50-735">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="71d50-736">Protokollfilterung</span><span class="sxs-lookup"><span data-stu-id="71d50-736">Log filtering</span></span>

<span data-ttu-id="71d50-737">Sie können einen Mindestprotokolliergrad für einen bestimmten Anbieter und eine bestimmte Kategorie oder für alle Anbieter oder alle Kategorien festlegen.</span><span class="sxs-lookup"><span data-stu-id="71d50-737">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="71d50-738">Alle Protokolle unter dem Mindestgrad werden nicht an diesen Anbieter weitergeleitet, sodass sie nicht angezeigt oder gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-738">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="71d50-739">Wenn Sie alle Protokolle unterdrücken möchten, geben Sie `LogLevel.None` als Mindestprotokolliergrad an.</span><span class="sxs-lookup"><span data-stu-id="71d50-739">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="71d50-740">Der ganzzahlige Wert von `LogLevel.None` lautet 6 und liegt über `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="71d50-740">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="71d50-741">Erstellen von Filterregeln in der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="71d50-741">Create filter rules in configuration</span></span>

<span data-ttu-id="71d50-742">Der Projektvorlagencode ruft `CreateDefaultBuilder` auf, um die Protokollierung für die Konsolen-, Debug- und EventSource-Anbieter (ASP.NET Core 2.2 oder höher) einzurichten.</span><span class="sxs-lookup"><span data-stu-id="71d50-742">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="71d50-743">Die `CreateDefaultBuilder`-Methode richtet die Protokollierung ein, um nach der Konfiguration in einem `Logging`-Abschnitt zu suchen; dies wird [weiter oben in diesem Artikel](#configuration) erläutert.</span><span class="sxs-lookup"><span data-stu-id="71d50-743">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="71d50-744">Die Konfigurationsdaten geben die Mindestprotokolliergrade nach Anbieter und Kategorie an, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="71d50-744">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="71d50-745">Diese JSON-Konfiguration erstellt sechs Filterregeln: eine für den Debuganbieter, vier für den Konsolenanbieter und eine für alle Anbieter.</span><span class="sxs-lookup"><span data-stu-id="71d50-745">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="71d50-746">Eine einzelne Regel wird für jeden Anbieter ausgewählt, wenn ein `ILogger`-Objekt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-746">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="71d50-747">Filterregeln im Code</span><span class="sxs-lookup"><span data-stu-id="71d50-747">Filter rules in code</span></span>

<span data-ttu-id="71d50-748">Das folgende Beispiel zeigt, wie Filterregeln im Code registriert werden:</span><span class="sxs-lookup"><span data-stu-id="71d50-748">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="71d50-749">Das zweite `AddFilter` gibt den Debuganbieter durch Verwendung des zugehörigen Typnamens an.</span><span class="sxs-lookup"><span data-stu-id="71d50-749">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="71d50-750">Das erste `AddFilter` gilt für alle Anbieter, weil kein Anbietertyp angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-750">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="71d50-751">Anwendung von Filterregeln</span><span class="sxs-lookup"><span data-stu-id="71d50-751">How filtering rules are applied</span></span>

<span data-ttu-id="71d50-752">Die Konfigurationsdaten und der in den vorangegangenen Beispielen gezeigte `AddFilter`-Code erzeugen die in der folgenden Tabelle gezeigten Regeln.</span><span class="sxs-lookup"><span data-stu-id="71d50-752">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="71d50-753">Die ersten sechs Regeln stammen aus dem Konfigurationsbeispiel, die letzten zwei Filter stammen aus dem Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="71d50-753">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="71d50-754">Anzahl</span><span class="sxs-lookup"><span data-stu-id="71d50-754">Number</span></span> | <span data-ttu-id="71d50-755">Anbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-755">Provider</span></span>      | <span data-ttu-id="71d50-756">Kategorien beginnend mit...</span><span class="sxs-lookup"><span data-stu-id="71d50-756">Categories that begin with ...</span></span>          | <span data-ttu-id="71d50-757">Mindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="71d50-757">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="71d50-758">1</span><span class="sxs-lookup"><span data-stu-id="71d50-758">1</span></span>      | <span data-ttu-id="71d50-759">Debug</span><span class="sxs-lookup"><span data-stu-id="71d50-759">Debug</span></span>         | <span data-ttu-id="71d50-760">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="71d50-760">All categories</span></span>                          | <span data-ttu-id="71d50-761">Information</span><span class="sxs-lookup"><span data-stu-id="71d50-761">Information</span></span>       |
| <span data-ttu-id="71d50-762">2</span><span class="sxs-lookup"><span data-stu-id="71d50-762">2</span></span>      | <span data-ttu-id="71d50-763">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-763">Console</span></span>       | <span data-ttu-id="71d50-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="71d50-764">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="71d50-765">Warnung</span><span class="sxs-lookup"><span data-stu-id="71d50-765">Warning</span></span>           |
| <span data-ttu-id="71d50-766">3</span><span class="sxs-lookup"><span data-stu-id="71d50-766">3</span></span>      | <span data-ttu-id="71d50-767">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-767">Console</span></span>       | <span data-ttu-id="71d50-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="71d50-768">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="71d50-769">Debug</span><span class="sxs-lookup"><span data-stu-id="71d50-769">Debug</span></span>             |
| <span data-ttu-id="71d50-770">4</span><span class="sxs-lookup"><span data-stu-id="71d50-770">4</span></span>      | <span data-ttu-id="71d50-771">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-771">Console</span></span>       | <span data-ttu-id="71d50-772">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="71d50-772">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="71d50-773">Fehler</span><span class="sxs-lookup"><span data-stu-id="71d50-773">Error</span></span>             |
| <span data-ttu-id="71d50-774">5</span><span class="sxs-lookup"><span data-stu-id="71d50-774">5</span></span>      | <span data-ttu-id="71d50-775">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-775">Console</span></span>       | <span data-ttu-id="71d50-776">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="71d50-776">All categories</span></span>                          | <span data-ttu-id="71d50-777">Information</span><span class="sxs-lookup"><span data-stu-id="71d50-777">Information</span></span>       |
| <span data-ttu-id="71d50-778">6</span><span class="sxs-lookup"><span data-stu-id="71d50-778">6</span></span>      | <span data-ttu-id="71d50-779">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-779">All providers</span></span> | <span data-ttu-id="71d50-780">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="71d50-780">All categories</span></span>                          | <span data-ttu-id="71d50-781">Debug</span><span class="sxs-lookup"><span data-stu-id="71d50-781">Debug</span></span>             |
| <span data-ttu-id="71d50-782">7</span><span class="sxs-lookup"><span data-stu-id="71d50-782">7</span></span>      | <span data-ttu-id="71d50-783">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-783">All providers</span></span> | <span data-ttu-id="71d50-784">System</span><span class="sxs-lookup"><span data-stu-id="71d50-784">System</span></span>                                  | <span data-ttu-id="71d50-785">Debug</span><span class="sxs-lookup"><span data-stu-id="71d50-785">Debug</span></span>             |
| <span data-ttu-id="71d50-786">8</span><span class="sxs-lookup"><span data-stu-id="71d50-786">8</span></span>      | <span data-ttu-id="71d50-787">Debug</span><span class="sxs-lookup"><span data-stu-id="71d50-787">Debug</span></span>         | <span data-ttu-id="71d50-788">Microsoft</span><span class="sxs-lookup"><span data-stu-id="71d50-788">Microsoft</span></span>                               | <span data-ttu-id="71d50-789">Ablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="71d50-789">Trace</span></span>             |

<span data-ttu-id="71d50-790">Wenn ein `ILogger`-Objekt erstellt wird, wählt das `ILoggerFactory`-Objekt eine einzige Regel pro Anbieter aus, die auf diese Protokollierung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-790">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="71d50-791">Alle von einer `ILogger`-Instanz geschriebenen Meldungen werden auf Grundlage der ausgewählten Regeln gefiltert.</span><span class="sxs-lookup"><span data-stu-id="71d50-791">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="71d50-792">Aus den verfügbaren Regeln wird die für jeden Anbieter und jedes Kategoriepaar spezifischste Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="71d50-792">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="71d50-793">Beim Erstellen von `ILogger` für eine vorgegebene Kategorie wird für jeden Anbieter der folgende Algorithmus verwendet:</span><span class="sxs-lookup"><span data-stu-id="71d50-793">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="71d50-794">Es werden alle Regeln ausgewählt, die dem Anbieter oder dem zugehörigen Alias entsprechen.</span><span class="sxs-lookup"><span data-stu-id="71d50-794">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="71d50-795">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln mit einem leeren Anbieter ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="71d50-795">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="71d50-796">Aus dem Ergebnis des vorherigen Schritts werden die Regeln mit dem längsten übereinstimmenden Kategoriepräfix ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="71d50-796">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="71d50-797">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln ohne Angabe einer Kategorie ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="71d50-797">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="71d50-798">Wenn mehrere Regeln ausgewählt sind, wird die **letzte** Regel verwendet.</span><span class="sxs-lookup"><span data-stu-id="71d50-798">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="71d50-799">Wenn keine Regel ausgewählt ist, wird `MinimumLevel` verwendet.</span><span class="sxs-lookup"><span data-stu-id="71d50-799">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="71d50-800">Angenommen, Sie erstellen mit der oben aufgeführten Liste mit Regeln ein `ILogger`-Objekt für die Kategorie „Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine“:</span><span class="sxs-lookup"><span data-stu-id="71d50-800">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="71d50-801">Für den Debuganbieter gelten die Regeln 1, 6 und 8.</span><span class="sxs-lookup"><span data-stu-id="71d50-801">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="71d50-802">Regel 8 ist die spezifischste Regel, deshalb wird diese Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="71d50-802">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="71d50-803">Für den Konsolenanbieter gelten die Regeln 3, 4, 5 und 6.</span><span class="sxs-lookup"><span data-stu-id="71d50-803">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="71d50-804">Regel 3 ist die spezifischste Regel.</span><span class="sxs-lookup"><span data-stu-id="71d50-804">Rule 3 is most specific.</span></span>

<span data-ttu-id="71d50-805">Die sich ergebende `ILogger`-Instanz sendet Protokolle mit dem Protokolliergrad `Trace` und höher an den Debuganbieter.</span><span class="sxs-lookup"><span data-stu-id="71d50-805">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="71d50-806">Protokolle mit dem Protokolliergrad `Debug` und höher werden an den Konsolenanbieter gesendet.</span><span class="sxs-lookup"><span data-stu-id="71d50-806">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="71d50-807">Anbieteraliase</span><span class="sxs-lookup"><span data-stu-id="71d50-807">Provider aliases</span></span>

<span data-ttu-id="71d50-808">Jeder Anbieter definiert einen *Alias*, der in der Konfiguration anstelle des vollqualifizierten Typnamens verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="71d50-808">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="71d50-809">Verwenden Sie für die integrierten Anbieter die folgenden Aliase:</span><span class="sxs-lookup"><span data-stu-id="71d50-809">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="71d50-810">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-810">Console</span></span>
* <span data-ttu-id="71d50-811">Debug</span><span class="sxs-lookup"><span data-stu-id="71d50-811">Debug</span></span>
* <span data-ttu-id="71d50-812">EventSource</span><span class="sxs-lookup"><span data-stu-id="71d50-812">EventSource</span></span>
* <span data-ttu-id="71d50-813">EventLog</span><span class="sxs-lookup"><span data-stu-id="71d50-813">EventLog</span></span>
* <span data-ttu-id="71d50-814">TraceSource</span><span class="sxs-lookup"><span data-stu-id="71d50-814">TraceSource</span></span>
* <span data-ttu-id="71d50-815">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="71d50-815">AzureAppServicesFile</span></span>
* <span data-ttu-id="71d50-816">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="71d50-816">AzureAppServicesBlob</span></span>
* <span data-ttu-id="71d50-817">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="71d50-817">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="71d50-818">Standardmindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="71d50-818">Default minimum level</span></span>

<span data-ttu-id="71d50-819">Es gibt eine Einstellung für den Mindestprotokolliergrad, die nur dann wirksam wird, wenn für einen bestimmten Anbieter und eine bestimmte Kategorie keine Regeln aus Konfiguration oder Code gelten.</span><span class="sxs-lookup"><span data-stu-id="71d50-819">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="71d50-820">Im folgenden Beispiel wird das Festlegen des Mindestprotokolliergrads veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="71d50-820">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="71d50-821">Wenn Sie den Mindestprotokolliergrad nicht explizit festlegen, lautet der Standardwert `Information`, d.h. Protokolle der Ebene `Trace` und `Debug` werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="71d50-821">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="71d50-822">Filterfunktionen</span><span class="sxs-lookup"><span data-stu-id="71d50-822">Filter functions</span></span>

<span data-ttu-id="71d50-823">Eine Filterfunktion wird für alle Anbieter und Kategorien aufgerufen, denen keine Regeln durch Konfiguration oder Code zugewiesen sind.</span><span class="sxs-lookup"><span data-stu-id="71d50-823">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="71d50-824">Code in der Funktion verfügt über Zugriff auf den Anbietertyp, die Kategorie und den Protokolliergrad.</span><span class="sxs-lookup"><span data-stu-id="71d50-824">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="71d50-825">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="71d50-825">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="71d50-826">Systemkategorien und -protokolliergrade</span><span class="sxs-lookup"><span data-stu-id="71d50-826">System categories and levels</span></span>

<span data-ttu-id="71d50-827">Dies sind einige Kategorien, die vom ASP.NET Core und Entity Framework Core verwendet werden, mit Hinweisen darauf, welche Protokolle von ihnen zu erwarten sind:</span><span class="sxs-lookup"><span data-stu-id="71d50-827">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="71d50-828">Kategorie</span><span class="sxs-lookup"><span data-stu-id="71d50-828">Category</span></span>                            | <span data-ttu-id="71d50-829">Hinweise</span><span class="sxs-lookup"><span data-stu-id="71d50-829">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="71d50-830">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="71d50-830">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="71d50-831">Allgemeine ASP.NET Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="71d50-831">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="71d50-832">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="71d50-832">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="71d50-833">Gibt an, welche Schlüssel in Betracht gezogen, gefunden und verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="71d50-833">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="71d50-834">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="71d50-834">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="71d50-835">Hosts sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="71d50-835">Hosts allowed.</span></span> |
| <span data-ttu-id="71d50-836">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="71d50-836">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="71d50-837">Gibt an, wie lange es bis zum Abschluss von HTTP-Anforderungen gedauert hat und zu welcher Zeit sie gestartet wurden.</span><span class="sxs-lookup"><span data-stu-id="71d50-837">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="71d50-838">Gibt an, welche Hostingstartassemblys geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="71d50-838">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="71d50-839">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="71d50-839">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="71d50-840">MVC- und Razor-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="71d50-840">MVC and Razor diagnostics.</span></span> <span data-ttu-id="71d50-841">Modellbindung, Filterausführung, Ansichtskompilierung, Aktionsauswahl.</span><span class="sxs-lookup"><span data-stu-id="71d50-841">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="71d50-842">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="71d50-842">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="71d50-843">Gibt Routenabgleichsinformationen an.</span><span class="sxs-lookup"><span data-stu-id="71d50-843">Route matching information.</span></span> |
| <span data-ttu-id="71d50-844">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="71d50-844">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="71d50-845">Start-, Beendigungs- und Keep-Alive-Antworten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="71d50-845">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="71d50-846">HTTPS-Zertifikatinformationen.</span><span class="sxs-lookup"><span data-stu-id="71d50-846">HTTPS certificate information.</span></span> |
| <span data-ttu-id="71d50-847">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="71d50-847">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="71d50-848">Die bereitgestellten Dateien.</span><span class="sxs-lookup"><span data-stu-id="71d50-848">Files served.</span></span> |
| <span data-ttu-id="71d50-849">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="71d50-849">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="71d50-850">Allgemeine Entity Framework Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="71d50-850">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="71d50-851">Datenbankaktivität und -konfiguration, Änderungserkennung, Migrationen.</span><span class="sxs-lookup"><span data-stu-id="71d50-851">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="71d50-852">Protokollbereiche</span><span class="sxs-lookup"><span data-stu-id="71d50-852">Log scopes</span></span>

 <span data-ttu-id="71d50-853">Ein *Bereich* kann einen Satz logischer Vorgänge gruppieren.</span><span class="sxs-lookup"><span data-stu-id="71d50-853">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="71d50-854">Diese Gruppierung kann verwendet werden, um an jedes Protokoll, das als Teil einer Gruppe erstellt wird, die gleichen Daten anzufügen.</span><span class="sxs-lookup"><span data-stu-id="71d50-854">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="71d50-855">So kann beispielsweise jedes Protokoll, das im Rahmen der Verarbeitung einer Transaktion erstellt wird, die Transaktions-ID enthalten.</span><span class="sxs-lookup"><span data-stu-id="71d50-855">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="71d50-856">Ein Bereich ist ein `IDisposable`-Typ, der von der <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>-Methode zurückgegeben und so lange beibehalten wird, bis er verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-856">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="71d50-857">Verwenden Sie einen Bereich, indem Sie Protokollierungsaufrufe mit einem `using`-Block umschließen, der als Wrapper verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="71d50-857">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="71d50-858">Der folgende Code aktiviert Bereiche für den Konsolenanbieter:</span><span class="sxs-lookup"><span data-stu-id="71d50-858">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="71d50-859">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="71d50-859">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="71d50-860">Um die bereichsbasierte Protokollierung zu aktivieren, muss die Konsolenprotokollierungsoption `IncludeScopes` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-860">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="71d50-861">Weitere Informationen zur Konfiguration finden Sie im Abschnitt [Konfiguration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="71d50-861">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="71d50-862">Jede Protokollmeldung enthält die bereichsbezogenen Informationen:</span><span class="sxs-lookup"><span data-stu-id="71d50-862">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="71d50-863">Integrierte Protokollierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-863">Built-in logging providers</span></span>

<span data-ttu-id="71d50-864">ASP.NET Core wird mit den folgenden Anbietern bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="71d50-864">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="71d50-865">Konsole</span><span class="sxs-lookup"><span data-stu-id="71d50-865">Console</span></span>](#console-provider)
* [<span data-ttu-id="71d50-866">Debuggen</span><span class="sxs-lookup"><span data-stu-id="71d50-866">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="71d50-867">EventSource</span><span class="sxs-lookup"><span data-stu-id="71d50-867">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="71d50-868">EventLog</span><span class="sxs-lookup"><span data-stu-id="71d50-868">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="71d50-869">TraceSource</span><span class="sxs-lookup"><span data-stu-id="71d50-869">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="71d50-870">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="71d50-870">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="71d50-871">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="71d50-871">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="71d50-872">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="71d50-872">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="71d50-873">Weitere Informationen zu „stdout“ und zur Debugprotokollierung mit dem ASP.NET Core-Modul finden Sie unter <xref:test/troubleshoot-azure-iis> und <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="71d50-873">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="71d50-874">Der Konsolenanbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-874">Console provider</span></span>

<span data-ttu-id="71d50-875">Das Anbieterpaket [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sendet eine Protokollausgabe an die Konsole.</span><span class="sxs-lookup"><span data-stu-id="71d50-875">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="71d50-876">Um die Ausgabe der Konsolenprotokollierung anzuzeigen, öffnen Sie eine Eingabeaufforderung im Projektordner und führen den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="71d50-876">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="71d50-877">Der Debuganbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-877">Debug provider</span></span>

<span data-ttu-id="71d50-878">Beim Anbieterpaket [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) erfolgt die Protokollausgabe unter Verwendung der Klasse [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine`-Methodenaufrufe).</span><span class="sxs-lookup"><span data-stu-id="71d50-878">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="71d50-879">Unter Linux werden Protokolle dieses Anbieters in */var/log/message* geschrieben.</span><span class="sxs-lookup"><span data-stu-id="71d50-879">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="71d50-880">Ereignisquellenanbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-880">Event Source provider</span></span>

<span data-ttu-id="71d50-881">Das [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource)-Anbieterpaket schreibt in eine plattformübergreifende Ereignisquelle mit dem Namen `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="71d50-881">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="71d50-882">Unter Windows verwendet der Anbieter die [Ereignisablaufverfolgung für Windows (ETW)](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="71d50-882">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="71d50-883">Der Ereignisquellenanbieter wird automatisch hinzugefügt, wenn `CreateDefaultBuilder` zum Erstellen des Hosts aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-883">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="71d50-884">Verwenden Sie das [PerfView-Hilfsprogramm](https://github.com/Microsoft/perfview) zum Sammeln und Anzeigen von Protokollen.</span><span class="sxs-lookup"><span data-stu-id="71d50-884">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="71d50-885">Es gibt andere Tools zur Anzeige von ETW-Protokollen, aber PerfView bietet die besten Ergebnisse bei der Arbeit mit ETW-Ereignissen, die von ASP.NET Core ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-885">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="71d50-886">Um PerfView für das Erfassen von Ereignissen zu konfigurieren, die von diesem Anbieter protokolliert wurden, fügen Sie die Zeichenfolge `*Microsoft-Extensions-Logging` zur Liste **Zusätzliche Anbieter** hinzu.</span><span class="sxs-lookup"><span data-stu-id="71d50-886">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="71d50-887">(Vergessen Sie nicht das Sternchen am Anfang der Zeichenfolge.)</span><span class="sxs-lookup"><span data-stu-id="71d50-887">(Don't miss the asterisk at the start of the string.)</span></span>

![Zusätzliche PerfView-Anbieter](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="71d50-889">Der Windows-EventLog-Anbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-889">Windows EventLog provider</span></span>

<span data-ttu-id="71d50-890">Das Anbieterpaket [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sendet eine Protokollausgabe in das Windows-Ereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="71d50-890">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="71d50-891">Mithilfe von [AddEventLog-Überladungen](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) können Sie <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> übergeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-891">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="71d50-892">Wenn `null` oder nicht angegeben, werden die folgenden Standardeinstellungen verwendet:</span><span class="sxs-lookup"><span data-stu-id="71d50-892">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="71d50-893">`LogName` „Anwendung“</span><span class="sxs-lookup"><span data-stu-id="71d50-893">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="71d50-894">`SourceName` „.NET Runtime“</span><span class="sxs-lookup"><span data-stu-id="71d50-894">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="71d50-895">`MachineName` Lokaler Computer</span><span class="sxs-lookup"><span data-stu-id="71d50-895">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="71d50-896">Ereignisse werden für die [Warnstufe und höher](#log-level) protokolliert.</span><span class="sxs-lookup"><span data-stu-id="71d50-896">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="71d50-897">Legen Sie die Protokollierungsebene fest, um Ereignisse, die niedriger als `Warning` sind, zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="71d50-897">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="71d50-898">Fügen Sie beispielsweise Folgendes zur *appsettings.json*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="71d50-898">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="71d50-899">Der TraceSource-Anbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-899">TraceSource provider</span></span>

<span data-ttu-id="71d50-900">Das Anbieterpaket [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) verwendet die <xref:System.Diagnostics.TraceSource>-Bibliotheken und -Anbieter.</span><span class="sxs-lookup"><span data-stu-id="71d50-900">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="71d50-901">Mithilfe von [AddTraceSource-Überladungen](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) können Sie eine Quelloption und einen Listener für die Ablaufverfolgung übergeben.</span><span class="sxs-lookup"><span data-stu-id="71d50-901">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="71d50-902">Um diesen Anbieter zu verwenden, muss eine App unter .NET Framework (anstelle von .NET Core) ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="71d50-902">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="71d50-903">Der Anbieter kann Nachrichten an eine Vielzahl von [Listenern](/dotnet/framework/debug-trace-profile/trace-listeners) weiterleiten, z.B. an den in der Beispiel-App verwendeten <xref:System.Diagnostics.TextWriterTraceListener>.</span><span class="sxs-lookup"><span data-stu-id="71d50-903">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="71d50-904">Der Azure App Service-Anbieter</span><span class="sxs-lookup"><span data-stu-id="71d50-904">Azure App Service provider</span></span>

<span data-ttu-id="71d50-905">Das Anbieterpaket [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) schreibt Protokolle in Textdateien in das Dateisystem einer Azure App Service-App und in [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in einem Azure Storage-Konto.</span><span class="sxs-lookup"><span data-stu-id="71d50-905">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="71d50-906">Dieses Anbieterpaket ist nicht im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="71d50-906">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="71d50-907">Wenn Sie Anwendungen für .NET Framework entwickeln oder auf das `Microsoft.AspNetCore.App`-Metapaket verweisen, fügen Sie das Anbieterpaket dem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="71d50-907">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="71d50-908">Eine <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>-Überladung ermöglicht das Übergeben von <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="71d50-908">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="71d50-909">Das settings-Objekt kann Standardeinstellungen überschreiben, z.B. die Protokollierungsausgabevorlage, den Blobnamen und die maximale Dateigröße.</span><span class="sxs-lookup"><span data-stu-id="71d50-909">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="71d50-910">(Die *Ausgabevorlage* ist eine Nachrichtenvorlage, die auf alle Protokolle zusätzlich zu dem angewendet wird, was mit einem `ILogger`-Methodenaufruf bereitgestellt wird.)</span><span class="sxs-lookup"><span data-stu-id="71d50-910">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="71d50-911">Wenn Sie eine Bereitstellung in einer App Service-App durchführen, berücksichtigt die Anwendung die Einstellungen im Abschnitt [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) (App Service-Protokolle) auf der Seite **App Services** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="71d50-911">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="71d50-912">Bei einem Update der folgenden Einstellungen werden die Änderungen sofort wirksam, ohne dass ein Neustart oder eine erneute Bereitstellung der App notwendig ist.</span><span class="sxs-lookup"><span data-stu-id="71d50-912">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="71d50-913">**Anwendungsprotokoll (Dateisystem)**</span><span class="sxs-lookup"><span data-stu-id="71d50-913">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="71d50-914">**Anwendungsprotokoll (Blob)**</span><span class="sxs-lookup"><span data-stu-id="71d50-914">**Application Logging (Blob)**</span></span>

<span data-ttu-id="71d50-915">Der Standardspeicherort für Protokolldateien ist der Ordner *D:\\home\\LogFiles\\Application*, und der standardmäßige Dateiname lautet *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="71d50-915">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="71d50-916">Die Dateigröße ist standardmäßig auf 10 MB beschränkt, und die maximal zulässige Anzahl beibehaltener Dateien lautet 2.</span><span class="sxs-lookup"><span data-stu-id="71d50-916">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="71d50-917">Der Standardblobname lautet *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="71d50-917">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="71d50-918">Der Anbieter funktioniert nur, wenn das Projekt in der Azure-Umgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-918">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="71d50-919">Bei einer lokalen Ausführung zeigt er keine Auswirkungen. Der Anbieter schreibt keine Protokolle in lokale Dateien oder den lokalen Entwicklungsspeicher für BLOBs.</span><span class="sxs-lookup"><span data-stu-id="71d50-919">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="71d50-920">Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="71d50-920">Azure log streaming</span></span>

<span data-ttu-id="71d50-921">Azure-Protokollstreaming ermöglicht Ihnen eine Echtzeitanzeige der Protokollaktivität für:</span><span class="sxs-lookup"><span data-stu-id="71d50-921">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="71d50-922">App-Server</span><span class="sxs-lookup"><span data-stu-id="71d50-922">The app server</span></span>
* <span data-ttu-id="71d50-923">Webserver</span><span class="sxs-lookup"><span data-stu-id="71d50-923">The web server</span></span>
* <span data-ttu-id="71d50-924">Ablaufverfolgung für Anforderungsfehler</span><span class="sxs-lookup"><span data-stu-id="71d50-924">Failed request tracing</span></span>

<span data-ttu-id="71d50-925">So konfigurieren Sie das Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="71d50-925">To configure Azure log streaming:</span></span>

* <span data-ttu-id="71d50-926">Navigieren Sie von der Portalseite Ihrer App zur Seite **App Service-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="71d50-926">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="71d50-927">Legen Sie **Anwendungsprotokollierung (Dateisystem)** auf **Ein** fest.</span><span class="sxs-lookup"><span data-stu-id="71d50-927">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="71d50-928">Wählen Sie die **Protokollierungsebene**.</span><span class="sxs-lookup"><span data-stu-id="71d50-928">Choose the log **Level**.</span></span> <span data-ttu-id="71d50-929">Diese Einstellung gilt nur für das Azure-Protokollstreaming, nicht für andere Protokollierungsanbieter in der App.</span><span class="sxs-lookup"><span data-stu-id="71d50-929">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="71d50-930">Navigieren Sie zur Seite **Log Stream** (Protokollstream), um App-Meldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="71d50-930">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="71d50-931">Diese werden von der App über die `ILogger`-Schnittstelle protokolliert.</span><span class="sxs-lookup"><span data-stu-id="71d50-931">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="71d50-932">Ablaufverfolgungsprotokollierung für Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="71d50-932">Azure Application Insights trace logging</span></span>

<span data-ttu-id="71d50-933">Das [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)-Anbieterpaket schreibt Protokolle in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="71d50-933">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="71d50-934">Application Insights ist ein Dienst, der eine Web-App überwacht und Tools für Abfragen und Analysen von Telemetriedaten zur Verfügung stellt.</span><span class="sxs-lookup"><span data-stu-id="71d50-934">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="71d50-935">Wenn Sie diesen Anbieter verwenden, können Sie Ihre Protokolle mithilfe der Application Insights-Tools abfragen und analysieren.</span><span class="sxs-lookup"><span data-stu-id="71d50-935">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="71d50-936">Der Protokollierungsanbieter ist als Abhängigkeit von [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) enthalten, d.h. als das Paket, das alle verfügbaren Telemetriedaten für ASP.NET Core bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="71d50-936">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="71d50-937">Wenn Sie dieses Paket verwenden, ist nicht erforderlich, das Anbieterpaket zu installieren.</span><span class="sxs-lookup"><span data-stu-id="71d50-937">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="71d50-938">Verwenden Sie nicht das [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)-Paket, das für ASP.NET 4.x bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="71d50-938">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="71d50-939">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="71d50-939">For more information, see the following resources:</span></span>

* [<span data-ttu-id="71d50-940">Application Insights-Übersicht</span><span class="sxs-lookup"><span data-stu-id="71d50-940">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="71d50-941">[Anwendungseinblicke für ASP.NET Core-Anwendungen](/azure/azure-monitor/app/asp-net-core): Beginnen Sie hier, wenn Sie die gesamte Bandbreite der Application Insights-Telemetrie zusammen mit der Protokollierung implementieren möchten.</span><span class="sxs-lookup"><span data-stu-id="71d50-941">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="71d50-942">[ApplicationInsightsLoggerProvider für .NET Core ILogger-Protokolle](/azure/azure-monitor/app/ilogger): Beginnen Sie hier, wenn Sie den Protokollierungsanbieter ohne die übrige Application Insights-Telemetrie implementieren möchten.</span><span class="sxs-lookup"><span data-stu-id="71d50-942">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="71d50-943">[Application Insights logging adapters (Adapter zur Protokollierung für Application Insights)](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="71d50-943">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="71d50-944">[Installieren, Konfigurieren und Initialisieren des Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): interaktive Tutorials auf der Microsoft Learn-Website.</span><span class="sxs-lookup"><span data-stu-id="71d50-944">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="71d50-945">Protokollierungsanbieter von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="71d50-945">Third-party logging providers</span></span>

<span data-ttu-id="71d50-946">Protokollierungsframeworks von Drittanbietern aufgeführt, die mit ASP.NET Core funktionieren:</span><span class="sxs-lookup"><span data-stu-id="71d50-946">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="71d50-947">[elmah.io](https://elmah.io/) ([GitHub-Repository](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="71d50-947">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="71d50-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub-Repository](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="71d50-948">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="71d50-949">[JSNLog](https://jsnlog.com/) ([GitHub-Repository](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="71d50-949">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="71d50-950">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="71d50-950">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="71d50-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub-Repository](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="71d50-951">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="71d50-952">[Loggr](https://loggr.net/) ([GitHub-Repository](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="71d50-952">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="71d50-953">[NLog](https://nlog-project.org/) ([GitHub-Repository](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="71d50-953">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="71d50-954">[Sentry](https://sentry.io/welcome/) ([GitHub-Repository](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="71d50-954">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="71d50-955">[Serilog](https://serilog.net/) ([GitHub-Repository](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="71d50-955">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="71d50-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub-Repository](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="71d50-956">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="71d50-957">Einige Drittanbieterframeworks können eine [semantische Protokollierung (auch als strukturierte Protokollierung bezeichnet)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) ausführen.</span><span class="sxs-lookup"><span data-stu-id="71d50-957">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="71d50-958">Die Verwendung eines Frameworks von Drittanbietern ist ähnlich wie die Verwendung eines integrierten Anbieters:</span><span class="sxs-lookup"><span data-stu-id="71d50-958">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="71d50-959">Fügen Sie Ihrem Paket ein NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="71d50-959">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="71d50-960">Rufen Sie eine `ILoggerFactory`-Erweiterungsmethode auf, die vom Protokollierungsframework bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="71d50-960">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="71d50-961">Weitere Informationen finden Sie in der Dokumentation zum jeweiligen Anbieter.</span><span class="sxs-lookup"><span data-stu-id="71d50-961">For more information, see each provider's documentation.</span></span> <span data-ttu-id="71d50-962">Protokollierungsanbieter von Drittanbietern werden von Microsoft nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="71d50-962">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71d50-963">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="71d50-963">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
