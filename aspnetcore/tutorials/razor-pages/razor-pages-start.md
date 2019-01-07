---
title: Erste Schritte mit Razor Pages in ASP.NET Core
author: rick-anderson
monikerRange: '>= aspnetcore-2.2'
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1152ebfcee48a46ecd28c941fce32d3fc1e05c41
ms.sourcegitcommit: 9bb58d7c8dad4bbd03419bcc183d027667fefa20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52861627"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-Webapp mit Razor Pages.

Die App verwaltet eine Datenbank mit Filmtiteln. Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen einer Razor Pages-Web-App
> * Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell
> * Arbeiten mit einer Datenbank
> * Hinzufügen von Such- und Überprüfungsfunktionen

Am Ende verfügen Sie über eine App, die Filmtitel verwalten und anzeigen kann.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="prerequisites"></a>Erforderliche Komponenten

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-web-app"></a>Erstellen einer Razor-Web-App

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Erstellen Sie eine neue ASP.NET Core-Webanwendung. Nennen Sie das Projekt **RazorPagesMovie**. Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren/einfügen.
 ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* Wählen Sie in der Dropdownliste **ASP.NET Core 2.2** und anschließend **Webanwendung** aus.

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  Das folgende Startprojekt wird erstellt:

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

* Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.

  Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus. Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer. Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet. In der vorherigen Abbildung ist die Portnummer 5001. Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.

  Das Starten der App mit **STRG+F5** (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen. Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.
* Führen Sie den folgenden Befehl aus:

   ```console
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

  * Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "RazorPagesMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**  Wählen Sie **Ja** aus.

  * `dotnet new webapp -o RazorPagesMovie`: Erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.
  * `code -r RazorPagesMovie`: Lädt die Projektdatei *RazorPagesMovie.csproj* in Visual Studio Code.

### <a name="launch-the-app"></a>Starten der App

* Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.

  Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`. Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer.

  Das Starten der App mit **STRG+F5** (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen. Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Führen Sie über ein Terminal die folgenden Befehle aus:

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
cd RazorPagesMovie
dotnet run
```

Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Projekt mit Razor Pages zu erstellen und auszuführen. Öffnen Sie http://localhost:5000 in einem Browser, um sich die Anwendung anzeigen zu lassen.

## <a name="open-the-project"></a>Öffnen des Projekts

Drücken Sie STRG+C, um die Anwendung zu beenden.

Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.

### <a name="launch-the-app"></a>Starten der App

Klicken Sie in Visual Studio auf **Ausführen > Ohne Debuggen starten**, um die App zu starten. Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.

<!-- End of VS tabs -->

---

* Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen. Diese App verfolgt keine personenbezogenen Informationen nach. Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

## <a name="project-files-and-folders"></a>Projektdateien und -ordner

Die folgende Tabelle listet die Dateien und Ordner im Projekt auf. An diesem Punkt des Tutorials ist die Datei *Startup.cs*, die wichtigste, die Sie kennen müssen. Sie müssen nicht jeden der unten angegebenen Links ansehen. Die Links werden als Referenz bereitgestellt, wenn Sie weitere Informationen zu einer Datei oder einem Ordner im Projekt benötigen.

| Datei oder Ordner              | Zweck |
| ----------------- | ------------ |
| *wwwroot* | Enthält statische Dateien. Weitere Informationen finden Sie unter [Statische Dateien](xref:fundamentals/static-files). |
| *Pages* | Ordner für [Razor Pages](xref:razor-pages/index). |
| *appsettings.json* | [Konfiguration](xref:fundamentals/configuration/index) |
| *Program.cs* | [Hostet](xref:fundamentals/host/index) die ASP.NET Core-App.|
| *Startup.cs* | Konfiguriert Dienste und die Anforderungspipeline. Siehe [Startup](xref:fundamentals/startup).|

### <a name="the-pages-folder"></a>Der Seitenordner

Die Datei *_Layout.cshtml* enthält häufige HTML-Elemente (Skripts und Stylesheets) und legt das Layout für die Anwendung fest. Wenn Sie beispielsweise auf **RazorPagesMovie**, **Home** (Startseite) oder **Privacy** (Datenschutz) klicken, werden dieselben Elemente angezeigt. Die häufigen Elemente umfassen das Navigationsmenü am oberen Rand und den Header am unteren Rand des Fensters. Weitere Informationen finden Sie unter [Layout](xref:mvc/views/layout).

Die Datei *_ViewImports.cshtml* enthält Razor-Anweisungen, die in jede Razor Page importiert werden. Weitere Informationen finden Sie unter [Importing Shared Directives (Importieren gemeinsamer Anweisungen)](xref:mvc/views/layout#importing-shared-directives).

Die Datei *_ViewStart.cshtml* legt die Eigenschaft `Layout` der Razor Pages auf die Verwendung der Datei *_Layout.cshtml* fest. Weitere Informationen finden Sie unter [Layout](xref:mvc/views/layout).

Die Datei *_ValidationScriptsPartial.cshtml* enthält einen Verweis auf [jQuery](https://jquery.com/)-Validierungsskripts. Wenn Sie im späteren Verlauf des Tutorials die Seiten `Create` und `Edit` hinzufügen, wird dazu die Datei *_ValidationScriptsPartial.cshtml* verwendet.

Die Seiten `Index`, `Error` und `Privacy` werden zu folgenden Zwecken bereitgestellt:

* `Index`: Starten einer App
* `Error`: Anzeigen von Fehlerinformationen
* `Privacy`: Angeben von Details zur Datenschutzrichtlinie der Website

Für das vorliegende Tutorial werden diese Seiten nicht verwendet.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="f7"></a>
### <a name="use-f7-to-toggle-between-a-razor-page-and-the-pagemodel"></a>Verwenden von F7 zum Umschalten zwischen einer Razor-Seite und dem PageModel

Mit F7 wechseln Sie zwischen einer Razor Page-Datei (*\*.cshtml*) und der C#-Datei (*\*. cshtml.cs*).

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!-- TODO review  Need something in these tabs -->

Per Konvention weisen die Razor Page-Datei (*\*.cshtml*) und das zugehörige `PageModel` den gleichen Stammdateinamen auf.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Per Konvention weisen die Razor Page-Datei (*\*.cshtml*) und das zugehörige `PageModel` den gleichen Stammdateinamen auf.

---

> [!div class="step-by-step"]
> [Nächstes Tutorial: Adding a model to a Razor Pages app in ASP.NET Core with Visual Studio for Mac (Hinzufügen eines Modells zu einer App mit Razor Pages in ASP.NET Core mit Visual Studio für Mac)](xref:tutorials/razor-pages/model)
