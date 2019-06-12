---
title: Protokollierung in ASP.NET Core
author: tdykstra
description: Erfahren Sie mehr über das Protokollierungsframework in ASP.NET Core. Lernen Sie die integrierten Anbieter für die Protokollierung kennen, und erfahren Sie mehr über beliebte Anbieter von Drittanbietern.
ms.author: tdykstra
ms.custom: mvc
ms.date: 05/01/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 435f06b85af4a1a5a78a870c2add3e15ff1ffe89
ms.sourcegitcommit: 1bb3f3f1905b4e7d4ca1b314f2ce6ee5dd8be75f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2019
ms.locfileid: "66837277"
---
# <a name="logging-in-aspnet-core"></a><span data-ttu-id="54b4c-104">Protokollierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="54b4c-104">Logging in ASP.NET Core</span></span>

<span data-ttu-id="54b4c-105">Von [Steve Smith](https://ardalis.com/) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="54b4c-105">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="54b4c-106">ASP.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="54b4c-106">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="54b4c-107">Dieser Artikel zeigt, wie Sie die Protokollierungs-API mit integrierten Anbietern verwenden können.</span><span class="sxs-lookup"><span data-stu-id="54b4c-107">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="54b4c-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="54b4c-108">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="54b4c-109">Hinzufügen von Anbietern</span><span class="sxs-lookup"><span data-stu-id="54b4c-109">Add providers</span></span>

<span data-ttu-id="54b4c-110">Ein Protokollierungsanbieter zeigt Protokolle an oder speichert diese.</span><span class="sxs-lookup"><span data-stu-id="54b4c-110">A logging provider displays or stores logs.</span></span> <span data-ttu-id="54b4c-111">Beispielsweise zeigt der Konsolenanbieter Protokolle in der Konsole an, und der Azure Application Insights-Anbieter speichert sie in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="54b4c-111">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="54b4c-112">Protokolle können durch das Hinzufügen mehrerer Anbieter an mehrere Ziele gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-112">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="54b4c-113">Um einen Anbieter hinzuzufügen, rufen Sie die `Add{provider name}`-Erweiterungsmethode des Anbieters in *Program.cs* auf:</span><span class="sxs-lookup"><span data-stu-id="54b4c-113">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=17-19)]

<span data-ttu-id="54b4c-114">Die Standardprojektvorlage ruft die Methode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> auf, die die folgenden Protokollierungsanbieter hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="54b4c-114">The default project template calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which adds the following logging providers:</span></span>

* <span data-ttu-id="54b4c-115">Konsole</span><span class="sxs-lookup"><span data-stu-id="54b4c-115">Console</span></span>
* <span data-ttu-id="54b4c-116">Debug</span><span class="sxs-lookup"><span data-stu-id="54b4c-116">Debug</span></span>
* <span data-ttu-id="54b4c-117">EventSource (beginnend mit ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="54b4c-117">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="54b4c-118">Bei Verwendung von `CreateDefaultBuilder` können Sie die Standardanbieter durch Ihre eigene Auswahl ersetzen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-118">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="54b4c-119">Rufen Sie <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> auf, und fügen Sie die gewünschten Anbieter hinzu.</span><span class="sxs-lookup"><span data-stu-id="54b4c-119">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="54b4c-120">Um einen Anbieter zu verwenden, installieren Sie das zugehörige NuGet-Paket und rufen die Erweiterungsmethode des Anbieters für eine Instanz von <xref:Microsoft.Extensions.Logging.ILoggerFactory> auf:</span><span class="sxs-lookup"><span data-stu-id="54b4c-120">To use a provider, install its NuGet package and call the provider's extension method on an instance of <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

<span data-ttu-id="54b4c-121">ASP.NET Core-[Abhängigkeitsinjektion (Dependency Injection, DI)](xref:fundamentals/dependency-injection) stellt die `ILoggerFactory`-Instanz bereit.</span><span class="sxs-lookup"><span data-stu-id="54b4c-121">ASP.NET Core [dependency injection (DI)](xref:fundamentals/dependency-injection) provides the `ILoggerFactory` instance.</span></span> <span data-ttu-id="54b4c-122">Die Erweiterungsmethoden `AddConsole` und `AddDebug` sind in den Paketen [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) und [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) definiert.</span><span class="sxs-lookup"><span data-stu-id="54b4c-122">The `AddConsole` and `AddDebug` extension methods are defined in the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) and [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) packages.</span></span> <span data-ttu-id="54b4c-123">Jede Erweiterungsmethode ruft die Methode `ILoggerFactory.AddProvider` auf und übergibt eine Instanz des Anbieters.</span><span class="sxs-lookup"><span data-stu-id="54b4c-123">Each extension method calls the `ILoggerFactory.AddProvider` method, passing in an instance of the provider.</span></span>

> [!NOTE]
> <span data-ttu-id="54b4c-124">Die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) fügt Protokollanbieter in der `Startup.Configure`-Methode hinzu.</span><span class="sxs-lookup"><span data-stu-id="54b4c-124">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) adds logging providers in the `Startup.Configure` method.</span></span> <span data-ttu-id="54b4c-125">Wenn Sie für zuvor ausgeführten Code eine Protokollausgabe abrufen möchten, fügen Sie Protokollierungsanbieter im `Startup`-Klassenkonstruktor hinzu.</span><span class="sxs-lookup"><span data-stu-id="54b4c-125">To obtain log output from code that executes earlier, add logging providers in the `Startup` class constructor.</span></span>

::: moniker-end

<span data-ttu-id="54b4c-126">Informationen zu den [integrierten Protokollierungsanbietern](#built-in-logging-providers) sowie zu [Protokollierungsanbietern von Drittanbietern](#third-party-logging-providers) finden Sie weiter unten in diesem Artikel.</span><span class="sxs-lookup"><span data-stu-id="54b4c-126">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="54b4c-127">Erstellen von Protokollen</span><span class="sxs-lookup"><span data-stu-id="54b4c-127">Create logs</span></span>

<span data-ttu-id="54b4c-128">Rufen Sie ein <xref:Microsoft.Extensions.Logging.ILogger%601>-Objekt aus DI ab.</span><span class="sxs-lookup"><span data-stu-id="54b4c-128">Get an <xref:Microsoft.Extensions.Logging.ILogger%601> object from DI.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="54b4c-129">Das folgende Controllerbeispiel erstellt `Information`- und `Warning`-Protokolle.</span><span class="sxs-lookup"><span data-stu-id="54b4c-129">The following controller example creates `Information` and `Warning` logs.</span></span> <span data-ttu-id="54b4c-130">Die *Kategorie* ist `TodoApiSample.Controllers.TodoController` (der vollqualifizierte Klassenname von `TodoController` in der Beispiel-App):</span><span class="sxs-lookup"><span data-stu-id="54b4c-130">The *category* is `TodoApiSample.Controllers.TodoController` (the fully qualified class name of `TodoController` in the sample app):</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="54b4c-131">Das folgende Razor Pages-Beispiel erstellt Protokolle mit `Information` als *Grad* und `TodoApiSample.Pages.AboutModel` als *Kategorie*:</span><span class="sxs-lookup"><span data-stu-id="54b4c-131">The following Razor Pages example creates logs with `Information` as the *level* and `TodoApiSample.Pages.AboutModel` as the *category*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3, 7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="54b4c-132">Das vorherige Beispiel erstellt Protokolle mit `Information` und `Warning` als *Grad* und der `TodoController`-Klasse als *Kategorie*.</span><span class="sxs-lookup"><span data-stu-id="54b4c-132">The preceding example creates logs with `Information` and `Warning` as the *level* and `TodoController` class as the *category*.</span></span> 

::: moniker-end

<span data-ttu-id="54b4c-133">Der Protokollierungs*grad* gibt den Schweregrad des protokollierten Ereignisses an.</span><span class="sxs-lookup"><span data-stu-id="54b4c-133">The Log *level* indicates the severity of the logged event.</span></span> <span data-ttu-id="54b4c-134">Die Protokoll*kategorie* ist eine Zeichenfolge, die jedem Protokoll zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="54b4c-134">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="54b4c-135">Die `ILogger<T>`-Instanz erstellt Protokolle, die den vollqualifizierten Namen vom Typ `T` als Kategorie aufweisen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-135">The `ILogger<T>` instance creates logs that have the fully qualified name of type `T` as the category.</span></span> <span data-ttu-id="54b4c-136">[Grade](#log-level) und [Kategorien](#log-category) werden weiter unten in diesem Artikel ausführlicher erläutert.</span><span class="sxs-lookup"><span data-stu-id="54b4c-136">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-2.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="54b4c-137">Erstellen von Protokollen in der Startklasse</span><span class="sxs-lookup"><span data-stu-id="54b4c-137">Create logs in Startup</span></span>

<span data-ttu-id="54b4c-138">Zum Schreiben von Protokollen in der `Startup`-Klasse schließen Sie einen `ILogger`-Parameter in die Konstruktorsignatur ein:</span><span class="sxs-lookup"><span data-stu-id="54b4c-138">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-program"></a><span data-ttu-id="54b4c-139">Erstellen von Protokollen in der Programmklasse</span><span class="sxs-lookup"><span data-stu-id="54b4c-139">Create logs in Program</span></span>

<span data-ttu-id="54b4c-140">Zum Schreiben von Protokollen in der `Program`-Klasse rufen Sie eine `ILogger`-Instanz aus DI ab:</span><span class="sxs-lookup"><span data-stu-id="54b4c-140">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="54b4c-141">Keine asynchronen Protokollierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="54b4c-141">No asynchronous logger methods</span></span>

<span data-ttu-id="54b4c-142">Die Protokollierung sollte so schnell erfolgen, dass sich die Leistungskosten von asynchronem Code nicht lohnen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-142">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="54b4c-143">Wenn Ihr Protokollierungsdatenspeicher langsam ist, schreiben Sie nicht direkt in ihn.</span><span class="sxs-lookup"><span data-stu-id="54b4c-143">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="54b4c-144">Erwägen Sie, die Protokollnachrichten zunächst in einen schnellen Speicher zu schreiben und sie dann später in den langsamen Speicher zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="54b4c-144">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="54b4c-145">Wenn Sie beispielsweise in SQL Server protokollieren, möchten Sie dies nicht direkt in einer `Log`-Methode tun, da die `Log`-Methoden synchron sind.</span><span class="sxs-lookup"><span data-stu-id="54b4c-145">For example, if you're logging to SQL Server, you don't want to do that directly in a `Log` method, since the `Log` methods are synchronous.</span></span> <span data-ttu-id="54b4c-146">Fügen Sie stattdessen die Protokollmeldungen synchron zu einer Warteschlange im Arbeitsspeicher hinzu, und rufen Sie mithilfe eines Hintergrundworkers die Nachrichten aus der Warteschlange ab, um sie dann asynchron per Pushvorgang an SQL Server zu übertragen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-146">Instead, synchronously add log messages to an in-memory queue and have a background worker pull the messages out of the queue to do the asynchronous work of pushing data to SQL Server.</span></span>

## <a name="configuration"></a><span data-ttu-id="54b4c-147">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="54b4c-147">Configuration</span></span>

<span data-ttu-id="54b4c-148">Die Konfiguration von Protokollierungsanbietern erfolgt durch mindestens einen Konfigurationsanbieter:</span><span class="sxs-lookup"><span data-stu-id="54b4c-148">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="54b4c-149">Dateiformate (INI, JSON und XML)</span><span class="sxs-lookup"><span data-stu-id="54b4c-149">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="54b4c-150">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="54b4c-150">Command-line arguments.</span></span>
* <span data-ttu-id="54b4c-151">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-151">Environment variables.</span></span>
* <span data-ttu-id="54b4c-152">Speicherinterne .NET-Objekte</span><span class="sxs-lookup"><span data-stu-id="54b4c-152">In-memory .NET objects.</span></span>
* <span data-ttu-id="54b4c-153">Der unverschlüsselte Speicher von [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="54b4c-153">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="54b4c-154">Ein unverschlüsselter Benutzerspeicher, z.B. [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="54b4c-154">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="54b4c-155">Benutzerdefinierte Anbieter (installiert oder erstellt).</span><span class="sxs-lookup"><span data-stu-id="54b4c-155">Custom providers (installed or created).</span></span>

<span data-ttu-id="54b4c-156">Die Konfiguration der Protokollierung wird meist vom `Logging`-Abschnitt von Anwendungseinstellungsdateien angegeben.</span><span class="sxs-lookup"><span data-stu-id="54b4c-156">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="54b4c-157">Im folgenden Beispiel werden die Inhalte einer herkömmlichen *appsettings.Development.json*-Datei veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="54b4c-157">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

::: moniker range=">= aspnetcore-2.1"

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

<span data-ttu-id="54b4c-158">Die `Logging`-Eigenschaft kann den `LogLevel` und Protokollanbietereigenschaften beinhalten (Konsole wird angezeigt).</span><span class="sxs-lookup"><span data-stu-id="54b4c-158">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="54b4c-159">Die `LogLevel`-Eigenschaft unter `Logging` gibt den Mindest[grad](#log-level) an, der für ausgewählte Kategorien protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="54b4c-159">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="54b4c-160">Im Beispiel protokollieren die Kategorien `System` und `Microsoft` mit dem Grad `Information` und alle anderen Kategorien mit dem Grad `Debug`.</span><span class="sxs-lookup"><span data-stu-id="54b4c-160">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="54b4c-161">Weitere Eigenschaften unter `Logging` geben Protokollierungsanbieter an.</span><span class="sxs-lookup"><span data-stu-id="54b4c-161">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="54b4c-162">Das Beispiel bezieht sich auf den Konsolenanbieter.</span><span class="sxs-lookup"><span data-stu-id="54b4c-162">The example is for the Console provider.</span></span> <span data-ttu-id="54b4c-163">Wenn ein Anbieter [Protokollbereiche](#log-scopes) unterstützt, gibt `IncludeScopes` an, ob sie aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="54b4c-163">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="54b4c-164">Eine Anbietereigenschaft (z.B. `Console` im Beispiel) kann auch eine `LogLevel`-Eigenschaft angeben.</span><span class="sxs-lookup"><span data-stu-id="54b4c-164">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="54b4c-165">`LogLevel` unter einem Anbieter gibt die Grade an, die für diesen Anbieter protokolliert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-165">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="54b4c-166">Wenn Grade in `Logging.{providername}.LogLevel` angegeben werden, überschreiben sie alle Angaben in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="54b4c-166">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.1"

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    }
  }
}
```

<span data-ttu-id="54b4c-167">`LogLevel`-Schlüssel stellen Protokollnamen dar.</span><span class="sxs-lookup"><span data-stu-id="54b4c-167">`LogLevel` keys represent log names.</span></span> <span data-ttu-id="54b4c-168">Der `Default`-Schlüssel gilt für Protokolle, die nicht explizit aufgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-168">The `Default` key applies to logs not explicitly listed.</span></span> <span data-ttu-id="54b4c-169">Der Wert entspricht dem auf das jeweilige Protokoll angewendeten [Protokolliergrad](#log-level).</span><span class="sxs-lookup"><span data-stu-id="54b4c-169">The value represents the [log level](#log-level) applied to the given log.</span></span>

::: moniker-end

<span data-ttu-id="54b4c-170">Informationen zur Implementierung von Konfigurationsanbieter finden Sie hier: <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="54b4c-170">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="54b4c-171">Beispiel einer Protokollierungsausgabe</span><span class="sxs-lookup"><span data-stu-id="54b4c-171">Sample logging output</span></span>

<span data-ttu-id="54b4c-172">Mit dem im vorherigen Abschnitt gezeigten Beispielcode werden Protokolle in der Konsole angezeigt, wenn die Anwendung über die Befehlszeile ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="54b4c-172">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="54b4c-173">Hier ein Beispiel für eine Konsolenausgabe:</span><span class="sxs-lookup"><span data-stu-id="54b4c-173">Here's an example of console output:</span></span>

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

<span data-ttu-id="54b4c-174">Die vorherigen Protokolle wurden generiert, indem eine HTTP Get-Anforderung an die Beispiel-App unter `http://localhost:5000/api/todo/0` vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="54b4c-174">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="54b4c-175">Nachfolgend sehen Sie, wie die gleichen Protokolle im Debugfenster angezeigt werden, wenn Sie die Beispiel-App in Visual Studio ausführen:</span><span class="sxs-lookup"><span data-stu-id="54b4c-175">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="54b4c-176">Die über die `ILogger`-Aufrufe aus dem vorherigen Abschnitt erstellten Protokolle beginnen mit „TodoApi.Controllers.TodoController“.</span><span class="sxs-lookup"><span data-stu-id="54b4c-176">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi.Controllers.TodoController".</span></span> <span data-ttu-id="54b4c-177">Protokolle, die mit Microsoft-Kategorien beginnen, stammen aus ASP.NET Core-Frameworkcode.</span><span class="sxs-lookup"><span data-stu-id="54b4c-177">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="54b4c-178">ASP.NET Core und Anwendungscode verwenden dieselbe Protokollierungs-API und dieselben Protokollierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="54b4c-178">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="54b4c-179">In den übrigen Abschnitten dieses Artikels werden einige Details und Optionen für die Protokollierung erläutert.</span><span class="sxs-lookup"><span data-stu-id="54b4c-179">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="54b4c-180">NuGet-Pakete</span><span class="sxs-lookup"><span data-stu-id="54b4c-180">NuGet packages</span></span>

<span data-ttu-id="54b4c-181">Die Schnittstellen `ILogger` und `ILoggerFactory` befinden sich in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), ihre Standardimplementierungen sind in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) enthalten.</span><span class="sxs-lookup"><span data-stu-id="54b4c-181">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="54b4c-182">Protokollkategorie</span><span class="sxs-lookup"><span data-stu-id="54b4c-182">Log category</span></span>

<span data-ttu-id="54b4c-183">Wenn ein `ILogger`-Objekt erstellt wird, wird eine *Kategorie* für dieses Objekt angegeben.</span><span class="sxs-lookup"><span data-stu-id="54b4c-183">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="54b4c-184">Diese Kategorie ist in jeder Protokollmeldung enthalten, die von dieser Instanz von `ILogger` erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="54b4c-184">That category is included with each log message created by that instance of `ILogger`.</span></span> <span data-ttu-id="54b4c-185">Die Kategorie kann eine beliebige Zeichenfolge sein, aber die Konvention besteht in der Verwendung des Klassennamens, z.B. „TodoApi.Controllers.TodoController“.</span><span class="sxs-lookup"><span data-stu-id="54b4c-185">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="54b4c-186">Verwenden Sie `ILogger<T>` zum Abrufen einer `ILogger`-Instanz, die den vollqualifizierten Typnamen von `T` als Kategorie verwendet:</span><span class="sxs-lookup"><span data-stu-id="54b4c-186">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="54b4c-187">Um die Kategorie explizit anzugeben, rufen Sie `ILoggerFactory.CreateLogger` auf:</span><span class="sxs-lookup"><span data-stu-id="54b4c-187">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="54b4c-188">`ILogger<T>` entspricht dem Aufruf von `CreateLogger` mit dem vollqualifizierten Typnamen `T`.</span><span class="sxs-lookup"><span data-stu-id="54b4c-188">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="54b4c-189">Protokolliergrad</span><span class="sxs-lookup"><span data-stu-id="54b4c-189">Log level</span></span>

<span data-ttu-id="54b4c-190">Jedes Protokoll gibt einen <xref:Microsoft.Extensions.Logging.LogLevel>-Wert an.</span><span class="sxs-lookup"><span data-stu-id="54b4c-190">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="54b4c-191">Der Protokolliergrad gibt den Schweregrad oder die Wichtigkeit an.</span><span class="sxs-lookup"><span data-stu-id="54b4c-191">The log level indicates the severity or importance.</span></span> <span data-ttu-id="54b4c-192">Sie können beispielsweise ein `Information`-Protokoll schreiben, wenn eine Methode normal endet, und ein `Warning`-Protokoll, wenn eine Methode den Statuscode *404 Not Found* zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="54b4c-192">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="54b4c-193">Der folgende Code erstellt `Information`- und `Warning`-Protokolle:</span><span class="sxs-lookup"><span data-stu-id="54b4c-193">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="54b4c-194">Im Code oben ist der erste Parameter die [Protokollereignis-ID](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="54b4c-194">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="54b4c-195">Der zweite Parameter ist eine Meldungsvorlage mit Platzhaltern für Argumentwerte, die von den verbleibenden Methodenparametern bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-195">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="54b4c-196">Die Methodenparameter werden im [Abschnitt „Meldungsvorlage“](#log-message-template) später in diesem Artikel erläutert.</span><span class="sxs-lookup"><span data-stu-id="54b4c-196">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="54b4c-197">Protokollmethoden, die den Protokolliergrad im Methodennamen enthalten (z.B. `LogInformation` und `LogWarning`), sind [Erweiterungsmethoden für ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="54b4c-197">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="54b4c-198">Diese Methoden rufen eine `Log`-Methode auf, die einen `LogLevel`-Parameter annimmt.</span><span class="sxs-lookup"><span data-stu-id="54b4c-198">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="54b4c-199">Sie können anstelle eines Aufrufs dieser Erweiterungsmethoden die `Log`-Methode direkt aufrufen, aber die Syntax ist relativ kompliziert.</span><span class="sxs-lookup"><span data-stu-id="54b4c-199">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="54b4c-200">Weitere Informationen finden Sie unter <xref:Microsoft.Extensions.Logging.ILogger> und im [Quellcode für Protokollierungserweiterungen](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="54b4c-200">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="54b4c-201">ASP.NET Core definiert die folgenden Protokolliergrade. Die Reihenfolge reicht vom geringsten bis zum höchsten Schweregrad.</span><span class="sxs-lookup"><span data-stu-id="54b4c-201">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="54b4c-202">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="54b4c-202">Trace = 0</span></span>

  <span data-ttu-id="54b4c-203">Protokolliert Informationen, die in der Regel nur für das Debuggen nützlich sind.</span><span class="sxs-lookup"><span data-stu-id="54b4c-203">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="54b4c-204">Diese Meldungen können sensible Anwendungsdaten enthalten und sollten daher nicht in einer Produktionsumgebung aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-204">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="54b4c-205">*Standardmäßig deaktiviert.*</span><span class="sxs-lookup"><span data-stu-id="54b4c-205">*Disabled by default.*</span></span>

* <span data-ttu-id="54b4c-206">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="54b4c-206">Debug = 1</span></span>

  <span data-ttu-id="54b4c-207">Protokolliert Informationen, die bei der Entwicklung und beim Debuggen hilfreich sein können.</span><span class="sxs-lookup"><span data-stu-id="54b4c-207">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="54b4c-208">Beispiel: `Entering method Configure with flag set to true.` Aktivieren Sie Protokolle mit dem Protokolliergrad `Debug` aufgrund des hohen Protokollaufkommens nur zur Problembehandlung in der Produktion.</span><span class="sxs-lookup"><span data-stu-id="54b4c-208">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="54b4c-209">Information = 2</span><span class="sxs-lookup"><span data-stu-id="54b4c-209">Information = 2</span></span>

  <span data-ttu-id="54b4c-210">Zur Nachverfolgung des allgemeinen Ablaufs der App.</span><span class="sxs-lookup"><span data-stu-id="54b4c-210">For tracking the general flow of the app.</span></span> <span data-ttu-id="54b4c-211">Diese Protokolle haben typischerweise einen langfristigen Nutzen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-211">These logs typically have some long-term value.</span></span> <span data-ttu-id="54b4c-212">Ein Beispiel: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="54b4c-212">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="54b4c-213">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="54b4c-213">Warning = 3</span></span>

  <span data-ttu-id="54b4c-214">Für ungewöhnliche oder unerwartete Ereignisse im App-Verlauf.</span><span class="sxs-lookup"><span data-stu-id="54b4c-214">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="54b4c-215">Dazu können Fehler oder andere Bedingungen gehören, die zwar nicht zum Beenden der App führen, aber eventuell untersucht werden müssen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-215">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="54b4c-216">Behandelte Ausnahmen sind eine typische Verwendung für den Protokolliergrad `Warning`.</span><span class="sxs-lookup"><span data-stu-id="54b4c-216">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="54b4c-217">Ein Beispiel: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="54b4c-217">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="54b4c-218">Error = 4</span><span class="sxs-lookup"><span data-stu-id="54b4c-218">Error = 4</span></span>

  <span data-ttu-id="54b4c-219">Für Fehler und Ausnahmen, die nicht behandelt werden können.</span><span class="sxs-lookup"><span data-stu-id="54b4c-219">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="54b4c-220">Diese Meldungen weisen auf einen Fehler in der aktuellen Aktivität oder im aktuellen Vorgang (z.B. in der aktuellen HTTP-Anforderung) und nicht auf einen anwendungsweiten Fehler hin.</span><span class="sxs-lookup"><span data-stu-id="54b4c-220">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="54b4c-221">Beispielprotokollmeldung: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="54b4c-221">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="54b4c-222">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="54b4c-222">Critical = 5</span></span>

  <span data-ttu-id="54b4c-223">Für Fehler, die sofortige Aufmerksamkeit erfordern.</span><span class="sxs-lookup"><span data-stu-id="54b4c-223">For failures that require immediate attention.</span></span> <span data-ttu-id="54b4c-224">Beispiel: Szenarios mit Datenverlust, Speichermangel.</span><span class="sxs-lookup"><span data-stu-id="54b4c-224">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="54b4c-225">Verwenden Sie den Protokolliergrad, um die Menge an Protokollausgabedaten zu steuern, die in ein bestimmtes Speichermedium geschrieben oder an ein Anzeigefenster ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-225">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="54b4c-226">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="54b4c-226">For example:</span></span>

* <span data-ttu-id="54b4c-227">Senden Sie in der Produktion `Trace` über den Protokolliergrad `Information` an einen Volumedatenspeicher.</span><span class="sxs-lookup"><span data-stu-id="54b4c-227">In production, send `Trace` through `Information` level to a volume data store.</span></span> <span data-ttu-id="54b4c-228">Senden Sie `Warning` über `Critical` an einen Wertdatenspeicher.</span><span class="sxs-lookup"><span data-stu-id="54b4c-228">Send `Warning` through `Critical` to a value data store.</span></span>
* <span data-ttu-id="54b4c-229">Senden Sie während der Entwicklung `Warning` über `Critical` an die Konsole, und fügen Sie `Trace` über `Information` bei der Problembehandlung hinzu.</span><span class="sxs-lookup"><span data-stu-id="54b4c-229">During development, send `Warning` through `Critical` to the console, and add `Trace` through `Information` when troubleshooting.</span></span>

<span data-ttu-id="54b4c-230">Im Abschnitt [Protokollfilterung](#log-filtering) weiter unten in diesem Artikel wird erläutert, wie Sie steuern, welche Protokolliergrade ein Anbieter verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="54b4c-230">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="54b4c-231">ASP.NET Core schreibt Protokolle für Frameworkereignisse.</span><span class="sxs-lookup"><span data-stu-id="54b4c-231">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="54b4c-232">Die zuvor in diesem Artikel gezeigten Protokollbeispiele enthielten Protokolle unterhalb des Protokolliergrads `Information`, deshalb wurden keine Protokolle des Protokolliergrads `Debug` oder `Trace` erstellt.</span><span class="sxs-lookup"><span data-stu-id="54b4c-232">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="54b4c-233">Hier ist ein Beispiel für Konsolenprotokolle, die durch Ausführen der Beispiel-App generiert werden, die so konfiguriert ist, dass sie `Debug`-Protokolle anzeigt:</span><span class="sxs-lookup"><span data-stu-id="54b4c-233">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="54b4c-234">Protokollereignis-ID</span><span class="sxs-lookup"><span data-stu-id="54b4c-234">Log event ID</span></span>

<span data-ttu-id="54b4c-235">Jedes Protokoll kann eine *Ereignis-ID* angeben.</span><span class="sxs-lookup"><span data-stu-id="54b4c-235">Each log can specify an *event ID*.</span></span> <span data-ttu-id="54b4c-236">Die Beispiel-App verwendet hierzu eine lokal definierte `LoggingEvents`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="54b4c-236">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="54b4c-237">Eine Ereignis-ID ordnet eine Gruppe von Ereignissen zu.</span><span class="sxs-lookup"><span data-stu-id="54b4c-237">An event ID associates a set of events.</span></span> <span data-ttu-id="54b4c-238">Beispielsweise können alle Protokolle, die sich auf die Anzeige einer Liste von Elementen auf einer Seite beziehen, 1001 sein.</span><span class="sxs-lookup"><span data-stu-id="54b4c-238">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="54b4c-239">Der Protokollierungsanbieter kann die Ereignis-ID in einem ID-Feld, in der Protokollierungsmeldung oder gar nicht speichern.</span><span class="sxs-lookup"><span data-stu-id="54b4c-239">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="54b4c-240">Der Debuganbieter zeigt keine Ereignis-IDs an.</span><span class="sxs-lookup"><span data-stu-id="54b4c-240">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="54b4c-241">Der Konsolenanbieter zeigt Ereignis-IDs in Klammern hinter der Kategorie an:</span><span class="sxs-lookup"><span data-stu-id="54b4c-241">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="54b4c-242">Protokollmeldungsvorlage</span><span class="sxs-lookup"><span data-stu-id="54b4c-242">Log message template</span></span>

<span data-ttu-id="54b4c-243">Jedes Protokoll gibt eine Meldungsvorlage an.</span><span class="sxs-lookup"><span data-stu-id="54b4c-243">Each log specifies a message template.</span></span> <span data-ttu-id="54b4c-244">Die Meldungsvorlage kann Platzhalter enthalten, für die Argumente bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-244">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="54b4c-245">Verwenden Sie Namen für die Platzhalter, keine Zahlen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-245">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="54b4c-246">Die Reihenfolge der Platzhalter – nicht ihre Namen – bestimmt, welche Parameter verwendet werden, um ihre Werte zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-246">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="54b4c-247">Beachten Sie im folgenden Code, dass die Parameternamen in der Meldungsvorlage nicht in der richtigen Reihenfolge vorhanden sind:</span><span class="sxs-lookup"><span data-stu-id="54b4c-247">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="54b4c-248">Dieser Code erstellt eine Protokollierungsmeldung mit den Parameterwerten in der richtigen Reihenfolge:</span><span class="sxs-lookup"><span data-stu-id="54b4c-248">This code creates a log message with the parameter values in sequence:</span></span>

```
Parameter values: parm1, parm2
```

<span data-ttu-id="54b4c-249">Das Protokollierungsframework funktioniert auf diese Weise, damit Protokollierungsanbieter [semantische Protokollierung, die auch als strukturierte Protokollierung bezeichnet wird](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging), implementieren können.</span><span class="sxs-lookup"><span data-stu-id="54b4c-249">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="54b4c-250">Die Argumente selbst (nicht nur die formatierte Meldungsvorlage) werden an das Protokollierungssystem übergeben.</span><span class="sxs-lookup"><span data-stu-id="54b4c-250">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="54b4c-251">Diese Informationen ermöglichen es Protokollierungsanbietern, die Parameterwerte als Felder zu speichern.</span><span class="sxs-lookup"><span data-stu-id="54b4c-251">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="54b4c-252">Nehmen wir zum Beispiel an, dass Aufrufe von Protokollierungsmethoden folgendermaßen aussehen:</span><span class="sxs-lookup"><span data-stu-id="54b4c-252">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="54b4c-253">Wenn Sie die Protokolle an Azure Table Storage senden, kann jede Azure Table-Entität die Eigenschaften `ID` und `RequestTime` aufweisen. Dies vereinfacht die Abfrage von Protokolldaten.</span><span class="sxs-lookup"><span data-stu-id="54b4c-253">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="54b4c-254">Eine Abfrage kann alle Protokolle in einem bestimmten `RequestTime`-Bereich finden, ohne die Uhrzeit aus den Textmeldungen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="54b4c-254">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="54b4c-255">Protokollieren von Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="54b4c-255">Logging exceptions</span></span>

<span data-ttu-id="54b4c-256">Die Protokollierungsmethoden umfassen Überladungen, die das Übergeben von Ausnahmen ermöglichen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="54b4c-256">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="54b4c-257">Die verschiedenen Anbieter verarbeiten die Ausnahmeinformationen auf unterschiedliche Weise.</span><span class="sxs-lookup"><span data-stu-id="54b4c-257">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="54b4c-258">Hier sehen Sie ein Beispiel einer Debuganbieterausgabe aus dem obigen Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="54b4c-258">Here's an example of Debug provider output from the code shown above.</span></span>

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="54b4c-259">Protokollfilterung</span><span class="sxs-lookup"><span data-stu-id="54b4c-259">Log filtering</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="54b4c-260">Sie können einen Mindestprotokolliergrad für einen bestimmten Anbieter und eine bestimmte Kategorie oder für alle Anbieter oder alle Kategorien festlegen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-260">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="54b4c-261">Alle Protokolle unter dem Mindestgrad werden nicht an diesen Anbieter weitergeleitet, sodass sie nicht angezeigt oder gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-261">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="54b4c-262">Wenn Sie alle Protokolle unterdrücken möchten, geben Sie `LogLevel.None` als Mindestprotokolliergrad an.</span><span class="sxs-lookup"><span data-stu-id="54b4c-262">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="54b4c-263">Der ganzzahlige Wert von `LogLevel.None` lautet 6 und liegt über `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="54b4c-263">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="54b4c-264">Erstellen von Filterregeln in der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="54b4c-264">Create filter rules in configuration</span></span>

<span data-ttu-id="54b4c-265">Der Projektvorlagencode ruft `CreateDefaultBuilder` auf, um die Protokollierung für die Konsolen- und Debuganbieter einzurichten.</span><span class="sxs-lookup"><span data-stu-id="54b4c-265">The project template code calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="54b4c-266">Die `CreateDefaultBuilder`-Methode richtet die Protokollierung auch ein, um nach der Konfiguration in einem `Logging`-Abschnitt zu suchen. Hierbei wird Code wie der folgende verwendet:</span><span class="sxs-lookup"><span data-stu-id="54b4c-266">The `CreateDefaultBuilder` method also sets up logging to look for configuration in a `Logging` section, using code like the following:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=16)]

<span data-ttu-id="54b4c-267">Die Konfigurationsdaten geben die Mindestprotokolliergrade nach Anbieter und Kategorie an, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="54b4c-267">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="54b4c-268">Diese JSON-Konfiguration erstellt sechs Filterregeln: eine für den Debuganbieter, vier für den Konsolenanbieter und eine für alle Anbieter.</span><span class="sxs-lookup"><span data-stu-id="54b4c-268">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="54b4c-269">Eine einzelne Regel wird für jeden Anbieter ausgewählt, wenn ein `ILogger`-Objekt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="54b4c-269">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="54b4c-270">Filterregeln im Code</span><span class="sxs-lookup"><span data-stu-id="54b4c-270">Filter rules in code</span></span>

<span data-ttu-id="54b4c-271">Das folgende Beispiel zeigt, wie Filterregeln im Code registriert werden:</span><span class="sxs-lookup"><span data-stu-id="54b4c-271">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="54b4c-272">Das zweite `AddFilter` gibt den Debuganbieter durch Verwendung des zugehörigen Typnamens an.</span><span class="sxs-lookup"><span data-stu-id="54b4c-272">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="54b4c-273">Das erste `AddFilter` gilt für alle Anbieter, weil kein Anbietertyp angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="54b4c-273">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="54b4c-274">Anwendung von Filterregeln</span><span class="sxs-lookup"><span data-stu-id="54b4c-274">How filtering rules are applied</span></span>

<span data-ttu-id="54b4c-275">Die Konfigurationsdaten und der in den vorangegangenen Beispielen gezeigte `AddFilter`-Code erzeugen die in der folgenden Tabelle gezeigten Regeln.</span><span class="sxs-lookup"><span data-stu-id="54b4c-275">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="54b4c-276">Die ersten sechs Regeln stammen aus dem Konfigurationsbeispiel, die letzten zwei Filter stammen aus dem Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="54b4c-276">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="54b4c-277">Anzahl</span><span class="sxs-lookup"><span data-stu-id="54b4c-277">Number</span></span> | <span data-ttu-id="54b4c-278">Anbieter</span><span class="sxs-lookup"><span data-stu-id="54b4c-278">Provider</span></span>      | <span data-ttu-id="54b4c-279">Kategorien beginnend mit...</span><span class="sxs-lookup"><span data-stu-id="54b4c-279">Categories that begin with ...</span></span>          | <span data-ttu-id="54b4c-280">Mindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="54b4c-280">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="54b4c-281">1</span><span class="sxs-lookup"><span data-stu-id="54b4c-281">1</span></span>      | <span data-ttu-id="54b4c-282">Debug</span><span class="sxs-lookup"><span data-stu-id="54b4c-282">Debug</span></span>         | <span data-ttu-id="54b4c-283">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="54b4c-283">All categories</span></span>                          | <span data-ttu-id="54b4c-284">Information</span><span class="sxs-lookup"><span data-stu-id="54b4c-284">Information</span></span>       |
| <span data-ttu-id="54b4c-285">2</span><span class="sxs-lookup"><span data-stu-id="54b4c-285">2</span></span>      | <span data-ttu-id="54b4c-286">Konsole</span><span class="sxs-lookup"><span data-stu-id="54b4c-286">Console</span></span>       | <span data-ttu-id="54b4c-287">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="54b4c-287">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="54b4c-288">Warnung</span><span class="sxs-lookup"><span data-stu-id="54b4c-288">Warning</span></span>           |
| <span data-ttu-id="54b4c-289">3</span><span class="sxs-lookup"><span data-stu-id="54b4c-289">3</span></span>      | <span data-ttu-id="54b4c-290">Konsole</span><span class="sxs-lookup"><span data-stu-id="54b4c-290">Console</span></span>       | <span data-ttu-id="54b4c-291">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="54b4c-291">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="54b4c-292">Debug</span><span class="sxs-lookup"><span data-stu-id="54b4c-292">Debug</span></span>             |
| <span data-ttu-id="54b4c-293">4</span><span class="sxs-lookup"><span data-stu-id="54b4c-293">4</span></span>      | <span data-ttu-id="54b4c-294">Konsole</span><span class="sxs-lookup"><span data-stu-id="54b4c-294">Console</span></span>       | <span data-ttu-id="54b4c-295">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="54b4c-295">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="54b4c-296">Fehler</span><span class="sxs-lookup"><span data-stu-id="54b4c-296">Error</span></span>             |
| <span data-ttu-id="54b4c-297">5</span><span class="sxs-lookup"><span data-stu-id="54b4c-297">5</span></span>      | <span data-ttu-id="54b4c-298">Konsole</span><span class="sxs-lookup"><span data-stu-id="54b4c-298">Console</span></span>       | <span data-ttu-id="54b4c-299">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="54b4c-299">All categories</span></span>                          | <span data-ttu-id="54b4c-300">Information</span><span class="sxs-lookup"><span data-stu-id="54b4c-300">Information</span></span>       |
| <span data-ttu-id="54b4c-301">6</span><span class="sxs-lookup"><span data-stu-id="54b4c-301">6</span></span>      | <span data-ttu-id="54b4c-302">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="54b4c-302">All providers</span></span> | <span data-ttu-id="54b4c-303">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="54b4c-303">All categories</span></span>                          | <span data-ttu-id="54b4c-304">Debug</span><span class="sxs-lookup"><span data-stu-id="54b4c-304">Debug</span></span>             |
| <span data-ttu-id="54b4c-305">7</span><span class="sxs-lookup"><span data-stu-id="54b4c-305">7</span></span>      | <span data-ttu-id="54b4c-306">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="54b4c-306">All providers</span></span> | <span data-ttu-id="54b4c-307">System</span><span class="sxs-lookup"><span data-stu-id="54b4c-307">System</span></span>                                  | <span data-ttu-id="54b4c-308">Debug</span><span class="sxs-lookup"><span data-stu-id="54b4c-308">Debug</span></span>             |
| <span data-ttu-id="54b4c-309">8</span><span class="sxs-lookup"><span data-stu-id="54b4c-309">8</span></span>      | <span data-ttu-id="54b4c-310">Debug</span><span class="sxs-lookup"><span data-stu-id="54b4c-310">Debug</span></span>         | <span data-ttu-id="54b4c-311">Microsoft</span><span class="sxs-lookup"><span data-stu-id="54b4c-311">Microsoft</span></span>                               | <span data-ttu-id="54b4c-312">Ablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="54b4c-312">Trace</span></span>             |

<span data-ttu-id="54b4c-313">Wenn ein `ILogger`-Objekt erstellt wird, wählt das `ILoggerFactory`-Objekt eine einzige Regel pro Anbieter aus, die auf diese Protokollierung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="54b4c-313">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="54b4c-314">Alle von einer `ILogger`-Instanz geschriebenen Meldungen werden auf Grundlage der ausgewählten Regeln gefiltert.</span><span class="sxs-lookup"><span data-stu-id="54b4c-314">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="54b4c-315">Aus den verfügbaren Regeln wird die für jeden Anbieter und jedes Kategoriepaar spezifischste Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="54b4c-315">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="54b4c-316">Beim Erstellen von `ILogger` für eine vorgegebene Kategorie wird für jeden Anbieter der folgende Algorithmus verwendet:</span><span class="sxs-lookup"><span data-stu-id="54b4c-316">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="54b4c-317">Es werden alle Regeln ausgewählt, die dem Anbieter oder dem zugehörigen Alias entsprechen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-317">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="54b4c-318">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln mit einem leeren Anbieter ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="54b4c-318">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="54b4c-319">Aus dem Ergebnis des vorherigen Schritts werden die Regeln mit dem längsten übereinstimmenden Kategoriepräfix ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="54b4c-319">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="54b4c-320">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln ohne Angabe einer Kategorie ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="54b4c-320">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="54b4c-321">Wenn mehrere Regeln ausgewählt sind, wird die **letzte** Regel verwendet.</span><span class="sxs-lookup"><span data-stu-id="54b4c-321">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="54b4c-322">Wenn keine Regel ausgewählt ist, wird `MinimumLevel` verwendet.</span><span class="sxs-lookup"><span data-stu-id="54b4c-322">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="54b4c-323">Angenommen, Sie erstellen mit der oben aufgeführten Liste mit Regeln ein `ILogger`-Objekt für die Kategorie „Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine“:</span><span class="sxs-lookup"><span data-stu-id="54b4c-323">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="54b4c-324">Für den Debuganbieter gelten die Regeln 1, 6 und 8.</span><span class="sxs-lookup"><span data-stu-id="54b4c-324">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="54b4c-325">Regel 8 ist die spezifischste Regel, deshalb wird diese Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="54b4c-325">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="54b4c-326">Für den Konsolenanbieter gelten die Regeln 3, 4, 5 und 6.</span><span class="sxs-lookup"><span data-stu-id="54b4c-326">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="54b4c-327">Regel 3 ist die spezifischste Regel.</span><span class="sxs-lookup"><span data-stu-id="54b4c-327">Rule 3 is most specific.</span></span>

<span data-ttu-id="54b4c-328">Die sich ergebende `ILogger`-Instanz sendet Protokolle mit dem Protokolliergrad `Trace` und höher an den Debuganbieter.</span><span class="sxs-lookup"><span data-stu-id="54b4c-328">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="54b4c-329">Protokolle mit dem Protokolliergrad `Debug` und höher werden an den Konsolenanbieter gesendet.</span><span class="sxs-lookup"><span data-stu-id="54b4c-329">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="54b4c-330">Anbieteraliase</span><span class="sxs-lookup"><span data-stu-id="54b4c-330">Provider aliases</span></span>

<span data-ttu-id="54b4c-331">Jeder Anbieter definiert einen *Alias*, der in der Konfiguration anstelle des vollqualifizierten Typnamens verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="54b4c-331">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="54b4c-332">Verwenden Sie für die integrierten Anbieter die folgenden Aliase:</span><span class="sxs-lookup"><span data-stu-id="54b4c-332">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="54b4c-333">Konsole</span><span class="sxs-lookup"><span data-stu-id="54b4c-333">Console</span></span>
* <span data-ttu-id="54b4c-334">Debug</span><span class="sxs-lookup"><span data-stu-id="54b4c-334">Debug</span></span>
* <span data-ttu-id="54b4c-335">EventSource</span><span class="sxs-lookup"><span data-stu-id="54b4c-335">EventSource</span></span>
* <span data-ttu-id="54b4c-336">EventLog</span><span class="sxs-lookup"><span data-stu-id="54b4c-336">EventLog</span></span>
* <span data-ttu-id="54b4c-337">TraceSource</span><span class="sxs-lookup"><span data-stu-id="54b4c-337">TraceSource</span></span>
* <span data-ttu-id="54b4c-338">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="54b4c-338">AzureAppServicesFile</span></span>
* <span data-ttu-id="54b4c-339">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="54b4c-339">AzureAppServicesBlob</span></span>
* <span data-ttu-id="54b4c-340">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="54b4c-340">ApplicationInsights</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="54b4c-341">Standardmindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="54b4c-341">Default minimum level</span></span>

<span data-ttu-id="54b4c-342">Es gibt eine Einstellung für den Mindestprotokolliergrad, die nur dann wirksam wird, wenn für einen bestimmten Anbieter und eine bestimmte Kategorie keine Regeln aus Konfiguration oder Code gelten.</span><span class="sxs-lookup"><span data-stu-id="54b4c-342">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="54b4c-343">Im folgenden Beispiel wird das Festlegen des Mindestprotokolliergrads veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="54b4c-343">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="54b4c-344">Wenn Sie den Mindestprotokolliergrad nicht explizit festlegen, lautet der Standardwert `Information`, d.h. Protokolle der Ebene `Trace` und `Debug` werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="54b4c-344">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="54b4c-345">Filterfunktionen</span><span class="sxs-lookup"><span data-stu-id="54b4c-345">Filter functions</span></span>

<span data-ttu-id="54b4c-346">Eine Filterfunktion wird für alle Anbieter und Kategorien aufgerufen, denen keine Regeln durch Konfiguration oder Code zugewiesen sind.</span><span class="sxs-lookup"><span data-stu-id="54b4c-346">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="54b4c-347">Code in der Funktion verfügt über Zugriff auf den Anbietertyp, die Kategorie und den Protokolliergrad.</span><span class="sxs-lookup"><span data-stu-id="54b4c-347">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="54b4c-348">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="54b4c-348">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="54b4c-349">Bei einigen Protokollierungsanbietern können Sie angeben, wann Protokolle in ein Speichermedium geschrieben oder ignoriert werden sollen – je nach Protokolliergrad und Kategorie.</span><span class="sxs-lookup"><span data-stu-id="54b4c-349">Some logging providers let you specify when logs should be written to a storage medium or ignored based on log level and category.</span></span>

<span data-ttu-id="54b4c-350">Die Erweiterungsmethoden `AddConsole` und `AddDebug` bieten Überladungen, die Filterkriterien annehmen können.</span><span class="sxs-lookup"><span data-stu-id="54b4c-350">The `AddConsole` and `AddDebug` extension methods provide overloads that accept filtering criteria.</span></span> <span data-ttu-id="54b4c-351">Der folgende Beispielcode veranlasst den Konsolenanbieter, Protokolle unterhalb der Ebene `Warning` zu ignorieren, während der Debuganbieter vom Framework erstellte Protokolle ignoriert.</span><span class="sxs-lookup"><span data-stu-id="54b4c-351">The following sample code causes the console provider to ignore logs below `Warning` level, while the Debug provider ignores logs that the framework creates.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

<span data-ttu-id="54b4c-352">Die Methode `AddEventLog` umfasst eine Überladung mit einer `EventLogSettings`-Instanz, die eine Filterfunktion in ihrer `Filter`-Eigenschaft enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="54b4c-352">The `AddEventLog` method has an overload that takes an `EventLogSettings` instance, which may contain a filtering function in its `Filter` property.</span></span> <span data-ttu-id="54b4c-353">Der TraceSource-Anbieter stellt keine dieser Überladungen zur Verfügung, da der zugehörige Protokolliergrad und weitere Parameter auf der Verwendung von `SourceSwitch` und `TraceListener` basieren.</span><span class="sxs-lookup"><span data-stu-id="54b4c-353">The TraceSource provider doesn't provide any of those overloads, since its logging level and other parameters are based on the `SourceSwitch` and `TraceListener` it uses.</span></span>

<span data-ttu-id="54b4c-354">Um Filterregeln für alle bei einer `ILoggerFactory`-Instanz registrierten Anbieter festzulegen, verwenden Sie die Erweiterungsmethode `WithFilter`.</span><span class="sxs-lookup"><span data-stu-id="54b4c-354">To set filtering rules for all providers that are registered with an `ILoggerFactory` instance, use the `WithFilter` extension method.</span></span> <span data-ttu-id="54b4c-355">Das folgende Beispiel beschränkt Frameworkprotokolle (Kategorie beginnt mit „Microsoft“ oder „System“) auf Warnungen, während die Protokollierung auf Debugebene für Protokolle erfolgt, die durch Anwendungscode erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-355">The example below limits framework logs (category begins with "Microsoft" or "System") to warnings while logging at debug level for logs created by application code.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

<span data-ttu-id="54b4c-356">Um zu verhindern, dass Protokolle geschrieben werden, geben Sie `LogLevel.None` als Mindestprotokolliergrad an.</span><span class="sxs-lookup"><span data-stu-id="54b4c-356">To prevent any logs from being written, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="54b4c-357">Der ganzzahlige Wert von `LogLevel.None` lautet 6 und liegt über `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="54b4c-357">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="54b4c-358">Die Erweiterungsmethode `WithFilter` wird vom NuGet-Paket [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="54b4c-358">The `WithFilter` extension method is provided by the [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet package.</span></span> <span data-ttu-id="54b4c-359">Die Methode gibt eine neue `ILoggerFactory`-Instanz zurück, die Protokollmeldungen für alle bei ihr registrierten Protokollierungsanbieter filtert.</span><span class="sxs-lookup"><span data-stu-id="54b4c-359">The method returns a new `ILoggerFactory` instance that will filter the log messages passed to all logger providers registered with it.</span></span> <span data-ttu-id="54b4c-360">Andere `ILoggerFactory`-Instanzen, die ursprüngliche `ILoggerFactory`-Instanz eingeschlossen, sind nicht betroffen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-360">It doesn't affect any other `ILoggerFactory` instances, including the original `ILoggerFactory` instance.</span></span>

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="54b4c-361">Systemkategorien und -protokolliergrade</span><span class="sxs-lookup"><span data-stu-id="54b4c-361">System categories and levels</span></span>

<span data-ttu-id="54b4c-362">Dies sind einige Kategorien, die vom ASP.NET Core und Entity Framework Core verwendet werden, mit Hinweisen darauf, welche Protokolle von ihnen zu erwarten sind:</span><span class="sxs-lookup"><span data-stu-id="54b4c-362">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="54b4c-363">Kategorie</span><span class="sxs-lookup"><span data-stu-id="54b4c-363">Category</span></span>                            | <span data-ttu-id="54b4c-364">Hinweise</span><span class="sxs-lookup"><span data-stu-id="54b4c-364">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="54b4c-365">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="54b4c-365">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="54b4c-366">Allgemeine ASP.NET Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="54b4c-366">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="54b4c-367">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="54b4c-367">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="54b4c-368">Gibt an, welche Schlüssel in Betracht gezogen, gefunden und verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-368">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="54b4c-369">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="54b4c-369">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="54b4c-370">Hosts sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="54b4c-370">Hosts allowed.</span></span> |
| <span data-ttu-id="54b4c-371">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="54b4c-371">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="54b4c-372">Gibt an, wie lange es bis zum Abschluss von HTTP-Anforderungen gedauert hat und zu welcher Zeit sie gestartet wurden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-372">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="54b4c-373">Gibt an, welche Hostingstartassemblys geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-373">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="54b4c-374">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="54b4c-374">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="54b4c-375">MVC- und Razor-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="54b4c-375">MVC and Razor diagnostics.</span></span> <span data-ttu-id="54b4c-376">Modellbindung, Filterausführung, Ansichtskompilierung, Aktionsauswahl.</span><span class="sxs-lookup"><span data-stu-id="54b4c-376">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="54b4c-377">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="54b4c-377">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="54b4c-378">Gibt Routenabgleichsinformationen an.</span><span class="sxs-lookup"><span data-stu-id="54b4c-378">Route matching information.</span></span> |
| <span data-ttu-id="54b4c-379">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="54b4c-379">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="54b4c-380">Start-, Beendigungs- und Keep-Alive-Antworten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="54b4c-380">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="54b4c-381">HTTPS-Zertifikatinformationen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-381">HTTPS certificate information.</span></span> |
| <span data-ttu-id="54b4c-382">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="54b4c-382">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="54b4c-383">Die bereitgestellten Dateien.</span><span class="sxs-lookup"><span data-stu-id="54b4c-383">Files served.</span></span> |
| <span data-ttu-id="54b4c-384">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="54b4c-384">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="54b4c-385">Allgemeine Entity Framework Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="54b4c-385">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="54b4c-386">Datenbankaktivität und -konfiguration, Änderungserkennung, Migrationen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-386">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="54b4c-387">Protokollbereiche</span><span class="sxs-lookup"><span data-stu-id="54b4c-387">Log scopes</span></span>

 <span data-ttu-id="54b4c-388">Ein *Bereich* kann einen Satz logischer Vorgänge gruppieren.</span><span class="sxs-lookup"><span data-stu-id="54b4c-388">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="54b4c-389">Diese Gruppierung kann verwendet werden, um an jedes Protokoll, das als Teil einer Gruppe erstellt wird, die gleichen Daten anzufügen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-389">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="54b4c-390">So kann beispielsweise jedes Protokoll, das im Rahmen der Verarbeitung einer Transaktion erstellt wird, die Transaktions-ID enthalten.</span><span class="sxs-lookup"><span data-stu-id="54b4c-390">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="54b4c-391">Ein Bereich ist ein `IDisposable`-Typ, der von der <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>-Methode zurückgegeben und so lange beibehalten wird, bis er verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="54b4c-391">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="54b4c-392">Verwenden Sie einen Bereich, indem Sie Protokollierungsaufrufe mit einem `using`-Block umschließen, der als Wrapper verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="54b4c-392">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="54b4c-393">Der folgende Code aktiviert Bereiche für den Konsolenanbieter:</span><span class="sxs-lookup"><span data-stu-id="54b4c-393">The following code enables scopes for the console provider:</span></span>

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="54b4c-394">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="54b4c-394">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="54b4c-395">Um die bereichsbasierte Protokollierung zu aktivieren, muss die Konsolenprotokollierungsoption `IncludeScopes` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-395">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="54b4c-396">Weitere Informationen zur Konfiguration finden Sie im Abschnitt [Konfiguration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="54b4c-396">For information on configuration, see the [Configuration](#configuration) section.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="54b4c-397">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="54b4c-397">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="54b4c-398">Um die bereichsbasierte Protokollierung zu aktivieren, muss die Konsolenprotokollierungsoption `IncludeScopes` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-398">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="54b4c-399">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="54b4c-399">*Startup.cs*:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

<span data-ttu-id="54b4c-400">Jede Protokollmeldung enthält die bereichsbezogenen Informationen:</span><span class="sxs-lookup"><span data-stu-id="54b4c-400">Each log message includes the scoped information:</span></span>

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="54b4c-401">Integrierte Protokollierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="54b4c-401">Built-in logging providers</span></span>

<span data-ttu-id="54b4c-402">ASP.NET Core wird mit den folgenden Anbietern bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="54b4c-402">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="54b4c-403">Konsole</span><span class="sxs-lookup"><span data-stu-id="54b4c-403">Console</span></span>](#console-provider)
* [<span data-ttu-id="54b4c-404">Debuggen</span><span class="sxs-lookup"><span data-stu-id="54b4c-404">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="54b4c-405">EventSource</span><span class="sxs-lookup"><span data-stu-id="54b4c-405">EventSource</span></span>](#eventsource-provider)
* [<span data-ttu-id="54b4c-406">EventLog</span><span class="sxs-lookup"><span data-stu-id="54b4c-406">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="54b4c-407">TraceSource</span><span class="sxs-lookup"><span data-stu-id="54b4c-407">TraceSource</span></span>](#tracesource-provider)
* [<span data-ttu-id="54b4c-408">AzureAppServicesFile</span><span class="sxs-lookup"><span data-stu-id="54b4c-408">AzureAppServicesFile</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="54b4c-409">AzureAppServicesBlob</span><span class="sxs-lookup"><span data-stu-id="54b4c-409">AzureAppServicesBlob</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="54b4c-410">ApplicationInsights</span><span class="sxs-lookup"><span data-stu-id="54b4c-410">ApplicationInsights</span></span>](#azure-application-insights-trace-logging)

<span data-ttu-id="54b4c-411">Weitere Informationen zur stdout-Protokollierung finden Sie unter <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> und <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>.</span><span class="sxs-lookup"><span data-stu-id="54b4c-411">For information about stdout logging, see <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> and <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="54b4c-412">Der Konsolenanbieter</span><span class="sxs-lookup"><span data-stu-id="54b4c-412">Console provider</span></span>

<span data-ttu-id="54b4c-413">Das Anbieterpaket [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sendet eine Protokollausgabe an die Konsole.</span><span class="sxs-lookup"><span data-stu-id="54b4c-413">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddConsole();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddConsole();
```

<span data-ttu-id="54b4c-414">Mithilfe von [AddConsole-Überladungen](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) können Sie einen Mindestprotokolliergrad, eine Filterfunktion und einen booleschen Wert übergeben, der angibt, ob Bereiche unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-414">[AddConsole overloads](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) let you pass in a minimum log level, a filter function, and a boolean that indicates whether scopes are supported.</span></span> <span data-ttu-id="54b4c-415">Darüber hinaus kann ein `IConfiguration`-Objekt übergeben werden, mit dem Bereichsunterstützung und Protokolliergrade angegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="54b4c-415">Another option is to pass in an `IConfiguration` object, which can specify scopes support and logging levels.</span></span>

<span data-ttu-id="54b4c-416">Der Konsolenanbieter hat einen erheblichen Einfluss auf die Leistung und ist allgemein nicht für den Einsatz in der Produktion geeignet.</span><span class="sxs-lookup"><span data-stu-id="54b4c-416">The console provider has a significant impact on performance and is generally not appropriate for use in production.</span></span>

<span data-ttu-id="54b4c-417">Wenn Sie ein neues Projekt in Visual Studio erstellen, sieht die `AddConsole`-Methode folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="54b4c-417">When you create a new project in Visual Studio, the `AddConsole` method looks like this:</span></span>

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

<span data-ttu-id="54b4c-418">Dieser Code verweist auf den `Logging`-Abschnitt der Datei *appSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="54b4c-418">This code refers to the `Logging` section of the *appSettings.json* file:</span></span>

[!code-json[](index/samples/1.x/TodoApiSample//appsettings.json)]

<span data-ttu-id="54b4c-419">Die gezeigten Einstellungen schränken die Frameworkprotokolle auf Warnungen ein, während die App eine Protokollierung auf Debugebene durchführt, wie im Abschnitt [Protokollfilterung](#log-filtering) erläutert.</span><span class="sxs-lookup"><span data-stu-id="54b4c-419">The settings shown limit framework logs to warnings while allowing the app to log at debug level, as explained in the [Log filtering](#log-filtering) section.</span></span> <span data-ttu-id="54b4c-420">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="54b4c-420">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

::: moniker-end

<span data-ttu-id="54b4c-421">Um die Ausgabe der Konsolenprotokollierung anzuzeigen, öffnen Sie eine Eingabeaufforderung im Projektordner und führen den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="54b4c-421">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```console
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="54b4c-422">Der Debuganbieter</span><span class="sxs-lookup"><span data-stu-id="54b4c-422">Debug provider</span></span>

<span data-ttu-id="54b4c-423">Beim Anbieterpaket [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) erfolgt die Protokollausgabe unter Verwendung der Klasse [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine`-Methodenaufrufe).</span><span class="sxs-lookup"><span data-stu-id="54b4c-423">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="54b4c-424">Unter Linux werden Protokolle dieses Anbieters in */var/log/message* geschrieben.</span><span class="sxs-lookup"><span data-stu-id="54b4c-424">On Linux, this provider writes logs to */var/log/message*.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddDebug();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddDebug();
```

<span data-ttu-id="54b4c-425">Mithilfe von [AddDebug-Überladungen](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) können Sie einen Mindestprotokolliergrad oder eine Filterfunktion übergeben.</span><span class="sxs-lookup"><span data-stu-id="54b4c-425">[AddDebug overloads](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) let you pass in a minimum log level or a filter function.</span></span>

::: moniker-end

### <a name="eventsource-provider"></a><span data-ttu-id="54b4c-426">Der EventSource-Anbieter</span><span class="sxs-lookup"><span data-stu-id="54b4c-426">EventSource provider</span></span>

<span data-ttu-id="54b4c-427">Für Apps, die für ASP.NET Core 1.1.0 oder höher konzipiert sind, kann mit dem Anbieterpaket [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) eine Ereignisablaufverfolgung implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-427">For apps that target ASP.NET Core 1.1.0 or later, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="54b4c-428">Verwenden Sie unter Windows [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="54b4c-428">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="54b4c-429">Der Anbieter ist plattformunabhängig, aber für Linux oder macOS sind Ereignissammlung und Anzeigetools noch nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="54b4c-429">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventSourceLogger();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventSourceLogger();
```

::: moniker-end

<span data-ttu-id="54b4c-430">Eine gute Möglichkeit zum Erfassen und Anzeigen von Protokollen ist die Verwendung des Hilfsprogramms [PerfView](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="54b4c-430">A good way to collect and view logs is to use the [PerfView utility](https://github.com/Microsoft/perfview).</span></span> <span data-ttu-id="54b4c-431">Es gibt andere Tools zur Anzeige von ETW-Protokollen, aber PerfView bietet die besten Ergebnisse bei der Arbeit mit ETW-Ereignissen, die von ASP.NET ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-431">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET.</span></span>

<span data-ttu-id="54b4c-432">Um PerfView für das Erfassen von Ereignissen zu konfigurieren, die von diesem Anbieter protokolliert wurden, fügen Sie die Zeichenfolge `*Microsoft-Extensions-Logging` zur Liste **Zusätzliche Anbieter** hinzu.</span><span class="sxs-lookup"><span data-stu-id="54b4c-432">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="54b4c-433">(Vergessen Sie nicht das Sternchen am Anfang der Zeichenfolge.)</span><span class="sxs-lookup"><span data-stu-id="54b4c-433">(Don't miss the asterisk at the start of the string.)</span></span>

![Zusätzliche PerfView-Anbieter](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="54b4c-435">Der Windows-EventLog-Anbieter</span><span class="sxs-lookup"><span data-stu-id="54b4c-435">Windows EventLog provider</span></span>

<span data-ttu-id="54b4c-436">Das Anbieterpaket [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sendet eine Protokollausgabe in das Windows-Ereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="54b4c-436">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventLog();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventLog();
```

<span data-ttu-id="54b4c-437">Mithilfe von [AddEventLog-Überladungen](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) können Sie `EventLogSettings` oder einen Mindestprotokolliergrad übergeben.</span><span class="sxs-lookup"><span data-stu-id="54b4c-437">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in `EventLogSettings` or a minimum log level.</span></span>

::: moniker-end

### <a name="tracesource-provider"></a><span data-ttu-id="54b4c-438">Der TraceSource-Anbieter</span><span class="sxs-lookup"><span data-stu-id="54b4c-438">TraceSource provider</span></span>

<span data-ttu-id="54b4c-439">Das Anbieterpaket [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) verwendet die <xref:System.Diagnostics.TraceSource>-Bibliotheken und -Anbieter.</span><span class="sxs-lookup"><span data-stu-id="54b4c-439">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddTraceSource(sourceSwitchName);
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddTraceSource(sourceSwitchName);
```

::: moniker-end

<span data-ttu-id="54b4c-440">Mithilfe von [AddTraceSource-Überladungen](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) können Sie eine Quelloption und einen Listener für die Ablaufverfolgung übergeben.</span><span class="sxs-lookup"><span data-stu-id="54b4c-440">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="54b4c-441">Um diesen Anbieter zu verwenden, muss eine App unter .NET Framework (anstelle von .NET Core) ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="54b4c-441">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="54b4c-442">Der Anbieter kann Nachrichten an eine Vielzahl von [Listenern](/dotnet/framework/debug-trace-profile/trace-listeners) weiterleiten, z.B. an den in der Beispiel-App verwendeten <xref:System.Diagnostics.TextWriterTraceListener>.</span><span class="sxs-lookup"><span data-stu-id="54b4c-442">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="54b4c-443">Im folgenden Beispiel wird ein `TraceSource`-Anbieter konfiguriert, der Protokollmeldungen der Ebene `Warning` und höher an das Konsolenfenster ausgibt.</span><span class="sxs-lookup"><span data-stu-id="54b4c-443">The following example configures a `TraceSource` provider that logs `Warning` and higher messages to the console window.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

::: moniker-end

### <a name="azure-app-service-provider"></a><span data-ttu-id="54b4c-444">Der Azure App Service-Anbieter</span><span class="sxs-lookup"><span data-stu-id="54b4c-444">Azure App Service provider</span></span>

<span data-ttu-id="54b4c-445">Das Anbieterpaket [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) schreibt Protokolle in Textdateien in das Dateisystem einer Azure App Service-App und in [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in einem Azure Storage-Konto.</span><span class="sxs-lookup"><span data-stu-id="54b4c-445">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span> <span data-ttu-id="54b4c-446">Das Anbieterpaket ist für Apps verfügbar, die für .NET Core 1.1 oder höher konzipiert sind.</span><span class="sxs-lookup"><span data-stu-id="54b4c-446">The provider package is available for apps targeting .NET Core 1.1 or later.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="54b4c-447">Wenn Sie Ihre App auf .NET Core ausrichten, beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="54b4c-447">If targeting .NET Core, note the following points:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="54b4c-448">Das Anbieterpaket ist im ASP.NET Core-Metapaket [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) enthalten.</span><span class="sxs-lookup"><span data-stu-id="54b4c-448">The provider package is included in the ASP.NET Core [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="54b4c-449">Dieses Anbieterpaket ist nicht im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="54b4c-449">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="54b4c-450">Um den Anbieter zu verwenden, installieren Sie das Paket.</span><span class="sxs-lookup"><span data-stu-id="54b4c-450">To use the provider, install the package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="54b4c-451">Wenn Sie Anwendungen für .NET Framework entwickeln oder auf das `Microsoft.AspNetCore.App`-Metapaket verweisen, fügen Sie dem Projekt das Anbieterpaket hinzu.</span><span class="sxs-lookup"><span data-stu-id="54b4c-451">If targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> <span data-ttu-id="54b4c-452">Rufen Sie `AddAzureWebAppDiagnostics` auf:</span><span class="sxs-lookup"><span data-stu-id="54b4c-452">Invoke `AddAzureWebAppDiagnostics`:</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="= aspnetcore-1.1"

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="54b4c-453">Eine <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>-Überladung ermöglicht das Übergeben von <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="54b4c-453">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="54b4c-454">Das settings-Objekt kann Standardeinstellungen überschreiben, z.B. die Protokollierungsausgabevorlage, den Blobnamen und die maximale Dateigröße.</span><span class="sxs-lookup"><span data-stu-id="54b4c-454">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="54b4c-455">(Die *Ausgabevorlage* ist eine Nachrichtenvorlage, die auf alle Protokolle zusätzlich zu dem angewendet wird, was mit einem `ILogger`-Methodenaufruf bereitgestellt wird.)</span><span class="sxs-lookup"><span data-stu-id="54b4c-455">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="54b4c-456">Verwenden Sie <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> und <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions> wie im folgenden Beispiel, um die Anbietereinstellungen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="54b4c-456">To configure provider settings, use <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> and <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>, as shown in the following example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

<span data-ttu-id="54b4c-457">Wenn Sie eine Bereitstellung in einer App Service-App durchführen, berücksichtigt die Anwendung die Einstellungen im Abschnitt [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) (App Service-Protokolle) auf der Seite **App Services** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="54b4c-457">When you deploy to an App Service app, the application honors the settings in the [App Service logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="54b4c-458">Bei einem Update der folgenden Einstellungen werden die Änderungen sofort wirksam, ohne dass ein Neustart oder eine erneute Bereitstellung der App notwendig ist.</span><span class="sxs-lookup"><span data-stu-id="54b4c-458">When the following settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

* <span data-ttu-id="54b4c-459">**Anwendungsprotokoll (Dateisystem)**</span><span class="sxs-lookup"><span data-stu-id="54b4c-459">**Application Logging (Filesystem)**</span></span>
* <span data-ttu-id="54b4c-460">**Anwendungsprotokoll (Blob)**</span><span class="sxs-lookup"><span data-stu-id="54b4c-460">**Application Logging (Blob)**</span></span>

<span data-ttu-id="54b4c-461">Der Standardspeicherort für Protokolldateien ist der Ordner *D:\\home\\LogFiles\\Application*, und der standardmäßige Dateiname lautet *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="54b4c-461">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="54b4c-462">Die Dateigröße ist standardmäßig auf 10 MB beschränkt, und die maximal zulässige Anzahl beibehaltener Dateien lautet 2.</span><span class="sxs-lookup"><span data-stu-id="54b4c-462">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="54b4c-463">Der Standardblobname lautet *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="54b4c-463">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span>

<span data-ttu-id="54b4c-464">Der Anbieter funktioniert nur, wenn das Projekt in der Azure-Umgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="54b4c-464">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="54b4c-465">Bei einer lokalen Ausführung zeigt er keine Auswirkungen. Der Anbieter schreibt keine Protokolle in lokale Dateien oder den lokalen Entwicklungsspeicher für BLOBs.</span><span class="sxs-lookup"><span data-stu-id="54b4c-465">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

#### <a name="azure-log-streaming"></a><span data-ttu-id="54b4c-466">Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="54b4c-466">Azure log streaming</span></span>

<span data-ttu-id="54b4c-467">Azure-Protokollstreaming ermöglicht Ihnen eine Echtzeitanzeige der Protokollaktivität für:</span><span class="sxs-lookup"><span data-stu-id="54b4c-467">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="54b4c-468">App-Server</span><span class="sxs-lookup"><span data-stu-id="54b4c-468">The app server</span></span>
* <span data-ttu-id="54b4c-469">Webserver</span><span class="sxs-lookup"><span data-stu-id="54b4c-469">The web server</span></span>
* <span data-ttu-id="54b4c-470">Ablaufverfolgung für Anforderungsfehler</span><span class="sxs-lookup"><span data-stu-id="54b4c-470">Failed request tracing</span></span>

<span data-ttu-id="54b4c-471">So konfigurieren Sie das Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="54b4c-471">To configure Azure log streaming:</span></span>

* <span data-ttu-id="54b4c-472">Navigieren Sie von der Portalseite Ihrer App zur Seite **App Service-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="54b4c-472">Navigate to the **App Service logs** page from your app's portal page.</span></span>
* <span data-ttu-id="54b4c-473">Legen Sie **Anwendungsprotokollierung (Dateisystem)** auf **Ein** fest.</span><span class="sxs-lookup"><span data-stu-id="54b4c-473">Set **Application Logging (Filesystem)** to **On**.</span></span>
* <span data-ttu-id="54b4c-474">Wählen Sie die **Protokollierungsebene**.</span><span class="sxs-lookup"><span data-stu-id="54b4c-474">Choose the log **Level**.</span></span>

<span data-ttu-id="54b4c-475">Navigieren Sie zur Seite **Log Stream** (Protokollstream), um App-Meldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-475">Navigate to the **Log Stream** page to view app messages.</span></span> <span data-ttu-id="54b4c-476">Diese werden von der App über die `ILogger`-Schnittstelle protokolliert.</span><span class="sxs-lookup"><span data-stu-id="54b4c-476">They're logged by the app through the `ILogger` interface.</span></span>

::: moniker range=">= aspnetcore-1.1"

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="54b4c-477">Ablaufverfolgungsprotokollierung für Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="54b4c-477">Azure Application Insights trace logging</span></span>

<span data-ttu-id="54b4c-478">Das [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)-Anbieterpaket schreibt Protokolle in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="54b4c-478">The [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) provider package writes logs to Azure Application Insights.</span></span> <span data-ttu-id="54b4c-479">Application Insights ist ein Dienst, der eine Web-App überwacht und Tools für Abfragen und Analysen von Telemetriedaten zur Verfügung stellt.</span><span class="sxs-lookup"><span data-stu-id="54b4c-479">Application Insights is a service that monitors a web app and provides tools for querying and analyzing the telemetry data.</span></span> <span data-ttu-id="54b4c-480">Wenn Sie diesen Anbieter verwenden, können Sie Ihre Protokolle mithilfe der Application Insights-Tools abfragen und analysieren.</span><span class="sxs-lookup"><span data-stu-id="54b4c-480">If you use this provider, you can query and analyze your logs by using the Application Insights tools.</span></span>

<span data-ttu-id="54b4c-481">Der Protokollierungsanbieter ist als Abhängigkeit von [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) enthalten, d.h. als das Paket, das alle verfügbaren Telemetriedaten für ASP.NET Core bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="54b4c-481">The logging provider is included as a dependency of [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore), which is the package that provides all available telemetry for ASP.NET Core.</span></span> <span data-ttu-id="54b4c-482">Wenn Sie dieses Paket verwenden, ist nicht erforderlich, das Anbieterpaket zu installieren.</span><span class="sxs-lookup"><span data-stu-id="54b4c-482">If you use this package, you don't have to install the provider package.</span></span>

<span data-ttu-id="54b4c-483">Verwenden Sie nicht das [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)-Paket, das für ASP.NET 4.x bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="54b4c-483">Don't use the [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) package&mdash;that's for ASP.NET 4.x.</span></span>

<span data-ttu-id="54b4c-484">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="54b4c-484">For more information, see the following resources:</span></span>

* [<span data-ttu-id="54b4c-485">Application Insights-Übersicht</span><span class="sxs-lookup"><span data-stu-id="54b4c-485">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* <span data-ttu-id="54b4c-486">[Anwendungseinblicke für ASP.NET Core-Anwendungen](/azure/azure-monitor/app/asp-net-core-no-visualstudio): Beginnen Sie hier, wenn Sie die gesamte Bandbreite der Application Insights-Telemetrie zusammen mit der Protokollierung implementieren möchten.</span><span class="sxs-lookup"><span data-stu-id="54b4c-486">[Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core-no-visualstudio) - Start here if you want to implement the full range of Application Insights telemetry along with logging.</span></span>
* <span data-ttu-id="54b4c-487">[ApplicationInsightsLoggerProvider für .NET Core ILogger-Protokolle](/azure/azure-monitor/app/ilogger): Beginnen Sie hier, wenn Sie den Protokollierungsanbieter ohne die übrige Application Insights-Telemetrie implementieren möchten.</span><span class="sxs-lookup"><span data-stu-id="54b4c-487">[ApplicationInsightsLoggerProvider for .NET Core ILogger logs](/azure/azure-monitor/app/ilogger) - Start here if you want to implement the logging provider without the rest of Application Insights telemetry.</span></span>
* <span data-ttu-id="54b4c-488">[Application Insights logging adapters (Adapter zur Protokollierung für Application Insights)](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md).</span><span class="sxs-lookup"><span data-stu-id="54b4c-488">[Application Insights logging adapters](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md).</span></span>
* <span data-ttu-id="54b4c-489">[Installieren, Konfigurieren und Initialisieren des Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights): interaktive Tutorials auf der Microsoft Learn-Website.</span><span class="sxs-lookup"><span data-stu-id="54b4c-489">[Install, configure, and initialize the Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Interactive tutorial on the Microsoft Learn site.</span></span>
::: moniker-end

> [!NOTE]
> <span data-ttu-id="54b4c-490">Seit dem 1.5.2019 ist der Artikel mit dem Titel [Application Insights für ASP.NET Core](/azure/azure-monitor/app/asp-net-core) veraltet, und die Schritte im Tutorial funktionieren nicht mehr.</span><span class="sxs-lookup"><span data-stu-id="54b4c-490">As of 5/1/2019, the article titled [Application Insights for ASP.NET Core](/azure/azure-monitor/app/asp-net-core) is out of date, and the tutorial steps don't work.</span></span> <span data-ttu-id="54b4c-491">Ziehen Sie stattdessen [Application Insights für ASP.NET Core-Anwendungen](/azure/azure-monitor/app/asp-net-core-no-visualstudio) zurate.</span><span class="sxs-lookup"><span data-stu-id="54b4c-491">Refer to [Application Insights for ASP.NET Core applications](/azure/azure-monitor/app/asp-net-core-no-visualstudio) instead.</span></span> <span data-ttu-id="54b4c-492">Wir kennen das Problem und arbeiten an einer Lösung.</span><span class="sxs-lookup"><span data-stu-id="54b4c-492">We are aware of the issue and are working to correct it.</span></span>

## <a name="third-party-logging-providers"></a><span data-ttu-id="54b4c-493">Protokollierungsanbieter von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="54b4c-493">Third-party logging providers</span></span>

<span data-ttu-id="54b4c-494">Protokollierungsframeworks von Drittanbietern aufgeführt, die mit ASP.NET Core funktionieren:</span><span class="sxs-lookup"><span data-stu-id="54b4c-494">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="54b4c-495">[elmah.io](https://elmah.io/) ([GitHub-Repository](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="54b4c-495">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="54b4c-496">[Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub-Repository](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="54b4c-496">[Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="54b4c-497">[JSNLog](http://jsnlog.com/) ([GitHub-Repository](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="54b4c-497">[JSNLog](http://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="54b4c-498">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="54b4c-498">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="54b4c-499">[Loggr](http://loggr.net/) ([GitHub-Repository](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="54b4c-499">[Loggr](http://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="54b4c-500">[NLog](http://nlog-project.org/) ([GitHub-Repository](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="54b4c-500">[NLog](http://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="54b4c-501">[Sentry](https://sentry.io/welcome/) ([GitHub-Repository](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="54b4c-501">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="54b4c-502">[Serilog](https://serilog.net/) ([GitHub-Repository](https://github.com/serilog/serilog-aspnetcore))</span><span class="sxs-lookup"><span data-stu-id="54b4c-502">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-aspnetcore))</span></span>
* <span data-ttu-id="54b4c-503">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub-Repository](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="54b4c-503">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="54b4c-504">Einige Drittanbieterframeworks können eine [semantische Protokollierung (auch als strukturierte Protokollierung bezeichnet)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) ausführen.</span><span class="sxs-lookup"><span data-stu-id="54b4c-504">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="54b4c-505">Die Verwendung eines Frameworks von Drittanbietern ist ähnlich wie die Verwendung eines integrierten Anbieters:</span><span class="sxs-lookup"><span data-stu-id="54b4c-505">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="54b4c-506">Fügen Sie Ihrem Paket ein NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="54b4c-506">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="54b4c-507">Rufen Sie eine `ILoggerFactory` auf.</span><span class="sxs-lookup"><span data-stu-id="54b4c-507">Call an `ILoggerFactory`.</span></span>

<span data-ttu-id="54b4c-508">Weitere Informationen finden Sie in der Dokumentation zum jeweiligen Anbieter.</span><span class="sxs-lookup"><span data-stu-id="54b4c-508">For more information, see each provider's documentation.</span></span> <span data-ttu-id="54b4c-509">Protokollierungsanbieter von Drittanbietern werden von Microsoft nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="54b4c-509">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54b4c-510">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="54b4c-510">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
