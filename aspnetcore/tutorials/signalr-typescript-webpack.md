---
title: Verwenden von ASP.NET Core SignalR mit TypeScript und Webpack
author: ssougnez
description: In diesem Tutorial konfigurieren Sie Webpack zum Bündeln und Erstellen einer ASP.NET Core SignalR-Web-App, deren Client in TypeScript geschrieben ist.
ms.author: bradyg
ms.custom: mvc
ms.date: 04/23/2019
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 8bebdd9102f93d2b2a8bf142db1053def9d001a0
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64884605"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a>Verwenden von ASP.NET Core SignalR mit TypeScript und Webpack

Von [Sébastien Sougnez](https://twitter.com/ssougnez) und [Scott Addie](https://twitter.com/Scott_Addie)

Mit [Webpack](https://webpack.js.org/) können Entwickler clientseitige Ressourcen einer Web-App bündeln und erstellen. In diesem Tutorial wird veranschaulicht, wie Webpack in einer ASP.NET Core SignalR-Web-App verwendet wird, deren Client in [TypeScript](https://www.typescriptlang.org/) geschrieben ist.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Aufbauen einer einfachen ASP.NET Core SignalR-App
> * Konfigurieren des SignalR-TypeScript-Clients
> * Konfigurieren einer Buildpipeline mithilfe von Webpack
> * Konfigurieren des SignalR-Servers
> * Aktivieren der Kommunikation zwischen Client und Server

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Erforderliche Komponenten

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Version 15.9 von Visual Studio 2017 oder höher](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) mit der Workload **ASP.NET und Webentwicklung**
* [.NET Core SDK 2.2 oder höher](https://www.microsoft.com/net/download/all)
* [Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [.NET Core SDK 2.2 oder höher](https://www.microsoft.com/net/download/all)
* [C# für Visual Studio Code Version 1.17.1 oder höher](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)

---

## <a name="create-the-aspnet-core-web-app"></a>Erstellen einer ASP.NET Core-Web-App

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

Konfigurieren Sie Visual Studio, damit in der Umgebungsvariable *PATH* nach NPM gesucht wird. Visual Studio verwendet standardmäßig die Version von NPM, die sich im Installationsverzeichnis befindet. Führen Sie die folgenden Anweisungen in Visual Studio aus:

1. Navigieren Sie zu **Extras** > **Optionen** > **Projekte und Projektmappen** > **Webpaketverwaltung** > **Externe Webtools**.
1. Wählen Sie in der Liste den Eintrag *$(PATH)* aus. Klicken Sie auf den Pfeil nach oben, um den Eintrag auf die zweite Position in der Liste zu verschieben.

    ![Visual Studio-Konfiguration](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

Die Konfiguration von Visual Studio ist abgeschlossen. Jetzt ist es an der Zeit, das Projekt zu erstellen.

1. Verwenden Sie die Menüoption **Datei** > **Neu** > **Projekt**, und wählen Sie die Vorlage **ASP.NET Core-Web-App** aus.
1. Benennen Sie das Projekt *SignalRWebPack*, und klicken Sie auf **OK**.
1. Wählen Sie in der Dropdownliste mit Zielframeworks *.NET Core* und in der Dropdownliste zur Auswahl des Frameworks *ASP.NET Core 2.2* aus. Wählen Sie die **leere** Vorlage aus, und klicken Sie auf **OK**.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie den folgenden Befehl aus dem **integrierten Terminal** aus:

```console
dotnet new web -o SignalRWebPack
```

Eine leere ASP.NET Core-Web-App für .NET Core wird in einem *SignalRWebPack*-Verzeichnis erstellt.

---

## <a name="configure-webpack-and-typescript"></a>Konfigurieren von Webpack und TypeScript

In den folgenden Schritten wird die Konvertierung von TypeScript zu JavaScript und die Bündelung clientseitiger Ressourcen konfiguriert.

1. Führen Sie den folgenden Befehl im Projektstamm aus, um eine *package.json*-Datei zu erstellen:

    ```console
    npm init -y
    ```

1. Fügen Sie der *package.json*-Datei die hervorgehobene Eigenschaft hinzu:

    [!code-json[package.json](signalr-typescript-webpack/sample/snippets/package1.json?highlight=4)]

    Durch Festlegen der Eigenschaft `private` auf `true` werden Warnungen bei der Paketinstallation im nächsten Schritt verhindert.

1. Installieren Sie die erforderlichen NPM-Pakete. Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    Beachten Sie folgende Informationen:

    * Auf das `@`-Zeichen folgt bei jedem Paketnamen eine Versionsnummer. Die spezifischen Paketversionen werden von NPM installiert.
    * Die Option `-E` deaktiviert das Standardverhalten von NPM, das Bereichsoperatoren für die [semantische Versionierung](https://semver.org/) in die *package.json*-Datei schreibt. Beispielsweise wird `"webpack": "4.29.3"` anstelle von `"webpack": "^4.29.3"` verwendet. Diese Option verhindert unbeabsichtigte Upgrades auf neuere Paketversionen.

    Ausführliche Informationen finden Sie in der offiziellen Dokumentation zu [npm-install](https://docs.npmjs.com/cli/install).

1. Ersetzen Sie die `scripts`-Eigenschaft der Datei *package.json* durch den folgenden Codeausschnitt:

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    Im Folgenden werden die Skripts erklärt:

    * `build`: Bündelt Ihre clientseitigen Ressourcen im Entwicklungsmodus und prüft auf Änderungen an Dateien. Der Dateiwatcher generiert das Bündel jedes Mal neu, wenn eine Änderung an einer Projektdatei vorgenommen wird. Die Option `mode` deaktiviert Produktionsoptimierungsvorgänge wie das „Tree Shaking“ und die „Minimierung“. Verwenden Sie `build` nur in der Entwicklung.
    * `release`: Bündelt clientseitige Ressourcen im Produktionsmodus.
    * `publish`: Führt das `release`-Skript aus, um die clientseitigen Ressourcen im Produktionsmodus zu bündeln. Es ruft den [publish](/dotnet/core/tools/dotnet-publish)-Befehl der .NET Core-CLI auf, um die App zu veröffentlichen.

1. Erstellen Sie eine Datei namens *webpack.config.js* im Projektstamm mit dem folgenden Inhalt:

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/webpack.config.js)]

    Die vorangehende Datei konfiguriert die Webpack-Kompilierung. Zu beachtende Konfigurationsdetails:

    * Die Eigenschaft `output` überschreibt den Standardwert von *dist*. Das Bündel wird stattdessen an das Verzeichnis *wwwroot* ausgegeben.
    * Das `resolve.extensions`-Array enthält *.js*, um den JavaScript-Code des SignalR-Clients zu importieren.

1. Erstellen Sie ein neues *src*-Verzeichnis im Projektstamm. Es dient als Speicherort für clientseitige Ressourcen des Projekts.

1. Erstellen Sie *src/index.html* mit dem folgenden Inhalt.

    [!code-html[index.html](signalr-typescript-webpack/sample/src/index.html)]

    Der vorangehende HTML-Code definiert die Markupbausteine der Homepage.

1. Erstellen Sie ein neues *src/css*-Verzeichnis. Es dient als Speicherort für die *CSS*-Dateien des Projekts.

1. Erstellen Sie *src/css/main.css* mit dem folgenden Inhalt:

    [!code-css[main.css](signalr-typescript-webpack/sample/src/css/main.css)]

    Die vorangehende Datei *main.css* formatiert die App.

1. Erstellen Sie *src/tsconfig.json* mit dem folgenden Inhalt:

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/src/tsconfig.json)]

    Der vorangehende Code konfiguriert den TypeScript-Compiler, um JavaScript-Code zu erstellen, der mit [ECMAScript 5](https://wikipedia.org/wiki/ECMAScript) kompatibel ist.

1. Erstellen Sie *src/index.ts* mit dem folgenden Inhalt:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    Der vorangehende TypeScript-Code ruft Verweise auf DOM-Elemente ab und fügt zwei Ereignishandler an:

    * `keyup`: Dieses Ereignis wird ausgelöst, wenn der Benutzer etwas in das Textfeld eingibt, das als `tbMessage` erkannt wird. Die Funktion `send` wird aufgerufen, wenn der Benutzer die **EINGABETASTE** drückt.
    * `click`: Dieses Ereignis wird ausgelöst, wenn der Benutzer auf die Schaltfläche **Senden** klickt. Die Funktion `send` wird aufgerufen.

## <a name="configure-the-aspnet-core-app"></a>Konfigurieren einer ASP.NET Core-App

1. Der in der Methode `Startup.Configure` bereitgestellte Code zeigt *Hello World!* an. Ersetzen Sie den Aufruf der Methode `app.Run` durch Aufrufe von [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) und [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    Mit dem vorangehenden Code wird dem Server ermöglicht, die Datei *index.html* zu finden und bereitzustellen, unabhängig davon, ob der Benutzer die vollständige URL oder die Stamm-URL der Web-App eingibt.

1. Rufen Sie [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in der Methode `Startup.ConfigureServices` auf. Mit ihr werden die SignalR-Dienste in das Projekt eingefügt.

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_AddSignalR)]

1. Ordnen Sie dem `ChatHub`-Hub eine */hub*-Route zu. Fügen Sie die folgenden Zeilen am Ende der `Startup.Configure`-Methode hinzu:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_UseSignalR)]

1. Erstellen Sie ein neues Verzeichnis namens *Hubs* am Projektstamm. Es dient als Speicherort des SignalR-Hubs, der im nächsten Schritt erstellt wird.

1. Erstellen Sie mit dem folgenden Code den Hub *Hubs/ChatHub.cs*:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* ein, um den `ChatHub`-Verweis aufzulösen:

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a>Aktivieren der Kommunikation zwischen Client und Server

Derzeit zeigt die App ein einfaches Formular zum Senden von Nachrichten an. Es geschieht jedoch nichts, wenn Sie versuchen, es zu verwenden. Der Server lauscht einer spezifischen Route, aber er verarbeitet gesendete Nachrichten nicht.

1. Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:

    ```console
    npm install @aspnet/signalr
    ```

    Der vorangehende Befehl installiert den [SignalR-TypeScript-Client](https://www.npmjs.com/package/@aspnet/signalr), mit dem der Client Nachrichten an den Server senden kann.

1. Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    Der vorangehende Code unterstützt das Empfangen von Nachrichten vom Server. Die Klasse `HubConnectionBuilder` erstellt einen neuen Generator für die Konfiguration der Serververbindung. Die Funktion `withUrl` konfiguriert die Hub-URL.

    Mit SignalR wird der Austausch von Nachrichten zwischen einem Client und einem Server ermöglicht. Jede Nachricht verfügt über einen spezifischen Namen. Sie können beispielsweise über Nachrichten mit dem Namen `messageReceived` verfügen, die die Logik zum Anzeigen neuer Nachrichten im Nachrichtenbereich ausführen. Mit der `on`-Funktion kann eine spezifische Nachricht belauscht werden. Sie können auf eine beliebige Anzahl von Nachrichtennamen lauschen. Außerdem können Parameter an die Nachricht übergeben werden, z.B. der Name des Autors und der Inhalt der empfangenen Nachricht. Sobald der Client eine Nachricht empfängt, wird ein neues `div`-Element mit dem Namen des Autors und dem Inhalt der Nachricht im `innerHTML`-Attribut erstellt. Es wird dem Hauptelement `div` hinzugefügt, das die Nachrichten anzeigt.

1. Da der Client nun dazu in der Lage ist, Nachrichten zu empfangen, konfigurieren Sie ihn zum Senden von Nachrichten. Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/src/index.ts?highlight=34-35)]

    Für das Senden einer Nachricht über die WebSockets-Verbindung ist das Aufrufen der Methode `send` erforderlich. Der erste Parameter der Methode ist der Name der Nachricht. Die Nachrichtendaten befinden sich in den anderen Parametern. In diesem Beispiel wird eine Nachricht, die als `newMessage` erkannt wird, an den Server gesendet. Die Nachricht besteht aus dem Benutzernamen und der Benutzereingabe eines Textfelds. Wenn das Senden funktioniert hat, wird der Textfeldwert gelöscht.

1. Fügen Sie der `ChatHub`-Klasse die hervorgehobene Methode hinzu:

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/Hubs/ChatHub.cs?highlight=8-11)]

    Der vorangehende Code überträgt die empfangenen Nachrichten an alle verbundenen Benutzer, sobald der Server sie empfängt. Es ist nicht notwendig, eine generische `on`-Methode zum Empfangen aller Nachrichten zu verwenden. Eine Methode mit demselben Namen wie die Nachricht genügt.

    In diesem Beispiel sendet der TypeScript-Client eine Nachricht, die als `newMessage` erkannt wurde. Die C#-Methode `NewMessage` erwartet die vom Client gesendeten Daten. Die Methode [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) wird auf [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) aufgerufen. Die empfangenen Nachrichten werden an alle mit dem Hub verbundenen Clients gesendet.

## <a name="test-the-app"></a>Testen der App

Mit den folgenden Schritten können Sie überprüfen, ob die App funktioniert.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Führen Sie Webpack im *Releasemodus* aus. Führen Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl im Projektstammverzeichnis aus. Wenn Sie sich nicht im Projektstammverzeichnis befinden, geben Sie `cd SignalRWebPack` ein, bevor Sie den Befehl eingeben.

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Klicken Sie auf **Debuggen** > **Starten ohne Debugging**, um die App in einem Browser zu starten, ohne den Debugger anzufügen. Die Datei *wwwroot/index.html* wird unter `http://localhost:<port_number>` bereitgestellt.

1. Öffnen Sie eine weitere Browserinstanz (beliebiger Browser). Fügen Sie die URL in die Adressleiste ein.

1. Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**. Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Führen Sie Webpack im *Releasemodus* aus, indem Sie den folgenden Befehl im Projektstamm ausführen:

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. Erstellen Sie die App, und führen Sie sie aus, indem Sie den folgenden Befehl im Projektstamm ausführen:

    ```console
    dotnet run
    ```

    Der Webserver beginnt mit der App und stellt sie auf „localhost“ zur Verfügung.

1. Öffnen Sie `http://localhost:<port_number>` im Browser. Die Datei *wwwroot/index.html* wird bereitgestellt. Kopieren Sie die URL aus der Adressleiste.

1. Öffnen Sie eine weitere Browserinstanz (beliebiger Browser). Fügen Sie die URL in die Adressleiste ein.

1. Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**. Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.

---

![Die Nachricht wird in beiden Browserfenstern angezeigt](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
