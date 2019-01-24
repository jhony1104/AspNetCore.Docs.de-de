---
uid: signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
title: 'Tutorial: Erstellen Sie in Echtzeit hoher Frequenz-app mit SignalR 2 | Microsoft-Dokumentation'
author: bradygaster
description: Dieses Tutorial veranschaulicht, wie eine Webanwendung erstellen, die ASP.NET SignalR verwendet wird, um häufige-messaging-Funktionalität bereitzustellen.
ms.author: bradyg
ms.date: 01/22/2019
ms.assetid: 9f969dda-78ea-4329-b1e3-e51c02210a2b
msc.legacyurl: /signalr/overview/getting-started/tutorial-high-frequency-realtime-with-signalr
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 44aaa2b0c059de310e963f642fa56c2f00a7e443
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836726"
---
# <a name="tutorial-create-high-frequency-real-time-app-with-signalr-2"></a><span data-ttu-id="792b4-103">Tutorial: Erstellen Sie in Echtzeit hoher Frequenz-app mit SignalR 2</span><span class="sxs-lookup"><span data-stu-id="792b4-103">Tutorial: Create high-frequency real-time app with SignalR 2</span></span>

<span data-ttu-id="792b4-104">Dieses Tutorial veranschaulicht, wie eine Webanwendung erstellen, die ASP.NET SignalR 2 verwendet wird, um häufige-messaging-Funktionalität bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="792b4-104">This tutorial shows how to create a web application that uses ASP.NET SignalR 2 to provide high-frequency messaging functionality.</span></span> <span data-ttu-id="792b4-105">In diesem Fall also "hochfrequentes messaging" sendet der Server die Updates mit einer festen Rate.</span><span class="sxs-lookup"><span data-stu-id="792b4-105">In this case, "high-frequency messaging" means the server sends updates at a fixed rate.</span></span> <span data-ttu-id="792b4-106">Sie senden bis zu 10 Nachrichten pro Sekunde.</span><span class="sxs-lookup"><span data-stu-id="792b4-106">You send up to 10 messages a second.</span></span>

<span data-ttu-id="792b4-107">Die Anwendung, die Sie erstellen, zeigt eine Form, die Benutzer ziehen können.</span><span class="sxs-lookup"><span data-stu-id="792b4-107">The application you create displays a shape that users can drag.</span></span> <span data-ttu-id="792b4-108">Der Server wird die Position der Form in allen verbundenen Browsern entsprechend die Position der gezogene Form mithilfe von zeitlich festgelegte Updates aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="792b4-108">The server updates the position of the shape in all connected browsers to match the position of the dragged shape using timed updates.</span></span>

<span data-ttu-id="792b4-109">In diesem Tutorial eingeführte Konzepte müssen Anwendungen in Echtzeit Spiele und andere Anwendungen für die Simulation.</span><span class="sxs-lookup"><span data-stu-id="792b4-109">Concepts introduced in this tutorial have applications in real-time gaming and other simulation applications.</span></span>

<span data-ttu-id="792b4-110">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="792b4-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="792b4-111">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="792b4-111">Set up the project</span></span>
> * <span data-ttu-id="792b4-112">Erstellen Sie die grundlegende Anwendung</span><span class="sxs-lookup"><span data-stu-id="792b4-112">Create the base application</span></span>
> * <span data-ttu-id="792b4-113">Mit dem Hub zugeordnet ist, beim Starten der app</span><span class="sxs-lookup"><span data-stu-id="792b4-113">Map to the hub when app starts</span></span>
> * <span data-ttu-id="792b4-114">Fügen Sie dem Client hinzu.</span><span class="sxs-lookup"><span data-stu-id="792b4-114">Add the client</span></span>
> * <span data-ttu-id="792b4-115">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="792b4-115">Run the app</span></span>
> * <span data-ttu-id="792b4-116">Die Client-Schleife hinzufügen</span><span class="sxs-lookup"><span data-stu-id="792b4-116">Add the client loop</span></span>
> * <span data-ttu-id="792b4-117">Der Server-Schleife hinzufügen</span><span class="sxs-lookup"><span data-stu-id="792b4-117">Add the server loop</span></span>
> * <span data-ttu-id="792b4-118">Fügen Sie flüssige Animation hinzu.</span><span class="sxs-lookup"><span data-stu-id="792b4-118">Add smooth animation</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

## <a name="prerequisites"></a><span data-ttu-id="792b4-119">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="792b4-119">Prerequisites</span></span>

* <span data-ttu-id="792b4-120">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) mit der **ASP.NET und Webentwicklung** arbeitsauslastung.</span><span class="sxs-lookup"><span data-stu-id="792b4-120">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="set-up-the-project"></a><span data-ttu-id="792b4-121">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="792b4-121">Set up the project</span></span>

<span data-ttu-id="792b4-122">In diesem Abschnitt erstellen Sie das Projekt in Visual Studio 2017.</span><span class="sxs-lookup"><span data-stu-id="792b4-122">In this section, you create the project in Visual Studio 2017.</span></span>

<span data-ttu-id="792b4-123">In diesem Abschnitt veranschaulicht, wie Visual Studio 2017 verwenden, um eine leere ASP.NET Web-Anwendung erstellen, und fügen Sie die SignalR und jQuery.UI-Bibliotheken hinzu.</span><span class="sxs-lookup"><span data-stu-id="792b4-123">This section shows how to use Visual Studio 2017 to create an empty ASP.NET Web Application and add the SignalR and jQuery.UI libraries.</span></span>

1. <span data-ttu-id="792b4-124">Erstellen Sie eine ASP.NET-Webanwendung in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="792b4-124">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![Erstellen Sie web](tutorial-high-frequency-realtime-with-signalr/_static/image1.png)

1. <span data-ttu-id="792b4-126">In der **neue ASP.NET-Webanwendung – MoveShapeDemo** Fenster verlassen **leere** ausgewählt, und wählen Sie **OK**.</span><span class="sxs-lookup"><span data-stu-id="792b4-126">In the **New ASP.NET Web Application - MoveShapeDemo** window, leave **Empty** selected and select **OK**.</span></span>

1. <span data-ttu-id="792b4-127">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.</span><span class="sxs-lookup"><span data-stu-id="792b4-127">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="792b4-128">In **neues Element hinzufügen - MoveShapeDemo**Option **installiert** > **Visual C#**   >  **Web**  >  **SignalR** und wählen Sie dann **SignalR-Hubklasse (v2)**.</span><span class="sxs-lookup"><span data-stu-id="792b4-128">In **Add New Item - MoveShapeDemo**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="792b4-129">Nennen Sie die Klasse *MoveShapeHub* und fügen sie dem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="792b4-129">Name the class *MoveShapeHub* and add it to the project.</span></span>

    <span data-ttu-id="792b4-130">Dieser Schritt erstellt der *MoveShapeHub.cs* Klassendatei.</span><span class="sxs-lookup"><span data-stu-id="792b4-130">This step creates the *MoveShapeHub.cs* class file.</span></span> <span data-ttu-id="792b4-131">Gleichzeitig wird eine Reihe von Skriptdateien und Assemblyverweise, die dem Projekt SignalR unterstützen.</span><span class="sxs-lookup"><span data-stu-id="792b4-131">Simultaneously, it adds  a set of script files and assembly references that support SignalR to the project.</span></span>

1. <span data-ttu-id="792b4-132">Wählen Sie **Tools** > **NuGet Paket-Manager** > **Paket-Manager Konsole**.</span><span class="sxs-lookup"><span data-stu-id="792b4-132">Select **Tools** > **NuGet Package Manager** > **Package Manager Console**.</span></span>

1. <span data-ttu-id="792b4-133">In **-Paket-Manager-Konsole**, führen Sie diesen Befehl:</span><span class="sxs-lookup"><span data-stu-id="792b4-133">In **Package Manager Console**, run this command:</span></span>

    ```console
    Install-Package jQuery.UI.Combined
    ```

    <span data-ttu-id="792b4-134">Der Befehl installiert die jQuery-UI-Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="792b4-134">The command installs the jQuery UI library.</span></span> <span data-ttu-id="792b4-135">Damit können Sie um die Form zu animieren.</span><span class="sxs-lookup"><span data-stu-id="792b4-135">You use it to animate the shape.</span></span>

1. <span data-ttu-id="792b4-136">In **Projektmappen-Explorer**, erweitern Sie den Knoten des Skripts.</span><span class="sxs-lookup"><span data-stu-id="792b4-136">In **Solution Explorer**, expand the Scripts node.</span></span>

    ![Verweisen auf](tutorial-high-frequency-realtime-with-signalr/_static/image2.png)

    <span data-ttu-id="792b4-138">Skriptbibliotheken für jQuery, jQueryUI und SignalR werden in das Projekt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="792b4-138">Script libraries for jQuery, jQueryUI, and SignalR are visible in the project.</span></span>

## <a name="create-the-base-application"></a><span data-ttu-id="792b4-139">Erstellen Sie die grundlegende Anwendung</span><span class="sxs-lookup"><span data-stu-id="792b4-139">Create the base application</span></span>

<span data-ttu-id="792b4-140">In diesem Abschnitt erstellen Sie eine Browser-Anwendung.</span><span class="sxs-lookup"><span data-stu-id="792b4-140">In this section, you create a browser application.</span></span> <span data-ttu-id="792b4-141">Die app sendet die Position der Form an den Server während der einzelnen MouseMove-Ereignis.</span><span class="sxs-lookup"><span data-stu-id="792b4-141">The app sends the location of the shape to the server during each mouse move event.</span></span> <span data-ttu-id="792b4-142">Der Server sendet diese Informationen an alle anderen verbundenen Clients in Echtzeit.</span><span class="sxs-lookup"><span data-stu-id="792b4-142">The server broadcasts this information to all other connected clients in real time.</span></span> <span data-ttu-id="792b4-143">Erfahren Sie mehr über diese Anwendung in späteren Abschnitten.</span><span class="sxs-lookup"><span data-stu-id="792b4-143">You learn more about this application in later sections.</span></span>

1. <span data-ttu-id="792b4-144">Öffnen der *MoveShapeHub.cs* Datei.</span><span class="sxs-lookup"><span data-stu-id="792b4-144">Open the *MoveShapeHub.cs* file.</span></span>

1. <span data-ttu-id="792b4-145">Ersetzen Sie den Code in die *MoveShapeHub.cs* Datei durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="792b4-145">Replace the code in the *MoveShapeHub.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample1.cs)]

1. <span data-ttu-id="792b4-146">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="792b4-146">Save the file.</span></span>

<span data-ttu-id="792b4-147">Die `MoveShapeHub` -Klasse ist eine Implementierung eines SignalR-Hubs.</span><span class="sxs-lookup"><span data-stu-id="792b4-147">The `MoveShapeHub` class is an implementation of a SignalR hub.</span></span> <span data-ttu-id="792b4-148">Wie in der [erste Schritte mit SignalR](tutorial-getting-started-with-signalr.md) Tutorial der Hub verfügt über eine Methode, die den Clients direkt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="792b4-148">As in the [Getting Started with SignalR](tutorial-getting-started-with-signalr.md) tutorial, the hub has a method that the clients call directly.</span></span> <span data-ttu-id="792b4-149">In diesem Fall ist der Client sendet ein Objekt mit der neuen X- und Y-Koordinaten der Form auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="792b4-149">In this case, the client sends an object with the new X and Y coordinates of the shape to the server.</span></span> <span data-ttu-id="792b4-150">Diese Koordinaten erhalten haben, alle anderen verbundenen Clients übertragen.</span><span class="sxs-lookup"><span data-stu-id="792b4-150">Those coordinates get broadcasted to all other connected clients.</span></span> <span data-ttu-id="792b4-151">SignalR wird automatisch dieses Objekts unter Verwendung von JSON serialisiert.</span><span class="sxs-lookup"><span data-stu-id="792b4-151">SignalR automatically serializes this object using JSON.</span></span>

<span data-ttu-id="792b4-152">Die app sendet die `ShapeModel` Objekt an den Client.</span><span class="sxs-lookup"><span data-stu-id="792b4-152">The app sends the `ShapeModel` object to the client.</span></span> <span data-ttu-id="792b4-153">Verfügt er über Member, um die Position der Form zu speichern.</span><span class="sxs-lookup"><span data-stu-id="792b4-153">It has members to store the position of the shape.</span></span> <span data-ttu-id="792b4-154">Die Version des Objekts auf dem Server hat auch ein Element zum Nachverfolgen des Clients die Daten gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="792b4-154">The version of the object on the server also has a member to track which client's data is being stored.</span></span> <span data-ttu-id="792b4-155">Dieses Objekt wird verhindert, dass der Server sendet die Daten eines Clients an sich selbst.</span><span class="sxs-lookup"><span data-stu-id="792b4-155">This object prevents the server from sending a client's data back to itself.</span></span> <span data-ttu-id="792b4-156">Dieses Element verwendet die `JsonIgnore` Attribut, um die Anwendung verhindern, dass die Daten serialisieren und das Senden an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="792b4-156">This member uses the `JsonIgnore` attribute to keep the application from serializing the data and sending it back to the client.</span></span>

## <a name="map-to-the-hub-when-app-starts"></a><span data-ttu-id="792b4-157">Mit dem Hub zugeordnet ist, beim Starten der app</span><span class="sxs-lookup"><span data-stu-id="792b4-157">Map to the hub when app starts</span></span>

<span data-ttu-id="792b4-158">Als Nächstes richten Sie die Zuordnung mit dem Hub beim Starten der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="792b4-158">Next, you set up mapping to the hub when the application starts.</span></span> <span data-ttu-id="792b4-159">In SignalR 2 wird Hinzufügen einer OWIN-Startklasse die Zuordnung aus.</span><span class="sxs-lookup"><span data-stu-id="792b4-159">In SignalR 2, adding an OWIN startup class creates the mapping.</span></span>

1. <span data-ttu-id="792b4-160">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.</span><span class="sxs-lookup"><span data-stu-id="792b4-160">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="792b4-161">In **neues Element hinzufügen - MoveShapeDemo** wählen **installiert** > **Visual C#**   >  **Web** und klicken Sie dann Wählen Sie **OWIN-Startklasse**.</span><span class="sxs-lookup"><span data-stu-id="792b4-161">In **Add New Item - MoveShapeDemo** select **Installed** > **Visual C#** > **Web** and then select **OWIN Startup Class**.</span></span>

1. <span data-ttu-id="792b4-162">Nennen Sie die Klasse *Start* , und wählen Sie **OK**.</span><span class="sxs-lookup"><span data-stu-id="792b4-162">Name the class *Startup* and select **OK**.</span></span>

1. <span data-ttu-id="792b4-163">Ersetzen Sie den Standardcode in der *"Startup.cs"* Datei durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="792b4-163">Replace the default code in the *Startup.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample2.cs)]

<span data-ttu-id="792b4-164">Die OWIN-Startup-Klasse Aufrufe `MapSignalR` Ausführung die app die `Configuration` Methode.</span><span class="sxs-lookup"><span data-stu-id="792b4-164">The OWIN startup class calls `MapSignalR` when the app executes the `Configuration` method.</span></span> <span data-ttu-id="792b4-165">Die app fügt die Klasse, um OWINs-Startup Verarbeitung mit der `OwinStartup` Assembly-Attribut.</span><span class="sxs-lookup"><span data-stu-id="792b4-165">The app adds the class to OWIN's startup process using the `OwinStartup` assembly attribute.</span></span>

## <a name="add-the-client"></a><span data-ttu-id="792b4-166">Fügen Sie dem Client hinzu.</span><span class="sxs-lookup"><span data-stu-id="792b4-166">Add the client</span></span>

<span data-ttu-id="792b4-167">Fügen Sie der HTML-Seite für den Client hinzu.</span><span class="sxs-lookup"><span data-stu-id="792b4-167">Add the HTML page for the client.</span></span>

1. <span data-ttu-id="792b4-168">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **HTML-Seite**.</span><span class="sxs-lookup"><span data-stu-id="792b4-168">In **Solution Explorer**, right-click the project and select **Add** > **HTML Page**.</span></span>

1. <span data-ttu-id="792b4-169">Nennen Sie die Seite **Standard** , und wählen Sie **OK**.</span><span class="sxs-lookup"><span data-stu-id="792b4-169">Name the page **Default** and select **OK**.</span></span>

1. <span data-ttu-id="792b4-170">In **Projektmappen-Explorer**, mit der rechten Maustaste *"default.HTML"* , und wählen Sie **als Startseite festlegen**.</span><span class="sxs-lookup"><span data-stu-id="792b4-170">In **Solution Explorer**, right-click *Default.html* and select **Set as Start Page**.</span></span>

1. <span data-ttu-id="792b4-171">Ersetzen Sie den Standardcode in der *"default.HTML"* Datei durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="792b4-171">Replace the default code in the *Default.html* file with this code:</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample3.html?highlight=14-16)]

1. <span data-ttu-id="792b4-172">In **Projektmappen-Explorer**, erweitern Sie **Skripts**.</span><span class="sxs-lookup"><span data-stu-id="792b4-172">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="792b4-173">Skriptbibliotheken für jQuery und SignalR werden in das Projekt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="792b4-173">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="792b4-174">Der Paket-Manager wird installiert, eine höhere Version von SignalR-Skripts.</span><span class="sxs-lookup"><span data-stu-id="792b4-174">The package manager installs a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="792b4-175">Aktualisieren Sie die Skriptverweise im Codeblock auf die Versionen der Skriptdateien im Projekt entsprechen.</span><span class="sxs-lookup"><span data-stu-id="792b4-175">Update the script references in the code block to correspond to the versions of the script files in the project.</span></span>

<span data-ttu-id="792b4-176">Dieser HTML und JavaScript-Code erstellt ein rotes `div` namens `shape`.</span><span class="sxs-lookup"><span data-stu-id="792b4-176">This HTML and JavaScript code creates a red `div` called `shape`.</span></span> <span data-ttu-id="792b4-177">Sie können das Verhalten des Shapes ziehen mit der jQuery-Bibliothek und verwendet die `drag` Ereignis, um die Position der Form an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="792b4-177">It enables the shape's dragging behavior using the jQuery library and uses the `drag` event to send the shape's position to the server.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="792b4-178">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="792b4-178">Run the app</span></span>

<span data-ttu-id="792b4-179">Sie können die app ausführen, se'e es funktioniert.</span><span class="sxs-lookup"><span data-stu-id="792b4-179">You can run the app to se\`e it work.</span></span> <span data-ttu-id="792b4-180">Wenn Sie die Form in einem Browserfenster ziehen, verschiebt die Form zu anderen Browser.</span><span class="sxs-lookup"><span data-stu-id="792b4-180">When you drag the shape around a browser window, the shape moves in the other browsers too.</span></span>

1. <span data-ttu-id="792b4-181">In der Symbolleiste aktivieren **Skriptdebugging** und wählen Sie dann auf die entsprechende Schaltfläche, um die Anwendung im Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="792b4-181">In the toolbar, turn on **Script Debugging** and then select the play button to run the application in Debug mode.</span></span>

    ![Screenshot der Benutzer das Aktivieren von debugging-Modus und Play auswählen.](tutorial-high-frequency-realtime-with-signalr/_static/image3.png)

    <span data-ttu-id="792b4-183">Ein Browserfenster wird geöffnet, mit der Form "red", in der oberen rechten Ecke.</span><span class="sxs-lookup"><span data-stu-id="792b4-183">A browser window opens with the red shape in the upper-right corner.</span></span>

1. <span data-ttu-id="792b4-184">Kopieren Sie die URL der Seite.</span><span class="sxs-lookup"><span data-stu-id="792b4-184">Copy the page's URL.</span></span>

1. <span data-ttu-id="792b4-185">Öffnen Sie einen anderen Browser, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="792b4-185">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="792b4-186">Ziehen Sie die Form in einem Fenster Ihres Browsers ein.</span><span class="sxs-lookup"><span data-stu-id="792b4-186">Drag the shape in one of the browser windows.</span></span> <span data-ttu-id="792b4-187">Die Form im anderen Browserfenster folgt.</span><span class="sxs-lookup"><span data-stu-id="792b4-187">The shape in the other browser window follows.</span></span>

<span data-ttu-id="792b4-188">Während die Anwendung Funktionen, die mit dieser Methode, es ist kein empfohlene Programmiermodell.</span><span class="sxs-lookup"><span data-stu-id="792b4-188">While the application functions using this method, it's not a recommended programming model.</span></span> <span data-ttu-id="792b4-189">Es gibt keine obere Grenze für die Anzahl der gesendeten Daten abrufen.</span><span class="sxs-lookup"><span data-stu-id="792b4-189">There's no upper limit to the number of messages getting sent.</span></span> <span data-ttu-id="792b4-190">Daher erhalten die Clients und Server überlastet werden mit Nachrichten und die Leistung beeinträchtigt wird.</span><span class="sxs-lookup"><span data-stu-id="792b4-190">As a result, the clients and server get overwhelmed with messages and performance degrades.</span></span> <span data-ttu-id="792b4-191">Darüber hinaus zeigt die app eine separate Animation, auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="792b4-191">Also, the app displays a disjointed animation on the client.</span></span> <span data-ttu-id="792b4-192">Diese Animationen ruckartig liegt daran, dass die Form von jeder Methode sofort verschoben wird.</span><span class="sxs-lookup"><span data-stu-id="792b4-192">This jerky animation happens because the shape moves instantly by each method.</span></span> <span data-ttu-id="792b4-193">Es ist besser, wenn die Form reibungslos auf jeder neuen Speicherort verschoben wird.</span><span class="sxs-lookup"><span data-stu-id="792b4-193">It's better if the shape moves smoothly to each new location.</span></span> <span data-ttu-id="792b4-194">Als Nächstes erfahren Sie, wie Sie diese Probleme beheben.</span><span class="sxs-lookup"><span data-stu-id="792b4-194">Next, you learn how to fix those issues.</span></span>

## <a name="add-the-client-loop"></a><span data-ttu-id="792b4-195">Die Client-Schleife hinzufügen</span><span class="sxs-lookup"><span data-stu-id="792b4-195">Add the client loop</span></span>

<span data-ttu-id="792b4-196">Senden den Speicherort der Form auf jede MouseMove-Ereignis erstellt eine unnötige Umfang des Netzwerkverkehrs.</span><span class="sxs-lookup"><span data-stu-id="792b4-196">Sending the location of the shape on every mouse move event creates an unnecessary amount of network traffic.</span></span> <span data-ttu-id="792b4-197">Die app muss die Nachrichten vom Client gedrosselt.</span><span class="sxs-lookup"><span data-stu-id="792b4-197">The app needs to throttle the messages from the client.</span></span>

<span data-ttu-id="792b4-198">Verwenden Sie den JavaScript-Code `setInterval` Funktion, um eine Schleife einrichten, die Informationen zur neuen Position an den Server mit einer festen Rate sendet.</span><span class="sxs-lookup"><span data-stu-id="792b4-198">Use the javascript `setInterval` function to set up a loop that sends new position information to the server at a fixed rate.</span></span> <span data-ttu-id="792b4-199">Diese Schleife ist eine einfache Darstellung von "game Schleife".</span><span class="sxs-lookup"><span data-stu-id="792b4-199">This loop is a basic representation of a "game loop."</span></span> <span data-ttu-id="792b4-200">Es ist eine wiederholt aufgerufene Funktion, die alle die Funktionalität eines Spiels steuert.</span><span class="sxs-lookup"><span data-stu-id="792b4-200">It's a repeatedly called function that drives all the functionality of a game.</span></span>

1. <span data-ttu-id="792b4-201">Ersetzen Sie den Clientcode in die *"default.HTML"* Datei durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="792b4-201">Replace the client code in the *Default.html* file with this code:</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample4.html?highlight=14-16)]

    > [!IMPORTANT]
    > <span data-ttu-id="792b4-202">Sie müssen die Skriptverweise erneut zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="792b4-202">You have to replace the script references again.</span></span> <span data-ttu-id="792b4-203">Sie müssen die Versionen der Skripts im Projekt übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="792b4-203">They must match the versions of the scripts in the project.</span></span>

    <span data-ttu-id="792b4-204">Dieser neue Code fügt die `updateServerModel` Funktion.</span><span class="sxs-lookup"><span data-stu-id="792b4-204">This new code adds the `updateServerModel` function.</span></span> <span data-ttu-id="792b4-205">Es wird auf einem festen Intervall aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="792b4-205">It gets called on a fixed frequency.</span></span> <span data-ttu-id="792b4-206">Die Funktion sendet die Positionsdaten, an den Server nach der `moved` Flag gibt an, dass neue die zu sendenden Positionsdaten.</span><span class="sxs-lookup"><span data-stu-id="792b4-206">The function sends the position data to the server whenever the `moved` flag indicates that there's new position data to send.</span></span>

1. <span data-ttu-id="792b4-207">Wählen Sie die entsprechende Schaltfläche, um die Anwendung starten</span><span class="sxs-lookup"><span data-stu-id="792b4-207">Select the play button to start the application</span></span>

1. <span data-ttu-id="792b4-208">Kopieren Sie die URL der Seite.</span><span class="sxs-lookup"><span data-stu-id="792b4-208">Copy the page's URL.</span></span>

1. <span data-ttu-id="792b4-209">Öffnen Sie einen anderen Browser, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="792b4-209">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="792b4-210">Ziehen Sie die Form in einem Fenster Ihres Browsers ein.</span><span class="sxs-lookup"><span data-stu-id="792b4-210">Drag the shape in one of the browser windows.</span></span> <span data-ttu-id="792b4-211">Die Form im anderen Browserfenster folgt.</span><span class="sxs-lookup"><span data-stu-id="792b4-211">The shape in the other browser window follows.</span></span>

<span data-ttu-id="792b4-212">Haben zunächst, da die app die Anzahl der Nachrichten, die an den Server gesendet werden können drosselt, die Animation so reibungslos nicht angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="792b4-212">Since the app throttles the number of messages that get sent to the server, the animation won't appear as smooth did at first.</span></span>

## <a name="add-the-server-loop"></a><span data-ttu-id="792b4-213">Der Server-Schleife hinzufügen</span><span class="sxs-lookup"><span data-stu-id="792b4-213">Add the server loop</span></span>

<span data-ttu-id="792b4-214">Die aktuelle Anwendung wechseln Sie in Nachrichten, die vom Server an den Client gesendet so oft, sobald sie eingetroffen sind.</span><span class="sxs-lookup"><span data-stu-id="792b4-214">In the current application, messages sent from the server to the client go out as often as they're received.</span></span> <span data-ttu-id="792b4-215">Dieser Netzwerkverkehr stellt ein ähnliches Problem auf, wie Sie sehen auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="792b4-215">This network traffic presents a similar problem as we see on the client.</span></span>

<span data-ttu-id="792b4-216">Die app kann senden Nachrichten häufiger als sie benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="792b4-216">The app can send messages more often than they're needed.</span></span> <span data-ttu-id="792b4-217">Die Verbindung kann daher überflutet werden.</span><span class="sxs-lookup"><span data-stu-id="792b4-217">The connection can become flooded as a result.</span></span> <span data-ttu-id="792b4-218">Dieser Abschnitt beschreibt die Vorgehensweise beim Aktualisieren des Servers, um einen Timer hinzuzufügen, der die Rate der ausgehenden Nachrichten drosselt.</span><span class="sxs-lookup"><span data-stu-id="792b4-218">This section describes how to update the server to add a timer that throttles the rate of the outgoing messages.</span></span>

1. <span data-ttu-id="792b4-219">Ersetzen Sie den Inhalt der `MoveShapeHub.cs` durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="792b4-219">Replace the contents of `MoveShapeHub.cs` with this code:</span></span>

    [!code-csharp[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample5.cs)]

1. <span data-ttu-id="792b4-220">Wählen Sie die entsprechende Schaltfläche, um die Anwendung zu starten.</span><span class="sxs-lookup"><span data-stu-id="792b4-220">Select the play button to start the application.</span></span>

1. <span data-ttu-id="792b4-221">Kopieren Sie die URL der Seite.</span><span class="sxs-lookup"><span data-stu-id="792b4-221">Copy the page's URL.</span></span>

1. <span data-ttu-id="792b4-222">Öffnen Sie einen anderen Browser, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="792b4-222">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="792b4-223">Ziehen Sie die Form in einem Fenster Ihres Browsers ein.</span><span class="sxs-lookup"><span data-stu-id="792b4-223">Drag the shape in one of the browser windows.</span></span>

<span data-ttu-id="792b4-224">Dieser Code dehnt der Client hinzufügen die `Broadcaster` Klasse.</span><span class="sxs-lookup"><span data-stu-id="792b4-224">This code expands the client to add the `Broadcaster` class.</span></span> <span data-ttu-id="792b4-225">Die neue Klasse drosselt die ausgehenden Nachrichten unter Verwendung der `Timer` Klasse von .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="792b4-225">The new class throttles the outgoing messages using the `Timer` class from the .NET framework.</span></span>

<span data-ttu-id="792b4-226">Es ist ratsam, erfahren, dass für der eigentlichen Hub vorübergehend ist.</span><span class="sxs-lookup"><span data-stu-id="792b4-226">It's good to learn that the hub itself is transitory.</span></span> <span data-ttu-id="792b4-227">Er wird erstellt jedes Mal, wenn er benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="792b4-227">It's created every time it's needed.</span></span> <span data-ttu-id="792b4-228">Damit die app erstellt die `Broadcaster` als Singleton.</span><span class="sxs-lookup"><span data-stu-id="792b4-228">So the app creates the `Broadcaster` as a singleton.</span></span> <span data-ttu-id="792b4-229">Verzögerten Initialisierung für das Zurückstellen von verwendet die `Broadcaster`Erstellung, bis diese benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="792b4-229">It uses lazy initialization to defer the `Broadcaster`'s creation until it's needed.</span></span> <span data-ttu-id="792b4-230">Dies garantiert, dass es sich bei die app die erste hubinstanz vollständig erstellt, bevor der Timer gestartet.</span><span class="sxs-lookup"><span data-stu-id="792b4-230">That guarantees that the app creates the first hub instance completely before starting the timer.</span></span>

<span data-ttu-id="792b4-231">Der Aufruf des Clients `UpdateShape` Funktion wird dann aus des Hubs des verschoben `UpdateModel` Methode.</span><span class="sxs-lookup"><span data-stu-id="792b4-231">The call to the clients' `UpdateShape` function is then moved out of the hub's `UpdateModel` method.</span></span> <span data-ttu-id="792b4-232">Sie wird nicht mehr aufgerufen, sobald die app auf eingehende Nachrichten empfängt.</span><span class="sxs-lookup"><span data-stu-id="792b4-232">It's no longer called immediately whenever the app receives incoming messages.</span></span> <span data-ttu-id="792b4-233">Stattdessen sendet die app die Nachrichten an die Clients mit einer Geschwindigkeit von 25 Aufrufe pro Sekunde.</span><span class="sxs-lookup"><span data-stu-id="792b4-233">Instead, the app sends the messages to the clients at a rate of 25 calls per second.</span></span> <span data-ttu-id="792b4-234">Der Prozess wird von verwaltet die `_broadcastLoop` Zeitgeber innerhalb der `Broadcaster` Klasse.</span><span class="sxs-lookup"><span data-stu-id="792b4-234">The process is  managed by the `_broadcastLoop` timer from within the `Broadcaster` class.</span></span>

<span data-ttu-id="792b4-235">Abschließend anstelle der Methode aus dem Hub direkt die `Broadcaster` Klasse muss einen Verweis auf die gerade arbeiten `_hubContext` Hub.</span><span class="sxs-lookup"><span data-stu-id="792b4-235">Lastly, instead of calling the client method from the hub directly, the `Broadcaster` class needs to get a reference to the currently operating `_hubContext` hub.</span></span> <span data-ttu-id="792b4-236">Er ruft den Verweis auf die `GlobalHost`.</span><span class="sxs-lookup"><span data-stu-id="792b4-236">It gets the reference with the `GlobalHost`.</span></span>

## <a name="add-smooth-animation"></a><span data-ttu-id="792b4-237">Fügen Sie flüssige Animation hinzu.</span><span class="sxs-lookup"><span data-stu-id="792b4-237">Add smooth animation</span></span>

<span data-ttu-id="792b4-238">Die Anwendung ist fast fertig, aber stellen wir eine weitere Verbesserung.</span><span class="sxs-lookup"><span data-stu-id="792b4-238">The application is almost finished, but we could make one more improvement.</span></span> <span data-ttu-id="792b4-239">Die app verschiebt die Form auf dem Client als Antwort auf die Server-Meldungen.</span><span class="sxs-lookup"><span data-stu-id="792b4-239">The app moves the shape on the client in response to server messages.</span></span> <span data-ttu-id="792b4-240">Anstatt die Position der Form an den neuen Speicherort, der vom Server erhalten, verwenden Sie der JQuery-UI-Bibliotheks `animate` Funktion.</span><span class="sxs-lookup"><span data-stu-id="792b4-240">Instead of setting the position of the shape to the new location given by the server, use the JQuery UI library's `animate` function.</span></span> <span data-ttu-id="792b4-241">Sie können die Form reibungslos zwischen der aktuellen und neuen Position verschieben.</span><span class="sxs-lookup"><span data-stu-id="792b4-241">It can move the shape smoothly between its current and new position.</span></span>

1. <span data-ttu-id="792b4-242">Aktualisieren des Clients `updateShape` -Methode in der die *"default.HTML"* Datei der hervorgehobene Code aussehen:</span><span class="sxs-lookup"><span data-stu-id="792b4-242">Update the client's `updateShape` method in the *Default.html* file to look like the highlighted code:</span></span>

    [!code-html[Main](tutorial-high-frequency-realtime-with-signalr/samples/sample6.html?highlight=33-40)]

1. <span data-ttu-id="792b4-243">Wählen Sie die entsprechende Schaltfläche, um die Anwendung zu starten.</span><span class="sxs-lookup"><span data-stu-id="792b4-243">Select the play button to start the application.</span></span>

1. <span data-ttu-id="792b4-244">Kopieren Sie die URL der Seite.</span><span class="sxs-lookup"><span data-stu-id="792b4-244">Copy the page's URL.</span></span>

1. <span data-ttu-id="792b4-245">Öffnen Sie einen anderen Browser, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="792b4-245">Open another browser and paste the URL into the address bar.</span></span>

1. <span data-ttu-id="792b4-246">Ziehen Sie die Form in einem Fenster Ihres Browsers ein.</span><span class="sxs-lookup"><span data-stu-id="792b4-246">Drag the shape in one of the browser windows.</span></span>

<span data-ttu-id="792b4-247">Weniger ruckartige, wird die Verschiebung von der Form, in dem anderen Fenster angezeigt.</span><span class="sxs-lookup"><span data-stu-id="792b4-247">The movement of the shape in the other window appears less jerky.</span></span> <span data-ttu-id="792b4-248">Die app führt ihre Bewegung über anstatt einmal pro eingehender Nachricht festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="792b4-248">The app interpolates its movement over time rather than being set once per incoming message.</span></span>

<span data-ttu-id="792b4-249">Dieser Code wird die Form am alten Speicherort in das neue Projekt verschoben.</span><span class="sxs-lookup"><span data-stu-id="792b4-249">This code moves the shape from the old location to the new one.</span></span> <span data-ttu-id="792b4-250">Der Server gibt die Position der Form im Verlauf des Intervalls Animation an.</span><span class="sxs-lookup"><span data-stu-id="792b4-250">The server gives the position of the shape over the course of the animation interval.</span></span> <span data-ttu-id="792b4-251">In diesem Fall ist, die 100 Millisekunden.</span><span class="sxs-lookup"><span data-stu-id="792b4-251">In this case, that's 100 milliseconds.</span></span> <span data-ttu-id="792b4-252">Die app löscht alle vorherige Animation, auf die Form ausgeführt werden, bevor die neue Animation gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="792b4-252">The app clears any previous animation running on the shape before the new animation starts.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="792b4-253">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="792b4-253">Get the code</span></span>

[<span data-ttu-id="792b4-254">Abgeschlossenes Projekt herunterladen</span><span class="sxs-lookup"><span data-stu-id="792b4-254">Download Completed Project</span></span>](http://code.msdn.microsoft.com/SignalR-20-MoveShape-Demo-6285b83a)

## <a name="additional-resources"></a><span data-ttu-id="792b4-255">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="792b4-255">Additional resources</span></span>

<span data-ttu-id="792b4-256">Das Communication-Paradigma, die Sie gerade gelernt haben, zu eignet sich für die Entwicklung von Onlinespielen und andere Simulationen, wie z. B. [ShootR Spiel erstellt, die mit SignalR](https://shootr.azurewebsites.net/).</span><span class="sxs-lookup"><span data-stu-id="792b4-256">The communication paradigm you just learned about is useful for developing online games and other simulations, like [the ShootR game created with SignalR](https://shootr.azurewebsites.net/).</span></span>

<span data-ttu-id="792b4-257">Weitere Informationen zu SignalR finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="792b4-257">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="792b4-258">SignalR-Projekt</span><span class="sxs-lookup"><span data-stu-id="792b4-258">SignalR Project</span></span>](http://signalr.net)

* [<span data-ttu-id="792b4-259">SignalR GitHub und Beispiele</span><span class="sxs-lookup"><span data-stu-id="792b4-259">SignalR GitHub and Samples</span></span>](https://github.com/SignalR/SignalR)

* [<span data-ttu-id="792b4-260">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="792b4-260">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="792b4-261">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="792b4-261">Next steps</span></span>

<span data-ttu-id="792b4-262">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="792b4-262">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="792b4-263">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="792b4-263">Set up the project</span></span>
> * <span data-ttu-id="792b4-264">Die Basis-Anwendung erstellt</span><span class="sxs-lookup"><span data-stu-id="792b4-264">Created the base application</span></span>
> * <span data-ttu-id="792b4-265">An den Hub zugeordnet werden, wenn die app gestartet wird</span><span class="sxs-lookup"><span data-stu-id="792b4-265">Mapped to the hub when app starts</span></span>
> * <span data-ttu-id="792b4-266">Den Client hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="792b4-266">Added the client</span></span>
> * <span data-ttu-id="792b4-267">Die app ausgeführt</span><span class="sxs-lookup"><span data-stu-id="792b4-267">Ran the app</span></span>
> * <span data-ttu-id="792b4-268">Die Client-Schleife hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="792b4-268">Added the client loop</span></span>
> * <span data-ttu-id="792b4-269">Die Schleife Server hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="792b4-269">Added the server loop</span></span>
> * <span data-ttu-id="792b4-270">Hinzugefügte flüssige animation</span><span class="sxs-lookup"><span data-stu-id="792b4-270">Added smooth animation</span></span>

<span data-ttu-id="792b4-271">Wechseln Sie zum nächsten Artikel erfahren, wie eine Webanwendung erstellen, die ASP.NET SignalR 2 verwendet, um Server-broadcast-Funktionalität bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="792b4-271">Advance to the next article to learn how to create a web application that uses ASP.NET SignalR 2 to provide server broadcast functionality.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="792b4-272">SignalR 2 und Server broadcasting</span><span class="sxs-lookup"><span data-stu-id="792b4-272">SignalR 2 and server broadcasting</span></span>](tutorial-server-broadcast-with-signalr.md)