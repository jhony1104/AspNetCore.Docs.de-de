---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/file-providers
ms.openlocfilehash: 1e243d31a1c6b1f6ac6c9f7966ce07ecb01ceae5
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106182"
---
# <a name="file-providers-in-aspnet-core"></a>'Identity'

'Let's Encrypt'

::: moniker range=">= aspnetcore-3.0"

'Razor' 'SignalR' uid: Dateianbieter in ASP.NET Core

* Von [Steve Smith](https://ardalis.com/)
* ASP.NET Core abstrahiert Dateisystemzugriff durch die Verwendung von Dateianbietern.
* Dateianbieter werden im gesamten ASP.NET Core-Framework verwendet.
* Zum Beispiel:

<xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> macht das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root) der App als `IFileProvider`-Typen verfügbar.

## <a name="file-provider-interfaces"></a>Die [Middleware der statischen Dateien](xref:fundamentals/static-files) verwendet Dateianbieter, um statische Dateien zu suchen.

[Razor](xref:mvc/views/razor) verwendet Dateianbieter, um Seiten und Ansichten zu suchen. .NET Core-Tools verwenden Dateianbieter und Globmuster, um anzugeben, welche Dateien veröffentlicht werden sollen.

* [Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))
* Dateianbieterschnittstellen
* Die primäre Schnittstelle ist <xref:Microsoft.Extensions.FileProviders.IFileProvider>.

`IFileProvider` stellt Methoden zur Verfügung, zum:

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* Abrufen von Dateiinformationen (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).
* Abrufen von Verzeichnisinformationen (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).

Einrichten von Änderungsbenachrichtigungen (mithilfe eines <xref:Microsoft.Extensions.Primitives.IChangeToken>).

`IFileInfo` stellt Methoden und Eigenschaften für die Arbeit mit Dateien bereit:

## <a name="file-provider-implementations"></a><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in Bytes)

<xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> – ein Datum

| Sie können die Datei mithilfe der <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType>-Methode lesen. | Die Beispiel-App *FileProviderSample* demonstriert die Konfiguration eines Dateianbieters in `Startup.ConfigureServices` für die Verwendung in der gesamten App über [Dependency Injection](xref:fundamentals/dependency-injection). |
| -------------- | ----------- |
| Dateianbieterimplementierungen | In der folgenden Tabelle werden Implementierungen von `IFileProvider` aufgeführt. |
| Implementierung | Beschreibung |
| title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor' |

### <a name="physicalfileprovider"></a>'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity'

'Let's Encrypt'

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

'Razor'

* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'

'Identity' 'Let's Encrypt' 'Razor'

'SignalR' uid:

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity'

'Let's Encrypt'

1. 'Razor'
1. 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

'Blazor'

'Identity'

'Let's Encrypt'

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

'Razor'

* 'SignalR' uid:
* ------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'

| 'Identity' | 'Let's Encrypt' |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | 'Razor' 'SignalR' uid: |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | 'Identity' 'Let's Encrypt' |

### <a name="compositefileprovider"></a>'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity'

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>'Let's Encrypt'

'Razor' 'SignalR' uid:

* ------ | | [CompositeFileProvider](#compositefileprovider) | Wird zur Bereitstellung von kombiniertem Zugriff auf Dateien und Verzeichnisse über einen oder mehrere andere Anbieter verwendet
* | | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Wird für den Zugriff auf in Assemblys eingebettete Dateien verwendet

| | [PhysicalFileProvider](#physicalfileprovider) | Wird für den Zugriff auf die physischen Systemdateien verwendet

* PhysicalFileProvider
* Der <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> ermöglicht den Zugriff auf das physische Dateisystem. `PhysicalFileProvider` verwendet den <xref:System.IO.File?displayProperty=fullName>-Typ (für den physischen Anbieter). Alle Pfade werden einem Verzeichnis und dessen untergeordneten Elementen zugeordnet. Diese Zuordnung verhindert den Zugriff auf das Dateisystem außerhalb des angegebenen Verzeichnisses und den untergeordneten Elementen.

Das häufigste Szenario, in dem ein `PhysicalFileProvider` erstellt und verwendet wird, ist die Anforderung eines `IFileProvider` in einem Konstruktor über eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

Beim direkten Instanziieren dieses Anbieters ist ein absoluter Verzeichnispfad erforderlich, der als Basispfad für alle Anforderungen über diesen Anbieter dient. Globmuster werden im Verzeichnispfad nicht unterstützt.

### <a name="glob-patterns"></a>Im folgenden Code wird die Verwendung von `PhysicalFileProvider` zum Abrufen von Verzeichnisinhalten und Dateiinformationen veranschaulicht:

Typen im vorherigen Beispiel: `provider` ist ein `IFileProvider`. `contents` ist ein `IDirectoryContents`.

**`*`**  
`fileInfo` ist ein `IFileInfo`. Der Dateianbieter kann verwendet werden, um das durch `applicationRoot` angegebene Verzeichnis zu durchlaufen oder `GetFileInfo` aufzurufen, um die Informationen einer Datei anzuzeigen.

**`**`**  
Globmuster können nicht an die `GetFileInfo`-Methode übergeben werden. Der Dateianbieter hat keinen Zugriff außerhalb des `applicationRoot`-Verzeichnisses.

Die Beispiel-App *FileProviderSample* erstellt den Anbieter in der `Startup.ConfigureServices`-Methode mithilfe von <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:

|ManifestEmbeddedFileProvider  |Der <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind.  |
|---------|---------|
|`directory/file.txt`|Der `ManifestEmbeddedFileProvider` verwendet ein in die Assembly kompiliertes Manifest, um die ursprünglichen Pfade der eingebetteten Dateien zu rekonstruieren.|
|`directory/*.txt`|So generieren Sie ein Manifest der eingebetteten Dateien:|
|`directory/*/appsettings.json`|Fügen Sie das NuGet-Paket [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) zu Ihrem Projekt hinzu.|
|`directory/**/*.txt`|Legen Sie die `<GenerateEmbeddedFilesManifest>` -Eigenschaft auf `true`fest.|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Geben Sie die einzubettenden Dateien mit [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) an: Verwenden Sie [Globmuster](#glob-patterns), um eine oder mehr Dateien anzugeben, die in die Assembly eingebettet werden sollen.

* Die Beispiel-App *FileProviderSample* erstellt einen `ManifestEmbeddedFileProvider` und übergibt die aktuell ausgeführte Assembly an den Konstruktor.
* *Startup.cs*:
* Mit zusätzlichen Überladungen können Sie:
* Einen relativen Dateipfad angeben.

Dateien einem zuletzt geänderten Datum zuordnen.

## <a name="file-provider-interfaces"></a>Die eingebettete Ressource benennen, die das eingebettete Dateimanifest enthält.

Überladung Beschreibung

* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'

'Let's Encrypt'

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* 'Razor'
* 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor'

## <a name="file-provider-implementations"></a>'Identity'

'Let's Encrypt'

| 'Razor' | 'SignalR' uid: |
| -------------- | ----------- |
| ---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor' |
| 'Identity' | 'Let's Encrypt' |
| 'Razor' | 'SignalR' uid: |

### <a name="physicalfileprovider"></a>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

'Blazor'

* 'Identity'
* 'Let's Encrypt'
* 'Razor'

'SignalR' uid: ------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Akzeptiert einen optionalen relativen `root`-Pfadparameter

Geben Sie `root` an, um Aufrufe von <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> auf die Ressourcen im angegebenen Pfad zu begrenzen.

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a>| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Akzeptiert einen optionalen relativen `root`-Pfadparameter und einen Datumsparameter für `lastModified` (<xref:System.DateTimeOffset>)

Das `lastModified`-Datum ordnet das letzte Änderungsdatum für die <xref:Microsoft.Extensions.FileProviders.IFileInfo>-Instanzen zu, die vom <xref:Microsoft.Extensions.FileProviders.IFileProvider> zurückgegeben wurden. | | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Akzeptiert einen optionalen relativen `root`-Pfadparameter, einen Datumsparameter für `lastModified` und einen `manifestName`-Parameter

`manifestName` stellt den Namen der eingebetteten Ressource dar, die das Manifest enthält. CompositeFileProvider

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

Der <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> kombiniert `IFileProvider`-Instanzen, die eine einzelne Schnittstelle zum Arbeiten mit Dateien von mehreren Anbietern verfügbar machen.

Beim Erstellen des `CompositeFileProvider` übergeben Sie eine oder mehrere `IFileProvider`-Instanzen an seinen Konstruktor.

In der Beispiel-App *FileProviderSample* stellen die Anbieter `PhysicalFileProvider` und `ManifestEmbeddedFileProvider` Dateien für einen `CompositeFileProvider`-Anbieter bereit, der im Dienstcontainer der App registriert ist.

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

Der folgende Code befindet sich in der `Startup.ConfigureServices`-Methode des Projekts:

* Überwachen auf Änderungen
* Die <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType>-Methode bietet ein Szenario zum Überwachen von Änderungen an Dateien oder Verzeichnissen.
* Die `Watch`-Methode:

| akzeptiert eine Dateipfadzeichenfolge, die [Globmuster](#glob-patterns) verwenden kann, um mehrere Dateien anzugeben. | Gibt einen <xref:Microsoft.Extensions.Primitives.IChangeToken> zurück. |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | Das resultierende Änderungstoken stellt Folgendes zur Verfügung: <xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Eine Eigenschaft, die überprüft werden kann, um festzustellen, ob eine Änderung vorgenommen wurde. |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Diese Methode wird aufgerufen, wenn Änderungen an der angegebenen Pfadzeichenfolge erkannt werden. Jedes Änderungstoken ruft nur seine zugeordneten Rückrufe als Antwort auf eine einzelne Änderung auf. |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Zur Aktivierung einer konstanten Überwachung verwenden Sie eine <xref:System.Threading.Tasks.TaskCompletionSource`1> (wie unten dargestellt), oder erstellen Sie `IChangeToken`-Instanzen nach Änderungen neu. Die Beispiel-App *WatchConsole* schreibt eine Nachricht, wenn eine *TXT*-Datei im Verzeichnis *TextFiles* geändert wird: |

### <a name="compositefileprovider"></a>Einige Dateisysteme, z.B. Docker-Container und Netzwerkfreigaben, senden Änderungsmeldungen möglicherweise nicht zuverlässig .

Legen Sie die `DOTNET_USE_POLLING_FILE_WATCHER`-Umgebungsvariable auf `1` oder `true` fest, um das Dateisystem alle 4 Sekunden nach Änderungen zu untersuchen (nicht konfigurierbar). Globmuster

Dateisystempfade verwenden Platzhaltermuster namens *Globmuster*.

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Geben Sie Gruppen von Dateien mit diesen Mustern an.

Die zwei Platzhalterzeichen sind `*` und `**`: Überprüft alles auf der aktuellen Ordnerebene, jeden Dateinamen oder jede Dateierweiterung. Übereinstimmungen werden durch `/`- und `.`-Zeichen im Dateipfad beendet. Überprüft alles auf mehrere Verzeichnisebenen.

* Kann verwendet werden, um rekursiv eine Übereinstimmung für viele Dateien innerhalb einer Verzeichnishierarchie zu finden.
* Die folgende Tabelle enthält gängige Beispiele für Globmuster. Muster Beschreibung

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

'Blazor' 'Identity'

## <a name="glob-patterns"></a>'Let's Encrypt'

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

**`*`**  
'Blazor' 'Identity'

**`**`**  
'Let's Encrypt' 'Razor'

'SignalR' uid:

**`directory/file.txt`**  
-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

**`directory/*.txt`**  
'Blazor'

**`directory/*/appsettings.json`**  
'Identity'

**`directory/**/*.txt`**  
'Let's Encrypt'

::: moniker-end
