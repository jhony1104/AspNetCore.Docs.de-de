---
title: Erste Schritte mit ASP.NET Core SignalR
author: bradygaster
description: In diesem Tutorial erstellen Sie eine Chat-App, die ASP.NET Core SignalR verwendet.
ms.author: bradyg
ms.custom: mvc
ms.date: 10/03/2019
uid: tutorials/signalr
ms.openlocfilehash: 078f1875d22a90f90575826e6f212205cd4b3d5b
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519177"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a>Tutorial: Erste Schritte mit ASP.NET Core SignalR

::: moniker range=">= aspnetcore-3.0"

In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR beschrieben. Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen Sie ein Webprojekt.
> * Hinzufügen der SignalR-Clientbibliothek
> * Erstellen eines SignalR-Hubs
> * Konfigurieren des Projekts zur Verwendung von SignalR
> * Hinzufügen von Code, mit dem Nachrichten von jedem Client an alle verbundene Clients gesendet werden.

Am Ende verfügen Sie über eine funktionierende Chat-App:

![SignalR-Beispiel-App](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a>Erforderliche Komponenten

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a>Erstellen Sie ein Web-App-Projekt.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)

* Klicken Sie im Menü auf **Datei > Neues Projekt**.

* Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **ASP.NET Core-Webanwendung** aus, und klicken Sie dann auf **Weiter**.

* Geben Sie im Dialogfeld **Neues Projekt konfigurieren** dem Projekt den Namen *SignalRChat*, und wählen Sie dann **Erstellen** aus.

* Wählen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.0** aus. 

* Klicken Sie auf **Webanwendung**, um ein Projekt zu erstellen, das Razor Pages verwendet, und wählen Sie dann **Erstellen** aus.

  ![Dialogfeld „Neues Projekt“ in Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) in dem Ordner, in dem der neue Projektordner erstellt wird.

* Führen Sie die folgenden Befehle aus:

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie im Menü auf **Datei > Neue Projektmappe**.

* Wählen Sie **.NET Core > App > Webanwendung** aus (nicht **Webanwendung (Model View Controller)** auswählen), und klicken Sie dann auf **Weiter**.

* Stellen Sie sicher, dass das **Zielframework** auf **.NET Core 3.0** festgelegt ist, und wählen Sie dann **Weiter** aus.

* Nennen Sie das Projekt *SignalRChat*, und wählen Sie dann **Erstellen** aus.

---

## <a name="add-the-signalr-client-library"></a>Hinzufügen der SignalR-Clientbibliothek

Die SignalR-Serverbibliothek ist im freigegebenen ASP.NET Core 3.0-Framework enthalten. Die JavaScript-Clientbibliothek ist nicht automatisch im Projekt enthalten. In diesem Tutorial verwenden Sie den Bibliotheks-Manager (LibMan), um die Clientbibliothek von *unpkg* abzurufen. unpkg ist ein CDN (Content Delivery Network), mit dem Sie alles bereitstellen können, was im npm (Node.js-Paket-Manager) zu finden ist.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Client-Side Library** (Clientseitige Bibliothek) aus.

* Wählen Sie **unpkg** im Dialogfeld **Add Client-Side Library** (Clientseitige Bibliothek hinzufügen) als **Anbieter** aus.

* Geben Sie für **Bibliothek** `@microsoft/signalr@latest` ein.

* Klicken Sie auf **Choose specific files** (Spezifische Dateien auswählen), erweitern Sie den Ordner *dist/browser*, und wählen Sie *signalr.js* und *signalr.min.js* aus.

* Legen Sie *wwwroot/js/signalr/* als **Zielspeicherort** fest, und klicken Sie auf **Installieren**.

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“: Auswählen der Bibliothek](signalr/_static/3.x/find-signalr-client-libs-select-files.png)

  Der Ordner *wwwroot/js/signalr* wird von LibMan erstellt, und die ausgewählten Dateien werden in ihn hineinkopiert.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

* Führen Sie den folgenden Befehl über das integrierte Terminal aus, um LibMan zu installieren.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen. Es kann einige Sekunden dauern, bis die Ausgabe angezeigt wird.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Die Parameter legen folgende Optionen fest:
  * Die Verwendung des Anbieters „unpkg“.
  * Das Kopieren der Dateien in den Zielordner *wwwroot/js/signalr*.
  * Das Kopieren von ausschließlich den angegebenen Dateien.

  Die Ausgabe sieht wie folgt aus:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Führen Sie den folgenden Befehl über das **Terminal** aus, um LibMan zu installieren.

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* Navigieren Sie zum Projektordner (der die Datei *SignalRChat.csproj* enthält).

* Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.

  ```console
  libman install @microsoft/signalr@latest -p unpkg -d wwwroot/js/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  Die Parameter legen folgende Optionen fest:
  * Die Verwendung des Anbieters „unpkg“.
  * Das Kopieren der Dateien in den Zielordner *wwwroot/js/signalr*.
  * Das Kopieren von ausschließlich den angegebenen Dateien.

  Die Ausgabe sieht wie folgt aus:

  ```console
  wwwroot/js/signalr/dist/browser/signalr.js written to disk
  wwwroot/js/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@microsoft/signalr@latest" to "wwwroot/js/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a>Erstellen eines SignalR-Hubs

Ein *Hub* ist eine Klasse, die als grundlegende Pipeline verwendet wird, mit der Client/Server-Kommunikation verarbeitet wird.

* Erstellen Sie im SignalRChat-Projektordner einen *Hubs*-Ordner.

* Erstellen Sie im *Hubs*-Ordner eine *ChatHub.cs*-Datei mit folgendem Code:

  [!code-csharp[ChatHub](signalr/sample-snapshot/3.x/ChatHub.cs)]

  Die `ChatHub`-Klasse erbt von der `Hub`-Klasse von SignalR. Die `Hub`-Klasse verwaltet Verbindungen, Gruppen und Messaging.

  Die `SendMessage`-Methode kann von einem verbundenen Client aufgerufen werden, um eine Nachricht an alle Clients zu senden. JavaScript-Clientcode, in dem die Methode aufgerufen wird, wird später in diesem Tutorial vorgestellt. SignalR-Code ist asynchron, damit die maximale Skalierbarkeit gewährleistet werden kann.

## <a name="configure-signalr"></a>Konfigurieren von SignalR

Der SignalR-Server muss zunächst konfiguriert werden, um Anforderungen an SignalR zu senden.

* Fügen Sie der *Startup.cs*-Datei den folgenden hervorgehobenen Code zu.

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=11,28,55)]

  Durch diese Änderungen wird SignalR zur ASP.NET Core-Abhängigkeitsinjektion und zu den Routingsystemen hinzugefügt.

## <a name="add-signalr-client-code"></a>Hinzufügen des SignalR-Clientcodes

* Ersetzen Sie den Inhalt in *Pages\Index.cshtml* durch den folgenden Code:

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  Der vorangehende Code:

  * erstellt Textfelder für den Namen und den Nachrichtentext sowie eine Senden-Schaltfläche
  * erstellt eine Liste mit `id="messagesList"` zum Anzeigen von Nachrichten, die vom SignalR-Hub empfangen werden
  * schließt Skriptverweise sowie den *chat.js*-Anwendungscode, den Sie im folgenden Schritt erstellen, in SignalR ein

* Erstellen Sie im *wwwroot/js*-Ordner eine *chat.js*-Datei mit folgendem Code:

  [!code-javascript[chat](signalr/sample-snapshot/3.x/chat.js)]

  Der vorangehende Code:

  * erstellt und startet eine Verbindung
  * fügt einen Handler zur Senden-Schaltfläche hinzu, der Nachrichten an den Hub sendet
  * fügt einen Handler zum Verbindungsobjekt hinzu, der Nachrichten vom Hub empfängt und diese der Liste hinzufügt

## <a name="run-the-app"></a>Ausführen der App

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Drücken Sie **STRG+F5**, um die App ohne Debugging zu starten.

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Führen Sie über das integrierte Terminal den folgenden Befehl aus:

  ```dotnetcli
  dotnet watch run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Wählen Sie im Menü **Ausführen > Starten ohne Debuggen** aus.

---

* Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.

* Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Nachricht senden**.

  Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt.

  ![SignalR-Beispiel-App](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * Wenn die App nicht funktioniert, öffnen Sie die Browser-Entwicklungstools (F12), und wechseln Sie zur Konsole. Möglicherweise werden Fehler in Bezug auf Ihren HTML- und JavaScript-Code angezeigt. Nehmen wir an, dass Sie z.B. *signalr.js* in einen anderen Ordner als vorgeschrieben platziert haben. In diesem Fall funktioniert der Verweis auf diese Datei nicht, und Ihnen wird der Fehler 404 in der Konsole angezeigt.
>   ![Fehler „signalr.js nicht gefunden“](signalr/_static/3.x/f12-console.png)
> * Wenn Sie den Fehler ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome erhalten, führen Sie folgende Befehle aus, um das Entwicklungszertifikat zu aktualisieren:
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu SignalR finden Sie in der folgenden Einführung:

> [!div class="nextstepaction"]
> [Einführung in ASP.NET Core SignalR](xref:signalr/introduction)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR beschrieben. Sie lernen Folgendes:   

> [!div class="checklist"]  
> * Erstellen Sie ein Webprojekt.   
> * Hinzufügen der SignalR-Clientbibliothek 
> * Erstellen eines SignalR-Hubs   
> * Konfigurieren des Projekts zur Verwendung von SignalR   
> * Hinzufügen von Code, mit dem Nachrichten von jedem Client an alle verbundene Clients gesendet werden.  
Am Ende verfügen Sie über eine funktionierende Chat-App: ![SignalR-Beispiel-App](signalr/_static/2.x/signalr-get-started-finished.png) 

## <a name="prerequisites"></a>Erforderliche Komponenten    

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)   

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)] 

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]    

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)   

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]    

--- 

## <a name="create-a-web-project"></a>Erstellen eines Webprojekts 

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* Klicken Sie im Menü auf **Datei > Neues Projekt**. 

* Wählen Sie im Dialogfeld **Neues Projekt** **Installiert > Visual C# > Web > ASP.NET Core-Webanwendung** aus. Geben Sie als Projektname *SignalRChat* ein. 

  ![Dialogfeld „Neues Projekt“ in Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)    

* Klicken Sie auf **Webanwendung**, um ein Projekt zu erstellen, das Razor Pages verwendet. 

* Wählen Sie **.NET Core** als Zielframework sowie **ASP.NET Core 2.2** aus, und klicken Sie auf **OK**.    

  ![Dialogfeld „Neues Projekt“ in Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)   

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) in dem Ordner, in dem der neue Projektordner erstellt wird.  

* Führen Sie die folgenden Befehle aus:   

   ```dotnetcli 
   dotnet new webapp -o SignalRChat 
   code -r SignalRChat  
   ```  

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)   

* Klicken Sie im Menü auf **Datei > Neue Projektmappe**.    

* Klicken Sie auf **.NET Core > App > ASP.NET Core-Web-App** (Wählen Sie nicht **ASP.NET Core-Web-App (MVC)** aus).  

* Klicken Sie auf **Weiter**.  

* Nennen Sie das Projekt *SignalRChat*, und wählen Sie dann **Erstellen** aus.   

--- 

## <a name="add-the-signalr-client-library"></a>Hinzufügen der SignalR-Clientbibliothek   

Die SignalR-Serverbibliothek ist im Metapaket `Microsoft.AspNetCore.App` enthalten. Die JavaScript-Clientbibliothek ist nicht automatisch im Projekt enthalten. In diesem Tutorial verwenden Sie den Bibliotheks-Manager (LibMan), um die Clientbibliothek von *unpkg* abzurufen. unpkg ist ein CDN (Content Delivery Network), mit dem Sie alles bereitstellen können, was im npm (Node.js-Paket-Manager) zu finden ist.    

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio/)  

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Client-Side Library** (Clientseitige Bibliothek) aus.  

* Wählen Sie **unpkg** im Dialogfeld **Add Client-Side Library** (Clientseitige Bibliothek hinzufügen) als **Anbieter** aus. 

* Geben Sie `@aspnet/signalr@1` für die **Bibliothek** ein, und wählen Sie die neueste Version aus, die keine Vorschauversion ist. 

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“: Auswählen der Bibliothek](signalr/_static/2.x/libman1.png)   

* Klicken Sie auf **Choose specific files** (Spezifische Dateien auswählen), erweitern Sie den Ordner *dist/browser*, und wählen Sie *signalr.js* und *signalr.min.js* aus. 

* Legen Sie *wwwroot/lib/signalr/* als **Zielspeicherort** fest, und klicken Sie auf **Installieren**.    

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“: Auswählen der Dateien und des Zielspeicherorts](signalr/_static/2.x/libman2.png) 

  Der Ordner *wwwroot/lib/signalr* wird von LibMan erstellt, und die ausgewählten Dateien werden in ihn hineinkopiert.    

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)    

* Führen Sie den folgenden Befehl über das integrierte Terminal aus, um LibMan zu installieren.  

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen. Es kann einige Sekunden dauern, bis die Ausgabe angezeigt wird.   

  ```console    
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js    
  ```   

  Die Parameter legen folgende Optionen fest: 
  * Die Verwendung des Anbieters „unpkg“. 
  * Das Kopieren der Dateien in den Zielordner *wwwroot/lib/signalr*.    
  * Das Kopieren von ausschließlich den angegebenen Dateien.  

  Die Ausgabe sieht wie folgt aus:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"    
  ```   

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)   

* Führen Sie den folgenden Befehl über das **Terminal** aus, um LibMan zu installieren. 

  ```dotnetcli  
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli   
  ```   

* Navigieren Sie zum Projektordner (der die Datei *SignalRChat.csproj* enthält). 

* Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.  

  ```console    
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js    
  ```   

  Die Parameter legen folgende Optionen fest: 
  * Die Verwendung des Anbieters „unpkg“. 
  * Das Kopieren der Dateien in den Zielordner *wwwroot/lib/signalr*.    
  * Das Kopieren von ausschließlich den angegebenen Dateien.  

  Die Ausgabe sieht wie folgt aus:  

  ```console    
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk   
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk   
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"    
  ```   

--- 

## <a name="create-a-signalr-hub"></a>Erstellen eines SignalR-Hubs 

Ein *Hub* ist eine Klasse, die als grundlegende Pipeline verwendet wird, mit der Client/Server-Kommunikation verarbeitet wird.   

* Erstellen Sie im SignalRChat-Projektordner einen *Hubs*-Ordner.    

* Erstellen Sie im *Hubs*-Ordner eine *ChatHub.cs*-Datei mit folgendem Code: 

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]   

  Die `ChatHub`-Klasse erbt von der `Hub`-Klasse von SignalR. Die `Hub`-Klasse verwaltet Verbindungen, Gruppen und Messaging.    

  Die `SendMessage`-Methode kann von einem verbundenen Client aufgerufen werden, um eine Nachricht an alle Clients zu senden. JavaScript-Clientcode, in dem die Methode aufgerufen wird, wird später in diesem Tutorial vorgestellt. SignalR-Code ist asynchron, damit die maximale Skalierbarkeit gewährleistet werden kann.  

## <a name="configure-signalr"></a>Konfigurieren von SignalR    

Der SignalR-Server muss zunächst konfiguriert werden, um Anforderungen an SignalR zu senden.  

* Fügen Sie der *Startup.cs*-Datei den folgenden hervorgehobenen Code zu.  

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]  

  Durch diese Änderungen wird SignalR zum Dependency Injection-System von ASP.NET Core sowie der Middlewarepipeline hinzugefügt.    

## <a name="add-signalr-client-code"></a>Hinzufügen des SignalR-Clientcodes  

* Ersetzen Sie den Inhalt in *Pages\Index.cshtml* durch den folgenden Code:  

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]   

  Der vorangehende Code:   

  * erstellt Textfelder für den Namen und den Nachrichtentext sowie eine Senden-Schaltfläche  
  * erstellt eine Liste mit `id="messagesList"` zum Anzeigen von Nachrichten, die vom SignalR-Hub empfangen werden 
  * schließt Skriptverweise sowie den *chat.js*-Anwendungscode, den Sie im folgenden Schritt erstellen, in SignalR ein  

* Erstellen Sie im *wwwroot/js*-Ordner eine *chat.js*-Datei mit folgendem Code:  

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]    

  Der vorangehende Code:   

  * erstellt und startet eine Verbindung    
  * fügt einen Handler zur Senden-Schaltfläche hinzu, der Nachrichten an den Hub sendet 
  * fügt einen Handler zum Verbindungsobjekt hinzu, der Nachrichten vom Hub empfängt und diese der Liste hinzufügt  

## <a name="run-the-app"></a>Ausführen der App  

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)   

* Drücken Sie **STRG+F5**, um die App ohne Debugging zu starten.   

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Führen Sie über das integrierte Terminal den folgenden Befehl aus:    

  ```dotnetcli  
  dotnet run -p SignalRChat.csproj  
  ```   

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)   

* Wählen Sie im Menü **Ausführen > Starten ohne Debuggen** aus.  

--- 

* Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.    

* Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Nachricht senden**.  

  Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt.   

  ![SignalR-Beispiel-App](signalr/_static/2.x/signalr-get-started-finished.png)   

> [!TIP]    
> Wenn die App nicht funktioniert, öffnen Sie die Browser-Entwicklungstools (F12), und wechseln Sie zur Konsole. Möglicherweise werden Fehler in Bezug auf Ihren HTML- und JavaScript-Code angezeigt. Nehmen wir an, dass Sie z.B. *signalr.js* in einen anderen Ordner als vorgeschrieben platziert haben. In diesem Fall funktioniert der Verweis auf diese Datei nicht, und Ihnen wird der Fehler 404 in der Konsole angezeigt.   
> ![Fehler „signalr.js nicht gefunden“](signalr/_static/2.x/f12-console.png)    
## <a name="additional-resources"></a>Zusätzliche Ressourcen 
* [Dieses Tutorials auf YouTube](https://www.youtube.com/watch?v=iKlVmu-r0JQ)   

## <a name="next-steps"></a>Nächste Schritte   

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:   

> [!div class="checklist"]  
> * Erstellen Sie ein Web-App-Projekt.   
> * Hinzufügen der SignalR-Clientbibliothek 
> * Erstellen eines SignalR-Hubs   
> * Konfigurieren des Projekts zur Verwendung von SignalR   
> * Hinzufügen von Code, der den Hub verwendet, um Nachrichten von einem beliebigen Client an alle verbundenen Clients zu senden   
Weitere Informationen zu SignalR finden Sie in der folgenden Einführung:  
> [!div class="nextstepaction"] 
> [Einführung in ASP.NET Core SignalR](xref:signalr/introduction) 
::: moniker-end

