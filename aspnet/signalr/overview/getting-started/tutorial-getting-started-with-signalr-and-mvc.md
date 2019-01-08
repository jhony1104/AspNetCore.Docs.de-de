---
uid: signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
title: 'Tutorial: Echtzeit-Chat mit SignalR 2 und MVC 5 | Microsoft-Dokumentation'
author: pfletcher
description: Dieses Tutorial veranschaulicht, wie ASP.NET SignalR 2 zu verwenden, um eine chatanwendung mit Echtzeitfunktionalität erstellen. Eine MVC 5-Anwendung hinzugefügt SignalR.
ms.author: riande
ms.date: 01/02/2019
ms.assetid: 80bfe5fb-bdfc-41fe-ac43-2132e5d69fac
msc.legacyurl: /signalr/overview/getting-started/tutorial-getting-started-with-signalr-and-mvc
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: eb4b7e1403f4070d65702b756bf98c5294c7fb17
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098603"
---
# <a name="tutorial-real-time-chat-with-signalr-2-and-mvc-5"></a><span data-ttu-id="4edef-104">Tutorial: Echtzeit-Chat mit SignalR 2 und MVC 5</span><span class="sxs-lookup"><span data-stu-id="4edef-104">Tutorial: Real-time chat with SignalR 2 and MVC 5</span></span>

<span data-ttu-id="4edef-105">Dieses Tutorial veranschaulicht, wie ASP.NET SignalR 2 zu verwenden, um eine chatanwendung mit Echtzeitfunktionalität erstellen.</span><span class="sxs-lookup"><span data-stu-id="4edef-105">This tutorial shows how to use ASP.NET SignalR 2 to create a real-time chat application.</span></span> <span data-ttu-id="4edef-106">Sie SignalR zu einer MVC 5-Anwendung hinzufügen und erstellen eine Chat-Ansicht zum Senden und Nachrichten anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4edef-106">You add SignalR to an MVC 5 application and create a chat view to send and display messages.</span></span>

<span data-ttu-id="4edef-107">In diesem Tutorial haben Sie:</span><span class="sxs-lookup"><span data-stu-id="4edef-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4edef-108">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="4edef-108">Set up the project</span></span>
> * <span data-ttu-id="4edef-109">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="4edef-109">Run the sample</span></span>
> * <span data-ttu-id="4edef-110">Überprüfen Sie den code</span><span class="sxs-lookup"><span data-stu-id="4edef-110">Examine the code</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a><span data-ttu-id="4edef-111">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="4edef-111">Prerequisites</span></span>

* <span data-ttu-id="4edef-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) mit der **ASP.NET und Webentwicklung** arbeitsauslastung.</span><span class="sxs-lookup"><span data-stu-id="4edef-112">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="4edef-113">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="4edef-113">Set up the Project</span></span>

<span data-ttu-id="4edef-114">In diesem Abschnitt wird gezeigt, wie Sie mit Visual Studio 2017 und SignalR 2 eine leere ASP.NET MVC 5-Anwendung erstellen, fügen Sie die SignalR-Bibliothek und die chatanwendung erstellen.</span><span class="sxs-lookup"><span data-stu-id="4edef-114">This section shows how to use Visual Studio 2017 and SignalR 2 to create an empty ASP.NET MVC 5 application, add the SignalR library, and create the chat application.</span></span>

1. <span data-ttu-id="4edef-115">In Visual Studio Erstellen einer c# ASP.NET-Anwendung, die auf .NET Framework 4.5 abzielt, nennen Sie sie SignalRChat, und klicken Sie auf OK.</span><span class="sxs-lookup"><span data-stu-id="4edef-115">In Visual Studio, create a C# ASP.NET application that targets .NET Framework 4.5, name it SignalRChat, and click OK.</span></span>

    ![Erstellen Sie web](tutorial-getting-started-with-signalr-and-mvc/_static/image1.png)

1. <span data-ttu-id="4edef-117">In **neue ASP.NET-Webanwendung – SignalRMvcChat**Option **MVC** und wählen Sie dann **Authentifizierung ändern**.</span><span class="sxs-lookup"><span data-stu-id="4edef-117">In **New ASP.NET Web Application - SignalRMvcChat**, select **MVC** and then select **Change Authentication**.</span></span>

1. <span data-ttu-id="4edef-118">In **Authentifizierung ändern**Option **keine Authentifizierung** , und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="4edef-118">In **Change Authentication**, select **No Authentication** and click **OK**.</span></span>

    ![Wählen Sie keine Authentifizierung](tutorial-getting-started-with-signalr-and-mvc/_static/image2.png)

1. <span data-ttu-id="4edef-120">In **neue ASP.NET-Webanwendung – SignalRMvcChat**Option **OK**.</span><span class="sxs-lookup"><span data-stu-id="4edef-120">In **New ASP.NET Web Application - SignalRMvcChat**, select **OK**.</span></span>

1. <span data-ttu-id="4edef-121">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.</span><span class="sxs-lookup"><span data-stu-id="4edef-121">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="4edef-122">In **neues Element hinzufügen - SignalRChat**Option **installiert** > **Visual C#**   >  **Web**  >  **SignalR** und wählen Sie dann **SignalR-Hubklasse (v2)**.</span><span class="sxs-lookup"><span data-stu-id="4edef-122">In **Add New Item - SignalRChat**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="4edef-123">Nennen Sie die Klasse *ChatHub* und fügen sie dem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="4edef-123">Name the class *ChatHub* and add it to the project.</span></span>

    <span data-ttu-id="4edef-124">Dieser Schritt erstellt der *ChatHub.cs* Klassendatei, und fügt einen Satz von Skriptdateien und Assemblyverweise, die dem Projekt SignalR unterstützen.</span><span class="sxs-lookup"><span data-stu-id="4edef-124">This step creates the *ChatHub.cs* class file and adds a set of script files and assembly references that support SignalR to the project.</span></span>

1. <span data-ttu-id="4edef-125">Ersetzen Sie den Code in der neuen *ChatHub.cs* Klassendatei mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4edef-125">Replace the code in the new *ChatHub.cs* class file with this code:</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample1.cs)]

1. <span data-ttu-id="4edef-126">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="4edef-126">In **Solution Explorer**, right-click the project and select **Add** > **Class**.</span></span>

1. <span data-ttu-id="4edef-127">Nennen Sie die neue Klasse *Start* und fügen sie dem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="4edef-127">Name the new class *Startup* and add it to the project.</span></span>

1. <span data-ttu-id="4edef-128">Ersetzen Sie den Code in die *"Startup.cs"* Klassendatei mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4edef-128">Replace the code in the *Startup.cs* class file with this code:</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample2.cs)]

1. <span data-ttu-id="4edef-129">In **Projektmappen-Explorer**Option **Controller** > **"HomeController.cs"**.</span><span class="sxs-lookup"><span data-stu-id="4edef-129">In **Solution Explorer**, select **Controllers** > **HomeController.cs**.</span></span>

1. <span data-ttu-id="4edef-130">Fügen Sie diese Methode, um die *"HomeController.cs"*.</span><span class="sxs-lookup"><span data-stu-id="4edef-130">Add this method to the *HomeController.cs*.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample3.cs)]

    <span data-ttu-id="4edef-131">Diese Methode gibt die **Chat** anzeigen, die Sie in einem späteren Schritt erstellen.</span><span class="sxs-lookup"><span data-stu-id="4edef-131">This method returns the **Chat** view that you create in a later step.</span></span>

1. <span data-ttu-id="4edef-132">In **Projektmappen-Explorer**, mit der rechten Maustaste **Ansichten** > **Startseite**, und wählen Sie **hinzufügen**  >    **Ansicht**.</span><span class="sxs-lookup"><span data-stu-id="4edef-132">In **Solution Explorer**, right-click **Views** > **Home**, and select **Add** >  **View**.</span></span>

1. <span data-ttu-id="4edef-133">In **Ansicht hinzufügen**, benennen Sie die neue Ansicht **Chat** , und wählen Sie **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="4edef-133">In **Add View**, name the new view **Chat** and select **Add**.</span></span>

1. <span data-ttu-id="4edef-134">Ersetzen Sie den Inhalt der **Chat.cshtml** durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="4edef-134">Replace the contents of **Chat.cshtml** with this code:</span></span>

    [!code-cshtml[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample4.cshtml)]

1. <span data-ttu-id="4edef-135">In **Projektmappen-Explorer**, erweitern Sie **Skripts**.</span><span class="sxs-lookup"><span data-stu-id="4edef-135">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="4edef-136">Skriptbibliotheken für jQuery und SignalR werden in das Projekt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="4edef-136">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="4edef-137">Der Paket-Manager möglicherweise eine höhere Version von SignalR-Skripts installiert.</span><span class="sxs-lookup"><span data-stu-id="4edef-137">The package manager may have installed a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="4edef-138">Überprüfen Sie, dass die Skriptverweise im Codeblock auf die Versionen der Skriptdateien im Projekt entsprechen.</span><span class="sxs-lookup"><span data-stu-id="4edef-138">Check that the script references in the code block correspond to the versions of the script files in the project.</span></span>

    <span data-ttu-id="4edef-139">Die Skriptverweise aus der ursprünglichen Codeblock:</span><span class="sxs-lookup"><span data-stu-id="4edef-139">Script references from the original code block:</span></span>

    ```cshtml
    <!--Script references. -->
    <!--The jQuery library is required and is referenced by default in _Layout.cshtml. -->
    <!--Reference the SignalR library. -->
    <script src="~/Scripts/jquery.signalR-2.1.0.min.js"></script>
    ```

1. <span data-ttu-id="4edef-140">Wenn sie nicht übereinstimmen, aktualisieren Sie die *.cshtml* Datei.</span><span class="sxs-lookup"><span data-stu-id="4edef-140">If they don't match, update the *.cshtml* file.</span></span>

1. <span data-ttu-id="4edef-141">Wählen Sie in der Menüleiste **Datei** > **Alles speichern**.</span><span class="sxs-lookup"><span data-stu-id="4edef-141">From the menu bar, select **File** > **Save All**.</span></span>

## <a name="run-the-sample"></a><span data-ttu-id="4edef-142">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="4edef-142">Run the Sample</span></span>

1. <span data-ttu-id="4edef-143">In der Symbolleiste aktivieren **Skriptdebugging** und wählen Sie dann auf die entsprechende Schaltfläche, um das Beispiel im Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4edef-143">In the toolbar, turn on **Script Debugging** and then select the play button to run the sample in Debug mode.</span></span>

    ![Benutzername eingeben](tutorial-getting-started-with-signalr-and-mvc/_static/image3.png)

1. <span data-ttu-id="4edef-145">Wenn der Browser geöffnet wird, geben Sie einen Namen für Ihre Chat-Identität aus.</span><span class="sxs-lookup"><span data-stu-id="4edef-145">When the browser opens, enter a name for your chat identity.</span></span>

1. <span data-ttu-id="4edef-146">Kopieren Sie die URL aus dem Browser, öffnen Sie zwei andere Browser, und fügen Sie die URLs in die Adresse leisten.</span><span class="sxs-lookup"><span data-stu-id="4edef-146">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

1. <span data-ttu-id="4edef-147">Geben Sie in jedem Browser einen eindeutigen Namen ein.</span><span class="sxs-lookup"><span data-stu-id="4edef-147">In each browser, enter a unique name.</span></span>

1. <span data-ttu-id="4edef-148">Fügen Sie nun einen Kommentar, und wählen **senden**.</span><span class="sxs-lookup"><span data-stu-id="4edef-148">Now, add a comment and select **Send**.</span></span> <span data-ttu-id="4edef-149">Wiederholen Sie, die in den anderen Browsern.</span><span class="sxs-lookup"><span data-stu-id="4edef-149">Repeat that in the other browsers.</span></span> <span data-ttu-id="4edef-150">Die Kommentare werden in Echtzeit angezeigt.</span><span class="sxs-lookup"><span data-stu-id="4edef-150">The comments appear in real time.</span></span>

    > [!NOTE]
    > <span data-ttu-id="4edef-151">Diese einfache chatanwendung werden den Kontext der Diskussion auf dem Server nicht verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="4edef-151">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="4edef-152">Der Hub sendet, Kommentare, um alle aktuellen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="4edef-152">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="4edef-153">Benutzer, die im Chat später zusammenfügen sehen, dass Nachrichten ab dem Zeitpunkt hinzugefügt werden, dass sie aufgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="4edef-153">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="4edef-154">Sehen Sie, wie der Chat-Anwendung in drei verschiedenen Browsern ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4edef-154">See how the chat application runs in three different browsers.</span></span> <span data-ttu-id="4edef-155">Beim Tom, Anand und Susan Senden von Nachrichten von allen Browsern, die in Echtzeit aktualisiert werden:</span><span class="sxs-lookup"><span data-stu-id="4edef-155">When Tom, Anand, and Susan send messages, all browsers update in real time:</span></span>

    ![Alle drei in Browsern angezeigt, den gleichen Chatverlauf](tutorial-getting-started-with-signalr-and-mvc/_static/image4.png)

1. <span data-ttu-id="4edef-157">In **Projektmappen-Explorer**, überprüfen Sie die **Skriptdokumente** Knoten für die ausgeführte Anwendung.</span><span class="sxs-lookup"><span data-stu-id="4edef-157">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="4edef-158">Es gibt eine Skriptdatei namens *Hubs* , die den SignalR-Bibliothek zur Laufzeit generiert.</span><span class="sxs-lookup"><span data-stu-id="4edef-158">There's a script file named *hubs* that the SignalR library generates at runtime.</span></span> <span data-ttu-id="4edef-159">Diese Datei, verwaltet die Kommunikation zwischen der jQuery-Skripts und serverseitigen Code.</span><span class="sxs-lookup"><span data-stu-id="4edef-159">This file manages the communication between jQuery script and server-side code.</span></span>

    ![automatisch generierte-Hubs-Skript im Knoten "Skriptdokumente"](tutorial-getting-started-with-signalr-and-mvc/_static/image5.png)

## <a name="examine-the-code"></a><span data-ttu-id="4edef-161">Überprüfen Sie den Code</span><span class="sxs-lookup"><span data-stu-id="4edef-161">Examine the Code</span></span>

<span data-ttu-id="4edef-162">Der SignalR Chat-Anwendung veranschaulicht zwei grundlegende Aufgaben für die SignalR-Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="4edef-162">The SignalR chat application demonstrates two basic SignalR development tasks.</span></span> <span data-ttu-id="4edef-163">Es veranschaulicht einen Hub erstellen.</span><span class="sxs-lookup"><span data-stu-id="4edef-163">It shows you how to create a hub.</span></span> <span data-ttu-id="4edef-164">Der Server verwendet den Hub als Haupt-Coordination-Objekt.</span><span class="sxs-lookup"><span data-stu-id="4edef-164">The server uses that hub as the main coordination object.</span></span> <span data-ttu-id="4edef-165">Der Hub verwendet die SignalR-jQuery-Bibliothek zum Senden und Empfangen von Nachrichten an.</span><span class="sxs-lookup"><span data-stu-id="4edef-165">The hub uses the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs-in-the-chathubcs"></a><span data-ttu-id="4edef-166">SignalR-Hubs in der ChatHub.cs</span><span class="sxs-lookup"><span data-stu-id="4edef-166">SignalR Hubs in the ChatHub.cs</span></span>

<span data-ttu-id="4edef-167">Im Codebeispiel das `ChatHub` Klasse leitet sich von der `Microsoft.AspNet.SignalR.Hub` Klasse.</span><span class="sxs-lookup"><span data-stu-id="4edef-167">In the code sample, the `ChatHub` class derives from the `Microsoft.AspNet.SignalR.Hub` class.</span></span> <span data-ttu-id="4edef-168">Ableiten von der `Hub` Klasse ist eine gute Möglichkeit, eine SignalR-Anwendung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4edef-168">Deriving from the `Hub` class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="4edef-169">Sie können öffentliche Methoden auf Ihrem Hub-Klasse erstellen und dann Zugriff auf diese Methoden durch einen Aufruf über Skripts auf einer Webseite.</span><span class="sxs-lookup"><span data-stu-id="4edef-169">You can create public methods on your hub class and then access those methods by calling them from scripts in a web page.</span></span>

<span data-ttu-id="4edef-170">Im Code Chat Clients rufen die `ChatHub.Send` Methode, um eine neue Nachricht zu senden.</span><span class="sxs-lookup"><span data-stu-id="4edef-170">In the chat code, clients call the `ChatHub.Send` method to send a new message.</span></span> <span data-ttu-id="4edef-171">Der Hub wiederum sendet die Nachricht an alle Clients durch den Aufruf `Clients.All.addNewMessageToPage`.</span><span class="sxs-lookup"><span data-stu-id="4edef-171">The hub in turn sends the message to all clients by calling `Clients.All.addNewMessageToPage`.</span></span>

<span data-ttu-id="4edef-172">Die `Send` -Methode demonstriert, mehrere Hub-Konzepte:</span><span class="sxs-lookup"><span data-stu-id="4edef-172">The `Send` method demonstrates several hub concepts:</span></span>

* <span data-ttu-id="4edef-173">Deklarieren Sie öffentliche Methoden für einen Hub, damit Clients, die sie aufrufen können.</span><span class="sxs-lookup"><span data-stu-id="4edef-173">Declare public methods on a hub so that clients can call them.</span></span>

* <span data-ttu-id="4edef-174">Verwenden der `Microsoft.AspNet.SignalR.Hub.Clients` dynamische Eigenschaft für die Kommunikation mit allen Clients, die mit diesem Hub verbunden sind.</span><span class="sxs-lookup"><span data-stu-id="4edef-174">Use the `Microsoft.AspNet.SignalR.Hub.Clients` dynamic property to communicate with all clients connected to this hub.</span></span>

* <span data-ttu-id="4edef-175">Aufrufen einer Funktion auf dem Client (z. B. die `addNewMessageToPage` Funktion) zum Aktualisieren von Clients.</span><span class="sxs-lookup"><span data-stu-id="4edef-175">Call a function on the client (like the `addNewMessageToPage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample5.cs)]

### <a name="signalr-and-jquery-chatcshtml"></a><span data-ttu-id="4edef-176">SignalR und jQuery Chat.cshtml</span><span class="sxs-lookup"><span data-stu-id="4edef-176">SignalR and jQuery Chat.cshtml</span></span>

<span data-ttu-id="4edef-177">Die *Chat.cshtml* Ansicht im Codebeispiel zeigt, wie die SignalR-jQuery-Bibliothek, die zur Kommunikation mit einer SignalR-Hub verwenden.</span><span class="sxs-lookup"><span data-stu-id="4edef-177">The *Chat.cshtml* view file in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span>  <span data-ttu-id="4edef-178">Der Code führt viele wichtige Aufgaben.</span><span class="sxs-lookup"><span data-stu-id="4edef-178">The code carries out many important tasks.</span></span> <span data-ttu-id="4edef-179">Es erstellt einen Verweis auf den automatisch generierten Proxy für den Hub, deklariert eine Funktion, dass der Server, das Sie aufrufen kann, um Inhalt an Clients zu verteilen, und sie eine Verbindung zum Senden von Nachrichten an den Hub beginnt.</span><span class="sxs-lookup"><span data-stu-id="4edef-179">It creates a reference to the autogenerated proxy for the hub, declares a function that the server can call to push content to clients, and it starts a connection to send messages to the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample6.js)]

> [!NOTE]
> <span data-ttu-id="4edef-180">In JavaScript wird der Verweis auf die Server-Klasse und deren Member camelCase.</span><span class="sxs-lookup"><span data-stu-id="4edef-180">In JavaScript, the reference to the server class and its members is in camelCase.</span></span> <span data-ttu-id="4edef-181">Das Beispiel verweist auf die C# `ChatHub` Klasse in JavaScript als `chatHub`.</span><span class="sxs-lookup"><span data-stu-id="4edef-181">The code sample references the C# `ChatHub` class in JavaScript as `chatHub`.</span></span>

<span data-ttu-id="4edef-182">In diesem Codeblock erstellen Sie eine Callback-Funktion im Skript.</span><span class="sxs-lookup"><span data-stu-id="4edef-182">In this code block, you create a callback function in the script.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample7.html)]

<span data-ttu-id="4edef-183">Die hubklasse auf dem Server ruft diese Funktion, um Inhaltsupdates per Pushvorgang an jeden Client übertragen.</span><span class="sxs-lookup"><span data-stu-id="4edef-183">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="4edef-184">Der Aufruf optional die `htmlEncode` Funktion zeigt die Möglichkeit, HTML codieren von Inhalt der Nachricht vor der Anzeige auf der Seite.</span><span class="sxs-lookup"><span data-stu-id="4edef-184">The optional call to the `htmlEncode` function shows a way to HTML encode the message content before displaying it in the page.</span></span> <span data-ttu-id="4edef-185">Es ist eine Möglichkeit zum Skript-Injection verhindern.</span><span class="sxs-lookup"><span data-stu-id="4edef-185">It's a way to prevent script injection.</span></span>

<span data-ttu-id="4edef-186">Dieser Code öffnet eine Verbindung mit dem Hub.</span><span class="sxs-lookup"><span data-stu-id="4edef-186">This code opens a connection with the hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr-and-mvc/samples/sample8.js)]

> [!NOTE]
> <span data-ttu-id="4edef-187">Dieser Ansatz wird sichergestellt, dass Sie eine Verbindung herstellen, bevor der Ereignishandler ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="4edef-187">This approach ensures that you establish a connection before the event handler executes.</span></span>

<span data-ttu-id="4edef-188">Der Code wird die Verbindung gestartet und leitet diese dann in eine Funktion zum Behandeln der Click-Ereignis auf der **senden** auf der Seite Chat.</span><span class="sxs-lookup"><span data-stu-id="4edef-188">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the Chat page.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4edef-189">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4edef-189">Additional resources</span></span>

<span data-ttu-id="4edef-190">Weitere Informationen zu SignalR finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="4edef-190">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="4edef-191">SignalR-Projekt</span><span class="sxs-lookup"><span data-stu-id="4edef-191">SignalR Project</span></span>](http://signalr.net)

* [<span data-ttu-id="4edef-192">SignalR GitHub und Beispiele</span><span class="sxs-lookup"><span data-stu-id="4edef-192">SignalR GitHub and Samples</span></span>](https://github.com/SignalR/SignalR)

* [<span data-ttu-id="4edef-193">SignalR-Wiki</span><span class="sxs-lookup"><span data-stu-id="4edef-193">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="4edef-194">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="4edef-194">Next steps</span></span>

<span data-ttu-id="4edef-195">In diesem Tutorial haben Sie:</span><span class="sxs-lookup"><span data-stu-id="4edef-195">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4edef-196">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="4edef-196">Set up the project</span></span>
> * <span data-ttu-id="4edef-197">Das Beispiel ausgeführt</span><span class="sxs-lookup"><span data-stu-id="4edef-197">Ran the sample</span></span>
> * <span data-ttu-id="4edef-198">Überprüft den code</span><span class="sxs-lookup"><span data-stu-id="4edef-198">Examined the code</span></span>

<span data-ttu-id="4edef-199">Wechseln Sie zum nächsten Artikel erfahren, wie eine Webanwendung erstellen, die ASP.NET SignalR 2 verwendet wird, um häufige-messaging-Funktionalität bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="4edef-199">Advance to the next article to learn how to create a web application that uses ASP.NET SignalR 2 to provide high-frequency messaging functionality.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="4edef-200">Web-app mit hoher Frequenz messaging</span><span class="sxs-lookup"><span data-stu-id="4edef-200">Web app with high-frequency messaging</span></span>](tutorial-high-frequency-realtime-with-signalr.md)