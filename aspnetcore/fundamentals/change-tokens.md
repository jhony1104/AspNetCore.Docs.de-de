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
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>Erkennen von Änderungen mit Änderungstoken in ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

Ein *Änderungstoken* ist ein allgemeiner Baustein auf niedriger Ebene zum Nachverfolgen von Änderungen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/sample/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="ichangetoken-interface"></a>IChangeToken-Schnittstelle

[IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) verbreitet Benachrichtigungen, wenn eine Änderung aufgetreten ist. `IChangeToken` befindet sich im [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives)-Namespace. Für Anwendungen, die das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 oder höher) nicht verwenden, verweisen Sie auf das NuGet-Paket [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) in der Projektdatei.

`IChangeToken` verfügt über zwei Eigenschaften:

* [ActiveChangedCallbacks](/dotnet/api/microsoft.extensions.primitives.ichangetoken.activechangecallbacks) geben an, ob das Token proaktiv Rückrufe auslöst. Wenn `ActiveChangedCallbacks` auf `false` festgelegt ist, wird ein Rückruf nie aufgerufen, und die Anwendung muss `HasChanged` für Änderungen abrufen. Es ist auch möglich, dass ein Token nie abgebrochen wird, wenn keine Änderungen auftreten oder der zugrunde liegenden Änderungslistener gelöscht oder deaktiviert wird.
* [HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) ruft einen Wert auf, der angibt, ob eine Änderung aufgetreten ist.

Die Schnittstelle verfügt über eine Methode, [RegisterChangeCallback(Action&lt;Object&gt;, Object)](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback), die einen Rückruf registriert, der aufgerufen wird, wenn das Token geändert wurde. `HasChanged` muss festgelegt werden, bevor der Rückruf aufgerufen wird.

## <a name="changetoken-class"></a>ChangeToken-Klasse

`ChangeToken` ist eine statische Klasse, die zur Verbreitung von Änderungsbenachrichtigungen verwendet wird. `ChangeToken` befindet sich im [Microsoft.Extensions.Primitives](/dotnet/api/microsoft.extensions.primitives)-Namespace. Für Anwendungen, die das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nicht verwenden, verweisen Sie auf das NuGet-Paket [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/) in der Projektdatei.

Die `ChangeToken` [OnChange (Func&lt;IChangeToken&gt;, Action)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action_)-Methode registriert eine aufzurufende `Action`, wenn das Token geändert wird:

* `Func<IChangeToken>` erzeugt das Token.
* `Action` wird aufgerufen, wenn das Token geändert wird.

`ChangeToken` verfügt über eine [OnChange&lt;TState&gt;(Func&lt;IChangeToken&gt;, Action&lt;TState&gt;, TState)](/dotnet/api/microsoft.extensions.primitives.changetoken.onchange?view=aspnetcore-2.0#Microsoft_Extensions_Primitives_ChangeToken_OnChange__1_System_Func_Microsoft_Extensions_Primitives_IChangeToken__System_Action___0____0_)-Überladung mit einem zusätzlichen `TState`-Parameter, das an den Tokennutzer `Action` übergeben wird.

`OnChange` gibt [IDisposable](/dotnet/api/system.idisposable) zurück. Der Aufruf von [Dispose](/dotnet/api/system.idisposable.dispose) beendet den Überwachungsvorgang des Tokens für weitere Änderungen und gibt die Ressourcen des Tokens frei.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>Beispielverwendungen von Änderungstoken in ASP.NET Core

Änderungstoken werden in gut sichtbaren Bereichen von ASP.NET Core verwendet. Sie überwachen Objektänderungen:

* Für die Überwachung von Änderungen der Dateien erstellt die [Überwachen](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch)-Methode des [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) ein `IChangeToken` zur Verfügung, das die angegebenen Dateien oder Ordner überwacht.
* `IChangeToken`-Token können zu Cacheeinträgen hinzugefügt werden, um bei Änderungen Cacheentfernungen auszulösen.
* Für `TOptions`-Änderungen weist die Standard-[OptionsMonitor](/dotnet/api/microsoft.extensions.options.optionsmonitor-1)-Implementierung von [IOptionsMonitor](/dotnet/api/microsoft.extensions.options.ioptionsmonitor-1) eine Überladung auf, die eine oder mehrere [IOptionsChangeTokenSource](/dotnet/api/microsoft.extensions.options.ioptionschangetokensource-1)-Instanzen akzeptiert. Jede Instanz gibt ein `IChangeToken` zurück, um den Rückruf einer Änderungsbenachrichtigung zur Nachverfolgung von Optionsänderungen zu registrieren.

## <a name="monitoring-for-configuration-changes"></a>Überwachen von Konfigurationsänderungen

ASP.NET Core-Vorlagen verwenden standardmäßig [JSON-Konfigurationsdateien](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* und *appsettings.Production.json*), um Konfigurationseinstellungen der Anwendungen zu laden.

Diese Dateien werden mithilfe der [AddJsonFile (IConfigurationBuilder, String, Boolean, Boolean)](/dotnet/api/microsoft.extensions.configuration.jsonconfigurationextensions.addjsonfile#Microsoft_Extensions_Configuration_JsonConfigurationExtensions_AddJsonFile_Microsoft_Extensions_Configuration_IConfigurationBuilder_System_String_System_Boolean_System_Boolean_)-Erweiterungsmethode auf [ConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.configurationbuilder) konfiguriert, der einen `reloadOnChange`-Parameter (ASP.NET Core 1.1 und höher) akzeptiert. `reloadOnChange` gibt an, ob Konfigurationen auf Dateiänderungen neu geladen werden soll. Diese Einstellung sehen Sie in der [WebHost](/dotnet/api/microsoft.aspnetcore.webhost)-Hilfsmethode [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder):

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, reloadOnChange: true);
```

Dateibasierte Konfiguration wird von [FileConfigurationSource](/dotnet/api/microsoft.extensions.configuration.fileconfigurationsource) dargestellt. `FileConfigurationSource` verwendet [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) zur Überwachung von Dateien.

`IFileMonitor` wird standardmäßig von einem [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) bereitgestellt, der [FileSystemWatcher](/dotnet/api/system.io.filesystemwatcher) zum Überwachen der Änderungen von Konfigurationsdateien verwendet.

Die Beispielanwendung veranschaulicht zwei Implementierungen für die Überwachung von Konfigurationsänderungen. Wenn entweder die *appsettings.json*-Datei oder die Umgebungsversion der Datei geändert wird, führt jede Implementierung benutzerdefinierten Code aus. Die Beispielanwendung schreibt eine Nachricht an die Konsole.

Der `FileSystemWatcher` einer Konfigurationsdatei kann mehrere Tokenrückrufe für eine einzelne Dateikonfigurationsänderung auslösen. Die Implementierung des Beispiels schützt vor diesem Problem, indem die Dateihashes der Konfigurationsdateien überprüft werden. Die Überprüfung von Dateihashes stellt sicher, dass mindestens eine der Konfigurationsdateien geändert wurde, bevor der benutzerdefinierte Code ausgeführt wird. Das Beispiel verwendet SHA1-Dateihashing (*Utilities/Utilities.cs*):

   [!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet1)]

   Eine Wiederholung wird mit einem exponentiellen Backoff implementiert. Die Wiederholung ist vorhanden, da Dateisperren auftreten können, die vorübergehend das Berechnen eines neuen Hashs auf einer der Dateien verhindert.

### <a name="simple-startup-change-token"></a>Einfaches Starten von Änderungstoken

Registrieren Sie einen `Action`-Rückruf eines Tokennutzer für Änderungsbenachrichtigungen an das Token zum Neuladen der Konfiguration (*Startup.cs*):

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet2)]

`config.GetReloadToken()` stellt das Token bereit. Der Rückruf ist die `InvokeChanged`-Methode:

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet3)]

Der `state` des Rückrufs wird zum Übergang in die `IHostingEnvironment` verwendet. Dies ist hilfreich, um die richtige, zu überwachende *appSettings*-JSON-Konfigurationsdatei zu definieren *appSettings.&lt;Umgebung&gt;.json*. Dateihashes werden verwendet, um zu verhindern, dass die `WriteConsole`-Anweisung mehrere Male ausgeführt wird. Dies liegt an mehreren Tokenrückrufen, wenn die Konfigurationsdatei nur einmal geändert wurde.

Dieses System wird so lange ausgeführt, wie die Anwendung ausgeführt wird. Es kann nicht vom Benutzer deaktiviert werden.

### <a name="monitoring-configuration-changes-as-a-service"></a>Überwachen von Konfigurationsänderungen als Dienst

Das Beispiel implementiert:

* Grundlegendes Überwachen des Starttokens.
* Überwachung als Dienst.
* Ein Mechanismus zum Aktivieren und Deaktivieren des Monitoring.

Das Beispiel etabliert eine `IConfigurationMonitor`-Schnittstelle (*Extensions/ConfigurationMonitor.cs*):

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Der Konstruktor der implementierten `ConfigurationMonitor`-Klasse registriert einen Rückruf für Änderungsbenachrichtigungen:

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet2)]

`config.GetReloadToken()` stellt das Token bereit. `InvokeChanged` ist die Rückrufmethode. In dieser Instanz stellt `state` einen Verweis auf die `IConfigurationMonitor`-Instanz dar, die verwendet wird, um auf den Überwachungsstatus zuzugreifen. Es werden zwei Eigenschaften verwendet:

* `MonitoringEnabled` gibt an, ob der Rückruf seinen benutzerdefinierten Codes ausführen soll.
* `CurrentState` beschreibt den aktuellen Überwachungsstatus für die Verwendung in der Benutzeroberfläche.

Die `InvokeChanged`-Methode ist vergleichbar mit dem früheren Ansatz, außer dass sie:

* Den Code nicht ausführt, es sei denn, `MonitoringEnabled` ist `true`.
* Den aktuellen `state` in seiner `WriteConsole`-Ausgabe berücksichtigt.

[!code-csharp[](change-tokens/sample/Extensions/ConfigurationMonitor.cs?name=snippet3)]

Eine `ConfigurationMonitor`-Instanz wird von *Startup.cs* in `ConfigureServices` als Dienst registriert:

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet1)]

Die Indexseite bietet das Benutzer Kontrolle über die Konfigurationsüberwachung. Die `IConfigurationMonitor`-Instanz wird in das `IndexModel` eingefügt:

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet1)]

Eine Schaltfläche aktiviert und deaktiviert die Überwachung:

[!code-cshtml[](change-tokens/sample/Pages/Index.cshtml?range=35)]

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet2)]

Wenn `OnPostStartMonitoring` ausgelöst wird, wird die Überwachung aktiviert, und der aktuelle Status ist deaktiviert. Wenn `OnPostStopMonitoring` ausgelöst wird, wird Überwachung deaktiviert, und der Zustand zeigt, dass die Überwachung nicht ausgeführt wird.

## <a name="monitoring-cached-file-changes"></a>Überwachen zwischengespeicherter Dateiänderungen

Der Inhalt der Datei kann mithilfe von [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) speicherintern zwischengespeichert werden. Zwischenspeicherung im Arbeitsspeicher wird im Thema [Cache in-memory (Zwischenspeicherung im Arbeitsspeicher)](xref:performance/caching/memory) beschrieben. Ohne zusätzliche Schritte, wie der unten beschriebenen Implementierung, werden *veraltete* Daten aus einem Zwischenspeicher zurückgegeben, wenn sich die Quelldaten ändern.

Wird der Status einer zwischengespeicherten Quelldatei beim Erneuern eines [variablen Ablaufzeitraums](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) nicht berücksichtigt, führt dies zu veralteten Cachedaten. Jede Datenanforderung erneuert den variablen Ablaufzeitraum, aber die Datei wird nie neu in den Zwischenspeicher geladen. Alle Anwendungsfeatures, die den zwischengespeicherten Inhalt der Datei verwenden, erhalten möglicherweise veraltete Inhalte.

Wenn Sie Änderungstoken in einer Dateizwischenspeicherung verwenden, wird verhindert, dass veraltete Dateiinhalte im Zwischenspeicher auftreten. Die Beispielanwendung zeigt eine Implementierung des Ansatzes.

Das Beispiel verwendet `GetFileContent`, um:

* Dateiinhalt zurückzugeben.
* Einen Wiederholungsalgorithmus mit exponentiellen Backoff zu implementieren, um Fälle abzudecken, in denen eine Dateisperre vorübergehend verhindert, dass eine Datei gelesen wird.

*Utilities/Utilities.cs*:

[!code-csharp[](change-tokens/sample/Utilities/Utilities.cs?name=snippet2)]

Ein `FileService` wird erstellt, um die zwischengespeicherten Dateisuchvorgänge zu behandeln. Der `GetFileContent`-Methodenaufruf des Diensts versucht, den Dateiinhalt aus dem speicherinternen Cache abzurufen und ihn an den Aufrufer zurückzugeben (*Services/FileService.cs*).

Wenn mithilfe des Cacheschlüssels zwischengespeicherte Inhalte nicht gefunden werden, werden folgende Aktionen durchgeführt:

1. Der Inhalt der Datei wird mit `GetFileContent` abgerufen.
1. Ein Änderungstoken wird mithilfe von [IFileProviders.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) aus dem Dateianbieter abgerufen. Wenn die Datei geändert wird, wird ein Rückruf des Tokens ausgelöst.
1. Der Inhalt der Datei wird mit einem [variablen Ablaufzeitraum](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.slidingexpiration) zwischengespeichert. Das Änderungstoken wird mit [MemoryCacheEntryExtensions.AddExpirationToken](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.addexpirationtoken) angefügt, um den Cacheeintrag zu entfernen, wenn die sich Datei ändert, während sie zwischengespeichert wird.

[!code-csharp[](change-tokens/sample/Services/FileService.cs?name=snippet1)]

Der `FileService` ist zusammen mit dem Speichercachedienst (*Startup.cs*) im Dienstcontainer registriert:

[!code-csharp[](change-tokens/sample/Startup.cs?name=snippet4)]

Das Seitenmodell lädt mithilfe des Diensts (*Pages/Index.cshtml.cs*) den Inhalt der Datei:

[!code-csharp[](change-tokens/sample/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>CompositeChangeToken-Klasse

Verwenden Sie für die Darstellung von einer oder mehreren `IChangeToken`-Instanzen in einem einzelnen Objekt die Klasse [CompositeChangeToken](/dotnet/api/microsoft.extensions.primitives.compositechangetoken).

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

`HasChanged` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `HasChanged`-Token `true` ist. `ActiveChangeCallbacks` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `ActiveChangeCallbacks`-Token `true` ist. Wenn mehrere gleichzeitige Änderungsereignisse auftreten, wird der Rückruf für die kombinierte Änderung genau einmal aufgerufen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
