---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr
title: 'Tutorial: Echtzeit-Chat mit SignalR 2 | Microsoft-Dokumentation'
author: pfletcher
description: Dieses Tutorial zeigt, wie Sie mithilfe von SignalR eine Chatanwendung mit Echtzeitfunktionalität erstellen. Eine leere ASP.NET-Webanwendung hinzugefügt SignalR.
ms.author: riande
ms.date: 01/02/2019
ms.assetid: a8b3b778-f009-4369-85c7-e90f9878d8b4
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: aa015abc47bb2450e04e167c0404aaa1d119ba2c
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098623"
---
# <a name="tutorial-real-time-chat-with-signalr-2"></a><span data-ttu-id="c660d-104">Tutorial: Echtzeit-Chat mit SignalR 2</span><span class="sxs-lookup"><span data-stu-id="c660d-104">Tutorial: Real-time chat with SignalR 2</span></span>

<span data-ttu-id="c660d-105">In diesem Tutorial erfahren Sie, wie mit SignalR eine chatanwendung mit Echtzeitfunktionalität erstellen können.</span><span class="sxs-lookup"><span data-stu-id="c660d-105">This tutorial shows you how to use SignalR to create a real-time chat application.</span></span> <span data-ttu-id="c660d-106">Sie SignalR eine leere ASP.NET-Webanwendung hinzufügen und erstellen Sie eine HTML-Seite zum Senden und Meldungen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="c660d-106">You add SignalR to an empty ASP.NET web application and create an HTML page to send and display messages.</span></span>

<span data-ttu-id="c660d-107">In diesem Tutorial haben Sie:</span><span class="sxs-lookup"><span data-stu-id="c660d-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c660d-108">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="c660d-108">Set up the project</span></span>
> * <span data-ttu-id="c660d-109">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="c660d-109">Run the sample</span></span>
> * <span data-ttu-id="c660d-110">Überprüfen Sie den code</span><span class="sxs-lookup"><span data-stu-id="c660d-110">Examine the code</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a><span data-ttu-id="c660d-111">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="c660d-111">Prerequisites</span></span>

* <span data-ttu-id="c660d-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) mit der **ASP.NET und Webentwicklung** arbeitsauslastung.</span><span class="sxs-lookup"><span data-stu-id="c660d-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="c660d-113">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="c660d-113">Set up the Project</span></span>

<span data-ttu-id="c660d-114">Fügen Sie in diesem Abschnitt zeigt, wie Sie Visual Studio 2017 und SignalR 2 verwenden, um eine leere ASP.NET-Webanwendung erstellen SignalR hinzu, und erstellen Sie die chatanwendung.</span><span class="sxs-lookup"><span data-stu-id="c660d-114">This section shows how to use Visual Studio 2017 and SignalR 2 to create an empty ASP.NET web application, add SignalR, and create the chat application.</span></span>

1. <span data-ttu-id="c660d-115">Erstellen Sie eine ASP.NET-Webanwendung in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c660d-115">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![Erstellen Sie web](tutorial-getting-started-with-signalr/_static/image2.png)

1. <span data-ttu-id="c660d-117">In der **neues ASP.NET-Projekt – SignalRChat** Fenster verlassen **leere** ausgewählt, und wählen Sie **OK**.</span><span class="sxs-lookup"><span data-stu-id="c660d-117">In the **New ASP.NET Project - SignalRChat** window, leave **Empty** selected and select **OK**.</span></span>

1. <span data-ttu-id="c660d-118">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.</span><span class="sxs-lookup"><span data-stu-id="c660d-118">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="c660d-119">In **neues Element hinzufügen - SignalRChat**Option **installiert** > **Visual C#**   >  **Web**  >  **SignalR** und wählen Sie dann **SignalR-Hubklasse (v2)**.</span><span class="sxs-lookup"><span data-stu-id="c660d-119">In **Add New Item - SignalRChat**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="c660d-120">Nennen Sie die Klasse *ChatHub* und fügen sie dem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="c660d-120">Name the class *ChatHub* and add it to the project.</span></span>

    <span data-ttu-id="c660d-121">Dieser Schritt erstellt der *ChatHub.cs* Klassendatei, und fügt einen Satz von Skriptdateien und Assemblyverweise, die dem Projekt SignalR unterstützen.</span><span class="sxs-lookup"><span data-stu-id="c660d-121">This step creates the *ChatHub.cs* class file and adds a set of script files and assembly references that support SignalR to the project.</span></span>

1. <span data-ttu-id="c660d-122">Ersetzen Sie den Code in der neuen *ChatHub.cs* Klassendatei mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c660d-122">Replace the code in the new *ChatHub.cs* class file with this code:</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]

1. <span data-ttu-id="c660d-123">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.</span><span class="sxs-lookup"><span data-stu-id="c660d-123">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="c660d-124">In **neues Element hinzufügen - SignalRChat** wählen **installiert** > **Visual C#**   >  **Web** und klicken Sie dann Wählen Sie **OWIN-Startklasse**.</span><span class="sxs-lookup"><span data-stu-id="c660d-124">In **Add New Item - SignalRChat** select **Installed** > **Visual C#** > **Web**  and then select **OWIN Startup Class**.</span></span>

1. <span data-ttu-id="c660d-125">Nennen Sie die Klasse *Start* und fügen sie dem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="c660d-125">Name the class *Startup* and add it to the project.</span></span>

1. <span data-ttu-id="c660d-126">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **HTML-Seite**.</span><span class="sxs-lookup"><span data-stu-id="c660d-126">In **Solution Explorer**, right-click the project and select **Add** > **HTML Page**.</span></span>

1. <span data-ttu-id="c660d-127">Nennen Sie die neue Seite *Index* , und wählen Sie **OK**.</span><span class="sxs-lookup"><span data-stu-id="c660d-127">Name the new page *index* and select **OK**.</span></span>

1. <span data-ttu-id="c660d-128">In **Projektmappen-Explorer**mit der rechten Maustaste auf die HTML-Seite, die Sie erstellt haben, und wählen Sie **als Startseite festlegen**.</span><span class="sxs-lookup"><span data-stu-id="c660d-128">In **Solution Explorer**, right-click the HTML page you created and select **Set as Start Page**.</span></span>

1. <span data-ttu-id="c660d-129">Ersetzen Sie den Standardcode in der HTML-Seite mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c660d-129">Replace the default code in the HTML page with this code:</span></span>

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample3.html)]

1. <span data-ttu-id="c660d-130">In **Projektmappen-Explorer**, erweitern Sie **Skripts**.</span><span class="sxs-lookup"><span data-stu-id="c660d-130">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="c660d-131">Skriptbibliotheken für jQuery und SignalR werden in das Projekt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c660d-131">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="c660d-132">Der Paket-Manager möglicherweise eine höhere Version von SignalR-Skripts installiert.</span><span class="sxs-lookup"><span data-stu-id="c660d-132">The package manager may have installed a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="c660d-133">Überprüfen Sie, dass die Skriptverweise im Codeblock auf die Versionen der Skriptdateien im Projekt entsprechen.</span><span class="sxs-lookup"><span data-stu-id="c660d-133">Check that the script references in the code block correspond to the versions of the script files in the project.</span></span>

    <span data-ttu-id="c660d-134">Die Skriptverweise aus der ursprünglichen Codeblock:</span><span class="sxs-lookup"><span data-stu-id="c660d-134">Script references from the original code block:</span></span>
    ```html
    <!--Script references. -->
    <!--Reference the jQuery library. -->
    <script src="Scripts/jquery-3.1.1.min.js" ></script>
    <!--Reference the SignalR library. -->
    <script src="Scripts/jquery.signalR-2.2.1.min.js"></script>
    ```

1. <span data-ttu-id="c660d-135">Wenn sie nicht übereinstimmen, aktualisieren Sie die *.html* Datei.</span><span class="sxs-lookup"><span data-stu-id="c660d-135">If they don't match, update the *.html* file.</span></span>

1. <span data-ttu-id="c660d-136">Wählen Sie in der Menüleiste **Datei** > **Alles speichern**.</span><span class="sxs-lookup"><span data-stu-id="c660d-136">From the menu bar, select **File** > **Save All**.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="c660d-137">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="c660d-137">Run the Sample</span></span>

1. <span data-ttu-id="c660d-138">In der Symbolleiste aktivieren **Skriptdebugging** und wählen Sie dann auf die entsprechende Schaltfläche, um das Beispiel im Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c660d-138">In the toolbar, turn on **Script Debugging** and then select the play button to run the sample in Debug mode.</span></span>

    ![Benutzername eingeben](tutorial-getting-started-with-signalr/_static/image3.png)

1. <span data-ttu-id="c660d-140">Wenn der Browser geöffnet wird, geben Sie einen Namen für Ihre Chat-Identität aus.</span><span class="sxs-lookup"><span data-stu-id="c660d-140">When the browser opens, enter a name for your chat identity.</span></span>

1. <span data-ttu-id="c660d-141">Kopieren Sie die URL aus dem Browser, öffnen Sie zwei andere Browser, und fügen Sie die URLs in die Adresse leisten.</span><span class="sxs-lookup"><span data-stu-id="c660d-141">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

1. <span data-ttu-id="c660d-142">Geben Sie in jedem Browser einen eindeutigen Namen ein.</span><span class="sxs-lookup"><span data-stu-id="c660d-142">In each browser, enter a unique name.</span></span>

1. <span data-ttu-id="c660d-143">Fügen Sie nun einen Kommentar, und wählen **senden**.</span><span class="sxs-lookup"><span data-stu-id="c660d-143">Now, add a comment and select **Send**.</span></span> <span data-ttu-id="c660d-144">Wiederholen Sie, die in den anderen Browsern.</span><span class="sxs-lookup"><span data-stu-id="c660d-144">Repeat that in the other browsers.</span></span> <span data-ttu-id="c660d-145">Die Kommentare werden in Echtzeit angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c660d-145">The comments appear in real-time.</span></span>

    > [!NOTE]
    > <span data-ttu-id="c660d-146">Diese einfache chatanwendung werden den Kontext der Diskussion auf dem Server nicht verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="c660d-146">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="c660d-147">Der Hub sendet, Kommentare, um alle aktuellen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="c660d-147">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="c660d-148">Benutzer, die im Chat später zusammenfügen sehen, dass Nachrichten ab dem Zeitpunkt hinzugefügt werden, dass sie aufgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="c660d-148">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="c660d-149">Sehen Sie, wie der Chat-Anwendung in drei verschiedenen Browsern ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="c660d-149">See how the chat application runs in three different browsers.</span></span> <span data-ttu-id="c660d-150">Beim Tom, Anand und Susan Senden von Nachrichten von allen Browsern, die in Echtzeit aktualisiert werden:</span><span class="sxs-lookup"><span data-stu-id="c660d-150">When Tom, Anand, and Susan send messages, all browsers update in real time:</span></span>

    ![Alle drei in Browsern angezeigt, den gleichen Chatverlauf](tutorial-getting-started-with-signalr/_static/image4.png)

1. <span data-ttu-id="c660d-152">In **Projektmappen-Explorer**, überprüfen Sie die **Skriptdokumente** Knoten für die ausgeführte Anwendung.</span><span class="sxs-lookup"><span data-stu-id="c660d-152">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="c660d-153">Es gibt eine Skriptdatei namens *Hubs* , die den SignalR-Bibliothek zur Laufzeit generiert.</span><span class="sxs-lookup"><span data-stu-id="c660d-153">There's a script file named *hubs* that the SignalR library generates at runtime.</span></span> <span data-ttu-id="c660d-154">Diese Datei, verwaltet die Kommunikation zwischen der jQuery-Skripts und serverseitigen Code.</span><span class="sxs-lookup"><span data-stu-id="c660d-154">This file manages the communication between jQuery script and server-side code.</span></span>

    ![automatisch generierte-Hubs-Skript im Knoten "Skriptdokumente"](tutorial-getting-started-with-signalr/_static/image5.png)

## <a name="examine-the-code"></a><span data-ttu-id="c660d-156">Überprüfen Sie den Code</span><span class="sxs-lookup"><span data-stu-id="c660d-156">Examine the Code</span></span>

<span data-ttu-id="c660d-157">Die Anwendung SignalRChat veranschaulicht zwei grundlegende Aufgaben für die SignalR-Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="c660d-157">The SignalRChat application demonstrates two basic SignalR development tasks.</span></span> <span data-ttu-id="c660d-158">Es veranschaulicht einen Hub erstellen.</span><span class="sxs-lookup"><span data-stu-id="c660d-158">It shows you how to create a hub.</span></span> <span data-ttu-id="c660d-159">Der Server verwendet den Hub als Haupt-Coordination-Objekt.</span><span class="sxs-lookup"><span data-stu-id="c660d-159">The server uses that hub as the main coordination object.</span></span> <span data-ttu-id="c660d-160">Der Hub verwendet die SignalR-jQuery-Bibliothek zum Senden und Empfangen von Nachrichten an.</span><span class="sxs-lookup"><span data-stu-id="c660d-160">The hub uses the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs-in-the-chathubcs"></a><span data-ttu-id="c660d-161">SignalR-Hubs in der ChatHub.cs</span><span class="sxs-lookup"><span data-stu-id="c660d-161">SignalR Hubs in the ChatHub.cs</span></span>

<span data-ttu-id="c660d-162">Im obigen Codebeispiel die `ChatHub` Klasse leitet sich von der `Microsoft.AspNet.SignalR.Hub` Klasse.</span><span class="sxs-lookup"><span data-stu-id="c660d-162">In the code sample above, the `ChatHub` class derives from the `Microsoft.AspNet.SignalR.Hub` class.</span></span> <span data-ttu-id="c660d-163">Ableiten von der `Hub` Klasse ist eine gute Möglichkeit, eine SignalR-Anwendung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c660d-163">Deriving from the `Hub` class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="c660d-164">Sie können öffentliche Methoden auf Ihrem Hub-Klasse erstellen und dann diese Methoden durch einen Aufruf über Skripts auf einer Webseite.</span><span class="sxs-lookup"><span data-stu-id="c660d-164">You can create public methods on your hub class and then use those methods by calling them from scripts in a web page.</span></span>

<span data-ttu-id="c660d-165">Im Code Chat Clients rufen die `ChatHub.Send` Methode, um eine neue Nachricht zu senden.</span><span class="sxs-lookup"><span data-stu-id="c660d-165">In the chat code, clients call the `ChatHub.Send` method to send a new message.</span></span> <span data-ttu-id="c660d-166">Der Hub sendet dann die Nachricht an alle Clients durch den Aufruf `Clients.All.broadcastMessage`.</span><span class="sxs-lookup"><span data-stu-id="c660d-166">The hub then sends the message to all clients by calling `Clients.All.broadcastMessage`.</span></span>

<span data-ttu-id="c660d-167">Die `Send` -Methode demonstriert, mehrere Hub-Konzepte:</span><span class="sxs-lookup"><span data-stu-id="c660d-167">The `Send` method demonstrates several hub concepts:</span></span>

* <span data-ttu-id="c660d-168">Deklarieren Sie öffentliche Methoden für einen Hub, damit Clients, die sie aufrufen können.</span><span class="sxs-lookup"><span data-stu-id="c660d-168">Declare public methods on a hub so that clients can call them.</span></span>

* <span data-ttu-id="c660d-169">Verwenden der `Microsoft.AspNet.SignalR.Hub.Clients` dynamische Eigenschaft für die Kommunikation mit allen Clients, die mit diesem Hub verbunden sind.</span><span class="sxs-lookup"><span data-stu-id="c660d-169">Use the `Microsoft.AspNet.SignalR.Hub.Clients` dynamic property to communicate with all clients connected to this hub.</span></span>

* <span data-ttu-id="c660d-170">Aufrufen einer Funktion auf dem Client (z. B. die `broadcastMessage` Funktion) zum Aktualisieren von Clients.</span><span class="sxs-lookup"><span data-stu-id="c660d-170">Call a function on the client (like the `broadcastMessage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample4.cs)]

### <a name="signalr-and-jquery-in-the-indexhtml"></a><span data-ttu-id="c660d-171">SignalR und jQuery in der "Index.HTML"</span><span class="sxs-lookup"><span data-stu-id="c660d-171">SignalR and jQuery in the index.html</span></span>

<span data-ttu-id="c660d-172">Die *"Index.HTML"* Seite im Codebeispiel zeigt, wie die SignalR-jQuery-Bibliothek, die zur Kommunikation mit einer SignalR-Hub verwenden.</span><span class="sxs-lookup"><span data-stu-id="c660d-172">The *index.html* page in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="c660d-173">Der Code führt viele wichtige Aufgaben.</span><span class="sxs-lookup"><span data-stu-id="c660d-173">The code carries out many important tasks.</span></span> <span data-ttu-id="c660d-174">Er deklariert einen Proxy für den Hub zu verweisen, deklariert eine Funktion, dass der Server, das Sie, um die pushübertragung von Inhalten an Clients aufrufen kann, und sie eine Verbindung zum Senden von Nachrichten an den Hub beginnt.</span><span class="sxs-lookup"><span data-stu-id="c660d-174">It declares a proxy to reference the hub, declares a function that the server can call to push content to clients, and it starts a connection to send messages to the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample5.js)]

> [!NOTE]
> <span data-ttu-id="c660d-175">In JavaScript muss der Verweis auf die Server-Klasse und ihre Member CamelCase sein.</span><span class="sxs-lookup"><span data-stu-id="c660d-175">In JavaScript the reference to the server class and its members has to be camelCase.</span></span> <span data-ttu-id="c660d-176">Das Beispiel verweist auf die C# *ChatHub* Klasse in JavaScript als `chatHub`.</span><span class="sxs-lookup"><span data-stu-id="c660d-176">The code sample references the C# *ChatHub* class in JavaScript as `chatHub`.</span></span>

<span data-ttu-id="c660d-177">In diesem Codeblock erstellen Sie eine Callback-Funktion im Skript.</span><span class="sxs-lookup"><span data-stu-id="c660d-177">In this code block, you create a callback function in the script.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample6.html)]

<span data-ttu-id="c660d-178">Die hubklasse auf dem Server ruft diese Funktion, um Inhaltsupdates per Pushvorgang an jeden Client übertragen.</span><span class="sxs-lookup"><span data-stu-id="c660d-178">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="c660d-179">Die beiden Linien, HTML-Codierung der Inhalt vor der Anzeige sind optional, und zeigen eine gute Möglichkeit, Script-Injection verhindern.</span><span class="sxs-lookup"><span data-stu-id="c660d-179">The two lines that HTML-encode the content before displaying it are optional and show a good way to prevent script injection.</span></span>

<span data-ttu-id="c660d-180">Dieser Code öffnet eine Verbindung mit dem Hub.</span><span class="sxs-lookup"><span data-stu-id="c660d-180">This code opens a connection with the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample7.js)]

> [!NOTE]
> <span data-ttu-id="c660d-181">Dieser Ansatz wird sichergestellt, dass der Code eine Verbindung hergestellt wird, bevor der Ereignishandler ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="c660d-181">This approach ensures that the code establishes a connection before the event handler executes.</span></span>

<span data-ttu-id="c660d-182">Der Code wird die Verbindung gestartet und leitet diese dann in eine Funktion zum Behandeln der Click-Ereignis auf der **senden** auf der HTML-Seite.</span><span class="sxs-lookup"><span data-stu-id="c660d-182">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the HTML page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c660d-183">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c660d-183">Additional resources</span></span>

<span data-ttu-id="c660d-184">Weitere Informationen zu SignalR finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="c660d-184">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="c660d-185">SignalR-Projekt</span><span class="sxs-lookup"><span data-stu-id="c660d-185">SignalR Project</span></span>](http://signalr.net)

* [<span data-ttu-id="c660d-186">SignalR Github und Beispiele</span><span class="sxs-lookup"><span data-stu-id="c660d-186">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)

* [<span data-ttu-id="c660d-187">SignalR-Wiki</span><span class="sxs-lookup"><span data-stu-id="c660d-187">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="c660d-188">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="c660d-188">Next steps</span></span>

<span data-ttu-id="c660d-189">In diesem Tutorial Sie:</span><span class="sxs-lookup"><span data-stu-id="c660d-189">In this tutorial you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c660d-190">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="c660d-190">Set up the project</span></span>
> * <span data-ttu-id="c660d-191">Das Beispiel ausgeführt</span><span class="sxs-lookup"><span data-stu-id="c660d-191">Ran the sample</span></span>
> * <span data-ttu-id="c660d-192">Überprüft den code</span><span class="sxs-lookup"><span data-stu-id="c660d-192">Examined the code</span></span>

<span data-ttu-id="c660d-193">Wechseln Sie zum nächsten Artikel erfahren, wie SignalR und MVC 5 verwendet.</span><span class="sxs-lookup"><span data-stu-id="c660d-193">Advance to the next article to learn how to use SignalR and MVC 5.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="c660d-194">SignalR 2 und MVC 5</span><span class="sxs-lookup"><span data-stu-id="c660d-194">SignalR 2 and MVC 5</span></span>](tutorial-getting-started-with-signalr-and-mvc.md)