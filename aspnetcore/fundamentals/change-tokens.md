---
title: Erkennen von Änderungen mit Änderungstoken in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Änderungstoken verwenden, um Änderungen nachzuverfolgen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/27/2019
uid: fundamentals/change-tokens
ms.openlocfilehash: 86cde7b60f5c398fc6bb215b593643c05565cf3c
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384715"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="1a408-103">Erkennen von Änderungen mit Änderungstoken in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a408-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="1a408-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1a408-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1a408-105">Ein *Änderungstoken* ist ein universeller Baustein auf niedriger Ebene, mit dem Änderungen nachverfolgt werden können.</span><span class="sxs-lookup"><span data-stu-id="1a408-105">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="1a408-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1a408-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="1a408-107">IChangeToken-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="1a408-107">IChangeToken interface</span></span>

<span data-ttu-id="1a408-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> verbreitet Benachrichtigungen, wenn eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="1a408-108"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="1a408-109">`IChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="1a408-109">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="1a408-110">Das [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/)-NuGet-Paket wird implizit für die ASP.NET Core-Apps bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1a408-110">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="1a408-111">`IChangeToken` verfügt über zwei Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="1a408-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="1a408-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> geben an, ob das Token proaktiv Rückrufe auslöst.</span><span class="sxs-lookup"><span data-stu-id="1a408-112"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="1a408-113">Wenn `ActiveChangedCallbacks` auf `false` festgelegt ist, wird ein Rückruf nie aufgerufen, und die Anwendung muss `HasChanged` für Änderungen abrufen.</span><span class="sxs-lookup"><span data-stu-id="1a408-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="1a408-114">Es ist auch möglich, dass ein Token nie abgebrochen wird, wenn keine Änderungen auftreten oder der zugrunde liegenden Änderungslistener gelöscht oder deaktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="1a408-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> ruft einen Wert auf, der angibt, ob eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="1a408-115"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="1a408-116">Die `IChangeToken`-Schnittstelle umfasst die Methode [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*). Diese registriert einen Rückruf, der bei einer Tokenänderung aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-116">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="1a408-117">`HasChanged` muss festgelegt werden, bevor der Rückruf aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="1a408-118">ChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="1a408-118">ChangeToken class</span></span>

<span data-ttu-id="1a408-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> ist eine statische Klasse, die zur Verbreitung von Änderungsbenachrichtigungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-119"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="1a408-120">`ChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="1a408-120">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="1a408-121">Das [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/)-NuGet-Paket wird implizit für die ASP.NET Core-Apps bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1a408-121">The [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package is implicitly provided to the ASP.NET Core apps.</span></span>

<span data-ttu-id="1a408-122">Die [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*)-Methode registriert einen `Action`, der bei jeder Änderung des Tokens aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="1a408-122">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="1a408-123">`Func<IChangeToken>` erzeugt das Token.</span><span class="sxs-lookup"><span data-stu-id="1a408-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="1a408-124">`Action` wird aufgerufen, wenn das Token geändert wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="1a408-125">Die [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*)-Überladung akzeptiert einen zusätzlichen `TState`-Parameter, der an den Tokenconsumer `Action` übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-125">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="1a408-126"><xref:System.IDisposable> gibt `OnChange` zurück.</span><span class="sxs-lookup"><span data-stu-id="1a408-126">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="1a408-127">Der Aufruf von <xref:System.IDisposable.Dispose*> beendet den Überwachungsvorgang des Tokens auf weitere Änderungen und gibt die Tokenressourcen frei.</span><span class="sxs-lookup"><span data-stu-id="1a408-127">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="1a408-128">Beispielverwendungen von Änderungstoken in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a408-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="1a408-129">Änderungstoken werden in wichtigen Bereichen von ASP.NET Core verwendet, um auf Objektänderungen zu überwachen:</span><span class="sxs-lookup"><span data-stu-id="1a408-129">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="1a408-130">Für die Überwachung von Änderungen an Dateien erstellt die <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*>-Methode von <xref:Microsoft.Extensions.FileProviders.IFileProvider> ein `IChangeToken` für die angegebenen Dateien oder einen Ordner, der überwacht werden soll.</span><span class="sxs-lookup"><span data-stu-id="1a408-130">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="1a408-131">`IChangeToken`-Token können zu Cacheeinträgen hinzugefügt werden, um bei Änderungen Cacheentfernungen auszulösen.</span><span class="sxs-lookup"><span data-stu-id="1a408-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="1a408-132">Für `TOptions`-Änderungen umfasst die <xref:Microsoft.Extensions.Options.OptionsMonitor`1>-Standardimplementierung von <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> eine Überladung, die eine oder mehrere <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>-Instanzen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="1a408-132">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="1a408-133">Jede Instanz gibt ein `IChangeToken` zurück, um den Rückruf einer Änderungsbenachrichtigung zur Nachverfolgung von Optionsänderungen zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="1a408-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="1a408-134">Überwachen von Konfigurationsänderungen</span><span class="sxs-lookup"><span data-stu-id="1a408-134">Monitor for configuration changes</span></span>

<span data-ttu-id="1a408-135">ASP.NET Core-Vorlagen verwenden standardmäßig [JSON-Konfigurationsdateien](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* und *appsettings.Production.json*), um Konfigurationseinstellungen der Anwendungen zu laden.</span><span class="sxs-lookup"><span data-stu-id="1a408-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="1a408-136">Diese Dateien werden mithilfe der [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*)-Erweiterungsmethode für <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> konfiguriert, die einen `reloadOnChange`-Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="1a408-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="1a408-137">`reloadOnChange` gibt an, ob Konfigurationen auf Dateiänderungen neu geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="1a408-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="1a408-138">Diese Einstellung wird in der <xref:Microsoft.Extensions.Hosting.Host>-Hilfsmethode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> verwendet:</span><span class="sxs-lookup"><span data-stu-id="1a408-138">This setting appears in the <xref:Microsoft.Extensions.Hosting.Host> convenience method <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="1a408-139">Die dateibasierte Konfiguration wird durch <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> repräsentiert.</span><span class="sxs-lookup"><span data-stu-id="1a408-139">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="1a408-140">`FileConfigurationSource` verwendet <xref:Microsoft.Extensions.FileProviders.IFileProvider> zum Überwachen von Dateien.</span><span class="sxs-lookup"><span data-stu-id="1a408-140">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="1a408-141">`IFileMonitor` wird standardmäßig von einem <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> bereitgestellt, der zum Überwachen auf Konfigurationsdateiänderungen <xref:System.IO.FileSystemWatcher> verwendet.</span><span class="sxs-lookup"><span data-stu-id="1a408-141">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="1a408-142">Die Beispielanwendung veranschaulicht zwei Implementierungen für die Überwachung von Konfigurationsänderungen.</span><span class="sxs-lookup"><span data-stu-id="1a408-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="1a408-143">Wenn sich eine beliebige der *appsettings*-Dateien ändert, führen beide Dateiüberwachungsimplementierungen benutzerdefinierten Code aus – die Beispiel-App gibt eine Meldung an die Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="1a408-143">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="1a408-144">Der `FileSystemWatcher` einer Konfigurationsdatei kann mehrere Tokenrückrufe für eine einzelne Dateikonfigurationsänderung auslösen.</span><span class="sxs-lookup"><span data-stu-id="1a408-144">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="1a408-145">Um sicherzustellen, dass der benutzerdefinierte Code bei Auslösung mehrerer Tokenrückrufe nur einmal ausgeführt wird, überprüft die Beispielimplementierung Dateihashes.</span><span class="sxs-lookup"><span data-stu-id="1a408-145">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="1a408-146">Das Beispiel verwendet SHA1-Dateihashing.</span><span class="sxs-lookup"><span data-stu-id="1a408-146">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="1a408-147">Eine Wiederholung wird mit einem exponentiellen Backoff implementiert.</span><span class="sxs-lookup"><span data-stu-id="1a408-147">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="1a408-148">Die Wiederholung ist vorhanden, weil Dateisperren auftreten können, die vorübergehend das Berechnen eines neuen Hashs für eine der Dateien verhindern.</span><span class="sxs-lookup"><span data-stu-id="1a408-148">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="1a408-149">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a408-149">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="1a408-150">Einfaches Starten von Änderungstoken</span><span class="sxs-lookup"><span data-stu-id="1a408-150">Simple startup change token</span></span>

<span data-ttu-id="1a408-151">Registrieren Sie einen `Action`-Rückruf eines Tokenconsumers für Änderungsbenachrichtigungen an das Token zum Neuladen der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="1a408-151">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="1a408-152">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1a408-152">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="1a408-153">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="1a408-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="1a408-154">Der Rückruf ist die `InvokeChanged`-Methode:</span><span class="sxs-lookup"><span data-stu-id="1a408-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="1a408-155">Der `state` des Rückrufs wird zur Übergabe an die `IWebHostEnvironment` verwendet. Dies ist hilfreich, um die richtige *appsettings*-Konfigurationsdatei anzugeben, die überwacht werden soll (in der Entwicklungsumgebung beispielsweise *appsettings.Development.json*).</span><span class="sxs-lookup"><span data-stu-id="1a408-155">The `state` of the callback is used to pass in the `IWebHostEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="1a408-156">Dateihashes werden verwendet, um zu verhindern, dass die `WriteConsole`-Anweisung mehrere Male ausgeführt wird. Dies liegt an mehreren Tokenrückrufen, wenn die Konfigurationsdatei nur einmal geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="1a408-156">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="1a408-157">Dieses System wird so lange ausgeführt, wie die Anwendung ausgeführt wird. Es kann nicht vom Benutzer deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="1a408-157">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="1a408-158">Überwachen von Konfigurationsänderungen als Dienst</span><span class="sxs-lookup"><span data-stu-id="1a408-158">Monitor configuration changes as a service</span></span>

<span data-ttu-id="1a408-159">Das Beispiel implementiert:</span><span class="sxs-lookup"><span data-stu-id="1a408-159">The sample implements:</span></span>

* <span data-ttu-id="1a408-160">Grundlegendes Überwachen des Starttokens.</span><span class="sxs-lookup"><span data-stu-id="1a408-160">Basic startup token monitoring.</span></span>
* <span data-ttu-id="1a408-161">Überwachung als Dienst.</span><span class="sxs-lookup"><span data-stu-id="1a408-161">Monitoring as a service.</span></span>
* <span data-ttu-id="1a408-162">Ein Mechanismus zum Aktivieren und Deaktivieren des Monitoring.</span><span class="sxs-lookup"><span data-stu-id="1a408-162">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="1a408-163">In diesem Beispiel wird eine `IConfigurationMonitor`-Schnittstelle eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="1a408-163">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="1a408-164">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a408-164">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="1a408-165">Der Konstruktor der implementierten `ConfigurationMonitor`-Klasse registriert einen Rückruf für Änderungsbenachrichtigungen:</span><span class="sxs-lookup"><span data-stu-id="1a408-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="1a408-166">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="1a408-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="1a408-167">`InvokeChanged` ist die Rückrufmethode.</span><span class="sxs-lookup"><span data-stu-id="1a408-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="1a408-168">In dieser Instanz stellt `state` einen Verweis auf die `IConfigurationMonitor`-Instanz dar, die zum Zugriff auf den Überwachungsstatus verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="1a408-169">Es werden zwei Eigenschaften verwendet:</span><span class="sxs-lookup"><span data-stu-id="1a408-169">Two properties are used:</span></span>

* <span data-ttu-id="1a408-170">`MonitoringEnabled` &ndash; gibt an, ob der Rückruf den zugehörigen benutzerdefinierten Code ausführen soll.</span><span class="sxs-lookup"><span data-stu-id="1a408-170">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="1a408-171">`CurrentState` &ndash; beschreibt den aktuellen Überwachungsstatus zur Verwendung in der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="1a408-171">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="1a408-172">Die `InvokeChanged`-Methode ist vergleichbar mit dem früheren Ansatz, außer dass sie:</span><span class="sxs-lookup"><span data-stu-id="1a408-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="1a408-173">Den Code nicht ausführt, es sei denn, `MonitoringEnabled` ist `true`.</span><span class="sxs-lookup"><span data-stu-id="1a408-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="1a408-174">Den aktuellen `state` in der zugehörigen `WriteConsole`-Ausgabe berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="1a408-174">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="1a408-175">Eine `ConfigurationMonitor`-Instanz wird in `Startup.ConfigureServices` als Dienst registriert:</span><span class="sxs-lookup"><span data-stu-id="1a408-175">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="1a408-176">Die Indexseite bietet das Benutzer Kontrolle über die Konfigurationsüberwachung.</span><span class="sxs-lookup"><span data-stu-id="1a408-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="1a408-177">Die `IConfigurationMonitor`-Instanz wird in das `IndexModel` eingefügt.</span><span class="sxs-lookup"><span data-stu-id="1a408-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="1a408-178">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a408-178">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="1a408-179">Der Konfigurationsmonitor (`_monitor`) wird verwendet, um die Überwachung zu aktivieren oder zu deaktivieren und den aktuellen Status für Benutzeroberflächenfeedback festzulegen:</span><span class="sxs-lookup"><span data-stu-id="1a408-179">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="1a408-180">Wenn `OnPostStartMonitoring` ausgelöst wird, wird die Überwachung aktiviert, und der aktuelle Status ist deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="1a408-180">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="1a408-181">Wenn `OnPostStopMonitoring` ausgelöst wird, wird Überwachung deaktiviert, und der Zustand zeigt, dass die Überwachung nicht ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-181">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="1a408-182">Die Überwachung wird über Schaltflächen in der Benutzeroberfläche aktiviert und deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="1a408-182">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="1a408-183">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1a408-183">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="1a408-184">Überwachen von zwischengespeicherten Dateiänderungen</span><span class="sxs-lookup"><span data-stu-id="1a408-184">Monitor cached file changes</span></span>

<span data-ttu-id="1a408-185">Dateiinhalt kann mithilfe von <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> speicherintern zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="1a408-185">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="1a408-186">Zwischenspeicherung im Arbeitsspeicher wird im Thema [Cache in-memory (Zwischenspeicherung im Arbeitsspeicher)](xref:performance/caching/memory) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="1a408-186">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="1a408-187">Ohne zusätzliche Schritte, wie der unten beschriebenen Implementierung, werden *veraltete* Daten aus einem Zwischenspeicher zurückgegeben, wenn sich die Quelldaten ändern.</span><span class="sxs-lookup"><span data-stu-id="1a408-187">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="1a408-188">Wird beispielsweise der Status einer zwischengespeicherten Quelldatei beim Erneuern eines [variablen Ablaufzeitraums](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) nicht berücksichtigt, führt dies zu veralteten Dateidaten im Cache.</span><span class="sxs-lookup"><span data-stu-id="1a408-188">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="1a408-189">Jede Datenanforderung erneuert den variablen Ablaufzeitraum, aber die Datei wird nie neu in den Zwischenspeicher geladen.</span><span class="sxs-lookup"><span data-stu-id="1a408-189">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="1a408-190">Alle Anwendungsfeatures, die den zwischengespeicherten Inhalt der Datei verwenden, erhalten möglicherweise veraltete Inhalte.</span><span class="sxs-lookup"><span data-stu-id="1a408-190">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="1a408-191">Durch die Verwendung von Änderungstoken in einem Szenario mit Dateizwischenspeicherung wird verhindert, dass veraltete Dateiinhalte im Zwischenspeicher auftreten.</span><span class="sxs-lookup"><span data-stu-id="1a408-191">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="1a408-192">Die Beispielanwendung zeigt eine Implementierung des Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="1a408-192">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="1a408-193">Das Beispiel verwendet `GetFileContent`, um:</span><span class="sxs-lookup"><span data-stu-id="1a408-193">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="1a408-194">Dateiinhalt zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="1a408-194">Return file content.</span></span>
* <span data-ttu-id="1a408-195">Einen Wiederholungsalgorithmus mit exponentiellem Backoff zu implementieren, um Fälle abzudecken, in denen eine Dateisperre vorübergehend das Lesen einer Datei verhindert.</span><span class="sxs-lookup"><span data-stu-id="1a408-195">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="1a408-196">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a408-196">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="1a408-197">Ein `FileService` wird erstellt, um die zwischengespeicherten Dateisuchvorgänge zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="1a408-197">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="1a408-198">Der `GetFileContent`-Methodenaufruf des Diensts versucht, den Dateiinhalt aus dem speicherinternen Cache abzurufen und ihn an den Aufrufer zurückzugeben (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="1a408-198">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="1a408-199">Wenn mithilfe des Cacheschlüssels zwischengespeicherte Inhalte nicht gefunden werden, werden folgende Aktionen durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="1a408-199">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="1a408-200">Der Inhalt der Datei wird mit `GetFileContent` abgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a408-200">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="1a408-201">Ein Änderungstoken wird mithilfe von [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) aus dem Dateianbieter abgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a408-201">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="1a408-202">Wenn die Datei geändert wird, wird ein Rückruf des Tokens ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="1a408-202">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="1a408-203">Der Inhalt der Datei wird mit einem [variablen Ablaufzeitraum](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="1a408-203">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="1a408-204">Das Änderungstoken wird mit [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) angefügt, um den Cacheeintrag zu entfernen, wenn die sich Datei ändert, während sie zwischengespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-204">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="1a408-205">Im folgenden Beispiel werden Dateien im Stammpfad für App-Inhalte gespeichert.</span><span class="sxs-lookup"><span data-stu-id="1a408-205">In the following example, files are stored in the app's content root.</span></span> <span data-ttu-id="1a408-206">`IWebHostEnvironment.ContentRootFileProvider` wird verwendet, um einen <xref:Microsoft.Extensions.FileProviders.IFileProvider> zu erhalten, der auf den `IWebHostEnvironment.ContentRootPath` der App verweist.</span><span class="sxs-lookup"><span data-stu-id="1a408-206">`IWebHostEnvironment.ContentRootFileProvider` is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's `IWebHostEnvironment.ContentRootPath`.</span></span> <span data-ttu-id="1a408-207">Der `filePath` wird mit [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) abgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a408-207">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="1a408-208">Der `FileService` ist zusammen mit dem Speichercachedienst im Dienstcontainer registriert:</span><span class="sxs-lookup"><span data-stu-id="1a408-208">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="1a408-209">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1a408-209">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="1a408-210">Das Seitenmodell lädt mithilfe des Diensts den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="1a408-210">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="1a408-211">In der `OnGet`-Methode auf der Indexseite (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="1a408-211">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="1a408-212">CompositeChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="1a408-212">CompositeChangeToken class</span></span>

<span data-ttu-id="1a408-213">Verwenden Sie für die Darstellung von einer oder mehreren `IChangeToken`-Instanzen in einem einzelnen Objekt die Klasse <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="1a408-213">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="1a408-214">`HasChanged` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `HasChanged`-Token `true` ist.</span><span class="sxs-lookup"><span data-stu-id="1a408-214">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="1a408-215">`ActiveChangeCallbacks` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `ActiveChangeCallbacks`-Token `true` ist.</span><span class="sxs-lookup"><span data-stu-id="1a408-215">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="1a408-216">Wenn mehrere Änderungsereignisse gleichzeitig auftreten, wird der Rückruf für die kombinierte Änderung einmal aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a408-216">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1a408-217">Ein *Änderungstoken* ist ein universeller Baustein auf niedriger Ebene, mit dem Änderungen nachverfolgt werden können.</span><span class="sxs-lookup"><span data-stu-id="1a408-217">A *change token* is a general-purpose, low-level building block used to track state changes.</span></span>

<span data-ttu-id="1a408-218">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1a408-218">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="1a408-219">IChangeToken-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="1a408-219">IChangeToken interface</span></span>

<span data-ttu-id="1a408-220"><xref:Microsoft.Extensions.Primitives.IChangeToken> verbreitet Benachrichtigungen, wenn eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="1a408-220"><xref:Microsoft.Extensions.Primitives.IChangeToken> propagates notifications that a change has occurred.</span></span> <span data-ttu-id="1a408-221">`IChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="1a408-221">`IChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="1a408-222">Für Anwendungen, die das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nicht verwenden, erstellen Sie einen Paketverweis auf das NuGet-Paket [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="1a408-222">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="1a408-223">`IChangeToken` verfügt über zwei Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="1a408-223">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="1a408-224"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> geben an, ob das Token proaktiv Rückrufe auslöst.</span><span class="sxs-lookup"><span data-stu-id="1a408-224"><xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="1a408-225">Wenn `ActiveChangedCallbacks` auf `false` festgelegt ist, wird ein Rückruf nie aufgerufen, und die Anwendung muss `HasChanged` für Änderungen abrufen.</span><span class="sxs-lookup"><span data-stu-id="1a408-225">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="1a408-226">Es ist auch möglich, dass ein Token nie abgebrochen wird, wenn keine Änderungen auftreten oder der zugrunde liegenden Änderungslistener gelöscht oder deaktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-226">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="1a408-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> ruft einen Wert auf, der angibt, ob eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="1a408-227"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> receives a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="1a408-228">Die `IChangeToken`-Schnittstelle umfasst die Methode [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*). Diese registriert einen Rückruf, der bei einer Tokenänderung aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-228">The `IChangeToken` interface includes the [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*) method, which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="1a408-229">`HasChanged` muss festgelegt werden, bevor der Rückruf aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-229">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="1a408-230">ChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="1a408-230">ChangeToken class</span></span>

<span data-ttu-id="1a408-231"><xref:Microsoft.Extensions.Primitives.ChangeToken> ist eine statische Klasse, die zur Verbreitung von Änderungsbenachrichtigungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-231"><xref:Microsoft.Extensions.Primitives.ChangeToken> is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="1a408-232">`ChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="1a408-232">`ChangeToken` resides in the <xref:Microsoft.Extensions.Primitives?displayProperty=fullName> namespace.</span></span> <span data-ttu-id="1a408-233">Für Anwendungen, die das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nicht verwenden, erstellen Sie einen Paketverweis auf das NuGet-Paket [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).</span><span class="sxs-lookup"><span data-stu-id="1a408-233">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference for the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package.</span></span>

<span data-ttu-id="1a408-234">Die [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*)-Methode registriert einen `Action`, der bei jeder Änderung des Tokens aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="1a408-234">The [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="1a408-235">`Func<IChangeToken>` erzeugt das Token.</span><span class="sxs-lookup"><span data-stu-id="1a408-235">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="1a408-236">`Action` wird aufgerufen, wenn das Token geändert wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-236">`Action` is called when the token changes.</span></span>

<span data-ttu-id="1a408-237">Die [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*)-Überladung akzeptiert einen zusätzlichen `TState`-Parameter, der an den Tokenconsumer `Action` übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-237">The [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) overload takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="1a408-238"><xref:System.IDisposable> gibt `OnChange` zurück.</span><span class="sxs-lookup"><span data-stu-id="1a408-238">`OnChange` returns an <xref:System.IDisposable>.</span></span> <span data-ttu-id="1a408-239">Der Aufruf von <xref:System.IDisposable.Dispose*> beendet den Überwachungsvorgang des Tokens auf weitere Änderungen und gibt die Tokenressourcen frei.</span><span class="sxs-lookup"><span data-stu-id="1a408-239">Calling <xref:System.IDisposable.Dispose*> stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="1a408-240">Beispielverwendungen von Änderungstoken in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a408-240">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="1a408-241">Änderungstoken werden in wichtigen Bereichen von ASP.NET Core verwendet, um auf Objektänderungen zu überwachen:</span><span class="sxs-lookup"><span data-stu-id="1a408-241">Change tokens are used in prominent areas of ASP.NET Core to monitor for changes to objects:</span></span>

* <span data-ttu-id="1a408-242">Für die Überwachung von Änderungen an Dateien erstellt die <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*>-Methode von <xref:Microsoft.Extensions.FileProviders.IFileProvider> ein `IChangeToken` für die angegebenen Dateien oder einen Ordner, der überwacht werden soll.</span><span class="sxs-lookup"><span data-stu-id="1a408-242">For monitoring changes to files, <xref:Microsoft.Extensions.FileProviders.IFileProvider>'s <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*> method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="1a408-243">`IChangeToken`-Token können zu Cacheeinträgen hinzugefügt werden, um bei Änderungen Cacheentfernungen auszulösen.</span><span class="sxs-lookup"><span data-stu-id="1a408-243">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="1a408-244">Für `TOptions`-Änderungen umfasst die <xref:Microsoft.Extensions.Options.OptionsMonitor`1>-Standardimplementierung von <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> eine Überladung, die eine oder mehrere <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>-Instanzen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="1a408-244">For `TOptions` changes, the default <xref:Microsoft.Extensions.Options.OptionsMonitor`1> implementation of <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> has an overload that accepts one or more <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1> instances.</span></span> <span data-ttu-id="1a408-245">Jede Instanz gibt ein `IChangeToken` zurück, um den Rückruf einer Änderungsbenachrichtigung zur Nachverfolgung von Optionsänderungen zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="1a408-245">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitor-for-configuration-changes"></a><span data-ttu-id="1a408-246">Überwachen von Konfigurationsänderungen</span><span class="sxs-lookup"><span data-stu-id="1a408-246">Monitor for configuration changes</span></span>

<span data-ttu-id="1a408-247">ASP.NET Core-Vorlagen verwenden standardmäßig [JSON-Konfigurationsdateien](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* und *appsettings.Production.json*), um Konfigurationseinstellungen der Anwendungen zu laden.</span><span class="sxs-lookup"><span data-stu-id="1a408-247">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="1a408-248">Diese Dateien werden mithilfe der [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*)-Erweiterungsmethode für <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> konfiguriert, die einen `reloadOnChange`-Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="1a408-248">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*) extension method on <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> that accepts a `reloadOnChange` parameter.</span></span> <span data-ttu-id="1a408-249">`reloadOnChange` gibt an, ob Konfigurationen auf Dateiänderungen neu geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="1a408-249">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="1a408-250">Diese Einstellung wird in der <xref:Microsoft.AspNetCore.WebHost>-Hilfsmethode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> verwendet:</span><span class="sxs-lookup"><span data-stu-id="1a408-250">This setting appears in the <xref:Microsoft.AspNetCore.WebHost> convenience method <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>:</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

<span data-ttu-id="1a408-251">Die dateibasierte Konfiguration wird durch <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> repräsentiert.</span><span class="sxs-lookup"><span data-stu-id="1a408-251">File-based configuration is represented by <xref:Microsoft.Extensions.Configuration.FileConfigurationSource>.</span></span> <span data-ttu-id="1a408-252">`FileConfigurationSource` verwendet <xref:Microsoft.Extensions.FileProviders.IFileProvider> zum Überwachen von Dateien.</span><span class="sxs-lookup"><span data-stu-id="1a408-252">`FileConfigurationSource` uses <xref:Microsoft.Extensions.FileProviders.IFileProvider> to monitor files.</span></span>

<span data-ttu-id="1a408-253">`IFileMonitor` wird standardmäßig von einem <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> bereitgestellt, der zum Überwachen auf Konfigurationsdateiänderungen <xref:System.IO.FileSystemWatcher> verwendet.</span><span class="sxs-lookup"><span data-stu-id="1a408-253">By default, the `IFileMonitor` is provided by a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>, which uses <xref:System.IO.FileSystemWatcher> to monitor for configuration file changes.</span></span>

<span data-ttu-id="1a408-254">Die Beispielanwendung veranschaulicht zwei Implementierungen für die Überwachung von Konfigurationsänderungen.</span><span class="sxs-lookup"><span data-stu-id="1a408-254">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="1a408-255">Wenn sich eine beliebige der *appsettings*-Dateien ändert, führen beide Dateiüberwachungsimplementierungen benutzerdefinierten Code aus – die Beispiel-App gibt eine Meldung an die Konsole aus.</span><span class="sxs-lookup"><span data-stu-id="1a408-255">If any of the *appsettings* files change, both of the file monitoring implementations execute custom code&mdash;the sample app writes a message to the console.</span></span>

<span data-ttu-id="1a408-256">Der `FileSystemWatcher` einer Konfigurationsdatei kann mehrere Tokenrückrufe für eine einzelne Dateikonfigurationsänderung auslösen.</span><span class="sxs-lookup"><span data-stu-id="1a408-256">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="1a408-257">Um sicherzustellen, dass der benutzerdefinierte Code bei Auslösung mehrerer Tokenrückrufe nur einmal ausgeführt wird, überprüft die Beispielimplementierung Dateihashes.</span><span class="sxs-lookup"><span data-stu-id="1a408-257">To ensure that the custom code is only run once when multiple token callbacks are triggered, the sample's implementation checks file hashes.</span></span> <span data-ttu-id="1a408-258">Das Beispiel verwendet SHA1-Dateihashing.</span><span class="sxs-lookup"><span data-stu-id="1a408-258">The sample uses SHA1 file hashing.</span></span> <span data-ttu-id="1a408-259">Eine Wiederholung wird mit einem exponentiellen Backoff implementiert.</span><span class="sxs-lookup"><span data-stu-id="1a408-259">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="1a408-260">Die Wiederholung ist vorhanden, weil Dateisperren auftreten können, die vorübergehend das Berechnen eines neuen Hashs für eine der Dateien verhindern.</span><span class="sxs-lookup"><span data-stu-id="1a408-260">The retry is present because file locking may occur that temporarily prevents computing a new hash on a file.</span></span>

<span data-ttu-id="1a408-261">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a408-261">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a><span data-ttu-id="1a408-262">Einfaches Starten von Änderungstoken</span><span class="sxs-lookup"><span data-stu-id="1a408-262">Simple startup change token</span></span>

<span data-ttu-id="1a408-263">Registrieren Sie einen `Action`-Rückruf eines Tokenconsumers für Änderungsbenachrichtigungen an das Token zum Neuladen der Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="1a408-263">Register a token consumer `Action` callback for change notifications to the configuration reload token.</span></span>

<span data-ttu-id="1a408-264">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="1a408-264">In `Startup.Configure`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

<span data-ttu-id="1a408-265">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="1a408-265">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="1a408-266">Der Rückruf ist die `InvokeChanged`-Methode:</span><span class="sxs-lookup"><span data-stu-id="1a408-266">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<span data-ttu-id="1a408-267">Der `state` des Rückrufs wird zur Übergabe an die `IHostingEnvironment` verwendet. Dies ist hilfreich, um die richtige *appsettings*-Konfigurationsdatei anzugeben, die überwacht werden soll (in der Entwicklungsumgebung beispielsweise *appsettings.Development.json*).</span><span class="sxs-lookup"><span data-stu-id="1a408-267">The `state` of the callback is used to pass in the `IHostingEnvironment`, which is useful for specifying the correct *appsettings* configuration file to monitor (for example, *appsettings.Development.json* when in the Development environment).</span></span> <span data-ttu-id="1a408-268">Dateihashes werden verwendet, um zu verhindern, dass die `WriteConsole`-Anweisung mehrere Male ausgeführt wird. Dies liegt an mehreren Tokenrückrufen, wenn die Konfigurationsdatei nur einmal geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="1a408-268">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="1a408-269">Dieses System wird so lange ausgeführt, wie die Anwendung ausgeführt wird. Es kann nicht vom Benutzer deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="1a408-269">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitor-configuration-changes-as-a-service"></a><span data-ttu-id="1a408-270">Überwachen von Konfigurationsänderungen als Dienst</span><span class="sxs-lookup"><span data-stu-id="1a408-270">Monitor configuration changes as a service</span></span>

<span data-ttu-id="1a408-271">Das Beispiel implementiert:</span><span class="sxs-lookup"><span data-stu-id="1a408-271">The sample implements:</span></span>

* <span data-ttu-id="1a408-272">Grundlegendes Überwachen des Starttokens.</span><span class="sxs-lookup"><span data-stu-id="1a408-272">Basic startup token monitoring.</span></span>
* <span data-ttu-id="1a408-273">Überwachung als Dienst.</span><span class="sxs-lookup"><span data-stu-id="1a408-273">Monitoring as a service.</span></span>
* <span data-ttu-id="1a408-274">Ein Mechanismus zum Aktivieren und Deaktivieren des Monitoring.</span><span class="sxs-lookup"><span data-stu-id="1a408-274">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="1a408-275">In diesem Beispiel wird eine `IConfigurationMonitor`-Schnittstelle eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="1a408-275">The sample establishes an `IConfigurationMonitor` interface.</span></span>

<span data-ttu-id="1a408-276">*Extensions/ConfigurationMonitor.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a408-276">*Extensions/ConfigurationMonitor.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="1a408-277">Der Konstruktor der implementierten `ConfigurationMonitor`-Klasse registriert einen Rückruf für Änderungsbenachrichtigungen:</span><span class="sxs-lookup"><span data-stu-id="1a408-277">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="1a408-278">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="1a408-278">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="1a408-279">`InvokeChanged` ist die Rückrufmethode.</span><span class="sxs-lookup"><span data-stu-id="1a408-279">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="1a408-280">In dieser Instanz stellt `state` einen Verweis auf die `IConfigurationMonitor`-Instanz dar, die zum Zugriff auf den Überwachungsstatus verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-280">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that's used to access the monitoring state.</span></span> <span data-ttu-id="1a408-281">Es werden zwei Eigenschaften verwendet:</span><span class="sxs-lookup"><span data-stu-id="1a408-281">Two properties are used:</span></span>

* <span data-ttu-id="1a408-282">`MonitoringEnabled` &ndash; gibt an, ob der Rückruf den zugehörigen benutzerdefinierten Code ausführen soll.</span><span class="sxs-lookup"><span data-stu-id="1a408-282">`MonitoringEnabled` &ndash; Indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="1a408-283">`CurrentState` &ndash; beschreibt den aktuellen Überwachungsstatus zur Verwendung in der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="1a408-283">`CurrentState` &ndash; Describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="1a408-284">Die `InvokeChanged`-Methode ist vergleichbar mit dem früheren Ansatz, außer dass sie:</span><span class="sxs-lookup"><span data-stu-id="1a408-284">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="1a408-285">Den Code nicht ausführt, es sei denn, `MonitoringEnabled` ist `true`.</span><span class="sxs-lookup"><span data-stu-id="1a408-285">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="1a408-286">Den aktuellen `state` in der zugehörigen `WriteConsole`-Ausgabe berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="1a408-286">Outputs the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="1a408-287">Eine `ConfigurationMonitor`-Instanz wird in `Startup.ConfigureServices` als Dienst registriert:</span><span class="sxs-lookup"><span data-stu-id="1a408-287">An instance `ConfigurationMonitor` is registered as a service in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="1a408-288">Die Indexseite bietet das Benutzer Kontrolle über die Konfigurationsüberwachung.</span><span class="sxs-lookup"><span data-stu-id="1a408-288">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="1a408-289">Die `IConfigurationMonitor`-Instanz wird in das `IndexModel` eingefügt.</span><span class="sxs-lookup"><span data-stu-id="1a408-289">The instance of `IConfigurationMonitor` is injected into the `IndexModel`.</span></span>

<span data-ttu-id="1a408-290">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a408-290">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="1a408-291">Der Konfigurationsmonitor (`_monitor`) wird verwendet, um die Überwachung zu aktivieren oder zu deaktivieren und den aktuellen Status für Benutzeroberflächenfeedback festzulegen:</span><span class="sxs-lookup"><span data-stu-id="1a408-291">The configuration monitor (`_monitor`) is used to enable or disable monitoring and set the current state for UI feedback:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="1a408-292">Wenn `OnPostStartMonitoring` ausgelöst wird, wird die Überwachung aktiviert, und der aktuelle Status ist deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="1a408-292">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="1a408-293">Wenn `OnPostStopMonitoring` ausgelöst wird, wird Überwachung deaktiviert, und der Zustand zeigt, dass die Überwachung nicht ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-293">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

<span data-ttu-id="1a408-294">Die Überwachung wird über Schaltflächen in der Benutzeroberfläche aktiviert und deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="1a408-294">Buttons in the UI enable and disable monitoring.</span></span>

<span data-ttu-id="1a408-295">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="1a408-295">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a><span data-ttu-id="1a408-296">Überwachen von zwischengespeicherten Dateiänderungen</span><span class="sxs-lookup"><span data-stu-id="1a408-296">Monitor cached file changes</span></span>

<span data-ttu-id="1a408-297">Dateiinhalt kann mithilfe von <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> speicherintern zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="1a408-297">File content can be cached in-memory using <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="1a408-298">Zwischenspeicherung im Arbeitsspeicher wird im Thema [Cache in-memory (Zwischenspeicherung im Arbeitsspeicher)](xref:performance/caching/memory) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="1a408-298">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="1a408-299">Ohne zusätzliche Schritte, wie der unten beschriebenen Implementierung, werden *veraltete* Daten aus einem Zwischenspeicher zurückgegeben, wenn sich die Quelldaten ändern.</span><span class="sxs-lookup"><span data-stu-id="1a408-299">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="1a408-300">Wird beispielsweise der Status einer zwischengespeicherten Quelldatei beim Erneuern eines [variablen Ablaufzeitraums](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) nicht berücksichtigt, führt dies zu veralteten Dateidaten im Cache.</span><span class="sxs-lookup"><span data-stu-id="1a408-300">For example, not taking into account the status of a cached source file when renewing a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period leads to stale cached file data.</span></span> <span data-ttu-id="1a408-301">Jede Datenanforderung erneuert den variablen Ablaufzeitraum, aber die Datei wird nie neu in den Zwischenspeicher geladen.</span><span class="sxs-lookup"><span data-stu-id="1a408-301">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="1a408-302">Alle Anwendungsfeatures, die den zwischengespeicherten Inhalt der Datei verwenden, erhalten möglicherweise veraltete Inhalte.</span><span class="sxs-lookup"><span data-stu-id="1a408-302">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="1a408-303">Durch die Verwendung von Änderungstoken in einem Szenario mit Dateizwischenspeicherung wird verhindert, dass veraltete Dateiinhalte im Zwischenspeicher auftreten.</span><span class="sxs-lookup"><span data-stu-id="1a408-303">Using change tokens in a file caching scenario prevents the presence of stale file content in the cache.</span></span> <span data-ttu-id="1a408-304">Die Beispielanwendung zeigt eine Implementierung des Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="1a408-304">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="1a408-305">Das Beispiel verwendet `GetFileContent`, um:</span><span class="sxs-lookup"><span data-stu-id="1a408-305">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="1a408-306">Dateiinhalt zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="1a408-306">Return file content.</span></span>
* <span data-ttu-id="1a408-307">Einen Wiederholungsalgorithmus mit exponentiellem Backoff zu implementieren, um Fälle abzudecken, in denen eine Dateisperre vorübergehend das Lesen einer Datei verhindert.</span><span class="sxs-lookup"><span data-stu-id="1a408-307">Implement a retry algorithm with exponential back-off to cover cases where a file lock temporarily prevents reading a file.</span></span>

<span data-ttu-id="1a408-308">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="1a408-308">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="1a408-309">Ein `FileService` wird erstellt, um die zwischengespeicherten Dateisuchvorgänge zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="1a408-309">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="1a408-310">Der `GetFileContent`-Methodenaufruf des Diensts versucht, den Dateiinhalt aus dem speicherinternen Cache abzurufen und ihn an den Aufrufer zurückzugeben (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="1a408-310">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="1a408-311">Wenn mithilfe des Cacheschlüssels zwischengespeicherte Inhalte nicht gefunden werden, werden folgende Aktionen durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="1a408-311">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="1a408-312">Der Inhalt der Datei wird mit `GetFileContent` abgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a408-312">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="1a408-313">Ein Änderungstoken wird mithilfe von [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) aus dem Dateianbieter abgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a408-313">A change token is obtained from the file provider with [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*).</span></span> <span data-ttu-id="1a408-314">Wenn die Datei geändert wird, wird ein Rückruf des Tokens ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="1a408-314">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="1a408-315">Der Inhalt der Datei wird mit einem [variablen Ablaufzeitraum](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="1a408-315">The file content is cached with a [sliding expiration](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) period.</span></span> <span data-ttu-id="1a408-316">Das Änderungstoken wird mit [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) angefügt, um den Cacheeintrag zu entfernen, wenn die sich Datei ändert, während sie zwischengespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="1a408-316">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) to evict the cache entry if the file changes while it's cached.</span></span>

<span data-ttu-id="1a408-317">Im folgenden Beispiel werden Dateien im Stammpfad für App-Inhalte gespeichert.</span><span class="sxs-lookup"><span data-stu-id="1a408-317">In the following example, files are stored in the app's content root.</span></span> <span data-ttu-id="1a408-318">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) wird verwendet, um einen <xref:Microsoft.Extensions.FileProviders.IFileProvider> abzurufen, der auf den <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> der App zeigt.</span><span class="sxs-lookup"><span data-stu-id="1a408-318">[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) is used to obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> pointing at the app's <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>.</span></span> <span data-ttu-id="1a408-319">Der `filePath` wird mit [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) abgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a408-319">The `filePath` is obtained with [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath).</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="1a408-320">Der `FileService` ist zusammen mit dem Speichercachedienst im Dienstcontainer registriert:</span><span class="sxs-lookup"><span data-stu-id="1a408-320">The `FileService` is registered in the service container along with the memory caching service.</span></span>

<span data-ttu-id="1a408-321">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1a408-321">In `Startup.ConfigureServices`:</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<span data-ttu-id="1a408-322">Das Seitenmodell lädt mithilfe des Diensts den Inhalt der Datei.</span><span class="sxs-lookup"><span data-stu-id="1a408-322">The page model loads the file's content using the service.</span></span>

<span data-ttu-id="1a408-323">In der `OnGet`-Methode auf der Indexseite (*Pages/Index.cshtml.cs*):</span><span class="sxs-lookup"><span data-stu-id="1a408-323">In the Index page's `OnGet` method (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="1a408-324">CompositeChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="1a408-324">CompositeChangeToken class</span></span>

<span data-ttu-id="1a408-325">Verwenden Sie für die Darstellung von einer oder mehreren `IChangeToken`-Instanzen in einem einzelnen Objekt die Klasse <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.</span><span class="sxs-lookup"><span data-stu-id="1a408-325">For representing one or more `IChangeToken` instances in a single object, use the <xref:Microsoft.Extensions.Primitives.CompositeChangeToken> class.</span></span>

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

<span data-ttu-id="1a408-326">`HasChanged` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `HasChanged`-Token `true` ist.</span><span class="sxs-lookup"><span data-stu-id="1a408-326">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="1a408-327">`ActiveChangeCallbacks` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `ActiveChangeCallbacks`-Token `true` ist.</span><span class="sxs-lookup"><span data-stu-id="1a408-327">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="1a408-328">Wenn mehrere Änderungsereignisse gleichzeitig auftreten, wird der Rückruf für die kombinierte Änderung einmal aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="1a408-328">If multiple concurrent change events occur, the composite change callback is invoked one time.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1a408-329">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1a408-329">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
