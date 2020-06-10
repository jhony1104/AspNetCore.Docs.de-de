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
# <a name="detect-changes-with-change-tokens-in-aspnet-core"></a>'Identity'

::: moniker range=">= aspnetcore-3.0"

'Let's Encrypt'

'Razor'

## <a name="ichangetoken-interface"></a>'SignalR' uid:

Erkennen von Änderungen mit Änderungstoken in ASP.NET Core Ein *Änderungstoken* ist ein universeller Baustein auf niedriger Ebene, mit dem Änderungen nachverfolgt werden können. [Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

IChangeToken-Schnittstelle

* <xref:Microsoft.Extensions.Primitives.IChangeToken> verbreitet Benachrichtigungen, wenn eine Änderung aufgetreten ist. `IChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten. Das [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/)-NuGet-Paket wird implizit für die ASP.NET Core-Apps bereitgestellt.
* `IChangeToken` verfügt über zwei Eigenschaften:

<xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> geben an, ob das Token proaktiv Rückrufe auslöst. Wenn `ActiveChangedCallbacks` auf `false` festgelegt ist, wird ein Rückruf nie aufgerufen, und die Anwendung muss `HasChanged` für Änderungen abrufen.

## <a name="changetoken-class"></a>Es ist auch möglich, dass ein Token nie abgebrochen wird, wenn keine Änderungen auftreten oder der zugrunde liegenden Änderungslistener gelöscht oder deaktiviert wird.

<xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> ruft einen Wert auf, der angibt, ob eine Änderung aufgetreten ist. Die `IChangeToken`-Schnittstelle verfügt über die Methode [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*). Diese registriert einen Rückruf, der bei einer Tokenänderung aufgerufen wird. `HasChanged` muss festgelegt werden, bevor der Rückruf aufgerufen wird.

ChangeToken-Klasse

* <xref:Microsoft.Extensions.Primitives.ChangeToken> ist eine statische Klasse, die zur Verbreitung von Änderungsbenachrichtigungen verwendet wird.
* `ChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.

Das [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/)-NuGet-Paket wird implizit für die ASP.NET Core-Apps bereitgestellt.

Die Methode [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registriert eine bei einer Tokenänderung aufzurufende `Action`: `Func<IChangeToken>` erzeugt das Token.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>`Action` wird aufgerufen, wenn das Token geändert wird.

Die Überladung [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) akzeptiert einen zusätzlichen `TState`-Parameter, der an die Tokenconsumer-`Action` übergeben wird.

* <xref:System.IDisposable> gibt `OnChange` zurück.
* Der Aufruf von <xref:System.IDisposable.Dispose*> beendet den Überwachungsvorgang des Tokens auf weitere Änderungen und gibt die Tokenressourcen frei.
* Beispielverwendungen von Änderungstoken in ASP.NET Core Änderungstoken werden in wichtigen Bereichen von ASP.NET Core verwendet, um auf Objektänderungen zu überwachen:

## <a name="monitor-for-configuration-changes"></a>Für die Überwachung von Änderungen an Dateien erstellt die <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*>-Methode von <xref:Microsoft.Extensions.FileProviders.IFileProvider> ein `IChangeToken` für die angegebenen Dateien oder einen Ordner, der überwacht werden soll.

`IChangeToken`-Token können zu Cacheeinträgen hinzugefügt werden, um bei Änderungen Cacheentfernungen auszulösen.

Für `TOptions`-Änderungen umfasst die <xref:Microsoft.Extensions.Options.OptionsMonitor`1>-Standardimplementierung von <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> eine Überladung, die eine oder mehrere <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>-Instanzen akzeptiert. Jede Instanz gibt ein `IChangeToken` zurück, um den Rückruf einer Änderungsbenachrichtigung zur Nachverfolgung von Optionsänderungen zu registrieren. Überwachen von Konfigurationsänderungen

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

ASP.NET Core-Vorlagen verwenden standardmäßig [JSON-Konfigurationsdateien](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* und *appsettings.Production.json*), um Konfigurationseinstellungen der Anwendungen zu laden. Diese Dateien werden mithilfe der [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*)-Erweiterungsmethode für <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> konfiguriert, die einen `reloadOnChange`-Parameter akzeptiert.

`reloadOnChange` gibt an, ob Konfigurationen auf Dateiänderungen neu geladen werden soll.

Diese Einstellung wird in der <xref:Microsoft.Extensions.Hosting.Host>-Hilfsmethode <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*> verwendet: Die dateibasierte Konfiguration wird durch <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> repräsentiert.

`FileConfigurationSource` verwendet <xref:Microsoft.Extensions.FileProviders.IFileProvider> zum Überwachen von Dateien. `IFileMonitor` wird standardmäßig von einem <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> bereitgestellt, der zum Überwachen auf Konfigurationsdateiänderungen <xref:System.IO.FileSystemWatcher> verwendet. Die Beispielanwendung veranschaulicht zwei Implementierungen für die Überwachung von Konfigurationsänderungen. Wenn sich eine beliebige der *appsettings*-Dateien ändert, führen beide Dateiüberwachungsimplementierungen benutzerdefinierten Code aus – die Beispiel-App gibt eine Meldung an die Konsole aus. Der `FileSystemWatcher` einer Konfigurationsdatei kann mehrere Tokenrückrufe für eine einzelne Dateikonfigurationsänderung auslösen.

Um sicherzustellen, dass der benutzerdefinierte Code bei Auslösung mehrerer Tokenrückrufe nur einmal ausgeführt wird, überprüft die Beispielimplementierung Dateihashes.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Das Beispiel verwendet SHA1-Dateihashing.

Eine Wiederholung wird mit einem exponentiellen Backoff implementiert.

Die Wiederholung ist vorhanden, weil Dateisperren auftreten können, die vorübergehend das Berechnen eines neuen Hashs für eine der Dateien verhindern.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

*Utilities/Utilities.cs*: Einfaches Starten von Änderungstoken

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

Registrieren Sie einen `Action`-Rückruf eines Tokenconsumers für Änderungsbenachrichtigungen an das Token zum Neuladen der Konfiguration. In `Startup.Configure`:

`config.GetReloadToken()` stellt das Token bereit.

### <a name="monitor-configuration-changes-as-a-service"></a>Der Rückruf ist die `InvokeChanged`-Methode:

Der `state` des Rückrufs wird zur Übergabe an die `IWebHostEnvironment` verwendet. Dies ist hilfreich, um die richtige *appsettings*-Konfigurationsdatei anzugeben, die überwacht werden soll (in der Entwicklungsumgebung beispielsweise *appsettings.Development.json*).

* Dateihashes werden verwendet, um zu verhindern, dass die `WriteConsole`-Anweisung mehrere Male ausgeführt wird. Dies liegt an mehreren Tokenrückrufen, wenn die Konfigurationsdatei nur einmal geändert wurde.
* Dieses System wird so lange ausgeführt, wie die Anwendung ausgeführt wird. Es kann nicht vom Benutzer deaktiviert werden.
* Überwachen von Konfigurationsänderungen als Dienst

Das Beispiel implementiert:

Grundlegendes Überwachen des Starttokens.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Überwachung als Dienst.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

Ein Mechanismus zum Aktivieren und Deaktivieren des Monitoring. In diesem Beispiel wird eine `IConfigurationMonitor`-Schnittstelle eingerichtet. *Extensions/ConfigurationMonitor.cs*: Der Konstruktor der implementierten `ConfigurationMonitor`-Klasse registriert einen Rückruf für Änderungsbenachrichtigungen:

* `config.GetReloadToken()` stellt das Token bereit.
* `InvokeChanged` ist die Rückrufmethode.

In dieser Instanz stellt `state` einen Verweis auf die `IConfigurationMonitor`-Instanz dar, die zum Zugriff auf den Überwachungsstatus verwendet wird.

* Es werden zwei Eigenschaften verwendet:
* `MonitoringEnabled`: Diese Eigenschaft gibt an, ob der Rückruf den zugehörigen benutzerdefinierten Code ausführen soll.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

`CurrentState`: Diese Eigenschaft beschreibt den aktuellen Überwachungsstatus zur Verwendung in der Benutzeroberfläche.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Die `InvokeChanged`-Methode ist vergleichbar mit dem früheren Ansatz, außer dass sie: Den Code nicht ausführt, es sei denn, `MonitoringEnabled` ist `true`.

Den aktuellen `state` in der zugehörigen `WriteConsole`-Ausgabe berücksichtigt.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Eine `ConfigurationMonitor`-Instanz wird in `Startup.ConfigureServices` als Dienst registriert:

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Die Indexseite bietet das Benutzer Kontrolle über die Konfigurationsüberwachung. Die `IConfigurationMonitor`-Instanz wird in das `IndexModel` eingefügt.

*Pages/Index.cshtml.cs*:

Der Konfigurationsmonitor (`_monitor`) wird verwendet, um die Überwachung zu aktivieren oder zu deaktivieren und den aktuellen Status für Benutzeroberflächenfeedback festzulegen:

[!code-cshtml[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Wenn `OnPostStartMonitoring` ausgelöst wird, wird die Überwachung aktiviert, und der aktuelle Status ist deaktiviert.

Wenn `OnPostStopMonitoring` ausgelöst wird, wird Überwachung deaktiviert, und der Zustand zeigt, dass die Überwachung nicht ausgeführt wird. Die Überwachung wird über Schaltflächen in der Benutzeroberfläche aktiviert und deaktiviert. *Pages/Index.cshtml*:

Überwachen von zwischengespeicherten Dateiänderungen Dateiinhalt kann mithilfe von <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> speicherintern zwischengespeichert werden. Zwischenspeicherung im Arbeitsspeicher wird im Thema [Cache in-memory (Zwischenspeicherung im Arbeitsspeicher)](xref:performance/caching/memory) beschrieben.

Ohne zusätzliche Schritte, wie der unten beschriebenen Implementierung, werden *veraltete* Daten aus einem Zwischenspeicher zurückgegeben, wenn sich die Quelldaten ändern. Wird beispielsweise der Status einer zwischengespeicherten Quelldatei beim Erneuern eines [variablen Ablaufzeitraums](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) nicht berücksichtigt, führt dies zu veralteten Dateidaten im Cache.

Jede Datenanforderung erneuert den variablen Ablaufzeitraum, aber die Datei wird nie neu in den Zwischenspeicher geladen.

* Alle Anwendungsfeatures, die den zwischengespeicherten Inhalt der Datei verwenden, erhalten möglicherweise veraltete Inhalte.
* Durch die Verwendung von Änderungstoken in einem Szenario mit Dateizwischenspeicherung wird verhindert, dass veraltete Dateiinhalte im Zwischenspeicher auftreten.

Die Beispielanwendung zeigt eine Implementierung des Ansatzes.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Das Beispiel verwendet `GetFileContent`, um: Dateiinhalt zurückzugeben.

Einen Wiederholungsalgorithmus mit exponentiellem Backoff zu implementieren, um Fälle abzudecken, in denen eine Dateisperre vorübergehend das Lesen einer Datei verhindert.

1. *Utilities/Utilities.cs*:
1. Ein `FileService` wird erstellt, um die zwischengespeicherten Dateisuchvorgänge zu behandeln. Der `GetFileContent`-Methodenaufruf des Diensts versucht, den Dateiinhalt aus dem speicherinternen Cache abzurufen und ihn an den Aufrufer zurückzugeben (*Services/FileService.cs*).
1. Wenn mithilfe des Cacheschlüssels zwischengespeicherte Inhalte nicht gefunden werden, werden folgende Aktionen durchgeführt: Der Inhalt der Datei wird mit `GetFileContent` abgerufen.

Ein Änderungstoken wird mithilfe von [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) aus dem Dateianbieter abgerufen. Wenn die Datei geändert wird, wird ein Rückruf des Tokens ausgelöst. Der Inhalt der Datei wird mit einem [variablen Ablaufzeitraum](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) zwischengespeichert.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Services/FileService.cs?name=snippet1)]

Das Änderungstoken wird mit [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) angefügt, um den Cacheeintrag zu entfernen, wenn die sich Datei ändert, während sie zwischengespeichert wird.

Im folgenden Beispiel werden Dateien im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App gespeichert.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

`IWebHostEnvironment.ContentRootFileProvider` wird verwendet, um einen <xref:Microsoft.Extensions.FileProviders.IFileProvider> zu erhalten, der auf den `IWebHostEnvironment.ContentRootPath` der App verweist.

Der `filePath` wird mit [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) abgerufen.

[!code-csharp[](change-tokens/samples/3.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Der `FileService` ist zusammen mit dem Speichercachedienst im Dienstcontainer registriert:

In `Startup.ConfigureServices`:

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

Das Seitenmodell lädt mithilfe des Diensts den Inhalt der Datei. In der `OnGet`-Methode auf der Indexseite (*Pages/Index.cshtml.cs*): CompositeChangeToken-Klasse

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Verwenden Sie für die Darstellung von einer oder mehreren `IChangeToken`-Instanzen in einem einzelnen Objekt die Klasse <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.

`HasChanged` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `HasChanged`-Token `true` ist.

## <a name="ichangetoken-interface"></a>`ActiveChangeCallbacks` für die kombinierten Tokenberichte ist `true`, wenn ein dargestelltes `ActiveChangeCallbacks`-Token `true` ist.

Wenn mehrere Änderungsereignisse gleichzeitig auftreten, wird der Rückruf für die kombinierte Änderung einmal aufgerufen. Ein *Änderungstoken* ist ein universeller Baustein auf niedriger Ebene, mit dem Änderungen nachverfolgt werden können. [Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/change-tokens/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

IChangeToken-Schnittstelle

* <xref:Microsoft.Extensions.Primitives.IChangeToken> verbreitet Benachrichtigungen, wenn eine Änderung aufgetreten ist. `IChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten. Für Anwendungen, die das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nicht verwenden, erstellen Sie einen Paketverweis auf das NuGet-Paket [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).
* `IChangeToken` verfügt über zwei Eigenschaften:

<xref:Microsoft.Extensions.Primitives.IChangeToken.ActiveChangeCallbacks> geben an, ob das Token proaktiv Rückrufe auslöst. Wenn `ActiveChangedCallbacks` auf `false` festgelegt ist, wird ein Rückruf nie aufgerufen, und die Anwendung muss `HasChanged` für Änderungen abrufen.

## <a name="changetoken-class"></a>Es ist auch möglich, dass ein Token nie abgebrochen wird, wenn keine Änderungen auftreten oder der zugrunde liegenden Änderungslistener gelöscht oder deaktiviert wird.

<xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> ruft einen Wert auf, der angibt, ob eine Änderung aufgetreten ist. Die `IChangeToken`-Schnittstelle verfügt über die Methode [RegisterChangeCallback(Action\<Object>, Object)](xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*). Diese registriert einen Rückruf, der bei einer Tokenänderung aufgerufen wird. `HasChanged` muss festgelegt werden, bevor der Rückruf aufgerufen wird.

ChangeToken-Klasse

* <xref:Microsoft.Extensions.Primitives.ChangeToken> ist eine statische Klasse, die zur Verbreitung von Änderungsbenachrichtigungen verwendet wird.
* `ChangeToken` ist im <xref:Microsoft.Extensions.Primitives?displayProperty=fullName>-Namespace enthalten.

Für Anwendungen, die das Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) nicht verwenden, erstellen Sie einen Paketverweis auf das NuGet-Paket [Microsoft.Extensions.Primitives](https://www.nuget.org/packages/Microsoft.Extensions.Primitives/).

Die Methode [ChangeToken.OnChange(Func\<IChangeToken>, Action)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) registriert eine bei einer Tokenänderung aufzurufende `Action`: `Func<IChangeToken>` erzeugt das Token.

## <a name="example-uses-of-change-tokens-in-aspnet-core"></a>`Action` wird aufgerufen, wenn das Token geändert wird.

Die Überladung [ChangeToken.OnChange\<TState>(Func\<IChangeToken>, Action\<TState>, TState)](xref:Microsoft.Extensions.Primitives.ChangeToken.OnChange*) akzeptiert einen zusätzlichen `TState`-Parameter, der an die Tokenconsumer-`Action` übergeben wird.

* <xref:System.IDisposable> gibt `OnChange` zurück.
* Der Aufruf von <xref:System.IDisposable.Dispose*> beendet den Überwachungsvorgang des Tokens auf weitere Änderungen und gibt die Tokenressourcen frei.
* Beispielverwendungen von Änderungstoken in ASP.NET Core Änderungstoken werden in wichtigen Bereichen von ASP.NET Core verwendet, um auf Objektänderungen zu überwachen:

## <a name="monitor-for-configuration-changes"></a>Für die Überwachung von Änderungen an Dateien erstellt die <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*>-Methode von <xref:Microsoft.Extensions.FileProviders.IFileProvider> ein `IChangeToken` für die angegebenen Dateien oder einen Ordner, der überwacht werden soll.

`IChangeToken`-Token können zu Cacheeinträgen hinzugefügt werden, um bei Änderungen Cacheentfernungen auszulösen.

Für `TOptions`-Änderungen umfasst die <xref:Microsoft.Extensions.Options.OptionsMonitor`1>-Standardimplementierung von <xref:Microsoft.Extensions.Options.IOptionsMonitor`1> eine Überladung, die eine oder mehrere <xref:Microsoft.Extensions.Options.IOptionsChangeTokenSource`1>-Instanzen akzeptiert. Jede Instanz gibt ein `IChangeToken` zurück, um den Rückruf einer Änderungsbenachrichtigung zur Nachverfolgung von Optionsänderungen zu registrieren. Überwachen von Konfigurationsänderungen

```csharp
config.AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
      .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true, 
          reloadOnChange: true);
```

ASP.NET Core-Vorlagen verwenden standardmäßig [JSON-Konfigurationsdateien](xref:fundamentals/configuration/index#json-configuration-provider) (*appsettings.json*, *appsettings.Development.json* und *appsettings.Production.json*), um Konfigurationseinstellungen der Anwendungen zu laden. Diese Dateien werden mithilfe der [AddJsonFile(IConfigurationBuilder, String, Boolean, Boolean)](xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*)-Erweiterungsmethode für <xref:Microsoft.Extensions.Configuration.ConfigurationBuilder> konfiguriert, die einen `reloadOnChange`-Parameter akzeptiert.

`reloadOnChange` gibt an, ob Konfigurationen auf Dateiänderungen neu geladen werden soll.

Diese Einstellung wird in der <xref:Microsoft.AspNetCore.WebHost>-Hilfsmethode <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> verwendet: Die dateibasierte Konfiguration wird durch <xref:Microsoft.Extensions.Configuration.FileConfigurationSource> repräsentiert.

`FileConfigurationSource` verwendet <xref:Microsoft.Extensions.FileProviders.IFileProvider> zum Überwachen von Dateien. `IFileMonitor` wird standardmäßig von einem <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> bereitgestellt, der zum Überwachen auf Konfigurationsdateiänderungen <xref:System.IO.FileSystemWatcher> verwendet. Die Beispielanwendung veranschaulicht zwei Implementierungen für die Überwachung von Konfigurationsänderungen. Wenn sich eine beliebige der *appsettings*-Dateien ändert, führen beide Dateiüberwachungsimplementierungen benutzerdefinierten Code aus – die Beispiel-App gibt eine Meldung an die Konsole aus. Der `FileSystemWatcher` einer Konfigurationsdatei kann mehrere Tokenrückrufe für eine einzelne Dateikonfigurationsänderung auslösen.

Um sicherzustellen, dass der benutzerdefinierte Code bei Auslösung mehrerer Tokenrückrufe nur einmal ausgeführt wird, überprüft die Beispielimplementierung Dateihashes.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet1)]

### <a name="simple-startup-change-token"></a>Das Beispiel verwendet SHA1-Dateihashing.

Eine Wiederholung wird mit einem exponentiellen Backoff implementiert.

Die Wiederholung ist vorhanden, weil Dateisperren auftreten können, die vorübergehend das Berechnen eines neuen Hashs für eine der Dateien verhindern.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

*Utilities/Utilities.cs*: Einfaches Starten von Änderungstoken

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Registrieren Sie einen `Action`-Rückruf eines Tokenconsumers für Änderungsbenachrichtigungen an das Token zum Neuladen der Konfiguration. In `Startup.Configure`:

`config.GetReloadToken()` stellt das Token bereit.

### <a name="monitor-configuration-changes-as-a-service"></a>Der Rückruf ist die `InvokeChanged`-Methode:

Der `state` des Rückrufs wird zur Übergabe an die `IHostingEnvironment` verwendet. Dies ist hilfreich, um die richtige *appsettings*-Konfigurationsdatei anzugeben, die überwacht werden soll (in der Entwicklungsumgebung beispielsweise *appsettings.Development.json*).

* Dateihashes werden verwendet, um zu verhindern, dass die `WriteConsole`-Anweisung mehrere Male ausgeführt wird. Dies liegt an mehreren Tokenrückrufen, wenn die Konfigurationsdatei nur einmal geändert wurde.
* Dieses System wird so lange ausgeführt, wie die Anwendung ausgeführt wird. Es kann nicht vom Benutzer deaktiviert werden.
* Überwachen von Konfigurationsänderungen als Dienst

Das Beispiel implementiert:

Grundlegendes Überwachen des Starttokens.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet1)]

Überwachung als Dienst.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet2)]

Ein Mechanismus zum Aktivieren und Deaktivieren des Monitoring. In diesem Beispiel wird eine `IConfigurationMonitor`-Schnittstelle eingerichtet. *Extensions/ConfigurationMonitor.cs*: Der Konstruktor der implementierten `ConfigurationMonitor`-Klasse registriert einen Rückruf für Änderungsbenachrichtigungen:

* `config.GetReloadToken()` stellt das Token bereit.
* `InvokeChanged` ist die Rückrufmethode.

In dieser Instanz stellt `state` einen Verweis auf die `IConfigurationMonitor`-Instanz dar, die zum Zugriff auf den Überwachungsstatus verwendet wird.

* Es werden zwei Eigenschaften verwendet:
* `MonitoringEnabled`: Diese Eigenschaft gibt an, ob der Rückruf den zugehörigen benutzerdefinierten Code ausführen soll.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Extensions/ConfigurationMonitor.cs?name=snippet3)]

`CurrentState`: Diese Eigenschaft beschreibt den aktuellen Überwachungsstatus zur Verwendung in der Benutzeroberfläche.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Die `InvokeChanged`-Methode ist vergleichbar mit dem früheren Ansatz, außer dass sie: Den Code nicht ausführt, es sei denn, `MonitoringEnabled` ist `true`.

Den aktuellen `state` in der zugehörigen `WriteConsole`-Ausgabe berücksichtigt.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet1)]

Eine `ConfigurationMonitor`-Instanz wird in `Startup.ConfigureServices` als Dienst registriert:

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet2)]

Die Indexseite bietet das Benutzer Kontrolle über die Konfigurationsüberwachung. Die `IConfigurationMonitor`-Instanz wird in das `IndexModel` eingefügt.

*Pages/Index.cshtml.cs*:

Der Konfigurationsmonitor (`_monitor`) wird verwendet, um die Überwachung zu aktivieren oder zu deaktivieren und den aktuellen Status für Benutzeroberflächenfeedback festzulegen:

[!code-cshtml[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml?name=snippet_Buttons)]

## <a name="monitor-cached-file-changes"></a>Wenn `OnPostStartMonitoring` ausgelöst wird, wird die Überwachung aktiviert, und der aktuelle Status ist deaktiviert.

Wenn `OnPostStopMonitoring` ausgelöst wird, wird Überwachung deaktiviert, und der Zustand zeigt, dass die Überwachung nicht ausgeführt wird. Die Überwachung wird über Schaltflächen in der Benutzeroberfläche aktiviert und deaktiviert. *Pages/Index.cshtml*:

Überwachen von zwischengespeicherten Dateiänderungen Dateiinhalt kann mithilfe von <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> speicherintern zwischengespeichert werden. Zwischenspeicherung im Arbeitsspeicher wird im Thema [Cache in-memory (Zwischenspeicherung im Arbeitsspeicher)](xref:performance/caching/memory) beschrieben.

Ohne zusätzliche Schritte, wie der unten beschriebenen Implementierung, werden *veraltete* Daten aus einem Zwischenspeicher zurückgegeben, wenn sich die Quelldaten ändern. Wird beispielsweise der Status einer zwischengespeicherten Quelldatei beim Erneuern eines [variablen Ablaufzeitraums](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) nicht berücksichtigt, führt dies zu veralteten Dateidaten im Cache.

Jede Datenanforderung erneuert den variablen Ablaufzeitraum, aber die Datei wird nie neu in den Zwischenspeicher geladen.

* Alle Anwendungsfeatures, die den zwischengespeicherten Inhalt der Datei verwenden, erhalten möglicherweise veraltete Inhalte.
* Durch die Verwendung von Änderungstoken in einem Szenario mit Dateizwischenspeicherung wird verhindert, dass veraltete Dateiinhalte im Zwischenspeicher auftreten.

Die Beispielanwendung zeigt eine Implementierung des Ansatzes.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Utilities/Utilities.cs?name=snippet2)]

Das Beispiel verwendet `GetFileContent`, um: Dateiinhalt zurückzugeben.

Einen Wiederholungsalgorithmus mit exponentiellem Backoff zu implementieren, um Fälle abzudecken, in denen eine Dateisperre vorübergehend das Lesen einer Datei verhindert.

1. *Utilities/Utilities.cs*:
1. Ein `FileService` wird erstellt, um die zwischengespeicherten Dateisuchvorgänge zu behandeln. Der `GetFileContent`-Methodenaufruf des Diensts versucht, den Dateiinhalt aus dem speicherinternen Cache abzurufen und ihn an den Aufrufer zurückzugeben (*Services/FileService.cs*).
1. Wenn mithilfe des Cacheschlüssels zwischengespeicherte Inhalte nicht gefunden werden, werden folgende Aktionen durchgeführt: Der Inhalt der Datei wird mit `GetFileContent` abgerufen.

Ein Änderungstoken wird mithilfe von [IFileProviders.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) aus dem Dateianbieter abgerufen. Wenn die Datei geändert wird, wird ein Rückruf des Tokens ausgelöst. Der Inhalt der Datei wird mit einem [variablen Ablaufzeitraum](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.SlidingExpiration) zwischengespeichert.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Services/FileService.cs?name=snippet1)]

Das Änderungstoken wird mit [MemoryCacheEntryExtensions.AddExpirationToken](xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.AddExpirationToken*) angefügt, um den Cacheeintrag zu entfernen, wenn die sich Datei ändert, während sie zwischengespeichert wird.

Im folgenden Beispiel werden Dateien im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App gespeichert.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

[IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootFileProvider) wird verwendet, um einen <xref:Microsoft.Extensions.FileProviders.IFileProvider> abzurufen, der auf den <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath> der App zeigt.

Der `filePath` wird mit [IFileInfo.PhysicalPath](xref:Microsoft.Extensions.FileProviders.IFileInfo.PhysicalPath) abgerufen.

[!code-csharp[](change-tokens/samples/2.x/SampleApp/Pages/Index.cshtml.cs?name=snippet3)]

## <a name="compositechangetoken-class"></a>Der `FileService` ist zusammen mit dem Speichercachedienst im Dienstcontainer registriert:

In `Startup.ConfigureServices`:

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

Das Seitenmodell lädt mithilfe des Diensts den Inhalt der Datei. In der `OnGet`-Methode auf der Indexseite (*Pages/Index.cshtml.cs*): CompositeChangeToken-Klasse

::: moniker-end

## <a name="additional-resources"></a>Verwenden Sie für die Darstellung von einer oder mehreren `IChangeToken`-Instanzen in einem einzelnen Objekt die Klasse <xref:Microsoft.Extensions.Primitives.CompositeChangeToken>.

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
