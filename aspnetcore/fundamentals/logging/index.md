---
title: Protokollierung in ASP.NET Core
author: tdykstra
description: Erfahren Sie mehr über das Protokollierungsframework in ASP.NET Core. Lernen Sie die integrierten Anbieter für die Protokollierung kennen, und erfahren Sie mehr über beliebte Anbieter von Drittanbietern.
ms.author: tdykstra
ms.custom: mvc
ms.date: 01/14/2019
uid: fundamentals/logging/index
ms.openlocfilehash: 81620f0c844f3dbb1a2da0e9f1c319f87d9790b6
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667699"
---
# <a name="logging-in-aspnet-core"></a><span data-ttu-id="84930-104">Protokollierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84930-104">Logging in ASP.NET Core</span></span>

<span data-ttu-id="84930-105">Von [Steve Smith](https://ardalis.com/) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="84930-105">By [Steve Smith](https://ardalis.com/) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="84930-106">ASP.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="84930-106">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="84930-107">Dieser Artikel zeigt, wie Sie die Protokollierungs-API mit integrierten Anbietern verwenden können.</span><span class="sxs-lookup"><span data-stu-id="84930-107">This article shows how to use the logging API with built-in providers.</span></span>

<span data-ttu-id="84930-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="84930-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="add-providers"></a><span data-ttu-id="84930-109">Hinzufügen von Anbietern</span><span class="sxs-lookup"><span data-stu-id="84930-109">Add providers</span></span>

<span data-ttu-id="84930-110">Ein Protokollierungsanbieter zeigt Protokolle an oder speichert diese.</span><span class="sxs-lookup"><span data-stu-id="84930-110">A logging provider displays or stores logs.</span></span> <span data-ttu-id="84930-111">Beispielsweise zeigt der Konsolenanbieter Protokolle in der Konsole an, und der Azure Application Insights-Anbieter speichert sie in Azure Application Insights.</span><span class="sxs-lookup"><span data-stu-id="84930-111">For example, the Console provider displays logs on the console, and the Azure Application Insights provider stores them in Azure Application Insights.</span></span> <span data-ttu-id="84930-112">Protokolle können durch das Hinzufügen mehrerer Anbieter an mehrere Ziele gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="84930-112">Logs can be sent to multiple destinations by adding multiple providers.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="84930-113">Um einen Anbieter hinzuzufügen, rufen Sie die `Add{provider name}`-Erweiterungsmethode des Anbieters in *Program.cs* auf:</span><span class="sxs-lookup"><span data-stu-id="84930-113">To add a provider, call the provider's `Add{provider name}` extension method in *Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=17-19)]

<span data-ttu-id="84930-114">Die Standardprojektvorlage ruft die <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>-Erweiterungsmethode auf, die die folgenden Protokollierungsanbieter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="84930-114">The default project template calls the <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> extension method, which adds the following logging providers:</span></span>

* <span data-ttu-id="84930-115">Konsole</span><span class="sxs-lookup"><span data-stu-id="84930-115">Console</span></span>
* <span data-ttu-id="84930-116">Debug</span><span class="sxs-lookup"><span data-stu-id="84930-116">Debug</span></span>
* <span data-ttu-id="84930-117">EventSource (beginnend mit ASP.NET Core 2.2)</span><span class="sxs-lookup"><span data-stu-id="84930-117">EventSource (starting in ASP.NET Core 2.2)</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

<span data-ttu-id="84930-118">Bei Verwendung von `CreateDefaultBuilder` können Sie die Standardanbieter durch Ihre eigene Auswahl ersetzen.</span><span class="sxs-lookup"><span data-stu-id="84930-118">If you use `CreateDefaultBuilder`, you can replace the default providers with your own choices.</span></span> <span data-ttu-id="84930-119">Rufen Sie <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> auf, und fügen Sie die gewünschten Anbieter hinzu.</span><span class="sxs-lookup"><span data-stu-id="84930-119">Call <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>, and add the providers you want.</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="84930-120">Um einen Anbieter zu verwenden, installieren Sie das zugehörige NuGet-Paket und rufen die Erweiterungsmethode des Anbieters für eine Instanz von <xref:Microsoft.Extensions.Logging.ILoggerFactory> auf:</span><span class="sxs-lookup"><span data-stu-id="84930-120">To use a provider, install its NuGet package and call the provider's extension method on an instance of <xref:Microsoft.Extensions.Logging.ILoggerFactory>:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample//Startup.cs?name=snippet_AddConsoleAndDebug&highlight=3,5-7)]

<span data-ttu-id="84930-121">ASP.NET Core-[Abhängigkeitsinjektion (Dependency Injection, DI)](xref:fundamentals/dependency-injection) stellt die `ILoggerFactory`-Instanz bereit.</span><span class="sxs-lookup"><span data-stu-id="84930-121">ASP.NET Core [dependency injection (DI)](xref:fundamentals/dependency-injection) provides the `ILoggerFactory` instance.</span></span> <span data-ttu-id="84930-122">Die Erweiterungsmethoden `AddConsole` und `AddDebug` sind in den Paketen [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) und [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) definiert.</span><span class="sxs-lookup"><span data-stu-id="84930-122">The `AddConsole` and `AddDebug` extension methods are defined in the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/) and [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug/) packages.</span></span> <span data-ttu-id="84930-123">Jede Erweiterungsmethode ruft die Methode `ILoggerFactory.AddProvider` auf und übergibt eine Instanz des Anbieters.</span><span class="sxs-lookup"><span data-stu-id="84930-123">Each extension method calls the `ILoggerFactory.AddProvider` method, passing in an instance of the provider.</span></span>

> [!NOTE]
> <span data-ttu-id="84930-124">Die [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) fügt Protokollanbieter in der `Startup.Configure`-Methode hinzu.</span><span class="sxs-lookup"><span data-stu-id="84930-124">The [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/1.x) adds logging providers in the `Startup.Configure` method.</span></span> <span data-ttu-id="84930-125">Wenn Sie für zuvor ausgeführten Code eine Protokollausgabe abrufen möchten, fügen Sie Protokollierungsanbieter im `Startup`-Klassenkonstruktor hinzu.</span><span class="sxs-lookup"><span data-stu-id="84930-125">To obtain log output from code that executes earlier, add logging providers in the `Startup` class constructor.</span></span>

::: moniker-end

<span data-ttu-id="84930-126">Informationen zu den [integrierten Protokollierungsanbietern](#built-in-logging-providers) sowie zu [Protokollierungsanbietern von Drittanbietern](#third-party-logging-providers) finden Sie weiter unten in diesem Artikel.</span><span class="sxs-lookup"><span data-stu-id="84930-126">Learn more about [built-in logging providers](#built-in-logging-providers) and [third-party logging providers](#third-party-logging-providers) later in the article.</span></span>

## <a name="create-logs"></a><span data-ttu-id="84930-127">Erstellen von Protokollen</span><span class="sxs-lookup"><span data-stu-id="84930-127">Create logs</span></span>

<span data-ttu-id="84930-128">Rufen Sie ein <xref:Microsoft.Extensions.Logging.ILogger`1>-Objekt aus DI ab.</span><span class="sxs-lookup"><span data-stu-id="84930-128">Get an <xref:Microsoft.Extensions.Logging.ILogger`1> object from DI.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="84930-129">Das folgende Controllerbeispiel erstellt `Information`- und `Warning`-Protokolle.</span><span class="sxs-lookup"><span data-stu-id="84930-129">The following controller example creates `Information` and `Warning` logs.</span></span> <span data-ttu-id="84930-130">Die *Kategorie* ist `TodoApiSample.Controllers.TodoController` (der vollqualifizierte Klassenname von `TodoController` in der Beispiel-App):</span><span class="sxs-lookup"><span data-stu-id="84930-130">The *category* is `TodoApiSample.Controllers.TodoController` (the fully qualified class name of `TodoController` in the sample app):</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=4,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="84930-131">Das folgende Razor Pages-Beispiel erstellt Protokolle mit `Information` als *Grad* und `TodoApiSample.Pages.AboutModel` als *Kategorie*:</span><span class="sxs-lookup"><span data-stu-id="84930-131">The following Razor Pages example creates logs with `Information` as the *level* and `TodoApiSample.Pages.AboutModel` as the *category*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3, 7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

<span data-ttu-id="84930-132">Das vorherige Beispiel erstellt Protokolle mit `Information` und `Warning` als *Grad* und der `TodoController`-Klasse als *Kategorie*.</span><span class="sxs-lookup"><span data-stu-id="84930-132">The preceding example creates logs with `Information` and `Warning` as the *level* and `TodoController` class as the *category*.</span></span> 

::: moniker-end

<span data-ttu-id="84930-133">Der Protokollierungs*grad* gibt den Schweregrad des protokollierten Ereignisses an.</span><span class="sxs-lookup"><span data-stu-id="84930-133">The Log *level* indicates the severity of the logged event.</span></span> <span data-ttu-id="84930-134">Die Protokoll*kategorie* ist eine Zeichenfolge, die jedem Protokoll zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="84930-134">The log *category* is a string that is associated with each log.</span></span> <span data-ttu-id="84930-135">Die `ILogger<T>`-Instanz erstellt Protokolle, die den vollqualifizierten Namen vom Typ `T` als Kategorie aufweisen.</span><span class="sxs-lookup"><span data-stu-id="84930-135">The `ILogger<T>` instance creates logs that have the fully qualified name of type `T` as the category.</span></span> <span data-ttu-id="84930-136">[Grade](#log-level) und [Kategorien](#log-category) werden weiter unten in diesem Artikel ausführlicher erläutert.</span><span class="sxs-lookup"><span data-stu-id="84930-136">[Levels](#log-level) and [categories](#log-category) are explained in more detail later in this article.</span></span> 

::: moniker range=">= aspnetcore-2.0"

### <a name="create-logs-in-startup"></a><span data-ttu-id="84930-137">Erstellen von Protokollen in der Startklasse</span><span class="sxs-lookup"><span data-stu-id="84930-137">Create logs in Startup</span></span>

<span data-ttu-id="84930-138">Zum Schreiben von Protokollen in der `Startup`-Klasse schließen Sie einen `ILogger`-Parameter in die Konstruktorsignatur ein:</span><span class="sxs-lookup"><span data-stu-id="84930-138">To write logs in the `Startup` class, include an `ILogger` parameter in the constructor signature:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,19,26)]

### <a name="create-logs-in-program"></a><span data-ttu-id="84930-139">Erstellen von Protokollen in der Programmklasse</span><span class="sxs-lookup"><span data-stu-id="84930-139">Create logs in Program</span></span>

<span data-ttu-id="84930-140">Zum Schreiben von Protokollen in der `Program`-Klasse rufen Sie eine `ILogger`-Instanz aus DI ab:</span><span class="sxs-lookup"><span data-stu-id="84930-140">To write logs in the `Program` class, get an `ILogger` instance from DI:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a><span data-ttu-id="84930-141">Keine asynchronen Protokollierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="84930-141">No asynchronous logger methods</span></span>

<span data-ttu-id="84930-142">Die Protokollierung sollte so schnell erfolgen, dass sich die Leistungskosten von asynchronem Code nicht lohnen.</span><span class="sxs-lookup"><span data-stu-id="84930-142">Logging should be so fast that it isn't worth the performance cost of asynchronous code.</span></span> <span data-ttu-id="84930-143">Wenn Ihr Protokollierungsdatenspeicher langsam ist, schreiben Sie nicht direkt in ihn.</span><span class="sxs-lookup"><span data-stu-id="84930-143">If your logging data store is slow, don't write to it directly.</span></span> <span data-ttu-id="84930-144">Erwägen Sie, die Protokollnachrichten zunächst in einen schnellen Speicher zu schreiben und sie dann später in den langsamen Speicher zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="84930-144">Consider writing the log messages to a fast store initially, then move them to the slow store later.</span></span> <span data-ttu-id="84930-145">Führen Sie die Protokollierung beispielsweise in einer Nachrichtenwarteschlange durch, die von einem anderen Prozess gelesen und in einem langsamen Speicher persistiert wird.</span><span class="sxs-lookup"><span data-stu-id="84930-145">For example, log to a message queue that's read and persisted to slow storage by another process.</span></span>

## <a name="configuration"></a><span data-ttu-id="84930-146">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="84930-146">Configuration</span></span>

<span data-ttu-id="84930-147">Die Konfiguration von Protokollierungsanbietern erfolgt durch mindestens einen Konfigurationsanbieter:</span><span class="sxs-lookup"><span data-stu-id="84930-147">Logging provider configuration is provided by one or more configuration providers:</span></span>

* <span data-ttu-id="84930-148">Dateiformate (INI, JSON und XML)</span><span class="sxs-lookup"><span data-stu-id="84930-148">File formats (INI, JSON, and XML).</span></span>
* <span data-ttu-id="84930-149">Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="84930-149">Command-line arguments.</span></span>
* <span data-ttu-id="84930-150">Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="84930-150">Environment variables.</span></span>
* <span data-ttu-id="84930-151">Speicherinterne .NET-Objekte</span><span class="sxs-lookup"><span data-stu-id="84930-151">In-memory .NET objects.</span></span>
* <span data-ttu-id="84930-152">Der unverschlüsselte Speicher von [Secret Manager](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="84930-152">The unencrypted [Secret Manager](xref:security/app-secrets) storage.</span></span>
* <span data-ttu-id="84930-153">Ein unverschlüsselter Benutzerspeicher, z.B. [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="84930-153">An encrypted user store, such as [Azure Key Vault](xref:security/key-vault-configuration).</span></span>
* <span data-ttu-id="84930-154">Benutzerdefinierte Anbieter (installiert oder erstellt).</span><span class="sxs-lookup"><span data-stu-id="84930-154">Custom providers (installed or created).</span></span>

<span data-ttu-id="84930-155">Die Konfiguration der Protokollierung wird meist vom `Logging`-Abschnitt von Anwendungseinstellungsdateien angegeben.</span><span class="sxs-lookup"><span data-stu-id="84930-155">For example, logging configuration is commonly provided by the `Logging` section of app settings files.</span></span> <span data-ttu-id="84930-156">Im folgenden Beispiel werden die Inhalte einer herkömmlichen *appsettings.Development.json*-Datei veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="84930-156">The following example shows the contents of a typical *appsettings.Development.json* file:</span></span>

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

<span data-ttu-id="84930-157">Die `Logging`-Eigenschaft kann den `LogLevel` und Protokollanbietereigenschaften beinhalten (Konsole wird angezeigt).</span><span class="sxs-lookup"><span data-stu-id="84930-157">The `Logging` property can have `LogLevel` and log provider properties (Console is shown).</span></span>

<span data-ttu-id="84930-158">Die `LogLevel`-Eigenschaft unter `Logging` gibt den Mindest[grad](#log-level) an, der für ausgewählte Kategorien protokolliert werden soll.</span><span class="sxs-lookup"><span data-stu-id="84930-158">The `LogLevel` property under `Logging` specifies the minimum [level](#log-level) to log for selected categories.</span></span> <span data-ttu-id="84930-159">Im Beispiel protokollieren die Kategorien `System` und `Microsoft` mit dem Grad `Information` und alle anderen Kategorien mit dem Grad `Debug`.</span><span class="sxs-lookup"><span data-stu-id="84930-159">In the example, `System` and `Microsoft` categories log at `Information` level, and all others log at `Debug` level.</span></span>

<span data-ttu-id="84930-160">Weitere Eigenschaften unter `Logging` geben Protokollierungsanbieter an.</span><span class="sxs-lookup"><span data-stu-id="84930-160">Other properties under `Logging` specify logging providers.</span></span> <span data-ttu-id="84930-161">Das Beispiel bezieht sich auf den Konsolenanbieter.</span><span class="sxs-lookup"><span data-stu-id="84930-161">The example is for the Console provider.</span></span> <span data-ttu-id="84930-162">Wenn ein Anbieter [Protokollbereiche](#log-scopes) unterstützt, gibt `IncludeScopes` an, ob sie aktiviert sind.</span><span class="sxs-lookup"><span data-stu-id="84930-162">If a provider supports [log scopes](#log-scopes), `IncludeScopes` indicates whether they're enabled.</span></span> <span data-ttu-id="84930-163">Eine Anbietereigenschaft (z.B. `Console` im Beispiel) kann auch eine `LogLevel`-Eigenschaft angeben.</span><span class="sxs-lookup"><span data-stu-id="84930-163">A provider property (such as `Console` in the example) may also specify a `LogLevel` property.</span></span> <span data-ttu-id="84930-164">`LogLevel` unter einem Anbieter gibt die Grade an, die für diesen Anbieter protokolliert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="84930-164">`LogLevel` under a provider specifies levels to log for that provider.</span></span>

<span data-ttu-id="84930-165">Wenn Grade in `Logging.{providername}.LogLevel` angegeben werden, überschreiben sie alle Angaben in `Logging.LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="84930-165">If levels are specified in `Logging.{providername}.LogLevel`, they override anything set in `Logging.LogLevel`.</span></span>

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

<span data-ttu-id="84930-166">`LogLevel`-Schlüssel stellen Protokollnamen dar.</span><span class="sxs-lookup"><span data-stu-id="84930-166">`LogLevel` keys represent log names.</span></span> <span data-ttu-id="84930-167">Der `Default`-Schlüssel gilt für Protokolle, die nicht explizit aufgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="84930-167">The `Default` key applies to logs not explicitly listed.</span></span> <span data-ttu-id="84930-168">Der Wert entspricht dem auf das jeweilige Protokoll angewendeten [Protokolliergrad](#log-level).</span><span class="sxs-lookup"><span data-stu-id="84930-168">The value represents the [log level](#log-level) applied to the given log.</span></span>

::: moniker-end

<span data-ttu-id="84930-169">Informationen zur Implementierung von Konfigurationsanbieter finden Sie hier: <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="84930-169">For information on implementing configuration providers, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="sample-logging-output"></a><span data-ttu-id="84930-170">Beispiel einer Protokollierungsausgabe</span><span class="sxs-lookup"><span data-stu-id="84930-170">Sample logging output</span></span>

<span data-ttu-id="84930-171">Mit dem im vorherigen Abschnitt gezeigten Beispielcode werden Protokolle in der Konsole angezeigt, wenn die Anwendung über die Befehlszeile ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="84930-171">With the sample code shown in the preceding section, logs appear in the console when the app is run from the command line.</span></span> <span data-ttu-id="84930-172">Hier ein Beispiel für eine Konsolenausgabe:</span><span class="sxs-lookup"><span data-stu-id="84930-172">Here's an example of console output:</span></span>

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

<span data-ttu-id="84930-173">Die vorherigen Protokolle wurden generiert, indem eine HTTP Get-Anforderung an die Beispiel-App unter `http://localhost:5000/api/todo/0` vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="84930-173">The preceding logs were generated by making an HTTP Get request to the sample app at `http://localhost:5000/api/todo/0`.</span></span>

<span data-ttu-id="84930-174">Nachfolgend sehen Sie, wie die gleichen Protokolle im Debugfenster angezeigt werden, wenn Sie die Beispiel-App in Visual Studio ausführen:</span><span class="sxs-lookup"><span data-stu-id="84930-174">Here's an example of the same logs as they appear in the Debug window when you run the sample app in Visual Studio:</span></span>


```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

<span data-ttu-id="84930-175">Die über die `ILogger`-Aufrufe aus dem vorherigen Abschnitt erstellten Protokolle beginnen mit „TodoApi.Controllers.TodoController“.</span><span class="sxs-lookup"><span data-stu-id="84930-175">The logs that are created by the `ILogger` calls shown in the preceding section begin with "TodoApi.Controllers.TodoController".</span></span> <span data-ttu-id="84930-176">Protokolle, die mit Microsoft-Kategorien beginnen, stammen aus ASP.NET Core-Frameworkcode.</span><span class="sxs-lookup"><span data-stu-id="84930-176">The logs that begin with "Microsoft" categories are from ASP.NET Core framework code.</span></span> <span data-ttu-id="84930-177">ASP.NET Core und Anwendungscode verwenden dieselbe Protokollierungs-API und dieselben Protokollierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="84930-177">ASP.NET Core and application code are using the same logging API and providers.</span></span>

<span data-ttu-id="84930-178">In den übrigen Abschnitten dieses Artikels werden einige Details und Optionen für die Protokollierung erläutert.</span><span class="sxs-lookup"><span data-stu-id="84930-178">The remainder of this article explains some details and options for logging.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="84930-179">NuGet-Pakete</span><span class="sxs-lookup"><span data-stu-id="84930-179">NuGet packages</span></span>

<span data-ttu-id="84930-180">Die Schnittstellen `ILogger` und `ILoggerFactory` befinden sich in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), ihre Standardimplementierungen sind in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) enthalten.</span><span class="sxs-lookup"><span data-stu-id="84930-180">The `ILogger` and `ILoggerFactory` interfaces are in [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/), and default implementations for them are in [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/).</span></span>

## <a name="log-category"></a><span data-ttu-id="84930-181">Protokollkategorie</span><span class="sxs-lookup"><span data-stu-id="84930-181">Log category</span></span>

<span data-ttu-id="84930-182">Wenn ein `ILogger`-Objekt erstellt wird, wird eine *Kategorie* für dieses Objekt angegeben.</span><span class="sxs-lookup"><span data-stu-id="84930-182">When an `ILogger` object is created, a *category* is specified for it.</span></span> <span data-ttu-id="84930-183">Diese Kategorie ist in jeder Protokollmeldung enthalten, die von dieser Instanz von `Ilogger` erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="84930-183">That category is included with each log message created by that instance of `Ilogger`.</span></span> <span data-ttu-id="84930-184">Die Kategorie kann eine beliebige Zeichenfolge sein, aber die Konvention besteht in der Verwendung des Klassennamens, z.B. „TodoApi.Controllers.TodoController“.</span><span class="sxs-lookup"><span data-stu-id="84930-184">The category may be any string, but the convention is to use the class name, such as "TodoApi.Controllers.TodoController".</span></span>

<span data-ttu-id="84930-185">Verwenden Sie `ILogger<T>` zum Abrufen einer `ILogger`-Instanz, die den vollqualifizierten Typnamen von `T` als Kategorie verwendet:</span><span class="sxs-lookup"><span data-stu-id="84930-185">Use `ILogger<T>` to get an `ILogger` instance that uses the fully qualified type name of `T` as the category:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

<span data-ttu-id="84930-186">Um die Kategorie explizit anzugeben, rufen Sie `ILoggerFactory.CreateLogger` auf:</span><span class="sxs-lookup"><span data-stu-id="84930-186">To explicitly specify the category, call `ILoggerFactory.CreateLogger`:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

<span data-ttu-id="84930-187">`ILogger<T>` entspricht dem Aufruf von `CreateLogger` mit dem vollqualifizierten Typnamen `T`.</span><span class="sxs-lookup"><span data-stu-id="84930-187">`ILogger<T>` is equivalent to calling `CreateLogger` with the fully qualified type name of `T`.</span></span>

## <a name="log-level"></a><span data-ttu-id="84930-188">Protokolliergrad</span><span class="sxs-lookup"><span data-stu-id="84930-188">Log level</span></span>

<span data-ttu-id="84930-189">Jedes Protokoll gibt einen <xref:Microsoft.Extensions.Logging.LogLevel>-Wert an.</span><span class="sxs-lookup"><span data-stu-id="84930-189">Every log specifies a <xref:Microsoft.Extensions.Logging.LogLevel> value.</span></span> <span data-ttu-id="84930-190">Der Protokolliergrad gibt den Schweregrad oder die Wichtigkeit an.</span><span class="sxs-lookup"><span data-stu-id="84930-190">The log level indicates the severity or importance.</span></span> <span data-ttu-id="84930-191">Sie können beispielsweise ein `Information`-Protokoll schreiben, wenn eine Methode normal endet, und ein `Warning`-Protokoll, wenn eine Methode den Statuscode *404 Not Found* zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="84930-191">For example, you might write an `Information` log when a method ends normally and a `Warning` log when a method returns a *404 Not Found* status code.</span></span>

<span data-ttu-id="84930-192">Der folgende Code erstellt `Information`- und `Warning`-Protokolle:</span><span class="sxs-lookup"><span data-stu-id="84930-192">The following code creates `Information` and `Warning` logs:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="84930-193">Im Code oben ist der erste Parameter die [Protokollereignis-ID](#log-event-id).</span><span class="sxs-lookup"><span data-stu-id="84930-193">In the preceding code, the first parameter is the [Log event ID](#log-event-id).</span></span> <span data-ttu-id="84930-194">Der zweite Parameter ist eine Meldungsvorlage mit Platzhaltern für Argumentwerte, die von den verbleibenden Methodenparametern bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="84930-194">The second parameter is a message template with placeholders for argument values provided by the remaining method parameters.</span></span> <span data-ttu-id="84930-195">Die Methodenparameter werden im [Abschnitt „Meldungsvorlage“](#log-message-template) später in diesem Artikel erläutert.</span><span class="sxs-lookup"><span data-stu-id="84930-195">The method parameters are explained in the [message template section](#log-message-template) later in this article.</span></span>

<span data-ttu-id="84930-196">Protokollmethoden, die den Protokolliergrad im Methodennamen enthalten (z.B. `LogInformation` und `LogWarning`), sind [Erweiterungsmethoden für ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span><span class="sxs-lookup"><span data-stu-id="84930-196">Log methods that include the level in the method name (for example, `LogInformation` and `LogWarning`) are [extension methods for ILogger](xref:Microsoft.Extensions.Logging.LoggerExtensions).</span></span> <span data-ttu-id="84930-197">Diese Methoden rufen eine `Log`-Methode auf, die einen `LogLevel`-Parameter annimmt.</span><span class="sxs-lookup"><span data-stu-id="84930-197">These methods call a `Log` method that takes a `LogLevel` parameter.</span></span> <span data-ttu-id="84930-198">Sie können anstelle eines Aufrufs dieser Erweiterungsmethoden die `Log`-Methode direkt aufrufen, aber die Syntax ist relativ kompliziert.</span><span class="sxs-lookup"><span data-stu-id="84930-198">You can call the `Log` method directly rather than one of these extension methods, but the syntax is relatively complicated.</span></span> <span data-ttu-id="84930-199">Weitere Informationen finden Sie unter <xref:Microsoft.Extensions.Logging.ILogger> und im [Quellcode für Protokollierungserweiterungen](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="84930-199">For more information, see <xref:Microsoft.Extensions.Logging.ILogger> and the [logger extensions source code](https://github.com/aspnet/Extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs).</span></span>

<span data-ttu-id="84930-200">ASP.NET Core definiert die folgenden Protokolliergrade. Die Reihenfolge reicht vom geringsten bis zum höchsten Schweregrad.</span><span class="sxs-lookup"><span data-stu-id="84930-200">ASP.NET Core defines the following log levels, ordered here from lowest to highest severity.</span></span>

* <span data-ttu-id="84930-201">Trace = 0</span><span class="sxs-lookup"><span data-stu-id="84930-201">Trace = 0</span></span>

  <span data-ttu-id="84930-202">Protokolliert Informationen, die in der Regel nur für das Debuggen nützlich sind.</span><span class="sxs-lookup"><span data-stu-id="84930-202">For information that's typically valuable only for debugging.</span></span> <span data-ttu-id="84930-203">Diese Meldungen können sensible Anwendungsdaten enthalten und sollten daher nicht in einer Produktionsumgebung aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="84930-203">These messages may contain sensitive application data and so shouldn't be enabled in a production environment.</span></span> <span data-ttu-id="84930-204">*Standardmäßig deaktiviert.*</span><span class="sxs-lookup"><span data-stu-id="84930-204">*Disabled by default.*</span></span>

* <span data-ttu-id="84930-205">Debug = 1</span><span class="sxs-lookup"><span data-stu-id="84930-205">Debug = 1</span></span>

  <span data-ttu-id="84930-206">Protokolliert Informationen, die bei der Entwicklung und beim Debuggen hilfreich sein können.</span><span class="sxs-lookup"><span data-stu-id="84930-206">For information that may be useful in development and debugging.</span></span> <span data-ttu-id="84930-207">Beispiel: `Entering method Configure with flag set to true.` Aktivieren Sie Protokolle mit dem Protokolliergrad `Debug` aufgrund des hohen Protokollaufkommens nur zur Problembehandlung in der Produktion.</span><span class="sxs-lookup"><span data-stu-id="84930-207">Example: `Entering method Configure with flag set to true.` Enable `Debug` level logs in production only when troubleshooting, due to the high volume of logs.</span></span>

* <span data-ttu-id="84930-208">Information = 2</span><span class="sxs-lookup"><span data-stu-id="84930-208">Information = 2</span></span>

  <span data-ttu-id="84930-209">Zur Nachverfolgung des allgemeinen Ablaufs der App.</span><span class="sxs-lookup"><span data-stu-id="84930-209">For tracking the general flow of the app.</span></span> <span data-ttu-id="84930-210">Diese Protokolle haben typischerweise einen langfristigen Nutzen.</span><span class="sxs-lookup"><span data-stu-id="84930-210">These logs typically have some long-term value.</span></span> <span data-ttu-id="84930-211">Ein Beispiel: `Request received for path /api/todo`</span><span class="sxs-lookup"><span data-stu-id="84930-211">Example: `Request received for path /api/todo`</span></span>

* <span data-ttu-id="84930-212">Warning = 3</span><span class="sxs-lookup"><span data-stu-id="84930-212">Warning = 3</span></span>

  <span data-ttu-id="84930-213">Für ungewöhnliche oder unerwartete Ereignisse im App-Verlauf.</span><span class="sxs-lookup"><span data-stu-id="84930-213">For abnormal or unexpected events in the app flow.</span></span> <span data-ttu-id="84930-214">Dazu können Fehler oder andere Bedingungen gehören, die zwar nicht zum Beenden der App führen, aber eventuell untersucht werden müssen.</span><span class="sxs-lookup"><span data-stu-id="84930-214">These may include errors or other conditions that don't cause the app to stop but might need to be investigated.</span></span> <span data-ttu-id="84930-215">Behandelte Ausnahmen sind eine typische Verwendung für den Protokolliergrad `Warning`.</span><span class="sxs-lookup"><span data-stu-id="84930-215">Handled exceptions are a common place to use the `Warning` log level.</span></span> <span data-ttu-id="84930-216">Ein Beispiel: `FileNotFoundException for file quotes.txt.`</span><span class="sxs-lookup"><span data-stu-id="84930-216">Example: `FileNotFoundException for file quotes.txt.`</span></span>

* <span data-ttu-id="84930-217">Error = 4</span><span class="sxs-lookup"><span data-stu-id="84930-217">Error = 4</span></span>

  <span data-ttu-id="84930-218">Für Fehler und Ausnahmen, die nicht behandelt werden können.</span><span class="sxs-lookup"><span data-stu-id="84930-218">For errors and exceptions that cannot be handled.</span></span> <span data-ttu-id="84930-219">Diese Meldungen weisen auf einen Fehler in der aktuellen Aktivität oder im aktuellen Vorgang (z.B. in der aktuellen HTTP-Anforderung) und nicht auf einen anwendungsweiten Fehler hin.</span><span class="sxs-lookup"><span data-stu-id="84930-219">These messages indicate a failure in the current activity or operation (such as the current HTTP request), not an app-wide failure.</span></span> <span data-ttu-id="84930-220">Beispielprotokollmeldung: `Cannot insert record due to duplicate key violation.`</span><span class="sxs-lookup"><span data-stu-id="84930-220">Example log message: `Cannot insert record due to duplicate key violation.`</span></span>

* <span data-ttu-id="84930-221">Critical = 5</span><span class="sxs-lookup"><span data-stu-id="84930-221">Critical = 5</span></span>

  <span data-ttu-id="84930-222">Für Fehler, die sofortige Aufmerksamkeit erfordern.</span><span class="sxs-lookup"><span data-stu-id="84930-222">For failures that require immediate attention.</span></span> <span data-ttu-id="84930-223">Beispiel: Szenarios mit Datenverlust, Speichermangel.</span><span class="sxs-lookup"><span data-stu-id="84930-223">Examples: data loss scenarios, out of disk space.</span></span>

<span data-ttu-id="84930-224">Verwenden Sie den Protokolliergrad, um die Menge an Protokollausgabedaten zu steuern, die in ein bestimmtes Speichermedium geschrieben oder an ein Anzeigefenster ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="84930-224">Use the log level to control how much log output is written to a particular storage medium or display window.</span></span> <span data-ttu-id="84930-225">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="84930-225">For example:</span></span>

* <span data-ttu-id="84930-226">Senden Sie in der Produktion `Trace` über den Protokolliergrad `Information` an einen Volumedatenspeicher.</span><span class="sxs-lookup"><span data-stu-id="84930-226">In production, send `Trace` through `Information` level to a volume data store.</span></span> <span data-ttu-id="84930-227">Senden Sie `Warning` über `Critical` an einen Wertdatenspeicher.</span><span class="sxs-lookup"><span data-stu-id="84930-227">Send `Warning` through `Critical` to a value data store.</span></span>
* <span data-ttu-id="84930-228">Senden Sie während der Entwicklung `Warning` über `Critical` an die Konsole, und fügen Sie `Trace` über `Information` bei der Problembehandlung hinzu.</span><span class="sxs-lookup"><span data-stu-id="84930-228">During development, send `Warning` through `Critical` to the console, and add `Trace` through `Information` when troubleshooting.</span></span>

<span data-ttu-id="84930-229">Im Abschnitt [Protokollfilterung](#log-filtering) weiter unten in diesem Artikel wird erläutert, wie Sie steuern, welche Protokolliergrade ein Anbieter verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="84930-229">The [Log filtering](#log-filtering) section later in this article explains how to control which log levels a provider handles.</span></span>

<span data-ttu-id="84930-230">ASP.NET Core schreibt Protokolle für Frameworkereignisse.</span><span class="sxs-lookup"><span data-stu-id="84930-230">ASP.NET Core writes logs for framework events.</span></span> <span data-ttu-id="84930-231">Die zuvor in diesem Artikel gezeigten Protokollbeispiele enthielten Protokolle unterhalb des Protokolliergrads `Information`, deshalb wurden keine Protokolle des Protokolliergrads `Debug` oder `Trace` erstellt.</span><span class="sxs-lookup"><span data-stu-id="84930-231">The log examples earlier in this article excluded logs below `Information` level, so no `Debug` or `Trace` level logs were created.</span></span> <span data-ttu-id="84930-232">Hier ist ein Beispiel für Konsolenprotokolle, die durch Ausführen der Beispiel-App generiert werden, die so konfiguriert ist, dass sie `Debug`-Protokolle anzeigt:</span><span class="sxs-lookup"><span data-stu-id="84930-232">Here's an example of console logs produced by running the sample app configured to show `Debug` logs:</span></span>

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

## <a name="log-event-id"></a><span data-ttu-id="84930-233">Protokollereignis-ID</span><span class="sxs-lookup"><span data-stu-id="84930-233">Log event ID</span></span>

<span data-ttu-id="84930-234">Jedes Protokoll kann eine *Ereignis-ID* angeben.</span><span class="sxs-lookup"><span data-stu-id="84930-234">Each log can specify an *event ID*.</span></span> <span data-ttu-id="84930-235">Die Beispiel-App verwendet hierzu eine lokal definierte `LoggingEvents`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="84930-235">The sample app does this by using a locally defined `LoggingEvents` class:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/1.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

<span data-ttu-id="84930-236">Eine Ereignis-ID ordnet eine Gruppe von Ereignissen zu.</span><span class="sxs-lookup"><span data-stu-id="84930-236">An event ID associates a set of events.</span></span> <span data-ttu-id="84930-237">Beispielsweise können alle Protokolle, die sich auf die Anzeige einer Liste von Elementen auf einer Seite beziehen, 1001 sein.</span><span class="sxs-lookup"><span data-stu-id="84930-237">For example, all logs related to displaying a list of items on a page might be 1001.</span></span>

<span data-ttu-id="84930-238">Der Protokollierungsanbieter kann die Ereignis-ID in einem ID-Feld, in der Protokollierungsmeldung oder gar nicht speichern.</span><span class="sxs-lookup"><span data-stu-id="84930-238">The logging provider may store the event ID in an ID field, in the logging message, or not at all.</span></span> <span data-ttu-id="84930-239">Der Debuganbieter zeigt keine Ereignis-IDs an.</span><span class="sxs-lookup"><span data-stu-id="84930-239">The Debug provider doesn't show event IDs.</span></span> <span data-ttu-id="84930-240">Der Konsolenanbieter zeigt Ereignis-IDs in Klammern hinter der Kategorie an:</span><span class="sxs-lookup"><span data-stu-id="84930-240">The console provider shows event IDs in brackets after the category:</span></span>

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a><span data-ttu-id="84930-241">Protokollmeldungsvorlage</span><span class="sxs-lookup"><span data-stu-id="84930-241">Log message template</span></span>

<span data-ttu-id="84930-242">Jedes Protokoll gibt eine Meldungsvorlage an.</span><span class="sxs-lookup"><span data-stu-id="84930-242">Each log specifies a message template.</span></span> <span data-ttu-id="84930-243">Die Meldungsvorlage kann Platzhalter enthalten, für die Argumente bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="84930-243">The message template can contain placeholders for which arguments are provided.</span></span> <span data-ttu-id="84930-244">Verwenden Sie Namen für die Platzhalter, keine Zahlen.</span><span class="sxs-lookup"><span data-stu-id="84930-244">Use names for the placeholders, not numbers.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

<span data-ttu-id="84930-245">Die Reihenfolge der Platzhalter – nicht ihre Namen – bestimmt, welche Parameter verwendet werden, um ihre Werte zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="84930-245">The order of placeholders, not their names, determines which parameters are used to provide their values.</span></span> <span data-ttu-id="84930-246">Beachten Sie im folgenden Code, dass die Parameternamen in der Meldungsvorlage nicht in der richtigen Reihenfolge vorhanden sind:</span><span class="sxs-lookup"><span data-stu-id="84930-246">In the following code, notice that the parameter names are out of sequence in the message template:</span></span>

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

<span data-ttu-id="84930-247">Dieser Code erstellt eine Protokollierungsmeldung mit den Parameterwerten in der richtigen Reihenfolge:</span><span class="sxs-lookup"><span data-stu-id="84930-247">This code creates a log message with the parameter values in sequence:</span></span>

```
Parameter values: parm1, parm2
```

<span data-ttu-id="84930-248">Das Protokollierungsframework funktioniert auf diese Weise, damit Protokollierungsanbieter [semantische Protokollierung, die auch als strukturierte Protokollierung bezeichnet wird](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging), implementieren können.</span><span class="sxs-lookup"><span data-stu-id="84930-248">The logging framework works this way so that logging providers can implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span> <span data-ttu-id="84930-249">Die Argumente selbst (nicht nur die formatierte Meldungsvorlage) werden an das Protokollierungssystem übergeben.</span><span class="sxs-lookup"><span data-stu-id="84930-249">The arguments themselves are passed to the logging system, not just the formatted message template.</span></span> <span data-ttu-id="84930-250">Diese Informationen ermöglichen es Protokollierungsanbietern, die Parameterwerte als Felder zu speichern.</span><span class="sxs-lookup"><span data-stu-id="84930-250">This information enables logging providers to store the parameter values as fields.</span></span> <span data-ttu-id="84930-251">Nehmen wir zum Beispiel an, dass Aufrufe von Protokollierungsmethoden folgendermaßen aussehen:</span><span class="sxs-lookup"><span data-stu-id="84930-251">For example, suppose logger method calls look like this:</span></span>

```csharp
_logger.LogInformation("Getting item {ID} at {RequestTime}", id, DateTime.Now);
```

<span data-ttu-id="84930-252">Wenn Sie die Protokolle an Azure Table Storage senden, kann jede Azure Table-Entität die Eigenschaften `ID` und `RequestTime` aufweisen. Dies vereinfacht die Abfrage von Protokolldaten.</span><span class="sxs-lookup"><span data-stu-id="84930-252">If you're sending the logs to Azure Table Storage, each Azure Table entity can have `ID` and `RequestTime` properties, which simplifies queries on log data.</span></span> <span data-ttu-id="84930-253">Eine Abfrage kann alle Protokolle in einem bestimmten `RequestTime`-Bereich finden, ohne die Uhrzeit aus den Textmeldungen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="84930-253">A query can find all logs within a particular `RequestTime` range without parsing the time out of the text message.</span></span>

## <a name="logging-exceptions"></a><span data-ttu-id="84930-254">Protokollieren von Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="84930-254">Logging exceptions</span></span>

<span data-ttu-id="84930-255">Die Protokollierungsmethoden umfassen Überladungen, die das Übergeben von Ausnahmen ermöglichen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="84930-255">The logger methods have overloads that let you pass in an exception, as in the following example:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

<span data-ttu-id="84930-256">Die verschiedenen Anbieter verarbeiten die Ausnahmeinformationen auf unterschiedliche Weise.</span><span class="sxs-lookup"><span data-stu-id="84930-256">Different providers handle the exception information in different ways.</span></span> <span data-ttu-id="84930-257">Hier sehen Sie ein Beispiel einer Debuganbieterausgabe aus dem obigen Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="84930-257">Here's an example of Debug provider output from the code shown above.</span></span>

```
TodoApi.Controllers.TodoController:Warning: GetById(036dd898-fb01-47e8-9a65-f92eb73cf924) NOT FOUND

System.Exception: Item not found exception.
 at TodoApi.Controllers.TodoController.GetById(String id) in C:\logging\sample\src\TodoApi\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a><span data-ttu-id="84930-258">Protokollfilterung</span><span class="sxs-lookup"><span data-stu-id="84930-258">Log filtering</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="84930-259">Sie können einen Mindestprotokolliergrad für einen bestimmten Anbieter und eine bestimmte Kategorie oder für alle Anbieter oder alle Kategorien festlegen.</span><span class="sxs-lookup"><span data-stu-id="84930-259">You can specify a minimum log level for a specific provider and category or for all providers or all categories.</span></span> <span data-ttu-id="84930-260">Alle Protokolle unter dem Mindestgrad werden nicht an diesen Anbieter weitergeleitet, sodass sie nicht angezeigt oder gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="84930-260">Any logs below the minimum level aren't passed to that provider, so they don't get displayed or stored.</span></span>

<span data-ttu-id="84930-261">Wenn Sie alle Protokolle unterdrücken möchten, geben Sie `LogLevel.None` als Mindestprotokolliergrad an.</span><span class="sxs-lookup"><span data-stu-id="84930-261">To suppress all logs, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="84930-262">Der ganzzahlige Wert von `LogLevel.None` lautet 6 und liegt über `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="84930-262">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

### <a name="create-filter-rules-in-configuration"></a><span data-ttu-id="84930-263">Erstellen von Filterregeln in der Konfiguration</span><span class="sxs-lookup"><span data-stu-id="84930-263">Create filter rules in configuration</span></span>

<span data-ttu-id="84930-264">Der Projektvorlagencode ruft `CreateDefaultBuilder` auf, um die Protokollierung für die Konsolen- und Debuganbieter einzurichten.</span><span class="sxs-lookup"><span data-stu-id="84930-264">The project template code calls `CreateDefaultBuilder` to set up logging for the Console and Debug providers.</span></span> <span data-ttu-id="84930-265">Die `CreateDefaultBuilder`-Methode richtet die Protokollierung auch ein, um nach der Konfiguration in einem `Logging`-Abschnitt zu suchen. Hierbei wird Code wie der folgende verwendet:</span><span class="sxs-lookup"><span data-stu-id="84930-265">The `CreateDefaultBuilder` method also sets up logging to look for configuration in a `Logging` section, using code like the following:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=16)]

<span data-ttu-id="84930-266">Die Konfigurationsdaten geben die Mindestprotokolliergrade nach Anbieter und Kategorie an, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="84930-266">The configuration data specifies minimum log levels by provider and category, as in the following example:</span></span>

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

<span data-ttu-id="84930-267">Diese JSON-Konfiguration erstellt sechs Filterregeln: eine für den Debuganbieter, vier für den Konsolenanbieter und eine für alle Anbieter.</span><span class="sxs-lookup"><span data-stu-id="84930-267">This JSON creates six filter rules: one for the Debug provider, four for the Console provider, and one for all providers.</span></span> <span data-ttu-id="84930-268">Eine einzelne Regel wird für jeden Anbieter ausgewählt, wenn ein `ILogger`-Objekt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="84930-268">A single rule is chosen for each provider when an `ILogger` object is created.</span></span>

### <a name="filter-rules-in-code"></a><span data-ttu-id="84930-269">Filterregeln im Code</span><span class="sxs-lookup"><span data-stu-id="84930-269">Filter rules in code</span></span>

<span data-ttu-id="84930-270">Das folgende Beispiel zeigt, wie Filterregeln im Code registriert werden:</span><span class="sxs-lookup"><span data-stu-id="84930-270">The following example shows how to register filter rules in code:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

<span data-ttu-id="84930-271">Das zweite `AddFilter` gibt den Debuganbieter durch Verwendung des zugehörigen Typnamens an.</span><span class="sxs-lookup"><span data-stu-id="84930-271">The second `AddFilter` specifies the Debug provider by using its type name.</span></span> <span data-ttu-id="84930-272">Das erste `AddFilter` gilt für alle Anbieter, weil kein Anbietertyp angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="84930-272">The first `AddFilter` applies to all providers because it doesn't specify a provider type.</span></span>

### <a name="how-filtering-rules-are-applied"></a><span data-ttu-id="84930-273">Anwendung von Filterregeln</span><span class="sxs-lookup"><span data-stu-id="84930-273">How filtering rules are applied</span></span>

<span data-ttu-id="84930-274">Die Konfigurationsdaten und der in den vorangegangenen Beispielen gezeigte `AddFilter`-Code erzeugen die in der folgenden Tabelle gezeigten Regeln.</span><span class="sxs-lookup"><span data-stu-id="84930-274">The configuration data and the `AddFilter` code shown in the preceding examples create the rules shown in the following table.</span></span> <span data-ttu-id="84930-275">Die ersten sechs Regeln stammen aus dem Konfigurationsbeispiel, die letzten zwei Filter stammen aus dem Codebeispiel.</span><span class="sxs-lookup"><span data-stu-id="84930-275">The first six come from the configuration example and the last two come from the code example.</span></span>

| <span data-ttu-id="84930-276">Zahl</span><span class="sxs-lookup"><span data-stu-id="84930-276">Number</span></span> | <span data-ttu-id="84930-277">Anbieter</span><span class="sxs-lookup"><span data-stu-id="84930-277">Provider</span></span>      | <span data-ttu-id="84930-278">Kategorien beginnend mit...</span><span class="sxs-lookup"><span data-stu-id="84930-278">Categories that begin with ...</span></span>          | <span data-ttu-id="84930-279">Mindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="84930-279">Minimum log level</span></span> |
| :----: | ------------- | --------------------------------------- | ----------------- |
| <span data-ttu-id="84930-280">1</span><span class="sxs-lookup"><span data-stu-id="84930-280">1</span></span>      | <span data-ttu-id="84930-281">Debug</span><span class="sxs-lookup"><span data-stu-id="84930-281">Debug</span></span>         | <span data-ttu-id="84930-282">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="84930-282">All categories</span></span>                          | <span data-ttu-id="84930-283">Information</span><span class="sxs-lookup"><span data-stu-id="84930-283">Information</span></span>       |
| <span data-ttu-id="84930-284">2</span><span class="sxs-lookup"><span data-stu-id="84930-284">2</span></span>      | <span data-ttu-id="84930-285">Konsole</span><span class="sxs-lookup"><span data-stu-id="84930-285">Console</span></span>       | <span data-ttu-id="84930-286">Microsoft.AspNetCore.Mvc.Razor.Internal</span><span class="sxs-lookup"><span data-stu-id="84930-286">Microsoft.AspNetCore.Mvc.Razor.Internal</span></span> | <span data-ttu-id="84930-287">Warnung</span><span class="sxs-lookup"><span data-stu-id="84930-287">Warning</span></span>           |
| <span data-ttu-id="84930-288">3</span><span class="sxs-lookup"><span data-stu-id="84930-288">3</span></span>      | <span data-ttu-id="84930-289">Konsole</span><span class="sxs-lookup"><span data-stu-id="84930-289">Console</span></span>       | <span data-ttu-id="84930-290">Microsoft.AspNetCore.Mvc.Razor.Razor</span><span class="sxs-lookup"><span data-stu-id="84930-290">Microsoft.AspNetCore.Mvc.Razor.Razor</span></span>    | <span data-ttu-id="84930-291">Debug</span><span class="sxs-lookup"><span data-stu-id="84930-291">Debug</span></span>             |
| <span data-ttu-id="84930-292">4</span><span class="sxs-lookup"><span data-stu-id="84930-292">4</span></span>      | <span data-ttu-id="84930-293">Konsole</span><span class="sxs-lookup"><span data-stu-id="84930-293">Console</span></span>       | <span data-ttu-id="84930-294">Microsoft.AspNetCore.Mvc.Razor</span><span class="sxs-lookup"><span data-stu-id="84930-294">Microsoft.AspNetCore.Mvc.Razor</span></span>          | <span data-ttu-id="84930-295">Fehler</span><span class="sxs-lookup"><span data-stu-id="84930-295">Error</span></span>             |
| <span data-ttu-id="84930-296">5</span><span class="sxs-lookup"><span data-stu-id="84930-296">5</span></span>      | <span data-ttu-id="84930-297">Konsole</span><span class="sxs-lookup"><span data-stu-id="84930-297">Console</span></span>       | <span data-ttu-id="84930-298">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="84930-298">All categories</span></span>                          | <span data-ttu-id="84930-299">Information</span><span class="sxs-lookup"><span data-stu-id="84930-299">Information</span></span>       |
| <span data-ttu-id="84930-300">6</span><span class="sxs-lookup"><span data-stu-id="84930-300">6</span></span>      | <span data-ttu-id="84930-301">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="84930-301">All providers</span></span> | <span data-ttu-id="84930-302">Alle Kategorien</span><span class="sxs-lookup"><span data-stu-id="84930-302">All categories</span></span>                          | <span data-ttu-id="84930-303">Debug</span><span class="sxs-lookup"><span data-stu-id="84930-303">Debug</span></span>             |
| <span data-ttu-id="84930-304">7</span><span class="sxs-lookup"><span data-stu-id="84930-304">7</span></span>      | <span data-ttu-id="84930-305">Alle Anbieter</span><span class="sxs-lookup"><span data-stu-id="84930-305">All providers</span></span> | <span data-ttu-id="84930-306">System</span><span class="sxs-lookup"><span data-stu-id="84930-306">System</span></span>                                  | <span data-ttu-id="84930-307">Debug</span><span class="sxs-lookup"><span data-stu-id="84930-307">Debug</span></span>             |
| <span data-ttu-id="84930-308">8</span><span class="sxs-lookup"><span data-stu-id="84930-308">8</span></span>      | <span data-ttu-id="84930-309">Debug</span><span class="sxs-lookup"><span data-stu-id="84930-309">Debug</span></span>         | <span data-ttu-id="84930-310">Microsoft</span><span class="sxs-lookup"><span data-stu-id="84930-310">Microsoft</span></span>                               | <span data-ttu-id="84930-311">Ablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="84930-311">Trace</span></span>             |

<span data-ttu-id="84930-312">Wenn ein `ILogger`-Objekt erstellt wird, wählt das `ILoggerFactory`-Objekt eine einzige Regel pro Anbieter aus, die auf diese Protokollierung angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="84930-312">When an `ILogger` object is created, the `ILoggerFactory` object selects a single rule per provider to apply to that logger.</span></span> <span data-ttu-id="84930-313">Alle von einer `ILogger`-Instanz geschriebenen Meldungen werden auf Grundlage der ausgewählten Regeln gefiltert.</span><span class="sxs-lookup"><span data-stu-id="84930-313">All messages written by an `ILogger` instance are filtered based on the selected rules.</span></span> <span data-ttu-id="84930-314">Aus den verfügbaren Regeln wird die für jeden Anbieter und jedes Kategoriepaar spezifischste Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="84930-314">The most specific rule possible for each provider and category pair is selected from the available rules.</span></span>

<span data-ttu-id="84930-315">Beim Erstellen von `ILogger` für eine vorgegebene Kategorie wird für jeden Anbieter der folgende Algorithmus verwendet:</span><span class="sxs-lookup"><span data-stu-id="84930-315">The following algorithm is used for each provider when an `ILogger` is created for a given category:</span></span>

* <span data-ttu-id="84930-316">Es werden alle Regeln ausgewählt, die dem Anbieter oder dem zugehörigen Alias entsprechen.</span><span class="sxs-lookup"><span data-stu-id="84930-316">Select all rules that match the provider or its alias.</span></span> <span data-ttu-id="84930-317">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln mit einem leeren Anbieter ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="84930-317">If no match is found, select all rules with an empty provider.</span></span>
* <span data-ttu-id="84930-318">Aus dem Ergebnis des vorherigen Schritts werden die Regeln mit dem längsten übereinstimmenden Kategoriepräfix ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="84930-318">From the result of the preceding step, select rules with longest matching category prefix.</span></span> <span data-ttu-id="84930-319">Wenn keine Übereinstimmung gefunden wird, werden alle Regeln ohne Angabe einer Kategorie ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="84930-319">If no match is found, select all rules that don't specify a category.</span></span>
* <span data-ttu-id="84930-320">Wenn mehrere Regeln ausgewählt sind, wird die **letzte** Regel verwendet.</span><span class="sxs-lookup"><span data-stu-id="84930-320">If multiple rules are selected, take the **last** one.</span></span>
* <span data-ttu-id="84930-321">Wenn keine Regel ausgewählt ist, wird `MinimumLevel` verwendet.</span><span class="sxs-lookup"><span data-stu-id="84930-321">If no rules are selected, use `MinimumLevel`.</span></span>

<span data-ttu-id="84930-322">Angenommen, Sie erstellen mit der oben aufgeführten Liste mit Regeln ein `ILogger`-Objekt für die Kategorie „Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine“:</span><span class="sxs-lookup"><span data-stu-id="84930-322">With the preceding list of rules, suppose you create an `ILogger` object for category "Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine":</span></span>

* <span data-ttu-id="84930-323">Für den Debuganbieter gelten die Regeln 1, 6 und 8.</span><span class="sxs-lookup"><span data-stu-id="84930-323">For the Debug provider, rules 1, 6, and 8 apply.</span></span> <span data-ttu-id="84930-324">Regel 8 ist die spezifischste Regel, deshalb wird diese Regel ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="84930-324">Rule 8 is most specific, so that's the one selected.</span></span>
* <span data-ttu-id="84930-325">Für den Konsolenanbieter gelten die Regeln 3, 4, 5 und 6.</span><span class="sxs-lookup"><span data-stu-id="84930-325">For the Console provider, rules 3, 4, 5, and 6 apply.</span></span> <span data-ttu-id="84930-326">Regel 3 ist die spezifischste Regel.</span><span class="sxs-lookup"><span data-stu-id="84930-326">Rule 3 is most specific.</span></span>

<span data-ttu-id="84930-327">Die sich ergebende `ILogger`-Instanz sendet Protokolle mit dem Protokolliergrad `Trace` und höher an den Debuganbieter.</span><span class="sxs-lookup"><span data-stu-id="84930-327">The resulting `ILogger` instance sends logs of `Trace` level and above to the Debug provider.</span></span> <span data-ttu-id="84930-328">Protokolle mit dem Protokolliergrad `Debug` und höher werden an den Konsolenanbieter gesendet.</span><span class="sxs-lookup"><span data-stu-id="84930-328">Logs of `Debug` level and above are sent to the Console provider.</span></span>

### <a name="provider-aliases"></a><span data-ttu-id="84930-329">Anbieteraliase</span><span class="sxs-lookup"><span data-stu-id="84930-329">Provider aliases</span></span>

<span data-ttu-id="84930-330">Jeder Anbieter definiert einen *Alias*, der in der Konfiguration anstelle des vollqualifizierten Typnamens verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="84930-330">Each provider defines an *alias* that can be used in configuration in place of the fully qualified type name.</span></span>  <span data-ttu-id="84930-331">Verwenden Sie für die integrierten Anbieter die folgenden Aliase:</span><span class="sxs-lookup"><span data-stu-id="84930-331">For the built-in providers, use the following aliases:</span></span>

* <span data-ttu-id="84930-332">Konsole</span><span class="sxs-lookup"><span data-stu-id="84930-332">Console</span></span>
* <span data-ttu-id="84930-333">Debug</span><span class="sxs-lookup"><span data-stu-id="84930-333">Debug</span></span>
* <span data-ttu-id="84930-334">EventLog</span><span class="sxs-lookup"><span data-stu-id="84930-334">EventLog</span></span>
* <span data-ttu-id="84930-335">AzureAppServices</span><span class="sxs-lookup"><span data-stu-id="84930-335">AzureAppServices</span></span>
* <span data-ttu-id="84930-336">TraceSource</span><span class="sxs-lookup"><span data-stu-id="84930-336">TraceSource</span></span>
* <span data-ttu-id="84930-337">EventSource</span><span class="sxs-lookup"><span data-stu-id="84930-337">EventSource</span></span>

### <a name="default-minimum-level"></a><span data-ttu-id="84930-338">Standardmindestprotokolliergrad</span><span class="sxs-lookup"><span data-stu-id="84930-338">Default minimum level</span></span>

<span data-ttu-id="84930-339">Es gibt eine Einstellung für den Mindestprotokolliergrad, die nur dann wirksam wird, wenn für einen bestimmten Anbieter und eine bestimmte Kategorie keine Regeln aus Konfiguration oder Code gelten.</span><span class="sxs-lookup"><span data-stu-id="84930-339">There's a minimum level setting that takes effect only if no rules from configuration or code apply for a given provider and category.</span></span> <span data-ttu-id="84930-340">Im folgenden Beispiel wird das Festlegen des Mindestprotokolliergrads veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="84930-340">The following example shows how to set the minimum level:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

<span data-ttu-id="84930-341">Wenn Sie den Mindestprotokolliergrad nicht explizit festlegen, lautet der Standardwert `Information`, d.h. Protokolle der Ebene `Trace` und `Debug` werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="84930-341">If you don't explicitly set the minimum level, the default value is `Information`, which means that `Trace` and `Debug` logs are ignored.</span></span>

### <a name="filter-functions"></a><span data-ttu-id="84930-342">Filterfunktionen</span><span class="sxs-lookup"><span data-stu-id="84930-342">Filter functions</span></span>

<span data-ttu-id="84930-343">Eine Filterfunktion wird für alle Anbieter und Kategorien aufgerufen, denen keine Regeln durch Konfiguration oder Code zugewiesen sind.</span><span class="sxs-lookup"><span data-stu-id="84930-343">A filter function is invoked for all providers and categories that don't have rules assigned to them by configuration or code.</span></span> <span data-ttu-id="84930-344">Code in der Funktion verfügt über Zugriff auf den Anbietertyp, die Kategorie und den Protokolliergrad.</span><span class="sxs-lookup"><span data-stu-id="84930-344">Code in the function has access to the provider type, category, and log level.</span></span> <span data-ttu-id="84930-345">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="84930-345">For example:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="84930-346">Bei einigen Protokollierungsanbietern können Sie angeben, wann Protokolle in ein Speichermedium geschrieben oder ignoriert werden sollen – je nach Protokolliergrad und Kategorie.</span><span class="sxs-lookup"><span data-stu-id="84930-346">Some logging providers let you specify when logs should be written to a storage medium or ignored based on log level and category.</span></span>

<span data-ttu-id="84930-347">Die Erweiterungsmethoden `AddConsole` und `AddDebug` bieten Überladungen, die Filterkriterien annehmen können.</span><span class="sxs-lookup"><span data-stu-id="84930-347">The `AddConsole` and `AddDebug` extension methods provide overloads that accept filtering criteria.</span></span> <span data-ttu-id="84930-348">Der folgende Beispielcode veranlasst den Konsolenanbieter, Protokolle unterhalb der Ebene `Warning` zu ignorieren, während der Debuganbieter vom Framework erstellte Protokolle ignoriert.</span><span class="sxs-lookup"><span data-stu-id="84930-348">The following sample code causes the console provider to ignore logs below `Warning` level, while the Debug provider ignores logs that the framework creates.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_AddConsoleAndDebugWithFilter&highlight=6-7)]

<span data-ttu-id="84930-349">Die Methode `AddEventLog` umfasst eine Überladung mit einer `EventLogSettings`-Instanz, die eine Filterfunktion in ihrer `Filter`-Eigenschaft enthalten kann.</span><span class="sxs-lookup"><span data-stu-id="84930-349">The `AddEventLog` method has an overload that takes an `EventLogSettings` instance, which may contain a filtering function in its `Filter` property.</span></span> <span data-ttu-id="84930-350">Der TraceSource-Anbieter stellt keine dieser Überladungen zur Verfügung, da der zugehörige Protokolliergrad und weitere Parameter auf der Verwendung von `SourceSwitch` und `TraceListener` basieren.</span><span class="sxs-lookup"><span data-stu-id="84930-350">The TraceSource provider doesn't provide any of those overloads, since its logging level and other parameters are based on the `SourceSwitch` and `TraceListener` it uses.</span></span>

<span data-ttu-id="84930-351">Um Filterregeln für alle bei einer `ILoggerFactory`-Instanz registrierten Anbieter festzulegen, verwenden Sie die Erweiterungsmethode `WithFilter`.</span><span class="sxs-lookup"><span data-stu-id="84930-351">To set filtering rules for all providers that are registered with an `ILoggerFactory` instance, use the `WithFilter` extension method.</span></span> <span data-ttu-id="84930-352">Das folgende Beispiel beschränkt Frameworkprotokolle (Kategorie beginnt mit „Microsoft“ oder „System“) auf Warnungen, während die Protokollierung auf Debugebene für Protokolle erfolgt, die durch Anwendungscode erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="84930-352">The example below limits framework logs (category begins with "Microsoft" or "System") to warnings while logging at debug level for logs created by application code.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_FactoryFilter&highlight=6-11)]

<span data-ttu-id="84930-353">Um zu verhindern, dass Protokolle geschrieben werden, geben Sie `LogLevel.None` als Mindestprotokolliergrad an.</span><span class="sxs-lookup"><span data-stu-id="84930-353">To prevent any logs from being written, specify `LogLevel.None` as the minimum log level.</span></span> <span data-ttu-id="84930-354">Der ganzzahlige Wert von `LogLevel.None` lautet 6 und liegt über `LogLevel.Critical` (5).</span><span class="sxs-lookup"><span data-stu-id="84930-354">The integer value of `LogLevel.None` is 6, which is higher than `LogLevel.Critical` (5).</span></span>

<span data-ttu-id="84930-355">Die Erweiterungsmethode `WithFilter` wird vom NuGet-Paket [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="84930-355">The `WithFilter` extension method is provided by the [Microsoft.Extensions.Logging.Filter](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Filter) NuGet package.</span></span> <span data-ttu-id="84930-356">Die Methode gibt eine neue `ILoggerFactory`-Instanz zurück, die Protokollmeldungen für alle bei ihr registrierten Protokollierungsanbieter filtert.</span><span class="sxs-lookup"><span data-stu-id="84930-356">The method returns a new `ILoggerFactory` instance that will filter the log messages passed to all logger providers registered with it.</span></span> <span data-ttu-id="84930-357">Andere `ILoggerFactory`-Instanzen, die ursprüngliche `ILoggerFactory`-Instanz eingeschlossen, sind nicht betroffen.</span><span class="sxs-lookup"><span data-stu-id="84930-357">It doesn't affect any other `ILoggerFactory` instances, including the original `ILoggerFactory` instance.</span></span>

::: moniker-end

## <a name="system-categories-and-levels"></a><span data-ttu-id="84930-358">Systemkategorien und -protokolliergrade</span><span class="sxs-lookup"><span data-stu-id="84930-358">System categories and levels</span></span>

<span data-ttu-id="84930-359">Dies sind einige Kategorien, die vom ASP.NET Core und Entity Framework Core verwendet werden, mit Hinweisen darauf, welche Protokolle von ihnen zu erwarten sind:</span><span class="sxs-lookup"><span data-stu-id="84930-359">Here are some categories used by ASP.NET Core and Entity Framework Core, with notes about what logs to expect from them:</span></span>

| <span data-ttu-id="84930-360">Kategorie</span><span class="sxs-lookup"><span data-stu-id="84930-360">Category</span></span>                            | <span data-ttu-id="84930-361">Hinweise</span><span class="sxs-lookup"><span data-stu-id="84930-361">Notes</span></span> |
| ----------------------------------- | ----- |
| <span data-ttu-id="84930-362">Microsoft.AspNetCore</span><span class="sxs-lookup"><span data-stu-id="84930-362">Microsoft.AspNetCore</span></span>                | <span data-ttu-id="84930-363">Allgemeine ASP.NET Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="84930-363">General ASP.NET Core diagnostics.</span></span> |
| <span data-ttu-id="84930-364">Microsoft.AspNetCore.DataProtection</span><span class="sxs-lookup"><span data-stu-id="84930-364">Microsoft.AspNetCore.DataProtection</span></span> | <span data-ttu-id="84930-365">Gibt an, welche Schlüssel in Betracht gezogen, gefunden und verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="84930-365">Which keys were considered, found, and used.</span></span> |
| <span data-ttu-id="84930-366">Microsoft.AspNetCore.HostFiltering</span><span class="sxs-lookup"><span data-stu-id="84930-366">Microsoft.AspNetCore.HostFiltering</span></span>  | <span data-ttu-id="84930-367">Hosts sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="84930-367">Hosts allowed.</span></span> |
| <span data-ttu-id="84930-368">Microsoft.AspNetCore.Hosting</span><span class="sxs-lookup"><span data-stu-id="84930-368">Microsoft.AspNetCore.Hosting</span></span>        | <span data-ttu-id="84930-369">Gibt an, wie lange es bis zum Abschluss von HTTP-Anforderungen gedauert hat und zu welcher Zeit sie gestartet wurden.</span><span class="sxs-lookup"><span data-stu-id="84930-369">How long HTTP requests took to complete and what time they started.</span></span> <span data-ttu-id="84930-370">Gibt an, welche Hostingstartassemblys geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="84930-370">Which hosting startup assemblies were loaded.</span></span> |
| <span data-ttu-id="84930-371">Microsoft.AspNetCore.Mvc</span><span class="sxs-lookup"><span data-stu-id="84930-371">Microsoft.AspNetCore.Mvc</span></span>            | <span data-ttu-id="84930-372">MVC- und Razor-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="84930-372">MVC and Razor diagnostics.</span></span> <span data-ttu-id="84930-373">Modellbindung, Filterausführung, Ansichtskompilierung, Aktionsauswahl.</span><span class="sxs-lookup"><span data-stu-id="84930-373">Model binding, filter execution, view compilation, action selection.</span></span> |
| <span data-ttu-id="84930-374">Microsoft.AspNetCore.Routing</span><span class="sxs-lookup"><span data-stu-id="84930-374">Microsoft.AspNetCore.Routing</span></span>        | <span data-ttu-id="84930-375">Gibt Routenabgleichsinformationen an.</span><span class="sxs-lookup"><span data-stu-id="84930-375">Route matching information.</span></span> |
| <span data-ttu-id="84930-376">Microsoft.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="84930-376">Microsoft.AspNetCore.Server</span></span>         | <span data-ttu-id="84930-377">Start-, Beendigungs- und Keep-Alive-Antworten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="84930-377">Connection start, stop, and keep alive responses.</span></span> <span data-ttu-id="84930-378">HTTPS-Zertifikatinformationen.</span><span class="sxs-lookup"><span data-stu-id="84930-378">HTTPS certificate information.</span></span> |
| <span data-ttu-id="84930-379">Microsoft.AspNetCore.StaticFiles</span><span class="sxs-lookup"><span data-stu-id="84930-379">Microsoft.AspNetCore.StaticFiles</span></span>    | <span data-ttu-id="84930-380">Die bereitgestellten Dateien.</span><span class="sxs-lookup"><span data-stu-id="84930-380">Files served.</span></span> |
| <span data-ttu-id="84930-381">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="84930-381">Microsoft.EntityFrameworkCore</span></span>       | <span data-ttu-id="84930-382">Allgemeine Entity Framework Core-Diagnose.</span><span class="sxs-lookup"><span data-stu-id="84930-382">General Entity Framework Core diagnostics.</span></span> <span data-ttu-id="84930-383">Datenbankaktivität und -konfiguration, Änderungserkennung, Migrationen.</span><span class="sxs-lookup"><span data-stu-id="84930-383">Database activity and configuration, change detection, migrations.</span></span> |

## <a name="log-scopes"></a><span data-ttu-id="84930-384">Protokollbereiche</span><span class="sxs-lookup"><span data-stu-id="84930-384">Log scopes</span></span>

 <span data-ttu-id="84930-385">Ein *Bereich* kann einen Satz logischer Vorgänge gruppieren.</span><span class="sxs-lookup"><span data-stu-id="84930-385">A *scope* can group a set of logical operations.</span></span> <span data-ttu-id="84930-386">Diese Gruppierung kann verwendet werden, um an jedes Protokoll, das als Teil einer Gruppe erstellt wird, die gleichen Daten anzufügen.</span><span class="sxs-lookup"><span data-stu-id="84930-386">This grouping can be used to attach the same data to each log that's created as part of a set.</span></span> <span data-ttu-id="84930-387">So kann beispielsweise jedes Protokoll, das im Rahmen der Verarbeitung einer Transaktion erstellt wird, die Transaktions-ID enthalten.</span><span class="sxs-lookup"><span data-stu-id="84930-387">For example, every log created as part of processing a transaction can include the transaction ID.</span></span>

<span data-ttu-id="84930-388">Ein Bereich ist ein `IDisposable`-Typ, der von der <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*>-Methode zurückgegeben und so lange beibehalten wird, bis er verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="84930-388">A scope is an `IDisposable` type that's returned by the <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> method and lasts until it's disposed.</span></span> <span data-ttu-id="84930-389">Verwenden Sie einen Bereich, indem Sie Protokollierungsaufrufe mit einem `using`-Block umschließen, der als Wrapper verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="84930-389">Use a scope by wrapping logger calls in a `using` block:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

<span data-ttu-id="84930-390">Der folgende Code aktiviert Bereiche für den Konsolenanbieter:</span><span class="sxs-lookup"><span data-stu-id="84930-390">The following code enables scopes for the console provider:</span></span>

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="84930-391">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="84930-391">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="84930-392">Um die bereichsbasierte Protokollierung zu aktivieren, muss die Konsolenprotokollierungsoption `IncludeScopes` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="84930-392">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>
>
> <span data-ttu-id="84930-393">Weitere Informationen zur Konfiguration finden Sie im Abschnitt [Konfiguration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="84930-393">For information on configuration, see the [Configuration](#configuration) section.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="84930-394">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="84930-394">*Program.cs*:</span></span>

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

> [!NOTE]
> <span data-ttu-id="84930-395">Um die bereichsbasierte Protokollierung zu aktivieren, muss die Konsolenprotokollierungsoption `IncludeScopes` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="84930-395">Configuring the `IncludeScopes` console logger option is required to enable scope-based logging.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="84930-396">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="84930-396">*Startup.cs*:</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

<span data-ttu-id="84930-397">Jede Protokollmeldung enthält die bereichsbezogenen Informationen:</span><span class="sxs-lookup"><span data-stu-id="84930-397">Each log message includes the scoped information:</span></span>

```
info: TodoApi.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApi.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a><span data-ttu-id="84930-398">Integrierte Protokollierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="84930-398">Built-in logging providers</span></span>

<span data-ttu-id="84930-399">ASP.NET Core wird mit den folgenden Anbietern bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="84930-399">ASP.NET Core ships the following providers:</span></span>

* [<span data-ttu-id="84930-400">Konsole</span><span class="sxs-lookup"><span data-stu-id="84930-400">Console</span></span>](#console-provider)
* [<span data-ttu-id="84930-401">Debuggen</span><span class="sxs-lookup"><span data-stu-id="84930-401">Debug</span></span>](#debug-provider)
* [<span data-ttu-id="84930-402">EventSource</span><span class="sxs-lookup"><span data-stu-id="84930-402">EventSource</span></span>](#eventsource-provider)
* [<span data-ttu-id="84930-403">EventLog</span><span class="sxs-lookup"><span data-stu-id="84930-403">EventLog</span></span>](#windows-eventlog-provider)
* [<span data-ttu-id="84930-404">TraceSource</span><span class="sxs-lookup"><span data-stu-id="84930-404">TraceSource</span></span>](#tracesource-provider)

<span data-ttu-id="84930-405">Optionen für [Protokollierung in Azure](#logging-in-azure) werden weiter unten in diesem Artikel behandelt.</span><span class="sxs-lookup"><span data-stu-id="84930-405">Options for [Logging in Azure](#logging-in-azure) are covered later in this article.</span></span>

<span data-ttu-id="84930-406">Weitere Informationen zur stdout-Protokollierung finden Sie unter <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> und <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>.</span><span class="sxs-lookup"><span data-stu-id="84930-406">For information about stdout logging, see <xref:host-and-deploy/iis/troubleshoot#aspnet-core-module-stdout-log> and <xref:host-and-deploy/azure-apps/troubleshoot#aspnet-core-module-stdout-log>.</span></span>

### <a name="console-provider"></a><span data-ttu-id="84930-407">Der Konsolenanbieter</span><span class="sxs-lookup"><span data-stu-id="84930-407">Console provider</span></span>

<span data-ttu-id="84930-408">Das Anbieterpaket [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) sendet eine Protokollausgabe an die Konsole.</span><span class="sxs-lookup"><span data-stu-id="84930-408">The [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) provider package sends log output to the console.</span></span> 

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddConsole();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddConsole();
```

<span data-ttu-id="84930-409">Mithilfe von [AddConsole-Überladungen](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) können Sie einen Mindestprotokolliergrad, eine Filterfunktion und einen booleschen Wert übergeben, der angibt, ob Bereiche unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="84930-409">[AddConsole overloads](xref:Microsoft.Extensions.Logging.ConsoleLoggerExtensions) let you pass in a minimum log level, a filter function, and a boolean that indicates whether scopes are supported.</span></span> <span data-ttu-id="84930-410">Darüber hinaus kann ein `IConfiguration`-Objekt übergeben werden, mit dem Bereichsunterstützung und Protokolliergrade angegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="84930-410">Another option is to pass in an `IConfiguration` object, which can specify scopes support and logging levels.</span></span>

<span data-ttu-id="84930-411">Der Konsolenanbieter hat einen erheblichen Einfluss auf die Leistung und ist allgemein nicht für den Einsatz in der Produktion geeignet.</span><span class="sxs-lookup"><span data-stu-id="84930-411">The console provider has a significant impact on performance and is generally not appropriate for use in production.</span></span>

<span data-ttu-id="84930-412">Wenn Sie ein neues Projekt in Visual Studio erstellen, sieht die `AddConsole`-Methode folgendermaßen aus:</span><span class="sxs-lookup"><span data-stu-id="84930-412">When you create a new project in Visual Studio, the `AddConsole` method looks like this:</span></span>

```csharp
loggerFactory.AddConsole(Configuration.GetSection("Logging"));
```

<span data-ttu-id="84930-413">Dieser Code verweist auf den `Logging`-Abschnitt der Datei *appSettings.json*:</span><span class="sxs-lookup"><span data-stu-id="84930-413">This code refers to the `Logging` section of the *appSettings.json* file:</span></span>

[!code-json[](index/samples/1.x/TodoApiSample//appsettings.json)]

<span data-ttu-id="84930-414">Die gezeigten Einstellungen schränken die Frameworkprotokolle auf Warnungen ein, während die App eine Protokollierung auf Debugebene durchführt, wie im Abschnitt [Protokollfilterung](#log-filtering) erläutert.</span><span class="sxs-lookup"><span data-stu-id="84930-414">The settings shown limit framework logs to warnings while allowing the app to log at debug level, as explained in the [Log filtering](#log-filtering) section.</span></span> <span data-ttu-id="84930-415">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="84930-415">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

::: moniker-end

<span data-ttu-id="84930-416">Um die Ausgabe der Konsolenprotokollierung anzuzeigen, öffnen Sie eine Eingabeaufforderung im Projektordner und führen den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="84930-416">To see console logging output, open a command prompt in the project folder and run the following command:</span></span>

```console
dotnet run
```

### <a name="debug-provider"></a><span data-ttu-id="84930-417">Der Debuganbieter</span><span class="sxs-lookup"><span data-stu-id="84930-417">Debug provider</span></span>

<span data-ttu-id="84930-418">Beim Anbieterpaket [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) erfolgt die Protokollausgabe unter Verwendung der Klasse [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) (`Debug.WriteLine`-Methodenaufrufe).</span><span class="sxs-lookup"><span data-stu-id="84930-418">The [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) provider package writes log output by using the [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) class (`Debug.WriteLine` method calls).</span></span>

<span data-ttu-id="84930-419">Unter Linux werden Protokolle dieses Anbieters in */var/log/message* geschrieben.</span><span class="sxs-lookup"><span data-stu-id="84930-419">On Linux, this provider writes logs to */var/log/message*.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddDebug();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddDebug();
```

<span data-ttu-id="84930-420">Mithilfe von [AddDebug-Überladungen](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) können Sie einen Mindestprotokolliergrad oder eine Filterfunktion übergeben.</span><span class="sxs-lookup"><span data-stu-id="84930-420">[AddDebug overloads](xref:Microsoft.Extensions.Logging.DebugLoggerFactoryExtensions) let you pass in a minimum log level or a filter function.</span></span>

::: moniker-end

### <a name="eventsource-provider"></a><span data-ttu-id="84930-421">Der EventSource-Anbieter</span><span class="sxs-lookup"><span data-stu-id="84930-421">EventSource provider</span></span>

<span data-ttu-id="84930-422">Für Apps, die für ASP.NET Core 1.1.0 oder höher konzipiert sind, kann mit dem Anbieterpaket [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) eine Ereignisablaufverfolgung implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="84930-422">For apps that target ASP.NET Core 1.1.0 or later, the [Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) provider package can implement event tracing.</span></span> <span data-ttu-id="84930-423">Verwenden Sie unter Windows [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span><span class="sxs-lookup"><span data-stu-id="84930-423">On Windows, it uses [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803).</span></span> <span data-ttu-id="84930-424">Der Anbieter ist plattformunabhängig, aber für Linux oder macOS sind Ereignissammlung und Anzeigetools noch nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="84930-424">The provider is cross-platform, but there are no event collection and display tools yet for Linux or macOS.</span></span>

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

<span data-ttu-id="84930-425">Eine gute Möglichkeit zum Erfassen und Anzeigen von Protokollen ist die Verwendung des Hilfsprogramms [PerfView](https://github.com/Microsoft/perfview).</span><span class="sxs-lookup"><span data-stu-id="84930-425">A good way to collect and view logs is to use the [PerfView utility](https://github.com/Microsoft/perfview).</span></span> <span data-ttu-id="84930-426">Es gibt andere Tools zur Anzeige von ETW-Protokollen, aber PerfView bietet die besten Ergebnisse bei der Arbeit mit ETW-Ereignissen, die von ASP.NET ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="84930-426">There are other tools for viewing ETW logs, but PerfView provides the best experience for working with the ETW events emitted by ASP.NET.</span></span>

<span data-ttu-id="84930-427">Um PerfView für das Erfassen von Ereignissen zu konfigurieren, die von diesem Anbieter protokolliert wurden, fügen Sie die Zeichenfolge `*Microsoft-Extensions-Logging` zur Liste **Zusätzliche Anbieter** hinzu.</span><span class="sxs-lookup"><span data-stu-id="84930-427">To configure PerfView for collecting events logged by this provider, add the string `*Microsoft-Extensions-Logging` to the **Additional Providers** list.</span></span> <span data-ttu-id="84930-428">(Vergessen Sie nicht das Sternchen am Anfang der Zeichenfolge.)</span><span class="sxs-lookup"><span data-stu-id="84930-428">(Don't miss the asterisk at the start of the string.)</span></span>

![Zusätzliche PerfView-Anbieter](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a><span data-ttu-id="84930-430">Der Windows-EventLog-Anbieter</span><span class="sxs-lookup"><span data-stu-id="84930-430">Windows EventLog provider</span></span>

<span data-ttu-id="84930-431">Das Anbieterpaket [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) sendet eine Protokollausgabe in das Windows-Ereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="84930-431">The [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) provider package sends log output to the Windows Event Log.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
logging.AddEventLog();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
loggerFactory.AddEventLog();
```

<span data-ttu-id="84930-432">Mithilfe von [AddEventLog-Überladungen](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) können Sie `EventLogSettings` oder einen Mindestprotokolliergrad übergeben.</span><span class="sxs-lookup"><span data-stu-id="84930-432">[AddEventLog overloads](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions) let you pass in `EventLogSettings` or a minimum log level.</span></span>

::: moniker-end

### <a name="tracesource-provider"></a><span data-ttu-id="84930-433">Der TraceSource-Anbieter</span><span class="sxs-lookup"><span data-stu-id="84930-433">TraceSource provider</span></span>

<span data-ttu-id="84930-434">Das Anbieterpaket [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) verwendet die <xref:System.Diagnostics.TraceSource>-Bibliotheken und -Anbieter.</span><span class="sxs-lookup"><span data-stu-id="84930-434">The [Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) provider package uses the <xref:System.Diagnostics.TraceSource> libraries and providers.</span></span>

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

<span data-ttu-id="84930-435">Mithilfe von [AddTraceSource-Überladungen](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) können Sie eine Quelloption und einen Listener für die Ablaufverfolgung übergeben.</span><span class="sxs-lookup"><span data-stu-id="84930-435">[AddTraceSource overloads](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) let you pass in a source switch and a trace listener.</span></span>

<span data-ttu-id="84930-436">Um diesen Anbieter zu verwenden, muss eine App unter .NET Framework (anstelle von .NET Core) ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="84930-436">To use this provider, an app has to run on the .NET Framework (rather than .NET Core).</span></span> <span data-ttu-id="84930-437">Der Anbieter kann Nachrichten an eine Vielzahl von [Listenern](/dotnet/framework/debug-trace-profile/trace-listeners) weiterleiten, z.B. an den in der Beispiel-App verwendeten <xref:System.Diagnostics.TextWriterTraceListener>.</span><span class="sxs-lookup"><span data-stu-id="84930-437">The provider can route messages to a variety of [listeners](/dotnet/framework/debug-trace-profile/trace-listeners), such as the <xref:System.Diagnostics.TextWriterTraceListener> used in the sample app.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="84930-438">Im folgenden Beispiel wird ein `TraceSource`-Anbieter konfiguriert, der Protokollmeldungen der Ebene `Warning` und höher an das Konsolenfenster ausgibt.</span><span class="sxs-lookup"><span data-stu-id="84930-438">The following example configures a `TraceSource` provider that logs `Warning` and higher messages to the console window.</span></span>

[!code-csharp[](index/samples/1.x/TodoApiSample/Startup.cs?name=snippet_TraceSource&highlight=9-12)]

::: moniker-end

## <a name="logging-in-azure"></a><span data-ttu-id="84930-439">Protokollierung in Azure</span><span class="sxs-lookup"><span data-stu-id="84930-439">Logging in Azure</span></span>

<span data-ttu-id="84930-440">Informationen zur Protokollierung in Azure finden Sie in den folgenden Abschnitten:</span><span class="sxs-lookup"><span data-stu-id="84930-440">For information about logging in Azure, see the following sections:</span></span>

* [<span data-ttu-id="84930-441">Azure App Service-Anbieter</span><span class="sxs-lookup"><span data-stu-id="84930-441">Azure App Service provider</span></span>](#azure-app-service-provider)
* [<span data-ttu-id="84930-442">Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="84930-442">Azure log streaming</span></span>](#azure-log-streaming)

::: moniker range=">= aspnetcore-1.1"

* [<span data-ttu-id="84930-443">Ablaufverfolgungsprotokollierung für Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="84930-443">Azure Application Insights trace logging</span></span>](#azure-application-insights-trace-logging)

::: moniker-end

### <a name="azure-app-service-provider"></a><span data-ttu-id="84930-444">Der Azure App Service-Anbieter</span><span class="sxs-lookup"><span data-stu-id="84930-444">Azure App Service provider</span></span>

<span data-ttu-id="84930-445">Das Anbieterpaket [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) schreibt Protokolle in Textdateien in das Dateisystem einer Azure App Service-App und in [Blob Storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in einem Azure Storage-Konto.</span><span class="sxs-lookup"><span data-stu-id="84930-445">The [Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) provider package writes logs to text files in an Azure App Service app's file system and to [blob storage](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage) in an Azure Storage account.</span></span> <span data-ttu-id="84930-446">Das Anbieterpaket ist für Apps verfügbar, die für .NET Core 1.1 oder höher konzipiert sind.</span><span class="sxs-lookup"><span data-stu-id="84930-446">The provider package is available for apps targeting .NET Core 1.1 or later.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="84930-447">Wenn Sie Ihre App auf .NET Core ausrichten, beachten Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="84930-447">If targeting .NET Core, note the following points:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="84930-448">Das Anbieterpaket ist im ASP.NET Core-Metapaket [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) enthalten.</span><span class="sxs-lookup"><span data-stu-id="84930-448">The provider package is included in the ASP.NET Core [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="84930-449">Dieses Anbieterpaket ist nicht im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="84930-449">The provider package isn't included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="84930-450">Um den Anbieter zu verwenden, installieren Sie das Paket.</span><span class="sxs-lookup"><span data-stu-id="84930-450">To use the provider, install the package.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="84930-451">Rufen Sie <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> nicht explizit auf.</span><span class="sxs-lookup"><span data-stu-id="84930-451">Don't explicitly call <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>.</span></span> <span data-ttu-id="84930-452">Der Anbieter wird Ihrer App automatisch zur Verfügung gestellt, wenn diese in Azure App Service bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="84930-452">The provider is automatically made available to the app when the app is deployed to Azure App Service.</span></span>

<span data-ttu-id="84930-453">Wenn Sie Anwendungen für .NET Framework entwickeln oder auf das `Microsoft.AspNetCore.App`-Metapaket verweisen, fügen Sie dem Projekt das Anbieterpaket hinzu.</span><span class="sxs-lookup"><span data-stu-id="84930-453">If targeting .NET Framework or referencing the `Microsoft.AspNetCore.App` metapackage, add the provider package to the project.</span></span> <span data-ttu-id="84930-454">Rufen Sie `AddAzureWebAppDiagnostics` für eine <xref:Microsoft.Extensions.Logging.ILoggerFactory>-Instanz auf:</span><span class="sxs-lookup"><span data-stu-id="84930-454">Invoke `AddAzureWebAppDiagnostics` on an <xref:Microsoft.Extensions.Logging.ILoggerFactory> instance:</span></span>

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range="= aspnetcore-1.1"

```csharp
loggerFactory.AddAzureWebAppDiagnostics();
```

::: moniker-end

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="84930-455">Eine <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*>-Überladung ermöglicht das Übergeben von <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="84930-455">An <xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> overload lets you pass in <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span> <span data-ttu-id="84930-456">Das settings-Objekt kann Standardeinstellungen überschreiben, z.B. die Protokollierungsausgabevorlage, den Blobnamen und die maximale Dateigröße.</span><span class="sxs-lookup"><span data-stu-id="84930-456">The settings object can override default settings, such as the logging output template, blob name, and file size limit.</span></span> <span data-ttu-id="84930-457">(Die *Ausgabevorlage* ist eine Nachrichtenvorlage, die auf alle Protokolle zusätzlich zu dem angewendet wird, was mit einem `ILogger`-Methodenaufruf bereitgestellt wird.)</span><span class="sxs-lookup"><span data-stu-id="84930-457">(*Output template* is a message template that's applied to all logs in addition to what's provided with an `ILogger` method call.)</span></span>

<span data-ttu-id="84930-458">Wenn Sie eine Bereitstellung in einer App Service-App durchführen, berücksichtigt die Anwendung die Einstellungen im Abschnitt [Diagnoseprotokolle](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) der Seite **App Service** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="84930-458">When you deploy to an App Service app, the application honors the settings in the [Diagnostic Logs](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag) section of the **App Service** page of the Azure portal.</span></span> <span data-ttu-id="84930-459">Bei einem Update dieser Einstellungen werden die Änderungen sofort wirksam, ohne dass ein Neustart oder eine erneute Bereitstellung der App notwendig ist.</span><span class="sxs-lookup"><span data-stu-id="84930-459">When these settings are updated, the changes take effect immediately without requiring a restart or redeployment of the app.</span></span>

![Einstellungen für die Azure-Protokollierung](index/_static/azure-logging-settings.png)

<span data-ttu-id="84930-461">Der Standardspeicherort für Protokolldateien ist der Ordner *D:\\home\\LogFiles\\Application*, und der standardmäßige Dateiname lautet *diagnostics-yyyymmdd.txt*.</span><span class="sxs-lookup"><span data-stu-id="84930-461">The default location for log files is in the *D:\\home\\LogFiles\\Application* folder, and the default file name is *diagnostics-yyyymmdd.txt*.</span></span> <span data-ttu-id="84930-462">Die Dateigröße ist standardmäßig auf 10 MB beschränkt, und die maximal zulässige Anzahl beibehaltener Dateien lautet 2.</span><span class="sxs-lookup"><span data-stu-id="84930-462">The default file size limit is 10 MB, and the default maximum number of files retained is 2.</span></span> <span data-ttu-id="84930-463">Der Standardblobname lautet *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span><span class="sxs-lookup"><span data-stu-id="84930-463">The default blob name is *{app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt*.</span></span> <span data-ttu-id="84930-464">Weitere Informationen zum Standardverhalten finden Sie unter <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span><span class="sxs-lookup"><span data-stu-id="84930-464">For more information about default behavior, see <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>.</span></span>

<span data-ttu-id="84930-465">Der Anbieter funktioniert nur, wenn das Projekt in der Azure-Umgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="84930-465">The provider only works when the project runs in the Azure environment.</span></span> <span data-ttu-id="84930-466">Bei einer lokalen Ausführung zeigt er keine Auswirkungen. Der Anbieter schreibt keine Protokolle in lokale Dateien oder den lokalen Entwicklungsspeicher für BLOBs.</span><span class="sxs-lookup"><span data-stu-id="84930-466">It has no effect when the project is run locally&mdash;it doesn't write to local files or local development storage for blobs.</span></span>

::: moniker-end

### <a name="azure-log-streaming"></a><span data-ttu-id="84930-467">Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="84930-467">Azure log streaming</span></span>

<span data-ttu-id="84930-468">Azure-Protokollstreaming ermöglicht Ihnen eine Echtzeitanzeige der Protokollaktivität für:</span><span class="sxs-lookup"><span data-stu-id="84930-468">Azure log streaming lets you view log activity in real time from:</span></span>

* <span data-ttu-id="84930-469">App-Server</span><span class="sxs-lookup"><span data-stu-id="84930-469">The app server</span></span>
* <span data-ttu-id="84930-470">Webserver</span><span class="sxs-lookup"><span data-stu-id="84930-470">The web server</span></span>
* <span data-ttu-id="84930-471">Ablaufverfolgung für Anforderungsfehler</span><span class="sxs-lookup"><span data-stu-id="84930-471">Failed request tracing</span></span>

<span data-ttu-id="84930-472">So konfigurieren Sie das Azure-Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="84930-472">To configure Azure log streaming:</span></span>

* <span data-ttu-id="84930-473">Navigieren Sie von der Portalseite Ihrer App zur Seite **Diagnoseprotokolle**.</span><span class="sxs-lookup"><span data-stu-id="84930-473">Navigate to the **Diagnostics Logs** page from your app's portal page.</span></span>
* <span data-ttu-id="84930-474">Legen Sie **Anwendungsprotokollierung (Dateisystem)** auf **Ein** fest.</span><span class="sxs-lookup"><span data-stu-id="84930-474">Set **Application Logging (Filesystem)** to **On**.</span></span>

![Seite „Diagnoseprotokolle“ im Azure-Portal](index/_static/azure-diagnostic-logs.png)

<span data-ttu-id="84930-476">Navigieren Sie zur Seite **Protokollstreaming**, um App-Meldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="84930-476">Navigate to the **Log Streaming** page to view app messages.</span></span> <span data-ttu-id="84930-477">Diese werden von der App über die `ILogger`-Schnittstelle protokolliert.</span><span class="sxs-lookup"><span data-stu-id="84930-477">They're logged by the app through the `ILogger` interface.</span></span>

![Anwendungsprotokollstreaming im Azure-Portal](index/_static/azure-log-streaming.png)

::: moniker range=">= aspnetcore-1.1"

### <a name="azure-application-insights-trace-logging"></a><span data-ttu-id="84930-479">Ablaufverfolgungsprotokollierung für Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="84930-479">Azure Application Insights trace logging</span></span>

<span data-ttu-id="84930-480">Das Application Insights SDK kann Protokolle erfassen und melden, die von der ASP.NET Core-Protokollierungsinfrastruktur generiert wurden.</span><span class="sxs-lookup"><span data-stu-id="84930-480">The Application Insights SDK can collect and report logs generated by the ASP.NET Core logging infrastructure.</span></span> <span data-ttu-id="84930-481">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="84930-481">For more information, see the following resources:</span></span>

* [<span data-ttu-id="84930-482">Application Insights-Übersicht</span><span class="sxs-lookup"><span data-stu-id="84930-482">Application Insights overview</span></span>](/azure/application-insights/app-insights-overview)
* [<span data-ttu-id="84930-483">Application Insights für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="84930-483">Application Insights for ASP.NET Core</span></span>](/azure/application-insights/app-insights-asp-net-core)
* <span data-ttu-id="84930-484">[Application Insights logging adapters (Adapter zur Protokollierung für Application Insights)](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md).</span><span class="sxs-lookup"><span data-stu-id="84930-484">[Application Insights logging adapters](https://github.com/Microsoft/ApplicationInsights-dotnet-logging/blob/develop/README.md).</span></span>

::: moniker-end

## <a name="third-party-logging-providers"></a><span data-ttu-id="84930-485">Protokollierungsanbieter von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="84930-485">Third-party logging providers</span></span>

<span data-ttu-id="84930-486">Protokollierungsframeworks von Drittanbietern aufgeführt, die mit ASP.NET Core funktionieren:</span><span class="sxs-lookup"><span data-stu-id="84930-486">Third-party logging frameworks that work with ASP.NET Core:</span></span>

* <span data-ttu-id="84930-487">[elmah.io](https://elmah.io/) ([GitHub-Repository](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="84930-487">[elmah.io](https://elmah.io/) ([GitHub repo](https://github.com/elmahio/Elmah.Io.Extensions.Logging))</span></span>
* <span data-ttu-id="84930-488">[Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub-Repository](https://github.com/mattwcole/gelf-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="84930-488">[Gelf](http://docs.graylog.org/en/2.3/pages/gelf.html) ([GitHub repo](https://github.com/mattwcole/gelf-extensions-logging))</span></span>
* <span data-ttu-id="84930-489">[JSNLog](http://jsnlog.com/) ([GitHub-Repository](https://github.com/mperdeck/jsnlog))</span><span class="sxs-lookup"><span data-stu-id="84930-489">[JSNLog](http://jsnlog.com/) ([GitHub repo](https://github.com/mperdeck/jsnlog))</span></span>
* <span data-ttu-id="84930-490">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span><span class="sxs-lookup"><span data-stu-id="84930-490">[KissLog.net](https://kisslog.net/) ([GitHub repo](https://github.com/catalingavan/KissLog-net))</span></span>
* <span data-ttu-id="84930-491">[Loggr](http://loggr.net/) ([GitHub-Repository](https://github.com/imobile3/Loggr.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="84930-491">[Loggr](http://loggr.net/) ([GitHub repo](https://github.com/imobile3/Loggr.Extensions.Logging))</span></span>
* <span data-ttu-id="84930-492">[NLog](http://nlog-project.org/) ([GitHub-Repository](https://github.com/NLog/NLog.Extensions.Logging))</span><span class="sxs-lookup"><span data-stu-id="84930-492">[NLog](http://nlog-project.org/) ([GitHub repo](https://github.com/NLog/NLog.Extensions.Logging))</span></span>
* <span data-ttu-id="84930-493">[Sentry](https://sentry.io/welcome/) ([GitHub-Repository](https://github.com/getsentry/sentry-dotnet))</span><span class="sxs-lookup"><span data-stu-id="84930-493">[Sentry](https://sentry.io/welcome/) ([GitHub repo](https://github.com/getsentry/sentry-dotnet))</span></span>
* <span data-ttu-id="84930-494">[Serilog](https://serilog.net/) ([GitHub-Repository](https://github.com/serilog/serilog-extensions-logging))</span><span class="sxs-lookup"><span data-stu-id="84930-494">[Serilog](https://serilog.net/) ([GitHub repo](https://github.com/serilog/serilog-extensions-logging))</span></span>
* <span data-ttu-id="84930-495">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([GitHub-Repository](https://github.com/googleapis/google-cloud-dotnet))</span><span class="sxs-lookup"><span data-stu-id="84930-495">[Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging) ([Github repo](https://github.com/googleapis/google-cloud-dotnet))</span></span>

<span data-ttu-id="84930-496">Einige Drittanbieterframeworks können eine [semantische Protokollierung (auch als strukturierte Protokollierung bezeichnet)](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging) ausführen.</span><span class="sxs-lookup"><span data-stu-id="84930-496">Some third-party frameworks can perform [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="84930-497">Die Verwendung eines Frameworks von Drittanbietern ist ähnlich wie die Verwendung eines integrierten Anbieters:</span><span class="sxs-lookup"><span data-stu-id="84930-497">Using a third-party framework is similar to using one of the built-in providers:</span></span>

1. <span data-ttu-id="84930-498">Fügen Sie Ihrem Paket ein NuGet-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="84930-498">Add a NuGet package to your project.</span></span>
1. <span data-ttu-id="84930-499">Rufen Sie eine `ILoggerFactory` auf.</span><span class="sxs-lookup"><span data-stu-id="84930-499">Call an `ILoggerFactory`.</span></span>

<span data-ttu-id="84930-500">Weitere Informationen finden Sie in der Dokumentation zum jeweiligen Anbieter.</span><span class="sxs-lookup"><span data-stu-id="84930-500">For more information, see each provider's documentation.</span></span> <span data-ttu-id="84930-501">Protokollierungsanbieter von Drittanbietern werden von Microsoft nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="84930-501">Third-party logging providers aren't supported by Microsoft.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="84930-502">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="84930-502">Additional resources</span></span>

* <xref:fundamentals/logging/loggermessage>
