---
title: 'Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core'
author: rick-anderson
description: Diese Reihe von Tutorials zeigt, wie Sie Razor Pages in ASP.NET Core verwenden können. Erfahren Sie, wie Sie ein Modell erstellen, Code für Razor-Seiten generieren, Entity Framework Core und SQL Server für den Datenzugriff verwenden, Suchfunktionen hinzufügen, Eingabeüberprüfung hinzufügen und Migrationen verwenden, um das Modell zu aktualisieren.
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1d264ca4a605d8291e273a8f054c92e7eefa5548
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468846"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>Tutorial: Erste Schritte mit Razor Pages in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Dies ist das erste Tutorial aus einer Reihe. [In der Reihe](xref:tutorials/razor-pages/index) lernen Sie die Grundlagen zum Erstellen einer Razor Pages-Web-App mit ASP.NET Core.

[!INCLUDE[](~/includes/advancedRP.md)]

Am Ende der Reihe verfügen Sie über eine App, mit der eine Filmdatenbank verwaltet werden kann.  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

In diesem Tutorial:

> [!div class="checklist"]
> * Erstellen einer Razor Pages-Web-App
> * Führen Sie die App aus.
> * Überprüfen Sie die Projektdateien.

Am Ende dieses Tutorials verfügen Sie über eine funktionsfähige Razor Pages-Web-App, auf der Sie in späteren Tutorials aufbauen werden.

![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a>Erstellen einer Razor Pages-Web-App

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.

* Erstellen Sie eine neue ASP.NET Core-Webanwendung. Nennen Sie das Projekt **RazorPagesMovie**. Es ist wichtig, den Namen *RazorPagesMovie* zu verwenden, damit die Namespaces übereinstimmen, wenn Sie Code kopieren und einfügen.

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2.1.png)

* Wählen Sie in der Dropdownliste **ASP.NET Core 2.2** und anschließend **Webanwendung** aus.

  ![neue ASP.NET Core-Webanwendung](razor-pages-start/_static/np_2_2.2.png)

  Das folgende Startprojekt wird erstellt:

  ![Projektmappen-Explorer](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).

* Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.

* Führen Sie die folgenden Befehle aus:

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *RazorPagesMovie*.
  * Der Befehl `code` öffnet den Ordner *RazorPagesMovie* in einer neuen Instanz von Visual Studio Code.

  Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in "RazorPagesMovie" nicht vorhanden. Sollen sie hinzugefügt werden?**

* Wählen Sie **Ja** aus.

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Führen Sie über ein Terminal den folgenden Befehl aus:

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.

## <a name="open-the-project"></a>Öffnen des Projekts

Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *RazorPagesMovie.csproj* aus.

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>Ausführen der App

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Drücken Sie STRG+F5, um die Ausführung ohne den Debugger zu starten.

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio startet [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) und führt die App aus. Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer. Wenn in Visual Studio ein Webprojekt erstellt wird, wird für den Webserver ein zufälliger Port verwendet.

* Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.

  Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* Drücken Sie **STRG+F5**, um die Ausführung ohne den Debugger zu starten.

  Visual Studio Code startet [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`. Die Adressleiste zeigt `localhost:port#` an, nicht `example.com`. Das liegt daran, dass es sich bei `localhost` um den Standardhostnamen für den lokalen Computer handelt. „Localhost“ dient nur Webanforderungen vom lokalen Computer.

* Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.

  Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2.png)

  Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* Drücken Sie **CMD+OPT+F5** für eine Ausführung ohne Debugger.

  Visual Studio startet dann [Kestrel](xref:fundamentals/servers/kestrel) und einen Browser und navigiert zu `http://localhost:5001`.

* Wählen Sie auf der Homepage der App **Akzeptieren** aus, um der Nachverfolgung zuzustimmen.

  Diese App verfolgt keine persönlichen Informationen nach, aber die Projektvorlage enthält das Einverständniserklärungsfeature für den Fall, dass die App die [allgemeine Datenschutz-Grundverordnung (DSGVO)](xref:security/gdpr) der Europäischen Union erfüllen muss.

  ![Start- oder Indexseite](razor-pages-start/_static/homeGDPR2.2_safari.png)

  Die folgende Abbildung zeigt die App, nachdem Sie das Einverständnis zur Nachverfolgung gegeben haben:

  ![Start- oder Indexseite](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>Überprüfen der Projektdateien

Es folgt eine Übersicht über die Hauptprojektordner und -dateien, mit denen Sie in späteren Tutorials arbeiten werden.

### <a name="pages-folder"></a>Ordner „Seiten“

Enthält Razor-Seiten und unterstützende Dateien. Jede Razor-Seite besteht aus einem Dateienpaar:

* Eine *.cshtml*-Datei, die HTML-Markup mit C#-Code in Razor-Syntax enthält
* Eine *.cshtml.cs*-Datei mit C# Code, in dem Seitenereignisse verarbeitet werden

Unterstützende Dateien haben Namen, die mit einem Unterstrich beginnen. Zum Beispiel sind in der Datei *_Layout.cshtml* Benutzeroberflächenelemente konfiguriert, die für alle Seiten gelten. Mit dieser Datei werden das Navigationsmenü oben auf der Seite und der Urheberrechtshinweis unten auf der Seite eingerichtet. Weitere Informationen finden Sie unter <xref:mvc/views/layout>.

### <a name="wwwroot-folder"></a>Ordner „wwwroot“

Enthält statische Dateien, z. B. HTML-Dateien, JavaScript-Dateien und CSS-Dateien. Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.

### <a name="appsettingsjson"></a>appsettings.json

Enthält Konfigurationsdaten, z. B. Verbindungszeichenfolgen. Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.

### <a name="programcs"></a>Program.cs

Enthält den Einstiegspunkt für das Programm. Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.

### <a name="startupcs"></a>Startup.cs

Enthält Code, mit dem das App-Verhalten konfiguriert wird, beispielsweise, ob die App Zustimmung für Cookies erfordert. Weitere Informationen finden Sie unter <xref:fundamentals/startup>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dieses Tutorials auf YouTube](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Haben Sie eine Razor Pages-Web-App erstellt.
> * Haben Sie die App ausgeführt.
> * Haben Sie die Projektdateien überprüft.

Wechseln Sie zum nächsten Tutorial in der Reihe:

> [!div class="step-by-step"]
> [Hinzufügen eines Modells](xref:tutorials/razor-pages/model)
