---
title: 'title: author: description: monikerRange: ms.author: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/07/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/change-tokens
ms.openlocfilehash: f581e26b0d11923d9cf099700c4338c99e68bb59
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106610"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="b02cf-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b02cf-103">'Identity'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b02cf-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b02cf-104">'Let's Encrypt'</span></span>

<span data-ttu-id="b02cf-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b02cf-105">'Razor'</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="b02cf-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="b02cf-106">'SignalR' uid:</span></span>

<span data-ttu-id="b02cf-107">Erkennen von Änderungen mit Änderungstoken in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b02cf-107">Detect changes with change tokens in ASP.NET Core</span></span> <span data-ttu-id="b02cf-108">Ein *Änderungstoken* ist ein universeller Baustein auf niedriger Ebene, mit dem Änderungen nachverfolgt werden können.</span><span class="sxs-lookup"><span data-stu-id="b02cf-108">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span> <span data-ttu-id="b02cf-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b02cf-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b02cf-110">IChangeToken-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="b02cf-110">IChangeToken interface</span></span>

* <span data-ttu-id="b02cf-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> verbreitet Benachrichtigungen, wenn eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="b02cf-111"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="b02cf-112">`IChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="b02cf-112">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="b02cf-113">Das [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/)-NuGet-Paket wird implizit für die ASP.NET Core-Apps bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b02cf-113">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>
* <span data-ttu-id="b02cf-114">`IChangeToken` verfügt über zwei Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="b02cf-114">`IChangeToken` has two properties:</span></span>

<span data-ttu-id="b02cf-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> geben an, ob das Token proaktiv Rückrufe auslöst.</span><span class="sxs-lookup"><span data-stu-id="b02cf-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="b02cf-116">Wenn `ActiveChangedCallbacks` auf `false` festgelegt ist, wird ein Rückruf nie aufgerufen, und die Anwendung muss `HasChanged` für Änderungen abrufen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-116">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="b02cf-117">Es ist auch möglich, dass ein Token nie abgebrochen wird, wenn keine Änderungen auftreten oder der zugrunde liegenden Änderungslistener gelöscht oder deaktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-117">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>

<span data-ttu-id="b02cf-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> ruft einen Wert auf, der angibt, ob eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="b02cf-118"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span> <span data-ttu-id="b02cf-119">Die `IChangeToken`-Schnittstelle verfügt über die Methode [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*). Diese registriert einen Rückruf, der bei einer Tokenänderung aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-119">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="b02cf-120">`HasChanged` muss festgelegt werden, bevor der Rückruf aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-120">`HasChanged` must be set before the callback is invoked.</span></span>

<span data-ttu-id="b02cf-121">ChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="b02cf-121">ChangeToken class</span></span>

* <span data-ttu-id="b02cf-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> ist eine statische Klasse, die zur Verbreitung von Änderungsbenachrichtigungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-122"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span>
* <span data-ttu-id="b02cf-123">`ChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="b02cf-123">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="b02cf-124">Das [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/)-NuGet-Paket wird implizit für die ASP.NET Core-Apps bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b02cf-124">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="b02cf-125">Die Methode [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registriert eine bei einer Tokenänderung aufzurufende `Action`:</span><span class="sxs-lookup"><span data-stu-id="b02cf-125">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span> <span data-ttu-id="b02cf-126">`Func<IChangeToken>` erzeugt das Token.</span><span class="sxs-lookup"><span data-stu-id="b02cf-126">`Func<IChangeToken>` produces the token.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="b02cf-127">`Action` wird aufgerufen, wenn das Token geändert wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-127">`Action` is called when the token changes.</span></span>

<span data-ttu-id="b02cf-128">Die Überladung [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) akzeptiert einen zusätzlichen `TState`-Parameter, der an die Tokenconsumer-`Action` übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-128">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

* <span data-ttu-id="b02cf-129"><xref:System.IDisposable> gibt `OnChange` zurück.</span><span class="sxs-lookup"><span data-stu-id="b02cf-129">`OnChange` returns an <xref:System.IDisposable>.</span></span>
* <span data-ttu-id="b02cf-130">Der Aufruf von <xref:System.IDisposable.Dispose*> beendet den Überwachungsvorgang des Tokens auf weitere Änderungen und gibt die Tokenressourcen frei.</span><span class="sxs-lookup"><span data-stu-id="b02cf-130">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>
* <span data-ttu-id="b02cf-131">Beispielverwendungen von Änderungstoken in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b02cf-131">Example uses of change tokens in ASP.NET Core</span></span> <span data-ttu-id="b02cf-132">Änderungstoken werden in wichtigen Bereichen von ASP.NET Core verwendet, um auf Objektänderungen zu überwachen:</span><span class="sxs-lookup"><span data-stu-id="b02cf-132">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="b02cf-133">Für die Überwachung von Änderungen an Dateien erstellt die <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*>-Methode von <xref:Microsoft.Extensions.FileProviders.IFileProvider> ein `IChangeToken` für die angegebenen Dateien oder einen Ordner, der überwacht werden soll.</span><span class="sxs-lookup"><span data-stu-id="b02cf-133">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>

<span data-ttu-id="b02cf-134">`IChangeToken`-Token können zu Cacheeinträgen hinzugefügt werden, um bei Änderungen Cacheentfernungen auszulösen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-134">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>

<span data-ttu-id="b02cf-135">Für `TOptions`-Änderungen umfasst die <xref:Microsoft.Extensions.Options.OptionsMonitor`1>-Standardimplementierung von <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> eine Überladung, die eine oder mehrere <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>-Instanzen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-135">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="b02cf-136">Jede Instanz gibt ein `IChangeToken` zurück, um den Rückruf einer Änderungsbenachrichtigung zur Nachverfolgung von Optionsänderungen zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="b02cf-136">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span> <span data-ttu-id="b02cf-137">Überwachen von Konfigurationsänderungen</span><span class="sxs-lookup"><span data-stu-id="b02cf-137">Monitor for configuration changes</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="b02cf-138">ASP.NET Core-Vorlagen verwenden standardmäßig [JSON-Konfigurationsdateien](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* und *appsettings.Production.json*), um Konfigurationseinstellungen der Anwendungen zu laden.</span><span class="sxs-lookup"><span data-stu-id="b02cf-138">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span> <span data-ttu-id="b02cf-139">Diese Dateien werden mithilfe der [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*)-Erweiterungsmethode für <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> konfiguriert, die einen `reloadOnChange`-Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-139">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span>

<span data-ttu-id="b02cf-140">`reloadOnChange` gibt an, ob Konfigurationen auf Dateiänderungen neu geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="b02cf-140">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span>

<span data-ttu-id="b02cf-141">Diese Einstellung wird in der <xref:Microsoft.Extensions.Hosting.Host>-Hilfsmethode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> verwendet:</span><span class="sxs-lookup"><span data-stu-id="b02cf-141">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span> <span data-ttu-id="b02cf-142">Die dateibasierte Konfiguration wird durch <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> repräsentiert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-142">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span>

<span data-ttu-id="b02cf-143">`FileConfigurationSource` verwendet <xref:Microsoft.Extensions.FileProviders.IFileProvider> zum Überwachen von Dateien.</span><span class="sxs-lookup"><span data-stu-id="b02cf-143">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span> <span data-ttu-id="b02cf-144">`IFileMonitor` wird standardmäßig von einem <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> bereitgestellt, der zum Überwachen auf Konfigurationsdateiänderungen <xref:System.IO.FileSystemWatcher> verwendet.</span><span class="sxs-lookup"><span data-stu-id="b02cf-144">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span> <span data-ttu-id="b02cf-145">Die Beispielanwendung veranschaulicht zwei Implementierungen für die Überwachung von Konfigurationsänderungen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-145">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="b02cf-146">Wenn sich eine beliebige der *appsettings*-Dateien ändert, führen beide Dateiüberwachungsimplementierungen benutzerdefinierten Code aus – die Beispiel-App gibt eine Meldung an die Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="b02cf-146">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span> <span data-ttu-id="b02cf-147">Der `FileSystemWatcher` einer Konfigurationsdatei kann mehrere Tokenrückrufe für eine einzelne Dateikonfigurationsänderung auslösen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-147">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span>

<span data-ttu-id="b02cf-148">Um sicherzustellen, dass der benutzerdefinierte Code bei Auslösung mehrerer Tokenrückrufe nur einmal ausgeführt wird, überprüft die Beispielimplementierung Dateihashes.</span><span class="sxs-lookup"><span data-stu-id="b02cf-148">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="b02cf-149">Das Beispiel verwendet SHA1-Dateihashing.</span><span class="sxs-lookup"><span data-stu-id="b02cf-149">The sample uses SHA1 file hashing.</span></span>

<span data-ttu-id="b02cf-150">Eine Wiederholung wird mit einem exponentiellen Backoff implementiert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-150">A retry is implemented with an exponential back-off.</span></span>

<span data-ttu-id="b02cf-151">Die Wiederholung ist vorhanden, weil Dateisperren auftreten können, die vorübergehend das Berechnen eines neuen Hashs für eine der Dateien verhindern.</span><span class="sxs-lookup"><span data-stu-id="b02cf-151">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="b02cf-152">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="b02cf-152">*Utilities/Utilities.cs*:</span></span> <span data-ttu-id="b02cf-153">Einfaches Starten von Änderungstoken</span><span class="sxs-lookup"><span data-stu-id="b02cf-153">Simple startup change token</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="b02cf-154">Registrieren Sie einen `Action`-Rückruf eines Tokenconsumers für Änderungsbenachrichtigungen an das Token zum Neuladen der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="b02cf-154">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span> <span data-ttu-id="b02cf-155">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b02cf-155">In `Startup.Configure`:</span></span>

<span data-ttu-id="b02cf-156">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="b02cf-156">`config.GetReloadToken()` provides the token.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="b02cf-157">Der Rückruf ist die `InvokeChanged`-Methode:</span><span class="sxs-lookup"><span data-stu-id="b02cf-157">The callback is the `InvokeChanged` method:</span></span>

<span data-ttu-id="b02cf-158">Der `state` des Rückrufs wird zur Übergabe an die `IWebHostEnvironment` verwendet. Dies ist hilfreich, um die richtige *appsettings*-Konfigurationsdatei anzugeben, die überwacht werden soll (in der Entwicklungsumgebung beispielsweise *appsettings.Development.json*).</span><span class="sxs-lookup"><span data-stu-id="b02cf-158">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span>

* <span data-ttu-id="b02cf-159">Dateihashes werden verwendet, um zu verhindern, dass die `WriteConsole`-Anweisung mehrere Male ausgeführt wird. Dies liegt an mehreren Tokenrückrufen, wenn die Konfigurationsdatei nur einmal geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="b02cf-159">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>
* <span data-ttu-id="b02cf-160">Dieses System wird so lange ausgeführt, wie die Anwendung ausgeführt wird. Es kann nicht vom Benutzer deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="b02cf-160">This system runs as long as the app is running and can't be disabled by the user.</span></span>
* <span data-ttu-id="b02cf-161">Überwachen von Konfigurationsänderungen als Dienst</span><span class="sxs-lookup"><span data-stu-id="b02cf-161">Monitor configuration changes as a service</span></span>

<span data-ttu-id="b02cf-162">Das Beispiel implementiert:</span><span class="sxs-lookup"><span data-stu-id="b02cf-162">The sample implements:</span></span>

<span data-ttu-id="b02cf-163">Grundlegendes Überwachen des Starttokens.</span><span class="sxs-lookup"><span data-stu-id="b02cf-163">Basic startup token monitoring.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="b02cf-164">Überwachung als Dienst.</span><span class="sxs-lookup"><span data-stu-id="b02cf-164">Monitoring as a service.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="b02cf-165">Ein Mechanismus zum Aktivieren und Deaktivieren des Monitoring.</span><span class="sxs-lookup"><span data-stu-id="b02cf-165">A mechanism to enable and disable monitoring.</span></span> <span data-ttu-id="b02cf-166">In diesem Beispiel wird eine `IConfigurationMonitor`-Schnittstelle eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="b02cf-166">The sample establishes an `IConfigurationMonitor` interface.</span></span> <span data-ttu-id="b02cf-167">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="b02cf-167">*Extensions/ConfigurationMonitor.cs*:</span></span> <span data-ttu-id="b02cf-168">Der Konstruktor der implementierten `ConfigurationMonitor`-Klasse registriert einen Rückruf für Änderungsbenachrichtigungen:</span><span class="sxs-lookup"><span data-stu-id="b02cf-168">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

* <span data-ttu-id="b02cf-169">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="b02cf-169">`config.GetReloadToken()` supplies the token.</span></span>
* <span data-ttu-id="b02cf-170">`InvokeChanged` ist die Rückrufmethode.</span><span class="sxs-lookup"><span data-stu-id="b02cf-170">`InvokeChanged` is the callback method.</span></span>

<span data-ttu-id="b02cf-171">In dieser Instanz stellt `state` einen Verweis auf die `IConfigurationMonitor`-Instanz dar, die zum Zugriff auf den Überwachungsstatus verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-171">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span>

* <span data-ttu-id="b02cf-172">Es werden zwei Eigenschaften verwendet:</span><span class="sxs-lookup"><span data-stu-id="b02cf-172">Two properties are used:</span></span>
* <span data-ttu-id="b02cf-173">`MonitoringEnabled`: Diese Eigenschaft gibt an, ob der Rückruf den zugehörigen benutzerdefinierten Code ausführen soll.</span><span class="sxs-lookup"><span data-stu-id="b02cf-173">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="b02cf-174">`CurrentState`: Diese Eigenschaft beschreibt den aktuellen Überwachungsstatus zur Verwendung in der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="b02cf-174">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="b02cf-175">Die `InvokeChanged`-Methode ist vergleichbar mit dem früheren Ansatz, außer dass sie:</span><span class="sxs-lookup"><span data-stu-id="b02cf-175">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span> <span data-ttu-id="b02cf-176">Den Code nicht ausführt, es sei denn, `MonitoringEnabled` ist `true`.</span><span class="sxs-lookup"><span data-stu-id="b02cf-176">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>

<span data-ttu-id="b02cf-177">Den aktuellen `state` in der zugehörigen `WriteConsole`-Ausgabe berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="b02cf-177">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="b02cf-178">Eine `ConfigurationMonitor`-Instanz wird in `Startup.ConfigureServices` als Dienst registriert:</span><span class="sxs-lookup"><span data-stu-id="b02cf-178">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="b02cf-179">Die Indexseite bietet das Benutzer Kontrolle über die Konfigurationsüberwachung.</span><span class="sxs-lookup"><span data-stu-id="b02cf-179">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="b02cf-180">Die `IConfigurationMonitor`-Instanz wird in das `IndexModel` eingefügt.</span><span class="sxs-lookup"><span data-stu-id="b02cf-180">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="b02cf-181">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b02cf-181">*Pages/Index.cshtml.cs*:</span></span>

<span data-ttu-id="b02cf-182">Der Konfigurationsmonitor (`_monitor`) wird verwendet, um die Überwachung zu aktivieren oder zu deaktivieren und den aktuellen Status für Benutzeroberflächenfeedback festzulegen:</span><span class="sxs-lookup"><span data-stu-id="b02cf-182">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="b02cf-183">Wenn `OnPostStartMonitoring` ausgelöst wird, wird die Überwachung aktiviert, und der aktuelle Status ist deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-183">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span>

<span data-ttu-id="b02cf-184">Wenn `OnPostStopMonitoring` ausgelöst wird, wird Überwachung deaktiviert, und der Zustand zeigt, dass die Überwachung nicht ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-184">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span> <span data-ttu-id="b02cf-185">Die Überwachung wird über Schaltflächen in der Benutzeroberfläche aktiviert und deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-185">Buttons in the UI enable and disable monitoring.</span></span> <span data-ttu-id="b02cf-186">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b02cf-186">*Pages/Index.cshtml*:</span></span>

<span data-ttu-id="b02cf-187">Überwachen von zwischengespeicherten Dateiänderungen</span><span class="sxs-lookup"><span data-stu-id="b02cf-187">Monitor cached file changes</span></span> <span data-ttu-id="b02cf-188">Dateiinhalt kann mithilfe von <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> speicherintern zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="b02cf-188">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="b02cf-189">Zwischenspeicherung im Arbeitsspeicher wird im Thema [Cache in-memory (Zwischenspeicherung im Arbeitsspeicher)](xref:performance/caching/memory) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="b02cf-189">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span>

<span data-ttu-id="b02cf-190">Ohne zusätzliche Schritte, wie der unten beschriebenen Implementierung, werden *veraltete* Daten aus einem Zwischenspeicher zurückgegeben, wenn sich die Quelldaten ändern.</span><span class="sxs-lookup"><span data-stu-id="b02cf-190">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span> <span data-ttu-id="b02cf-191">Wird beispielsweise der Status einer zwischengespeicherten Quelldatei beim Erneuern eines [variablen Ablaufzeitraums](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) nicht berücksichtigt, führt dies zu veralteten Dateidaten im Cache.</span><span class="sxs-lookup"><span data-stu-id="b02cf-191">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span>

<span data-ttu-id="b02cf-192">Jede Datenanforderung erneuert den variablen Ablaufzeitraum, aber die Datei wird nie neu in den Zwischenspeicher geladen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-192">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span>

* <span data-ttu-id="b02cf-193">Alle Anwendungsfeatures, die den zwischengespeicherten Inhalt der Datei verwenden, erhalten möglicherweise veraltete Inhalte.</span><span class="sxs-lookup"><span data-stu-id="b02cf-193">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>
* <span data-ttu-id="b02cf-194">Durch die Verwendung von Änderungstoken in einem Szenario mit Dateizwischenspeicherung wird verhindert, dass veraltete Dateiinhalte im Zwischenspeicher auftreten.</span><span class="sxs-lookup"><span data-stu-id="b02cf-194">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span>

<span data-ttu-id="b02cf-195">Die Beispielanwendung zeigt eine Implementierung des Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="b02cf-195">The sample app demonstrates an implementation of the approach.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="b02cf-196">Das Beispiel verwendet `GetFileContent`, um:</span><span class="sxs-lookup"><span data-stu-id="b02cf-196">The sample uses `GetFileContent` to:</span></span> <span data-ttu-id="b02cf-197">Dateiinhalt zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="b02cf-197">Return file content.</span></span>

<span data-ttu-id="b02cf-198">Einen Wiederholungsalgorithmus mit exponentiellem Backoff zu implementieren, um Fälle abzudecken, in denen eine Dateisperre vorübergehend das Lesen einer Datei verhindert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-198">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

1. <span data-ttu-id="b02cf-199">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="b02cf-199">*Utilities/Utilities.cs*:</span></span>
1. <span data-ttu-id="b02cf-200">Ein `FileService` wird erstellt, um die zwischengespeicherten Dateisuchvorgänge zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="b02cf-200">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="b02cf-201">Der `GetFileContent`-Methodenaufruf des Diensts versucht, den Dateiinhalt aus dem speicherinternen Cache abzurufen und ihn an den Aufrufer zurückzugeben (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="b02cf-201">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>
1. <span data-ttu-id="b02cf-202">Wenn mithilfe des Cacheschlüssels zwischengespeicherte Inhalte nicht gefunden werden, werden folgende Aktionen durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="b02cf-202">If cached content isn't found using the cache key, the following actions are taken:</span></span> <span data-ttu-id="b02cf-203">Der Inhalt der Datei wird mit `GetFileContent` abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-203">The file content is obtained using `GetFileContent`.</span></span>

<span data-ttu-id="b02cf-204">Ein Änderungstoken wird mithilfe von [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) aus dem Dateianbieter abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-204">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="b02cf-205">Wenn die Datei geändert wird, wird ein Rückruf des Tokens ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="b02cf-205">The token's callback is triggered when the file is modified.</span></span> <span data-ttu-id="b02cf-206">Der Inhalt der Datei wird mit einem [variablen Ablaufzeitraum](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-206">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="b02cf-207">Das Änderungstoken wird mit [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) angefügt, um den Cacheeintrag zu entfernen, wenn die sich Datei ändert, während sie zwischengespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-207">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="b02cf-208">Im folgenden Beispiel werden Dateien im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App gespeichert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-208">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="b02cf-209">`IWebHostEnvironment.ContentRootFileProvider` wird verwendet, um einen <xref:Microsoft.Extensions.FileProviders.IFileProvider> zu erhalten, der auf den `IWebHostEnvironment.ContentRootPath` der App verweist.</span><span class="sxs-lookup"><span data-stu-id="b02cf-209">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span>

<span data-ttu-id="b02cf-210">Der `filePath` wird mit [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-210">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="b02cf-211">Der `FileService` ist zusammen mit dem Speichercachedienst im Dienstcontainer registriert:</span><span class="sxs-lookup"><span data-stu-id="b02cf-211">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="b02cf-212">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b02cf-212">In `Startup.ConfigureServices`:</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="b02cf-213">Das Seitenmodell lädt mithilfe des Diensts den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="b02cf-213">The page model loads the file's content using the service.</span></span> <span data-ttu-id="b02cf-214">In der `OnGet`-Methode auf der Indexseite (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b02cf-214">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span> <span data-ttu-id="b02cf-215">CompositeChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="b02cf-215">CompositeChangeToken class</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b02cf-216">Verwenden Sie für die Darstellung von einer oder mehreren `IChangeToken`-Instanzen in einem einzelnen Objekt die Klasse <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="b02cf-216">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

<span data-ttu-id="b02cf-217">`HasChanged` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `HasChanged`-Token `true` ist.</span><span class="sxs-lookup"><span data-stu-id="b02cf-217">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="b02cf-218">`ActiveChangeCallbacks` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `ActiveChangeCallbacks`-Token `true` ist.</span><span class="sxs-lookup"><span data-stu-id="b02cf-218">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span>

<span data-ttu-id="b02cf-219">Wenn mehrere Änderungsereignisse gleichzeitig auftreten, wird der Rückruf für die kombinierte Änderung einmal aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-219">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span> <span data-ttu-id="b02cf-220">Ein *Änderungstoken* ist ein universeller Baustein auf niedriger Ebene, mit dem Änderungen nachverfolgt werden können.</span><span class="sxs-lookup"><span data-stu-id="b02cf-220">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span> <span data-ttu-id="b02cf-221">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b02cf-221">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b02cf-222">IChangeToken-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="b02cf-222">IChangeToken interface</span></span>

* <span data-ttu-id="b02cf-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> verbreitet Benachrichtigungen, wenn eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="b02cf-223"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="b02cf-224">`IChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="b02cf-224">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="b02cf-225">Für Anwendungen, die das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nicht verwenden, erstellen Sie einen Paketverweis auf das NuGet-Paket [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="b02cf-225">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>
* <span data-ttu-id="b02cf-226">`IChangeToken` verfügt über zwei Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="b02cf-226">`IChangeToken` has two properties:</span></span>

<span data-ttu-id="b02cf-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> geben an, ob das Token proaktiv Rückrufe auslöst.</span><span class="sxs-lookup"><span data-stu-id="b02cf-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="b02cf-228">Wenn `ActiveChangedCallbacks` auf `false` festgelegt ist, wird ein Rückruf nie aufgerufen, und die Anwendung muss `HasChanged` für Änderungen abrufen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-228">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="b02cf-229">Es ist auch möglich, dass ein Token nie abgebrochen wird, wenn keine Änderungen auftreten oder der zugrunde liegenden Änderungslistener gelöscht oder deaktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-229">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>

<span data-ttu-id="b02cf-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> ruft einen Wert auf, der angibt, ob eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="b02cf-230"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span> <span data-ttu-id="b02cf-231">Die `IChangeToken`-Schnittstelle verfügt über die Methode [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*). Diese registriert einen Rückruf, der bei einer Tokenänderung aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-231">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="b02cf-232">`HasChanged` muss festgelegt werden, bevor der Rückruf aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-232">`HasChanged` must be set before the callback is invoked.</span></span>

<span data-ttu-id="b02cf-233">ChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="b02cf-233">ChangeToken class</span></span>

* <span data-ttu-id="b02cf-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> ist eine statische Klasse, die zur Verbreitung von Änderungsbenachrichtigungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-234"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span>
* <span data-ttu-id="b02cf-235">`ChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="b02cf-235">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="b02cf-236">Für Anwendungen, die das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nicht verwenden, erstellen Sie einen Paketverweis auf das NuGet-Paket [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="b02cf-236">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="b02cf-237">Die Methode [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registriert eine bei einer Tokenänderung aufzurufende `Action`:</span><span class="sxs-lookup"><span data-stu-id="b02cf-237">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span> <span data-ttu-id="b02cf-238">`Func<IChangeToken>` erzeugt das Token.</span><span class="sxs-lookup"><span data-stu-id="b02cf-238">`Func<IChangeToken>` produces the token.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="b02cf-239">`Action` wird aufgerufen, wenn das Token geändert wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-239">`Action` is called when the token changes.</span></span>

<span data-ttu-id="b02cf-240">Die Überladung [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) akzeptiert einen zusätzlichen `TState`-Parameter, der an die Tokenconsumer-`Action` übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-240">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

* <span data-ttu-id="b02cf-241"><xref:System.IDisposable> gibt `OnChange` zurück.</span><span class="sxs-lookup"><span data-stu-id="b02cf-241">`OnChange` returns an <xref:System.IDisposable>.</span></span>
* <span data-ttu-id="b02cf-242">Der Aufruf von <xref:System.IDisposable.Dispose*> beendet den Überwachungsvorgang des Tokens auf weitere Änderungen und gibt die Tokenressourcen frei.</span><span class="sxs-lookup"><span data-stu-id="b02cf-242">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>
* <span data-ttu-id="b02cf-243">Beispielverwendungen von Änderungstoken in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b02cf-243">Example uses of change tokens in ASP.NET Core</span></span> <span data-ttu-id="b02cf-244">Änderungstoken werden in wichtigen Bereichen von ASP.NET Core verwendet, um auf Objektänderungen zu überwachen:</span><span class="sxs-lookup"><span data-stu-id="b02cf-244">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="b02cf-245">Für die Überwachung von Änderungen an Dateien erstellt die <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*>-Methode von <xref:Microsoft.Extensions.FileProviders.IFileProvider> ein `IChangeToken` für die angegebenen Dateien oder einen Ordner, der überwacht werden soll.</span><span class="sxs-lookup"><span data-stu-id="b02cf-245">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>

<span data-ttu-id="b02cf-246">`IChangeToken`-Token können zu Cacheeinträgen hinzugefügt werden, um bei Änderungen Cacheentfernungen auszulösen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-246">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>

<span data-ttu-id="b02cf-247">Für `TOptions`-Änderungen umfasst die <xref:Microsoft.Extensions.Options.OptionsMonitor`1>-Standardimplementierung von <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> eine Überladung, die eine oder mehrere <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>-Instanzen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-247">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="b02cf-248">Jede Instanz gibt ein `IChangeToken` zurück, um den Rückruf einer Änderungsbenachrichtigung zur Nachverfolgung von Optionsänderungen zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="b02cf-248">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span> <span data-ttu-id="b02cf-249">Überwachen von Konfigurationsänderungen</span><span class="sxs-lookup"><span data-stu-id="b02cf-249">Monitor for configuration changes</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="b02cf-250">ASP.NET Core-Vorlagen verwenden standardmäßig [JSON-Konfigurationsdateien](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* und *appsettings.Production.json*), um Konfigurationseinstellungen der Anwendungen zu laden.</span><span class="sxs-lookup"><span data-stu-id="b02cf-250">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span> <span data-ttu-id="b02cf-251">Diese Dateien werden mithilfe der [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*)-Erweiterungsmethode für <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> konfiguriert, die einen `reloadOnChange`-Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-251">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span>

<span data-ttu-id="b02cf-252">`reloadOnChange` gibt an, ob Konfigurationen auf Dateiänderungen neu geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="b02cf-252">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span>

<span data-ttu-id="b02cf-253">Diese Einstellung wird in der <xref:Microsoft.AspNetCore.WebHost>-Hilfsmethode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> verwendet:</span><span class="sxs-lookup"><span data-stu-id="b02cf-253">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span> <span data-ttu-id="b02cf-254">Die dateibasierte Konfiguration wird durch <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> repräsentiert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-254">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span>

<span data-ttu-id="b02cf-255">`FileConfigurationSource` verwendet <xref:Microsoft.Extensions.FileProviders.IFileProvider> zum Überwachen von Dateien.</span><span class="sxs-lookup"><span data-stu-id="b02cf-255">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span> <span data-ttu-id="b02cf-256">`IFileMonitor` wird standardmäßig von einem <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> bereitgestellt, der zum Überwachen auf Konfigurationsdateiänderungen <xref:System.IO.FileSystemWatcher> verwendet.</span><span class="sxs-lookup"><span data-stu-id="b02cf-256">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span> <span data-ttu-id="b02cf-257">Die Beispielanwendung veranschaulicht zwei Implementierungen für die Überwachung von Konfigurationsänderungen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-257">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="b02cf-258">Wenn sich eine beliebige der *appsettings*-Dateien ändert, führen beide Dateiüberwachungsimplementierungen benutzerdefinierten Code aus – die Beispiel-App gibt eine Meldung an die Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="b02cf-258">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span> <span data-ttu-id="b02cf-259">Der `FileSystemWatcher` einer Konfigurationsdatei kann mehrere Tokenrückrufe für eine einzelne Dateikonfigurationsänderung auslösen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-259">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span>

<span data-ttu-id="b02cf-260">Um sicherzustellen, dass der benutzerdefinierte Code bei Auslösung mehrerer Tokenrückrufe nur einmal ausgeführt wird, überprüft die Beispielimplementierung Dateihashes.</span><span class="sxs-lookup"><span data-stu-id="b02cf-260">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="b02cf-261">Das Beispiel verwendet SHA1-Dateihashing.</span><span class="sxs-lookup"><span data-stu-id="b02cf-261">The sample uses SHA1 file hashing.</span></span>

<span data-ttu-id="b02cf-262">Eine Wiederholung wird mit einem exponentiellen Backoff implementiert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-262">A retry is implemented with an exponential back-off.</span></span>

<span data-ttu-id="b02cf-263">Die Wiederholung ist vorhanden, weil Dateisperren auftreten können, die vorübergehend das Berechnen eines neuen Hashs für eine der Dateien verhindern.</span><span class="sxs-lookup"><span data-stu-id="b02cf-263">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="b02cf-264">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="b02cf-264">*Utilities/Utilities.cs*:</span></span> <span data-ttu-id="b02cf-265">Einfaches Starten von Änderungstoken</span><span class="sxs-lookup"><span data-stu-id="b02cf-265">Simple startup change token</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="b02cf-266">Registrieren Sie einen `Action`-Rückruf eines Tokenconsumers für Änderungsbenachrichtigungen an das Token zum Neuladen der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="b02cf-266">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span> <span data-ttu-id="b02cf-267">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="b02cf-267">In `Startup.Configure`:</span></span>

<span data-ttu-id="b02cf-268">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="b02cf-268">`config.GetReloadToken()` provides the token.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="b02cf-269">Der Rückruf ist die `InvokeChanged`-Methode:</span><span class="sxs-lookup"><span data-stu-id="b02cf-269">The callback is the `InvokeChanged` method:</span></span>

<span data-ttu-id="b02cf-270">Der `state` des Rückrufs wird zur Übergabe an die `IHostingEnvironment` verwendet. Dies ist hilfreich, um die richtige *appsettings*-Konfigurationsdatei anzugeben, die überwacht werden soll (in der Entwicklungsumgebung beispielsweise *appsettings.Development.json*).</span><span class="sxs-lookup"><span data-stu-id="b02cf-270">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span>

* <span data-ttu-id="b02cf-271">Dateihashes werden verwendet, um zu verhindern, dass die `WriteConsole`-Anweisung mehrere Male ausgeführt wird. Dies liegt an mehreren Tokenrückrufen, wenn die Konfigurationsdatei nur einmal geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="b02cf-271">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>
* <span data-ttu-id="b02cf-272">Dieses System wird so lange ausgeführt, wie die Anwendung ausgeführt wird. Es kann nicht vom Benutzer deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="b02cf-272">This system runs as long as the app is running and can't be disabled by the user.</span></span>
* <span data-ttu-id="b02cf-273">Überwachen von Konfigurationsänderungen als Dienst</span><span class="sxs-lookup"><span data-stu-id="b02cf-273">Monitor configuration changes as a service</span></span>

<span data-ttu-id="b02cf-274">Das Beispiel implementiert:</span><span class="sxs-lookup"><span data-stu-id="b02cf-274">The sample implements:</span></span>

<span data-ttu-id="b02cf-275">Grundlegendes Überwachen des Starttokens.</span><span class="sxs-lookup"><span data-stu-id="b02cf-275">Basic startup token monitoring.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="b02cf-276">Überwachung als Dienst.</span><span class="sxs-lookup"><span data-stu-id="b02cf-276">Monitoring as a service.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="b02cf-277">Ein Mechanismus zum Aktivieren und Deaktivieren des Monitoring.</span><span class="sxs-lookup"><span data-stu-id="b02cf-277">A mechanism to enable and disable monitoring.</span></span> <span data-ttu-id="b02cf-278">In diesem Beispiel wird eine `IConfigurationMonitor`-Schnittstelle eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="b02cf-278">The sample establishes an `IConfigurationMonitor` interface.</span></span> <span data-ttu-id="b02cf-279">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="b02cf-279">*Extensions/ConfigurationMonitor.cs*:</span></span> <span data-ttu-id="b02cf-280">Der Konstruktor der implementierten `ConfigurationMonitor`-Klasse registriert einen Rückruf für Änderungsbenachrichtigungen:</span><span class="sxs-lookup"><span data-stu-id="b02cf-280">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

* <span data-ttu-id="b02cf-281">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="b02cf-281">`config.GetReloadToken()` supplies the token.</span></span>
* <span data-ttu-id="b02cf-282">`InvokeChanged` ist die Rückrufmethode.</span><span class="sxs-lookup"><span data-stu-id="b02cf-282">`InvokeChanged` is the callback method.</span></span>

<span data-ttu-id="b02cf-283">In dieser Instanz stellt `state` einen Verweis auf die `IConfigurationMonitor`-Instanz dar, die zum Zugriff auf den Überwachungsstatus verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-283">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span>

* <span data-ttu-id="b02cf-284">Es werden zwei Eigenschaften verwendet:</span><span class="sxs-lookup"><span data-stu-id="b02cf-284">Two properties are used:</span></span>
* <span data-ttu-id="b02cf-285">`MonitoringEnabled`: Diese Eigenschaft gibt an, ob der Rückruf den zugehörigen benutzerdefinierten Code ausführen soll.</span><span class="sxs-lookup"><span data-stu-id="b02cf-285">`MonitoringEnabled`: Indicates if the callback should run its custom code.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="b02cf-286">`CurrentState`: Diese Eigenschaft beschreibt den aktuellen Überwachungsstatus zur Verwendung in der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="b02cf-286">`CurrentState`: Describes the current monitoring state for use in the UI.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="b02cf-287">Die `InvokeChanged`-Methode ist vergleichbar mit dem früheren Ansatz, außer dass sie:</span><span class="sxs-lookup"><span data-stu-id="b02cf-287">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span> <span data-ttu-id="b02cf-288">Den Code nicht ausführt, es sei denn, `MonitoringEnabled` ist `true`.</span><span class="sxs-lookup"><span data-stu-id="b02cf-288">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>

<span data-ttu-id="b02cf-289">Den aktuellen `state` in der zugehörigen `WriteConsole`-Ausgabe berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="b02cf-289">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="b02cf-290">Eine `ConfigurationMonitor`-Instanz wird in `Startup.ConfigureServices` als Dienst registriert:</span><span class="sxs-lookup"><span data-stu-id="b02cf-290">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="b02cf-291">Die Indexseite bietet das Benutzer Kontrolle über die Konfigurationsüberwachung.</span><span class="sxs-lookup"><span data-stu-id="b02cf-291">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="b02cf-292">Die `IConfigurationMonitor`-Instanz wird in das `IndexModel` eingefügt.</span><span class="sxs-lookup"><span data-stu-id="b02cf-292">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="b02cf-293">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="b02cf-293">*Pages/Index.cshtml.cs*:</span></span>

<span data-ttu-id="b02cf-294">Der Konfigurationsmonitor (`_monitor`) wird verwendet, um die Überwachung zu aktivieren oder zu deaktivieren und den aktuellen Status für Benutzeroberflächenfeedback festzulegen:</span><span class="sxs-lookup"><span data-stu-id="b02cf-294">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="b02cf-295">Wenn `OnPostStartMonitoring` ausgelöst wird, wird die Überwachung aktiviert, und der aktuelle Status ist deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-295">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span>

<span data-ttu-id="b02cf-296">Wenn `OnPostStopMonitoring` ausgelöst wird, wird Überwachung deaktiviert, und der Zustand zeigt, dass die Überwachung nicht ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-296">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span> <span data-ttu-id="b02cf-297">Die Überwachung wird über Schaltflächen in der Benutzeroberfläche aktiviert und deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-297">Buttons in the UI enable and disable monitoring.</span></span> <span data-ttu-id="b02cf-298">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="b02cf-298">*Pages/Index.cshtml*:</span></span>

<span data-ttu-id="b02cf-299">Überwachen von zwischengespeicherten Dateiänderungen</span><span class="sxs-lookup"><span data-stu-id="b02cf-299">Monitor cached file changes</span></span> <span data-ttu-id="b02cf-300">Dateiinhalt kann mithilfe von <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> speicherintern zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="b02cf-300">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="b02cf-301">Zwischenspeicherung im Arbeitsspeicher wird im Thema [Cache in-memory (Zwischenspeicherung im Arbeitsspeicher)](xref:performance/caching/memory) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="b02cf-301">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span>

<span data-ttu-id="b02cf-302">Ohne zusätzliche Schritte, wie der unten beschriebenen Implementierung, werden *veraltete* Daten aus einem Zwischenspeicher zurückgegeben, wenn sich die Quelldaten ändern.</span><span class="sxs-lookup"><span data-stu-id="b02cf-302">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span> <span data-ttu-id="b02cf-303">Wird beispielsweise der Status einer zwischengespeicherten Quelldatei beim Erneuern eines [variablen Ablaufzeitraums](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) nicht berücksichtigt, führt dies zu veralteten Dateidaten im Cache.</span><span class="sxs-lookup"><span data-stu-id="b02cf-303">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span>

<span data-ttu-id="b02cf-304">Jede Datenanforderung erneuert den variablen Ablaufzeitraum, aber die Datei wird nie neu in den Zwischenspeicher geladen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-304">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span>

* <span data-ttu-id="b02cf-305">Alle Anwendungsfeatures, die den zwischengespeicherten Inhalt der Datei verwenden, erhalten möglicherweise veraltete Inhalte.</span><span class="sxs-lookup"><span data-stu-id="b02cf-305">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>
* <span data-ttu-id="b02cf-306">Durch die Verwendung von Änderungstoken in einem Szenario mit Dateizwischenspeicherung wird verhindert, dass veraltete Dateiinhalte im Zwischenspeicher auftreten.</span><span class="sxs-lookup"><span data-stu-id="b02cf-306">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span>

<span data-ttu-id="b02cf-307">Die Beispielanwendung zeigt eine Implementierung des Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="b02cf-307">The sample app demonstrates an implementation of the approach.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="b02cf-308">Das Beispiel verwendet `GetFileContent`, um:</span><span class="sxs-lookup"><span data-stu-id="b02cf-308">The sample uses `GetFileContent` to:</span></span> <span data-ttu-id="b02cf-309">Dateiinhalt zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="b02cf-309">Return file content.</span></span>

<span data-ttu-id="b02cf-310">Einen Wiederholungsalgorithmus mit exponentiellem Backoff zu implementieren, um Fälle abzudecken, in denen eine Dateisperre vorübergehend das Lesen einer Datei verhindert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-310">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

1. <span data-ttu-id="b02cf-311">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="b02cf-311">*Utilities/Utilities.cs*:</span></span>
1. <span data-ttu-id="b02cf-312">Ein `FileService` wird erstellt, um die zwischengespeicherten Dateisuchvorgänge zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="b02cf-312">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="b02cf-313">Der `GetFileContent`-Methodenaufruf des Diensts versucht, den Dateiinhalt aus dem speicherinternen Cache abzurufen und ihn an den Aufrufer zurückzugeben (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="b02cf-313">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>
1. <span data-ttu-id="b02cf-314">Wenn mithilfe des Cacheschlüssels zwischengespeicherte Inhalte nicht gefunden werden, werden folgende Aktionen durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="b02cf-314">If cached content isn't found using the cache key, the following actions are taken:</span></span> <span data-ttu-id="b02cf-315">Der Inhalt der Datei wird mit `GetFileContent` abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-315">The file content is obtained using `GetFileContent`.</span></span>

<span data-ttu-id="b02cf-316">Ein Änderungstoken wird mithilfe von [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) aus dem Dateianbieter abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-316">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="b02cf-317">Wenn die Datei geändert wird, wird ein Rückruf des Tokens ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="b02cf-317">The token's callback is triggered when the file is modified.</span></span> <span data-ttu-id="b02cf-318">Der Inhalt der Datei wird mit einem [variablen Ablaufzeitraum](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-318">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="b02cf-319">Das Änderungstoken wird mit [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) angefügt, um den Cacheeintrag zu entfernen, wenn die sich Datei ändert, während sie zwischengespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="b02cf-319">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="b02cf-320">Im folgenden Beispiel werden Dateien im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App gespeichert.</span><span class="sxs-lookup"><span data-stu-id="b02cf-320">In the following example, files are stored in the app's [content root](xref:fundamentals/index#content-root).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="b02cf-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) wird verwendet, um einen <xref:Microsoft.Extensions.FileProviders.IFileProvider> abzurufen, der auf den <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> der App zeigt.</span><span class="sxs-lookup"><span data-stu-id="b02cf-321">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span>

<span data-ttu-id="b02cf-322">Der `filePath` wird mit [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) abgerufen.</span><span class="sxs-lookup"><span data-stu-id="b02cf-322">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="b02cf-323">Der `FileService` ist zusammen mit dem Speichercachedienst im Dienstcontainer registriert:</span><span class="sxs-lookup"><span data-stu-id="b02cf-323">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="b02cf-324">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b02cf-324">In `Startup.ConfigureServices`:</span></span>

```csharp
var firstCancellationTokenSource = new CancellationTokenSource();
var secondCancellationTokenSource = new CancellationTokenSource();

var firstCancellationToken = firstCancellationTokenSource.Token;
var secondCancellationToken = secondCancellationTokenSource.Token;

var firstCancellationChangeToken = new CancellationChangeToken(firstCancellationToken);
var secondCancellationChangeToken = new CancellationChangeToken(secondCancellationToken);

var compositeChangeToken = 
    new CompositeChangeToken(
        new List<IChangeToken> 
        {
            firstCancellationChangeToken, 
            secondCancellationChangeToken
        });
```

<span data-ttu-id="b02cf-325">Das Seitenmodell lädt mithilfe des Diensts den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="b02cf-325">The page model loads the file's content using the service.</span></span> <span data-ttu-id="b02cf-326">In der `OnGet`-Methode auf der Indexseite (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="b02cf-326">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span> <span data-ttu-id="b02cf-327">CompositeChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="b02cf-327">CompositeChangeToken class</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b02cf-328">Verwenden Sie für die Darstellung von einer oder mehreren `IChangeToken`-Instanzen in einem einzelnen Objekt die Klasse <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="b02cf-328">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
