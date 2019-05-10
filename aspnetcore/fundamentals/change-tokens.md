---
title: Erkennen von Änderungen mit Änderungstoken in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Änderungstoken verwenden, um Änderungen nachzuverfolgen.
ms.author: riande
ms.date: 11/10/2017
uid: fundamentals/change-tokens
ms.openlocfilehash: f7dad52fc19b6dc5c8668ad852e69acd65a72162
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886875"
---
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a><span data-ttu-id="362ba-103">Erkennen von Änderungen mit Änderungstoken in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="362ba-103">Detect changes with change tokens in ASP.NET Core</span></span>

<span data-ttu-id="362ba-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="362ba-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="362ba-105">Ein *Änderungstoken* ist ein allgemeiner Baustein auf niedriger Ebene zum Nachverfolgen von Änderungen.</span><span class="sxs-lookup"><span data-stu-id="362ba-105">A *change token* is a general-purpose, low-level building block used to track changes.</span></span>

<span data-ttu-id="362ba-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="362ba-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="ichangetoken-interface"></a><span data-ttu-id="362ba-107">IChangeToken-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="362ba-107">IChangeToken interface</span></span>

<span data-ttu-id="362ba-108">[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) verbreitet Benachrichtigungen, wenn eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="362ba-108">[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) propagates notifications that a change has occurred.</span></span> <span data-ttu-id="362ba-109">`IChangeToken` befindet sich im [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives)-Namespace.</span><span class="sxs-lookup"><span data-stu-id="362ba-109">`IChangeToken` resides in the [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) namespace.</span></span> <span data-ttu-id="362ba-110">Für Anwendungen, die das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 oder höher) nicht verwenden, verweisen Sie auf das NuGet-Paket [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="362ba-110">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later), reference the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package in the project file.</span></span>

<span data-ttu-id="362ba-111">`IChangeToken` verfügt über zwei Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="362ba-111">`IChangeToken` has two properties:</span></span>

* <span data-ttu-id="362ba-112">[ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks) geben an, ob das Token proaktiv Rückrufe auslöst.</span><span class="sxs-lookup"><span data-stu-id="362ba-112">[ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks) indicate if the token proactively raises callbacks.</span></span> <span data-ttu-id="362ba-113">Wenn `ActiveChangedCallbacks` auf `false` festgelegt ist, wird ein Rückruf nie aufgerufen, und die Anwendung muss `HasChanged` für Änderungen abrufen.</span><span class="sxs-lookup"><span data-stu-id="362ba-113">If `ActiveChangedCallbacks` is set to `false`, a callback is never called, and the app must poll `HasChanged` for changes.</span></span> <span data-ttu-id="362ba-114">Es ist auch möglich, dass ein Token nie abgebrochen wird, wenn keine Änderungen auftreten oder der zugrunde liegenden Änderungslistener gelöscht oder deaktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="362ba-114">It's also possible for a token to never be cancelled if no changes occur or the underlying change listener is disposed or disabled.</span></span>
* <span data-ttu-id="362ba-115">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) ruft einen Wert auf, der angibt, ob eine Änderung aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="362ba-115">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) gets a value that indicates if a change has occurred.</span></span>

<span data-ttu-id="362ba-116">Die Schnittstelle verfügt über eine Methode, [RegisterChangeCallback(Action&lt;Object&gt;, Object)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback), die einen Rückruf registriert, der aufgerufen wird, wenn das Token geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="362ba-116">The interface has one method, [RegisterChangeCallback(Action&lt;Object&gt;, Object)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback), which registers a callback that's invoked when the token has changed.</span></span> <span data-ttu-id="362ba-117">`HasChanged` muss festgelegt werden, bevor der Rückruf aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="362ba-117">`HasChanged` must be set before the callback is invoked.</span></span>

## <a name="changetoken-class"></a><span data-ttu-id="362ba-118">ChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="362ba-118">ChangeToken class</span></span>

<span data-ttu-id="362ba-119">`ChangeToken` ist eine statische Klasse, die zur Verbreitung von Änderungsbenachrichtigungen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="362ba-119">`ChangeToken` is a static class used to propagate notifications that a change has occurred.</span></span> <span data-ttu-id="362ba-120">`ChangeToken` befindet sich im [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives)-Namespace.</span><span class="sxs-lookup"><span data-stu-id="362ba-120">`ChangeToken` resides in the [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives) namespace.</span></span> <span data-ttu-id="362ba-121">Für Anwendungen, die das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nicht verwenden, verweisen Sie auf das NuGet-Paket [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="362ba-121">For apps that don't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), reference the [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) NuGet package in the project file.</span></span>

<span data-ttu-id="362ba-122">Die `ChangeToken` [OnChange (Func&lt;IChangeToken&gt;, Action)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_)-Methode registriert eine aufzurufende `Action`, wenn das Token geändert wird:</span><span class="sxs-lookup"><span data-stu-id="362ba-122">The `ChangeToken` [OnChange(Func&lt;IChangeToken&gt;, Action)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_) method registers an `Action` to call whenever the token changes:</span></span>

* <span data-ttu-id="362ba-123">`Func<IChangeToken>` erzeugt das Token.</span><span class="sxs-lookup"><span data-stu-id="362ba-123">`Func<IChangeToken>` produces the token.</span></span>
* <span data-ttu-id="362ba-124">`Action` wird aufgerufen, wenn das Token geändert wird.</span><span class="sxs-lookup"><span data-stu-id="362ba-124">`Action` is called when the token changes.</span></span>

<span data-ttu-id="362ba-125">`ChangeToken` verfügt über eine [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, Action&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_)-Überladung mit einem zusätzlichen `TState`-Parameter, das an den Tokennutzer `Action` übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="362ba-125">`ChangeToken` has an [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, Action&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_) overload that takes an additional `TState` parameter that's passed into the token consumer `Action`.</span></span>

<span data-ttu-id="362ba-126">`OnChange` gibt [IDisposable](/dotnet/api/system.idisposable) zurück.</span><span class="sxs-lookup"><span data-stu-id="362ba-126">`OnChange` returns an [IDisposable](/dotnet/api/system.idisposable).</span></span> <span data-ttu-id="362ba-127">Der Aufruf von [Dispose](/dotnet/api/system.idisposable.dispose) beendet den Überwachungsvorgang des Tokens für weitere Änderungen und gibt die Ressourcen des Tokens frei.</span><span class="sxs-lookup"><span data-stu-id="362ba-127">Calling [Dispose](/dotnet/api/system.idisposable.dispose) stops the token from listening for further changes and releases the token's resources.</span></span>

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a><span data-ttu-id="362ba-128">Beispielverwendungen von Änderungstoken in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="362ba-128">Example uses of change tokens in ASP.NET Core</span></span>

<span data-ttu-id="362ba-129">Änderungstoken werden in gut sichtbaren Bereichen von ASP.NET Core verwendet. Sie überwachen Objektänderungen:</span><span class="sxs-lookup"><span data-stu-id="362ba-129">Change tokens are used in prominent areas of ASP.NET Core monitoring changes to objects:</span></span>

* <span data-ttu-id="362ba-130">Für die Überwachung von Änderungen der Dateien erstellt die [Überwachen](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch)-Methode des [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) ein `IChangeToken` zur Verfügung, das die angegebenen Dateien oder Ordner überwacht.</span><span class="sxs-lookup"><span data-stu-id="362ba-130">For monitoring changes to files, [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider)'s [Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) method creates an `IChangeToken` for the specified files or folder to watch.</span></span>
* <span data-ttu-id="362ba-131">`IChangeToken`-Token können zu Cacheeinträgen hinzugefügt werden, um bei Änderungen Cacheentfernungen auszulösen.</span><span class="sxs-lookup"><span data-stu-id="362ba-131">`IChangeToken` tokens can be added to cache entries to trigger cache evictions on change.</span></span>
* <span data-ttu-id="362ba-132">Für `TOptions`-Änderungen weist die Standard-[OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1)-Implementierung von [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) eine Überladung auf, die eine oder mehrere [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1)-Instanzen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="362ba-132">For `TOptions` changes, the default [OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1) implementation of [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) has an overload that accepts one or more [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1) instances.</span></span> <span data-ttu-id="362ba-133">Jede Instanz gibt ein `IChangeToken` zurück, um den Rückruf einer Änderungsbenachrichtigung zur Nachverfolgung von Optionsänderungen zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="362ba-133">Each instance returns an `IChangeToken` to register a change notification callback for tracking options changes.</span></span>

## <a name="monitoring-for-configuration-changes"></a><span data-ttu-id="362ba-134">Überwachen von Konfigurationsänderungen</span><span class="sxs-lookup"><span data-stu-id="362ba-134">Monitoring for configuration changes</span></span>

<span data-ttu-id="362ba-135">ASP.NET Core-Vorlagen verwenden standardmäßig [JSON-Konfigurationsdateien](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* und *appsettings.Production.json*), um Konfigurationseinstellungen der Anwendungen zu laden.</span><span class="sxs-lookup"><span data-stu-id="362ba-135">By default, ASP.NET Core templates use [JSON configuration files](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json*, and *appsettings.Production.json*) to load app configuration settings.</span></span>

<span data-ttu-id="362ba-136">Diese Dateien werden mithilfe der [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_)-Erweiterungsmethode auf [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) konfiguriert, der einen `reloadOnChange`-Parameter (ASP.NET Core 1.1 und höher) akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="362ba-136">These files are configured using the [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_) extension method on [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) that accepts a `reloadOnChange` parameter (ASP.NET Core 1.1 and later).</span></span> <span data-ttu-id="362ba-137">`reloadOnChange` gibt an, ob Konfigurationen auf Dateiänderungen neu geladen werden soll.</span><span class="sxs-lookup"><span data-stu-id="362ba-137">`reloadOnChange` indicates if configuration should be reloaded on file changes.</span></span> <span data-ttu-id="362ba-138">Diese Einstellung sehen Sie in der [WebHost](/dotnet/api/microsoft.aspnetcore.webhost)-Hilfsmethode [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder):</span><span class="sxs-lookup"><span data-stu-id="362ba-138">See this setting in the [WebHost](/dotnet/api/microsoft.aspnetcore.webhost) convenience method [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder):</span></span>

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);
```

<span data-ttu-id="362ba-139">Dateibasierte Konfiguration wird von [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource) dargestellt.</span><span class="sxs-lookup"><span data-stu-id="362ba-139">File-based configuration is represented by [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource).</span></span> <span data-ttu-id="362ba-140">`FileConfigurationSource` verwendet [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) zur Überwachung von Dateien.</span><span class="sxs-lookup"><span data-stu-id="362ba-140">`FileConfigurationSource` uses [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) to monitor files.</span></span>

<span data-ttu-id="362ba-141">`IFileMonitor` wird standardmäßig von einem [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) bereitgestellt, der [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) zum Überwachen der Änderungen von Konfigurationsdateien verwendet.</span><span class="sxs-lookup"><span data-stu-id="362ba-141">By default, the `IFileMonitor` is provided by a [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider), which uses [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) to monitor for configuration file changes.</span></span>

<span data-ttu-id="362ba-142">Die Beispielanwendung veranschaulicht zwei Implementierungen für die Überwachung von Konfigurationsänderungen.</span><span class="sxs-lookup"><span data-stu-id="362ba-142">The sample app demonstrates two implementations for monitoring configuration changes.</span></span> <span data-ttu-id="362ba-143">Wenn entweder die *appsettings.json*-Datei oder die Umgebungsversion der Datei geändert wird, führt jede Implementierung benutzerdefinierten Code aus.</span><span class="sxs-lookup"><span data-stu-id="362ba-143">If either the *appsettings.json* file changes or the Environment version of the file changes, each implementation executes custom code.</span></span> <span data-ttu-id="362ba-144">Die Beispielanwendung schreibt eine Nachricht an die Konsole.</span><span class="sxs-lookup"><span data-stu-id="362ba-144">The sample app writes a message to the console.</span></span>

<span data-ttu-id="362ba-145">Der `FileSystemWatcher` einer Konfigurationsdatei kann mehrere Tokenrückrufe für eine einzelne Dateikonfigurationsänderung auslösen.</span><span class="sxs-lookup"><span data-stu-id="362ba-145">A configuration file's `FileSystemWatcher` can trigger multiple token callbacks for a single configuration file change.</span></span> <span data-ttu-id="362ba-146">Die Implementierung des Beispiels schützt vor diesem Problem, indem die Dateihashes der Konfigurationsdateien überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="362ba-146">The sample's implementation guards against this problem by checking file hashes on the configuration files.</span></span> <span data-ttu-id="362ba-147">Die Überprüfung von Dateihashes stellt sicher, dass mindestens eine der Konfigurationsdateien geändert wurde, bevor der benutzerdefinierte Code ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="362ba-147">Checking file hashes ensures that at least one of the configuration files has changed before running the custom code.</span></span> <span data-ttu-id="362ba-148">Das Beispiel verwendet SHA1-Dateihashing (*Utilities/Utilities.cs*):</span><span class="sxs-lookup"><span data-stu-id="362ba-148">The sample uses SHA1 file hashing (*Utilities/Utilities.cs*):</span></span>

   [!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet1)]

   <span data-ttu-id="362ba-149">Eine Wiederholung wird mit einem exponentiellen Backoff implementiert.</span><span class="sxs-lookup"><span data-stu-id="362ba-149">A retry is implemented with an exponential back-off.</span></span> <span data-ttu-id="362ba-150">Die Wiederholung ist vorhanden, da Dateisperren auftreten können, die vorübergehend das Berechnen eines neuen Hashs auf einer der Dateien verhindert.</span><span class="sxs-lookup"><span data-stu-id="362ba-150">The re-try is present because file locking may occur that temporarily prevents computing a new hash on one of the files.</span></span>

### <a name="simple-startup-change-token"></a><span data-ttu-id="362ba-151">Einfaches Starten von Änderungstoken</span><span class="sxs-lookup"><span data-stu-id="362ba-151">Simple startup change token</span></span>

<span data-ttu-id="362ba-152">Registrieren Sie einen `Action`-Rückruf eines Tokennutzer für Änderungsbenachrichtigungen an das Token zum Neuladen der Konfiguration (*Startup.cs*):</span><span class="sxs-lookup"><span data-stu-id="362ba-152">Register a token consumer `Action` callback for change notifications to the configuration reload token (*Startup.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet2)]

<span data-ttu-id="362ba-153">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="362ba-153">`config.GetReloadToken()` provides the token.</span></span> <span data-ttu-id="362ba-154">Der Rückruf ist die `InvokeChanged`-Methode:</span><span class="sxs-lookup"><span data-stu-id="362ba-154">The callback is the `InvokeChanged` method:</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet3)]

<span data-ttu-id="362ba-155">Der `state` des Rückrufs wird zum Übergang in die `IHostingEnvironment` verwendet.</span><span class="sxs-lookup"><span data-stu-id="362ba-155">The `state` of the callback is used to pass in the `IHostingEnvironment`.</span></span> <span data-ttu-id="362ba-156">Dies ist hilfreich, um die richtige, zu überwachende *appSettings*-JSON-Konfigurationsdatei zu definieren *appSettings.&lt;Umgebung&gt;.json*.</span><span class="sxs-lookup"><span data-stu-id="362ba-156">This is useful to determine the correct *appsettings* configuration JSON file to monitor, *appsettings.&lt;Environment&gt;.json*.</span></span> <span data-ttu-id="362ba-157">Dateihashes werden verwendet, um zu verhindern, dass die `WriteConsole`-Anweisung mehrere Male ausgeführt wird. Dies liegt an mehreren Tokenrückrufen, wenn die Konfigurationsdatei nur einmal geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="362ba-157">File hashes are used to prevent the `WriteConsole` statement from running multiple times due to multiple token callbacks when the configuration file has only changed once.</span></span>

<span data-ttu-id="362ba-158">Dieses System wird so lange ausgeführt, wie die Anwendung ausgeführt wird. Es kann nicht vom Benutzer deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="362ba-158">This system runs as long as the app is running and can't be disabled by the user.</span></span>

### <a name="monitoring-configuration-changes-as-a-service"></a><span data-ttu-id="362ba-159">Überwachen von Konfigurationsänderungen als Dienst</span><span class="sxs-lookup"><span data-stu-id="362ba-159">Monitoring configuration changes as a service</span></span>

<span data-ttu-id="362ba-160">Das Beispiel implementiert:</span><span class="sxs-lookup"><span data-stu-id="362ba-160">The sample implements:</span></span>

* <span data-ttu-id="362ba-161">Grundlegendes Überwachen des Starttokens.</span><span class="sxs-lookup"><span data-stu-id="362ba-161">Basic startup token monitoring.</span></span>
* <span data-ttu-id="362ba-162">Überwachung als Dienst.</span><span class="sxs-lookup"><span data-stu-id="362ba-162">Monitoring as a service.</span></span>
* <span data-ttu-id="362ba-163">Ein Mechanismus zum Aktivieren und Deaktivieren des Monitoring.</span><span class="sxs-lookup"><span data-stu-id="362ba-163">A mechanism to enable and disable monitoring.</span></span>

<span data-ttu-id="362ba-164">Das Beispiel etabliert eine `IConfigurationMonitor`-Schnittstelle (*Extensions/ConfigurationMonitor.cs*):</span><span class="sxs-lookup"><span data-stu-id="362ba-164">The sample establishes an `IConfigurationMonitor` interface (*Extensions/ConfigurationMonitor.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet1)]

<span data-ttu-id="362ba-165">Der Konstruktor der implementierten `ConfigurationMonitor`-Klasse registriert einen Rückruf für Änderungsbenachrichtigungen:</span><span class="sxs-lookup"><span data-stu-id="362ba-165">The constructor of the implemented class, `ConfigurationMonitor`, registers a callback for change notifications:</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet2)]

<span data-ttu-id="362ba-166">`config.GetReloadToken()` stellt das Token bereit.</span><span class="sxs-lookup"><span data-stu-id="362ba-166">`config.GetReloadToken()` supplies the token.</span></span> <span data-ttu-id="362ba-167">`InvokeChanged` ist die Rückrufmethode.</span><span class="sxs-lookup"><span data-stu-id="362ba-167">`InvokeChanged` is the callback method.</span></span> <span data-ttu-id="362ba-168">In dieser Instanz stellt `state` einen Verweis auf die `IConfigurationMonitor`-Instanz dar, die verwendet wird, um auf den Überwachungsstatus zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="362ba-168">The `state` in this instance is a reference to the `IConfigurationMonitor` instance that is used to access the monitoring state.</span></span> <span data-ttu-id="362ba-169">Es werden zwei Eigenschaften verwendet:</span><span class="sxs-lookup"><span data-stu-id="362ba-169">Two properties are used:</span></span>

* <span data-ttu-id="362ba-170">`MonitoringEnabled` gibt an, ob der Rückruf seinen benutzerdefinierten Codes ausführen soll.</span><span class="sxs-lookup"><span data-stu-id="362ba-170">`MonitoringEnabled` indicates if the callback should run its custom code.</span></span>
* <span data-ttu-id="362ba-171">`CurrentState` beschreibt den aktuellen Überwachungsstatus für die Verwendung in der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="362ba-171">`CurrentState` describes the current monitoring state for use in the UI.</span></span>

<span data-ttu-id="362ba-172">Die `InvokeChanged`-Methode ist vergleichbar mit dem früheren Ansatz, außer dass sie:</span><span class="sxs-lookup"><span data-stu-id="362ba-172">The `InvokeChanged` method is similar to the earlier approach, except that it:</span></span>

* <span data-ttu-id="362ba-173">Den Code nicht ausführt, es sei denn, `MonitoringEnabled` ist `true`.</span><span class="sxs-lookup"><span data-stu-id="362ba-173">Doesn't run its code unless `MonitoringEnabled` is `true`.</span></span>
* <span data-ttu-id="362ba-174">Den aktuellen `state` in seiner `WriteConsole`-Ausgabe berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="362ba-174">Notes the current `state` in its `WriteConsole` output.</span></span>

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet3)]

<span data-ttu-id="362ba-175">Eine `ConfigurationMonitor`-Instanz wird von *Startup.cs* in `ConfigureServices` als Dienst registriert:</span><span class="sxs-lookup"><span data-stu-id="362ba-175">An instance `ConfigurationMonitor` is registered as a service in `ConfigureServices` of *Startup.cs*:</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet1)]

<span data-ttu-id="362ba-176">Die Indexseite bietet das Benutzer Kontrolle über die Konfigurationsüberwachung.</span><span class="sxs-lookup"><span data-stu-id="362ba-176">The Index page offers the user control over configuration monitoring.</span></span> <span data-ttu-id="362ba-177">Die `IConfigurationMonitor`-Instanz wird in das `IndexModel` eingefügt:</span><span class="sxs-lookup"><span data-stu-id="362ba-177">The instance of `IConfigurationMonitor` is injected into the `IndexModel`:</span></span>

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="362ba-178">Eine Schaltfläche aktiviert und deaktiviert die Überwachung:</span><span class="sxs-lookup"><span data-stu-id="362ba-178">A button enables and disables monitoring:</span></span>

[!code-cshtml[](change-tokens/sample/Pages/Index.cshtml?range=35)]

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet2)]

<span data-ttu-id="362ba-179">Wenn `OnPostStartMonitoring` ausgelöst wird, wird die Überwachung aktiviert, und der aktuelle Status ist deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="362ba-179">When `OnPostStartMonitoring` is triggered, monitoring is enabled, and the current state is cleared.</span></span> <span data-ttu-id="362ba-180">Wenn `OnPostStopMonitoring` ausgelöst wird, wird Überwachung deaktiviert, und der Zustand zeigt, dass die Überwachung nicht ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="362ba-180">When `OnPostStopMonitoring` is triggered, monitoring is disabled, and the state is set to reflect that monitoring isn't occurring.</span></span>

## <a name="monitoring-cached-file-changes"></a><span data-ttu-id="362ba-181">Überwachen zwischengespeicherter Dateiänderungen</span><span class="sxs-lookup"><span data-stu-id="362ba-181">Monitoring cached file changes</span></span>

<span data-ttu-id="362ba-182">Der Inhalt der Datei kann mithilfe von [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) speicherintern zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="362ba-182">File content can be cached in-memory using [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache).</span></span> <span data-ttu-id="362ba-183">Zwischenspeicherung im Arbeitsspeicher wird im Thema [Cache in-memory (Zwischenspeicherung im Arbeitsspeicher)](xref:performance/caching/memory) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="362ba-183">In-memory caching is described in the [Cache in-memory](xref:performance/caching/memory) topic.</span></span> <span data-ttu-id="362ba-184">Ohne zusätzliche Schritte, wie der unten beschriebenen Implementierung, werden *veraltete* Daten aus einem Zwischenspeicher zurückgegeben, wenn sich die Quelldaten ändern.</span><span class="sxs-lookup"><span data-stu-id="362ba-184">Without taking additional steps, such as the implementation described below, *stale* (outdated) data is returned from a cache if the source data changes.</span></span>

<span data-ttu-id="362ba-185">Wird der Status einer zwischengespeicherten Quelldatei beim Erneuern eines [variablen Ablaufzeitraums](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) nicht berücksichtigt, führt dies zu veralteten Cachedaten.</span><span class="sxs-lookup"><span data-stu-id="362ba-185">Not taking into account the status of a cached source file when renewing a [sliding expiration](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) period leads to stale cache data.</span></span> <span data-ttu-id="362ba-186">Jede Datenanforderung erneuert den variablen Ablaufzeitraum, aber die Datei wird nie neu in den Zwischenspeicher geladen.</span><span class="sxs-lookup"><span data-stu-id="362ba-186">Each request for the data renews the sliding expiration period, but the file is never reloaded into the cache.</span></span> <span data-ttu-id="362ba-187">Alle Anwendungsfeatures, die den zwischengespeicherten Inhalt der Datei verwenden, erhalten möglicherweise veraltete Inhalte.</span><span class="sxs-lookup"><span data-stu-id="362ba-187">Any app features that use the file's cached content are subject to possibly receiving stale content.</span></span>

<span data-ttu-id="362ba-188">Wenn Sie Änderungstoken in einer Dateizwischenspeicherung verwenden, wird verhindert, dass veraltete Dateiinhalte im Zwischenspeicher auftreten.</span><span class="sxs-lookup"><span data-stu-id="362ba-188">Using change tokens in a file caching scenario prevents stale file content in the cache.</span></span> <span data-ttu-id="362ba-189">Die Beispielanwendung zeigt eine Implementierung des Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="362ba-189">The sample app demonstrates an implementation of the approach.</span></span>

<span data-ttu-id="362ba-190">Das Beispiel verwendet `GetFileContent`, um:</span><span class="sxs-lookup"><span data-stu-id="362ba-190">The sample uses `GetFileContent` to:</span></span>

* <span data-ttu-id="362ba-191">Dateiinhalt zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="362ba-191">Return file content.</span></span>
* <span data-ttu-id="362ba-192">Einen Wiederholungsalgorithmus mit exponentiellen Backoff zu implementieren, um Fälle abzudecken, in denen eine Dateisperre vorübergehend verhindert, dass eine Datei gelesen wird.</span><span class="sxs-lookup"><span data-stu-id="362ba-192">Implement a retry algorithm with exponential back-off to cover cases where a file lock is temporarily preventing a file from being read.</span></span>

<span data-ttu-id="362ba-193">*Utilities/Utilities.cs*:</span><span class="sxs-lookup"><span data-stu-id="362ba-193">*Utilities/Utilities.cs*:</span></span>

[!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet2)]

<span data-ttu-id="362ba-194">Ein `FileService` wird erstellt, um die zwischengespeicherten Dateisuchvorgänge zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="362ba-194">A `FileService` is created to handle cached file lookups.</span></span> <span data-ttu-id="362ba-195">Der `GetFileContent`-Methodenaufruf des Diensts versucht, den Dateiinhalt aus dem speicherinternen Cache abzurufen und ihn an den Aufrufer zurückzugeben (*Services/FileService.cs*).</span><span class="sxs-lookup"><span data-stu-id="362ba-195">The `GetFileContent` method call of the service attempts to obtain file content from the in-memory cache and return it to the caller (*Services/FileService.cs*).</span></span>

<span data-ttu-id="362ba-196">Wenn mithilfe des Cacheschlüssels zwischengespeicherte Inhalte nicht gefunden werden, werden folgende Aktionen durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="362ba-196">If cached content isn't found using the cache key, the following actions are taken:</span></span>

1. <span data-ttu-id="362ba-197">Der Inhalt der Datei wird mit `GetFileContent` abgerufen.</span><span class="sxs-lookup"><span data-stu-id="362ba-197">The file content is obtained using `GetFileContent`.</span></span>
1. <span data-ttu-id="362ba-198">Ein Änderungstoken wird mithilfe von [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) aus dem Dateianbieter abgerufen.</span><span class="sxs-lookup"><span data-stu-id="362ba-198">A change token is obtained from the file provider with [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch).</span></span> <span data-ttu-id="362ba-199">Wenn die Datei geändert wird, wird ein Rückruf des Tokens ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="362ba-199">The token's callback is triggered when the file is modified.</span></span>
1. <span data-ttu-id="362ba-200">Der Inhalt der Datei wird mit einem [variablen Ablaufzeitraum](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="362ba-200">The file content is cached with a [sliding expiration](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) period.</span></span> <span data-ttu-id="362ba-201">Das Änderungstoken wird mit [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) angefügt, um den Cacheeintrag zu entfernen, wenn die sich Datei ändert, während sie zwischengespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="362ba-201">The change token is attached with [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) to evict the cache entry if the file changes while it's cached.</span></span>

[!code-csharp[](change-tokens/sample/Services/FileService.cs?name=snippet1)]

<span data-ttu-id="362ba-202">Der `FileService` ist zusammen mit dem Speichercachedienst (*Startup.cs*) im Dienstcontainer registriert:</span><span class="sxs-lookup"><span data-stu-id="362ba-202">The `FileService` is registered in the service container along with the memory caching service (*Startup.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet4)]

<span data-ttu-id="362ba-203">Das Seitenmodell lädt mithilfe des Diensts (*Pages/Index.cshtml.cs*) den Inhalt der Datei:</span><span class="sxs-lookup"><span data-stu-id="362ba-203">The page model loads the file's content using the service (*Pages/Index.cshtml.cs*):</span></span>

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a><span data-ttu-id="362ba-204">CompositeChangeToken-Klasse</span><span class="sxs-lookup"><span data-stu-id="362ba-204">CompositeChangeToken class</span></span>

<span data-ttu-id="362ba-205">Verwenden Sie für die Darstellung von einer oder mehreren `IChangeToken`-Instanzen in einem einzelnen Objekt die Klasse [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken).</span><span class="sxs-lookup"><span data-stu-id="362ba-205">For representing one or more `IChangeToken` instances in a single object, use the [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken) class.</span></span>

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

<span data-ttu-id="362ba-206">`HasChanged` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `HasChanged`-Token `true` ist.</span><span class="sxs-lookup"><span data-stu-id="362ba-206">`HasChanged` on the composite token reports `true` if any represented token `HasChanged` is `true`.</span></span> <span data-ttu-id="362ba-207">`ActiveChangeCallbacks` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `ActiveChangeCallbacks`-Token `true` ist.</span><span class="sxs-lookup"><span data-stu-id="362ba-207">`ActiveChangeCallbacks` on the composite token reports `true` if any represented token `ActiveChangeCallbacks` is `true`.</span></span> <span data-ttu-id="362ba-208">Wenn mehrere gleichzeitige Änderungsereignisse auftreten, wird der Rückruf für die kombinierte Änderung genau einmal aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="362ba-208">If multiple concurrent change events occur, the composite change callback is invoked exactly one time.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="362ba-209">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="362ba-209">Additional resources</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
