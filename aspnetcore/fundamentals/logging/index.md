---
title: Protokollieren in .NET Core und ASP.NET Core
author: rick-anderson
description: In diesem Artikel wird das Verwenden des Protokollierungsframeworks erläutert, das vom Microsoft.Extensions.Logging-NuGet-Paket bereitgestellt wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/17/2020
uid: fundamentals/logging/index
ms.openlocfilehash: a3c63b738d3eaa51249475b88d78572038348a7a
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440739"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="b71ed-103">Protokollieren in .NET Core und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b71ed-103">Logging in .NET Core and ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b71ed-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b71ed-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b71ed-105">.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="b71ed-106">Dieser Artikel zeigt, wie Sie die Protokollierungs-API mit integrierten Anbietern verwenden können.</span><span class="sxs-lookup"><span data-stu-id="b71ed-106">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="b71ed-107">Die meisten der Codebeispiele in diesem Artikel stammen aus ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="b71ed-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="b71ed-108">Die auf die Protokollierung bezogenen Teile dieser Codeausschnitte gelten für jede .NET Core-App, die den [generischen Host](xref:fundamentals/host/generic-host) verwendet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="b71ed-109">Ein Beispiel zum Verwenden des generischen Hosts in einer nicht webbasierten Konsolen-App finden Sie in der *Program.cs*-Datei der [Beispiel-App für Hintergrundaufgaben](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span><span class="sxs-lookup"><span data-stu-id="b71ed-109">For an example of how to use the Generic Host in a non-web console app, see the *Program.cs* file of the [Background Tasks sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) (<xref:fundamentals/host/hosted-services>).</span></span>

<span data-ttu-id="b71ed-110">Das Protokollieren von Code für Apps ohne generischen Host weicht in Bezug auf das [Hinzufügen von Anbietern](#add-providers) und das [Erstellen von Protokollierungen](#create-logs) ab.</span><span class="sxs-lookup"><span data-stu-id="b71ed-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="b71ed-111">Codebeispiele ohne Host finden sich in diesen Abschnitten des Artikels.</span><span class="sxs-lookup"><span data-stu-id="b71ed-111">Non-host code examples are shown in those sections of the article.</span></span>

<span data-ttu-id="b71ed-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b71ed-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="b71ed-113">Hinzufügen von Anbietern</span><span class="sxs-lookup"><span data-stu-id="b71ed-113">Add providers</span></span>

<span data-ttu-id="b71ed-114">Ein Protokollierungsanbieter zeigt Protokolle an oder speichert diese.</span><span class="sxs-lookup"><span data-stu-id="b71ed-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="b71ed-115">Beispielsweise zeigt der Konsolenanbieter Protokolle in der Konsole an, und der Azure Application Insights-Anbieter speichert sie in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="b71ed-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="b71ed-116">Protokolle können durch das Hinzufügen mehrerer Anbieter an mehrere Ziele gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="b71ed-117">Um einen Anbieter in einer App hinzuzufügen, die den generischen Host verwendet, rufen Sie die `Add{provider name}`-Erweiterungsmethode des Anbieters in *Program.cs* auf:</span><span class="sxs-lookup"><span data-stu-id="b71ed-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="b71ed-118">Rufen Sie in einer Konsolen-App ohne Host die `Add{provider name}`-Erweiterungsmethode des Anbieters auf, während Sie eine `LoggerFactory` erstellen:</span><span class="sxs-lookup"><span data-stu-id="b71ed-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="b71ed-119">`LoggerFactory` und `AddConsole` erfordern eine `using`-Anweisung für `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="b71ed-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="b71ed-120">Die ASP.NET-Standardprojektvorlagen rufen die Methode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> auf, die die folgenden Protokollierungsanbieter hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="b71ed-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* [<span data-ttu-id="b71ed-121">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-121">Console</span></span>](#console-provider)
* [<span data-ttu-id="b71ed-122">Debuggen</span><span class="sxs-lookup"><span data-stu-id="b71ed-122">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="b71ed-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="b71ed-123">EventSource</span></span>](#event-source-provider)
* <span data-ttu-id="b71ed-124">[EventLog](#windows-eventlog-provider) (nur bei Ausführung unter Windows)</span><span class="sxs-lookup"><span data-stu-id="b71ed-124">[EventLog](#windows-eventlog-provider) (only when running on Windows)</span></span>

<span data-ttu-id="b71ed-125">Sie können die Standardanbieter durch Ihre eigene Auswahl ersetzen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="b71ed-126">Rufen Sie <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> auf, und fügen Sie die gewünschten Anbieter hinzu.</span><span class="sxs-lookup"><span data-stu-id="b71ed-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

<span data-ttu-id="b71ed-127">Informationen zu den [integrierten Protokollierungsanbietern](#built-in-logging-providers) sowie zu [Protokollierungsanbietern von Drittanbietern](#third-party-logging-providers) finden Sie weiter unten in diesem Artikel.</span><span class="sxs-lookup"><span data-stu-id="b71ed-127">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="b71ed-128">Erstellen von Protokollen</span><span class="sxs-lookup"><span data-stu-id="b71ed-128">Create logs</span></span>

<span data-ttu-id="b71ed-129">Verwenden Sie zum Erstellen von Protokollen ein <xref:Microsoft.Extensions.Logging.ILogger%601>-Objekt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-129">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="b71ed-130">Rufen Sie in einer Web-App oder einem gehosteten Dienst einen `ILogger` aus der Abhängigkeitsinjektion ab.</span><span class="sxs-lookup"><span data-stu-id="b71ed-130">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="b71ed-131">Verwenden Sie in Konsolen-Apps ohne Host `LoggerFactory`, um einen `ILogger` zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-131">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="b71ed-132">Im folgenden ASP.NET Core-Beispiel wird eine Protokollierung mit `TodoApiSample.Pages.AboutModel` als Kategorie erstellt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-132">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="b71ed-133">Die Protokoll*kategorie* ist eine Zeichenfolge, die jedem Protokoll zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="b71ed-133">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="b71ed-134">Die von der Abhängigkeitsinjektion bereitgestellte `ILogger<T>`-Instanz erstellt Protokolle, die den vollqualifizierten Namen vom Typ `T` als Kategorie aufweisen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-134">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="b71ed-135">Im folgenden Beispiel einer Konsolen-App ohne Host wird eine Protokollierung mit `LoggingConsoleApp.Program` als Kategorie erstellt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-135">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

<span data-ttu-id="b71ed-136">In den folgenden Beispielen zu ASP.NET Core und einer Konsolen-App werden von der Protokollierung Protokolle mit dem Protokolliergrad `Information` erstellt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-136">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="b71ed-137">Der Protokollierungs*grad* gibt den Schweregrad des protokollierten Ereignisses an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-137">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

<span data-ttu-id="b71ed-138">[Grade](#log-level) und [Kategorien](#log-category) werden weiter unten in diesem Artikel ausführlicher erläutert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-138">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="b71ed-139">Erstellen von Protokollen in der Program-Klasse</span><span class="sxs-lookup"><span data-stu-id="b71ed-139">Create logs in the Program class</span></span>

<span data-ttu-id="b71ed-140">Wenn Protokolle in die `Program`-Klasse einer ASP.NET Core-App geschrieben werden sollen, rufen Sie nach dem Erstellen des Hosts eine `ILogger`-Instanz aus der Abhängigkeitsinjektion ab:</span><span class="sxs-lookup"><span data-stu-id="b71ed-140">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

<span data-ttu-id="b71ed-141">Die Protokollierung während der Hosterstellung wird nicht direkt unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-141">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="b71ed-142">Es kann jedoch eine separate Protokollierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-142">However, a separate logger can be used.</span></span> <span data-ttu-id="b71ed-143">Im folgenden Beispiel wird eine [Serilog](https://serilog.net/)-Protokollierung zum Anmelden von `CreateHostBuilder` verwendet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-143">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="b71ed-144">`AddSerilog` verwendet die in `Log.Logger` angegebene statische Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="b71ed-144">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="b71ed-145">Erstellen von Protokollen in der Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="b71ed-145">Create logs in the Startup class</span></span>

<span data-ttu-id="b71ed-146">Wenn Protokolle in die `Startup.Configure`-Methode einer ASP.NET Core-App geschrieben werden sollen, schließen Sie einen `ILogger`-Parameter in die Methodensignatur ein:</span><span class="sxs-lookup"><span data-stu-id="b71ed-146">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="b71ed-147">Das Schreiben von Protokollen vor Abschluss der Einrichtung des Abhängigkeitsinjektion-Containers in der `Startup.ConfigureServices`-Methode wird nicht unterstützt:</span><span class="sxs-lookup"><span data-stu-id="b71ed-147">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="b71ed-148">Die Protokollierungsinjektion in den `Startup`-Konstruktor wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-148">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="b71ed-149">Die Protokollierungsinjektion in die `Startup.ConfigureServices`-Methodensignatur wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-149">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="b71ed-150">Dies Einschränkung ist darauf zurückzuführen, dass die Protokollierung von der Abhängigkeitsinjektion und der Konfiguration abhängt (die wiederum von der Abhängigkeitsinjektion abhängt).</span><span class="sxs-lookup"><span data-stu-id="b71ed-150">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="b71ed-151">Der Abhängigkeitsinjektion-Container wird erst nach Fertigstellung von `ConfigureServices` eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-151">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="b71ed-152">Die Konstruktorinjektion einer Protokollierung in `Startup` funktioniert in früheren Versionen von ASP.NET Core, da für den Webhost ein separater Abhängigkeitsinjektion-Container erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-152">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="b71ed-153">Weitere Informationen dazu, warum nur ein Container für den generischen Host erstellt wird, erhalten Sie in der [Breaking Change-Ankündigung](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="b71ed-153">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="b71ed-154">Wenn Sie einen Dienst konfigurieren müssen, der von `ILogger<T>` abhängt, können Sie dies immer noch mithilfe der Konstruktorinjektion oder durch Bereitstellen einer Factorymethode ausführen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-154">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="b71ed-155">Die Nutzung einer Factorymethode wird nur empfohlen, wenn Sie keine andere Wahl haben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-155">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="b71ed-156">Angenommen, Sie müssen eine Eigenschaft mit einem Dienst aus der Abhängigkeitsinjektion füllen:</span><span class="sxs-lookup"><span data-stu-id="b71ed-156">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="b71ed-157">Der hervorgehobene Code oben ist eine `Func`, die ausgeführt wird, wenn der Abhängigkeitsinjektion-Container eine Instanz von `MyService` erstellen muss.</span><span class="sxs-lookup"><span data-stu-id="b71ed-157">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="b71ed-158">Auf diese Weise können Sie auf alle registrierten Dienste zugreifen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-158">You can access any of the registered services in this way.</span></span>

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="b71ed-159">Keine asynchronen Protokollierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="b71ed-159">No asynchronous logger methods</span></span>

<span data-ttu-id="b71ed-160">Die Protokollierung sollte so schnell erfolgen, dass sich die Leistungskosten von asynchronem Code nicht lohnen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-160">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="b71ed-161">Wenn Ihr Protokollierungsdatenspeicher langsam ist, schreiben Sie nicht direkt in ihn.</span><span class="sxs-lookup"><span data-stu-id="b71ed-161">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="b71ed-162">Erwägen Sie, die Protokollnachrichten zunächst in einen schnellen Speicher zu schreiben und sie dann später in den langsamen Speicher zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-162">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="b71ed-163">Wenn Sie beispielsweise in SQL Server protokollieren, möchten Sie dies nicht direkt in einer `Log`-Methode tun, da die `Log`-Methoden synchron sind.</span><span class="sxs-lookup"><span data-stu-id="b71ed-163">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="b71ed-164">Fügen Sie stattdessen die Protokollmeldungen synchron zu einer Warteschlange im Arbeitsspeicher hinzu, und rufen Sie mithilfe eines Hintergrundworkers die Nachrichten aus der Warteschlange ab, um sie dann asynchron per Pushvorgang an SQL Server zu übertragen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-164">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="b71ed-165">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/11801).</span><span class="sxs-lookup"><span data-stu-id="b71ed-165">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="b71ed-166">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b71ed-166">Configuration</span></span>

<span data-ttu-id="b71ed-167">Die Konfiguration von Protokollierungsanbietern erfolgt durch mindestens einen Konfigurationsanbieter:</span><span class="sxs-lookup"><span data-stu-id="b71ed-167">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="b71ed-168">Dateiformate (INI, JSON und XML)</span><span class="sxs-lookup"><span data-stu-id="b71ed-168">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="b71ed-169">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="b71ed-169">Command-line arguments.</span></span>
* <span data-ttu-id="b71ed-170">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-170">Environment variables.</span></span>
* <span data-ttu-id="b71ed-171">Speicherinterne .NET-Objekte</span><span class="sxs-lookup"><span data-stu-id="b71ed-171">In-memory .NET objects.</span></span>
* <span data-ttu-id="b71ed-172">Der unverschlüsselte Speicher von [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b71ed-172">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="b71ed-173">Ein unverschlüsselter Benutzerspeicher, z.B. [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="b71ed-173">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="b71ed-174">Benutzerdefinierte Anbieter (installiert oder erstellt).</span><span class="sxs-lookup"><span data-stu-id="b71ed-174">Custom providers (installed or created).</span></span>

<span data-ttu-id="b71ed-175">Die Konfiguration der Protokollierung wird meist vom `Logging`-Abschnitt von Anwendungseinstellungsdateien angegeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-175">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="b71ed-176">Im folgenden Beispiel werden die Inhalte einer herkömmlichen *appsettings.Development.json*-Datei veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="b71ed-176">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="b71ed-177">Die `Logging`-Eigenschaft kann den `LogLevel` und Protokollanbietereigenschaften beinhalten (Konsole wird angezeigt).</span><span class="sxs-lookup"><span data-stu-id="b71ed-177">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="b71ed-178">Die `LogLevel`-Eigenschaft unter `Logging` gibt den Mindest[grad](#log-level) an, der für ausgewählte Kategorien protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="b71ed-178">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="b71ed-179">Im Beispiel protokollieren die Kategorien `System` und `Microsoft` mit dem Grad `Information` und alle anderen Kategorien mit dem Grad `Debug`.</span><span class="sxs-lookup"><span data-stu-id="b71ed-179">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="b71ed-180">Weitere Eigenschaften unter `Logging` geben Protokollierungsanbieter an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-180">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="b71ed-181">Das Beispiel bezieht sich auf den Konsolenanbieter.</span><span class="sxs-lookup"><span data-stu-id="b71ed-181">The example is for the Console provider.</span></span> <span data-ttu-id="b71ed-182">Wenn ein Anbieter [Protokollbereiche](#log-scopes) unterstützt, gibt `IncludeScopes` an, ob sie aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="b71ed-182">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="b71ed-183">Eine Anbietereigenschaft (z.B. `Console` im Beispiel) kann auch eine `LogLevel`-Eigenschaft angeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-183">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="b71ed-184">`LogLevel` unter einem Anbieter gibt die Grade an, die für diesen Anbieter protokolliert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-184">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="b71ed-185">Wenn Grade in `Logging.{providername}.LogLevel` angegeben werden, überschreiben sie alle Angaben in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="b71ed-185">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="b71ed-186">Die Protokollierungs-API umfasst kein Szenario zum Ändern der Protokollebene, während eine App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-186">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="b71ed-187">Einige Konfigurationsanbieter können jedoch die Konfiguration erneut laden, was sich unmittelbar auf die Protokollierungskonfiguration auswirkt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-187">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="b71ed-188">Beispielsweise lädt der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider), der durch `CreateDefaultBuilder` zum Lesen von Einstellungsdateien hinzugefügt wird, die Protokollierungskonfiguration standardmäßig erneut.</span><span class="sxs-lookup"><span data-stu-id="b71ed-188">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="b71ed-189">Wenn die Konfiguration im Code geändert wird, während eine App ausgeführt wird, kann die App [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) aufrufen, um ihre Protokollierungskonfiguration zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b71ed-189">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="b71ed-190">Informationen zur Implementierung von Konfigurationsanbieter finden Sie hier: <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b71ed-190">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="b71ed-191">Beispiel einer Protokollierungsausgabe</span><span class="sxs-lookup"><span data-stu-id="b71ed-191">Sample logging output</span></span>

<span data-ttu-id="b71ed-192">Mit dem im vorherigen Abschnitt gezeigten Beispielcode werden Protokolle in der Konsole angezeigt, wenn die Anwendung über die Befehlszeile ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-192">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="b71ed-193">Hier ein Beispiel für eine Konsolenausgabe:</span><span class="sxs-lookup"><span data-stu-id="b71ed-193">Here's an example of console output:</span></span>

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

<span data-ttu-id="b71ed-194">Die vorherigen Protokolle wurden generiert, indem eine HTTP Get-Anforderung an die Beispiel-App unter `http://localhost:5000/api/todo/0` vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="b71ed-194">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="b71ed-195">Nachfolgend sehen Sie, wie die gleichen Protokolle im Debugfenster angezeigt werden, wenn Sie die Beispiel-App in Visual Studio ausführen:</span><span class="sxs-lookup"><span data-stu-id="b71ed-195">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="b71ed-196">Die über die `ILogger`-Aufrufe aus dem vorherigen Abschnitt erstellten Protokolle beginnen mit „TodoApiSample“.</span><span class="sxs-lookup"><span data-stu-id="b71ed-196">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="b71ed-197">Protokolle, die mit Microsoft-Kategorien beginnen, stammen aus ASP.NET Core-Frameworkcode.</span><span class="sxs-lookup"><span data-stu-id="b71ed-197">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="b71ed-198">ASP.NET Core und Anwendungscode verwenden dieselbe Protokollierungs-API und dieselben Protokollierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="b71ed-198">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="b71ed-199">In den übrigen Abschnitten dieses Artikels werden einige Details und Optionen für die Protokollierung erläutert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-199">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="b71ed-200">NuGet-Pakete</span><span class="sxs-lookup"><span data-stu-id="b71ed-200">NuGet packages</span></span>

<span data-ttu-id="b71ed-201">Die Schnittstellen `ILogger` und `ILoggerFactory` befinden sich in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), ihre Standardimplementierungen sind in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) enthalten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-201">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="b71ed-202">Protokollkategorie</span><span class="sxs-lookup"><span data-stu-id="b71ed-202">Log category</span></span>

<span data-ttu-id="b71ed-203">Wenn ein `ILogger`-Objekt erstellt wird, wird eine *Kategorie* für dieses Objekt angegeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-203">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="b71ed-204">Diese Kategorie ist in jeder Protokollmeldung enthalten, die von dieser Instanz von `ILogger` erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-204">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="b71ed-205">Die Kategorie kann eine beliebige Zeichenfolge sein, aber die Konvention besteht in der Verwendung des Klassennamens, z.B. „TodoApi.Controllers.TodoController“.</span><span class="sxs-lookup"><span data-stu-id="b71ed-205">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="b71ed-206">Verwenden Sie `ILogger<T>` zum Abrufen einer `ILogger`-Instanz, die den vollqualifizierten Typnamen von `T` als Kategorie verwendet:</span><span class="sxs-lookup"><span data-stu-id="b71ed-206">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="b71ed-207">Um die Kategorie explizit anzugeben, rufen Sie `ILoggerFactory.CreateLogger` auf:</span><span class="sxs-lookup"><span data-stu-id="b71ed-207">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="b71ed-208">`ILogger<T>` entspricht dem Aufruf von `CreateLogger` mit dem vollqualifizierten Typnamen `T`.</span><span class="sxs-lookup"><span data-stu-id="b71ed-208">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="b71ed-209">Protokolliergrad</span><span class="sxs-lookup"><span data-stu-id="b71ed-209">Log level</span></span>

<span data-ttu-id="b71ed-210">Jedes Protokoll gibt einen <xref:Microsoft.Extensions.Logging.LogLevel>-Wert an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-210">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="b71ed-211">Der Protokolliergrad gibt den Schweregrad oder die Wichtigkeit an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-211">The log level indicates the severity or importance.</span></span> <span data-ttu-id="b71ed-212">Sie können beispielsweise ein `Information`-Protokoll schreiben, wenn eine Methode normal endet, und ein `Warning`-Protokoll, wenn eine Methode den Statuscode *404 Not Found* zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-212">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="b71ed-213">Der folgende Code erstellt `Information`- und `Warning`-Protokolle:</span><span class="sxs-lookup"><span data-stu-id="b71ed-213">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="b71ed-214">Im Code oben ist der erste Parameter die [Protokollereignis-ID](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="b71ed-214">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="b71ed-215">Der zweite Parameter ist eine Meldungsvorlage mit Platzhaltern für Argumentwerte, die von den verbleibenden Methodenparametern bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-215">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="b71ed-216">Die Methodenparameter werden im [Abschnitt „Meldungsvorlage“](#log-message-template) später in diesem Artikel erläutert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-216">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="b71ed-217">Protokollmethoden, die den Protokolliergrad im Methodennamen enthalten (z.B. `LogInformation` und `LogWarning`), sind [Erweiterungsmethoden für ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="b71ed-217">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="b71ed-218">Diese Methoden rufen eine `Log`-Methode auf, die einen `LogLevel`-Parameter annimmt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-218">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="b71ed-219">Sie können anstelle eines Aufrufs dieser Erweiterungsmethoden die `Log`-Methode direkt aufrufen, aber die Syntax ist relativ kompliziert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-219">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="b71ed-220">Weitere Informationen finden Sie unter <xref:Microsoft.Extensions.Logging.ILogger> und im [Quellcode für Protokollierungserweiterungen](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="b71ed-220">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="b71ed-221">ASP.NET Core definiert die folgenden Protokolliergrade. Die Reihenfolge reicht vom geringsten bis zum höchsten Schweregrad.</span><span class="sxs-lookup"><span data-stu-id="b71ed-221">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="b71ed-222">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="b71ed-222">Trace = 0</span></span>

  <span data-ttu-id="b71ed-223">Protokolliert Informationen, die in der Regel nur für das Debuggen nützlich sind.</span><span class="sxs-lookup"><span data-stu-id="b71ed-223">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="b71ed-224">Diese Meldungen können sensible Anwendungsdaten enthalten und sollten daher nicht in einer Produktionsumgebung aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-224">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="b71ed-225">*Standardmäßig deaktiviert.*</span><span class="sxs-lookup"><span data-stu-id="b71ed-225">*Disabled by default.*</span></span>

* <span data-ttu-id="b71ed-226">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="b71ed-226">Debug = 1</span></span>

  <span data-ttu-id="b71ed-227">Protokolliert Informationen, die bei der Entwicklung und beim Debuggen hilfreich sein können.</span><span class="sxs-lookup"><span data-stu-id="b71ed-227">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="b71ed-228">Beispiel: `Entering method Configure with flag set to true.` Aktivieren Sie Protokolle mit dem Protokolliergrad `Debug` aufgrund des hohen Protokollaufkommens nur zur Problembehandlung in der Produktion.</span><span class="sxs-lookup"><span data-stu-id="b71ed-228">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="b71ed-229">Information = 2</span><span class="sxs-lookup"><span data-stu-id="b71ed-229">Information = 2</span></span>

  <span data-ttu-id="b71ed-230">Zur Nachverfolgung des allgemeinen Ablaufs der App.</span><span class="sxs-lookup"><span data-stu-id="b71ed-230">For tracking the general flow of the app.</span></span> <span data-ttu-id="b71ed-231">Diese Protokolle haben typischerweise einen langfristigen Nutzen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-231">These logs typically have some long-term value.</span></span> <span data-ttu-id="b71ed-232">Ein Beispiel: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="b71ed-232">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="b71ed-233">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="b71ed-233">Warning = 3</span></span>

  <span data-ttu-id="b71ed-234">Für ungewöhnliche oder unerwartete Ereignisse im App-Verlauf.</span><span class="sxs-lookup"><span data-stu-id="b71ed-234">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="b71ed-235">Dazu können Fehler oder andere Bedingungen gehören, die zwar nicht zum Beenden der App führen, aber eventuell untersucht werden müssen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-235">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="b71ed-236">Behandelte Ausnahmen sind eine typische Verwendung für den Protokolliergrad `Warning`.</span><span class="sxs-lookup"><span data-stu-id="b71ed-236">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="b71ed-237">Ein Beispiel: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="b71ed-237">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="b71ed-238">Error = 4</span><span class="sxs-lookup"><span data-stu-id="b71ed-238">Error = 4</span></span>

  <span data-ttu-id="b71ed-239">Für Fehler und Ausnahmen, die nicht behandelt werden können.</span><span class="sxs-lookup"><span data-stu-id="b71ed-239">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="b71ed-240">Diese Meldungen weisen auf einen Fehler in der aktuellen Aktivität oder im aktuellen Vorgang (z.B. in der aktuellen HTTP-Anforderung) und nicht auf einen anwendungsweiten Fehler hin.</span><span class="sxs-lookup"><span data-stu-id="b71ed-240">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="b71ed-241">Beispielprotokollmeldung: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="b71ed-241">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="b71ed-242">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="b71ed-242">Critical = 5</span></span>

  <span data-ttu-id="b71ed-243">Für Fehler, die sofortige Aufmerksamkeit erfordern.</span><span class="sxs-lookup"><span data-stu-id="b71ed-243">For failures that require immediate attention.</span></span> <span data-ttu-id="b71ed-244">Beispiel: Szenarios mit Datenverlust, Speichermangel.</span><span class="sxs-lookup"><span data-stu-id="b71ed-244">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="b71ed-245">Verwenden Sie den Protokolliergrad, um die Menge an Protokollausgabedaten zu steuern, die in ein bestimmtes Speichermedium geschrieben oder an ein Anzeigefenster ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-245">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="b71ed-246">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b71ed-246">For example:</span></span>

* <span data-ttu-id="b71ed-247">In einer Produktionsumgebung</span><span class="sxs-lookup"><span data-stu-id="b71ed-247">In production:</span></span>
  * <span data-ttu-id="b71ed-248">Das Protokollieren von `Trace` über die `Information`-Ebenen verursacht viele detaillierte Protokollmeldungen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-248">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="b71ed-249">Protokollieren Sie `Trace` über Nachrichten auf `Information`-Ebene in einem kostengünstigen Speicher mit hohem Volumen, um die Kosten zu überwachen und die Grenzwerte des Datenspeichers nicht zu überschreiten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-249">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="b71ed-250">Das Protokollieren von `Warning` über die `Critical`-Ebenen verursacht weniger und kürzere Protokollmeldungen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-250">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="b71ed-251">Daher müssen Sie sich über Kosten- und Speichergrenzwerte keine Sorgen machen und sind bei der Auswahl des Datenspeichers flexibler.</span><span class="sxs-lookup"><span data-stu-id="b71ed-251">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="b71ed-252">Entwicklungsphase:</span><span class="sxs-lookup"><span data-stu-id="b71ed-252">During development:</span></span>
  * <span data-ttu-id="b71ed-253">Protokollieren Sie `Warning` über `Critical`-Meldungen an der Konsole.</span><span class="sxs-lookup"><span data-stu-id="b71ed-253">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="b71ed-254">Fügen Sie bei der Fehlerbehebung `Trace` über `Information`-Meldungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="b71ed-254">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="b71ed-255">Im Abschnitt [Protokollfilterung](#log-filtering) weiter unten in diesem Artikel wird erläutert, wie Sie steuern, welche Protokolliergrade ein Anbieter verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-255">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="b71ed-256">ASP.NET Core schreibt Protokolle für Frameworkereignisse.</span><span class="sxs-lookup"><span data-stu-id="b71ed-256">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="b71ed-257">Die zuvor in diesem Artikel gezeigten Protokollbeispiele enthielten Protokolle unterhalb des Protokolliergrads `Information`, deshalb wurden keine Protokolle des Protokolliergrads `Debug` oder `Trace` erstellt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-257">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="b71ed-258">Hier ist ein Beispiel für Konsolenprotokolle, die durch Ausführen der Beispiel-App generiert werden, die so konfiguriert ist, dass sie `Debug`-Protokolle anzeigt:</span><span class="sxs-lookup"><span data-stu-id="b71ed-258">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="b71ed-259">Protokollereignis-ID</span><span class="sxs-lookup"><span data-stu-id="b71ed-259">Log event ID</span></span>

<span data-ttu-id="b71ed-260">Jedes Protokoll kann eine *Ereignis-ID* angeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-260">Each log can specify an *event ID*.</span></span> <span data-ttu-id="b71ed-261">Die Beispiel-App verwendet hierzu eine lokal definierte `LoggingEvents`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="b71ed-261">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="b71ed-262">Eine Ereignis-ID ordnet eine Gruppe von Ereignissen zu.</span><span class="sxs-lookup"><span data-stu-id="b71ed-262">An event ID associates a set of events.</span></span> <span data-ttu-id="b71ed-263">Beispielsweise können alle Protokolle, die sich auf die Anzeige einer Liste von Elementen auf einer Seite beziehen, 1001 sein.</span><span class="sxs-lookup"><span data-stu-id="b71ed-263">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="b71ed-264">Der Protokollierungsanbieter kann die Ereignis-ID in einem ID-Feld, in der Protokollierungsmeldung oder gar nicht speichern.</span><span class="sxs-lookup"><span data-stu-id="b71ed-264">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="b71ed-265">Der Debuganbieter zeigt keine Ereignis-IDs an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-265">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="b71ed-266">Der Konsolenanbieter zeigt Ereignis-IDs in Klammern hinter der Kategorie an:</span><span class="sxs-lookup"><span data-stu-id="b71ed-266">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="b71ed-267">Protokollmeldungsvorlage</span><span class="sxs-lookup"><span data-stu-id="b71ed-267">Log message template</span></span>

<span data-ttu-id="b71ed-268">Jedes Protokoll gibt eine Meldungsvorlage an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-268">Each log specifies a message template.</span></span> <span data-ttu-id="b71ed-269">Die Meldungsvorlage kann Platzhalter enthalten, für die Argumente bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-269">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="b71ed-270">Verwenden Sie Namen für die Platzhalter, keine Zahlen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-270">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="b71ed-271">Die Reihenfolge der Platzhalter – nicht ihre Namen – bestimmt, welche Parameter verwendet werden, um ihre Werte zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-271">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="b71ed-272">Beachten Sie im folgenden Code, dass die Parameternamen in der Meldungsvorlage nicht in der richtigen Reihenfolge vorhanden sind:</span><span class="sxs-lookup"><span data-stu-id="b71ed-272">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="b71ed-273">Dieser Code erstellt eine Protokollierungsmeldung mit den Parameterwerten in der richtigen Reihenfolge:</span><span class="sxs-lookup"><span data-stu-id="b71ed-273">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="b71ed-274">Das Protokollierungsframework funktioniert auf diese Weise, damit Protokollierungsanbieter [semantische Protokollierung, die auch als strukturierte Protokollierung bezeichnet wird](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging), implementieren können.</span><span class="sxs-lookup"><span data-stu-id="b71ed-274">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="b71ed-275">Die Argumente selbst (nicht nur die formatierte Meldungsvorlage) werden an das Protokollierungssystem übergeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-275">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="b71ed-276">Diese Informationen ermöglichen es Protokollierungsanbietern, die Parameterwerte als Felder zu speichern.</span><span class="sxs-lookup"><span data-stu-id="b71ed-276">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="b71ed-277">Nehmen wir zum Beispiel an, dass Aufrufe von Protokollierungsmethoden folgendermaßen aussehen:</span><span class="sxs-lookup"><span data-stu-id="b71ed-277">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="b71ed-278">Wenn Sie die Protokolle an Azure Table Storage senden, kann jede Azure Table-Entität die Eigenschaften `ID` und `RequestTime` aufweisen. Dies vereinfacht die Abfrage von Protokolldaten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-278">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="b71ed-279">Eine Abfrage kann alle Protokolle in einem bestimmten `RequestTime`-Bereich finden, ohne die Uhrzeit aus den Textmeldungen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="b71ed-279">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="b71ed-280">Protokollieren von Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="b71ed-280">Logging exceptions</span></span>

<span data-ttu-id="b71ed-281">Die Protokollierungsmethoden umfassen Überladungen, die das Übergeben von Ausnahmen ermöglichen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="b71ed-281">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="b71ed-282">Die verschiedenen Anbieter verarbeiten die Ausnahmeinformationen auf unterschiedliche Weise.</span><span class="sxs-lookup"><span data-stu-id="b71ed-282">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="b71ed-283">Hier sehen Sie ein Beispiel einer Debuganbieterausgabe aus dem obigen Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="b71ed-283">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="b71ed-284">Protokollfilterung</span><span class="sxs-lookup"><span data-stu-id="b71ed-284">Log filtering</span></span>

<span data-ttu-id="b71ed-285">Sie können einen Mindestprotokolliergrad für einen bestimmten Anbieter und eine bestimmte Kategorie oder für alle Anbieter oder alle Kategorien festlegen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-285">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="b71ed-286">Alle Protokolle unter dem Mindestgrad werden nicht an diesen Anbieter weitergeleitet, sodass sie nicht angezeigt oder gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-286">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="b71ed-287">Wenn Sie alle Protokolle unterdrücken möchten, geben Sie `LogLevel.None` als Mindestprotokolliergrad an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-287">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="b71ed-288">Der ganzzahlige Wert von `LogLevel.None` lautet 6 und liegt über `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="b71ed-288">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="b71ed-289">Erstellen von Filterregeln in der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b71ed-289">Create filter rules in configuration</span></span>

<span data-ttu-id="b71ed-290">Der Projektvorlagencode ruft `CreateDefaultBuilder` auf, um die Protokollierung für die Konsolen-, Debug- und EventSource-Anbieter (ASP.NET Core 2.2 oder höher) einzurichten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-290">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="b71ed-291">Die `CreateDefaultBuilder`-Methode richtet die Protokollierung ein, um nach der Konfiguration in einem `Logging`-Abschnitt zu suchen; dies wird [weiter oben in diesem Artikel](#configuration) erläutert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-291">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="b71ed-292">Die Konfigurationsdaten geben die Mindestprotokolliergrade nach Anbieter und Kategorie an, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="b71ed-292">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="b71ed-293">Diese JSON-Konfiguration erstellt sechs Filterregeln: eine für den Debuganbieter, vier für den Konsolenanbieter und eine für alle Anbieter.</span><span class="sxs-lookup"><span data-stu-id="b71ed-293">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="b71ed-294">Eine einzelne Regel wird für jeden Anbieter ausgewählt, wenn ein `ILogger`-Objekt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-294">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="b71ed-295">Filterregeln im Code</span><span class="sxs-lookup"><span data-stu-id="b71ed-295">Filter rules in code</span></span>

<span data-ttu-id="b71ed-296">Das folgende Beispiel zeigt, wie Filterregeln im Code registriert werden:</span><span class="sxs-lookup"><span data-stu-id="b71ed-296">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

<span data-ttu-id="b71ed-297">Das zweite `AddFilter` gibt den Debuganbieter durch Verwendung des zugehörigen Typnamens an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-297">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="b71ed-298">Das erste `AddFilter` gilt für alle Anbieter, weil kein Anbietertyp angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-298">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="b71ed-299">Anwendung von Filterregeln</span><span class="sxs-lookup"><span data-stu-id="b71ed-299">How filtering rules are applied</span></span>

<span data-ttu-id="b71ed-300">Die Konfigurationsdaten und der in den vorangegangenen Beispielen gezeigte `AddFilter`-Code erzeugen die in der folgenden Tabelle gezeigten Regeln.</span><span class="sxs-lookup"><span data-stu-id="b71ed-300">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="b71ed-301">Die ersten sechs Regeln stammen aus dem Konfigurationsbeispiel, die letzten zwei Filter stammen aus dem Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="b71ed-301">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="b71ed-302">Anzahl</span><span class="sxs-lookup"><span data-stu-id="b71ed-302">Number</span></span> | <span data-ttu-id="b71ed-303">Anbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-303">Provider</span></span>      | <span data-ttu-id="b71ed-304">Kategorien beginnend mit...</span><span class="sxs-lookup"><span data-stu-id="b71ed-304">Categories that begin with ...</span></span>          | <span data-ttu-id="b71ed-305">Mindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="b71ed-305">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="b71ed-306">1</span><span class="sxs-lookup"><span data-stu-id="b71ed-306">1</span></span>      | <span data-ttu-id="b71ed-307">Debug</span><span class="sxs-lookup"><span data-stu-id="b71ed-307">Debug</span></span>         | <span data-ttu-id="b71ed-308">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="b71ed-308">All categories</span></span>                          | <span data-ttu-id="b71ed-309">Information</span><span class="sxs-lookup"><span data-stu-id="b71ed-309">Information</span></span>       |
| <span data-ttu-id="b71ed-310">2</span><span class="sxs-lookup"><span data-stu-id="b71ed-310">2</span></span>      | <span data-ttu-id="b71ed-311">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-311">Console</span></span>       | <span data-ttu-id="b71ed-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="b71ed-312">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="b71ed-313">Warnung</span><span class="sxs-lookup"><span data-stu-id="b71ed-313">Warning</span></span>           |
| <span data-ttu-id="b71ed-314">3</span><span class="sxs-lookup"><span data-stu-id="b71ed-314">3</span></span>      | <span data-ttu-id="b71ed-315">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-315">Console</span></span>       | <span data-ttu-id="b71ed-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="b71ed-316">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="b71ed-317">Debug</span><span class="sxs-lookup"><span data-stu-id="b71ed-317">Debug</span></span>             |
| <span data-ttu-id="b71ed-318">4</span><span class="sxs-lookup"><span data-stu-id="b71ed-318">4</span></span>      | <span data-ttu-id="b71ed-319">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-319">Console</span></span>       | <span data-ttu-id="b71ed-320">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="b71ed-320">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="b71ed-321">Fehler</span><span class="sxs-lookup"><span data-stu-id="b71ed-321">Error</span></span>             |
| <span data-ttu-id="b71ed-322">5</span><span class="sxs-lookup"><span data-stu-id="b71ed-322">5</span></span>      | <span data-ttu-id="b71ed-323">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-323">Console</span></span>       | <span data-ttu-id="b71ed-324">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="b71ed-324">All categories</span></span>                          | <span data-ttu-id="b71ed-325">Information</span><span class="sxs-lookup"><span data-stu-id="b71ed-325">Information</span></span>       |
| <span data-ttu-id="b71ed-326">6</span><span class="sxs-lookup"><span data-stu-id="b71ed-326">6</span></span>      | <span data-ttu-id="b71ed-327">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-327">All providers</span></span> | <span data-ttu-id="b71ed-328">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="b71ed-328">All categories</span></span>                          | <span data-ttu-id="b71ed-329">Debug</span><span class="sxs-lookup"><span data-stu-id="b71ed-329">Debug</span></span>             |
| <span data-ttu-id="b71ed-330">7</span><span class="sxs-lookup"><span data-stu-id="b71ed-330">7</span></span>      | <span data-ttu-id="b71ed-331">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-331">All providers</span></span> | <span data-ttu-id="b71ed-332">System</span><span class="sxs-lookup"><span data-stu-id="b71ed-332">System</span></span>                                  | <span data-ttu-id="b71ed-333">Debug</span><span class="sxs-lookup"><span data-stu-id="b71ed-333">Debug</span></span>             |
| <span data-ttu-id="b71ed-334">8</span><span class="sxs-lookup"><span data-stu-id="b71ed-334">8</span></span>      | <span data-ttu-id="b71ed-335">Debug</span><span class="sxs-lookup"><span data-stu-id="b71ed-335">Debug</span></span>         | <span data-ttu-id="b71ed-336">Microsoft</span><span class="sxs-lookup"><span data-stu-id="b71ed-336">Microsoft</span></span>                               | <span data-ttu-id="b71ed-337">Ablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="b71ed-337">Trace</span></span>             |

<span data-ttu-id="b71ed-338">Wenn ein `ILogger`-Objekt erstellt wird, wählt das `ILoggerFactory`-Objekt eine einzige Regel pro Anbieter aus, die auf diese Protokollierung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-338">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="b71ed-339">Alle von einer `ILogger`-Instanz geschriebenen Meldungen werden auf Grundlage der ausgewählten Regeln gefiltert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-339">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="b71ed-340">Aus den verfügbaren Regeln wird die für jeden Anbieter und jedes Kategoriepaar spezifischste Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-340">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="b71ed-341">Beim Erstellen von `ILogger` für eine vorgegebene Kategorie wird für jeden Anbieter der folgende Algorithmus verwendet:</span><span class="sxs-lookup"><span data-stu-id="b71ed-341">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="b71ed-342">Es werden alle Regeln ausgewählt, die dem Anbieter oder dem zugehörigen Alias entsprechen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-342">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="b71ed-343">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln mit einem leeren Anbieter ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-343">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="b71ed-344">Aus dem Ergebnis des vorherigen Schritts werden die Regeln mit dem längsten übereinstimmenden Kategoriepräfix ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-344">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="b71ed-345">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln ohne Angabe einer Kategorie ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-345">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="b71ed-346">Wenn mehrere Regeln ausgewählt sind, wird die **letzte** Regel verwendet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-346">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="b71ed-347">Wenn keine Regel ausgewählt ist, wird `MinimumLevel` verwendet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-347">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="b71ed-348">Angenommen, Sie erstellen mit der oben aufgeführten Liste mit Regeln ein `ILogger`-Objekt für die Kategorie „Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine“:</span><span class="sxs-lookup"><span data-stu-id="b71ed-348">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="b71ed-349">Für den Debuganbieter gelten die Regeln 1, 6 und 8.</span><span class="sxs-lookup"><span data-stu-id="b71ed-349">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="b71ed-350">Regel 8 ist die spezifischste Regel, deshalb wird diese Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-350">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="b71ed-351">Für den Konsolenanbieter gelten die Regeln 3, 4, 5 und 6.</span><span class="sxs-lookup"><span data-stu-id="b71ed-351">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="b71ed-352">Regel 3 ist die spezifischste Regel.</span><span class="sxs-lookup"><span data-stu-id="b71ed-352">Rule 3 is most specific.</span></span>

<span data-ttu-id="b71ed-353">Die sich ergebende `ILogger`-Instanz sendet Protokolle mit dem Protokolliergrad `Trace` und höher an den Debuganbieter.</span><span class="sxs-lookup"><span data-stu-id="b71ed-353">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="b71ed-354">Protokolle mit dem Protokolliergrad `Debug` und höher werden an den Konsolenanbieter gesendet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-354">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="b71ed-355">Anbieteraliase</span><span class="sxs-lookup"><span data-stu-id="b71ed-355">Provider aliases</span></span>

<span data-ttu-id="b71ed-356">Jeder Anbieter definiert einen *Alias*, der in der Konfiguration anstelle des vollqualifizierten Typnamens verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="b71ed-356">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="b71ed-357">Verwenden Sie für die integrierten Anbieter die folgenden Aliase:</span><span class="sxs-lookup"><span data-stu-id="b71ed-357">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="b71ed-358">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-358">Console</span></span>
* <span data-ttu-id="b71ed-359">Debug</span><span class="sxs-lookup"><span data-stu-id="b71ed-359">Debug</span></span>
* <span data-ttu-id="b71ed-360">EventSource</span><span class="sxs-lookup"><span data-stu-id="b71ed-360">EventSource</span></span>
* <span data-ttu-id="b71ed-361">EventLog</span><span class="sxs-lookup"><span data-stu-id="b71ed-361">EventLog</span></span>
* <span data-ttu-id="b71ed-362">TraceSource</span><span class="sxs-lookup"><span data-stu-id="b71ed-362">TraceSource</span></span>
* <span data-ttu-id="b71ed-363">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="b71ed-363">AzureAppServicesFile</span></span>
* <span data-ttu-id="b71ed-364">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="b71ed-364">AzureAppServicesBlob</span></span>
* <span data-ttu-id="b71ed-365">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="b71ed-365">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="b71ed-366">Standardmindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="b71ed-366">Default minimum level</span></span>

<span data-ttu-id="b71ed-367">Es gibt eine Einstellung für den Mindestprotokolliergrad, die nur dann wirksam wird, wenn für einen bestimmten Anbieter und eine bestimmte Kategorie keine Regeln aus Konfiguration oder Code gelten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-367">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="b71ed-368">Im folgenden Beispiel wird das Festlegen des Mindestprotokolliergrads veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="b71ed-368">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="b71ed-369">Wenn Sie den Mindestprotokolliergrad nicht explizit festlegen, lautet der Standardwert `Information`, d.h. Protokolle der Ebene `Trace` und `Debug` werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-369">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="b71ed-370">Filterfunktionen</span><span class="sxs-lookup"><span data-stu-id="b71ed-370">Filter functions</span></span>

<span data-ttu-id="b71ed-371">Eine Filterfunktion wird für alle Anbieter und Kategorien aufgerufen, denen keine Regeln durch Konfiguration oder Code zugewiesen sind.</span><span class="sxs-lookup"><span data-stu-id="b71ed-371">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="b71ed-372">Code in der Funktion verfügt über Zugriff auf den Anbietertyp, die Kategorie und den Protokolliergrad.</span><span class="sxs-lookup"><span data-stu-id="b71ed-372">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="b71ed-373">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b71ed-373">For example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="b71ed-374">Systemkategorien und -protokolliergrade</span><span class="sxs-lookup"><span data-stu-id="b71ed-374">System categories and levels</span></span>

<span data-ttu-id="b71ed-375">Dies sind einige Kategorien, die vom ASP.NET Core und Entity Framework Core verwendet werden, mit Hinweisen darauf, welche Protokolle von ihnen zu erwarten sind:</span><span class="sxs-lookup"><span data-stu-id="b71ed-375">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="b71ed-376">Kategorie</span><span class="sxs-lookup"><span data-stu-id="b71ed-376">Category</span></span>                            | <span data-ttu-id="b71ed-377">Hinweise</span><span class="sxs-lookup"><span data-stu-id="b71ed-377">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="b71ed-378">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="b71ed-378">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="b71ed-379">Allgemeine ASP.NET Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="b71ed-379">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="b71ed-380">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="b71ed-380">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="b71ed-381">Gibt an, welche Schlüssel in Betracht gezogen, gefunden und verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-381">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="b71ed-382">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="b71ed-382">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="b71ed-383">Hosts sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="b71ed-383">Hosts allowed.</span></span> |
| <span data-ttu-id="b71ed-384">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="b71ed-384">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="b71ed-385">Gibt an, wie lange es bis zum Abschluss von HTTP-Anforderungen gedauert hat und zu welcher Zeit sie gestartet wurden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-385">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="b71ed-386">Gibt an, welche Hostingstartassemblys geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-386">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="b71ed-387">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="b71ed-387">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="b71ed-388">MVC- und Razor-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="b71ed-388">MVC and Razor diagnostics.</span></span> <span data-ttu-id="b71ed-389">Modellbindung, Filterausführung, Ansichtskompilierung, Aktionsauswahl.</span><span class="sxs-lookup"><span data-stu-id="b71ed-389">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="b71ed-390">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="b71ed-390">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="b71ed-391">Gibt Routenabgleichsinformationen an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-391">Route matching information.</span></span> |
| <span data-ttu-id="b71ed-392">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="b71ed-392">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="b71ed-393">Start-, Beendigungs- und Keep-Alive-Antworten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="b71ed-393">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="b71ed-394">HTTPS-Zertifikatinformationen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-394">HTTPS certificate information.</span></span> |
| <span data-ttu-id="b71ed-395">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="b71ed-395">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="b71ed-396">Die bereitgestellten Dateien.</span><span class="sxs-lookup"><span data-stu-id="b71ed-396">Files served.</span></span> |
| <span data-ttu-id="b71ed-397">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="b71ed-397">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="b71ed-398">Allgemeine Entity Framework Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="b71ed-398">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="b71ed-399">Datenbankaktivität und -konfiguration, Änderungserkennung, Migrationen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-399">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="b71ed-400">Protokollbereiche</span><span class="sxs-lookup"><span data-stu-id="b71ed-400">Log scopes</span></span>

 <span data-ttu-id="b71ed-401">Ein *Bereich* kann einen Satz logischer Vorgänge gruppieren.</span><span class="sxs-lookup"><span data-stu-id="b71ed-401">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="b71ed-402">Diese Gruppierung kann verwendet werden, um an jedes Protokoll, das als Teil einer Gruppe erstellt wird, die gleichen Daten anzufügen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-402">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="b71ed-403">So kann beispielsweise jedes Protokoll, das im Rahmen der Verarbeitung einer Transaktion erstellt wird, die Transaktions-ID enthalten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-403">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="b71ed-404">Ein Bereich ist ein `IDisposable`-Typ, der von der <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>-Methode zurückgegeben und so lange beibehalten wird, bis er verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-404">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="b71ed-405">Verwenden Sie einen Bereich, indem Sie Protokollierungsaufrufe mit einem `using`-Block umschließen, der als Wrapper verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="b71ed-405">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="b71ed-406">Der folgende Code aktiviert Bereiche für den Konsolenanbieter:</span><span class="sxs-lookup"><span data-stu-id="b71ed-406">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="b71ed-407">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b71ed-407">*Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

> [!NOTE]
> <span data-ttu-id="b71ed-408">Um die bereichsbasierte Protokollierung zu aktivieren, muss die Konsolenprotokollierungsoption `IncludeScopes` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-408">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="b71ed-409">Weitere Informationen zur Konfiguration finden Sie im Abschnitt [Konfiguration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="b71ed-409">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="b71ed-410">Jede Protokollmeldung enthält die bereichsbezogenen Informationen:</span><span class="sxs-lookup"><span data-stu-id="b71ed-410">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="b71ed-411">Integrierte Protokollierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-411">Built-in logging providers</span></span>

<span data-ttu-id="b71ed-412">ASP.NET Core wird mit den folgenden Anbietern bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="b71ed-412">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="b71ed-413">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-413">Console</span></span>](#console-provider)
* [<span data-ttu-id="b71ed-414">Debuggen</span><span class="sxs-lookup"><span data-stu-id="b71ed-414">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="b71ed-415">EventSource</span><span class="sxs-lookup"><span data-stu-id="b71ed-415">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="b71ed-416">EventLog</span><span class="sxs-lookup"><span data-stu-id="b71ed-416">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="b71ed-417">TraceSource</span><span class="sxs-lookup"><span data-stu-id="b71ed-417">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="b71ed-418">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="b71ed-418">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="b71ed-419">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="b71ed-419">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="b71ed-420">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="b71ed-420">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="b71ed-421">Weitere Informationen zu „stdout“ und zur Debugprotokollierung mit dem ASP.NET Core-Modul finden Sie unter <xref:test/troubleshoot-azure-iis> und <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="b71ed-421">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="b71ed-422">Der Konsolenanbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-422">Console provider</span></span>

<span data-ttu-id="b71ed-423">Das Anbieterpaket [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sendet eine Protokollausgabe an die Konsole.</span><span class="sxs-lookup"><span data-stu-id="b71ed-423">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="b71ed-424">Um die Ausgabe der Konsolenprotokollierung anzuzeigen, öffnen Sie eine Eingabeaufforderung im Projektordner und führen den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="b71ed-424">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="b71ed-425">Der Debuganbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-425">Debug provider</span></span>

<span data-ttu-id="b71ed-426">Beim Anbieterpaket [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) erfolgt die Protokollausgabe unter Verwendung der Klasse [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine`-Methodenaufrufe).</span><span class="sxs-lookup"><span data-stu-id="b71ed-426">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="b71ed-427">Unter Linux werden Protokolle dieses Anbieters in */var/log/message* geschrieben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-427">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="b71ed-428">Ereignisquellenanbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-428">Event Source provider</span></span>

<span data-ttu-id="b71ed-429">Das [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource)-Anbieterpaket schreibt in eine plattformübergreifende Ereignisquelle mit dem Namen `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="b71ed-429">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="b71ed-430">Unter Windows verwendet der Anbieter die [Ereignisablaufverfolgung für Windows (ETW)](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="b71ed-430">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="b71ed-431">Der Ereignisquellenanbieter wird automatisch hinzugefügt, wenn `CreateDefaultBuilder` zum Erstellen des Hosts aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-431">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="b71ed-432">dotnet-trace-Tool</span><span class="sxs-lookup"><span data-stu-id="b71ed-432">dotnet trace tooling</span></span>

<span data-ttu-id="b71ed-433">Das Tool [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) ist ein plattformübergreifendes globales Befehlszeilenschnittstellentool zum Sammeln von .NET Core-Ablaufverfolgungen eines ausgeführten Prozesses.</span><span class="sxs-lookup"><span data-stu-id="b71ed-433">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="b71ed-434">Das Tool sammelt <xref:Microsoft.Extensions.Logging.EventSource>-Anbieterdaten mithilfe einer <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="b71ed-434">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="b71ed-435">Installieren Sie das dotnet-trace-Tool mit dem folgenden Befehl:</span><span class="sxs-lookup"><span data-stu-id="b71ed-435">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="b71ed-436">Verwenden Sie das dotnet-trace-Tool, um die Ablaufverfolgung aus einer App zu erfassen:</span><span class="sxs-lookup"><span data-stu-id="b71ed-436">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="b71ed-437">Wenn die App den Host nicht mit `CreateDefaultBuilder` erstellt, fügen Sie den [Ereignisquellenanbieter](#event-source-provider) zur Protokollierungskonfiguration der App hinzu.</span><span class="sxs-lookup"><span data-stu-id="b71ed-437">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="b71ed-438">Führen Sie die App mit dem Befehl `dotnet run` aus.</span><span class="sxs-lookup"><span data-stu-id="b71ed-438">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="b71ed-439">Bestimmen Sie den Prozessbezeichner der .NET Core-App:</span><span class="sxs-lookup"><span data-stu-id="b71ed-439">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="b71ed-440">Unter Windows können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="b71ed-440">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="b71ed-441">Task-Manager (STRG+ALT+ENTF)</span><span class="sxs-lookup"><span data-stu-id="b71ed-441">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="b71ed-442">tasklist-Befehl</span><span class="sxs-lookup"><span data-stu-id="b71ed-442">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="b71ed-443">PowerShell-Befehl „Get-Process“</span><span class="sxs-lookup"><span data-stu-id="b71ed-443">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="b71ed-444">Verwenden Sie unter Linux den [pidof-Befehl](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="b71ed-444">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="b71ed-445">Suchen Sie den Prozessbezeichner für den Prozess, der den gleichen Namen wie die App-Assembly hat.</span><span class="sxs-lookup"><span data-stu-id="b71ed-445">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="b71ed-446">Führen Sie den `dotnet trace`-Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="b71ed-446">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="b71ed-447">Allgemeine Befehlssyntax:</span><span class="sxs-lookup"><span data-stu-id="b71ed-447">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="b71ed-448">Wenn Sie eine PowerShell-Befehlsshell verwenden, schließen Sie den `--providers`-Wert in einfache Anführungszeichen (`'`) ein:</span><span class="sxs-lookup"><span data-stu-id="b71ed-448">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="b71ed-449">Auf Plattformen, die nicht unter Windows ausgeführt werden, fügen Sie die `-f speedscope`-Option hinzu, um das Format der Ablaufverfolgungsdatei der Ausgabe in `speedscope` zu ändern.</span><span class="sxs-lookup"><span data-stu-id="b71ed-449">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="b71ed-450">Stichwort</span><span class="sxs-lookup"><span data-stu-id="b71ed-450">Keyword</span></span> | <span data-ttu-id="b71ed-451">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b71ed-451">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="b71ed-452">1</span><span class="sxs-lookup"><span data-stu-id="b71ed-452">1</span></span>       | <span data-ttu-id="b71ed-453">Protokolliert Metaereignisse über die `LoggingEventSource`.</span><span class="sxs-lookup"><span data-stu-id="b71ed-453">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="b71ed-454">Es werden keine Ereignisse von `ILogger`) protokolliert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-454">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="b71ed-455">2</span><span class="sxs-lookup"><span data-stu-id="b71ed-455">2</span></span>       | <span data-ttu-id="b71ed-456">Aktiviert das `Message`-Ereignis, wenn `ILogger.Log()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-456">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="b71ed-457">Die Informationen werden in einer programmgesteuerten (nicht formatierten) Weise ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-457">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="b71ed-458">4</span><span class="sxs-lookup"><span data-stu-id="b71ed-458">4</span></span>       | <span data-ttu-id="b71ed-459">Aktiviert das `FormatMessage`-Ereignis, wenn `ILogger.Log()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-459">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="b71ed-460">Gibt die formatierte Zeichenfolgeversion der Informationen an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-460">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="b71ed-461">8</span><span class="sxs-lookup"><span data-stu-id="b71ed-461">8</span></span>       | <span data-ttu-id="b71ed-462">Aktiviert das `MessageJson`-Ereignis, wenn `ILogger.Log()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-462">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="b71ed-463">Stellt eine JSON-Darstellung der Argumente bereit.</span><span class="sxs-lookup"><span data-stu-id="b71ed-463">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="b71ed-464">Ereignisgrad</span><span class="sxs-lookup"><span data-stu-id="b71ed-464">Event Level</span></span> | <span data-ttu-id="b71ed-465">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="b71ed-465">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="b71ed-466">0</span><span class="sxs-lookup"><span data-stu-id="b71ed-466">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="b71ed-467">1</span><span class="sxs-lookup"><span data-stu-id="b71ed-467">1</span></span>           | `Critical`      |
   | <span data-ttu-id="b71ed-468">2</span><span class="sxs-lookup"><span data-stu-id="b71ed-468">2</span></span>           | `Error`         |
   | <span data-ttu-id="b71ed-469">3</span><span class="sxs-lookup"><span data-stu-id="b71ed-469">3</span></span>           | `Warning`       |
   | <span data-ttu-id="b71ed-470">4</span><span class="sxs-lookup"><span data-stu-id="b71ed-470">4</span></span>           | `Informational` |
   | <span data-ttu-id="b71ed-471">5</span><span class="sxs-lookup"><span data-stu-id="b71ed-471">5</span></span>           | `Verbose`       |

   <span data-ttu-id="b71ed-472">`FilterSpecs`-Einträge für `{Logger Category}` und `{Event Level}` stellen zusätzliche Protokollfilterbedingungen dar.</span><span class="sxs-lookup"><span data-stu-id="b71ed-472">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="b71ed-473">Trennen Sie die `FilterSpecs`-Einträge durch ein Semikolon (`;`).</span><span class="sxs-lookup"><span data-stu-id="b71ed-473">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="b71ed-474">Beispiel mit einer Windows-Befehlsshell (**keine** einfachen Anführungszeichen um den `--providers`-Wert):</span><span class="sxs-lookup"><span data-stu-id="b71ed-474">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="b71ed-475">Mit dem vorangestellten Komma wird Folgendes aktiviert:</span><span class="sxs-lookup"><span data-stu-id="b71ed-475">The preceding command activates:</span></span>

   * <span data-ttu-id="b71ed-476">Die Ereignisquellenprotokollierung zur Erzeugung von formatierten Zeichenfolgen (`4`) für Fehler (`2`)</span><span class="sxs-lookup"><span data-stu-id="b71ed-476">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="b71ed-477">`Microsoft.AspNetCore.Hosting`-Protokollierung auf `Informational`-Protokollierungsebene (`4`)</span><span class="sxs-lookup"><span data-stu-id="b71ed-477">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="b71ed-478">Halten Sie das dotnet-trace-Tool an, indem Sie die EINGABETASTE oder STRG+C drücken.</span><span class="sxs-lookup"><span data-stu-id="b71ed-478">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="b71ed-479">Die Ablaufverfolgung wird mit dem Namen *trace.nettrace* in dem Ordner gespeichert, in dem der `dotnet trace`-Befehl ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-479">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="b71ed-480">Öffnen Sie die Ablaufverfolgung mit [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="b71ed-480">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="b71ed-481">Öffnen Sie die Datei *trace.nettrace*, und untersuchen Sie die Ablaufverfolgungsereignisse.</span><span class="sxs-lookup"><span data-stu-id="b71ed-481">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="b71ed-482">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="b71ed-482">For more information, see:</span></span>

* <span data-ttu-id="b71ed-483">[Ablaufverfolgung für das Hilfsprogramm zur Leistungsanalyse (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="b71ed-483">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="b71ed-484">[Ablaufverfolgung für das Hilfsprogramm zur Leistungsanalyse (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (GitHub-Repository-Dokumentation zu dotnet/diagnostics)</span><span class="sxs-lookup"><span data-stu-id="b71ed-484">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="b71ed-485">[LoggingEventSource-Klasse](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API-Browser)</span><span class="sxs-lookup"><span data-stu-id="b71ed-485">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="b71ed-486">[LoggingEventSource-Verweisquelle (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs)&ndash; Ändern Sie für das Abrufen der Verweisquelle für eine andere Version den Branch in `release/{Version}`, wobei `{Version}` die Version der gewünschten ASP.NET Core-Version darstellt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-486">[LoggingEventSource reference source (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="b71ed-487">[Perfview](#perfview) ist hilfreich zum Anzeigen der Ablaufverfolgung für Ereignisquellen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-487">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="b71ed-488">PerfView</span><span class="sxs-lookup"><span data-stu-id="b71ed-488">Perfview</span></span>

<span data-ttu-id="b71ed-489">Verwenden Sie das [PerfView-Hilfsprogramm](https://github.com/Microsoft/perfview) zum Sammeln und Anzeigen von Protokollen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-489">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="b71ed-490">Es gibt andere Tools zur Anzeige von ETW-Protokollen, aber PerfView bietet die besten Ergebnisse bei der Arbeit mit ETW-Ereignissen, die von ASP.NET Core ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-490">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="b71ed-491">Um PerfView für das Erfassen von Ereignissen zu konfigurieren, die von diesem Anbieter protokolliert wurden, fügen Sie die Zeichenfolge `*Microsoft-Extensions-Logging` zur Liste **Zusätzliche Anbieter** hinzu.</span><span class="sxs-lookup"><span data-stu-id="b71ed-491">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="b71ed-492">(Vergessen Sie nicht das Sternchen am Anfang der Zeichenfolge.)</span><span class="sxs-lookup"><span data-stu-id="b71ed-492">(Don't miss the asterisk at the start of the string.)</span></span>

![Zusätzliche PerfView-Anbieter](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="b71ed-494">Der Windows-EventLog-Anbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-494">Windows EventLog provider</span></span>

<span data-ttu-id="b71ed-495">Das Anbieterpaket [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sendet eine Protokollausgabe in das Windows-Ereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="b71ed-495">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="b71ed-496">Mithilfe von [AddEventLog-Überladungen](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) können Sie <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> übergeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-496">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="b71ed-497">Wenn `null` oder nicht angegeben, werden die folgenden Standardeinstellungen verwendet:</span><span class="sxs-lookup"><span data-stu-id="b71ed-497">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="b71ed-498">`LogName` „Anwendung“</span><span class="sxs-lookup"><span data-stu-id="b71ed-498">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="b71ed-499">`SourceName` „.NET Runtime“</span><span class="sxs-lookup"><span data-stu-id="b71ed-499">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="b71ed-500">`MachineName` Lokaler Computer</span><span class="sxs-lookup"><span data-stu-id="b71ed-500">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="b71ed-501">Ereignisse werden für die [Warnstufe und höher](#log-level) protokolliert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-501">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="b71ed-502">Legen Sie die Protokollierungsebene fest, um Ereignisse, die niedriger als `Warning` sind, zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="b71ed-502">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="b71ed-503">Fügen Sie beispielsweise Folgendes zur *appsettings.json*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="b71ed-503">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="b71ed-504">Der TraceSource-Anbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-504">TraceSource provider</span></span>

<span data-ttu-id="b71ed-505">Das Anbieterpaket [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) verwendet die <xref:System.Diagnostics.TraceSource>-Bibliotheken und -Anbieter.</span><span class="sxs-lookup"><span data-stu-id="b71ed-505">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="b71ed-506">Mithilfe von [AddTraceSource-Überladungen](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) können Sie eine Quelloption und einen Listener für die Ablaufverfolgung übergeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-506">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="b71ed-507">Um diesen Anbieter zu verwenden, muss eine App unter .NET Framework (anstelle von .NET Core) ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-507">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="b71ed-508">Der Anbieter kann Nachrichten an eine Vielzahl von [Listenern](/dotnet/framework/debug-trace-profile/trace-listeners) weiterleiten, z.B. an den in der Beispiel-App verwendeten <xref:System.Diagnostics.TextWriterTraceListener>.</span><span class="sxs-lookup"><span data-stu-id="b71ed-508">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="b71ed-509">Der Azure App Service-Anbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-509">Azure App Service provider</span></span>

<span data-ttu-id="b71ed-510">Das Anbieterpaket [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) schreibt Protokolle in Textdateien in das Dateisystem einer Azure App Service-App und in [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in einem Azure Storage-Konto.</span><span class="sxs-lookup"><span data-stu-id="b71ed-510">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="b71ed-511">Das Anbieterpaket ist nicht im freigegebenen Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-511">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="b71ed-512">Zum Verwenden des Anbieters müssen Sie das Anbieterpaket dem Projekt hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-512">To use the provider, add the provider package to the project.</span></span>

<span data-ttu-id="b71ed-513">Verwenden Sie <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> und <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> wie im folgenden Beispiel, um die Anbietereinstellungen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="b71ed-513">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

<span data-ttu-id="b71ed-514">Wenn Sie eine Bereitstellung in einer App Service-App durchführen, berücksichtigt die Anwendung die Einstellungen im Abschnitt [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) (App Service-Protokolle) auf der Seite **App Services** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="b71ed-514">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="b71ed-515">Bei einem Update der folgenden Einstellungen werden die Änderungen sofort wirksam, ohne dass ein Neustart oder eine erneute Bereitstellung der App notwendig ist.</span><span class="sxs-lookup"><span data-stu-id="b71ed-515">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="b71ed-516">**Anwendungsprotokoll (Dateisystem)**</span><span class="sxs-lookup"><span data-stu-id="b71ed-516">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="b71ed-517">**Anwendungsprotokoll (Blob)**</span><span class="sxs-lookup"><span data-stu-id="b71ed-517">**Application Logging (Blob)**</span></span>

<span data-ttu-id="b71ed-518">Der Standardspeicherort für Protokolldateien ist der Ordner *D:\\home\\LogFiles\\Application*, und der standardmäßige Dateiname lautet *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="b71ed-518">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="b71ed-519">Die Dateigröße ist standardmäßig auf 10 MB beschränkt, und die maximal zulässige Anzahl beibehaltener Dateien lautet 2.</span><span class="sxs-lookup"><span data-stu-id="b71ed-519">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="b71ed-520">Der Standardblobname lautet *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="b71ed-520">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="b71ed-521">Der Anbieter funktioniert nur, wenn das Projekt in der Azure-Umgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-521">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="b71ed-522">Bei einer lokalen Ausführung zeigt er keine Auswirkungen. Der Anbieter schreibt keine Protokolle in lokale Dateien oder den lokalen Entwicklungsspeicher für BLOBs.</span><span class="sxs-lookup"><span data-stu-id="b71ed-522">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="b71ed-523">Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="b71ed-523">Azure log streaming</span></span>

<span data-ttu-id="b71ed-524">Azure-Protokollstreaming ermöglicht Ihnen eine Echtzeitanzeige der Protokollaktivität für:</span><span class="sxs-lookup"><span data-stu-id="b71ed-524">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="b71ed-525">App-Server</span><span class="sxs-lookup"><span data-stu-id="b71ed-525">The app server</span></span>
* <span data-ttu-id="b71ed-526">Webserver</span><span class="sxs-lookup"><span data-stu-id="b71ed-526">The web server</span></span>
* <span data-ttu-id="b71ed-527">Ablaufverfolgung für Anforderungsfehler</span><span class="sxs-lookup"><span data-stu-id="b71ed-527">Failed request tracing</span></span>

<span data-ttu-id="b71ed-528">So konfigurieren Sie das Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="b71ed-528">To configure Azure log streaming:</span></span>

* <span data-ttu-id="b71ed-529">Navigieren Sie von der Portalseite Ihrer App zur Seite **App Service-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="b71ed-529">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="b71ed-530">Legen Sie **Anwendungsprotokollierung (Dateisystem)** auf **Ein** fest.</span><span class="sxs-lookup"><span data-stu-id="b71ed-530">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="b71ed-531">Wählen Sie die **Protokollierungsebene**.</span><span class="sxs-lookup"><span data-stu-id="b71ed-531">Choose the log **Level**.</span></span> <span data-ttu-id="b71ed-532">Diese Einstellung gilt nur für das Azure-Protokollstreaming, nicht für andere Protokollierungsanbieter in der App.</span><span class="sxs-lookup"><span data-stu-id="b71ed-532">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="b71ed-533">Navigieren Sie zur Seite **Log Stream** (Protokollstream), um App-Meldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-533">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="b71ed-534">Diese werden von der App über die `ILogger`-Schnittstelle protokolliert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-534">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="b71ed-535">Ablaufverfolgungsprotokollierung für Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="b71ed-535">Azure Application Insights trace logging</span></span>

<span data-ttu-id="b71ed-536">Das [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)-Anbieterpaket schreibt Protokolle in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="b71ed-536">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="b71ed-537">Application Insights ist ein Dienst, der eine Web-App überwacht und Tools für Abfragen und Analysen von Telemetriedaten zur Verfügung stellt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-537">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="b71ed-538">Wenn Sie diesen Anbieter verwenden, können Sie Ihre Protokolle mithilfe der Application Insights-Tools abfragen und analysieren.</span><span class="sxs-lookup"><span data-stu-id="b71ed-538">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="b71ed-539">Der Protokollierungsanbieter ist als Abhängigkeit von [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) enthalten, d.h. als das Paket, das alle verfügbaren Telemetriedaten für ASP.NET Core bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-539">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="b71ed-540">Wenn Sie dieses Paket verwenden, ist nicht erforderlich, das Anbieterpaket zu installieren.</span><span class="sxs-lookup"><span data-stu-id="b71ed-540">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="b71ed-541">Verwenden Sie nicht das [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)-Paket, das für ASP.NET 4.x bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="b71ed-541">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="b71ed-542">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="b71ed-542">For more information, see the following resources:</span></span>

* [<span data-ttu-id="b71ed-543">Application Insights-Übersicht</span><span class="sxs-lookup"><span data-stu-id="b71ed-543">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="b71ed-544">[Anwendungseinblicke für ASP.NET Core-Anwendungen](/azure/azure-monitor/app/asp-net-core): Beginnen Sie hier, wenn Sie die gesamte Bandbreite der Application Insights-Telemetrie zusammen mit der Protokollierung implementieren möchten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-544">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="b71ed-545">[ApplicationInsightsLoggerProvider für .NET Core ILogger-Protokolle](/azure/azure-monitor/app/ilogger): Beginnen Sie hier, wenn Sie den Protokollierungsanbieter ohne die übrige Application Insights-Telemetrie implementieren möchten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-545">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="b71ed-546">[Application Insights logging adapters (Adapter zur Protokollierung für Application Insights)](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="b71ed-546">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="b71ed-547">[Installieren, Konfigurieren und Initialisieren des Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): interaktive Tutorials auf der Microsoft Learn-Website.</span><span class="sxs-lookup"><span data-stu-id="b71ed-547">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="b71ed-548">Protokollierungsanbieter von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="b71ed-548">Third-party logging providers</span></span>

<span data-ttu-id="b71ed-549">Protokollierungsframeworks von Drittanbietern aufgeführt, die mit ASP.NET Core funktionieren:</span><span class="sxs-lookup"><span data-stu-id="b71ed-549">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="b71ed-550">[elmah.io](https://elmah.io/) ([GitHub-Repository](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="b71ed-550">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="b71ed-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub-Repository](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="b71ed-551">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="b71ed-552">[JSNLog](https://jsnlog.com/) ([GitHub-Repository](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="b71ed-552">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="b71ed-553">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="b71ed-553">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="b71ed-554">[Log4Net](https://logging.apache.org/log4net/) ([GitHub-Repository](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="b71ed-554">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="b71ed-555">[Loggr](https://loggr.net/) ([GitHub-Repository](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="b71ed-555">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="b71ed-556">[NLog](https://nlog-project.org/) ([GitHub-Repository](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="b71ed-556">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="b71ed-557">[Sentry](https://sentry.io/welcome/) ([GitHub-Repository](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="b71ed-557">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="b71ed-558">[Serilog](https://serilog.net/) ([GitHub-Repository](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="b71ed-558">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="b71ed-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub-Repository](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="b71ed-559">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="b71ed-560">Einige Drittanbieterframeworks können eine [semantische Protokollierung (auch als strukturierte Protokollierung bezeichnet)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) ausführen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-560">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="b71ed-561">Die Verwendung eines Frameworks von Drittanbietern ist ähnlich wie die Verwendung eines integrierten Anbieters:</span><span class="sxs-lookup"><span data-stu-id="b71ed-561">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="b71ed-562">Fügen Sie Ihrem Paket ein NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="b71ed-562">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="b71ed-563">Rufen Sie eine `ILoggerFactory`-Erweiterungsmethode auf, die vom Protokollierungsframework bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-563">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="b71ed-564">Weitere Informationen finden Sie in der Dokumentation zum jeweiligen Anbieter.</span><span class="sxs-lookup"><span data-stu-id="b71ed-564">For more information, see each provider's documentation.</span></span> <span data-ttu-id="b71ed-565">Protokollierungsanbieter von Drittanbietern werden von Microsoft nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-565">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b71ed-566">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b71ed-566">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b71ed-567">Von [Tom Dykstra](https://github.com/tdykstra) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b71ed-567">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b71ed-568">.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-568">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="b71ed-569">Dieser Artikel zeigt, wie Sie die Protokollierungs-API mit integrierten Anbietern verwenden können.</span><span class="sxs-lookup"><span data-stu-id="b71ed-569">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="b71ed-570">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b71ed-570">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="b71ed-571">Hinzufügen von Anbietern</span><span class="sxs-lookup"><span data-stu-id="b71ed-571">Add providers</span></span>

<span data-ttu-id="b71ed-572">Ein Protokollierungsanbieter zeigt Protokolle an oder speichert diese.</span><span class="sxs-lookup"><span data-stu-id="b71ed-572">A logging provider displays or stores logs.</span></span> <span data-ttu-id="b71ed-573">Beispielsweise zeigt der Konsolenanbieter Protokolle in der Konsole an, und der Azure Application Insights-Anbieter speichert sie in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="b71ed-573">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="b71ed-574">Protokolle können durch das Hinzufügen mehrerer Anbieter an mehrere Ziele gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-574">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

<span data-ttu-id="b71ed-575">Um einen Anbieter hinzuzufügen, rufen Sie die `Add{provider name}`-Erweiterungsmethode des Anbieters in *Program.cs* auf:</span><span class="sxs-lookup"><span data-stu-id="b71ed-575">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="b71ed-576">Der vorstehende Code erfordert Verweise auf `Microsoft.Extensions.Logging` und `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="b71ed-576">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="b71ed-577">Die Standardprojektvorlage ruft die Methode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> auf, die die folgenden Protokollierungsanbieter hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="b71ed-577">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="b71ed-578">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-578">Console</span></span>
* <span data-ttu-id="b71ed-579">Debug</span><span class="sxs-lookup"><span data-stu-id="b71ed-579">Debug</span></span>
* <span data-ttu-id="b71ed-580">EventSource (beginnend mit ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="b71ed-580">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="b71ed-581">Bei Verwendung von `CreateDefaultBuilder` können Sie die Standardanbieter durch Ihre eigene Auswahl ersetzen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-581">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="b71ed-582">Rufen Sie <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> auf, und fügen Sie die gewünschten Anbieter hinzu.</span><span class="sxs-lookup"><span data-stu-id="b71ed-582">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

<span data-ttu-id="b71ed-583">Informationen zu den [integrierten Protokollierungsanbietern](#built-in-logging-providers) sowie zu [Protokollierungsanbietern von Drittanbietern](#third-party-logging-providers) finden Sie weiter unten in diesem Artikel.</span><span class="sxs-lookup"><span data-stu-id="b71ed-583">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="b71ed-584">Erstellen von Protokollen</span><span class="sxs-lookup"><span data-stu-id="b71ed-584">Create logs</span></span>

<span data-ttu-id="b71ed-585">Verwenden Sie zum Erstellen von Protokollen ein <xref:Microsoft.Extensions.Logging.ILogger%601>-Objekt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-585">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="b71ed-586">Rufen Sie in einer Web-App oder einem gehosteten Dienst einen `ILogger` aus der Abhängigkeitsinjektion ab.</span><span class="sxs-lookup"><span data-stu-id="b71ed-586">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="b71ed-587">Verwenden Sie in Konsolen-Apps ohne Host `LoggerFactory`, um einen `ILogger` zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-587">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="b71ed-588">Im folgenden ASP.NET Core-Beispiel wird eine Protokollierung mit `TodoApiSample.Pages.AboutModel` als Kategorie erstellt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-588">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="b71ed-589">Die Protokoll*kategorie* ist eine Zeichenfolge, die jedem Protokoll zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="b71ed-589">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="b71ed-590">Die von der Abhängigkeitsinjektion bereitgestellte `ILogger<T>`-Instanz erstellt Protokolle, die den vollqualifizierten Namen vom Typ `T` als Kategorie aufweisen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-590">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="b71ed-591">In den folgenden Beispielen zu ASP.NET Core und einer Konsolen-App werden von der Protokollierung Protokolle mit dem Protokolliergrad `Information` erstellt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-591">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="b71ed-592">Der Protokollierungs*grad* gibt den Schweregrad des protokollierten Ereignisses an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-592">The Log *level* indicates the severity of the logged event.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

<span data-ttu-id="b71ed-593">[Grade](#log-level) und [Kategorien](#log-category) werden weiter unten in diesem Artikel ausführlicher erläutert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-593">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span>

### <a name="create-logs-in-startup"></a><span data-ttu-id="b71ed-594">Erstellen von Protokollen in der Startklasse</span><span class="sxs-lookup"><span data-stu-id="b71ed-594">Create logs in Startup</span></span>

<span data-ttu-id="b71ed-595">Zum Schreiben von Protokollen in der `Startup`-Klasse schließen Sie einen `ILogger`-Parameter in die Konstruktorsignatur ein:</span><span class="sxs-lookup"><span data-stu-id="b71ed-595">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="b71ed-596">Erstellen von Protokollen in der Program-Klasse</span><span class="sxs-lookup"><span data-stu-id="b71ed-596">Create logs in the Program class</span></span>

<span data-ttu-id="b71ed-597">Zum Schreiben von Protokollen in der `Program`-Klasse rufen Sie eine `ILogger`-Instanz aus DI ab:</span><span class="sxs-lookup"><span data-stu-id="b71ed-597">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="b71ed-598">Die Protokollierung während der Hosterstellung wird nicht direkt unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-598">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="b71ed-599">Es kann jedoch eine separate Protokollierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-599">However, a separate logger can be used.</span></span> <span data-ttu-id="b71ed-600">Im folgenden Beispiel wird eine [Serilog](https://serilog.net/)-Protokollierung zum Anmelden von `CreateWebHostBuilder` verwendet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-600">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="b71ed-601">`AddSerilog` verwendet die in `Log.Logger` angegebene statische Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="b71ed-601">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="b71ed-602">Keine asynchronen Protokollierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="b71ed-602">No asynchronous logger methods</span></span>

<span data-ttu-id="b71ed-603">Die Protokollierung sollte so schnell erfolgen, dass sich die Leistungskosten von asynchronem Code nicht lohnen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-603">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="b71ed-604">Wenn Ihr Protokollierungsdatenspeicher langsam ist, schreiben Sie nicht direkt in ihn.</span><span class="sxs-lookup"><span data-stu-id="b71ed-604">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="b71ed-605">Erwägen Sie, die Protokollnachrichten zunächst in einen schnellen Speicher zu schreiben und sie dann später in den langsamen Speicher zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-605">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="b71ed-606">Wenn Sie beispielsweise in SQL Server protokollieren, möchten Sie dies nicht direkt in einer `Log`-Methode tun, da die `Log`-Methoden synchron sind.</span><span class="sxs-lookup"><span data-stu-id="b71ed-606">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="b71ed-607">Fügen Sie stattdessen die Protokollmeldungen synchron zu einer Warteschlange im Arbeitsspeicher hinzu, und rufen Sie mithilfe eines Hintergrundworkers die Nachrichten aus der Warteschlange ab, um sie dann asynchron per Pushvorgang an SQL Server zu übertragen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-607">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span> <span data-ttu-id="b71ed-608">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/11801).</span><span class="sxs-lookup"><span data-stu-id="b71ed-608">For more information, see [this](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub issue.</span></span>

## <a name="configuration"></a><span data-ttu-id="b71ed-609">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b71ed-609">Configuration</span></span>

<span data-ttu-id="b71ed-610">Die Konfiguration von Protokollierungsanbietern erfolgt durch mindestens einen Konfigurationsanbieter:</span><span class="sxs-lookup"><span data-stu-id="b71ed-610">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="b71ed-611">Dateiformate (INI, JSON und XML)</span><span class="sxs-lookup"><span data-stu-id="b71ed-611">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="b71ed-612">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="b71ed-612">Command-line arguments.</span></span>
* <span data-ttu-id="b71ed-613">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-613">Environment variables.</span></span>
* <span data-ttu-id="b71ed-614">Speicherinterne .NET-Objekte</span><span class="sxs-lookup"><span data-stu-id="b71ed-614">In-memory .NET objects.</span></span>
* <span data-ttu-id="b71ed-615">Der unverschlüsselte Speicher von [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b71ed-615">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="b71ed-616">Ein unverschlüsselter Benutzerspeicher, z.B. [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="b71ed-616">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="b71ed-617">Benutzerdefinierte Anbieter (installiert oder erstellt).</span><span class="sxs-lookup"><span data-stu-id="b71ed-617">Custom providers (installed or created).</span></span>

<span data-ttu-id="b71ed-618">Die Konfiguration der Protokollierung wird meist vom `Logging`-Abschnitt von Anwendungseinstellungsdateien angegeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-618">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="b71ed-619">Im folgenden Beispiel werden die Inhalte einer herkömmlichen *appsettings.Development.json*-Datei veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="b71ed-619">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="b71ed-620">Die `Logging`-Eigenschaft kann den `LogLevel` und Protokollanbietereigenschaften beinhalten (Konsole wird angezeigt).</span><span class="sxs-lookup"><span data-stu-id="b71ed-620">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="b71ed-621">Die `LogLevel`-Eigenschaft unter `Logging` gibt den Mindest[grad](#log-level) an, der für ausgewählte Kategorien protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="b71ed-621">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="b71ed-622">Im Beispiel protokollieren die Kategorien `System` und `Microsoft` mit dem Grad `Information` und alle anderen Kategorien mit dem Grad `Debug`.</span><span class="sxs-lookup"><span data-stu-id="b71ed-622">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="b71ed-623">Weitere Eigenschaften unter `Logging` geben Protokollierungsanbieter an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-623">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="b71ed-624">Das Beispiel bezieht sich auf den Konsolenanbieter.</span><span class="sxs-lookup"><span data-stu-id="b71ed-624">The example is for the Console provider.</span></span> <span data-ttu-id="b71ed-625">Wenn ein Anbieter [Protokollbereiche](#log-scopes) unterstützt, gibt `IncludeScopes` an, ob sie aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="b71ed-625">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="b71ed-626">Eine Anbietereigenschaft (z.B. `Console` im Beispiel) kann auch eine `LogLevel`-Eigenschaft angeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-626">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="b71ed-627">`LogLevel` unter einem Anbieter gibt die Grade an, die für diesen Anbieter protokolliert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-627">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="b71ed-628">Wenn Grade in `Logging.{providername}.LogLevel` angegeben werden, überschreiben sie alle Angaben in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="b71ed-628">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="b71ed-629">Die Protokollierungs-API umfasst kein Szenario zum Ändern der Protokollebene, während eine App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-629">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="b71ed-630">Einige Konfigurationsanbieter können jedoch die Konfiguration erneut laden, was sich unmittelbar auf die Protokollierungskonfiguration auswirkt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-630">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="b71ed-631">Beispielsweise lädt der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider), der durch `CreateDefaultBuilder` zum Lesen von Einstellungsdateien hinzugefügt wird, die Protokollierungskonfiguration standardmäßig erneut.</span><span class="sxs-lookup"><span data-stu-id="b71ed-631">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="b71ed-632">Wenn die Konfiguration im Code geändert wird, während eine App ausgeführt wird, kann die App [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) aufrufen, um ihre Protokollierungskonfiguration zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b71ed-632">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="b71ed-633">Informationen zur Implementierung von Konfigurationsanbieter finden Sie hier: <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b71ed-633">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="b71ed-634">Beispiel einer Protokollierungsausgabe</span><span class="sxs-lookup"><span data-stu-id="b71ed-634">Sample logging output</span></span>

<span data-ttu-id="b71ed-635">Mit dem im vorherigen Abschnitt gezeigten Beispielcode werden Protokolle in der Konsole angezeigt, wenn die Anwendung über die Befehlszeile ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-635">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="b71ed-636">Hier ein Beispiel für eine Konsolenausgabe:</span><span class="sxs-lookup"><span data-stu-id="b71ed-636">Here's an example of console output:</span></span>

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

<span data-ttu-id="b71ed-637">Die vorherigen Protokolle wurden generiert, indem eine HTTP Get-Anforderung an die Beispiel-App unter `http://localhost:5000/api/todo/0` vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="b71ed-637">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="b71ed-638">Nachfolgend sehen Sie, wie die gleichen Protokolle im Debugfenster angezeigt werden, wenn Sie die Beispiel-App in Visual Studio ausführen:</span><span class="sxs-lookup"><span data-stu-id="b71ed-638">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="b71ed-639">Die über die `ILogger`-Aufrufe aus dem vorherigen Abschnitt erstellten Protokolle beginnen mit „TodoApi“.</span><span class="sxs-lookup"><span data-stu-id="b71ed-639">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="b71ed-640">Protokolle, die mit Microsoft-Kategorien beginnen, stammen aus ASP.NET Core-Frameworkcode.</span><span class="sxs-lookup"><span data-stu-id="b71ed-640">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="b71ed-641">ASP.NET Core und Anwendungscode verwenden dieselbe Protokollierungs-API und dieselben Protokollierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="b71ed-641">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="b71ed-642">In den übrigen Abschnitten dieses Artikels werden einige Details und Optionen für die Protokollierung erläutert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-642">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="b71ed-643">NuGet-Pakete</span><span class="sxs-lookup"><span data-stu-id="b71ed-643">NuGet packages</span></span>

<span data-ttu-id="b71ed-644">Die Schnittstellen `ILogger` und `ILoggerFactory` befinden sich in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), ihre Standardimplementierungen sind in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) enthalten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-644">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="b71ed-645">Protokollkategorie</span><span class="sxs-lookup"><span data-stu-id="b71ed-645">Log category</span></span>

<span data-ttu-id="b71ed-646">Wenn ein `ILogger`-Objekt erstellt wird, wird eine *Kategorie* für dieses Objekt angegeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-646">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="b71ed-647">Diese Kategorie ist in jeder Protokollmeldung enthalten, die von dieser Instanz von `ILogger` erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-647">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="b71ed-648">Die Kategorie kann eine beliebige Zeichenfolge sein, aber die Konvention besteht in der Verwendung des Klassennamens, z.B. „TodoApi.Controllers.TodoController“.</span><span class="sxs-lookup"><span data-stu-id="b71ed-648">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="b71ed-649">Verwenden Sie `ILogger<T>` zum Abrufen einer `ILogger`-Instanz, die den vollqualifizierten Typnamen von `T` als Kategorie verwendet:</span><span class="sxs-lookup"><span data-stu-id="b71ed-649">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

<span data-ttu-id="b71ed-650">Um die Kategorie explizit anzugeben, rufen Sie `ILoggerFactory.CreateLogger` auf:</span><span class="sxs-lookup"><span data-stu-id="b71ed-650">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

<span data-ttu-id="b71ed-651">`ILogger<T>` entspricht dem Aufruf von `CreateLogger` mit dem vollqualifizierten Typnamen `T`.</span><span class="sxs-lookup"><span data-stu-id="b71ed-651">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="b71ed-652">Protokolliergrad</span><span class="sxs-lookup"><span data-stu-id="b71ed-652">Log level</span></span>

<span data-ttu-id="b71ed-653">Jedes Protokoll gibt einen <xref:Microsoft.Extensions.Logging.LogLevel>-Wert an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-653">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="b71ed-654">Der Protokolliergrad gibt den Schweregrad oder die Wichtigkeit an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-654">The log level indicates the severity or importance.</span></span> <span data-ttu-id="b71ed-655">Sie können beispielsweise ein `Information`-Protokoll schreiben, wenn eine Methode normal endet, und ein `Warning`-Protokoll, wenn eine Methode den Statuscode *404 Not Found* zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-655">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="b71ed-656">Der folgende Code erstellt `Information`- und `Warning`-Protokolle:</span><span class="sxs-lookup"><span data-stu-id="b71ed-656">The following code creates `Information` and `Warning` logs:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="b71ed-657">Im Code oben ist der erste Parameter die [Protokollereignis-ID](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="b71ed-657">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="b71ed-658">Der zweite Parameter ist eine Meldungsvorlage mit Platzhaltern für Argumentwerte, die von den verbleibenden Methodenparametern bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-658">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="b71ed-659">Die Methodenparameter werden im [Abschnitt „Meldungsvorlage“](#log-message-template) später in diesem Artikel erläutert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-659">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="b71ed-660">Protokollmethoden, die den Protokolliergrad im Methodennamen enthalten (z.B. `LogInformation` und `LogWarning`), sind [Erweiterungsmethoden für ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="b71ed-660">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="b71ed-661">Diese Methoden rufen eine `Log`-Methode auf, die einen `LogLevel`-Parameter annimmt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-661">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="b71ed-662">Sie können anstelle eines Aufrufs dieser Erweiterungsmethoden die `Log`-Methode direkt aufrufen, aber die Syntax ist relativ kompliziert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-662">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="b71ed-663">Weitere Informationen finden Sie unter <xref:Microsoft.Extensions.Logging.ILogger> und im [Quellcode für Protokollierungserweiterungen](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="b71ed-663">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="b71ed-664">ASP.NET Core definiert die folgenden Protokolliergrade. Die Reihenfolge reicht vom geringsten bis zum höchsten Schweregrad.</span><span class="sxs-lookup"><span data-stu-id="b71ed-664">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="b71ed-665">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="b71ed-665">Trace = 0</span></span>

  <span data-ttu-id="b71ed-666">Protokolliert Informationen, die in der Regel nur für das Debuggen nützlich sind.</span><span class="sxs-lookup"><span data-stu-id="b71ed-666">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="b71ed-667">Diese Meldungen können sensible Anwendungsdaten enthalten und sollten daher nicht in einer Produktionsumgebung aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-667">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="b71ed-668">*Standardmäßig deaktiviert.*</span><span class="sxs-lookup"><span data-stu-id="b71ed-668">*Disabled by default.*</span></span>

* <span data-ttu-id="b71ed-669">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="b71ed-669">Debug = 1</span></span>

  <span data-ttu-id="b71ed-670">Protokolliert Informationen, die bei der Entwicklung und beim Debuggen hilfreich sein können.</span><span class="sxs-lookup"><span data-stu-id="b71ed-670">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="b71ed-671">Beispiel: `Entering method Configure with flag set to true.` Aktivieren Sie Protokolle mit dem Protokolliergrad `Debug` aufgrund des hohen Protokollaufkommens nur zur Problembehandlung in der Produktion.</span><span class="sxs-lookup"><span data-stu-id="b71ed-671">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="b71ed-672">Information = 2</span><span class="sxs-lookup"><span data-stu-id="b71ed-672">Information = 2</span></span>

  <span data-ttu-id="b71ed-673">Zur Nachverfolgung des allgemeinen Ablaufs der App.</span><span class="sxs-lookup"><span data-stu-id="b71ed-673">For tracking the general flow of the app.</span></span> <span data-ttu-id="b71ed-674">Diese Protokolle haben typischerweise einen langfristigen Nutzen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-674">These logs typically have some long-term value.</span></span> <span data-ttu-id="b71ed-675">Ein Beispiel: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="b71ed-675">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="b71ed-676">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="b71ed-676">Warning = 3</span></span>

  <span data-ttu-id="b71ed-677">Für ungewöhnliche oder unerwartete Ereignisse im App-Verlauf.</span><span class="sxs-lookup"><span data-stu-id="b71ed-677">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="b71ed-678">Dazu können Fehler oder andere Bedingungen gehören, die zwar nicht zum Beenden der App führen, aber eventuell untersucht werden müssen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-678">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="b71ed-679">Behandelte Ausnahmen sind eine typische Verwendung für den Protokolliergrad `Warning`.</span><span class="sxs-lookup"><span data-stu-id="b71ed-679">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="b71ed-680">Ein Beispiel: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="b71ed-680">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="b71ed-681">Error = 4</span><span class="sxs-lookup"><span data-stu-id="b71ed-681">Error = 4</span></span>

  <span data-ttu-id="b71ed-682">Für Fehler und Ausnahmen, die nicht behandelt werden können.</span><span class="sxs-lookup"><span data-stu-id="b71ed-682">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="b71ed-683">Diese Meldungen weisen auf einen Fehler in der aktuellen Aktivität oder im aktuellen Vorgang (z.B. in der aktuellen HTTP-Anforderung) und nicht auf einen anwendungsweiten Fehler hin.</span><span class="sxs-lookup"><span data-stu-id="b71ed-683">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="b71ed-684">Beispielprotokollmeldung: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="b71ed-684">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="b71ed-685">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="b71ed-685">Critical = 5</span></span>

  <span data-ttu-id="b71ed-686">Für Fehler, die sofortige Aufmerksamkeit erfordern.</span><span class="sxs-lookup"><span data-stu-id="b71ed-686">For failures that require immediate attention.</span></span> <span data-ttu-id="b71ed-687">Beispiel: Szenarios mit Datenverlust, Speichermangel.</span><span class="sxs-lookup"><span data-stu-id="b71ed-687">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="b71ed-688">Verwenden Sie den Protokolliergrad, um die Menge an Protokollausgabedaten zu steuern, die in ein bestimmtes Speichermedium geschrieben oder an ein Anzeigefenster ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-688">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="b71ed-689">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b71ed-689">For example:</span></span>

* <span data-ttu-id="b71ed-690">In einer Produktionsumgebung</span><span class="sxs-lookup"><span data-stu-id="b71ed-690">In production:</span></span>
  * <span data-ttu-id="b71ed-691">Das Protokollieren von `Trace` über die `Information`-Ebenen verursacht viele detaillierte Protokollmeldungen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-691">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="b71ed-692">Protokollieren Sie `Trace` über Nachrichten auf `Information`-Ebene in einem kostengünstigen Speicher mit hohem Volumen, um die Kosten zu überwachen und die Grenzwerte des Datenspeichers nicht zu überschreiten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-692">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="b71ed-693">Das Protokollieren von `Warning` über die `Critical`-Ebenen verursacht weniger und kürzere Protokollmeldungen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-693">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="b71ed-694">Daher müssen Sie sich über Kosten- und Speichergrenzwerte keine Sorgen machen und sind bei der Auswahl des Datenspeichers flexibler.</span><span class="sxs-lookup"><span data-stu-id="b71ed-694">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="b71ed-695">Entwicklungsphase:</span><span class="sxs-lookup"><span data-stu-id="b71ed-695">During development:</span></span>
  * <span data-ttu-id="b71ed-696">Protokollieren Sie `Warning` über `Critical`-Meldungen an der Konsole.</span><span class="sxs-lookup"><span data-stu-id="b71ed-696">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="b71ed-697">Fügen Sie bei der Fehlerbehebung `Trace` über `Information`-Meldungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="b71ed-697">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="b71ed-698">Im Abschnitt [Protokollfilterung](#log-filtering) weiter unten in diesem Artikel wird erläutert, wie Sie steuern, welche Protokolliergrade ein Anbieter verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-698">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="b71ed-699">ASP.NET Core schreibt Protokolle für Frameworkereignisse.</span><span class="sxs-lookup"><span data-stu-id="b71ed-699">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="b71ed-700">Die zuvor in diesem Artikel gezeigten Protokollbeispiele enthielten Protokolle unterhalb des Protokolliergrads `Information`, deshalb wurden keine Protokolle des Protokolliergrads `Debug` oder `Trace` erstellt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-700">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="b71ed-701">Hier ist ein Beispiel für Konsolenprotokolle, die durch Ausführen der Beispiel-App generiert werden, die so konfiguriert ist, dass sie `Debug`-Protokolle anzeigt:</span><span class="sxs-lookup"><span data-stu-id="b71ed-701">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="b71ed-702">Protokollereignis-ID</span><span class="sxs-lookup"><span data-stu-id="b71ed-702">Log event ID</span></span>

<span data-ttu-id="b71ed-703">Jedes Protokoll kann eine *Ereignis-ID* angeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-703">Each log can specify an *event ID*.</span></span> <span data-ttu-id="b71ed-704">Die Beispiel-App verwendet hierzu eine lokal definierte `LoggingEvents`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="b71ed-704">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

<span data-ttu-id="b71ed-705">Eine Ereignis-ID ordnet eine Gruppe von Ereignissen zu.</span><span class="sxs-lookup"><span data-stu-id="b71ed-705">An event ID associates a set of events.</span></span> <span data-ttu-id="b71ed-706">Beispielsweise können alle Protokolle, die sich auf die Anzeige einer Liste von Elementen auf einer Seite beziehen, 1001 sein.</span><span class="sxs-lookup"><span data-stu-id="b71ed-706">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="b71ed-707">Der Protokollierungsanbieter kann die Ereignis-ID in einem ID-Feld, in der Protokollierungsmeldung oder gar nicht speichern.</span><span class="sxs-lookup"><span data-stu-id="b71ed-707">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="b71ed-708">Der Debuganbieter zeigt keine Ereignis-IDs an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-708">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="b71ed-709">Der Konsolenanbieter zeigt Ereignis-IDs in Klammern hinter der Kategorie an:</span><span class="sxs-lookup"><span data-stu-id="b71ed-709">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="b71ed-710">Protokollmeldungsvorlage</span><span class="sxs-lookup"><span data-stu-id="b71ed-710">Log message template</span></span>

<span data-ttu-id="b71ed-711">Jedes Protokoll gibt eine Meldungsvorlage an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-711">Each log specifies a message template.</span></span> <span data-ttu-id="b71ed-712">Die Meldungsvorlage kann Platzhalter enthalten, für die Argumente bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-712">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="b71ed-713">Verwenden Sie Namen für die Platzhalter, keine Zahlen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-713">Use names for the placeholders, not numbers.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="b71ed-714">Die Reihenfolge der Platzhalter – nicht ihre Namen – bestimmt, welche Parameter verwendet werden, um ihre Werte zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-714">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="b71ed-715">Beachten Sie im folgenden Code, dass die Parameternamen in der Meldungsvorlage nicht in der richtigen Reihenfolge vorhanden sind:</span><span class="sxs-lookup"><span data-stu-id="b71ed-715">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="b71ed-716">Dieser Code erstellt eine Protokollierungsmeldung mit den Parameterwerten in der richtigen Reihenfolge:</span><span class="sxs-lookup"><span data-stu-id="b71ed-716">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="b71ed-717">Das Protokollierungsframework funktioniert auf diese Weise, damit Protokollierungsanbieter [semantische Protokollierung, die auch als strukturierte Protokollierung bezeichnet wird](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging), implementieren können.</span><span class="sxs-lookup"><span data-stu-id="b71ed-717">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="b71ed-718">Die Argumente selbst (nicht nur die formatierte Meldungsvorlage) werden an das Protokollierungssystem übergeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-718">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="b71ed-719">Diese Informationen ermöglichen es Protokollierungsanbietern, die Parameterwerte als Felder zu speichern.</span><span class="sxs-lookup"><span data-stu-id="b71ed-719">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="b71ed-720">Nehmen wir zum Beispiel an, dass Aufrufe von Protokollierungsmethoden folgendermaßen aussehen:</span><span class="sxs-lookup"><span data-stu-id="b71ed-720">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="b71ed-721">Wenn Sie die Protokolle an Azure Table Storage senden, kann jede Azure Table-Entität die Eigenschaften `ID` und `RequestTime` aufweisen. Dies vereinfacht die Abfrage von Protokolldaten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-721">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="b71ed-722">Eine Abfrage kann alle Protokolle in einem bestimmten `RequestTime`-Bereich finden, ohne die Uhrzeit aus den Textmeldungen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="b71ed-722">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="b71ed-723">Protokollieren von Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="b71ed-723">Logging exceptions</span></span>

<span data-ttu-id="b71ed-724">Die Protokollierungsmethoden umfassen Überladungen, die das Übergeben von Ausnahmen ermöglichen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="b71ed-724">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

<span data-ttu-id="b71ed-725">Die verschiedenen Anbieter verarbeiten die Ausnahmeinformationen auf unterschiedliche Weise.</span><span class="sxs-lookup"><span data-stu-id="b71ed-725">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="b71ed-726">Hier sehen Sie ein Beispiel einer Debuganbieterausgabe aus dem obigen Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="b71ed-726">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="b71ed-727">Protokollfilterung</span><span class="sxs-lookup"><span data-stu-id="b71ed-727">Log filtering</span></span>

<span data-ttu-id="b71ed-728">Sie können einen Mindestprotokolliergrad für einen bestimmten Anbieter und eine bestimmte Kategorie oder für alle Anbieter oder alle Kategorien festlegen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-728">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="b71ed-729">Alle Protokolle unter dem Mindestgrad werden nicht an diesen Anbieter weitergeleitet, sodass sie nicht angezeigt oder gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-729">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="b71ed-730">Wenn Sie alle Protokolle unterdrücken möchten, geben Sie `LogLevel.None` als Mindestprotokolliergrad an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-730">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="b71ed-731">Der ganzzahlige Wert von `LogLevel.None` lautet 6 und liegt über `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="b71ed-731">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="b71ed-732">Erstellen von Filterregeln in der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b71ed-732">Create filter rules in configuration</span></span>

<span data-ttu-id="b71ed-733">Der Projektvorlagencode ruft `CreateDefaultBuilder` auf, um die Protokollierung für die Konsolen-, Debug- und EventSource-Anbieter (ASP.NET Core 2.2 oder höher) einzurichten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-733">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="b71ed-734">Die `CreateDefaultBuilder`-Methode richtet die Protokollierung ein, um nach der Konfiguration in einem `Logging`-Abschnitt zu suchen; dies wird [weiter oben in diesem Artikel](#configuration) erläutert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-734">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="b71ed-735">Die Konfigurationsdaten geben die Mindestprotokolliergrade nach Anbieter und Kategorie an, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="b71ed-735">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="b71ed-736">Diese JSON-Konfiguration erstellt sechs Filterregeln: eine für den Debuganbieter, vier für den Konsolenanbieter und eine für alle Anbieter.</span><span class="sxs-lookup"><span data-stu-id="b71ed-736">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="b71ed-737">Eine einzelne Regel wird für jeden Anbieter ausgewählt, wenn ein `ILogger`-Objekt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-737">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="b71ed-738">Filterregeln im Code</span><span class="sxs-lookup"><span data-stu-id="b71ed-738">Filter rules in code</span></span>

<span data-ttu-id="b71ed-739">Das folgende Beispiel zeigt, wie Filterregeln im Code registriert werden:</span><span class="sxs-lookup"><span data-stu-id="b71ed-739">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="b71ed-740">Das zweite `AddFilter` gibt den Debuganbieter durch Verwendung des zugehörigen Typnamens an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-740">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="b71ed-741">Das erste `AddFilter` gilt für alle Anbieter, weil kein Anbietertyp angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-741">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="b71ed-742">Anwendung von Filterregeln</span><span class="sxs-lookup"><span data-stu-id="b71ed-742">How filtering rules are applied</span></span>

<span data-ttu-id="b71ed-743">Die Konfigurationsdaten und der in den vorangegangenen Beispielen gezeigte `AddFilter`-Code erzeugen die in der folgenden Tabelle gezeigten Regeln.</span><span class="sxs-lookup"><span data-stu-id="b71ed-743">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="b71ed-744">Die ersten sechs Regeln stammen aus dem Konfigurationsbeispiel, die letzten zwei Filter stammen aus dem Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="b71ed-744">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="b71ed-745">Anzahl</span><span class="sxs-lookup"><span data-stu-id="b71ed-745">Number</span></span> | <span data-ttu-id="b71ed-746">Anbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-746">Provider</span></span>      | <span data-ttu-id="b71ed-747">Kategorien beginnend mit...</span><span class="sxs-lookup"><span data-stu-id="b71ed-747">Categories that begin with ...</span></span>          | <span data-ttu-id="b71ed-748">Mindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="b71ed-748">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="b71ed-749">1</span><span class="sxs-lookup"><span data-stu-id="b71ed-749">1</span></span>      | <span data-ttu-id="b71ed-750">Debug</span><span class="sxs-lookup"><span data-stu-id="b71ed-750">Debug</span></span>         | <span data-ttu-id="b71ed-751">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="b71ed-751">All categories</span></span>                          | <span data-ttu-id="b71ed-752">Information</span><span class="sxs-lookup"><span data-stu-id="b71ed-752">Information</span></span>       |
| <span data-ttu-id="b71ed-753">2</span><span class="sxs-lookup"><span data-stu-id="b71ed-753">2</span></span>      | <span data-ttu-id="b71ed-754">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-754">Console</span></span>       | <span data-ttu-id="b71ed-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="b71ed-755">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="b71ed-756">Warnung</span><span class="sxs-lookup"><span data-stu-id="b71ed-756">Warning</span></span>           |
| <span data-ttu-id="b71ed-757">3</span><span class="sxs-lookup"><span data-stu-id="b71ed-757">3</span></span>      | <span data-ttu-id="b71ed-758">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-758">Console</span></span>       | <span data-ttu-id="b71ed-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="b71ed-759">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="b71ed-760">Debug</span><span class="sxs-lookup"><span data-stu-id="b71ed-760">Debug</span></span>             |
| <span data-ttu-id="b71ed-761">4</span><span class="sxs-lookup"><span data-stu-id="b71ed-761">4</span></span>      | <span data-ttu-id="b71ed-762">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-762">Console</span></span>       | <span data-ttu-id="b71ed-763">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="b71ed-763">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="b71ed-764">Fehler</span><span class="sxs-lookup"><span data-stu-id="b71ed-764">Error</span></span>             |
| <span data-ttu-id="b71ed-765">5</span><span class="sxs-lookup"><span data-stu-id="b71ed-765">5</span></span>      | <span data-ttu-id="b71ed-766">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-766">Console</span></span>       | <span data-ttu-id="b71ed-767">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="b71ed-767">All categories</span></span>                          | <span data-ttu-id="b71ed-768">Information</span><span class="sxs-lookup"><span data-stu-id="b71ed-768">Information</span></span>       |
| <span data-ttu-id="b71ed-769">6</span><span class="sxs-lookup"><span data-stu-id="b71ed-769">6</span></span>      | <span data-ttu-id="b71ed-770">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-770">All providers</span></span> | <span data-ttu-id="b71ed-771">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="b71ed-771">All categories</span></span>                          | <span data-ttu-id="b71ed-772">Debug</span><span class="sxs-lookup"><span data-stu-id="b71ed-772">Debug</span></span>             |
| <span data-ttu-id="b71ed-773">7</span><span class="sxs-lookup"><span data-stu-id="b71ed-773">7</span></span>      | <span data-ttu-id="b71ed-774">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-774">All providers</span></span> | <span data-ttu-id="b71ed-775">System</span><span class="sxs-lookup"><span data-stu-id="b71ed-775">System</span></span>                                  | <span data-ttu-id="b71ed-776">Debug</span><span class="sxs-lookup"><span data-stu-id="b71ed-776">Debug</span></span>             |
| <span data-ttu-id="b71ed-777">8</span><span class="sxs-lookup"><span data-stu-id="b71ed-777">8</span></span>      | <span data-ttu-id="b71ed-778">Debug</span><span class="sxs-lookup"><span data-stu-id="b71ed-778">Debug</span></span>         | <span data-ttu-id="b71ed-779">Microsoft</span><span class="sxs-lookup"><span data-stu-id="b71ed-779">Microsoft</span></span>                               | <span data-ttu-id="b71ed-780">Ablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="b71ed-780">Trace</span></span>             |

<span data-ttu-id="b71ed-781">Wenn ein `ILogger`-Objekt erstellt wird, wählt das `ILoggerFactory`-Objekt eine einzige Regel pro Anbieter aus, die auf diese Protokollierung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-781">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="b71ed-782">Alle von einer `ILogger`-Instanz geschriebenen Meldungen werden auf Grundlage der ausgewählten Regeln gefiltert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-782">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="b71ed-783">Aus den verfügbaren Regeln wird die für jeden Anbieter und jedes Kategoriepaar spezifischste Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-783">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="b71ed-784">Beim Erstellen von `ILogger` für eine vorgegebene Kategorie wird für jeden Anbieter der folgende Algorithmus verwendet:</span><span class="sxs-lookup"><span data-stu-id="b71ed-784">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="b71ed-785">Es werden alle Regeln ausgewählt, die dem Anbieter oder dem zugehörigen Alias entsprechen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-785">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="b71ed-786">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln mit einem leeren Anbieter ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-786">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="b71ed-787">Aus dem Ergebnis des vorherigen Schritts werden die Regeln mit dem längsten übereinstimmenden Kategoriepräfix ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-787">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="b71ed-788">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln ohne Angabe einer Kategorie ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-788">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="b71ed-789">Wenn mehrere Regeln ausgewählt sind, wird die **letzte** Regel verwendet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-789">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="b71ed-790">Wenn keine Regel ausgewählt ist, wird `MinimumLevel` verwendet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-790">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="b71ed-791">Angenommen, Sie erstellen mit der oben aufgeführten Liste mit Regeln ein `ILogger`-Objekt für die Kategorie „Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine“:</span><span class="sxs-lookup"><span data-stu-id="b71ed-791">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="b71ed-792">Für den Debuganbieter gelten die Regeln 1, 6 und 8.</span><span class="sxs-lookup"><span data-stu-id="b71ed-792">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="b71ed-793">Regel 8 ist die spezifischste Regel, deshalb wird diese Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-793">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="b71ed-794">Für den Konsolenanbieter gelten die Regeln 3, 4, 5 und 6.</span><span class="sxs-lookup"><span data-stu-id="b71ed-794">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="b71ed-795">Regel 3 ist die spezifischste Regel.</span><span class="sxs-lookup"><span data-stu-id="b71ed-795">Rule 3 is most specific.</span></span>

<span data-ttu-id="b71ed-796">Die sich ergebende `ILogger`-Instanz sendet Protokolle mit dem Protokolliergrad `Trace` und höher an den Debuganbieter.</span><span class="sxs-lookup"><span data-stu-id="b71ed-796">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="b71ed-797">Protokolle mit dem Protokolliergrad `Debug` und höher werden an den Konsolenanbieter gesendet.</span><span class="sxs-lookup"><span data-stu-id="b71ed-797">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="b71ed-798">Anbieteraliase</span><span class="sxs-lookup"><span data-stu-id="b71ed-798">Provider aliases</span></span>

<span data-ttu-id="b71ed-799">Jeder Anbieter definiert einen *Alias*, der in der Konfiguration anstelle des vollqualifizierten Typnamens verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="b71ed-799">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="b71ed-800">Verwenden Sie für die integrierten Anbieter die folgenden Aliase:</span><span class="sxs-lookup"><span data-stu-id="b71ed-800">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="b71ed-801">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-801">Console</span></span>
* <span data-ttu-id="b71ed-802">Debug</span><span class="sxs-lookup"><span data-stu-id="b71ed-802">Debug</span></span>
* <span data-ttu-id="b71ed-803">EventSource</span><span class="sxs-lookup"><span data-stu-id="b71ed-803">EventSource</span></span>
* <span data-ttu-id="b71ed-804">EventLog</span><span class="sxs-lookup"><span data-stu-id="b71ed-804">EventLog</span></span>
* <span data-ttu-id="b71ed-805">TraceSource</span><span class="sxs-lookup"><span data-stu-id="b71ed-805">TraceSource</span></span>
* <span data-ttu-id="b71ed-806">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="b71ed-806">AzureAppServicesFile</span></span>
* <span data-ttu-id="b71ed-807">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="b71ed-807">AzureAppServicesBlob</span></span>
* <span data-ttu-id="b71ed-808">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="b71ed-808">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="b71ed-809">Standardmindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="b71ed-809">Default minimum level</span></span>

<span data-ttu-id="b71ed-810">Es gibt eine Einstellung für den Mindestprotokolliergrad, die nur dann wirksam wird, wenn für einen bestimmten Anbieter und eine bestimmte Kategorie keine Regeln aus Konfiguration oder Code gelten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-810">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="b71ed-811">Im folgenden Beispiel wird das Festlegen des Mindestprotokolliergrads veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="b71ed-811">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="b71ed-812">Wenn Sie den Mindestprotokolliergrad nicht explizit festlegen, lautet der Standardwert `Information`, d.h. Protokolle der Ebene `Trace` und `Debug` werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-812">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="b71ed-813">Filterfunktionen</span><span class="sxs-lookup"><span data-stu-id="b71ed-813">Filter functions</span></span>

<span data-ttu-id="b71ed-814">Eine Filterfunktion wird für alle Anbieter und Kategorien aufgerufen, denen keine Regeln durch Konfiguration oder Code zugewiesen sind.</span><span class="sxs-lookup"><span data-stu-id="b71ed-814">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="b71ed-815">Code in der Funktion verfügt über Zugriff auf den Anbietertyp, die Kategorie und den Protokolliergrad.</span><span class="sxs-lookup"><span data-stu-id="b71ed-815">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="b71ed-816">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b71ed-816">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a><span data-ttu-id="b71ed-817">Systemkategorien und -protokolliergrade</span><span class="sxs-lookup"><span data-stu-id="b71ed-817">System categories and levels</span></span>

<span data-ttu-id="b71ed-818">Dies sind einige Kategorien, die vom ASP.NET Core und Entity Framework Core verwendet werden, mit Hinweisen darauf, welche Protokolle von ihnen zu erwarten sind:</span><span class="sxs-lookup"><span data-stu-id="b71ed-818">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="b71ed-819">Kategorie</span><span class="sxs-lookup"><span data-stu-id="b71ed-819">Category</span></span>                            | <span data-ttu-id="b71ed-820">Hinweise</span><span class="sxs-lookup"><span data-stu-id="b71ed-820">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="b71ed-821">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="b71ed-821">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="b71ed-822">Allgemeine ASP.NET Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="b71ed-822">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="b71ed-823">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="b71ed-823">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="b71ed-824">Gibt an, welche Schlüssel in Betracht gezogen, gefunden und verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-824">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="b71ed-825">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="b71ed-825">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="b71ed-826">Hosts sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="b71ed-826">Hosts allowed.</span></span> |
| <span data-ttu-id="b71ed-827">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="b71ed-827">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="b71ed-828">Gibt an, wie lange es bis zum Abschluss von HTTP-Anforderungen gedauert hat und zu welcher Zeit sie gestartet wurden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-828">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="b71ed-829">Gibt an, welche Hostingstartassemblys geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-829">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="b71ed-830">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="b71ed-830">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="b71ed-831">MVC- und Razor-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="b71ed-831">MVC and Razor diagnostics.</span></span> <span data-ttu-id="b71ed-832">Modellbindung, Filterausführung, Ansichtskompilierung, Aktionsauswahl.</span><span class="sxs-lookup"><span data-stu-id="b71ed-832">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="b71ed-833">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="b71ed-833">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="b71ed-834">Gibt Routenabgleichsinformationen an.</span><span class="sxs-lookup"><span data-stu-id="b71ed-834">Route matching information.</span></span> |
| <span data-ttu-id="b71ed-835">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="b71ed-835">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="b71ed-836">Start-, Beendigungs- und Keep-Alive-Antworten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="b71ed-836">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="b71ed-837">HTTPS-Zertifikatinformationen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-837">HTTPS certificate information.</span></span> |
| <span data-ttu-id="b71ed-838">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="b71ed-838">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="b71ed-839">Die bereitgestellten Dateien.</span><span class="sxs-lookup"><span data-stu-id="b71ed-839">Files served.</span></span> |
| <span data-ttu-id="b71ed-840">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="b71ed-840">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="b71ed-841">Allgemeine Entity Framework Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="b71ed-841">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="b71ed-842">Datenbankaktivität und -konfiguration, Änderungserkennung, Migrationen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-842">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="b71ed-843">Protokollbereiche</span><span class="sxs-lookup"><span data-stu-id="b71ed-843">Log scopes</span></span>

 <span data-ttu-id="b71ed-844">Ein *Bereich* kann einen Satz logischer Vorgänge gruppieren.</span><span class="sxs-lookup"><span data-stu-id="b71ed-844">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="b71ed-845">Diese Gruppierung kann verwendet werden, um an jedes Protokoll, das als Teil einer Gruppe erstellt wird, die gleichen Daten anzufügen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-845">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="b71ed-846">So kann beispielsweise jedes Protokoll, das im Rahmen der Verarbeitung einer Transaktion erstellt wird, die Transaktions-ID enthalten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-846">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="b71ed-847">Ein Bereich ist ein `IDisposable`-Typ, der von der <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>-Methode zurückgegeben und so lange beibehalten wird, bis er verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-847">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="b71ed-848">Verwenden Sie einen Bereich, indem Sie Protokollierungsaufrufe mit einem `using`-Block umschließen, der als Wrapper verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="b71ed-848">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="b71ed-849">Der folgende Code aktiviert Bereiche für den Konsolenanbieter:</span><span class="sxs-lookup"><span data-stu-id="b71ed-849">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="b71ed-850">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b71ed-850">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="b71ed-851">Um die bereichsbasierte Protokollierung zu aktivieren, muss die Konsolenprotokollierungsoption `IncludeScopes` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-851">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="b71ed-852">Weitere Informationen zur Konfiguration finden Sie im Abschnitt [Konfiguration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="b71ed-852">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="b71ed-853">Jede Protokollmeldung enthält die bereichsbezogenen Informationen:</span><span class="sxs-lookup"><span data-stu-id="b71ed-853">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="b71ed-854">Integrierte Protokollierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-854">Built-in logging providers</span></span>

<span data-ttu-id="b71ed-855">ASP.NET Core wird mit den folgenden Anbietern bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="b71ed-855">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="b71ed-856">Konsole</span><span class="sxs-lookup"><span data-stu-id="b71ed-856">Console</span></span>](#console-provider)
* [<span data-ttu-id="b71ed-857">Debuggen</span><span class="sxs-lookup"><span data-stu-id="b71ed-857">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="b71ed-858">EventSource</span><span class="sxs-lookup"><span data-stu-id="b71ed-858">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="b71ed-859">EventLog</span><span class="sxs-lookup"><span data-stu-id="b71ed-859">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="b71ed-860">TraceSource</span><span class="sxs-lookup"><span data-stu-id="b71ed-860">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="b71ed-861">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="b71ed-861">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="b71ed-862">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="b71ed-862">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="b71ed-863">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="b71ed-863">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="b71ed-864">Weitere Informationen zu „stdout“ und zur Debugprotokollierung mit dem ASP.NET Core-Modul finden Sie unter <xref:test/troubleshoot-azure-iis> und <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="b71ed-864">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="b71ed-865">Der Konsolenanbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-865">Console provider</span></span>

<span data-ttu-id="b71ed-866">Das Anbieterpaket [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sendet eine Protokollausgabe an die Konsole.</span><span class="sxs-lookup"><span data-stu-id="b71ed-866">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="b71ed-867">Um die Ausgabe der Konsolenprotokollierung anzuzeigen, öffnen Sie eine Eingabeaufforderung im Projektordner und führen den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="b71ed-867">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="b71ed-868">Der Debuganbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-868">Debug provider</span></span>

<span data-ttu-id="b71ed-869">Beim Anbieterpaket [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) erfolgt die Protokollausgabe unter Verwendung der Klasse [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine`-Methodenaufrufe).</span><span class="sxs-lookup"><span data-stu-id="b71ed-869">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="b71ed-870">Unter Linux werden Protokolle dieses Anbieters in */var/log/message* geschrieben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-870">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="b71ed-871">Ereignisquellenanbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-871">Event Source provider</span></span>

<span data-ttu-id="b71ed-872">Das [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource)-Anbieterpaket schreibt in eine plattformübergreifende Ereignisquelle mit dem Namen `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="b71ed-872">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="b71ed-873">Unter Windows verwendet der Anbieter die [Ereignisablaufverfolgung für Windows (ETW)](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="b71ed-873">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="b71ed-874">Der Ereignisquellenanbieter wird automatisch hinzugefügt, wenn `CreateDefaultBuilder` zum Erstellen des Hosts aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-874">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

<span data-ttu-id="b71ed-875">Verwenden Sie das [PerfView-Hilfsprogramm](https://github.com/Microsoft/perfview) zum Sammeln und Anzeigen von Protokollen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-875">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="b71ed-876">Es gibt andere Tools zur Anzeige von ETW-Protokollen, aber PerfView bietet die besten Ergebnisse bei der Arbeit mit ETW-Ereignissen, die von ASP.NET Core ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-876">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="b71ed-877">Um PerfView für das Erfassen von Ereignissen zu konfigurieren, die von diesem Anbieter protokolliert wurden, fügen Sie die Zeichenfolge `*Microsoft-Extensions-Logging` zur Liste **Zusätzliche Anbieter** hinzu.</span><span class="sxs-lookup"><span data-stu-id="b71ed-877">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="b71ed-878">(Vergessen Sie nicht das Sternchen am Anfang der Zeichenfolge.)</span><span class="sxs-lookup"><span data-stu-id="b71ed-878">(Don't miss the asterisk at the start of the string.)</span></span>

![Zusätzliche PerfView-Anbieter](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="b71ed-880">Der Windows-EventLog-Anbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-880">Windows EventLog provider</span></span>

<span data-ttu-id="b71ed-881">Das Anbieterpaket [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sendet eine Protokollausgabe in das Windows-Ereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="b71ed-881">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="b71ed-882">Mithilfe von [AddEventLog-Überladungen](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) können Sie <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> übergeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-882">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span> <span data-ttu-id="b71ed-883">Wenn `null` oder nicht angegeben, werden die folgenden Standardeinstellungen verwendet:</span><span class="sxs-lookup"><span data-stu-id="b71ed-883">If `null` or not specified, the following default settings are used:</span></span>

* <span data-ttu-id="b71ed-884">`LogName` „Anwendung“</span><span class="sxs-lookup"><span data-stu-id="b71ed-884">`LogName` &ndash; "Application"</span></span>
* <span data-ttu-id="b71ed-885">`SourceName` „.NET Runtime“</span><span class="sxs-lookup"><span data-stu-id="b71ed-885">`SourceName` &ndash; ".NET Runtime"</span></span>
* <span data-ttu-id="b71ed-886">`MachineName` Lokaler Computer</span><span class="sxs-lookup"><span data-stu-id="b71ed-886">`MachineName` &ndash; local machine</span></span>

<span data-ttu-id="b71ed-887">Ereignisse werden für die [Warnstufe und höher](#log-level) protokolliert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-887">Events are logged for [Warning level and higher](#log-level).</span></span> <span data-ttu-id="b71ed-888">Legen Sie die Protokollierungsebene fest, um Ereignisse, die niedriger als `Warning` sind, zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="b71ed-888">To log events lower than `Warning`, explicitly set the log level.</span></span> <span data-ttu-id="b71ed-889">Fügen Sie beispielsweise Folgendes zur *appsettings.json*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="b71ed-889">For example, add the following to the *appsettings.json* file:</span></span>

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a><span data-ttu-id="b71ed-890">Der TraceSource-Anbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-890">TraceSource provider</span></span>

<span data-ttu-id="b71ed-891">Das Anbieterpaket [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) verwendet die <xref:System.Diagnostics.TraceSource>-Bibliotheken und -Anbieter.</span><span class="sxs-lookup"><span data-stu-id="b71ed-891">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="b71ed-892">Mithilfe von [AddTraceSource-Überladungen](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) können Sie eine Quelloption und einen Listener für die Ablaufverfolgung übergeben.</span><span class="sxs-lookup"><span data-stu-id="b71ed-892">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="b71ed-893">Um diesen Anbieter zu verwenden, muss eine App unter .NET Framework (anstelle von .NET Core) ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b71ed-893">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="b71ed-894">Der Anbieter kann Nachrichten an eine Vielzahl von [Listenern](/dotnet/framework/debug-trace-profile/trace-listeners) weiterleiten, z.B. an den in der Beispiel-App verwendeten <xref:System.Diagnostics.TextWriterTraceListener>.</span><span class="sxs-lookup"><span data-stu-id="b71ed-894">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="b71ed-895">Der Azure App Service-Anbieter</span><span class="sxs-lookup"><span data-stu-id="b71ed-895">Azure App Service provider</span></span>

<span data-ttu-id="b71ed-896">Das Anbieterpaket [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) schreibt Protokolle in Textdateien in das Dateisystem einer Azure App Service-App und in [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in einem Azure Storage-Konto.</span><span class="sxs-lookup"><span data-stu-id="b71ed-896">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

<span data-ttu-id="b71ed-897">Dieses Anbieterpaket ist nicht im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-897">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="b71ed-898">Wenn Sie Anwendungen für .NET Framework entwickeln oder auf das `Microsoft.AspNetCore.App`-Metapaket verweisen, fügen Sie das Anbieterpaket dem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="b71ed-898">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

<span data-ttu-id="b71ed-899">Eine <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>-Überladung ermöglicht das Übergeben von <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="b71ed-899">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="b71ed-900">Das settings-Objekt kann Standardeinstellungen überschreiben, z.B. die Protokollierungsausgabevorlage, den Blobnamen und die maximale Dateigröße.</span><span class="sxs-lookup"><span data-stu-id="b71ed-900">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="b71ed-901">(Die *Ausgabevorlage* ist eine Nachrichtenvorlage, die auf alle Protokolle zusätzlich zu dem angewendet wird, was mit einem `ILogger`-Methodenaufruf bereitgestellt wird.)</span><span class="sxs-lookup"><span data-stu-id="b71ed-901">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="b71ed-902">Wenn Sie eine Bereitstellung in einer App Service-App durchführen, berücksichtigt die Anwendung die Einstellungen im Abschnitt [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) (App Service-Protokolle) auf der Seite **App Services** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="b71ed-902">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="b71ed-903">Bei einem Update der folgenden Einstellungen werden die Änderungen sofort wirksam, ohne dass ein Neustart oder eine erneute Bereitstellung der App notwendig ist.</span><span class="sxs-lookup"><span data-stu-id="b71ed-903">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="b71ed-904">**Anwendungsprotokoll (Dateisystem)**</span><span class="sxs-lookup"><span data-stu-id="b71ed-904">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="b71ed-905">**Anwendungsprotokoll (Blob)**</span><span class="sxs-lookup"><span data-stu-id="b71ed-905">**Application Logging (Blob)**</span></span>

<span data-ttu-id="b71ed-906">Der Standardspeicherort für Protokolldateien ist der Ordner *D:\\home\\LogFiles\\Application*, und der standardmäßige Dateiname lautet *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="b71ed-906">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="b71ed-907">Die Dateigröße ist standardmäßig auf 10 MB beschränkt, und die maximal zulässige Anzahl beibehaltener Dateien lautet 2.</span><span class="sxs-lookup"><span data-stu-id="b71ed-907">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="b71ed-908">Der Standardblobname lautet *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="b71ed-908">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="b71ed-909">Der Anbieter funktioniert nur, wenn das Projekt in der Azure-Umgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-909">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="b71ed-910">Bei einer lokalen Ausführung zeigt er keine Auswirkungen. Der Anbieter schreibt keine Protokolle in lokale Dateien oder den lokalen Entwicklungsspeicher für BLOBs.</span><span class="sxs-lookup"><span data-stu-id="b71ed-910">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="b71ed-911">Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="b71ed-911">Azure log streaming</span></span>

<span data-ttu-id="b71ed-912">Azure-Protokollstreaming ermöglicht Ihnen eine Echtzeitanzeige der Protokollaktivität für:</span><span class="sxs-lookup"><span data-stu-id="b71ed-912">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="b71ed-913">App-Server</span><span class="sxs-lookup"><span data-stu-id="b71ed-913">The app server</span></span>
* <span data-ttu-id="b71ed-914">Webserver</span><span class="sxs-lookup"><span data-stu-id="b71ed-914">The web server</span></span>
* <span data-ttu-id="b71ed-915">Ablaufverfolgung für Anforderungsfehler</span><span class="sxs-lookup"><span data-stu-id="b71ed-915">Failed request tracing</span></span>

<span data-ttu-id="b71ed-916">So konfigurieren Sie das Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="b71ed-916">To configure Azure log streaming:</span></span>

* <span data-ttu-id="b71ed-917">Navigieren Sie von der Portalseite Ihrer App zur Seite **App Service-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="b71ed-917">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="b71ed-918">Legen Sie **Anwendungsprotokollierung (Dateisystem)** auf **Ein** fest.</span><span class="sxs-lookup"><span data-stu-id="b71ed-918">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="b71ed-919">Wählen Sie die **Protokollierungsebene**.</span><span class="sxs-lookup"><span data-stu-id="b71ed-919">Choose the log **Level**.</span></span> <span data-ttu-id="b71ed-920">Diese Einstellung gilt nur für das Azure-Protokollstreaming, nicht für andere Protokollierungsanbieter in der App.</span><span class="sxs-lookup"><span data-stu-id="b71ed-920">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="b71ed-921">Navigieren Sie zur Seite **Log Stream** (Protokollstream), um App-Meldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-921">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="b71ed-922">Diese werden von der App über die `ILogger`-Schnittstelle protokolliert.</span><span class="sxs-lookup"><span data-stu-id="b71ed-922">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="b71ed-923">Ablaufverfolgungsprotokollierung für Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="b71ed-923">Azure Application Insights trace logging</span></span>

<span data-ttu-id="b71ed-924">Das [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)-Anbieterpaket schreibt Protokolle in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="b71ed-924">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="b71ed-925">Application Insights ist ein Dienst, der eine Web-App überwacht und Tools für Abfragen und Analysen von Telemetriedaten zur Verfügung stellt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-925">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="b71ed-926">Wenn Sie diesen Anbieter verwenden, können Sie Ihre Protokolle mithilfe der Application Insights-Tools abfragen und analysieren.</span><span class="sxs-lookup"><span data-stu-id="b71ed-926">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="b71ed-927">Der Protokollierungsanbieter ist als Abhängigkeit von [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) enthalten, d.h. als das Paket, das alle verfügbaren Telemetriedaten für ASP.NET Core bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-927">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="b71ed-928">Wenn Sie dieses Paket verwenden, ist nicht erforderlich, das Anbieterpaket zu installieren.</span><span class="sxs-lookup"><span data-stu-id="b71ed-928">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="b71ed-929">Verwenden Sie nicht das [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)-Paket, das für ASP.NET 4.x bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="b71ed-929">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="b71ed-930">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="b71ed-930">For more information, see the following resources:</span></span>

* [<span data-ttu-id="b71ed-931">Application Insights-Übersicht</span><span class="sxs-lookup"><span data-stu-id="b71ed-931">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="b71ed-932">[Anwendungseinblicke für ASP.NET Core-Anwendungen](/azure/azure-monitor/app/asp-net-core): Beginnen Sie hier, wenn Sie die gesamte Bandbreite der Application Insights-Telemetrie zusammen mit der Protokollierung implementieren möchten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-932">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="b71ed-933">[ApplicationInsightsLoggerProvider für .NET Core ILogger-Protokolle](/azure/azure-monitor/app/ilogger): Beginnen Sie hier, wenn Sie den Protokollierungsanbieter ohne die übrige Application Insights-Telemetrie implementieren möchten.</span><span class="sxs-lookup"><span data-stu-id="b71ed-933">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="b71ed-934">[Application Insights logging adapters (Adapter zur Protokollierung für Application Insights)](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="b71ed-934">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="b71ed-935">[Installieren, Konfigurieren und Initialisieren des Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): interaktive Tutorials auf der Microsoft Learn-Website.</span><span class="sxs-lookup"><span data-stu-id="b71ed-935">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="b71ed-936">Protokollierungsanbieter von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="b71ed-936">Third-party logging providers</span></span>

<span data-ttu-id="b71ed-937">Protokollierungsframeworks von Drittanbietern aufgeführt, die mit ASP.NET Core funktionieren:</span><span class="sxs-lookup"><span data-stu-id="b71ed-937">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="b71ed-938">[elmah.io](https://elmah.io/) ([GitHub-Repository](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="b71ed-938">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="b71ed-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub-Repository](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="b71ed-939">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="b71ed-940">[JSNLog](https://jsnlog.com/) ([GitHub-Repository](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="b71ed-940">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="b71ed-941">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="b71ed-941">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="b71ed-942">[Log4Net](https://logging.apache.org/log4net/) ([GitHub-Repository](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="b71ed-942">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="b71ed-943">[Loggr](https://loggr.net/) ([GitHub-Repository](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="b71ed-943">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="b71ed-944">[NLog](https://nlog-project.org/) ([GitHub-Repository](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="b71ed-944">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="b71ed-945">[Sentry](https://sentry.io/welcome/) ([GitHub-Repository](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="b71ed-945">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="b71ed-946">[Serilog](https://serilog.net/) ([GitHub-Repository](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="b71ed-946">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="b71ed-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub-Repository](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="b71ed-947">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="b71ed-948">Einige Drittanbieterframeworks können eine [semantische Protokollierung (auch als strukturierte Protokollierung bezeichnet)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) ausführen.</span><span class="sxs-lookup"><span data-stu-id="b71ed-948">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="b71ed-949">Die Verwendung eines Frameworks von Drittanbietern ist ähnlich wie die Verwendung eines integrierten Anbieters:</span><span class="sxs-lookup"><span data-stu-id="b71ed-949">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="b71ed-950">Fügen Sie Ihrem Paket ein NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="b71ed-950">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="b71ed-951">Rufen Sie eine `ILoggerFactory`-Erweiterungsmethode auf, die vom Protokollierungsframework bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="b71ed-951">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="b71ed-952">Weitere Informationen finden Sie in der Dokumentation zum jeweiligen Anbieter.</span><span class="sxs-lookup"><span data-stu-id="b71ed-952">For more information, see each provider's documentation.</span></span> <span data-ttu-id="b71ed-953">Protokollierungsanbieter von Drittanbietern werden von Microsoft nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b71ed-953">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b71ed-954">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b71ed-954">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>

::: moniker-end
