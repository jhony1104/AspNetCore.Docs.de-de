---
title: LibMan mit ASP.NET Core in Visual Studio verwenden
author: scottaddie
description: Erfahren Sie, wie LibMan in einem ASP.NET Core-Projekt mit Visual Studio verwenden.
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: d0494b96447ad2b256acbde7748209ff430ae7c1
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610183"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>LibMan mit ASP.NET Core in Visual Studio verwenden

Von [Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio verfügt über integrierte Unterstützung für [LibMan](xref:client-side/libman/index) in ASP.NET Core-Projekte, einschließlich:

* Unterstützung zum Konfigurieren und Ausführen von Wiederherstellungsvorgängen LibMan, bei der Erstellung.
* Menüelemente für das Auslösen eines LibMan wiederherstellen und Bereinigen von Vorgängen.
* Das Suchdialogfeld zum Suchen von Bibliotheken, und die Dateien zu einem Projekt hinzugefügt.
* Bearbeiten Sie die Unterstützung für *libman.json*&mdash;LibMan Manifestdatei.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [(Herunterladen von)](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Vorraussetzungen

* [Visual Studio-2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der **ASP.NET und Webentwicklung** arbeitsauslastung

## <a name="add-library-files"></a>Fügen Sie Bibliotheksdateien

Bibliotheksdateien können auf zwei unterschiedliche Arten zu einem ASP.NET Core-Projekt hinzugefügt werden:

1. [Verwenden Sie das Dialogfeld "Client-Side-Bibliothek hinzufügen"](#use-the-add-client-side-library-dialog)
1. [LibMan Manifestdatei Einträge manuell konfigurieren](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>Verwenden Sie das Dialogfeld "Client-Side-Bibliothek hinzufügen"

Um eine clientseitige Bibliothek zu installieren, gehen Sie wie folgt vor:

* In **Projektmappen-Explorer**, mit der rechten Maustaste in des Projektordner aus, in dem die Dateien hinzugefügt werden sollen. Wählen Sie **hinzufügen** > **eine clientseitige Bibliothek**. Die **hinzufügen eine clientseitige Bibliothek** Dialogfeld wird angezeigt:

  ![Eine clientseitige Bibliothek-Dialogfeld "hinzufügen"](_static/add-library-dialog.png)

* Wählen Sie den Bibliotheksanbieter aus der **Anbieter** Dropdown-Liste. CDNJS ist der Standardanbieter.
* Geben Sie den Namen der Bibliothek in abzurufenden der **Bibliothek** Textfeld. IntelliSense bietet eine Liste der Bibliotheken, die mit dem bereitgestellten Text ab.
* Wählen Sie die Bibliothek aus der IntelliSense-Liste ein. Beachten Sie, dass der Bibliotheksname ist mit dem Suffix der `@` Symbol und die neueste stabile Version für den ausgewählten Anbieter bezeichnet.
* Entscheiden Sie, welche Dateien enthalten:
  * Wählen Sie die **enthalten alle Bibliotheksdateien** Optionsfeld, um alle Dateien mit der Bibliothek enthalten sind.
  * Wählen Sie die **wählen Sie bestimmte Dateien** Optionsfeld, um eine Teilmenge der Bibliothek-Dateien enthalten. Wenn das Optionsfeld ausgewählt ist, wird die Selector-Dateistruktur aktiviert. Aktivieren Sie die Kontrollkästchen auf der linken Seite der Dateinamen zum Herunterladen.
* Geben Sie den Projektordner zum Speichern der Dateien in die **Zielspeicherort** Textfeld. Als Empfehlung speichern Sie jede Bibliothek in einem separaten Ordner.

  Die vorgeschlagenen **Zielspeicherort** Ordner basiert auf dem Standort, von dem aus das Dialogfeld gestartet:

  * Wenn Sie über das Stammverzeichnis des Projekts gestartet wird:
    * *Wwwroot/Lib* wird verwendet, wenn *"Wwwroot"* vorhanden ist.
    * *Lib* wird verwendet, wenn *"Wwwroot"* ist nicht vorhanden.
  * Wenn aus einem Projektordner gestartet wird, wird der entsprechende Ordnername verwendet.

  Der Ordner Vorschlag wird der Bibliotheksname angehängt. Die folgende Tabelle zeigt die Ordner-Vorschläge, bei der Installation von jQuery in einem Projekt mit Razor Pages.
  
  |Starten Sie Speicherort                           |Empfohlene Ordner      |
  |------------------------------------------|----------------------|
  |Stammverzeichnis des Projekts (Wenn *"Wwwroot"* vorhanden ist)        |*wwwroot/lib/jquery/* |
  |Stammverzeichnis des Projekts (Wenn *"Wwwroot"* ist nicht vorhanden.) |*lib/jquery/*         |
  |*Seiten* Ordner im Projekt                 |*Pages/jquery/*       |

* Klicken Sie auf die **installieren** Schaltfläche zum Herunterladen der Dateien, gemäß der Konfiguration in *libman.json*.
* Überprüfen Sie die **Hilfebibliotheks-Manager** des Feeds der **Ausgabe** Fenster für die Details zur Installation. Zum Beispiel:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>LibMan Manifestdatei Einträge manuell konfigurieren

Alle LibMan Vorgänge in Visual Studio basieren auf dem Inhalt des Manifests für das Stammverzeichnis des Projekts die LibMan (*libman.json*). Sie können manuell bearbeiten *libman.json* Library-Dateien für das Projekt zu konfigurieren. Visual Studio stellt alle Bibliotheksdateien einmal wieder her. *libman.json* gespeichert wird.

Zum Öffnen *libman.json* für die Bearbeitung gibt folgende Optionen:

* Doppelklicken Sie auf die *libman.json* Datei **Projektmappen-Explorer**.
* Mit der rechten Maustaste in des Projekts im **Projektmappen-Explorer** , und wählen Sie **clientseitige Bibliotheken verwalten**. **&#8224;**
* Wählen Sie **clientseitige Bibliotheken verwalten** von Visual Studio **Projekt** Menü. **&#8224;**

**&#8224;** Wenn die *libman.json* Datei ist nicht in das Stammverzeichnis des Projekts bereits vorhanden ist, wird es mit den standardmäßigen Element Vorlage-Inhalt erstellt.

Visual Studio bietet umfassende JSON bearbeitungsunterstützung wie z. B. farbliche Kennzeichnung, Formatierung, IntelliSense und Schema-Validierung. Die JSON-Schema des Manifests LibMan befindet sich [ http://json.schemastore.org/libman ](http://json.schemastore.org/libman).

Mit der folgenden Manifestdatei ruft LibMan Dateien gemäß der Konfiguration definiert, der `libraries` Eigenschaft. Eine Erläuterung der Objektliterale in definierten `libraries` folgt:

* Eine Teilmenge der [jQuery](https://jquery.com/) Version 3.3.1 wird vom Anbieter CDNJS abgerufen. Die Teilmenge wird definiert, der `files` Eigenschaft&mdash;*"jQuery.Min.js"*, *"jQuery.js"*, und *jquery.min.map*. Die Dateien des Projekts platziert sind *Wwwroot/Lib/Jquery* Ordner.
* Während des gesamten Entwicklungsprozesses [Bootstrap](https://getbootstrap.com/) Version 4.1.3 wird abgerufen und in einem *Wwwroot/Lib/Bootstrap* Ordner. Das Objektliteral `provider` -Eigenschaft überschreibt den `defaultProvider` -Eigenschaftswert. LibMan Ruft die Bootstrap-Dateien aus der Unpkg Anbieter ab.
* Eine Teilmenge der [Lodash](https://lodash.com/) , die von einem Governanceorgane innerhalb der Organisation genehmigt wurde. Die *lodash.js* und *lodash.min.js* Dateien werden abgerufen, aus dem lokalen Dateisystem auf *C:\\Temp\\Lodash\\*. Die Dateien des Projekts kopiert werden *Wwwroot/Lib/Lodash* Ordner.

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan unterstützt nur eine Version jeder Bibliothek aus jeder Anbieter. Die *libman.json* Datei nicht Schema-Validierung, wenn sie die beiden Bibliotheken mit dem gleichen Bibliotheksnamen für einen angegebenen Anbieter enthält.

## <a name="restore-library-files"></a>Wiederherstellen von Bibliotheksdateien

Um Library-Dateien in Visual Studio wiederherzustellen, es muss ein gültiger *libman.json* -Datei in das Stammverzeichnis des Projekts. Wiederhergestellte Dateien werden in das Projekt, an dem für jede Bibliothek angegebenen Speicherort platziert.

Bibliotheksdateien können in einem ASP.NET Core-Projekt auf zweierlei Weise wiederhergestellt werden:

1. [Wiederherstellen von Dateien während des Buildvorgangs](#restore-files-during-build)
1. [Wiederherstellen von Dateien manuell](#restore-files-manually)

### <a name="restore-files-during-build"></a>Wiederherstellen von Dateien während des Buildvorgangs

LibMan kann die definierten-Bibliotheksdateien als Teil des Buildprozesses wiederherstellen. In der Standardeinstellung die *Wiederherstellung Builds* Verhalten deaktiviert ist.

So aktivieren, und Testen Sie das Verhalten für die Wiederherstellung Builds:

* Mit der rechten Maustaste *libman.json* in **Projektmappen-Explorer** , und wählen Sie **wiederherstellen clientseitige Bibliotheken auf Build aktivieren** aus dem Kontextmenü.
* Klicken Sie auf die **Ja** Schaltfläche, wenn Sie aufgefordert werden, um ein NuGet-Paket zu installieren. Die [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet-Paket wird dem Projekt hinzugefügt:

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* Erstellen Sie das Projekt aus, um sicherzustellen, dass LibMan Datei Wiederherstellung erfolgt. Die `Microsoft.Web.LibraryManager.Build` -Paket Fügt ein MSBuild-Ziel, die LibMan während der Buildvorgang des Projekts ausgeführt wird.
* Überprüfen Sie die **erstellen** des Feeds der **Ausgabe** Fenster für ein Aktivitätsprotokoll LibMan:

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

Wenn das Verhalten für die Wiederherstellung Builds aktiviert ist, die *libman.json* Kontextmenü wird angezeigt. eine **deaktivieren wiederherstellen clientseitige Bibliotheken auf Build** Option. Diese Option entfernt die `Microsoft.Web.LibraryManager.Build` Paketverweis aus der Projektdatei. Die clientseitige Bibliotheken werden daher nicht mehr für jedes Build wiederhergestellt.

Unabhängig von der Restore-auf-Build-Einstellung, Sie können manuell wiederherstellen zu einem beliebigen Zeitpunkt aus der *libman.json* Kontextmenü. Weitere Informationen finden Sie unter [Dateien manuell wiederherstellen](#restore-files-manually).

### <a name="restore-files-manually"></a>Wiederherstellen von Dateien manuell

Bibliotheksdateien manuell wiederherstellen zu können:

* Für alle Projekte in der Projektmappe:
  * Mit der rechten Maustaste in des Namens der Projektmappe **Projektmappen-Explorer**.
  * Wählen Sie die **wiederherstellen clientseitige Bibliotheken** Option.
* Für ein bestimmtes Projekt:
  * Mit der rechten Maustaste die *libman.json* Datei **Projektmappen-Explorer**.
  * Wählen Sie die **wiederherstellen clientseitige Bibliotheken** Option.

Während des Wiederherstellungsvorgangs ausgeführt wird:

* Das Symbol "Task Status Center (TSC)" in der Statusleiste von Visual Studio animiert wird, und liest *Wiederherstellungsvorgang Schritte*. Klicken auf das Symbol klicken, wird eine QuickInfo, die Auflistung der bekannten Hintergrundaufgaben geöffnet.
* Nachrichten werden an die Statusleiste gesendet werden und die **Hilfebibliotheks-Manager** des Feeds der **Ausgabe** Fenster. Zum Beispiel:

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>Löschen Sie Bibliotheksdateien

Zum Ausführen der *Bereinigen* Vorgang, bei dem Bibliotheksdateien, die zuvor in Visual Studio wiederhergestellt wird gelöscht:

* Mit der rechten Maustaste die *libman.json* Datei **Projektmappen-Explorer**.
* Wählen Sie die **bereinigen clientseitige Bibliotheken** Option.

Um zu verhindern, dass unbeabsichtigt von nicht-Library-Dateien, nicht der Bereinigungsvorgang ganze Verzeichnisse zu löschen. Es werden nur Dateien in die vorherige Wiederherstellung einbezogen wurden entfernt.

Während die saubere Operation ausgeführt wird:

* Das Symbol "TSC" auf der Statusleiste von Visual Studio animiert wird, und liest *Client-Bibliotheken Vorgang gestartet*. Klicken auf das Symbol klicken, wird eine QuickInfo, die Auflistung der bekannten Hintergrundaufgaben geöffnet.
* Meldungen auf der Statusleiste und **Hilfebibliotheks-Manager** des Feeds der **Ausgabe** Fenster. Zum Beispiel:

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

Der Bereinigungsvorgang werden nur Dateien aus dem Projekt gelöscht. Bibliotheksdateien bleiben im Cache für schnelleren Zugriff auf zukünftige Wiederherstellungsvorgänge. Verwenden Sie zum Verwalten der Bibliotheksdateien, die aus dem Cache mit dem lokalen Computer die [LibMan CLI](xref:client-side/libman/libman-cli).

## <a name="uninstall-library-files"></a>Deinstallieren Sie Bibliotheksdateien

So deinstallieren Sie Bibliotheksdateien

* Open *libman.json*.
* Positionieren Sie den Textcursor in den entsprechenden `libraries` Objektliteral.
* Klicken Sie auf das Glühbirnensymbol klicken, die in den linken Rand angezeigt wird, und wählen **Deinstallieren \<Library_name > @\<Library_version >**:

  ![Deinstallieren Sie die Option im Kontextmenü für Bibliothek](_static/uninstall-menu-option.png)

Sie können auch manuell bearbeiten und speichern Sie das Manifest LibMan (*libman.json*). Die [Wiederherstellungsvorgang](#restore-library-files) ausgeführt wird, wenn die Datei gespeichert wird. Bibliotheksdateien, die nicht mehr definiert sind *libman.json* aus dem Projekt entfernt werden.

## <a name="update-library-version"></a>Update-Bibliotheksversion

So überprüfen Sie für eine Version für die aktualisierte Bibliothek

* Open *libman.json*.
* Positionieren Sie den Textcursor in den entsprechenden `libraries` Objektliteral.
* Klicken Sie auf das Glühbirnensymbol klicken, das in den linken Rand angezeigt wird. Zeigen Sie auf **nach Updates suchen**.

LibMan überprüft, ob eine Bibliotheksversion ist neuer als die installierte Version. Die folgenden Vorgänge möglich:

* Ein **keine Updates gefunden** Meldung wird angezeigt, wenn die aktuelle Version bereits installiert ist.
* Die neueste stabile Version wird angezeigt, sofern nicht bereits installiert.

  ![Überprüfung auf Updates Kontextmenüoption "".](_static/update-menu-option.png)

* Wenn eine Vorabversion, die neuer als die installierte Version verfügbar ist, wird die ältere Version angezeigt.

Ein Downgrade auf eine ältere Bibliotheksversion manuell bearbeiten, die *libman.json* Datei. Wenn die Datei gespeichert wird, wird die LibMan [Wiederherstellungsvorgang](#restore-library-files):

* Entfernt redundante Dateien aus der vorherigen Version.
* Neue und aktualisierte Dateien werden von der neuen Version hinzugefügt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:client-side/libman/libman-cli>
* [GitHub-Repository für LibMan](https://github.com/aspnet/LibraryManager)
