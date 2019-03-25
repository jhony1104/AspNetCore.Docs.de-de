---
title: Erste Schritte mit ASP.NET Core MVC
author: rick-anderson
description: Hier finden Sie Informationen zum Einstieg in ASP.NET Core MVC.
ms.author: riande
ms.date: 12/12/2018
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: dbc07558d7d7672e60e8834dc3e4e9d8aab437e3
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265288"
---
# <a name="get-started-with-aspnet-core-mvc"></a>Erste Schritte mit ASP.NET Core MVC

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE [consider RP](~/includes/razor.md)]

In diesem Tutorial lernen Sie Grundlegendes zur Erstellung einer ASP.NET Core-MVC-Web-App.

Die App verwaltet eine Datenbank mit Filmtiteln. Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen einer Web-App
> * Hinzufügen eines Modells und Erstellen eines Gerüsts für das Modell
> * Arbeiten mit einer Datenbank
> * Hinzufügen von Such- und Überprüfungsfunktionen

Am Ende verfügen Sie über eine App, die Filmdaten verwalten und anzeigen kann.

[!INCLUDE[](~/includes/mvc-intro/download.md)]

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-web-app"></a>Erstellen einer Web-App

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Klicken Sie in Visual Studio auf **Datei > Neu > Projekt**.

![Datei > Neu > Projekt](start-mvc/_static/alt_new_project.png)

Schließen Sie das Dialogfeld **Neues Projekt**ab:

* Wählen Sie im linken Bereich die Option **.NET Core** aus.
* Wählen Sie im mittleren Bereich die Option **ASP.NET Core-Webanwendung (.NET Core)** aus.
* Geben Sie dem Projekt den Namen „MvcMovie“. Es ist wichtig, dem Projekt diesen Namen zu geben, sodass beim Kopieren von Code der Namespace übereinstimmt.
* Wählen Sie **OK** aus.

![Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App ](start-mvc/_static/new_project2-21.png)

Schließen Sie das Dialogfeld **ASP.NET Core-Webanwendung (.NET Core) – MvcMovie** ab:

* Klicken Sie im Dropdownfeld der Versionsauswahl auf **ASP.NET Core 2.2**.
* Wählen Sie **Webanwendung (Model-View-Controller)** aus.
* Wählen Sie **OK** aus.

![Dialogfeld „Neues Projekt“, .NET Core im linken Bereich, ASP.NET Core-Web-App ](start-mvc/_static/new_project22-21.png)

Visual Studio verwendet eine Standardvorlage für das MVC-Projekt, das Sie gerade erstellt haben. Wenn Sie einen Projektnamen eingeben und einige Optionen festlegen, funktioniert Ihre App bereits. Dies ist ein grundlegendes Startprojekt und ein guter Einstieg.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Das Tutorial setzt voraus, dass Sie mit VS Code vertraut sind. Weitere Informationen finden Sie unter [Erste Schritte mit VS Code](https://code.visualstudio.com/docs) und [Hilfe zu Visual Studio Code](#visual-studio-code-help).

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).
* Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.
* Führen Sie den folgenden Befehl aus:

   ```console
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "MvcMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**  Wählen Sie **Ja** aus.

  * `dotnet new mvc -o MvcMovie`: Erstellt ein neues ASP.NET Core MVC-Projekt im Ordner *MvcMovie*.
  * `code -r MvcMovie`: Lädt die Projektdatei *MvcMovie.csproj* in Visual Studio Code.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Wählen Sie **Datei** > **Neue Projektmappe** aus.

  ![Neue Projektmappe in macOS](~/tutorials/first-web-api-mac/_static/sln.png)

* Wählen Sie **.NET Core App** > **ASP.NET Core** > **ASP.NET Core Web App (MVC)** > **Next** aus.

  ![Dialogfeld „Neue Projektmappe“ in macOS](~/tutorials/first-mvc-app-mac/start-mvc/1.png)

* Übernehmen Sie im Dialogfeld **Neue ASP.NET Core-Web-API konfigurieren** die Standardeinstellung **Zielframework** von **.NET Core 2.2*.

* Nennen Sie das Projekt **MvcMovie**, und wählen Sie dann **Erstellen** aus.

---

### <a name="run-the-app"></a>Ausführen der App

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Drücken Sie **STRG+F5**, um die App im Nicht-Debugmodus auszuführen.

[!INCLUDE[](~/includes/trustCertVS.md)]

* Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus. Beachten Sie, dass die Adressleiste `localhost:port#` und nicht etwas wie `example.com` anzeigt. Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt. Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.
* Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen. Viele Entwickler bevorzugen den Nicht-Debugmodus, um die App schnell zu starten und Änderungen anzuzeigen.
* Sie können die App über das Menüelement **Debuggen** im Debugmodus oder Nicht-Debugmodus starten:

  ![Menü „Debuggen“](start-mvc/_static/debug_menu.png)

* Sie können die App debuggen, indem Sie die Schaltfläche **IIS Express** auswählen.

  ![IIS Express](start-mvc/_static/iis_express.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.

[!INCLUDE[](~/includes/trustCertVSC.md)]

  Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `https://localhost:5001`. Die Adressleiste zeigt `localhost:port:5001` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer.

  Das Starten der App mit STRG+F5 (Nicht-Debugmodus) ermöglicht die Änderung des Codes, das Speichern der Datei, das Aktualisieren des Browsers und das Anzeigen von Codeänderungen. Viele Entwickler bevorzugen den Nicht-Debugmodus, um die Seite zu aktualisieren und Änderungen anzuzeigen.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App zu starten. Visual Studio für Mac startet den [Kestrel](xref:fundamentals/servers/index#kestrel)-Server und einen Browser und navigiert zu `http://localhost:port`, wobei es sich bei *port* um eine zufällig ausgewählte Portnummer handelt.

[!INCLUDE[](~/includes/trustCertMac.md)]

* Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standard-Hostnamen für Ihren lokalen Computer handelt. Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet. Wenn Sie die App ausführen, wird eine andere Portnummer angezeigt.
* Sie können die App über das Menü **Ausführen** im Debugmodus oder Nicht-Debugmodus starten.

---

* Wählen Sie **Akzeptieren** aus, um der Nachverfolgung zuzustimmen. Diese App verfolgt keine personenbezogenen Informationen nach. Der generierte Vorlagencode enthält Objekte, die bei der Erfüllung der [Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) als Unterstützung dienen sollen.

  ![Start- oder Indexseite](start-mvc/_static/privacy.png)

  Die folgende Abbildung zeigt die App, nachdem die Nachverfolgung akzeptiert wurde:

  ![Start- oder Indexseite](start-mvc/_static/home2.2.png)

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

Im nächsten Teil dieses Tutorials erfahren Sie mehr über MVC und beginnen mit dem Schreiben von Code.

> [!div class="step-by-step"]
> [Nächste](adding-controller.md)
