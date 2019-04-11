---
title: Dateianbieter in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie ASP.NET Core Dateisystemzugriff durch die Verwendung von Dateianbietern abstrahiert.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 2ce40ea0d576d08a6b42c3eb6693754f2a0bddce
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58809220"
---
# <a name="file-providers-in-aspnet-core"></a>Dateianbieter in ASP.NET Core

Von [Steve Smith](https://ardalis.com/) und [Luke Latham](https://github.com/guardrex)

ASP.NET Core abstrahiert Dateisystemzugriff durch die Verwendung von Dateianbietern. Dateianbieter werden im gesamten ASP.NET Core-Framework verwendet:

* [IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) stellt das Inhaltsstammverzeichnis und das Webstammverzeichnis der Anwendung als `IFileProvider`-Typen zur Verfügung.
* Die [Middleware der statischen Dateien](xref:fundamentals/static-files) verwendet Dateianbieter, um statische Dateien zu suchen.
* [Razor](xref:mvc/views/razor) verwendet Dateianbieter, um Seiten und Ansichten zu suchen.
* .NET Core-Tools verwenden Dateianbieter und Globmuster, um anzugeben, welche Dateien veröffentlicht werden sollen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="file-provider-interfaces"></a>Dateianbieterschnittstellen

Die primäre Schnittstelle ist [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider). `IFileProvider` stellt Methoden zur Verfügung, zum:

* Abrufen von Dateiinformationen ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).
* Abrufen von Verzeichnisinformationen ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).
* Einrichten von Änderungsbenachrichtigungen (mithilfe eines [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).

`IFileInfo` stellt Methoden und Eigenschaften für die Arbeit mit Dateien bereit:

* [Exists](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.exists)
* [IsDirectory](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.isdirectory)
* [Name](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.name)
* [Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (in Bytes)
* [LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified)-Datum

Mithilfe der [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream)-Methode können Sie die Datei auslesen.

Die Beispiel-App demonstriert die Konfiguration eines Dateianbieters in `Startup.ConfigureServices` für die Verwendung in der gesamten App über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).

## <a name="file-provider-implementations"></a>Dateianbieterimplementierungen

Es sind drei Implementierungen von `IFileProvider` verfügbar.

| Implementierung | Beschreibung |
| -------------- | ----------- |
| [PhysicalFileProvider](#physicalfileprovider) | Der physische Anbieter wird verwendet, um auf die physischen Systemdateien zuzugreifen. |
| [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Der eingebettete Manifesanbieter wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind. |
| [CompositeFileProvider](#compositefileprovider) | Der zusammengesetzte Anbieter wird verwendet, um kombinierten Zugriff auf Dateien und Verzeichnisse von einem oder mehreren anderen Anbietern bereitzustellen. |

### <a name="physicalfileprovider"></a>PhysicalFileProvider

Der [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) ermöglicht den Zugriff auf das physische Dateisystem. `PhysicalFileProvider` verwendet den [System.IO.File](/dotnet/api/system.io.file)-Typ (für den physischen Anbieter). Alle Pfade werden einem Verzeichnis und seinen untergeordneten Elementen zugeordnet. Diese Zuordnung verhindert den Zugriff auf das Dateisystem außerhalb des angegebenen Verzeichnisses und den untergeordneten Elementen. Beim Instanziieren dieses Anbieters ist ein Verzeichnispfad erforderlich, der als Basispfad für alle Anforderungen über diesen Anbieter dient. Sie können einen `PhysicalFileProvider`-Anbieter direkt instanziieren oder einen `IFileProvider` in einem Konstruktor über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) anfordern.

**Statische Typen**

Der folgende Code zeigt die Erstellung eines `PhysicalFileProvider` und dessen Verwendung zum Abrufen von Verzeichnisinhalten und Dateiinformationen:

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

Typen im vorherigen Beispiel:

* `provider` ist ein `IFileProvider`.
* `contents` ist ein `IDirectoryContents`.
* `fileInfo` ist ein `IFileInfo`.

Der Dateianbieter kann verwendet werden, um das durch `applicationRoot` angegebene Verzeichnis zu durchlaufen oder `GetFileInfo` aufzurufen, um die Informationen einer Datei anzuzeigen. Der Dateianbieter hat keinen Zugriff außerhalb des `applicationRoot`-Verzeichnisses.

Die Beispiel-App erstellt einen Anbieter in `Startup.ConfigureServices`-Klasse der App mit [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

**Abrufen der Dateianbietertypen mit Abhängigkeitsinjektion**

Fügen Sie den Anbieter in jeden Klassenkonstruktor ein, und weisen Sie ihn einem lokalen Feld zu. Verwenden Sie das Feld in der Methoden der Klasse für den Dateizugriff.

In der Beispiel-App erhält die `IndexModel`-Klasse eine `IFileProvider`-Instanz, um Inhalt des Verzeichnisses für die den Basispfad der App zu erhalten.

*Pages/Index.cshtml.cs*:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml.cs?name=snippet1)]

Die `IDirectoryContents` werden auf der Seite durchlaufen.

*Pages/Index.cshtml*:

[!code-cshtml[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml?name=snippet1)]

### <a name="manifestembeddedfileprovider"></a>ManifestEmbeddedFileProvider

Der [ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind. Der `ManifestEmbeddedFileProvider` verwendet ein in die Assembly kompiliertes Manifest, um die ursprünglichen Pfade der eingebetteten Dateien zu rekonstruieren.

Um ein Manifest der eingebetteten Dateien zu generieren, legen die `<GenerateEmbeddedFilesManifest>`-Eigenschaft auf `true` fest. Geben Sie die einzubettenden Dateien mit [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) an:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

Verwenden Sie [Globmuster](#glob-patterns), um eine oder mehr Dateien anzugeben, die in die Assembly eingebettet werden sollen.

Die Beispiel-App erstellt einen `ManifestEmbeddedFileProvider` und übergibt die aktuell ausgeführte Assembly an den Konstruktor.

*Startup.cs*:

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

Mit zusätzlichen Überladungen können Sie:

* Einen relativen Dateipfad angeben.
* Dateien einem zuletzt geänderten Datum zuordnen.
* Die eingebettete Ressource benennen, die das eingebettete Dateimanifest enthält.

| Überladung | Beschreibung |
| -------- | ----------- |
| [ManifestEmbeddedFileProvider(Assembly, String)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_) | Akzeptiert einen optionalen relativen `root`-Pfadparameter. Geben Sie `root` an, um Aufrufe an [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) den Ressourcen im bereitgestellten Pfad zuzuordnen. |
| [ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_DateTimeOffset_) | Akzeptiert einen optionalen relativen `root`-Pfadparameter und einen `lastModified` ([DateTimeOffset](/dotnet/api/system.datetimeoffset))-Datumsparameter. Das `lastModified`-Datum ordnet das letzte Änderungsdatum für die [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)-Instanzen zu, die vom [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) zurückgegeben wurden. |
| [ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_String_System_DateTimeOffset_) | Akzeptiert einen optionalen, relativen `root`-Pfad, ein `lastModified`-Datum und `manifestName`-Parameter. `manifestName` stellt den Namen der eingebetteten Ressource dar, die das Manifest enthält. |

### <a name="compositefileprovider"></a>CompositeFileProvider

Der [CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) kombiniert `IFileProvider`-Instanzen, die eine einzelne Schnittstelle zum Arbeiten mit Dateien von mehreren Anbietern verfügbar machen. Beim Erstellen des `CompositeFileProvider` übergeben Sie eine oder mehrere `IFileProvider`-Instanzen an seinen Konstruktor.

In der Beispiel-App stellt ein `PhysicalFileProvider` und ein `ManifestEmbeddedFileProvider` Dateien an ein `CompositeFileProvider`, der im Dienstcontainer der App registriert ist:

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a>Überwachen auf Änderungen

Die [IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch)-Methode bietet ein Szenario, eine oder mehrere Dateien oder Verzeichnisse auf Änderungen zu überwachen. `Watch`akzeptiert eine Pfadzeichenfolge, die [Globmuster](#glob-patterns) verwenden kann, um mehrere Dateien anzugeben. `Watch` gibt ein [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) zurück. Das Änderungstoken macht folgendes verfügbar:

* [HasChanged:](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) Eine Eigenschaft, die überprüft werden kann, um festzustellen, ob eine Änderung vorgenommen wurde.
* [RegisterChangeCallback:](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback) Diese Methode wird aufgerufen, wenn Änderungen an der angegebenen Pfadzeichenfolge erkannt werden. Jedes Änderungstoken ruft nur seine zugeordneten Rückrufe als Antwort auf eine einzelne Änderung auf. Zur Aktivierung der konstanten Überwachung können Sie wie unten dargestellt eine [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) verwenden oder `IChangeToken`-Instanzen als Reaktion auf Änderungen neu erstellen.

In der Beispiel-App wird die *WatchConsole*-Konsolen-App konfiguriert, damit sie bei einer Änderung einer Textdatei eine Meldung anzeigt:

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

Einige Dateisysteme, z.B. Docker-Container und Netzwerkfreigaben, senden Änderungsmeldungen möglicherweise nicht zuverlässig . Legen Sie die `DOTNET_USE_POLLING_FILE_WATCHER`-Umgebungsvariable auf `1` oder `true` fest, um das Dateisystem alle 4 Sekunden nach Änderungen zu untersuchen (nicht konfigurierbar).

## <a name="glob-patterns"></a>Globmuster

Dateisystempfade verwenden Platzhaltermuster namens *Globmuster*. Geben Sie Gruppen von Dateien mit diesen Mustern an. Die zwei Platzhalterzeichen sind `*` und `**`:

**`*`**  
Überprüft alles auf der aktuellen Ordnerebene, jeden Dateinamen oder jede Dateierweiterung. Übereinstimmungen werden durch `/`- und `.`-Zeichen im Dateipfad beendet.

**`**`**  
Überprüft alles auf mehrere Verzeichnisebenen. Kann verwendet werden, um rekursiv eine Übereinstimmung für viele Dateien innerhalb einer Verzeichnishierarchie zu finden.

**Beispiele für Globmuster**

**`directory/file.txt`**  
Entspricht einer bestimmten Datei in einem bestimmten Verzeichnis.

**`directory/*.txt`**  
Entspricht allen Dateien mit *.txt*-Erweiterung in einem bestimmten Verzeichnis.

**`directory/*/appsettings.json`**  
Entspricht allen `appsettings.json`-Dateien in Verzeichnissen auf der nächsttieferen Ebene des *directory*-Ordners.

**`directory/**/*.txt`**  
Entspricht allen Dateien mit *.txt*-Erweiterung, die an einer beliebigen Stelle unter dem *directory*-Ordner gefunden werden.
