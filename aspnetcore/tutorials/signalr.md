---
title: Erste Schritte mit ASP.NET Core SignalR
author: bradygaster
description: In diesem Tutorial erstellen Sie eine Chat-App, die ASP.NET Core SignalR verwendet.
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2019
uid: tutorials/signalr
ms.openlocfilehash: 7a6574bd3c463f0890f5dc076944f1ab0f0c919a
ms.sourcegitcommit: e54672f5c493258dc449fac5b98faf47eb123b28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71248402"
---
# <a name="tutorial-get-started-with-aspnet-core-signalr"></a><span data-ttu-id="3115a-103">Tutorial: Erste Schritte mit ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="3115a-103">Tutorial: Get started with ASP.NET Core SignalR</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3115a-104">In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR beschrieben.</span><span class="sxs-lookup"><span data-stu-id="3115a-104">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="3115a-105">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3115a-105">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3115a-106">Erstellen Sie ein Webprojekt.</span><span class="sxs-lookup"><span data-stu-id="3115a-106">Create a web project.</span></span>
> * <span data-ttu-id="3115a-107">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="3115a-107">Add the SignalR client library.</span></span>
> * <span data-ttu-id="3115a-108">Erstellen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="3115a-108">Create a SignalR hub.</span></span>
> * <span data-ttu-id="3115a-109">Konfigurieren des Projekts zur Verwendung von SignalR</span><span class="sxs-lookup"><span data-stu-id="3115a-109">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="3115a-110">Hinzufügen von Code, mit dem Nachrichten von jedem Client an alle verbundene Clients gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="3115a-110">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="3115a-111">Am Ende verfügen Sie über eine funktionierende Chat-App:</span><span class="sxs-lookup"><span data-stu-id="3115a-111">At the end, you'll have a working chat app:</span></span>

![SignalR-Beispiel-App](signalr/_static/3.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="3115a-113">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="3115a-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3115a-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3115a-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3115a-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3115a-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3115a-116">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="3115a-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app-project"></a><span data-ttu-id="3115a-117">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="3115a-117">Create a web app project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3115a-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3115a-118">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="3115a-119">Klicken Sie im Menü auf **Datei > Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="3115a-119">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="3115a-120">Wählen Sie im Dialogfeld **Neues Projekt erstellen** die Option **ASP.NET Core-Webanwendung** aus, und klicken Sie dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="3115a-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application**, and then select **Next**.</span></span>

* <span data-ttu-id="3115a-121">Geben Sie im Dialogfeld **Neues Projekt konfigurieren** dem Projekt den Namen *SignalRChat*, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-121">In the **Configure your new project** dialog, name the project *SignalRChat*, and then select **Create**.</span></span>

* <span data-ttu-id="3115a-122">Wählen Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.0** aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-122">In the **Create a new ASP.NET Core Web Application** dialog, select **.NET Core** and **ASP.NET Core 3.0**.</span></span> 

* <span data-ttu-id="3115a-123">Klicken Sie auf **Webanwendung**, um ein Projekt zu erstellen, das Razor Pages verwendet, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-123">Select **Web Application** to create a project that uses Razor Pages, and then select **Create**.</span></span>

  ![Dialogfeld „Neues Projekt“ in Visual Studio](signalr/_static/3.x/signalr-new-project-dialog.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3115a-125">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3115a-125">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="3115a-126">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) in dem Ordner, in dem der neue Projektordner erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="3115a-126">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="3115a-127">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="3115a-127">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3115a-128">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="3115a-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3115a-129">Klicken Sie im Menü auf **Datei > Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="3115a-129">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="3115a-130">Wählen Sie **.NET Core > App > Webanwendung** aus (nicht **Webanwendung (Model View Controller)** auswählen), und klicken Sie dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="3115a-130">Select **.NET Core > App > Web Application** (Don't select **Web Application (Model-View-Controller)**), and then select **Next**.</span></span>

* <span data-ttu-id="3115a-131">Stellen Sie sicher, dass das **Zielframework** auf **.NET Core 3.0** festgelegt ist, und wählen Sie dann **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-131">Make sure the **Target Framework** is set to **.NET Core 3.0**, and then select **Next**.</span></span>

* <span data-ttu-id="3115a-132">Nennen Sie das Projekt *SignalRChat*, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-132">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="3115a-133">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="3115a-133">Add the SignalR client library</span></span>

<span data-ttu-id="3115a-134">Die SignalR-Serverbibliothek ist im freigegebenen ASP.NET Core 3.0-Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="3115a-134">The SignalR server library is included in the ASP.NET Core 3.0 shared framework.</span></span> <span data-ttu-id="3115a-135">Die JavaScript-Clientbibliothek ist nicht automatisch im Projekt enthalten.</span><span class="sxs-lookup"><span data-stu-id="3115a-135">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="3115a-136">In diesem Tutorial verwenden Sie den Bibliotheks-Manager (LibMan), um die Clientbibliothek von *unpkg* abzurufen.</span><span class="sxs-lookup"><span data-stu-id="3115a-136">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="3115a-137">unpkg ist ein CDN (Content Delivery Network), mit dem Sie alles bereitstellen können, was im npm (Node.js-Paket-Manager) zu finden ist.</span><span class="sxs-lookup"><span data-stu-id="3115a-137">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3115a-138">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3115a-138">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="3115a-139">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Client-Side Library** (Clientseitige Bibliothek) aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-139">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="3115a-140">Wählen Sie **unpkg** im Dialogfeld **Add Client-Side Library** (Clientseitige Bibliothek hinzufügen) als **Anbieter** aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-140">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="3115a-141">Geben Sie für **Bibliothek** `@aspnet/signalr@next` ein.</span><span class="sxs-lookup"><span data-stu-id="3115a-141">For **Library**, enter `@aspnet/signalr@next`.</span></span>
<!-- when 3.0 is released, change @next to @latest -->

* <span data-ttu-id="3115a-142">Klicken Sie auf **Choose specific files** (Spezifische Dateien auswählen), erweitern Sie den Ordner *dist/browser*, und wählen Sie *signalr.js* und *signalr.min.js* aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-142">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="3115a-143">Legen Sie *wwwroot/lib/signalr/* als **Zielspeicherort** fest, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="3115a-143">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“: Auswählen der Bibliothek](signalr/_static/3.x/libman1.png)

  <span data-ttu-id="3115a-145">Der Ordner *wwwroot/lib/signalr* wird von LibMan erstellt, und die ausgewählten Dateien werden in ihn hineinkopiert.</span><span class="sxs-lookup"><span data-stu-id="3115a-145">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3115a-146">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3115a-146">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="3115a-147">Führen Sie den folgenden Befehl über das integrierte Terminal aus, um LibMan zu installieren.</span><span class="sxs-lookup"><span data-stu-id="3115a-147">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="3115a-148">Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.</span><span class="sxs-lookup"><span data-stu-id="3115a-148">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="3115a-149">Es kann einige Sekunden dauern, bis die Ausgabe angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="3115a-149">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="3115a-150">Die Parameter legen folgende Optionen fest:</span><span class="sxs-lookup"><span data-stu-id="3115a-150">The parameters specify the following options:</span></span>
  * <span data-ttu-id="3115a-151">Die Verwendung des Anbieters „unpkg“.</span><span class="sxs-lookup"><span data-stu-id="3115a-151">Use the unpkg provider.</span></span>
  * <span data-ttu-id="3115a-152">Das Kopieren der Dateien in den Zielordner *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="3115a-152">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="3115a-153">Das Kopieren von ausschließlich den angegebenen Dateien.</span><span class="sxs-lookup"><span data-stu-id="3115a-153">Copy only the specified files.</span></span>

  <span data-ttu-id="3115a-154">Die Ausgabe sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="3115a-154">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3115a-155">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="3115a-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3115a-156">Führen Sie den folgenden Befehl über das **Terminal** aus, um LibMan zu installieren.</span><span class="sxs-lookup"><span data-stu-id="3115a-156">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="3115a-157">Navigieren Sie zum Projektordner (der die Datei *SignalRChat.csproj* enthält).</span><span class="sxs-lookup"><span data-stu-id="3115a-157">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="3115a-158">Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.</span><span class="sxs-lookup"><span data-stu-id="3115a-158">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr@next -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="3115a-159">Die Parameter legen folgende Optionen fest:</span><span class="sxs-lookup"><span data-stu-id="3115a-159">The parameters specify the following options:</span></span>
  * <span data-ttu-id="3115a-160">Die Verwendung des Anbieters „unpkg“.</span><span class="sxs-lookup"><span data-stu-id="3115a-160">Use the unpkg provider.</span></span>
  * <span data-ttu-id="3115a-161">Das Kopieren der Dateien in den Zielordner *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="3115a-161">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="3115a-162">Das Kopieren von ausschließlich den angegebenen Dateien.</span><span class="sxs-lookup"><span data-stu-id="3115a-162">Copy only the specified files.</span></span>

  <span data-ttu-id="3115a-163">Die Ausgabe sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="3115a-163">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@next" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="3115a-164">Erstellen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="3115a-164">Create a SignalR hub</span></span>

<span data-ttu-id="3115a-165">Ein *Hub* ist eine Klasse, die als grundlegende Pipeline verwendet wird, mit der Client/Server-Kommunikation verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="3115a-165">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="3115a-166">Erstellen Sie im SignalRChat-Projektordner einen *Hubs*-Ordner.</span><span class="sxs-lookup"><span data-stu-id="3115a-166">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="3115a-167">Erstellen Sie im *Hubs*-Ordner eine *ChatHub.cs*-Datei mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="3115a-167">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/ChatHub.cs)]

  <span data-ttu-id="3115a-168">Die `ChatHub`-Klasse erbt von der `Hub`-Klasse von SignalR.</span><span class="sxs-lookup"><span data-stu-id="3115a-168">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="3115a-169">Die `Hub`-Klasse verwaltet Verbindungen, Gruppen und Messaging.</span><span class="sxs-lookup"><span data-stu-id="3115a-169">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="3115a-170">Die `SendMessage`-Methode kann von einem verbundenen Client aufgerufen werden, um eine Nachricht an alle Clients zu senden.</span><span class="sxs-lookup"><span data-stu-id="3115a-170">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="3115a-171">JavaScript-Clientcode, in dem die Methode aufgerufen wird, wird später in diesem Tutorial vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="3115a-171">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="3115a-172">SignalR-Code ist asynchron, damit die maximale Skalierbarkeit gewährleistet werden kann.</span><span class="sxs-lookup"><span data-stu-id="3115a-172">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="3115a-173">Konfigurieren von SignalR</span><span class="sxs-lookup"><span data-stu-id="3115a-173">Configure SignalR</span></span>

<span data-ttu-id="3115a-174">Der SignalR-Server muss zunächst konfiguriert werden, um Anforderungen an SignalR zu senden.</span><span class="sxs-lookup"><span data-stu-id="3115a-174">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="3115a-175">Fügen Sie der *Startup.cs*-Datei den folgenden hervorgehobenen Code zu.</span><span class="sxs-lookup"><span data-stu-id="3115a-175">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/3.x/Startup.cs?highlight=6,30,58)]

  <span data-ttu-id="3115a-176">Durch diese Änderungen wird SignalR zur ASP.NET Core-Abhängigkeitsinjektion und zu den Routingsystemen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="3115a-176">These changes add SignalR to the ASP.NET Core dependency injection and routing systems.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="3115a-177">Hinzufügen des SignalR-Clientcodes</span><span class="sxs-lookup"><span data-stu-id="3115a-177">Add SignalR client code</span></span>

* <span data-ttu-id="3115a-178">Ersetzen Sie den Inhalt in *Pages\Index.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="3115a-178">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/3.x/Index.cshtml)]

  <span data-ttu-id="3115a-179">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="3115a-179">The preceding code:</span></span>

  * <span data-ttu-id="3115a-180">erstellt Textfelder für den Namen und den Nachrichtentext sowie eine Senden-Schaltfläche</span><span class="sxs-lookup"><span data-stu-id="3115a-180">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="3115a-181">erstellt eine Liste mit `id="messagesList"` zum Anzeigen von Nachrichten, die vom SignalR-Hub empfangen werden</span><span class="sxs-lookup"><span data-stu-id="3115a-181">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="3115a-182">schließt Skriptverweise sowie den *chat.js*-Anwendungscode, den Sie im folgenden Schritt erstellen, in SignalR ein</span><span class="sxs-lookup"><span data-stu-id="3115a-182">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="3115a-183">Erstellen Sie im *wwwroot/js*-Ordner eine *chat.js*-Datei mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="3115a-183">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/3.x/chat.js)]

  <span data-ttu-id="3115a-184">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="3115a-184">The preceding code:</span></span>

  * <span data-ttu-id="3115a-185">erstellt und startet eine Verbindung</span><span class="sxs-lookup"><span data-stu-id="3115a-185">Creates and starts a connection.</span></span>
  * <span data-ttu-id="3115a-186">fügt einen Handler zur Senden-Schaltfläche hinzu, der Nachrichten an den Hub sendet</span><span class="sxs-lookup"><span data-stu-id="3115a-186">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="3115a-187">fügt einen Handler zum Verbindungsobjekt hinzu, der Nachrichten vom Hub empfängt und diese der Liste hinzufügt</span><span class="sxs-lookup"><span data-stu-id="3115a-187">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="3115a-188">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="3115a-188">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3115a-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3115a-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3115a-190">Drücken Sie **STRG+F5**, um die App ohne Debugging zu starten.</span><span class="sxs-lookup"><span data-stu-id="3115a-190">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3115a-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3115a-191">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3115a-192">Führen Sie über das integrierte Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="3115a-192">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3115a-193">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="3115a-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3115a-194">Wählen Sie im Menü **Ausführen > Starten ohne Debuggen** aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-194">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="3115a-195">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="3115a-195">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="3115a-196">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Nachricht senden**.</span><span class="sxs-lookup"><span data-stu-id="3115a-196">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="3115a-197">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3115a-197">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR-Beispiel-App](signalr/_static/3.x/signalr-get-started-finished.png)

> [!TIP]
> * <span data-ttu-id="3115a-199">Wenn die App nicht funktioniert, öffnen Sie die Browser-Entwicklungstools (F12), und wechseln Sie zur Konsole.</span><span class="sxs-lookup"><span data-stu-id="3115a-199">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="3115a-200">Möglicherweise werden Fehler in Bezug auf Ihren HTML- und JavaScript-Code angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3115a-200">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="3115a-201">Nehmen wir an, dass Sie z.B. *signalr.js* in einen anderen Ordner als vorgeschrieben platziert haben.</span><span class="sxs-lookup"><span data-stu-id="3115a-201">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="3115a-202">In diesem Fall funktioniert der Verweis auf diese Datei nicht, und Ihnen wird der Fehler 404 in der Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3115a-202">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
>   <span data-ttu-id="3115a-203">![Fehler „signalr.js nicht gefunden“](signalr/_static/3.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="3115a-203">![signalr.js not found error](signalr/_static/3.x/f12-console.png)</span></span>
> * <span data-ttu-id="3115a-204">Wenn Sie den Fehler ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome oder NS_ERROR_NET_INADEQUATE_SECURITY in Firefox erhalten, führen Sie folgende Befehle aus, um das Entwicklungszertifikat zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="3115a-204">If you get the error ERR_SPDY_INADEQUATE_TRANSPORT_SECURITY in Chrome or NS_ERROR_NET_INADEQUATE_SECURITY in Firefox, run these commands to update your development certificate:</span></span>
>
>   ```dotnetcli
>   dotnet dev-certs https --clean
>   dotnet dev-certs https --trust
>   ```

## <a name="next-steps"></a><span data-ttu-id="3115a-205">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="3115a-205">Next steps</span></span>

<span data-ttu-id="3115a-206">Weitere Informationen zu SignalR finden Sie in der folgenden Einführung:</span><span class="sxs-lookup"><span data-stu-id="3115a-206">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3115a-207">Einführung in ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="3115a-207">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3115a-208">In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR beschrieben.</span><span class="sxs-lookup"><span data-stu-id="3115a-208">This tutorial teaches the basics of building a real-time app using SignalR.</span></span> <span data-ttu-id="3115a-209">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3115a-209">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3115a-210">Erstellen Sie ein Webprojekt.</span><span class="sxs-lookup"><span data-stu-id="3115a-210">Create a web project.</span></span>
> * <span data-ttu-id="3115a-211">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="3115a-211">Add the SignalR client library.</span></span>
> * <span data-ttu-id="3115a-212">Erstellen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="3115a-212">Create a SignalR hub.</span></span>
> * <span data-ttu-id="3115a-213">Konfigurieren des Projekts zur Verwendung von SignalR</span><span class="sxs-lookup"><span data-stu-id="3115a-213">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="3115a-214">Hinzufügen von Code, mit dem Nachrichten von jedem Client an alle verbundene Clients gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="3115a-214">Add code that sends messages from any client to all connected clients.</span></span>

<span data-ttu-id="3115a-215">Am Ende verfügen Sie über eine funktionierende Chat-App:</span><span class="sxs-lookup"><span data-stu-id="3115a-215">At the end, you'll have a working chat app:</span></span>

![SignalR-Beispiel-App](signalr/_static/2.x/signalr-get-started-finished.png)

## <a name="prerequisites"></a><span data-ttu-id="3115a-217">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="3115a-217">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3115a-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3115a-218">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2017-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3115a-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3115a-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3115a-220">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="3115a-220">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-web-project"></a><span data-ttu-id="3115a-221">Erstellen eines Webprojekts</span><span class="sxs-lookup"><span data-stu-id="3115a-221">Create a web project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3115a-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3115a-222">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="3115a-223">Klicken Sie im Menü auf **Datei > Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="3115a-223">From the menu, select **File > New Project**.</span></span>

* <span data-ttu-id="3115a-224">Wählen Sie im Dialogfeld **Neues Projekt** **Installiert > Visual C# > Web > ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-224">In the **New Project** dialog, select **Installed > Visual C# > Web > ASP.NET Core Web Application**.</span></span> <span data-ttu-id="3115a-225">Geben Sie als Projektname *SignalRChat* ein.</span><span class="sxs-lookup"><span data-stu-id="3115a-225">Name the project *SignalRChat*.</span></span>

  ![Dialogfeld „Neues Projekt“ in Visual Studio](signalr/_static/2.x/signalr-new-project-dialog.png)

* <span data-ttu-id="3115a-227">Klicken Sie auf **Webanwendung**, um ein Projekt zu erstellen, das Razor Pages verwendet.</span><span class="sxs-lookup"><span data-stu-id="3115a-227">Select **Web Application** to create a project that uses Razor Pages.</span></span>

* <span data-ttu-id="3115a-228">Wählen Sie **.NET Core** als Zielframework sowie **ASP.NET Core 2.2** aus, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="3115a-228">Select a target framework of **.NET Core**, select **ASP.NET Core 2.2**, and click **OK**.</span></span>

  ![Dialogfeld „Neues Projekt“ in Visual Studio](signalr/_static/2.x/signalr-new-project-choose-type.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3115a-230">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3115a-230">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="3115a-231">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) in dem Ordner, in dem der neue Projektordner erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="3115a-231">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal) to the folder in which the new project folder will be created.</span></span>

* <span data-ttu-id="3115a-232">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="3115a-232">Run the following commands:</span></span>

   ```dotnetcli
   dotnet new webapp -o SignalRChat
   code -r SignalRChat
   ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3115a-233">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="3115a-233">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3115a-234">Klicken Sie im Menü auf **Datei > Neue Projektmappe**.</span><span class="sxs-lookup"><span data-stu-id="3115a-234">From the menu, select **File > New Solution**.</span></span>

* <span data-ttu-id="3115a-235">Klicken Sie auf **.NET Core > App > ASP.NET Core-Web-App** (Wählen Sie nicht **ASP.NET Core-Web-App (MVC)** aus).</span><span class="sxs-lookup"><span data-stu-id="3115a-235">Select **.NET Core > App > ASP.NET Core Web App** (Don't select **ASP.NET Core Web App (MVC)**).</span></span>

* <span data-ttu-id="3115a-236">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="3115a-236">Select **Next**.</span></span>

* <span data-ttu-id="3115a-237">Nennen Sie das Projekt *SignalRChat*, und wählen Sie dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-237">Name the project *SignalRChat*, and then select **Create**.</span></span>

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="3115a-238">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="3115a-238">Add the SignalR client library</span></span>

<span data-ttu-id="3115a-239">Die SignalR-Serverbibliothek ist im Metapaket `Microsoft.AspNetCore.App` enthalten.</span><span class="sxs-lookup"><span data-stu-id="3115a-239">The SignalR server library is included in the `Microsoft.AspNetCore.App` metapackage.</span></span> <span data-ttu-id="3115a-240">Die JavaScript-Clientbibliothek ist nicht automatisch im Projekt enthalten.</span><span class="sxs-lookup"><span data-stu-id="3115a-240">The JavaScript client library isn't automatically included in the project.</span></span> <span data-ttu-id="3115a-241">In diesem Tutorial verwenden Sie den Bibliotheks-Manager (LibMan), um die Clientbibliothek von *unpkg* abzurufen.</span><span class="sxs-lookup"><span data-stu-id="3115a-241">For this tutorial, you use Library Manager (LibMan) to get the client library from *unpkg*.</span></span> <span data-ttu-id="3115a-242">unpkg ist ein CDN (Content Delivery Network), mit dem Sie alles bereitstellen können, was im npm (Node.js-Paket-Manager) zu finden ist.</span><span class="sxs-lookup"><span data-stu-id="3115a-242">unpkg is a content delivery network (CDN)) that can deliver anything found in npm, the Node.js package manager.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3115a-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3115a-243">Visual Studio</span></span>](#tab/visual-studio/)

* <span data-ttu-id="3115a-244">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Client-Side Library** (Clientseitige Bibliothek) aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-244">In **Solution Explorer**, right-click the project, and select **Add** > **Client-Side Library**.</span></span>

* <span data-ttu-id="3115a-245">Wählen Sie **unpkg** im Dialogfeld **Add Client-Side Library** (Clientseitige Bibliothek hinzufügen) als **Anbieter** aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-245">In the **Add Client-Side Library** dialog, for **Provider** select **unpkg**.</span></span>

* <span data-ttu-id="3115a-246">Geben Sie `@aspnet/signalr@1` für die **Bibliothek** ein, und wählen Sie die neueste Version aus, die keine Vorschauversion ist.</span><span class="sxs-lookup"><span data-stu-id="3115a-246">For **Library**, enter `@aspnet/signalr@1`, and select the latest version that isn't preview.</span></span>

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“: Auswählen der Bibliothek](signalr/_static/2.x/libman1.png)

* <span data-ttu-id="3115a-248">Klicken Sie auf **Choose specific files** (Spezifische Dateien auswählen), erweitern Sie den Ordner *dist/browser*, und wählen Sie *signalr.js* und *signalr.min.js* aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-248">Select **Choose specific files**, expand the *dist/browser* folder, and select *signalr.js* and *signalr.min.js*.</span></span>

* <span data-ttu-id="3115a-249">Legen Sie *wwwroot/lib/signalr/* als **Zielspeicherort** fest, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="3115a-249">Set **Target Location** to *wwwroot/lib/signalr/*, and select **Install**.</span></span>

  ![Dialogfeld „Clientseitige Bibliothek hinzufügen“: Auswählen der Dateien und des Zielspeicherorts](signalr/_static/2.x/libman2.png)

  <span data-ttu-id="3115a-251">Der Ordner *wwwroot/lib/signalr* wird von LibMan erstellt, und die ausgewählten Dateien werden in ihn hineinkopiert.</span><span class="sxs-lookup"><span data-stu-id="3115a-251">LibMan creates a *wwwroot/lib/signalr* folder and copies the selected files to it.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3115a-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3115a-252">Visual Studio Code</span></span>](#tab/visual-studio-code/)

* <span data-ttu-id="3115a-253">Führen Sie den folgenden Befehl über das integrierte Terminal aus, um LibMan zu installieren.</span><span class="sxs-lookup"><span data-stu-id="3115a-253">In the integrated terminal, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="3115a-254">Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.</span><span class="sxs-lookup"><span data-stu-id="3115a-254">Run the following command to get the SignalR client library by using LibMan.</span></span> <span data-ttu-id="3115a-255">Es kann einige Sekunden dauern, bis die Ausgabe angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="3115a-255">You might have to wait a few seconds before seeing output.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="3115a-256">Die Parameter legen folgende Optionen fest:</span><span class="sxs-lookup"><span data-stu-id="3115a-256">The parameters specify the following options:</span></span>
  * <span data-ttu-id="3115a-257">Die Verwendung des Anbieters „unpkg“.</span><span class="sxs-lookup"><span data-stu-id="3115a-257">Use the unpkg provider.</span></span>
  * <span data-ttu-id="3115a-258">Das Kopieren der Dateien in den Zielordner *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="3115a-258">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="3115a-259">Das Kopieren von ausschließlich den angegebenen Dateien.</span><span class="sxs-lookup"><span data-stu-id="3115a-259">Copy only the specified files.</span></span>

  <span data-ttu-id="3115a-260">Die Ausgabe sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="3115a-260">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3115a-261">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="3115a-261">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3115a-262">Führen Sie den folgenden Befehl über das **Terminal** aus, um LibMan zu installieren.</span><span class="sxs-lookup"><span data-stu-id="3115a-262">In the **Terminal**, run the following command to install LibMan.</span></span>

  ```dotnetcli
  dotnet tool install -g Microsoft.Web.LibraryManager.Cli
  ```

* <span data-ttu-id="3115a-263">Navigieren Sie zum Projektordner (der die Datei *SignalRChat.csproj* enthält).</span><span class="sxs-lookup"><span data-stu-id="3115a-263">Navigate to the project folder (the one that contains the *SignalRChat.csproj* file).</span></span>

* <span data-ttu-id="3115a-264">Führen Sie den folgenden Befehl aus, um die SignalR-Clientbibliothek mit LibMan abzurufen.</span><span class="sxs-lookup"><span data-stu-id="3115a-264">Run the following command to get the SignalR client library by using LibMan.</span></span>

  ```console
  libman install @aspnet/signalr -p unpkg -d wwwroot/lib/signalr --files dist/browser/signalr.js --files dist/browser/signalr.min.js
  ```

  <span data-ttu-id="3115a-265">Die Parameter legen folgende Optionen fest:</span><span class="sxs-lookup"><span data-stu-id="3115a-265">The parameters specify the following options:</span></span>
  * <span data-ttu-id="3115a-266">Die Verwendung des Anbieters „unpkg“.</span><span class="sxs-lookup"><span data-stu-id="3115a-266">Use the unpkg provider.</span></span>
  * <span data-ttu-id="3115a-267">Das Kopieren der Dateien in den Zielordner *wwwroot/lib/signalr*.</span><span class="sxs-lookup"><span data-stu-id="3115a-267">Copy files to the *wwwroot/lib/signalr* destination.</span></span>
  * <span data-ttu-id="3115a-268">Das Kopieren von ausschließlich den angegebenen Dateien.</span><span class="sxs-lookup"><span data-stu-id="3115a-268">Copy only the specified files.</span></span>

  <span data-ttu-id="3115a-269">Die Ausgabe sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="3115a-269">The output looks like the following example:</span></span>

  ```console
  wwwroot/lib/signalr/dist/browser/signalr.js written to disk
  wwwroot/lib/signalr/dist/browser/signalr.min.js written to disk
  Installed library "@aspnet/signalr@1.0.3" to "wwwroot/lib/signalr"
  ```

---

## <a name="create-a-signalr-hub"></a><span data-ttu-id="3115a-270">Erstellen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="3115a-270">Create a SignalR hub</span></span>

<span data-ttu-id="3115a-271">Ein *Hub* ist eine Klasse, die als grundlegende Pipeline verwendet wird, mit der Client/Server-Kommunikation verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="3115a-271">A *hub* is a class that serves as a high-level pipeline that handles client-server communication.</span></span>

* <span data-ttu-id="3115a-272">Erstellen Sie im SignalRChat-Projektordner einen *Hubs*-Ordner.</span><span class="sxs-lookup"><span data-stu-id="3115a-272">In the SignalRChat project folder, create a *Hubs* folder.</span></span>

* <span data-ttu-id="3115a-273">Erstellen Sie im *Hubs*-Ordner eine *ChatHub.cs*-Datei mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="3115a-273">In the *Hubs* folder, create a *ChatHub.cs* file with the following code:</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/ChatHub.cs)]

  <span data-ttu-id="3115a-274">Die `ChatHub`-Klasse erbt von der `Hub`-Klasse von SignalR.</span><span class="sxs-lookup"><span data-stu-id="3115a-274">The `ChatHub` class inherits from the SignalR `Hub` class.</span></span> <span data-ttu-id="3115a-275">Die `Hub`-Klasse verwaltet Verbindungen, Gruppen und Messaging.</span><span class="sxs-lookup"><span data-stu-id="3115a-275">The `Hub` class manages connections, groups, and messaging.</span></span>

  <span data-ttu-id="3115a-276">Die `SendMessage`-Methode kann von einem verbundenen Client aufgerufen werden, um eine Nachricht an alle Clients zu senden.</span><span class="sxs-lookup"><span data-stu-id="3115a-276">The `SendMessage` method can be called by a connected client to send a message to all clients.</span></span> <span data-ttu-id="3115a-277">JavaScript-Clientcode, in dem die Methode aufgerufen wird, wird später in diesem Tutorial vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="3115a-277">JavaScript client code that calls the method is shown later in the tutorial.</span></span> <span data-ttu-id="3115a-278">SignalR-Code ist asynchron, damit die maximale Skalierbarkeit gewährleistet werden kann.</span><span class="sxs-lookup"><span data-stu-id="3115a-278">SignalR code is asynchronous to provide maximum scalability.</span></span>

## <a name="configure-signalr"></a><span data-ttu-id="3115a-279">Konfigurieren von SignalR</span><span class="sxs-lookup"><span data-stu-id="3115a-279">Configure SignalR</span></span>

<span data-ttu-id="3115a-280">Der SignalR-Server muss zunächst konfiguriert werden, um Anforderungen an SignalR zu senden.</span><span class="sxs-lookup"><span data-stu-id="3115a-280">The SignalR server must be configured to pass SignalR requests to SignalR.</span></span>

* <span data-ttu-id="3115a-281">Fügen Sie der *Startup.cs*-Datei den folgenden hervorgehobenen Code zu.</span><span class="sxs-lookup"><span data-stu-id="3115a-281">Add the following highlighted code to the *Startup.cs* file.</span></span>

  [!code-csharp[Startup](signalr/sample-snapshot/2.x/Startup.cs?highlight=7,33,52-55)]

  <span data-ttu-id="3115a-282">Durch diese Änderungen wird SignalR zum Dependency Injection-System von ASP.NET Core sowie der Middlewarepipeline hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="3115a-282">These changes add SignalR to the ASP.NET Core dependency injection system and the middleware pipeline.</span></span>

## <a name="add-signalr-client-code"></a><span data-ttu-id="3115a-283">Hinzufügen des SignalR-Clientcodes</span><span class="sxs-lookup"><span data-stu-id="3115a-283">Add SignalR client code</span></span>

* <span data-ttu-id="3115a-284">Ersetzen Sie den Inhalt in *Pages\Index.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="3115a-284">Replace the content in *Pages\Index.cshtml* with the following code:</span></span>

  [!code-cshtml[Index](signalr/sample-snapshot/2.x/Index.cshtml)]

  <span data-ttu-id="3115a-285">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="3115a-285">The preceding code:</span></span>

  * <span data-ttu-id="3115a-286">erstellt Textfelder für den Namen und den Nachrichtentext sowie eine Senden-Schaltfläche</span><span class="sxs-lookup"><span data-stu-id="3115a-286">Creates text boxes for name and message text, and a submit button.</span></span>
  * <span data-ttu-id="3115a-287">erstellt eine Liste mit `id="messagesList"` zum Anzeigen von Nachrichten, die vom SignalR-Hub empfangen werden</span><span class="sxs-lookup"><span data-stu-id="3115a-287">Creates a list with `id="messagesList"` for displaying messages that are received from the SignalR hub.</span></span>
  * <span data-ttu-id="3115a-288">schließt Skriptverweise sowie den *chat.js*-Anwendungscode, den Sie im folgenden Schritt erstellen, in SignalR ein</span><span class="sxs-lookup"><span data-stu-id="3115a-288">Includes script references to SignalR and the *chat.js* application code that you create in the next step.</span></span>

* <span data-ttu-id="3115a-289">Erstellen Sie im *wwwroot/js*-Ordner eine *chat.js*-Datei mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="3115a-289">In the *wwwroot/js* folder, create a *chat.js* file with the following code:</span></span>

  [!code-javascript[Index](signalr/sample-snapshot/2.x/chat.js)]

  <span data-ttu-id="3115a-290">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="3115a-290">The preceding code:</span></span>

  * <span data-ttu-id="3115a-291">erstellt und startet eine Verbindung</span><span class="sxs-lookup"><span data-stu-id="3115a-291">Creates and starts a connection.</span></span>
  * <span data-ttu-id="3115a-292">fügt einen Handler zur Senden-Schaltfläche hinzu, der Nachrichten an den Hub sendet</span><span class="sxs-lookup"><span data-stu-id="3115a-292">Adds to the submit button a handler that sends messages to the hub.</span></span>
  * <span data-ttu-id="3115a-293">fügt einen Handler zum Verbindungsobjekt hinzu, der Nachrichten vom Hub empfängt und diese der Liste hinzufügt</span><span class="sxs-lookup"><span data-stu-id="3115a-293">Adds to the connection object a handler that receives messages from the hub and adds them to the list.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="3115a-294">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="3115a-294">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3115a-295">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3115a-295">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="3115a-296">Drücken Sie **STRG+F5**, um die App ohne Debugging zu starten.</span><span class="sxs-lookup"><span data-stu-id="3115a-296">Press **CTRL+F5** to run the app without debugging.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3115a-297">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3115a-297">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="3115a-298">Führen Sie über das integrierte Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="3115a-298">In the integrated terminal, run the following command:</span></span>

  ```dotnetcli
  dotnet run -p SignalRChat.csproj
  ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3115a-299">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="3115a-299">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="3115a-300">Wählen Sie im Menü **Ausführen > Starten ohne Debuggen** aus.</span><span class="sxs-lookup"><span data-stu-id="3115a-300">From the menu, select **Run > Start Without Debugging**.</span></span>

---

* <span data-ttu-id="3115a-301">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="3115a-301">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

* <span data-ttu-id="3115a-302">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Nachricht senden**.</span><span class="sxs-lookup"><span data-stu-id="3115a-302">Choose either browser, enter a name and message, and select the **Send Message** button.</span></span>

  <span data-ttu-id="3115a-303">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3115a-303">The name and message are displayed on both pages instantly.</span></span>

  ![SignalR-Beispiel-App](signalr/_static/2.x/signalr-get-started-finished.png)

> [!TIP]
> <span data-ttu-id="3115a-305">Wenn die App nicht funktioniert, öffnen Sie die Browser-Entwicklungstools (F12), und wechseln Sie zur Konsole.</span><span class="sxs-lookup"><span data-stu-id="3115a-305">If the app doesn't work, open your browser developer tools (F12) and go to the console.</span></span> <span data-ttu-id="3115a-306">Möglicherweise werden Fehler in Bezug auf Ihren HTML- und JavaScript-Code angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3115a-306">You might see errors related to your HTML and JavaScript code.</span></span> <span data-ttu-id="3115a-307">Nehmen wir an, dass Sie z.B. *signalr.js* in einen anderen Ordner als vorgeschrieben platziert haben.</span><span class="sxs-lookup"><span data-stu-id="3115a-307">For example, suppose you put *signalr.js* in a different folder than directed.</span></span> <span data-ttu-id="3115a-308">In diesem Fall funktioniert der Verweis auf diese Datei nicht, und Ihnen wird der Fehler 404 in der Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="3115a-308">In that case the reference to that file won't work and you'll see a 404 error in the console.</span></span>
> <span data-ttu-id="3115a-309">![Fehler „signalr.js nicht gefunden“](signalr/_static/2.x/f12-console.png)</span><span class="sxs-lookup"><span data-stu-id="3115a-309">![signalr.js not found error](signalr/_static/2.x/f12-console.png)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3115a-310">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3115a-310">Additional resources</span></span>

* [<span data-ttu-id="3115a-311">Dieses Tutorials auf YouTube</span><span class="sxs-lookup"><span data-stu-id="3115a-311">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=iKlVmu-r0JQ)

## <a name="next-steps"></a><span data-ttu-id="3115a-312">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="3115a-312">Next steps</span></span>

<span data-ttu-id="3115a-313">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="3115a-313">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3115a-314">Erstellen Sie ein Web-App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="3115a-314">Create a web app project.</span></span>
> * <span data-ttu-id="3115a-315">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="3115a-315">Add the SignalR client library.</span></span>
> * <span data-ttu-id="3115a-316">Erstellen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="3115a-316">Create a SignalR hub.</span></span>
> * <span data-ttu-id="3115a-317">Konfigurieren des Projekts zur Verwendung von SignalR</span><span class="sxs-lookup"><span data-stu-id="3115a-317">Configure the project to use SignalR.</span></span>
> * <span data-ttu-id="3115a-318">Hinzufügen von Code, der den Hub verwendet, um Nachrichten von einem beliebigen Client an alle verbundenen Clients zu senden</span><span class="sxs-lookup"><span data-stu-id="3115a-318">Add code that uses the hub to send messages from any client to all connected clients.</span></span>

<span data-ttu-id="3115a-319">Weitere Informationen zu SignalR finden Sie in der folgenden Einführung:</span><span class="sxs-lookup"><span data-stu-id="3115a-319">To learn more about SignalR, see the introduction:</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="3115a-320">Einführung in ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="3115a-320">Introduction to ASP.NET Core SignalR</span></span>](xref:signalr/introduction)

::: moniker-end
