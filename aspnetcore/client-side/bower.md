---
title: Verwalten von clientseitigen Paketen mit Bower in ASP.NET Core
author: rick-anderson
description: Verwalten von clientseitigen Paketen mit Bower.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 08/09/2018
uid: client-side/bower
ms.openlocfilehash: 08e6daa537c6c6f92a1cf80d70745e8ef606f580
ms.sourcegitcommit: 191d21c1e37b56f0df0187e795d9a56388bbf4c7
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2019
ms.locfileid: "57665612"
---
# <a name="manage-client-side-packages-with-bower-in-aspnet-core"></a>Verwalten von clientseitigen Paketen mit Bower in ASP.NET Core

Durch [Rick Anderson](https://twitter.com/RickAndMSFT), [Noel Reis](https://twitter.com/noelrice1), und [Scott Addie](https://scottaddie.com)

> [!IMPORTANT]
> Während der Bower verwaltet wird, wird empfohlen, die Verwalter eine andere Lösung. [Hilfebibliotheks-Manager](https://blogs.msdn.microsoft.com/webdev/2018/04/18/what-happened-to-bower/) (kurz LibMan) ist Visual Studio neue clientseitige Bibliothek Lizenzerwerbs-Tool (Visual Studio 15,8 oder höher). Weitere Informationen finden Sie unter <xref:client-side/libman/index>. Bower ist in Visual Studio bis Version 15.5 unterstützt.
>
> Yarn mit Webpack ist eine weit verbreitete Alternative für die [migrationsanweisungen](https://bower.io/blog/2017/how-to-migrate-away-from-bower/) stehen zur Verfügung.

[Bower](https://bower.io/) ruft sich selbst "Paket-Manager für das Web". Innerhalb des Ökosystems .NET füllt die "void", um NuGets-Fehler zum Übermitteln von Dateien mit statischer Inhalt nach links. Diese statischen Dateien für ASP.NET Core-Projekte sind Bestandteil der clientseitige Bibliotheken wie [jQuery](http://jquery.com/) und [Bootstrap](http://getbootstrap.com/). Für .NET Bibliotheken verwenden, verwenden Sie immer noch [NuGet](https://www.nuget.org/) -Paket-Manager.

Buildprozess für neue Projekte, die mit der richten Sie die clientseitige ASP.NET Core-Projektvorlagen erstellt wurden. [jQuery](http://jquery.com/) und [Bootstrap](http://getbootstrap.com/) installiert sind, und Bower wird unterstützt.

Clientseitigen Paketen finden Sie in der *"bower.JSON"* Datei. Konfiguriert die ASP.NET Core-Projektvorlagen *"bower.JSON"* mit Bootstrap, jQuery und jQuery-Validierung.

In diesem Tutorial fügen wir Unterstützung für [Font Awesome](http://fontawesome.io). Bower-Pakete installiert werden können, mit der **Bower-Pakete verwalten** UI oder manuell in die *"bower.JSON"* Datei.

### <a name="installation-via-manage-bower-packages-ui"></a>Installation über Bower-Pakete verwalten UI

* Erstellen einer neuen ASP.NET Core-Web-app mit der **ASP.NET Core-Webanwendung ((.NET Core)** Vorlage. Wählen Sie **Webanwendung** und **keine Authentifizierung**.

* Mit der rechten Maustaste in des Projekts im Projektmappen-Explorer, und wählen Sie **Bower-Pakete verwalten** (Alternativ im Hauptmenü **Projekt** > **Bower-Pakete verwalten**).

* In der **Bower: \<Projektname\>**  , klicken Sie auf der Registerkarte "Durchsuchen", und klicken Sie dann die Pakete durch Eingabe Filterliste `font-awesome` in das Suchfeld:

  ![Bower-Pakete verwalten](bower/_static/manage-bower-packages.png)

* Überprüfen Sie, ob die "Änderungen in *" bower.JSON "*" das Kontrollkästchen aktiviert ist. Wählen Sie eine Version aus der Dropdown-Liste, und klicken Sie auf die **installieren** Schaltfläche. Die **Ausgabe** Fenster zeigt die Details zur Installation.

### <a name="manual-installation-in-bowerjson"></a>Manuelle Installation in "bower.JSON"

Öffnen der *"bower.JSON"* Datei, und fügen Sie "Font awesome" den Abhängigkeiten hinzu. IntelliSense zeigt die verfügbaren Pakete. Wenn ein Paket ausgewählt ist, werden die verfügbaren Versionen angezeigt. Die Bilder unten älter und wird nicht übereinstimmen, was Sie sehen.

![IntelliSense von Bower-Paket-explorer](bower/_static/add-package.png)

![Bower-Version IntelliSense](bower/_static/version-intelliSense.png)

Bower-verwendet [semantische Versionierung](http://semver.org/) zum Organisieren von Abhängigkeiten. Semantischer versionsverwaltung, auch bekannt als SemVer, identifiziert die Pakete mit den Nummerierungsschema \<wichtigen >.\< kleinere >. \<Patch >. IntelliSense vereinfacht semantische Versionierung, indem Sie nur einige gängige Optionen angezeigt. Das oberste Element in der IntelliSense-Liste (4.6.3 im obigen Beispiel), gilt die neueste stabile Version des Pakets. Das Caretzeichen (^) Symbol entspricht, die aktuellste Version, und die Tilde (~) entspricht der aktuellsten Nebenversion.

Speichern Sie die *"bower.JSON"* Datei. Visual Studio wird überwacht, ob die *"bower.JSON"* -Datei für die Änderungen. Beim Speichern der *Bower Install* Befehl ausgeführt wird. Finden Sie im Ausgabefenster **Bower-/Npm** Ansicht für den genauen, ausgeführten Befehl.

Öffnen der *".bowerrc"* Datei *"bower.JSON"*. Die `directory` -Eigenschaftensatz auf *Wwwroot/Lib* gibt an den Speicherort von Bower installiert die Paketressourcen.

```json
{
 "directory": "wwwroot/lib"
}
```

Sie können das Suchfeld im Projektmappen-Explorer verwenden, suchen und Anzeigen der Font awesome Paket.

Öffnen der *Views\Shared\_Layout.cshtml* Datei und hinzufügen die Font awesome CSS-Datei in der Umgebung [Taghilfsprogramm](xref:mvc/views/tag-helpers/intro) für `Development`. Klicken Sie im Projektmappen-Explorer ziehen, und legen Sie *Schriftart-awesome.css* innerhalb der `<environment names="Development">` Element.

[!code-html[](bower/sample/_Layout.cshtml?highlight=4&range=9-13)]

In einer Produktions-app hinzufügen *Schriftart-awesome.min.css* auf die Environment-taghilfsprogramm für `Staging,Production`.

Ersetzen Sie den Inhalt von der *Views\Home\About.cshtml* Razor-Datei mit folgendem Markup:

[!code-html[](bower/sample/About.cshtml)]

Führen Sie die app aus, und navigieren Sie zu der Ansicht "Info", um zu überprüfen, ob die Schriftart-awesome Paket funktioniert.

## <a name="exploring-the-client-side-build-process"></a>Untersuchen die Client-Side-Buildprozess

Die meisten ASP.NET Core-Projektvorlagen sind bereits konfiguriert, um die Nutzung von bower beginnen. Dieser nächste exemplarische Vorgehensweise beginnt mit einem leeren ASP.NET Core-Projekt und jedes Datenelement manuell hinzugefügt, sodass Sie ein Gefühl für die Verwendung von Bower in einem Projekt abrufen können. Sie können sehen, was geschieht mit der Projektstruktur und die Laufzeit auszugeben, wenn jede konfigurationsänderung vorgenommen wird.

Die allgemeinen Schritte des Buildprozesses für die clientseitige mit Bower verwendet werden:

* Definieren Sie in Ihrem Projekt verwendeten Pakete. <!-- once defined, you don't need to download them, VS does -->
* Die referenzpakete von Ihren Webseiten.

### <a name="define-packages"></a>Definieren von Paketen

Nachdem Sie Pakete in Auflisten der *"bower.JSON"* Visual Studio-Datei laden sie herunter. Im folgenden Beispiel wird Bower, jQuery und Bootstrap zum Laden der *"Wwwroot"* Ordner.

* Erstellen einer neuen ASP.NET Core-Web-app mit der **ASP.NET Core-Webanwendung ((.NET Core)** Vorlage. Wählen Sie die **leere** Projektvorlage aus, und klicken Sie auf **OK**.

* Klicken Sie im Projektmappen-Explorer mit der Maustaste des Projekts > **neues Element hinzufügen** , und wählen Sie **Bower-Konfigurationsdatei**. Hinweis: Ein *".bowerrc"* Datei wird ebenfalls hinzugefügt.

* Öffnen *"bower.JSON"*, Hinzufügen von Jquery und bootstrap auf dem `dependencies` Abschnitt. Die resultierende *"bower.JSON"* sieht die Datei wie im folgenden Beispiel. Die Versionen ändert sich im Laufe der Zeit und entsprechen möglicherweise nicht die folgenden Abbildung.

[!code-json[](bower/sample/bower.json?highlight=5,6)]

* Speichern Sie die *"bower.JSON"* Datei.

  Überprüfen Sie das Projekt enthält die *bootstrap* und *jQuery* Verzeichnisse im *Wwwroot/Lib*. Bower-verwendet die *".bowerrc"* Datei zum Installieren der Objekte in *Wwwroot/Lib*.

  Hinweis: Die Benutzeroberfläche "Bower-Pakete verwalten" bietet eine Alternative zum manuellen-Datei bearbeitet wird.

### <a name="enable-static-files"></a>Aktivieren Sie die statische Dateien

* Hinzufügen der `Microsoft.AspNetCore.StaticFiles` NuGet-Paket zum Projekt.
* Aktivieren Sie statische Dateien mit verarbeitet die [Middleware für statische Dateien](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions). Fügen Sie einen Aufruf von [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions) auf die `Configure` -Methode der `Startup`.

[!code-csharp[](bower/sample/Startup.cs?highlight=9)]

### <a name="reference-packages"></a>Referenzpakete

In diesem Abschnitt erstellen Sie eine HTML-Seite, um sicherzustellen, dass sie die bereitgestellten Pakete zugreifen kann.

* Hinzufügen einer neuen HTML-Seite, die mit dem Namen *"Index.HTML"* auf die *"Wwwroot"* Ordner. Hinweis: Sie müssen die HTML-Datei zum Hinzufügen der *"Wwwroot"* Ordner. Standardmäßig kann nicht außerhalb statischer Inhalt verarbeitet werden *"Wwwroot"*. Finden Sie unter [statische Dateien](xref:fundamentals/static-files) für Weitere Informationen.

  Ersetzen Sie den Inhalt der *"Index.HTML"* mit folgendem Markup:

[!code-html[](bower/sample/Index.html)]

* Führen Sie die app, und navigieren Sie zu `http://localhost:<port>/Index.html`. Sie können auch mit *"Index.HTML"* geöffnet haben, drücken Sie die `Ctrl+Shift+W`. Stellen Sie sicher, dass der Jumbotron-Stil angewendet wird, der jQuery-Code reagiert werden soll, wenn die Schaltfläche geklickt wird und die Schaltfläche "Bootstrap" Zustand ändert.

  ![angewendete Jumbotron-Stil](bower/_static/jumbotron.png)
