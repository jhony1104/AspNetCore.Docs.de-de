---
title: Protokollieren in .NET Core und ASP.NET Core
author: rick-anderson
description: In diesem Artikel wird das Verwenden des Protokollierungsframeworks erläutert, das vom Microsoft.Extensions.Logging-NuGet-Paket bereitgestellt wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/19/2019
uid: fundamentals/logging/index
ms.openlocfilehash: b23e64077290f0f613e904651e4bb640fcbba95d
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289092"
---
# <a name="logging-in-net-core-and-aspnet-core"></a><span data-ttu-id="b491c-103">Protokollieren in .NET Core und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b491c-103">Logging in .NET Core and ASP.NET Core</span></span>

<span data-ttu-id="b491c-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b491c-104">By [Tom Dykstra](https://github.com/tdykstra) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b491c-105">.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="b491c-105">.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="b491c-106">Dieser Artikel zeigt, wie Sie die Protokollierungs-API mit integrierten Anbietern verwenden können.</span><span class="sxs-lookup"><span data-stu-id="b491c-106">This article shows how to use the logging API with built-in providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b491c-107">Die meisten der Codebeispiele in diesem Artikel stammen aus ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="b491c-107">Most of the code examples shown in this article are from ASP.NET Core apps.</span></span> <span data-ttu-id="b491c-108">Die auf die Protokollierung bezogenen Teile dieser Codeausschnitte gelten für jede .NET Core-App, die den [generischen Host](xref:fundamentals/host/generic-host) verwendet.</span><span class="sxs-lookup"><span data-stu-id="b491c-108">The logging-specific parts of these code snippets apply to any .NET Core app that uses the [Generic Host](xref:fundamentals/host/generic-host).</span></span> <span data-ttu-id="b491c-109">Weitere Informationen zum Verwenden des generischen Hosts in Nicht-Webkonsolen-Apps finden Sie unter [Gehostete Dienste](xref:fundamentals/host/hosted-services).</span><span class="sxs-lookup"><span data-stu-id="b491c-109">For information about how to use the Generic Host in non-web console apps, see [Hosted services](xref:fundamentals/host/hosted-services).</span></span>

<span data-ttu-id="b491c-110">Das Protokollieren von Code für Apps ohne generischen Host weicht in Bezug auf das [Hinzufügen von Anbietern](#add-providers) und das [Erstellen von Protokollierungen](#create-logs) ab.</span><span class="sxs-lookup"><span data-stu-id="b491c-110">Logging code for apps without Generic Host differs in the way [providers are added](#add-providers) and [loggers are created](#create-logs).</span></span> <span data-ttu-id="b491c-111">Codebeispiele ohne Host finden sich in diesen Abschnitten des Artikels.</span><span class="sxs-lookup"><span data-stu-id="b491c-111">Non-host code examples are shown in those sections of the article.</span></span>

::: moniker-end

<span data-ttu-id="b491c-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b491c-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="b491c-113">Hinzufügen von Anbietern</span><span class="sxs-lookup"><span data-stu-id="b491c-113">Add providers</span></span>

<span data-ttu-id="b491c-114">Ein Protokollierungsanbieter zeigt Protokolle an oder speichert diese.</span><span class="sxs-lookup"><span data-stu-id="b491c-114">A logging provider displays or stores logs.</span></span> <span data-ttu-id="b491c-115">Beispielsweise zeigt der Konsolenanbieter Protokolle in der Konsole an, und der Azure Application Insights-Anbieter speichert sie in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="b491c-115">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="b491c-116">Protokolle können durch das Hinzufügen mehrerer Anbieter an mehrere Ziele gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="b491c-116">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b491c-117">Um einen Anbieter in einer App hinzuzufügen, die den generischen Host verwendet, rufen Sie die `Add{provider name}`-Erweiterungsmethode des Anbieters in *Program.cs* auf:</span><span class="sxs-lookup"><span data-stu-id="b491c-117">To add a provider in an app that uses Generic Host, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

<span data-ttu-id="b491c-118">Rufen Sie in einer Konsolen-App ohne Host die `Add{provider name}`-Erweiterungsmethode des Anbieters auf, während Sie eine `LoggerFactory` erstellen:</span><span class="sxs-lookup"><span data-stu-id="b491c-118">In a non-host console app, call the provider's `Add{provider name}` extension method while creating a `LoggerFactory`:</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

<span data-ttu-id="b491c-119">`LoggerFactory` und `AddConsole` erfordern eine `using`-Anweisung für `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="b491c-119">`LoggerFactory` and `AddConsole` require a `using` statement for `Microsoft.Extensions.Logging`.</span></span>

<span data-ttu-id="b491c-120">Die ASP.NET-Standardprojektvorlagen rufen die Methode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A> auf, die die folgenden Protokollierungsanbieter hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="b491c-120">The default ASP.NET Core project templates call <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="b491c-121">Konsole</span><span class="sxs-lookup"><span data-stu-id="b491c-121">Console</span></span>
* <span data-ttu-id="b491c-122">Debug</span><span class="sxs-lookup"><span data-stu-id="b491c-122">Debug</span></span>
* <span data-ttu-id="b491c-123">EventSource</span><span class="sxs-lookup"><span data-stu-id="b491c-123">EventSource</span></span>
* <span data-ttu-id="b491c-124">EventLog (nur bei Ausführung unter Windows)</span><span class="sxs-lookup"><span data-stu-id="b491c-124">EventLog (only when running on Windows)</span></span>

<span data-ttu-id="b491c-125">Sie können die Standardanbieter durch Ihre eigene Auswahl ersetzen.</span><span class="sxs-lookup"><span data-stu-id="b491c-125">You can replace the default providers with your own choices.</span></span> <span data-ttu-id="b491c-126">Rufen Sie <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> auf, und fügen Sie die gewünschten Anbieter hinzu.</span><span class="sxs-lookup"><span data-stu-id="b491c-126">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

<span data-ttu-id="b491c-127">Um einen Anbieter hinzuzufügen, rufen Sie die `Add{provider name}`-Erweiterungsmethode des Anbieters in *Program.cs* auf:</span><span class="sxs-lookup"><span data-stu-id="b491c-127">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

<span data-ttu-id="b491c-128">Der vorstehende Code erfordert Verweise auf `Microsoft.Extensions.Logging` und `Microsoft.Extensions.Configuration`.</span><span class="sxs-lookup"><span data-stu-id="b491c-128">The preceding code requires references to `Microsoft.Extensions.Logging` and `Microsoft.Extensions.Configuration`.</span></span>

<span data-ttu-id="b491c-129">Die Standardprojektvorlage ruft die Methode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> auf, die die folgenden Protokollierungsanbieter hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="b491c-129">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="b491c-130">Konsole</span><span class="sxs-lookup"><span data-stu-id="b491c-130">Console</span></span>
* <span data-ttu-id="b491c-131">Debug</span><span class="sxs-lookup"><span data-stu-id="b491c-131">Debug</span></span>
* <span data-ttu-id="b491c-132">EventSource (beginnend mit ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="b491c-132">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="b491c-133">Bei Verwendung von `CreateDefaultBuilder` können Sie die Standardanbieter durch Ihre eigene Auswahl ersetzen.</span><span class="sxs-lookup"><span data-stu-id="b491c-133">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="b491c-134">Rufen Sie <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> auf, und fügen Sie die gewünschten Anbieter hinzu.</span><span class="sxs-lookup"><span data-stu-id="b491c-134">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

<span data-ttu-id="b491c-135">Informationen zu den [integrierten Protokollierungsanbietern](#built-in-logging-providers) sowie zu [Protokollierungsanbietern von Drittanbietern](#third-party-logging-providers) finden Sie weiter unten in diesem Artikel.</span><span class="sxs-lookup"><span data-stu-id="b491c-135">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="b491c-136">Erstellen von Protokollen</span><span class="sxs-lookup"><span data-stu-id="b491c-136">Create logs</span></span>

<span data-ttu-id="b491c-137">Verwenden Sie zum Erstellen von Protokollen ein <xref:Microsoft.Extensions.Logging.ILogger%601>-Objekt.</span><span class="sxs-lookup"><span data-stu-id="b491c-137">To create logs, use an <xref:Microsoft.Extensions.Logging.ILogger%601> object.</span></span> <span data-ttu-id="b491c-138">Rufen Sie in einer Web-App oder einem gehosteten Dienst einen `ILogger` aus der Abhängigkeitsinjektion ab.</span><span class="sxs-lookup"><span data-stu-id="b491c-138">In a web app or hosted service, get an `ILogger` from dependency injection (DI).</span></span> <span data-ttu-id="b491c-139">Verwenden Sie in Konsolen-Apps ohne Host `LoggerFactory`, um einen `ILogger` zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b491c-139">In non-host console apps, use the `LoggerFactory` to create an `ILogger`.</span></span>

<span data-ttu-id="b491c-140">Im folgenden ASP.NET Core-Beispiel wird eine Protokollierung mit `TodoApiSample.Pages.AboutModel` als Kategorie erstellt.</span><span class="sxs-lookup"><span data-stu-id="b491c-140">The following ASP.NET Core example creates a logger with `TodoApiSample.Pages.AboutModel` as the category.</span></span> <span data-ttu-id="b491c-141">Die Protokoll*kategorie* ist eine Zeichenfolge, die jedem Protokoll zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="b491c-141">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="b491c-142">Die von der Abhängigkeitsinjektion bereitgestellte `ILogger<T>`-Instanz erstellt Protokolle, die den vollqualifizierten Namen vom Typ `T` als Kategorie aufweisen.</span><span class="sxs-lookup"><span data-stu-id="b491c-142">The `ILogger<T>` instance provided by DI creates logs that have the fully qualified name of type `T` as the category.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

<span data-ttu-id="b491c-143">Im folgenden Beispiel einer Konsolen-App ohne Host wird eine Protokollierung mit `LoggingConsoleApp.Program` als Kategorie erstellt.</span><span class="sxs-lookup"><span data-stu-id="b491c-143">The following non-host console app example creates a logger with `LoggingConsoleApp.Program` as the category.</span></span>

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

<span data-ttu-id="b491c-144">In den folgenden Beispielen zu ASP.NET Core und einer Konsolen-App werden von der Protokollierung Protokolle mit dem Protokolliergrad `Information` erstellt.</span><span class="sxs-lookup"><span data-stu-id="b491c-144">In the following ASP.NET Core and console app examples, the logger is used to create logs with `Information` as the level.</span></span> <span data-ttu-id="b491c-145">Der Protokollierungs*grad* gibt den Schweregrad des protokollierten Ereignisses an.</span><span class="sxs-lookup"><span data-stu-id="b491c-145">The Log *level* indicates the severity of the logged event.</span></span> 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

<span data-ttu-id="b491c-146">[Grade](#log-level) und [Kategorien](#log-category) werden weiter unten in diesem Artikel ausführlicher erläutert.</span><span class="sxs-lookup"><span data-stu-id="b491c-146">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="b491c-147">Erstellen von Protokollen in der Program-Klasse</span><span class="sxs-lookup"><span data-stu-id="b491c-147">Create logs in the Program class</span></span>

<span data-ttu-id="b491c-148">Wenn Protokolle in die `Program`-Klasse einer ASP.NET Core-App geschrieben werden sollen, rufen Sie nach dem Erstellen des Hosts eine `ILogger`-Instanz aus der Abhängigkeitsinjektion ab:</span><span class="sxs-lookup"><span data-stu-id="b491c-148">To write logs in the `Program` class of an ASP.NET Core app, get an `ILogger` instance from DI after building the host:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="b491c-149">Die Protokollierung während der Hosterstellung wird nicht direkt unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b491c-149">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="b491c-150">Es kann jedoch eine separate Protokollierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b491c-150">However, a separate logger can be used.</span></span> <span data-ttu-id="b491c-151">Im folgenden Beispiel wird eine [Serilog](https://serilog.net/)-Protokollierung zum Anmelden von `CreateHostBuilder` verwendet.</span><span class="sxs-lookup"><span data-stu-id="b491c-151">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateHostBuilder`.</span></span> <span data-ttu-id="b491c-152">`AddSerilog` verwendet die in `Log.Logger` angegebene statische Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="b491c-152">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="create-logs-in-the-startup-class"></a><span data-ttu-id="b491c-153">Erstellen von Protokollen in der Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="b491c-153">Create logs in the Startup class</span></span>

<span data-ttu-id="b491c-154">Wenn Protokolle in die `Startup.Configure`-Methode einer ASP.NET Core-App geschrieben werden sollen, schließen Sie einen `ILogger`-Parameter in die Methodensignatur ein:</span><span class="sxs-lookup"><span data-stu-id="b491c-154">To write logs in the `Startup.Configure` method of an ASP.NET Core app, include an `ILogger` parameter in the method signature:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

<span data-ttu-id="b491c-155">Das Schreiben von Protokollen vor Abschluss der Einrichtung des Abhängigkeitsinjektion-Containers in der `Startup.ConfigureServices`-Methode wird nicht unterstützt:</span><span class="sxs-lookup"><span data-stu-id="b491c-155">Writing logs before completion of the DI container setup in the `Startup.ConfigureServices` method is not supported:</span></span>

* <span data-ttu-id="b491c-156">Die Protokollierungsinjektion in den `Startup`-Konstruktor wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b491c-156">Logger injection into the `Startup` constructor is not supported.</span></span>
* <span data-ttu-id="b491c-157">Die Protokollierungsinjektion in die `Startup.ConfigureServices`-Methodensignatur wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b491c-157">Logger injection into the `Startup.ConfigureServices` method signature is not supported</span></span>

<span data-ttu-id="b491c-158">Dies Einschränkung ist darauf zurückzuführen, dass die Protokollierung von der Abhängigkeitsinjektion und der Konfiguration abhängt (die wiederum von der Abhängigkeitsinjektion abhängt).</span><span class="sxs-lookup"><span data-stu-id="b491c-158">The reason for this restriction is that logging depends on DI and on configuration, which in turns depends on DI.</span></span> <span data-ttu-id="b491c-159">Der Abhängigkeitsinjektion-Container wird erst nach Fertigstellung von `ConfigureServices` eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="b491c-159">The DI container isn't set up until `ConfigureServices` finishes.</span></span>

<span data-ttu-id="b491c-160">Die Konstruktorinjektion einer Protokollierung in `Startup` funktioniert in früheren Versionen von ASP.NET Core, da für den Webhost ein separater Abhängigkeitsinjektion-Container erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-160">Constructor injection of a logger into `Startup` works in earlier versions of ASP.NET Core because a separate DI container is created for the Web Host.</span></span> <span data-ttu-id="b491c-161">Weitere Informationen dazu, warum nur ein Container für den generischen Host erstellt wird, erhalten Sie in der [Breaking Change-Ankündigung](https://github.com/aspnet/Announcements/issues/353).</span><span class="sxs-lookup"><span data-stu-id="b491c-161">For information about why only one container is created for the Generic Host, see the [breaking change announcement](https://github.com/aspnet/Announcements/issues/353).</span></span>

<span data-ttu-id="b491c-162">Wenn Sie einen Dienst konfigurieren müssen, der von `ILogger<T>` abhängt, können Sie dies immer noch mithilfe der Konstruktorinjektion oder durch Bereitstellen einer Factorymethode ausführen.</span><span class="sxs-lookup"><span data-stu-id="b491c-162">If you need to configure a service that depends on `ILogger<T>`, you can still do that by using constructor injection or by providing a factory method.</span></span> <span data-ttu-id="b491c-163">Die Nutzung einer Factorymethode wird nur empfohlen, wenn Sie keine andere Wahl haben.</span><span class="sxs-lookup"><span data-stu-id="b491c-163">The factory method approach is recommended only if there is no other option.</span></span> <span data-ttu-id="b491c-164">Angenommen, Sie müssen eine Eigenschaft mit einem Dienst aus der Abhängigkeitsinjektion füllen:</span><span class="sxs-lookup"><span data-stu-id="b491c-164">For example, suppose you need to fill a property with a service from DI:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

<span data-ttu-id="b491c-165">Der hervorgehobene Code oben ist eine `Func`, die ausgeführt wird, wenn der Abhängigkeitsinjektion-Container eine Instanz von `MyService` erstellen muss.</span><span class="sxs-lookup"><span data-stu-id="b491c-165">The preceding highlighted code is a `Func` that runs the first time the DI container needs to construct an instance of `MyService`.</span></span> <span data-ttu-id="b491c-166">Auf diese Weise können Sie auf alle registrierten Dienste zugreifen.</span><span class="sxs-lookup"><span data-stu-id="b491c-166">You can access any of the registered services in this way.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="b491c-167">Erstellen von Protokollen in der Startklasse</span><span class="sxs-lookup"><span data-stu-id="b491c-167">Create logs in Startup</span></span>

<span data-ttu-id="b491c-168">Zum Schreiben von Protokollen in der `Startup`-Klasse schließen Sie einen `ILogger`-Parameter in die Konstruktorsignatur ein:</span><span class="sxs-lookup"><span data-stu-id="b491c-168">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a><span data-ttu-id="b491c-169">Erstellen von Protokollen in der Program-Klasse</span><span class="sxs-lookup"><span data-stu-id="b491c-169">Create logs in the Program class</span></span>

<span data-ttu-id="b491c-170">Zum Schreiben von Protokollen in der `Program`-Klasse rufen Sie eine `ILogger`-Instanz aus DI ab:</span><span class="sxs-lookup"><span data-stu-id="b491c-170">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

<span data-ttu-id="b491c-171">Die Protokollierung während der Hosterstellung wird nicht direkt unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b491c-171">Logging during host construction isn't directly supported.</span></span> <span data-ttu-id="b491c-172">Es kann jedoch eine separate Protokollierung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b491c-172">However, a separate logger can be used.</span></span> <span data-ttu-id="b491c-173">Im folgenden Beispiel wird eine [Serilog](https://serilog.net/)-Protokollierung zum Anmelden von `CreateWebHostBuilder` verwendet.</span><span class="sxs-lookup"><span data-stu-id="b491c-173">In the following example, a [Serilog](https://serilog.net/) logger is used to log in `CreateWebHostBuilder`.</span></span> <span data-ttu-id="b491c-174">`AddSerilog` verwendet die in `Log.Logger` angegebene statische Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="b491c-174">`AddSerilog` uses the static configuration specified in `Log.Logger`:</span></span>

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

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="b491c-175">Keine asynchronen Protokollierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="b491c-175">No asynchronous logger methods</span></span>

<span data-ttu-id="b491c-176">Die Protokollierung sollte so schnell erfolgen, dass sich die Leistungskosten von asynchronem Code nicht lohnen.</span><span class="sxs-lookup"><span data-stu-id="b491c-176">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="b491c-177">Wenn Ihr Protokollierungsdatenspeicher langsam ist, schreiben Sie nicht direkt in ihn.</span><span class="sxs-lookup"><span data-stu-id="b491c-177">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="b491c-178">Erwägen Sie, die Protokollnachrichten zunächst in einen schnellen Speicher zu schreiben und sie dann später in den langsamen Speicher zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="b491c-178">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="b491c-179">Wenn Sie beispielsweise in SQL Server protokollieren, möchten Sie dies nicht direkt in einer `Log`-Methode tun, da die `Log`-Methoden synchron sind.</span><span class="sxs-lookup"><span data-stu-id="b491c-179">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="b491c-180">Fügen Sie stattdessen die Protokollmeldungen synchron zu einer Warteschlange im Arbeitsspeicher hinzu, und rufen Sie mithilfe eines Hintergrundworkers die Nachrichten aus der Warteschlange ab, um sie dann asynchron per Pushvorgang an SQL Server zu übertragen.</span><span class="sxs-lookup"><span data-stu-id="b491c-180">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span>

## <a name="configuration"></a><span data-ttu-id="b491c-181">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b491c-181">Configuration</span></span>

<span data-ttu-id="b491c-182">Die Konfiguration von Protokollierungsanbietern erfolgt durch mindestens einen Konfigurationsanbieter:</span><span class="sxs-lookup"><span data-stu-id="b491c-182">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="b491c-183">Dateiformate (INI, JSON und XML)</span><span class="sxs-lookup"><span data-stu-id="b491c-183">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="b491c-184">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="b491c-184">Command-line arguments.</span></span>
* <span data-ttu-id="b491c-185">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="b491c-185">Environment variables.</span></span>
* <span data-ttu-id="b491c-186">Speicherinterne .NET-Objekte</span><span class="sxs-lookup"><span data-stu-id="b491c-186">In-memory .NET objects.</span></span>
* <span data-ttu-id="b491c-187">Der unverschlüsselte Speicher von [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="b491c-187">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="b491c-188">Ein unverschlüsselter Benutzerspeicher, z.B. [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="b491c-188">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="b491c-189">Benutzerdefinierte Anbieter (installiert oder erstellt).</span><span class="sxs-lookup"><span data-stu-id="b491c-189">Custom providers (installed or created).</span></span>

<span data-ttu-id="b491c-190">Die Konfiguration der Protokollierung wird meist vom `Logging`-Abschnitt von Anwendungseinstellungsdateien angegeben.</span><span class="sxs-lookup"><span data-stu-id="b491c-190">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="b491c-191">Im folgenden Beispiel werden die Inhalte einer herkömmlichen *appsettings.Development.json*-Datei veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="b491c-191">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="b491c-192">Die `Logging`-Eigenschaft kann den `LogLevel` und Protokollanbietereigenschaften beinhalten (Konsole wird angezeigt).</span><span class="sxs-lookup"><span data-stu-id="b491c-192">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="b491c-193">Die `LogLevel`-Eigenschaft unter `Logging` gibt den Mindest[grad](#log-level) an, der für ausgewählte Kategorien protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="b491c-193">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="b491c-194">Im Beispiel protokollieren die Kategorien `System` und `Microsoft` mit dem Grad `Information` und alle anderen Kategorien mit dem Grad `Debug`.</span><span class="sxs-lookup"><span data-stu-id="b491c-194">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="b491c-195">Weitere Eigenschaften unter `Logging` geben Protokollierungsanbieter an.</span><span class="sxs-lookup"><span data-stu-id="b491c-195">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="b491c-196">Das Beispiel bezieht sich auf den Konsolenanbieter.</span><span class="sxs-lookup"><span data-stu-id="b491c-196">The example is for the Console provider.</span></span> <span data-ttu-id="b491c-197">Wenn ein Anbieter [Protokollbereiche](#log-scopes) unterstützt, gibt `IncludeScopes` an, ob sie aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="b491c-197">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="b491c-198">Eine Anbietereigenschaft (z.B. `Console` im Beispiel) kann auch eine `LogLevel`-Eigenschaft angeben.</span><span class="sxs-lookup"><span data-stu-id="b491c-198">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="b491c-199">`LogLevel` unter einem Anbieter gibt die Grade an, die für diesen Anbieter protokolliert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="b491c-199">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="b491c-200">Wenn Grade in `Logging.{providername}.LogLevel` angegeben werden, überschreiben sie alle Angaben in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="b491c-200">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

<span data-ttu-id="b491c-201">Die Protokollierungs-API umfasst kein Szenario zum Ändern der Protokollebene, während eine App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-201">The Logging API doesn't include a scenario to change log levels while an app is running.</span></span> <span data-ttu-id="b491c-202">Einige Konfigurationsanbieter können jedoch die Konfiguration erneut laden, was sich unmittelbar auf die Protokollierungskonfiguration auswirkt.</span><span class="sxs-lookup"><span data-stu-id="b491c-202">However, some configuration providers are capable of reloading configuration, which takes immediate effect on logging configuration.</span></span> <span data-ttu-id="b491c-203">Beispielsweise lädt der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider), der durch `CreateDefaultBuilder` zum Lesen von Einstellungsdateien hinzugefügt wird, die Protokollierungskonfiguration standardmäßig erneut.</span><span class="sxs-lookup"><span data-stu-id="b491c-203">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider), which is added by `CreateDefaultBuilder` to read settings files, reloads logging configuration by default.</span></span> <span data-ttu-id="b491c-204">Wenn die Konfiguration im Code geändert wird, während eine App ausgeführt wird, kann die App [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) aufrufen, um ihre Protokollierungskonfiguration zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b491c-204">If configuration is changed in code while an app is running, the app can call [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) to update the app's logging configuration.</span></span>

<span data-ttu-id="b491c-205">Informationen zur Implementierung von Konfigurationsanbieter finden Sie hier: <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="b491c-205">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="b491c-206">Beispiel einer Protokollierungsausgabe</span><span class="sxs-lookup"><span data-stu-id="b491c-206">Sample logging output</span></span>

<span data-ttu-id="b491c-207">Mit dem im vorherigen Abschnitt gezeigten Beispielcode werden Protokolle in der Konsole angezeigt, wenn die Anwendung über die Befehlszeile ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-207">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="b491c-208">Hier ein Beispiel für eine Konsolenausgabe:</span><span class="sxs-lookup"><span data-stu-id="b491c-208">Here's an example of console output:</span></span>

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

<span data-ttu-id="b491c-209">Die vorherigen Protokolle wurden generiert, indem eine HTTP Get-Anforderung an die Beispiel-App unter `http://localhost:5000/api/todo/0` vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="b491c-209">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="b491c-210">Nachfolgend sehen Sie, wie die gleichen Protokolle im Debugfenster angezeigt werden, wenn Sie die Beispiel-App in Visual Studio ausführen:</span><span class="sxs-lookup"><span data-stu-id="b491c-210">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

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

<span data-ttu-id="b491c-211">Die über die `ILogger`-Aufrufe aus dem vorherigen Abschnitt erstellten Protokolle beginnen mit „TodoApiSample“.</span><span class="sxs-lookup"><span data-stu-id="b491c-211">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApiSample".</span></span> <span data-ttu-id="b491c-212">Protokolle, die mit Microsoft-Kategorien beginnen, stammen aus ASP.NET Core-Frameworkcode.</span><span class="sxs-lookup"><span data-stu-id="b491c-212">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="b491c-213">ASP.NET Core und Anwendungscode verwenden dieselbe Protokollierungs-API und dieselben Protokollierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="b491c-213">ASP.NET Core and application code are using the same logging API and providers.</span></span>

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

<span data-ttu-id="b491c-214">Die über die `ILogger`-Aufrufe aus dem vorherigen Abschnitt erstellten Protokolle beginnen mit „TodoApi“.</span><span class="sxs-lookup"><span data-stu-id="b491c-214">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi".</span></span> <span data-ttu-id="b491c-215">Protokolle, die mit Microsoft-Kategorien beginnen, stammen aus ASP.NET Core-Frameworkcode.</span><span class="sxs-lookup"><span data-stu-id="b491c-215">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="b491c-216">ASP.NET Core und Anwendungscode verwenden dieselbe Protokollierungs-API und dieselben Protokollierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="b491c-216">ASP.NET Core and application code are using the same logging API and providers.</span></span>

::: moniker-end

<span data-ttu-id="b491c-217">In den übrigen Abschnitten dieses Artikels werden einige Details und Optionen für die Protokollierung erläutert.</span><span class="sxs-lookup"><span data-stu-id="b491c-217">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="b491c-218">NuGet-Pakete</span><span class="sxs-lookup"><span data-stu-id="b491c-218">NuGet packages</span></span>

<span data-ttu-id="b491c-219">Die Schnittstellen `ILogger` und `ILoggerFactory` befinden sich in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), ihre Standardimplementierungen sind in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) enthalten.</span><span class="sxs-lookup"><span data-stu-id="b491c-219">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="b491c-220">Protokollkategorie</span><span class="sxs-lookup"><span data-stu-id="b491c-220">Log category</span></span>

<span data-ttu-id="b491c-221">Wenn ein `ILogger`-Objekt erstellt wird, wird eine *Kategorie* für dieses Objekt angegeben.</span><span class="sxs-lookup"><span data-stu-id="b491c-221">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="b491c-222">Diese Kategorie ist in jeder Protokollmeldung enthalten, die von dieser Instanz von `ILogger` erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-222">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="b491c-223">Die Kategorie kann eine beliebige Zeichenfolge sein, aber die Konvention besteht in der Verwendung des Klassennamens, z.B. „TodoApi.Controllers.TodoController“.</span><span class="sxs-lookup"><span data-stu-id="b491c-223">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="b491c-224">Verwenden Sie `ILogger<T>` zum Abrufen einer `ILogger`-Instanz, die den vollqualifizierten Typnamen von `T` als Kategorie verwendet:</span><span class="sxs-lookup"><span data-stu-id="b491c-224">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="b491c-225">Um die Kategorie explizit anzugeben, rufen Sie `ILoggerFactory.CreateLogger` auf:</span><span class="sxs-lookup"><span data-stu-id="b491c-225">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="b491c-226">`ILogger<T>` entspricht dem Aufruf von `CreateLogger` mit dem vollqualifizierten Typnamen `T`.</span><span class="sxs-lookup"><span data-stu-id="b491c-226">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="b491c-227">Protokolliergrad</span><span class="sxs-lookup"><span data-stu-id="b491c-227">Log level</span></span>

<span data-ttu-id="b491c-228">Jedes Protokoll gibt einen <xref:Microsoft.Extensions.Logging.LogLevel>-Wert an.</span><span class="sxs-lookup"><span data-stu-id="b491c-228">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="b491c-229">Der Protokolliergrad gibt den Schweregrad oder die Wichtigkeit an.</span><span class="sxs-lookup"><span data-stu-id="b491c-229">The log level indicates the severity or importance.</span></span> <span data-ttu-id="b491c-230">Sie können beispielsweise ein `Information`-Protokoll schreiben, wenn eine Methode normal endet, und ein `Warning`-Protokoll, wenn eine Methode den Statuscode *404 Not Found* zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="b491c-230">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="b491c-231">Der folgende Code erstellt `Information`- und `Warning`-Protokolle:</span><span class="sxs-lookup"><span data-stu-id="b491c-231">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="b491c-232">Im Code oben ist der erste Parameter die [Protokollereignis-ID](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="b491c-232">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="b491c-233">Der zweite Parameter ist eine Meldungsvorlage mit Platzhaltern für Argumentwerte, die von den verbleibenden Methodenparametern bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b491c-233">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="b491c-234">Die Methodenparameter werden im [Abschnitt „Meldungsvorlage“](#log-message-template) später in diesem Artikel erläutert.</span><span class="sxs-lookup"><span data-stu-id="b491c-234">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="b491c-235">Protokollmethoden, die den Protokolliergrad im Methodennamen enthalten (z.B. `LogInformation` und `LogWarning`), sind [Erweiterungsmethoden für ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="b491c-235">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="b491c-236">Diese Methoden rufen eine `Log`-Methode auf, die einen `LogLevel`-Parameter annimmt.</span><span class="sxs-lookup"><span data-stu-id="b491c-236">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="b491c-237">Sie können anstelle eines Aufrufs dieser Erweiterungsmethoden die `Log`-Methode direkt aufrufen, aber die Syntax ist relativ kompliziert.</span><span class="sxs-lookup"><span data-stu-id="b491c-237">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="b491c-238">Weitere Informationen finden Sie unter <xref:Microsoft.Extensions.Logging.ILogger> und im [Quellcode für Protokollierungserweiterungen](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="b491c-238">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="b491c-239">ASP.NET Core definiert die folgenden Protokolliergrade. Die Reihenfolge reicht vom geringsten bis zum höchsten Schweregrad.</span><span class="sxs-lookup"><span data-stu-id="b491c-239">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="b491c-240">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="b491c-240">Trace = 0</span></span>

  <span data-ttu-id="b491c-241">Protokolliert Informationen, die in der Regel nur für das Debuggen nützlich sind.</span><span class="sxs-lookup"><span data-stu-id="b491c-241">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="b491c-242">Diese Meldungen können sensible Anwendungsdaten enthalten und sollten daher nicht in einer Produktionsumgebung aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="b491c-242">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="b491c-243">*Standardmäßig deaktiviert.*</span><span class="sxs-lookup"><span data-stu-id="b491c-243">*Disabled by default.*</span></span>

* <span data-ttu-id="b491c-244">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="b491c-244">Debug = 1</span></span>

  <span data-ttu-id="b491c-245">Protokolliert Informationen, die bei der Entwicklung und beim Debuggen hilfreich sein können.</span><span class="sxs-lookup"><span data-stu-id="b491c-245">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="b491c-246">Beispiel: `Entering method Configure with flag set to true.` Aktivieren Sie Protokolle mit dem Protokolliergrad `Debug` aufgrund des hohen Protokollaufkommens nur zur Problembehandlung in der Produktion.</span><span class="sxs-lookup"><span data-stu-id="b491c-246">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="b491c-247">Information = 2</span><span class="sxs-lookup"><span data-stu-id="b491c-247">Information = 2</span></span>

  <span data-ttu-id="b491c-248">Zur Nachverfolgung des allgemeinen Ablaufs der App.</span><span class="sxs-lookup"><span data-stu-id="b491c-248">For tracking the general flow of the app.</span></span> <span data-ttu-id="b491c-249">Diese Protokolle haben typischerweise einen langfristigen Nutzen.</span><span class="sxs-lookup"><span data-stu-id="b491c-249">These logs typically have some long-term value.</span></span> <span data-ttu-id="b491c-250">Ein Beispiel: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="b491c-250">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="b491c-251">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="b491c-251">Warning = 3</span></span>

  <span data-ttu-id="b491c-252">Für ungewöhnliche oder unerwartete Ereignisse im App-Verlauf.</span><span class="sxs-lookup"><span data-stu-id="b491c-252">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="b491c-253">Dazu können Fehler oder andere Bedingungen gehören, die zwar nicht zum Beenden der App führen, aber eventuell untersucht werden müssen.</span><span class="sxs-lookup"><span data-stu-id="b491c-253">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="b491c-254">Behandelte Ausnahmen sind eine typische Verwendung für den Protokolliergrad `Warning`.</span><span class="sxs-lookup"><span data-stu-id="b491c-254">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="b491c-255">Ein Beispiel: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="b491c-255">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="b491c-256">Error = 4</span><span class="sxs-lookup"><span data-stu-id="b491c-256">Error = 4</span></span>

  <span data-ttu-id="b491c-257">Für Fehler und Ausnahmen, die nicht behandelt werden können.</span><span class="sxs-lookup"><span data-stu-id="b491c-257">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="b491c-258">Diese Meldungen weisen auf einen Fehler in der aktuellen Aktivität oder im aktuellen Vorgang (z.B. in der aktuellen HTTP-Anforderung) und nicht auf einen anwendungsweiten Fehler hin.</span><span class="sxs-lookup"><span data-stu-id="b491c-258">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="b491c-259">Beispielprotokollmeldung: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="b491c-259">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="b491c-260">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="b491c-260">Critical = 5</span></span>

  <span data-ttu-id="b491c-261">Für Fehler, die sofortige Aufmerksamkeit erfordern.</span><span class="sxs-lookup"><span data-stu-id="b491c-261">For failures that require immediate attention.</span></span> <span data-ttu-id="b491c-262">Beispiel: Szenarios mit Datenverlust, Speichermangel.</span><span class="sxs-lookup"><span data-stu-id="b491c-262">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="b491c-263">Verwenden Sie den Protokolliergrad, um die Menge an Protokollausgabedaten zu steuern, die in ein bestimmtes Speichermedium geschrieben oder an ein Anzeigefenster ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b491c-263">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="b491c-264">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b491c-264">For example:</span></span>

* <span data-ttu-id="b491c-265">In einer Produktionsumgebung</span><span class="sxs-lookup"><span data-stu-id="b491c-265">In production:</span></span>
  * <span data-ttu-id="b491c-266">Das Protokollieren von `Trace` über die `Information`-Ebenen verursacht viele detaillierte Protokollmeldungen.</span><span class="sxs-lookup"><span data-stu-id="b491c-266">Logging at the `Trace` through `Information` levels produces a high-volume of detailed log messages.</span></span> <span data-ttu-id="b491c-267">Protokollieren Sie `Trace` über Nachrichten auf `Information`-Ebene in einem kostengünstigen Speicher mit hohem Volumen, um die Kosten zu überwachen und die Grenzwerte des Datenspeichers nicht zu überschreiten.</span><span class="sxs-lookup"><span data-stu-id="b491c-267">To control costs and not exceed data storage limits, log `Trace` through `Information` level messages to a high-volume, low-cost data store.</span></span>
  * <span data-ttu-id="b491c-268">Das Protokollieren von `Warning` über die `Critical`-Ebenen verursacht weniger und kürzere Protokollmeldungen.</span><span class="sxs-lookup"><span data-stu-id="b491c-268">Logging at `Warning` through `Critical` levels typically produces fewer, smaller log messages.</span></span> <span data-ttu-id="b491c-269">Daher müssen Sie sich über Kosten- und Speichergrenzwerte keine Sorgen machen und sind bei der Auswahl des Datenspeichers flexibler.</span><span class="sxs-lookup"><span data-stu-id="b491c-269">Therefore, costs and storage limits usually aren't a concern, which results in greater flexibility of data store choice.</span></span>
* <span data-ttu-id="b491c-270">Entwicklungsphase:</span><span class="sxs-lookup"><span data-stu-id="b491c-270">During development:</span></span>
  * <span data-ttu-id="b491c-271">Protokollieren Sie `Warning` über `Critical`-Meldungen an der Konsole.</span><span class="sxs-lookup"><span data-stu-id="b491c-271">Log `Warning` through `Critical` messages to the console.</span></span>
  * <span data-ttu-id="b491c-272">Fügen Sie bei der Fehlerbehebung `Trace` über `Information`-Meldungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="b491c-272">Add `Trace` through `Information` messages when troubleshooting.</span></span>

<span data-ttu-id="b491c-273">Im Abschnitt [Protokollfilterung](#log-filtering) weiter unten in diesem Artikel wird erläutert, wie Sie steuern, welche Protokolliergrade ein Anbieter verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="b491c-273">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="b491c-274">ASP.NET Core schreibt Protokolle für Frameworkereignisse.</span><span class="sxs-lookup"><span data-stu-id="b491c-274">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="b491c-275">Die zuvor in diesem Artikel gezeigten Protokollbeispiele enthielten Protokolle unterhalb des Protokolliergrads `Information`, deshalb wurden keine Protokolle des Protokolliergrads `Debug` oder `Trace` erstellt.</span><span class="sxs-lookup"><span data-stu-id="b491c-275">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="b491c-276">Hier ist ein Beispiel für Konsolenprotokolle, die durch Ausführen der Beispiel-App generiert werden, die so konfiguriert ist, dass sie `Debug`-Protokolle anzeigt:</span><span class="sxs-lookup"><span data-stu-id="b491c-276">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="b491c-277">Protokollereignis-ID</span><span class="sxs-lookup"><span data-stu-id="b491c-277">Log event ID</span></span>

<span data-ttu-id="b491c-278">Jedes Protokoll kann eine *Ereignis-ID* angeben.</span><span class="sxs-lookup"><span data-stu-id="b491c-278">Each log can specify an *event ID*.</span></span> <span data-ttu-id="b491c-279">Die Beispiel-App verwendet hierzu eine lokal definierte `LoggingEvents`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="b491c-279">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="b491c-280">Eine Ereignis-ID ordnet eine Gruppe von Ereignissen zu.</span><span class="sxs-lookup"><span data-stu-id="b491c-280">An event ID associates a set of events.</span></span> <span data-ttu-id="b491c-281">Beispielsweise können alle Protokolle, die sich auf die Anzeige einer Liste von Elementen auf einer Seite beziehen, 1001 sein.</span><span class="sxs-lookup"><span data-stu-id="b491c-281">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="b491c-282">Der Protokollierungsanbieter kann die Ereignis-ID in einem ID-Feld, in der Protokollierungsmeldung oder gar nicht speichern.</span><span class="sxs-lookup"><span data-stu-id="b491c-282">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="b491c-283">Der Debuganbieter zeigt keine Ereignis-IDs an.</span><span class="sxs-lookup"><span data-stu-id="b491c-283">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="b491c-284">Der Konsolenanbieter zeigt Ereignis-IDs in Klammern hinter der Kategorie an:</span><span class="sxs-lookup"><span data-stu-id="b491c-284">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="b491c-285">Protokollmeldungsvorlage</span><span class="sxs-lookup"><span data-stu-id="b491c-285">Log message template</span></span>

<span data-ttu-id="b491c-286">Jedes Protokoll gibt eine Meldungsvorlage an.</span><span class="sxs-lookup"><span data-stu-id="b491c-286">Each log specifies a message template.</span></span> <span data-ttu-id="b491c-287">Die Meldungsvorlage kann Platzhalter enthalten, für die Argumente bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b491c-287">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="b491c-288">Verwenden Sie Namen für die Platzhalter, keine Zahlen.</span><span class="sxs-lookup"><span data-stu-id="b491c-288">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="b491c-289">Die Reihenfolge der Platzhalter – nicht ihre Namen – bestimmt, welche Parameter verwendet werden, um ihre Werte zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="b491c-289">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="b491c-290">Beachten Sie im folgenden Code, dass die Parameternamen in der Meldungsvorlage nicht in der richtigen Reihenfolge vorhanden sind:</span><span class="sxs-lookup"><span data-stu-id="b491c-290">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="b491c-291">Dieser Code erstellt eine Protokollierungsmeldung mit den Parameterwerten in der richtigen Reihenfolge:</span><span class="sxs-lookup"><span data-stu-id="b491c-291">This code creates a log message with the parameter values in sequence:</span></span>

```text
Parameter values: parm1, parm2
```

<span data-ttu-id="b491c-292">Das Protokollierungsframework funktioniert auf diese Weise, damit Protokollierungsanbieter [semantische Protokollierung, die auch als strukturierte Protokollierung bezeichnet wird](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging), implementieren können.</span><span class="sxs-lookup"><span data-stu-id="b491c-292">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="b491c-293">Die Argumente selbst (nicht nur die formatierte Meldungsvorlage) werden an das Protokollierungssystem übergeben.</span><span class="sxs-lookup"><span data-stu-id="b491c-293">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="b491c-294">Diese Informationen ermöglichen es Protokollierungsanbietern, die Parameterwerte als Felder zu speichern.</span><span class="sxs-lookup"><span data-stu-id="b491c-294">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="b491c-295">Nehmen wir zum Beispiel an, dass Aufrufe von Protokollierungsmethoden folgendermaßen aussehen:</span><span class="sxs-lookup"><span data-stu-id="b491c-295">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="b491c-296">Wenn Sie die Protokolle an Azure Table Storage senden, kann jede Azure Table-Entität die Eigenschaften `ID` und `RequestTime` aufweisen. Dies vereinfacht die Abfrage von Protokolldaten.</span><span class="sxs-lookup"><span data-stu-id="b491c-296">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="b491c-297">Eine Abfrage kann alle Protokolle in einem bestimmten `RequestTime`-Bereich finden, ohne die Uhrzeit aus den Textmeldungen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="b491c-297">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="b491c-298">Protokollieren von Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="b491c-298">Logging exceptions</span></span>

<span data-ttu-id="b491c-299">Die Protokollierungsmethoden umfassen Überladungen, die das Übergeben von Ausnahmen ermöglichen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="b491c-299">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="b491c-300">Die verschiedenen Anbieter verarbeiten die Ausnahmeinformationen auf unterschiedliche Weise.</span><span class="sxs-lookup"><span data-stu-id="b491c-300">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="b491c-301">Hier sehen Sie ein Beispiel einer Debuganbieterausgabe aus dem obigen Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="b491c-301">Here's an example of Debug provider output from the code shown above.</span></span>

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="b491c-302">Protokollfilterung</span><span class="sxs-lookup"><span data-stu-id="b491c-302">Log filtering</span></span>

<span data-ttu-id="b491c-303">Sie können einen Mindestprotokolliergrad für einen bestimmten Anbieter und eine bestimmte Kategorie oder für alle Anbieter oder alle Kategorien festlegen.</span><span class="sxs-lookup"><span data-stu-id="b491c-303">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="b491c-304">Alle Protokolle unter dem Mindestgrad werden nicht an diesen Anbieter weitergeleitet, sodass sie nicht angezeigt oder gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="b491c-304">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="b491c-305">Wenn Sie alle Protokolle unterdrücken möchten, geben Sie `LogLevel.None` als Mindestprotokolliergrad an.</span><span class="sxs-lookup"><span data-stu-id="b491c-305">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="b491c-306">Der ganzzahlige Wert von `LogLevel.None` lautet 6 und liegt über `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="b491c-306">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="b491c-307">Erstellen von Filterregeln in der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b491c-307">Create filter rules in configuration</span></span>

<span data-ttu-id="b491c-308">Der Projektvorlagencode ruft `CreateDefaultBuilder` auf, um die Protokollierung für die Konsolen-, Debug- und EventSource-Anbieter (ASP.NET Core 2.2 oder höher) einzurichten.</span><span class="sxs-lookup"><span data-stu-id="b491c-308">The project template code calls `CreateDefaultBuilder` to set up logging for the Console, Debug, and EventSource (ASP.NET Core 2.2 or later) providers.</span></span> <span data-ttu-id="b491c-309">Die `CreateDefaultBuilder`-Methode richtet die Protokollierung ein, um nach der Konfiguration in einem `Logging`-Abschnitt zu suchen; dies wird [weiter oben in diesem Artikel](#configuration) erläutert.</span><span class="sxs-lookup"><span data-stu-id="b491c-309">The `CreateDefaultBuilder` method sets up logging to look for configuration in a `Logging` section, as explained [earlier in this article](#configuration).</span></span>

<span data-ttu-id="b491c-310">Die Konfigurationsdaten geben die Mindestprotokolliergrade nach Anbieter und Kategorie an, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="b491c-310">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

<span data-ttu-id="b491c-311">Diese JSON-Konfiguration erstellt sechs Filterregeln: eine für den Debuganbieter, vier für den Konsolenanbieter und eine für alle Anbieter.</span><span class="sxs-lookup"><span data-stu-id="b491c-311">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="b491c-312">Eine einzelne Regel wird für jeden Anbieter ausgewählt, wenn ein `ILogger`-Objekt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-312">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="b491c-313">Filterregeln im Code</span><span class="sxs-lookup"><span data-stu-id="b491c-313">Filter rules in code</span></span>

<span data-ttu-id="b491c-314">Das folgende Beispiel zeigt, wie Filterregeln im Code registriert werden:</span><span class="sxs-lookup"><span data-stu-id="b491c-314">The following example shows how to register filter rules in code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

<span data-ttu-id="b491c-315">Das zweite `AddFilter` gibt den Debuganbieter durch Verwendung des zugehörigen Typnamens an.</span><span class="sxs-lookup"><span data-stu-id="b491c-315">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="b491c-316">Das erste `AddFilter` gilt für alle Anbieter, weil kein Anbietertyp angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-316">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="b491c-317">Anwendung von Filterregeln</span><span class="sxs-lookup"><span data-stu-id="b491c-317">How filtering rules are applied</span></span>

<span data-ttu-id="b491c-318">Die Konfigurationsdaten und der in den vorangegangenen Beispielen gezeigte `AddFilter`-Code erzeugen die in der folgenden Tabelle gezeigten Regeln.</span><span class="sxs-lookup"><span data-stu-id="b491c-318">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="b491c-319">Die ersten sechs Regeln stammen aus dem Konfigurationsbeispiel, die letzten zwei Filter stammen aus dem Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="b491c-319">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="b491c-320">Anzahl</span><span class="sxs-lookup"><span data-stu-id="b491c-320">Number</span></span> | <span data-ttu-id="b491c-321">Anbieter</span><span class="sxs-lookup"><span data-stu-id="b491c-321">Provider</span></span>      | <span data-ttu-id="b491c-322">Kategorien beginnend mit...</span><span class="sxs-lookup"><span data-stu-id="b491c-322">Categories that begin with ...</span></span>          | <span data-ttu-id="b491c-323">Mindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="b491c-323">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="b491c-324">1</span><span class="sxs-lookup"><span data-stu-id="b491c-324">1</span></span>      | <span data-ttu-id="b491c-325">Debug</span><span class="sxs-lookup"><span data-stu-id="b491c-325">Debug</span></span>         | <span data-ttu-id="b491c-326">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="b491c-326">All categories</span></span>                          | <span data-ttu-id="b491c-327">Information</span><span class="sxs-lookup"><span data-stu-id="b491c-327">Information</span></span>       |
| <span data-ttu-id="b491c-328">2</span><span class="sxs-lookup"><span data-stu-id="b491c-328">2</span></span>      | <span data-ttu-id="b491c-329">Konsole</span><span class="sxs-lookup"><span data-stu-id="b491c-329">Console</span></span>       | <span data-ttu-id="b491c-330">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="b491c-330">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="b491c-331">Warnung</span><span class="sxs-lookup"><span data-stu-id="b491c-331">Warning</span></span>           |
| <span data-ttu-id="b491c-332">3</span><span class="sxs-lookup"><span data-stu-id="b491c-332">3</span></span>      | <span data-ttu-id="b491c-333">Konsole</span><span class="sxs-lookup"><span data-stu-id="b491c-333">Console</span></span>       | <span data-ttu-id="b491c-334">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="b491c-334">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="b491c-335">Debug</span><span class="sxs-lookup"><span data-stu-id="b491c-335">Debug</span></span>             |
| <span data-ttu-id="b491c-336">4</span><span class="sxs-lookup"><span data-stu-id="b491c-336">4</span></span>      | <span data-ttu-id="b491c-337">Konsole</span><span class="sxs-lookup"><span data-stu-id="b491c-337">Console</span></span>       | <span data-ttu-id="b491c-338">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="b491c-338">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="b491c-339">Fehler</span><span class="sxs-lookup"><span data-stu-id="b491c-339">Error</span></span>             |
| <span data-ttu-id="b491c-340">5</span><span class="sxs-lookup"><span data-stu-id="b491c-340">5</span></span>      | <span data-ttu-id="b491c-341">Konsole</span><span class="sxs-lookup"><span data-stu-id="b491c-341">Console</span></span>       | <span data-ttu-id="b491c-342">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="b491c-342">All categories</span></span>                          | <span data-ttu-id="b491c-343">Information</span><span class="sxs-lookup"><span data-stu-id="b491c-343">Information</span></span>       |
| <span data-ttu-id="b491c-344">6</span><span class="sxs-lookup"><span data-stu-id="b491c-344">6</span></span>      | <span data-ttu-id="b491c-345">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="b491c-345">All providers</span></span> | <span data-ttu-id="b491c-346">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="b491c-346">All categories</span></span>                          | <span data-ttu-id="b491c-347">Debug</span><span class="sxs-lookup"><span data-stu-id="b491c-347">Debug</span></span>             |
| <span data-ttu-id="b491c-348">7</span><span class="sxs-lookup"><span data-stu-id="b491c-348">7</span></span>      | <span data-ttu-id="b491c-349">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="b491c-349">All providers</span></span> | <span data-ttu-id="b491c-350">System</span><span class="sxs-lookup"><span data-stu-id="b491c-350">System</span></span>                                  | <span data-ttu-id="b491c-351">Debug</span><span class="sxs-lookup"><span data-stu-id="b491c-351">Debug</span></span>             |
| <span data-ttu-id="b491c-352">8</span><span class="sxs-lookup"><span data-stu-id="b491c-352">8</span></span>      | <span data-ttu-id="b491c-353">Debug</span><span class="sxs-lookup"><span data-stu-id="b491c-353">Debug</span></span>         | <span data-ttu-id="b491c-354">Microsoft</span><span class="sxs-lookup"><span data-stu-id="b491c-354">Microsoft</span></span>                               | <span data-ttu-id="b491c-355">Ablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="b491c-355">Trace</span></span>             |

<span data-ttu-id="b491c-356">Wenn ein `ILogger`-Objekt erstellt wird, wählt das `ILoggerFactory`-Objekt eine einzige Regel pro Anbieter aus, die auf diese Protokollierung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-356">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="b491c-357">Alle von einer `ILogger`-Instanz geschriebenen Meldungen werden auf Grundlage der ausgewählten Regeln gefiltert.</span><span class="sxs-lookup"><span data-stu-id="b491c-357">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="b491c-358">Aus den verfügbaren Regeln wird die für jeden Anbieter und jedes Kategoriepaar spezifischste Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b491c-358">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="b491c-359">Beim Erstellen von `ILogger` für eine vorgegebene Kategorie wird für jeden Anbieter der folgende Algorithmus verwendet:</span><span class="sxs-lookup"><span data-stu-id="b491c-359">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="b491c-360">Es werden alle Regeln ausgewählt, die dem Anbieter oder dem zugehörigen Alias entsprechen.</span><span class="sxs-lookup"><span data-stu-id="b491c-360">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="b491c-361">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln mit einem leeren Anbieter ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b491c-361">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="b491c-362">Aus dem Ergebnis des vorherigen Schritts werden die Regeln mit dem längsten übereinstimmenden Kategoriepräfix ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b491c-362">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="b491c-363">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln ohne Angabe einer Kategorie ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b491c-363">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="b491c-364">Wenn mehrere Regeln ausgewählt sind, wird die **letzte** Regel verwendet.</span><span class="sxs-lookup"><span data-stu-id="b491c-364">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="b491c-365">Wenn keine Regel ausgewählt ist, wird `MinimumLevel` verwendet.</span><span class="sxs-lookup"><span data-stu-id="b491c-365">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="b491c-366">Angenommen, Sie erstellen mit der oben aufgeführten Liste mit Regeln ein `ILogger`-Objekt für die Kategorie „Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine“:</span><span class="sxs-lookup"><span data-stu-id="b491c-366">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="b491c-367">Für den Debuganbieter gelten die Regeln 1, 6 und 8.</span><span class="sxs-lookup"><span data-stu-id="b491c-367">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="b491c-368">Regel 8 ist die spezifischste Regel, deshalb wird diese Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b491c-368">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="b491c-369">Für den Konsolenanbieter gelten die Regeln 3, 4, 5 und 6.</span><span class="sxs-lookup"><span data-stu-id="b491c-369">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="b491c-370">Regel 3 ist die spezifischste Regel.</span><span class="sxs-lookup"><span data-stu-id="b491c-370">Rule 3 is most specific.</span></span>

<span data-ttu-id="b491c-371">Die sich ergebende `ILogger`-Instanz sendet Protokolle mit dem Protokolliergrad `Trace` und höher an den Debuganbieter.</span><span class="sxs-lookup"><span data-stu-id="b491c-371">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="b491c-372">Protokolle mit dem Protokolliergrad `Debug` und höher werden an den Konsolenanbieter gesendet.</span><span class="sxs-lookup"><span data-stu-id="b491c-372">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="b491c-373">Anbieteraliase</span><span class="sxs-lookup"><span data-stu-id="b491c-373">Provider aliases</span></span>

<span data-ttu-id="b491c-374">Jeder Anbieter definiert einen *Alias*, der in der Konfiguration anstelle des vollqualifizierten Typnamens verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="b491c-374">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="b491c-375">Verwenden Sie für die integrierten Anbieter die folgenden Aliase:</span><span class="sxs-lookup"><span data-stu-id="b491c-375">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="b491c-376">Konsole</span><span class="sxs-lookup"><span data-stu-id="b491c-376">Console</span></span>
* <span data-ttu-id="b491c-377">Debug</span><span class="sxs-lookup"><span data-stu-id="b491c-377">Debug</span></span>
* <span data-ttu-id="b491c-378">EventSource</span><span class="sxs-lookup"><span data-stu-id="b491c-378">EventSource</span></span>
* <span data-ttu-id="b491c-379">EventLog</span><span class="sxs-lookup"><span data-stu-id="b491c-379">EventLog</span></span>
* <span data-ttu-id="b491c-380">TraceSource</span><span class="sxs-lookup"><span data-stu-id="b491c-380">TraceSource</span></span>
* <span data-ttu-id="b491c-381">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="b491c-381">AzureAppServicesFile</span></span>
* <span data-ttu-id="b491c-382">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="b491c-382">AzureAppServicesBlob</span></span>
* <span data-ttu-id="b491c-383">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="b491c-383">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="b491c-384">Standardmindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="b491c-384">Default minimum level</span></span>

<span data-ttu-id="b491c-385">Es gibt eine Einstellung für den Mindestprotokolliergrad, die nur dann wirksam wird, wenn für einen bestimmten Anbieter und eine bestimmte Kategorie keine Regeln aus Konfiguration oder Code gelten.</span><span class="sxs-lookup"><span data-stu-id="b491c-385">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="b491c-386">Im folgenden Beispiel wird das Festlegen des Mindestprotokolliergrads veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="b491c-386">The following example shows how to set the minimum level:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

<span data-ttu-id="b491c-387">Wenn Sie den Mindestprotokolliergrad nicht explizit festlegen, lautet der Standardwert `Information`, d.h. Protokolle der Ebene `Trace` und `Debug` werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="b491c-387">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="b491c-388">Filterfunktionen</span><span class="sxs-lookup"><span data-stu-id="b491c-388">Filter functions</span></span>

<span data-ttu-id="b491c-389">Eine Filterfunktion wird für alle Anbieter und Kategorien aufgerufen, denen keine Regeln durch Konfiguration oder Code zugewiesen sind.</span><span class="sxs-lookup"><span data-stu-id="b491c-389">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="b491c-390">Code in der Funktion verfügt über Zugriff auf den Anbietertyp, die Kategorie und den Protokolliergrad.</span><span class="sxs-lookup"><span data-stu-id="b491c-390">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="b491c-391">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b491c-391">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="b491c-392">Systemkategorien und -protokolliergrade</span><span class="sxs-lookup"><span data-stu-id="b491c-392">System categories and levels</span></span>

<span data-ttu-id="b491c-393">Dies sind einige Kategorien, die vom ASP.NET Core und Entity Framework Core verwendet werden, mit Hinweisen darauf, welche Protokolle von ihnen zu erwarten sind:</span><span class="sxs-lookup"><span data-stu-id="b491c-393">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="b491c-394">Kategorie</span><span class="sxs-lookup"><span data-stu-id="b491c-394">Category</span></span>                            | <span data-ttu-id="b491c-395">Hinweise</span><span class="sxs-lookup"><span data-stu-id="b491c-395">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="b491c-396">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="b491c-396">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="b491c-397">Allgemeine ASP.NET Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="b491c-397">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="b491c-398">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="b491c-398">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="b491c-399">Gibt an, welche Schlüssel in Betracht gezogen, gefunden und verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="b491c-399">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="b491c-400">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="b491c-400">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="b491c-401">Hosts sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="b491c-401">Hosts allowed.</span></span> |
| <span data-ttu-id="b491c-402">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="b491c-402">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="b491c-403">Gibt an, wie lange es bis zum Abschluss von HTTP-Anforderungen gedauert hat und zu welcher Zeit sie gestartet wurden.</span><span class="sxs-lookup"><span data-stu-id="b491c-403">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="b491c-404">Gibt an, welche Hostingstartassemblys geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="b491c-404">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="b491c-405">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="b491c-405">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="b491c-406">MVC- und Razor-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="b491c-406">MVC and Razor diagnostics.</span></span> <span data-ttu-id="b491c-407">Modellbindung, Filterausführung, Ansichtskompilierung, Aktionsauswahl.</span><span class="sxs-lookup"><span data-stu-id="b491c-407">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="b491c-408">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="b491c-408">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="b491c-409">Gibt Routenabgleichsinformationen an.</span><span class="sxs-lookup"><span data-stu-id="b491c-409">Route matching information.</span></span> |
| <span data-ttu-id="b491c-410">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="b491c-410">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="b491c-411">Start-, Beendigungs- und Keep-Alive-Antworten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="b491c-411">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="b491c-412">HTTPS-Zertifikatinformationen.</span><span class="sxs-lookup"><span data-stu-id="b491c-412">HTTPS certificate information.</span></span> |
| <span data-ttu-id="b491c-413">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="b491c-413">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="b491c-414">Die bereitgestellten Dateien.</span><span class="sxs-lookup"><span data-stu-id="b491c-414">Files served.</span></span> |
| <span data-ttu-id="b491c-415">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="b491c-415">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="b491c-416">Allgemeine Entity Framework Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="b491c-416">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="b491c-417">Datenbankaktivität und -konfiguration, Änderungserkennung, Migrationen.</span><span class="sxs-lookup"><span data-stu-id="b491c-417">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="b491c-418">Protokollbereiche</span><span class="sxs-lookup"><span data-stu-id="b491c-418">Log scopes</span></span>

 <span data-ttu-id="b491c-419">Ein *Bereich* kann einen Satz logischer Vorgänge gruppieren.</span><span class="sxs-lookup"><span data-stu-id="b491c-419">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="b491c-420">Diese Gruppierung kann verwendet werden, um an jedes Protokoll, das als Teil einer Gruppe erstellt wird, die gleichen Daten anzufügen.</span><span class="sxs-lookup"><span data-stu-id="b491c-420">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="b491c-421">So kann beispielsweise jedes Protokoll, das im Rahmen der Verarbeitung einer Transaktion erstellt wird, die Transaktions-ID enthalten.</span><span class="sxs-lookup"><span data-stu-id="b491c-421">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="b491c-422">Ein Bereich ist ein `IDisposable`-Typ, der von der <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>-Methode zurückgegeben und so lange beibehalten wird, bis er verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-422">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="b491c-423">Verwenden Sie einen Bereich, indem Sie Protokollierungsaufrufe mit einem `using`-Block umschließen, der als Wrapper verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="b491c-423">Use a scope by wrapping logger calls in a `using` block:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

<span data-ttu-id="b491c-424">Der folgende Code aktiviert Bereiche für den Konsolenanbieter:</span><span class="sxs-lookup"><span data-stu-id="b491c-424">The following code enables scopes for the console provider:</span></span>

<span data-ttu-id="b491c-425">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b491c-425">*Program.cs*:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b491c-426">Um die bereichsbasierte Protokollierung zu aktivieren, muss die Konsolenprotokollierungsoption `IncludeScopes` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="b491c-426">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="b491c-427">Weitere Informationen zur Konfiguration finden Sie im Abschnitt [Konfiguration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="b491c-427">For information on configuration, see the [Configuration](#configuration) section.</span></span>

<span data-ttu-id="b491c-428">Jede Protokollmeldung enthält die bereichsbezogenen Informationen:</span><span class="sxs-lookup"><span data-stu-id="b491c-428">Each log message includes the scoped information:</span></span>

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="b491c-429">Integrierte Protokollierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="b491c-429">Built-in logging providers</span></span>

<span data-ttu-id="b491c-430">ASP.NET Core wird mit den folgenden Anbietern bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="b491c-430">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="b491c-431">Konsole</span><span class="sxs-lookup"><span data-stu-id="b491c-431">Console</span></span>](#console-provider)
* [<span data-ttu-id="b491c-432">Debuggen</span><span class="sxs-lookup"><span data-stu-id="b491c-432">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="b491c-433">EventSource</span><span class="sxs-lookup"><span data-stu-id="b491c-433">EventSource</span></span>](#event-source-provider)
* [<span data-ttu-id="b491c-434">EventLog</span><span class="sxs-lookup"><span data-stu-id="b491c-434">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="b491c-435">TraceSource</span><span class="sxs-lookup"><span data-stu-id="b491c-435">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="b491c-436">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="b491c-436">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="b491c-437">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="b491c-437">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="b491c-438">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="b491c-438">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="b491c-439">Weitere Informationen zu „stdout“ und zur Debugprotokollierung mit dem ASP.NET Core-Modul finden Sie unter <xref:test/troubleshoot-azure-iis> und <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span><span class="sxs-lookup"><span data-stu-id="b491c-439">For information on stdout and debug logging with the ASP.NET Core Module, see <xref:test/troubleshoot-azure-iis> and <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="b491c-440">Der Konsolenanbieter</span><span class="sxs-lookup"><span data-stu-id="b491c-440">Console provider</span></span>

<span data-ttu-id="b491c-441">Das Anbieterpaket [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sendet eine Protokollausgabe an die Konsole.</span><span class="sxs-lookup"><span data-stu-id="b491c-441">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

```csharp
logging.AddConsole();
```

<span data-ttu-id="b491c-442">Um die Ausgabe der Konsolenprotokollierung anzuzeigen, öffnen Sie eine Eingabeaufforderung im Projektordner und führen den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="b491c-442">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="b491c-443">Der Debuganbieter</span><span class="sxs-lookup"><span data-stu-id="b491c-443">Debug provider</span></span>

<span data-ttu-id="b491c-444">Beim Anbieterpaket [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) erfolgt die Protokollausgabe unter Verwendung der Klasse [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine`-Methodenaufrufe).</span><span class="sxs-lookup"><span data-stu-id="b491c-444">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="b491c-445">Unter Linux werden Protokolle dieses Anbieters in */var/log/message* geschrieben.</span><span class="sxs-lookup"><span data-stu-id="b491c-445">On Linux, this provider writes logs to */var/log/message*.</span></span>

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a><span data-ttu-id="b491c-446">Ereignisquellenanbieter</span><span class="sxs-lookup"><span data-stu-id="b491c-446">Event Source provider</span></span>

<span data-ttu-id="b491c-447">Das [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource)-Anbieterpaket schreibt in eine plattformübergreifende Ereignisquelle mit dem Namen `Microsoft-Extensions-Logging`.</span><span class="sxs-lookup"><span data-stu-id="b491c-447">The [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package writes to an Event Source cross-platform with the name `Microsoft-Extensions-Logging`.</span></span> <span data-ttu-id="b491c-448">Unter Windows verwendet der Anbieter die [Ereignisablaufverfolgung für Windows (ETW)](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="b491c-448">On Windows, the provider uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span>

```csharp
logging.AddEventSourceLogger();
```

<span data-ttu-id="b491c-449">Der Ereignisquellenanbieter wird automatisch hinzugefügt, wenn `CreateDefaultBuilder` zum Erstellen des Hosts aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-449">The Event Source provider is added automatically when `CreateDefaultBuilder` is called to build the host.</span></span>

::: moniker range=">= aspnetcore-3.0"

#### <a name="dotnet-trace-tooling"></a><span data-ttu-id="b491c-450">dotnet-trace-Tool</span><span class="sxs-lookup"><span data-stu-id="b491c-450">dotnet trace tooling</span></span>

<span data-ttu-id="b491c-451">Das Tool [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) ist ein plattformübergreifendes globales Befehlszeilenschnittstellentool zum Sammeln von .NET Core-Ablaufverfolgungen eines ausgeführten Prozesses.</span><span class="sxs-lookup"><span data-stu-id="b491c-451">The [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) tool is a cross-platform CLI global tool that enables the collection of .NET Core traces of a running process.</span></span> <span data-ttu-id="b491c-452">Das Tool sammelt <xref:Microsoft.Extensions.Logging.EventSource>-Anbieterdaten mithilfe einer <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span><span class="sxs-lookup"><span data-stu-id="b491c-452">The tool collects <xref:Microsoft.Extensions.Logging.EventSource> provider data using a <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource>.</span></span>

<span data-ttu-id="b491c-453">Installieren Sie das dotnet-trace-Tool mit dem folgenden Befehl:</span><span class="sxs-lookup"><span data-stu-id="b491c-453">Install the dotnet trace tooling with the following command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-trace
```

<span data-ttu-id="b491c-454">Verwenden Sie das dotnet-trace-Tool, um die Ablaufverfolgung aus einer App zu erfassen:</span><span class="sxs-lookup"><span data-stu-id="b491c-454">Use the dotnet trace tooling to collect a trace from an app:</span></span>

1. <span data-ttu-id="b491c-455">Wenn die App den Host nicht mit `CreateDefaultBuilder` erstellt, fügen Sie den [Ereignisquellenanbieter](#event-source-provider) zur Protokollierungskonfiguration der App hinzu.</span><span class="sxs-lookup"><span data-stu-id="b491c-455">If the app doesn't build the host with `CreateDefaultBuilder`, add the [Event Source provider](#event-source-provider) to the app's logging configuration.</span></span>

1. <span data-ttu-id="b491c-456">Führen Sie die App mit dem Befehl `dotnet run` aus.</span><span class="sxs-lookup"><span data-stu-id="b491c-456">Run the app with the `dotnet run` command.</span></span>

1. <span data-ttu-id="b491c-457">Bestimmen Sie den Prozessbezeichner der .NET Core-App:</span><span class="sxs-lookup"><span data-stu-id="b491c-457">Determine the process identifier (PID) of the .NET Core app:</span></span>

   * <span data-ttu-id="b491c-458">Unter Windows können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="b491c-458">On Windows, use one of the following approaches:</span></span>
     * <span data-ttu-id="b491c-459">Task-Manager (STRG+ALT+ENTF)</span><span class="sxs-lookup"><span data-stu-id="b491c-459">Task Manager (Ctrl+Alt+Del)</span></span>
     * [<span data-ttu-id="b491c-460">tasklist-Befehl</span><span class="sxs-lookup"><span data-stu-id="b491c-460">tasklist command</span></span>](/windows-server/administration/windows-commands/tasklist)
     * [<span data-ttu-id="b491c-461">PowerShell-Befehl „Get-Process“</span><span class="sxs-lookup"><span data-stu-id="b491c-461">Get-Process Powershell command</span></span>](/powershell/module/microsoft.powershell.management/get-process)
   * <span data-ttu-id="b491c-462">Verwenden Sie unter Linux den [pidof-Befehl](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span><span class="sxs-lookup"><span data-stu-id="b491c-462">On Linux, use the [pidof command](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html).</span></span>

   <span data-ttu-id="b491c-463">Suchen Sie den Prozessbezeichner für den Prozess, der den gleichen Namen wie die App-Assembly hat.</span><span class="sxs-lookup"><span data-stu-id="b491c-463">Find the PID for the process that has the same name as the app's assembly.</span></span>

1. <span data-ttu-id="b491c-464">Führen Sie den `dotnet trace`-Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="b491c-464">Execute the `dotnet trace` command.</span></span>

   <span data-ttu-id="b491c-465">Allgemeine Befehlssyntax:</span><span class="sxs-lookup"><span data-stu-id="b491c-465">General command syntax:</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   <span data-ttu-id="b491c-466">Wenn Sie eine PowerShell-Befehlsshell verwenden, schließen Sie den `--providers`-Wert in einfache Anführungszeichen (`'`) ein:</span><span class="sxs-lookup"><span data-stu-id="b491c-466">When using a PowerShell command shell, enclose the `--providers` value in single quotes (`'`):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   <span data-ttu-id="b491c-467">Auf Plattformen, die nicht unter Windows ausgeführt werden, fügen Sie die `-f speedscope`-Option hinzu, um das Format der Ablaufverfolgungsdatei der Ausgabe in `speedscope` zu ändern.</span><span class="sxs-lookup"><span data-stu-id="b491c-467">On non-Windows platforms, add the `-f speedscope` option to change the format of the output trace file to `speedscope`.</span></span>

   | <span data-ttu-id="b491c-468">Stichwort</span><span class="sxs-lookup"><span data-stu-id="b491c-468">Keyword</span></span> | <span data-ttu-id="b491c-469">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="b491c-469">Description</span></span> |
   | :-----: | ----------- |
   | <span data-ttu-id="b491c-470">1</span><span class="sxs-lookup"><span data-stu-id="b491c-470">1</span></span>       | <span data-ttu-id="b491c-471">Protokolliert Metaereignisse über die `LoggingEventSource`.</span><span class="sxs-lookup"><span data-stu-id="b491c-471">Log meta events about the `LoggingEventSource`.</span></span> <span data-ttu-id="b491c-472">Es werden keine Ereignisse von `ILogger`) protokolliert.</span><span class="sxs-lookup"><span data-stu-id="b491c-472">Doesn't log events from `ILogger`).</span></span> |
   | <span data-ttu-id="b491c-473">2</span><span class="sxs-lookup"><span data-stu-id="b491c-473">2</span></span>       | <span data-ttu-id="b491c-474">Aktiviert das `Message`-Ereignis, wenn `ILogger.Log()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-474">Turns on the `Message` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="b491c-475">Die Informationen werden in einer programmgesteuerten (nicht formatierten) Weise ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="b491c-475">Provides information in a programmatic (not formatted) way.</span></span> |
   | <span data-ttu-id="b491c-476">4</span><span class="sxs-lookup"><span data-stu-id="b491c-476">4</span></span>       | <span data-ttu-id="b491c-477">Aktiviert das `FormatMessage`-Ereignis, wenn `ILogger.Log()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-477">Turns on the `FormatMessage` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="b491c-478">Gibt die formatierte Zeichenfolgeversion der Informationen an.</span><span class="sxs-lookup"><span data-stu-id="b491c-478">Provides the formatted string version of the information.</span></span> |
   | <span data-ttu-id="b491c-479">8</span><span class="sxs-lookup"><span data-stu-id="b491c-479">8</span></span>       | <span data-ttu-id="b491c-480">Aktiviert das `MessageJson`-Ereignis, wenn `ILogger.Log()` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-480">Turns on the `MessageJson` event when `ILogger.Log()` is called.</span></span> <span data-ttu-id="b491c-481">Stellt eine JSON-Darstellung der Argumente bereit.</span><span class="sxs-lookup"><span data-stu-id="b491c-481">Provides a JSON representation of the arguments.</span></span> |

   | <span data-ttu-id="b491c-482">Ereignisgrad</span><span class="sxs-lookup"><span data-stu-id="b491c-482">Event Level</span></span> | <span data-ttu-id="b491c-483">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="b491c-483">Description</span></span>     |
   | :---------: | --------------- |
   | <span data-ttu-id="b491c-484">0</span><span class="sxs-lookup"><span data-stu-id="b491c-484">0</span></span>           | `LogAlways`     |
   | <span data-ttu-id="b491c-485">1</span><span class="sxs-lookup"><span data-stu-id="b491c-485">1</span></span>           | `Critical`      |
   | <span data-ttu-id="b491c-486">2</span><span class="sxs-lookup"><span data-stu-id="b491c-486">2</span></span>           | `Error`         |
   | <span data-ttu-id="b491c-487">3</span><span class="sxs-lookup"><span data-stu-id="b491c-487">3</span></span>           | `Warning`       |
   | <span data-ttu-id="b491c-488">4</span><span class="sxs-lookup"><span data-stu-id="b491c-488">4</span></span>           | `Informational` |
   | <span data-ttu-id="b491c-489">5</span><span class="sxs-lookup"><span data-stu-id="b491c-489">5</span></span>           | `Verbose`       |

   <span data-ttu-id="b491c-490">`FilterSpecs`-Einträge für `{Logger Category}` und `{Event Level}` stellen zusätzliche Protokollfilterbedingungen dar.</span><span class="sxs-lookup"><span data-stu-id="b491c-490">`FilterSpecs` entries for `{Logger Category}` and `{Event Level}` represent additional log filtering conditions.</span></span> <span data-ttu-id="b491c-491">Trennen Sie die `FilterSpecs`-Einträge durch ein Semikolon (`;`).</span><span class="sxs-lookup"><span data-stu-id="b491c-491">Separate `FilterSpecs` entries with a semicolon (`;`).</span></span>

   <span data-ttu-id="b491c-492">Beispiel mit einer Windows-Befehlsshell (**keine** einfachen Anführungszeichen um den `--providers`-Wert):</span><span class="sxs-lookup"><span data-stu-id="b491c-492">Example using a Windows command shell (**no** single quotes around the `--providers` value):</span></span>

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   <span data-ttu-id="b491c-493">Mit dem vorangestellten Komma wird Folgendes aktiviert:</span><span class="sxs-lookup"><span data-stu-id="b491c-493">The preceding command activates:</span></span>

   * <span data-ttu-id="b491c-494">Die Ereignisquellenprotokollierung zur Erzeugung von formatierten Zeichenfolgen (`4`) für Fehler (`2`)</span><span class="sxs-lookup"><span data-stu-id="b491c-494">The Event Source logger to produce formatted strings (`4`) for errors (`2`).</span></span>
   * <span data-ttu-id="b491c-495">`Microsoft.AspNetCore.Hosting`-Protokollierung auf `Informational`-Protokollierungsebene (`4`)</span><span class="sxs-lookup"><span data-stu-id="b491c-495">`Microsoft.AspNetCore.Hosting` logging at the `Informational` logging level (`4`).</span></span>

1. <span data-ttu-id="b491c-496">Halten Sie das dotnet-trace-Tool an, indem Sie die EINGABETASTE oder STRG+C drücken.</span><span class="sxs-lookup"><span data-stu-id="b491c-496">Stop the dotnet trace tooling by pressing the Enter key or Ctrl+C.</span></span>

   <span data-ttu-id="b491c-497">Die Ablaufverfolgung wird mit dem Namen *trace.nettrace* in dem Ordner gespeichert, in dem der `dotnet trace`-Befehl ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-497">The trace is saved with the name *trace.nettrace* in the folder where the `dotnet trace` command is executed.</span></span>

1. <span data-ttu-id="b491c-498">Öffnen Sie die Ablaufverfolgung mit [Perfview](#perfview).</span><span class="sxs-lookup"><span data-stu-id="b491c-498">Open the trace with [Perfview](#perfview).</span></span> <span data-ttu-id="b491c-499">Öffnen Sie die Datei *trace.nettrace*, und untersuchen Sie die Ablaufverfolgungsereignisse.</span><span class="sxs-lookup"><span data-stu-id="b491c-499">Open the *trace.nettrace* file and explore the trace events.</span></span>

<span data-ttu-id="b491c-500">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="b491c-500">For more information, see:</span></span>

* <span data-ttu-id="b491c-501">[Ablaufverfolgung für das Hilfsprogramm zur Leistungsanalyse (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="b491c-501">[Trace for performance analysis utility (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace) (.NET Core documentation)</span></span>
* <span data-ttu-id="b491c-502">[Ablaufverfolgung für das Hilfsprogramm zur Leistungsanalyse (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (GitHub-Repository-Dokumentation zu dotnet/diagnostics)</span><span class="sxs-lookup"><span data-stu-id="b491c-502">[Trace for performance analysis utility (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md) (dotnet/diagnostics GitHub repository documentation)</span></span>
* <span data-ttu-id="b491c-503">[LoggingEventSource-Klasse](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API-Browser)</span><span class="sxs-lookup"><span data-stu-id="b491c-503">[LoggingEventSource Class](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource) (.NET API Browser)</span></span>
* <xref:System.Diagnostics.Tracing.EventLevel>
* <span data-ttu-id="b491c-504">[LoggingEventSource-Verweisquelle (3.0)](https://github.com/aspnet/Extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; Ändern Sie für das Abrufen der Verweisquelle für eine andere Version den Branch in `release/{Version}`, wobei `{Version}` die Version der gewünschten ASP.NET Core-Version darstellt.</span><span class="sxs-lookup"><span data-stu-id="b491c-504">[LoggingEventSource reference source (3.0)](https://github.com/aspnet/Extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; To obtain reference source for a different version, change the branch to `release/{Version}`, where `{Version}` is the version of ASP.NET Core desired.</span></span>
* <span data-ttu-id="b491c-505">[Perfview](#perfview) &ndash; Nützlich zum Anzeigen von Ablaufverfolgung für Ereignisquellen.</span><span class="sxs-lookup"><span data-stu-id="b491c-505">[Perfview](#perfview) &ndash; Useful for viewing Event Source traces.</span></span>

#### <a name="perfview"></a><span data-ttu-id="b491c-506">PerfView</span><span class="sxs-lookup"><span data-stu-id="b491c-506">Perfview</span></span>

::: moniker-end

<span data-ttu-id="b491c-507">Verwenden Sie das [PerfView-Hilfsprogramm](https://github.com/Microsoft/perfview) zum Sammeln und Anzeigen von Protokollen.</span><span class="sxs-lookup"><span data-stu-id="b491c-507">Use the [PerfView utility](https://github.com/Microsoft/perfview) to collect and view logs.</span></span> <span data-ttu-id="b491c-508">Es gibt andere Tools zur Anzeige von ETW-Protokollen, aber PerfView bietet die besten Ergebnisse bei der Arbeit mit ETW-Ereignissen, die von ASP.NET Core ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b491c-508">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET Core.</span></span>

<span data-ttu-id="b491c-509">Um PerfView für das Erfassen von Ereignissen zu konfigurieren, die von diesem Anbieter protokolliert wurden, fügen Sie die Zeichenfolge `*Microsoft-Extensions-Logging` zur Liste **Zusätzliche Anbieter** hinzu.</span><span class="sxs-lookup"><span data-stu-id="b491c-509">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="b491c-510">(Vergessen Sie nicht das Sternchen am Anfang der Zeichenfolge.)</span><span class="sxs-lookup"><span data-stu-id="b491c-510">(Don't miss the asterisk at the start of the string.)</span></span>

![Zusätzliche PerfView-Anbieter](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="b491c-512">Der Windows-EventLog-Anbieter</span><span class="sxs-lookup"><span data-stu-id="b491c-512">Windows EventLog provider</span></span>

<span data-ttu-id="b491c-513">Das Anbieterpaket [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sendet eine Protokollausgabe in das Windows-Ereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="b491c-513">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

```csharp
logging.AddEventLog();
```

<span data-ttu-id="b491c-514">Mithilfe von [AddEventLog-Überladungen](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) können Sie <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings> übergeben.</span><span class="sxs-lookup"><span data-stu-id="b491c-514">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>.</span></span>

### <a name="tracesource-provider"></a><span data-ttu-id="b491c-515">Der TraceSource-Anbieter</span><span class="sxs-lookup"><span data-stu-id="b491c-515">TraceSource provider</span></span>

<span data-ttu-id="b491c-516">Das Anbieterpaket [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) verwendet die <xref:System.Diagnostics.TraceSource>-Bibliotheken und -Anbieter.</span><span class="sxs-lookup"><span data-stu-id="b491c-516">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

```csharp
logging.AddTraceSource(sourceSwitchName);
```

<span data-ttu-id="b491c-517">Mithilfe von [AddTraceSource-Überladungen](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) können Sie eine Quelloption und einen Listener für die Ablaufverfolgung übergeben.</span><span class="sxs-lookup"><span data-stu-id="b491c-517">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="b491c-518">Um diesen Anbieter zu verwenden, muss eine App unter .NET Framework (anstelle von .NET Core) ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b491c-518">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="b491c-519">Der Anbieter kann Nachrichten an eine Vielzahl von [Listenern](/dotnet/framework/debug-trace-profile/trace-listeners) weiterleiten, z.B. an den in der Beispiel-App verwendeten <xref:System.Diagnostics.TextWriterTraceListener>.</span><span class="sxs-lookup"><span data-stu-id="b491c-519">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

### <a name="azure-app-service-provider"></a><span data-ttu-id="b491c-520">Der Azure App Service-Anbieter</span><span class="sxs-lookup"><span data-stu-id="b491c-520">Azure App Service provider</span></span>

<span data-ttu-id="b491c-521">Das Anbieterpaket [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) schreibt Protokolle in Textdateien in das Dateisystem einer Azure App Service-App und in [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in einem Azure Storage-Konto.</span><span class="sxs-lookup"><span data-stu-id="b491c-521">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b491c-522">Das Anbieterpaket ist nicht im freigegebenen Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="b491c-522">The provider package isn't included in the shared framework.</span></span> <span data-ttu-id="b491c-523">Zum Verwenden des Anbieters müssen Sie das Anbieterpaket dem Projekt hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="b491c-523">To use the provider, add the provider package to the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b491c-524">Dieses Anbieterpaket ist nicht im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="b491c-524">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="b491c-525">Wenn Sie Anwendungen für .NET Framework entwickeln oder auf das `Microsoft.AspNetCore.App`-Metapaket verweisen, fügen Sie das Anbieterpaket dem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="b491c-525">When targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b491c-526">Verwenden Sie <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> und <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> wie im folgenden Beispiel, um die Anbietereinstellungen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="b491c-526">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b491c-527">Verwenden Sie <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> und <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> wie im folgenden Beispiel, um die Anbietereinstellungen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="b491c-527">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="b491c-528">Eine <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>-Überladung ermöglicht das Übergeben von <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="b491c-528">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="b491c-529">Das settings-Objekt kann Standardeinstellungen überschreiben, z.B. die Protokollierungsausgabevorlage, den Blobnamen und die maximale Dateigröße.</span><span class="sxs-lookup"><span data-stu-id="b491c-529">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="b491c-530">(Die *Ausgabevorlage* ist eine Nachrichtenvorlage, die auf alle Protokolle zusätzlich zu dem angewendet wird, was mit einem `ILogger`-Methodenaufruf bereitgestellt wird.)</span><span class="sxs-lookup"><span data-stu-id="b491c-530">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

<span data-ttu-id="b491c-531">Wenn Sie eine Bereitstellung in einer App Service-App durchführen, berücksichtigt die Anwendung die Einstellungen im Abschnitt [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) (App Service-Protokolle) auf der Seite **App Services** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="b491c-531">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="b491c-532">Bei einem Update der folgenden Einstellungen werden die Änderungen sofort wirksam, ohne dass ein Neustart oder eine erneute Bereitstellung der App notwendig ist.</span><span class="sxs-lookup"><span data-stu-id="b491c-532">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="b491c-533">**Anwendungsprotokoll (Dateisystem)**</span><span class="sxs-lookup"><span data-stu-id="b491c-533">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="b491c-534">**Anwendungsprotokoll (Blob)**</span><span class="sxs-lookup"><span data-stu-id="b491c-534">**Application Logging (Blob)**</span></span>

<span data-ttu-id="b491c-535">Der Standardspeicherort für Protokolldateien ist der Ordner *D:\\home\\LogFiles\\Application*, und der standardmäßige Dateiname lautet *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="b491c-535">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="b491c-536">Die Dateigröße ist standardmäßig auf 10 MB beschränkt, und die maximal zulässige Anzahl beibehaltener Dateien lautet 2.</span><span class="sxs-lookup"><span data-stu-id="b491c-536">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="b491c-537">Der Standardblobname lautet *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="b491c-537">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="b491c-538">Der Anbieter funktioniert nur, wenn das Projekt in der Azure-Umgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-538">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="b491c-539">Bei einer lokalen Ausführung zeigt er keine Auswirkungen. Der Anbieter schreibt keine Protokolle in lokale Dateien oder den lokalen Entwicklungsspeicher für BLOBs.</span><span class="sxs-lookup"><span data-stu-id="b491c-539">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="b491c-540">Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="b491c-540">Azure log streaming</span></span>

<span data-ttu-id="b491c-541">Azure-Protokollstreaming ermöglicht Ihnen eine Echtzeitanzeige der Protokollaktivität für:</span><span class="sxs-lookup"><span data-stu-id="b491c-541">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="b491c-542">App-Server</span><span class="sxs-lookup"><span data-stu-id="b491c-542">The app server</span></span>
* <span data-ttu-id="b491c-543">Webserver</span><span class="sxs-lookup"><span data-stu-id="b491c-543">The web server</span></span>
* <span data-ttu-id="b491c-544">Ablaufverfolgung für Anforderungsfehler</span><span class="sxs-lookup"><span data-stu-id="b491c-544">Failed request tracing</span></span>

<span data-ttu-id="b491c-545">So konfigurieren Sie das Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="b491c-545">To configure Azure log streaming:</span></span>

* <span data-ttu-id="b491c-546">Navigieren Sie von der Portalseite Ihrer App zur Seite **App Service-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="b491c-546">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="b491c-547">Legen Sie **Anwendungsprotokollierung (Dateisystem)** auf **Ein** fest.</span><span class="sxs-lookup"><span data-stu-id="b491c-547">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="b491c-548">Wählen Sie die **Protokollierungsebene**.</span><span class="sxs-lookup"><span data-stu-id="b491c-548">Choose the log **Level**.</span></span> <span data-ttu-id="b491c-549">Diese Einstellung gilt nur für das Azure-Protokollstreaming, nicht für andere Protokollierungsanbieter in der App.</span><span class="sxs-lookup"><span data-stu-id="b491c-549">This setting only applies to Azure log streaming, not other logging providers in the app.</span></span>

<span data-ttu-id="b491c-550">Navigieren Sie zur Seite **Log Stream** (Protokollstream), um App-Meldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b491c-550">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="b491c-551">Diese werden von der App über die `ILogger`-Schnittstelle protokolliert.</span><span class="sxs-lookup"><span data-stu-id="b491c-551">They're logged by the app through the `ILogger` interface.</span></span>

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="b491c-552">Ablaufverfolgungsprotokollierung für Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="b491c-552">Azure Application Insights trace logging</span></span>

<span data-ttu-id="b491c-553">Das [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)-Anbieterpaket schreibt Protokolle in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="b491c-553">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="b491c-554">Application Insights ist ein Dienst, der eine Web-App überwacht und Tools für Abfragen und Analysen von Telemetriedaten zur Verfügung stellt.</span><span class="sxs-lookup"><span data-stu-id="b491c-554">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="b491c-555">Wenn Sie diesen Anbieter verwenden, können Sie Ihre Protokolle mithilfe der Application Insights-Tools abfragen und analysieren.</span><span class="sxs-lookup"><span data-stu-id="b491c-555">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="b491c-556">Der Protokollierungsanbieter ist als Abhängigkeit von [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) enthalten, d.h. als das Paket, das alle verfügbaren Telemetriedaten für ASP.NET Core bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="b491c-556">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="b491c-557">Wenn Sie dieses Paket verwenden, ist nicht erforderlich, das Anbieterpaket zu installieren.</span><span class="sxs-lookup"><span data-stu-id="b491c-557">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="b491c-558">Verwenden Sie nicht das [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)-Paket, das für ASP.NET 4.x bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="b491c-558">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="b491c-559">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="b491c-559">For more information, see the following resources:</span></span>

* [<span data-ttu-id="b491c-560">Application Insights-Übersicht</span><span class="sxs-lookup"><span data-stu-id="b491c-560">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="b491c-561">[Anwendungseinblicke für ASP.NET Core-Anwendungen](/azure/azure-monitor/app/asp-net-core): Beginnen Sie hier, wenn Sie die gesamte Bandbreite der Application Insights-Telemetrie zusammen mit der Protokollierung implementieren möchten.</span><span class="sxs-lookup"><span data-stu-id="b491c-561">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="b491c-562">[ApplicationInsightsLoggerProvider für .NET Core ILogger-Protokolle](/azure/azure-monitor/app/ilogger): Beginnen Sie hier, wenn Sie den Protokollierungsanbieter ohne die übrige Application Insights-Telemetrie implementieren möchten.</span><span class="sxs-lookup"><span data-stu-id="b491c-562">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="b491c-563">[Application Insights logging adapters (Adapter zur Protokollierung für Application Insights)](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span><span class="sxs-lookup"><span data-stu-id="b491c-563">[Application Insights logging adapters](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs).</span></span>
* <span data-ttu-id="b491c-564">[Installieren, Konfigurieren und Initialisieren des Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): interaktive Tutorials auf der Microsoft Learn-Website.</span><span class="sxs-lookup"><span data-stu-id="b491c-564">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="b491c-565">Protokollierungsanbieter von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="b491c-565">Third-party logging providers</span></span>

<span data-ttu-id="b491c-566">Protokollierungsframeworks von Drittanbietern aufgeführt, die mit ASP.NET Core funktionieren:</span><span class="sxs-lookup"><span data-stu-id="b491c-566">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="b491c-567">[elmah.io](https://elmah.io/) ([GitHub-Repository](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="b491c-567">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="b491c-568">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub-Repository](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="b491c-568">[Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="b491c-569">[JSNLog](https://jsnlog.com/) ([GitHub-Repository](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="b491c-569">[JSNLog](https://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="b491c-570">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="b491c-570">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="b491c-571">[Log4Net](https://logging.apache.org/log4net/) ([GitHub-Repository](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span><span class="sxs-lookup"><span data-stu-id="b491c-571">[Log4Net](https://logging.apache.org/log4net/) ([GitHub repo](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore))</span></span>
* <span data-ttu-id="b491c-572">[Loggr](https://loggr.net/) ([GitHub-Repository](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="b491c-572">[Loggr](https://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="b491c-573">[NLog](https://nlog-project.org/) ([GitHub-Repository](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="b491c-573">[NLog](https://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="b491c-574">[Sentry](https://sentry.io/welcome/) ([GitHub-Repository](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="b491c-574">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="b491c-575">[Serilog](https://serilog.net/) ([GitHub-Repository](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="b491c-575">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="b491c-576">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub-Repository](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="b491c-576">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="b491c-577">Einige Drittanbieterframeworks können eine [semantische Protokollierung (auch als strukturierte Protokollierung bezeichnet)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) ausführen.</span><span class="sxs-lookup"><span data-stu-id="b491c-577">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="b491c-578">Die Verwendung eines Frameworks von Drittanbietern ist ähnlich wie die Verwendung eines integrierten Anbieters:</span><span class="sxs-lookup"><span data-stu-id="b491c-578">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="b491c-579">Fügen Sie Ihrem Paket ein NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="b491c-579">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="b491c-580">Rufen Sie eine `ILoggerFactory`-Erweiterungsmethode auf, die vom Protokollierungsframework bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="b491c-580">Call an `ILoggerFactory` extension method provided by the logging framework.</span></span>

<span data-ttu-id="b491c-581">Weitere Informationen finden Sie in der Dokumentation zum jeweiligen Anbieter.</span><span class="sxs-lookup"><span data-stu-id="b491c-581">For more information, see each provider's documentation.</span></span> <span data-ttu-id="b491c-582">Protokollierungsanbieter von Drittanbietern werden von Microsoft nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b491c-582">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b491c-583">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b491c-583">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
