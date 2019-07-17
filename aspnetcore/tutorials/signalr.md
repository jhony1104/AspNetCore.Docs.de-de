---
title: Erste Schritte mit ASP.NET Core SignalR
author: bradygaster
description: In diesem Tutorial erstellen Sie eine Chat-App, die ASP.NET Core SignalR verwendet.
ms.author: bradyg
ms.custom: mvc
ms.date: 07/08/2019
uid: tutorials/signalr
ms.openlocfilehash: fd3324dfa0731ae16747178d83bd93ed95dd15ce
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724474"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="aabeb-103">Tutorial: Erste Schritte mit ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="aabeb-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

<span data-ttu-id="aabeb-104">In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR beschrieben.</span><span class="sxs-lookup"><span data-stu-id="aabeb-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="aabeb-105">Folgendes wird vermittelt:</span><span class="sxs-lookup"><span data-stu-id="aabeb-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="aabeb-106">Erstellen Sie ein Webprojekt.</span><span class="sxs-lookup"><span data-stu-id="aabeb-106">Create a web project.</span></span>
> * <span data-ttu-id="aabeb-107">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="aabeb-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="aabeb-108">Erstellen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="aabeb-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="aabeb-109">Konfigurieren des Projekts zur Verwendung von SignalR</span><span class="sxs-lookup"><span data-stu-id="aabeb-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="aabeb-110">Hinzufügen von Code, mit dem Nachrichten von jedem Client an alle verbundene Clients gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="aabeb-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="aabeb-111">Am Ende verfügen Sie über eine funktionierende Chat-App:</span><span class="sxs-lookup"><span data-stu-id="aabeb-111">At the end, you'll have a working chat app:</span></span>

![SignalR-Beispiel-App](signalr/_static/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="aabeb-113">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="aabeb-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aabeb-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aabeb-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="aabeb-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aabeb-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="aabeb-116">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aabeb-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="aabeb-117">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="aabeb-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aabeb-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aabeb-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="aabeb-119">Klicken Sie im Menü auf **Datei > Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="aabeb-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="aabeb-120">Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **ASP.NET Core-Webanwendung** aus, und klicken Sie dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="aabeb-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="aabeb-121">Geben Sie im Dialogfeld **Neues Projekt konfigurieren** dem Projekt den Namen *SignalRChat*, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="aabeb-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="aabeb-122">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.0** aus.</span><span class="sxs-lookup"><span data-stu-id="aabeb-122">In the **Create a new ASP.NET Core Web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="aabeb-123">Klicken Sie auf **Webanwendung**, um ein Projekt zu erstellen, das Razor Pages verwendet, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="aabeb-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Dialogfeld „Neues Projekt“ in Visual Studio](signalr/_static/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="aabeb-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aabeb-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="aabeb-126">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) in dem Ordner, in dem der neue Projektordner erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="aabeb-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="aabeb-127">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="aabeb-127">Run the following commands:</span></span>

   ```console
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="aabeb-128">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aabeb-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="aabeb-129">Klicken Sie im Menü auf **Datei > Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="aabeb-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="aabeb-130">Wählen Sie **.NET Core > App > Webanwendung** aus (nicht **Webanwendung (Model View Controller)** auswählen), und klicken Sie dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="aabeb-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="aabeb-131">Stellen Sie sicher, dass das **Zielframework** auf **.NET Core 3.0** festgelegt ist, und wählen Sie dann **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="aabeb-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="aabeb-132">Nennen Sie das Projekt *SignalRChat*, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="aabeb-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="aabeb-133">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="aabeb-133">Add the SignalR client library</span></span>

<span data-ttu-id="aabeb-134">Die SignalR-Serverbibliothek ist im freigegebenen ASP.NET Core 3.0-Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="aabeb-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="aabeb-135">Die JavaScript-Clientbibliothek ist nicht automatisch im Projekt enthalten.</span><span class="sxs-lookup"><span data-stu-id="aabeb-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="aabeb-136">In diesem Tutorial verwenden Sie den Bibliotheks-Manager (LibMan), um die Clientbibliothek von *unpkg* abzurufen.</span><span class="sxs-lookup"><span data-stu-id="aabeb-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="aabeb-137">unpkg ist ein CDN (Content Delivery Network), mit dem Sie alles bereitstellen können, was im npm (Node.js-Paket-Manager) zu finden ist.</span><span class="sxs-lookup"><span data-stu-id="aabeb-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aabeb-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aabeb-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="aabeb-139">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Client-Side Library** (Clientseitige Bibliothek) aus.</span><span class="sxs-lookup"><span data-stu-id="aabeb-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="aabeb-140">Wählen Sie **unpkg** im Dialogfeld **Add Client-Side Library** (Clientseitige Bibliothek hinzufügen) als **Anbieter** aus.</span><span class="sxs-lookup"><span data-stu-id="aabeb-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="aabeb-141">Geben Sie für **Bibliothek** `@aspnet/signalr@next` ein.</span><span class="sxs-lookup"><span data-stu-id="aabeb-141">For **Library**, enter `@aspnet/signalr@next`.</span></span>
<!-- when 3.0 is released, change @next to @latest -->

* <span data-ttu-id="aabeb-142">Klicken Sie auf **Choose specific files** (Spezifische Dateien auswählen), erweitern Sie den Ordner *dist/browser*, und wählen Sie *signalr.js* und *signalr.min.js* aus.</span><span class="sxs-lookup"><span data-stu-id="aabeb-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="aabeb-143">Legen Sie *wwwroot/lib/signalr/* als **Zielspeicherort** fest, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="aabeb-143">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“: Auswählen der Bibliothek](signalr/_static/libman1.png)

  <span data-ttu-id="aabeb-145">Der Ordner *wwwroot/lib/signalr* wird von LibMan erstellt, und die ausgewählten Dateien werden in ihn hineinkopiert.</span><span class="sxs-lookup"><span data-stu-id="aabeb-145">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="aabeb-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aabeb-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="aabeb-147">Führen Sie den folgenden Befehl über das integrierte Terminal aus, um LibMan zu installieren.</span><span class="sxs-lookup"><span data-stu-id="aabeb-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="aabeb-148">Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.</span><span class="sxs-lookup"><span data-stu-id="aabeb-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="aabeb-149">Es kann einige Sekunden dauern, bis die Ausgabe angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="aabeb-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="aabeb-150">Die Parameter legen folgende Optionen fest:</span><span class="sxs-lookup"><span data-stu-id="aabeb-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="aabeb-151">Die Verwendung des Anbieters „unpkg“.</span><span class="sxs-lookup"><span data-stu-id="aabeb-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="aabeb-152">Das Kopieren der Dateien in den Zielordner *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="aabeb-152">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="aabeb-153">Das Kopieren von ausschließlich den angegebenen Dateien.</span><span class="sxs-lookup"><span data-stu-id="aabeb-153">Copy only the specified files.</span></span>

  <span data-ttu-id="aabeb-154">Die Ausgabe sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="aabeb-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="aabeb-155">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aabeb-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="aabeb-156">Führen Sie den folgenden Befehl über das **Terminal** aus, um LibMan zu installieren.</span><span class="sxs-lookup"><span data-stu-id="aabeb-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```console
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="aabeb-157">Navigieren Sie zum Projektordner (der die Datei *SignalRChat.csproj* enthält).</span><span class="sxs-lookup"><span data-stu-id="aabeb-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="aabeb-158">Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.</span><span class="sxs-lookup"><span data-stu-id="aabeb-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="aabeb-159">Die Parameter legen folgende Optionen fest:</span><span class="sxs-lookup"><span data-stu-id="aabeb-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="aabeb-160">Die Verwendung des Anbieters „unpkg“.</span><span class="sxs-lookup"><span data-stu-id="aabeb-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="aabeb-161">Das Kopieren der Dateien in den Zielordner *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="aabeb-161">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="aabeb-162">Das Kopieren von ausschließlich den angegebenen Dateien.</span><span class="sxs-lookup"><span data-stu-id="aabeb-162">Copy only the specified files.</span></span>

  <span data-ttu-id="aabeb-163">Die Ausgabe sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="aabeb-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="aabeb-164">Erstellen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="aabeb-164">Create a SignalR hub</span></span>

<span data-ttu-id="aabeb-165">Ein *Hub* ist eine Klasse, die als grundlegende Pipeline verwendet wird, mit der Client/Server-Kommunikation verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="aabeb-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="aabeb-166">Erstellen Sie im SignalRChat-Projektordner einen *Hubs*-Ordner.</span><span class="sxs-lookup"><span data-stu-id="aabeb-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="aabeb-167">Erstellen Sie im *Hubs*-Ordner eine *ChatHub.cs*-Datei mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="aabeb-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/ChatHub.cs)]

  <span data-ttu-id="aabeb-168">Die `ChatHub`-Klasse erbt von der `Hub`-Klasse von SignalR.</span><span class="sxs-lookup"><span data-stu-id="aabeb-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="aabeb-169">Die `Hub`-Klasse verwaltet Verbindungen, Gruppen und Messaging.</span><span class="sxs-lookup"><span data-stu-id="aabeb-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="aabeb-170">Die `SendMessage`-Methode kann von einem verbundenen Client aufgerufen werden, um eine Nachricht an alle Clients zu senden.</span><span class="sxs-lookup"><span data-stu-id="aabeb-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="aabeb-171">JavaScript-Clientcode, in dem die Methode aufgerufen wird, wird später in diesem Tutorial vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="aabeb-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="aabeb-172">SignalR-Code ist asynchron, damit die maximale Skalierbarkeit gewährleistet werden kann.</span><span class="sxs-lookup"><span data-stu-id="aabeb-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="aabeb-173">Konfigurieren von SignalR</span><span class="sxs-lookup"><span data-stu-id="aabeb-173">Configure SignalR</span></span>

<span data-ttu-id="aabeb-174">Der SignalR-Server muss zunächst konfiguriert werden, um Anforderungen an SignalR zu senden.</span><span class="sxs-lookup"><span data-stu-id="aabeb-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="aabeb-175">Fügen Sie der *Startup.cs*-Datei den folgenden hervorgehobenen Code zu.</span><span class="sxs-lookup"><span data-stu-id="aabeb-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/Startup.cs?highlight=6,30,58)]

  <span data-ttu-id="aabeb-176">Durch diese Änderungen wird SignalR zur ASP.NET Core-Abhängigkeitsinjektion und zu den Routingsystemen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="aabeb-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="aabeb-177">Hinzufügen des SignalR-Clientcodes</span><span class="sxs-lookup"><span data-stu-id="aabeb-177">Add SignalR client code</span></span>

* <span data-ttu-id="aabeb-178">Ersetzen Sie den Inhalt in *Pages\Index.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="aabeb-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/Index.cshtml)]

  <span data-ttu-id="aabeb-179">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="aabeb-179">The preceding code:</span></span>

  * <span data-ttu-id="aabeb-180">erstellt Textfelder für den Namen und den Nachrichtentext sowie eine Senden-Schaltfläche</span><span class="sxs-lookup"><span data-stu-id="aabeb-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="aabeb-181">erstellt eine Liste mit `id="messagesList"` zum Anzeigen von Nachrichten, die vom SignalR-Hub empfangen werden</span><span class="sxs-lookup"><span data-stu-id="aabeb-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="aabeb-182">schließt Skriptverweise sowie den *chat.js*-Anwendungscode, den Sie im folgenden Schritt erstellen, in SignalR ein</span><span class="sxs-lookup"><span data-stu-id="aabeb-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="aabeb-183">Erstellen Sie im *wwwroot/js*-Ordner eine *chat.js*-Datei mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="aabeb-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/chat.js)]

  <span data-ttu-id="aabeb-184">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="aabeb-184">The preceding code:</span></span>

  * <span data-ttu-id="aabeb-185">erstellt und startet eine Verbindung</span><span class="sxs-lookup"><span data-stu-id="aabeb-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="aabeb-186">fügt einen Handler zur Senden-Schaltfläche hinzu, der Nachrichten an den Hub sendet</span><span class="sxs-lookup"><span data-stu-id="aabeb-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="aabeb-187">fügt einen Handler zum Verbindungsobjekt hinzu, der Nachrichten vom Hub empfängt und diese der Liste hinzufügt</span><span class="sxs-lookup"><span data-stu-id="aabeb-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="aabeb-188">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="aabeb-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aabeb-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aabeb-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aabeb-190">Drücken Sie **STRG+F5**, um die App ohne Debugging zu starten.</span><span class="sxs-lookup"><span data-stu-id="aabeb-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="aabeb-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aabeb-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="aabeb-192">Führen Sie über das integrierte Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="aabeb-192">In the integrated terminal, run the following command:</span></span>

  ```console
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="aabeb-193">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aabeb-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="aabeb-194">Wählen Sie im Menü **Ausführen > Starten ohne Debuggen** aus.</span><span class="sxs-lookup"><span data-stu-id="aabeb-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="aabeb-195">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="aabeb-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="aabeb-196">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Nachricht senden**.</span><span class="sxs-lookup"><span data-stu-id="aabeb-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="aabeb-197">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="aabeb-197">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR-Beispiel-App](signalr/_static/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="aabeb-199">Wenn die App nicht funktioniert, öffnen Sie die Browser-Entwicklungstools (F12), und wechseln Sie zur Konsole.</span><span class="sxs-lookup"><span data-stu-id="aabeb-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="aabeb-200">Möglicherweise werden Fehler in Bezug auf Ihren HTML- und JavaScript-Code angezeigt.</span><span class="sxs-lookup"><span data-stu-id="aabeb-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="aabeb-201">Nehmen wir an, dass Sie z.B. *signalr.js* in einen anderen Ordner als vorgeschrieben platziert haben.</span><span class="sxs-lookup"><span data-stu-id="aabeb-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="aabeb-202">In diesem Fall funktioniert der Verweis auf diese Datei nicht, und Ihnen wird der Fehler 404 in der Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="aabeb-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="aabeb-203">![Fehler „signalr.js nicht gefunden“](signalr/_static/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="aabeb-203">![signalr.js not found error](signalr/_static/f12-console.png)</span></span>
> * <span data-ttu-id="aabeb-204">Wenn Sie den Fehler ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome oder NS_ERROR_NET_INADEQUATE_SECURITY in Firefox erhalten, führen Sie folgende Befehle aus, um das Entwicklungszertifikat zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="aabeb-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome or NS_ERROR_NET_INADEQUATE_SECURITY in Firefox, run these commands to update your development certificate:</span></span>
>   ```
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a><span data-ttu-id="aabeb-205">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="aabeb-205">Next steps</span></span>

<span data-ttu-id="aabeb-206">Weitere Informationen zu SignalR finden Sie in der folgenden Einführung:</span><span class="sxs-lookup"><span data-stu-id="aabeb-206">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="aabeb-207">Einführung in ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="aabeb-207">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)
