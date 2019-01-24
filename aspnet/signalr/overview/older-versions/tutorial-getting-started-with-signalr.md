---
uid: signalr/overview/older-versions/tutorial-getting-started-with-signalr
title: 'Tutorial: Erste Schritte mit SignalR 1.x | Microsoft-Dokumentation'
author: bradygaster
description: Verwenden Sie ASP.NET SignalR, um eine Echtzeit-Chat-Anwendung in eine HTML-Seite zu erstellen.
ms.author: bradyg
ms.date: 02/18/2013
ms.assetid: fdc3599a-5217-44c1-951f-0eec9812dce7
msc.legacyurl: /signalr/overview/older-versions/tutorial-getting-started-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: b4b632a84e40aa0b73dfc7a30da0cf28249cc5b4
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836631"
---
<a name="tutorial-getting-started-with-signalr-1x"></a><span data-ttu-id="05fea-103">Tutorial: Erste Schritte mit SignalR 1.x</span><span class="sxs-lookup"><span data-stu-id="05fea-103">Tutorial: Getting Started with SignalR 1.x</span></span>
====================
<span data-ttu-id="05fea-104">durch [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span><span class="sxs-lookup"><span data-stu-id="05fea-104">by [Patrick Fletcher](https://github.com/pfletcher), [Tim Teebken](https://github.com/timlt)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="05fea-105">Dieses Tutorial zeigt, wie Sie mithilfe von SignalR eine Chatanwendung mit Echtzeitfunktionalität erstellen.</span><span class="sxs-lookup"><span data-stu-id="05fea-105">This tutorial shows how to use SignalR to create a real-time chat application.</span></span> <span data-ttu-id="05fea-106">Sie werden eine leere ASP.NET-Webanwendung SignalR hinzu, und erstellen Sie eine HTML-Seite zum Senden und Meldungen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="05fea-106">You will add SignalR to an empty ASP.NET web application and create an HTML page to send and display messages.</span></span>


## <a name="overview"></a><span data-ttu-id="05fea-107">Übersicht</span><span class="sxs-lookup"><span data-stu-id="05fea-107">Overview</span></span>

<span data-ttu-id="05fea-108">In diesem Tutorial wird SignalR-Entwicklung eingeführt, wie eine einfache browserbasierte Chat-Anwendung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="05fea-108">This tutorial introduces SignalR development by showing how to build a simple browser-based chat application.</span></span> <span data-ttu-id="05fea-109">Sie werden die SignalR-Bibliothek mit einer leeren ASP.NET-Webanwendung hinzufügen, erstellen Sie eine hubklasse für das Senden von Nachrichten für Clients und erstellen Sie eine HTML-Seite, mit dem Benutzer, senden und Empfangen von Chatnachrichten.</span><span class="sxs-lookup"><span data-stu-id="05fea-109">You will add the SignalR library to an empty ASP.NET web application, create a hub class for sending messages to clients, and create an HTML page that lets users send and receive chat messages.</span></span> <span data-ttu-id="05fea-110">Ein ähnliches Tutorial, das Erstellen eine chatanwendung in MVC 4 zeigt die Verwendung einer MVC-Ansicht, finden Sie unter [erste Schritte mit SignalR und MVC 4](index.md).</span><span class="sxs-lookup"><span data-stu-id="05fea-110">For a similar tutorial that shows how to create a chat application in MVC 4 using an MVC view, see [Getting Started with SignalR and MVC 4](index.md).</span></span>

> [!NOTE]
> <span data-ttu-id="05fea-111">Dieses Tutorial verwendet die Version (1.x) Version von SignalR.</span><span class="sxs-lookup"><span data-stu-id="05fea-111">This tutorial uses the release (1.x) version of SignalR.</span></span> <span data-ttu-id="05fea-112">Weitere Informationen zu Änderungen zwischen SignalR 1.x und 2.0, finden Sie unter [aktualisieren SignalR 1.x-Projekte](../releases/upgrading-signalr-1x-projects-to-20.md).</span><span class="sxs-lookup"><span data-stu-id="05fea-112">For details on changes between SignalR 1.x and 2.0, see [Upgrading SignalR 1.x Projects](../releases/upgrading-signalr-1x-projects-to-20.md).</span></span>

<span data-ttu-id="05fea-113">SignalR ist eine Open Source-.NET Bibliothek zum Erstellen von Webanwendungen, die live Benutzerinteraktionen oder datenaktualisierungen in Echtzeit erfordern.</span><span class="sxs-lookup"><span data-stu-id="05fea-113">SignalR is an open-source .NET library for building web applications that require live user interaction or real-time data updates.</span></span> <span data-ttu-id="05fea-114">Beispiele hierfür sind soziale Anwendungen, mehrere Benutzer Spiele, Business-Zusammenarbeit und Neuigkeiten, Wetter oder finanzielle Anwendungsupdates an.</span><span class="sxs-lookup"><span data-stu-id="05fea-114">Examples include social applications, multiuser games, business collaboration, and news, weather, or financial update applications.</span></span> <span data-ttu-id="05fea-115">Diese werden häufig in Echtzeit Anwendungen bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="05fea-115">These are often called real-time applications.</span></span>

<span data-ttu-id="05fea-116">SignalR vereinfacht das Erstellen von Echtzeit-Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="05fea-116">SignalR simplifies the process of building real-time applications.</span></span> <span data-ttu-id="05fea-117">Es enthält eine Server-Bibliothek von ASP.NET und eine JavaScript-Clientbibliothek zum Verwalten von Client / Server-Verbindungen ein, und Übertragen von Inhaltsupdates an Clients zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="05fea-117">It includes an ASP.NET server library and a JavaScript client library to make it easier to manage client-server connections and push content updates to clients.</span></span> <span data-ttu-id="05fea-118">Sie können eine vorhandene ASP.NET-Anwendung um Echtzeitfunktionen zu erhalten. die SignalR-Bibliothek hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="05fea-118">You can add the SignalR library to an existing ASP.NET application to gain real-time functionality.</span></span>

<span data-ttu-id="05fea-119">Das Tutorial veranschaulicht die folgenden Entwicklungsaufgaben von SignalR:</span><span class="sxs-lookup"><span data-stu-id="05fea-119">The tutorial demonstrates the following SignalR development tasks:</span></span>

- <span data-ttu-id="05fea-120">Eine ASP.NET-Webanwendung wird die SignalR-Bibliothek hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="05fea-120">Adding the SignalR library to an ASP.NET web application.</span></span>
- <span data-ttu-id="05fea-121">Erstellen einer hubklasse, um Inhalt an Clients zu verteilen.</span><span class="sxs-lookup"><span data-stu-id="05fea-121">Creating a hub class to push content to clients.</span></span>
- <span data-ttu-id="05fea-122">Verwenden die SignalR-jQuery-Bibliothek auf einer Webseite zum Senden von Nachrichten und Updates vom Hub angezeigt.</span><span class="sxs-lookup"><span data-stu-id="05fea-122">Using the SignalR jQuery library in a web page to send messages and display updates from the hub.</span></span>

<span data-ttu-id="05fea-123">Der folgende Screenshot zeigt die chatanwendung, die in einem Browser ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="05fea-123">The following screen shot shows the chat application running in a browser.</span></span> <span data-ttu-id="05fea-124">Jeden neuer Benutzer kann Kommentare Posten und Kommentare hinzugefügt, nachdem der Benutzer im Chat verknüpft.</span><span class="sxs-lookup"><span data-stu-id="05fea-124">Each new user can post comments and see comments added after the user joins the chat.</span></span>

![Chatinstanzen](tutorial-getting-started-with-signalr/_static/image1.png)

<span data-ttu-id="05fea-126">Abschnitte:</span><span class="sxs-lookup"><span data-stu-id="05fea-126">Sections:</span></span>

- [<span data-ttu-id="05fea-127">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="05fea-127">Set up the Project</span></span>](#setup)
- [<span data-ttu-id="05fea-128">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="05fea-128">Run the Sample</span></span>](#run)
- [<span data-ttu-id="05fea-129">Überprüfen Sie den Code</span><span class="sxs-lookup"><span data-stu-id="05fea-129">Examine the Code</span></span>](#code)
- [<span data-ttu-id="05fea-130">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="05fea-130">Next Steps</span></span>](#next)

<a id="setup"></a>

## <a name="set-up-the-project"></a><span data-ttu-id="05fea-131">Einrichten des Projekts</span><span class="sxs-lookup"><span data-stu-id="05fea-131">Set up the Project</span></span>

<span data-ttu-id="05fea-132">Fügen Sie in diesem Abschnitt zeigt, wie Sie eine leere ASP.NET-Webanwendung erstellen SignalR hinzu, und erstellen Sie die chatanwendung.</span><span class="sxs-lookup"><span data-stu-id="05fea-132">This section shows how to create an empty ASP.NET web application, add SignalR, and create the chat application.</span></span>

<span data-ttu-id="05fea-133">Erforderliche Komponenten:</span><span class="sxs-lookup"><span data-stu-id="05fea-133">Prerequisites:</span></span>

- <span data-ttu-id="05fea-134">Visual Studio 2010 SP1 oder 2012.</span><span class="sxs-lookup"><span data-stu-id="05fea-134">Visual Studio 2010 SP1 or 2012.</span></span> <span data-ttu-id="05fea-135">Wenn Sie nicht über Visual Studio verfügen, finden Sie unter [ASP.NET-Downloads](https://www.asp.net/downloads) um die kostenlose Visual Studio 2012 Express Entwicklungstool zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="05fea-135">If you do not have Visual Studio, see [ASP.NET Downloads](https://www.asp.net/downloads) to get the free Visual Studio 2012 Express Development Tool.</span></span>
- <span data-ttu-id="05fea-136">[Microsoft ASP.NET und Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=279941).</span><span class="sxs-lookup"><span data-stu-id="05fea-136">[Microsoft ASP.NET and Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=279941).</span></span> <span data-ttu-id="05fea-137">Für Visual Studio 2012 fügt dieses Installationsprogramm neue ASP.NET-Funktionen, einschließlich der SignalR-Vorlagen für Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="05fea-137">For Visual Studio 2012, this installer adds new ASP.NET features including SignalR templates to Visual Studio.</span></span> <span data-ttu-id="05fea-138">Für Visual Studio 2010 SP1 ein Installationsprogramm ist nicht verfügbar, aber Sie können das Tutorial abschließen, indem Sie den SignalR-NuGet-Paket installiert wird, wie in den setupschritten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="05fea-138">For Visual Studio 2010 SP1, an installer is not available but you can complete the tutorial by installing the SignalR NuGet package as described in the setup steps.</span></span>

<span data-ttu-id="05fea-139">Die folgenden Schritte verwenden Visual Studio 2012, erstellen eine leere ASP.NET-Webanwendung und Hinzufügen von SignalR-Bibliothek:</span><span class="sxs-lookup"><span data-stu-id="05fea-139">The following steps use Visual Studio 2012 to create an ASP.NET Empty Web Application and add the SignalR library:</span></span>

1. <span data-ttu-id="05fea-140">Erstellen Sie eine leere ASP.NET-Webanwendung in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="05fea-140">In Visual Studio create an ASP.NET Empty Web Application.</span></span>

    ![Leeres Web erstellen](tutorial-getting-started-with-signalr/_static/image2.png)
2. <span data-ttu-id="05fea-142">Öffnen der **-Paket-Manager-Konsole** dazu **Tools | NuGet Paket-Manager | Paket-Manager-Konsole**.</span><span class="sxs-lookup"><span data-stu-id="05fea-142">Open the **Package Manager Console** by selecting **Tools | NuGet Package Manager | Package Manager Console**.</span></span> <span data-ttu-id="05fea-143">Geben Sie den folgenden Befehl in das Konsolenfenster ein:</span><span class="sxs-lookup"><span data-stu-id="05fea-143">Enter the following command into the console window:</span></span>

    `Install-Package Microsoft.AspNet.SignalR -Version 1.1.3`

    <span data-ttu-id="05fea-144">Dieser Befehl installiert die neueste Version von SignalR 1.x.</span><span class="sxs-lookup"><span data-stu-id="05fea-144">This command installs the latest version of SignalR 1.x.</span></span>
3. <span data-ttu-id="05fea-145">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, wählen Sie **hinzufügen | Klasse**.</span><span class="sxs-lookup"><span data-stu-id="05fea-145">In **Solution Explorer**, right-click the project, select **Add | Class**.</span></span> <span data-ttu-id="05fea-146">Nennen Sie die neue Klasse **ChatHub**.</span><span class="sxs-lookup"><span data-stu-id="05fea-146">Name the new class **ChatHub**.</span></span>
4. <span data-ttu-id="05fea-147">In **Projektmappen-Explorer** erweitern Sie den Knoten des Skripts.</span><span class="sxs-lookup"><span data-stu-id="05fea-147">In **Solution Explorer** expand the Scripts node.</span></span> <span data-ttu-id="05fea-148">Skriptbibliotheken für jQuery und SignalR werden in das Projekt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="05fea-148">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    ![Verweise auf die Typbibliothek](tutorial-getting-started-with-signalr/_static/image3.png)
5. <span data-ttu-id="05fea-150">Ersetzen Sie den Code in die **ChatHub** Klasse durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="05fea-150">Replace the code in the **ChatHub** class with the following code.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample1.cs)]
6. <span data-ttu-id="05fea-151">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und klicken Sie auf **hinzufügen | Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="05fea-151">In **Solution Explorer**, right-click the project, then click **Add | New Item**.</span></span> <span data-ttu-id="05fea-152">In der **neues Element hinzufügen** wählen Sie im Dialogfeld **Globale Anwendungsklasse** , und klicken Sie auf **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="05fea-152">In the **Add New Item** dialog, select **Global Application Class** and click **Add**.</span></span>

    ![Hinzufügen einer globalen](tutorial-getting-started-with-signalr/_static/image4.png)
7. <span data-ttu-id="05fea-154">Fügen Sie die folgenden `using` Anweisungen nach der bereitgestellten `using` Anweisungen in der Klasse "Global.asax.cs".</span><span class="sxs-lookup"><span data-stu-id="05fea-154">Add the following `using` statements after the provided `using` statements in the Global.asax.cs class.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample2.cs)]
8. <span data-ttu-id="05fea-155">Fügen Sie die folgende Zeile des Codes in der `Application_Start` -Methode der globalen Klasse um die Standardroute für SignalR-Hubs zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="05fea-155">Add the following line of code in the `Application_Start` method of the Global class to register the default route for SignalR hubs.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample3.cs)]
9. <span data-ttu-id="05fea-156">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und klicken Sie auf **hinzufügen | Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="05fea-156">In **Solution Explorer**, right-click the project, then click **Add | New Item**.</span></span> <span data-ttu-id="05fea-157">In der **neues Element hinzufügen** Dialogfeld, auf Html-Seite, und klicken Sie auf **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="05fea-157">In the **Add New Item** dialog, select Html Page and click **Add**.</span></span>
10. <span data-ttu-id="05fea-158">In **Projektmappen-Explorer**mit der rechten Maustaste auf die HTML-Seite, die Sie gerade erstellt haben, und klicken Sie auf **als Startseite festlegen**.</span><span class="sxs-lookup"><span data-stu-id="05fea-158">In **Solution Explorer**, right-click the HTML page you just created and click **Set as Start Page**.</span></span>
11. <span data-ttu-id="05fea-159">Ersetzen Sie den Standardcode in der HTML-Seite mit den folgenden Code ein.</span><span class="sxs-lookup"><span data-stu-id="05fea-159">Replace the default code in the HTML page with the following code.</span></span>

    [!code-html[Main](tutorial-getting-started-with-signalr/samples/sample4.html)]
12. <span data-ttu-id="05fea-160">**Speichern Sie alle** für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="05fea-160">**Save All** for the project.</span></span>

<a id="run"></a>

## <a name="run-the-sample"></a><span data-ttu-id="05fea-161">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="05fea-161">Run the Sample</span></span>

1. <span data-ttu-id="05fea-162">Drücken Sie F5, um das Projekt im Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="05fea-162">Press F5 to run the project in debug mode.</span></span> <span data-ttu-id="05fea-163">Die HTML-Seite lädt in eine Instanz eines Webbrowsers und aufforderungen für einen Benutzernamen ein.</span><span class="sxs-lookup"><span data-stu-id="05fea-163">The HTML page loads in a browser instance and prompts for a user name.</span></span>

    ![Benutzername eingeben](tutorial-getting-started-with-signalr/_static/image5.png)
2. <span data-ttu-id="05fea-165">Geben Sie einen Benutzernamen ein.</span><span class="sxs-lookup"><span data-stu-id="05fea-165">Enter a user name.</span></span>
3. <span data-ttu-id="05fea-166">Kopieren Sie die URL aus der Adresszeile des Browsers, und öffnen Sie zwei Browserinstanzen für weitere.</span><span class="sxs-lookup"><span data-stu-id="05fea-166">Copy the URL from the address line of the browser and use it to open two more browser instances.</span></span> <span data-ttu-id="05fea-167">Geben Sie in den einzelnen Instanzen Browser einen eindeutigen Benutzernamen ein.</span><span class="sxs-lookup"><span data-stu-id="05fea-167">In each browser instance, enter a unique user name.</span></span>
4. <span data-ttu-id="05fea-168">Klicken Sie in den einzelnen Instanzen Browser Fügt einen Kommentar hinzu, und klicken Sie auf **senden**.</span><span class="sxs-lookup"><span data-stu-id="05fea-168">In each browser instance, add a comment and click **Send**.</span></span> <span data-ttu-id="05fea-169">Die Kommentare sollte in alle Browserinstanzen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="05fea-169">The comments should display in all browser instances.</span></span>

    > [!NOTE]
    > <span data-ttu-id="05fea-170">Diese einfache chatanwendung werden den Kontext der Diskussion auf dem Server nicht verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="05fea-170">This simple chat application does not maintain the discussion context on the server.</span></span> <span data-ttu-id="05fea-171">Der Hub sendet, Kommentare, um alle aktuellen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="05fea-171">The hub broadcasts comments to all current users.</span></span> <span data-ttu-id="05fea-172">Benutzer, die im Chat später zusammenfügen sehen, dass Nachrichten ab dem Zeitpunkt hinzugefügt werden, dass sie aufgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="05fea-172">Users who join the chat later will see messages added from the time they join.</span></span>

    <span data-ttu-id="05fea-173">Der folgende Screenshot zeigt die Chat-Anwendung, die auf drei Browserinstanzen, die aktualisiert werden, wenn eine Instanz eine Nachricht sendet:</span><span class="sxs-lookup"><span data-stu-id="05fea-173">The following screen shot shows the chat application running in three browser instances, all of which are updated when one instance sends a message:</span></span>

    ![Chat-Browser](tutorial-getting-started-with-signalr/_static/image6.png)
5. <span data-ttu-id="05fea-175">In **Projektmappen-Explorer**, überprüfen Sie die **Skriptdokumente** Knoten für die ausgeführte Anwendung.</span><span class="sxs-lookup"><span data-stu-id="05fea-175">In **Solution Explorer**, inspect the **Script Documents** node for the running application.</span></span> <span data-ttu-id="05fea-176">Es gibt eine Skriptdatei namens **Hubs** , die den SignalR-Bibliothek wird zur Laufzeit dynamisch generiert.</span><span class="sxs-lookup"><span data-stu-id="05fea-176">There is a script file named **hubs** that the SignalR library dynamically generates at runtime.</span></span> <span data-ttu-id="05fea-177">Diese Datei, verwaltet die Kommunikation zwischen der jQuery-Skripts und serverseitigen Code.</span><span class="sxs-lookup"><span data-stu-id="05fea-177">This file manages the communication between jQuery script and server-side code.</span></span>

    ![Generierte Hub-Skript](tutorial-getting-started-with-signalr/_static/image7.png)

<a id="code"></a>

## <a name="examine-the-code"></a><span data-ttu-id="05fea-179">Überprüfen Sie den Code</span><span class="sxs-lookup"><span data-stu-id="05fea-179">Examine the Code</span></span>

<span data-ttu-id="05fea-180">Der SignalR Chat-Anwendung zeigt zwei grundlegende Aufgaben für die SignalR-Entwicklung: Erstellen eines Hubs wie das wichtigste Coordination-Objekt, auf dem Server, und mithilfe der SignalR-jQuery-Bibliothek zum Senden und Empfangen von Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="05fea-180">The SignalR chat application demonstrates two basic SignalR development tasks: creating a hub as the main coordination object on the server, and using the SignalR jQuery library to send and receive messages.</span></span>

### <a name="signalr-hubs"></a><span data-ttu-id="05fea-181">SignalR Hubs</span><span class="sxs-lookup"><span data-stu-id="05fea-181">SignalR Hubs</span></span>

<span data-ttu-id="05fea-182">Im Codebeispiel enthält die **ChatHub** Klasse leitet sich von der **Microsoft.AspNet.SignalR.Hub** Klasse.</span><span class="sxs-lookup"><span data-stu-id="05fea-182">In the code sample the **ChatHub** class derives from the **Microsoft.AspNet.SignalR.Hub** class.</span></span> <span data-ttu-id="05fea-183">Ableiten von der **Hub** Klasse ist eine gute Möglichkeit, eine SignalR-Anwendung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="05fea-183">Deriving from the **Hub** class is a useful way to build a SignalR application.</span></span> <span data-ttu-id="05fea-184">Sie können öffentliche Methoden auf Ihrem Hub-Klasse erstellen und dann Zugriff auf diese Methoden durch einen Aufruf über die jQuery-Skripts auf einer Webseite.</span><span class="sxs-lookup"><span data-stu-id="05fea-184">You can create public methods on your hub class and then access those methods by calling them from jQuery scripts in a web page.</span></span>

<span data-ttu-id="05fea-185">Im Code Chat Clients rufen die **ChatHub.Send** Methode, um eine neue Nachricht zu senden.</span><span class="sxs-lookup"><span data-stu-id="05fea-185">In the chat code, clients call the **ChatHub.Send** method to send a new message.</span></span> <span data-ttu-id="05fea-186">Der Hub wiederum sendet die Nachricht an alle Clients durch den Aufruf **Clients.All.broadcastMessage**.</span><span class="sxs-lookup"><span data-stu-id="05fea-186">The hub in turn sends the message to all clients by calling **Clients.All.broadcastMessage**.</span></span>

<span data-ttu-id="05fea-187">Die **senden** -Methode demonstriert, mehrere Hub-Konzepte:</span><span class="sxs-lookup"><span data-stu-id="05fea-187">The **Send** method demonstrates several hub concepts :</span></span>

- <span data-ttu-id="05fea-188">Deklarieren Sie öffentliche Methoden für einen Hub, damit Clients, die sie aufrufen können.</span><span class="sxs-lookup"><span data-stu-id="05fea-188">Declare public methods on a hub so that clients can call them.</span></span>
- <span data-ttu-id="05fea-189">Verwenden der **Microsoft.AspNet.SignalR.Hub.Clients** dynamische Eigenschaft alle Clients den Zugriff auf, die mit diesem Hub verbunden sind.</span><span class="sxs-lookup"><span data-stu-id="05fea-189">Use the **Microsoft.AspNet.SignalR.Hub.Clients** dynamic property to access all clients connected to this hub.</span></span>
- <span data-ttu-id="05fea-190">Aufrufen einer jQuery-Funktion auf dem Client (z. B. die `broadcastMessage` Funktion) zum Aktualisieren von Clients.</span><span class="sxs-lookup"><span data-stu-id="05fea-190">Call a jQuery function on the client (such as the `broadcastMessage` function) to update clients.</span></span>

    [!code-csharp[Main](tutorial-getting-started-with-signalr/samples/sample5.cs)]

### <a name="signalr-and-jquery"></a><span data-ttu-id="05fea-191">SignalR und jQuery</span><span class="sxs-lookup"><span data-stu-id="05fea-191">SignalR and jQuery</span></span>

<span data-ttu-id="05fea-192">Die HTML-Seite im Codebeispiel veranschaulicht, wie die SignalR-jQuery-Bibliothek für die Kommunikation mit einer SignalR-Hub.</span><span class="sxs-lookup"><span data-stu-id="05fea-192">The HTML page in the code sample shows how to use the SignalR jQuery library to communicate with a SignalR hub.</span></span> <span data-ttu-id="05fea-193">Die wesentlichen Aufgaben in den Code deklarieren einen Proxy für den Hub, Deklarieren einer Funktion, die Inhalt per Pushvorgang an Clients der Server aufrufen können, und starten eine Verbindung zum Senden von Nachrichten an den Hub zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="05fea-193">The essential tasks in the code are declaring a proxy to reference the hub, declaring a function that the server can call to push content to clients, and starting a connection to send messages to the hub.</span></span>

<span data-ttu-id="05fea-194">Der folgende Code deklariert einen Proxy für einen Hub.</span><span class="sxs-lookup"><span data-stu-id="05fea-194">The following code declares a proxy for a hub.</span></span>

[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample6.js)]

> [!NOTE]
> <span data-ttu-id="05fea-195">In jQuery wird der Verweis auf die Server-Klasse und ihre Member in Camel-Case.</span><span class="sxs-lookup"><span data-stu-id="05fea-195">In jQuery the reference to the server class and its members is in camel case.</span></span> <span data-ttu-id="05fea-196">Das Codebeispiel verweist auf die C#- **ChatHub** Klasse in jQuery als **ChatHub**.</span><span class="sxs-lookup"><span data-stu-id="05fea-196">The code sample references the C# **ChatHub** class in jQuery as **chatHub**.</span></span>


<span data-ttu-id="05fea-197">Der folgende Code ist, wie Sie eine Callback-Funktion im Skript erstellen.</span><span class="sxs-lookup"><span data-stu-id="05fea-197">The following code is how you create a callback function in the script.</span></span> <span data-ttu-id="05fea-198">Die hubklasse auf dem Server ruft diese Funktion, um Inhaltsupdates per Pushvorgang an jeden Client übertragen.</span><span class="sxs-lookup"><span data-stu-id="05fea-198">The hub class on the server calls this function to push content updates to each client.</span></span> <span data-ttu-id="05fea-199">Die beiden Zeilen an, dass die HTML-Codierung vor der Anzeige des Inhalts sind optional, und zeigen eine einfache Möglichkeit zum Skript-Injection verhindern.</span><span class="sxs-lookup"><span data-stu-id="05fea-199">The two lines that HTML encode the content before displaying it are optional and show a simple way to prevent script injection.</span></span>

[!code-html[Main](tutorial-getting-started-with-signalr/samples/sample7.html)]

<span data-ttu-id="05fea-200">Der folgende Code zeigt, wie Sie eine Verbindung mit dem Hub zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="05fea-200">The following code shows how to open a connection with the hub.</span></span> <span data-ttu-id="05fea-201">Der Code wird die Verbindung gestartet und leitet diese dann in eine Funktion zum Behandeln der Click-Ereignis auf der **senden** auf der HTML-Seite.</span><span class="sxs-lookup"><span data-stu-id="05fea-201">The code starts the connection and then passes it a function to handle the click event on the **Send** button in the HTML page.</span></span>

> [!NOTE]
> <span data-ttu-id="05fea-202">Dieser Ansatz wird sichergestellt, dass die Verbindung hergestellt wurde, bevor der Ereignishandler ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="05fea-202">This approach insures that the connection is established before the event handler executes.</span></span>


[!code-javascript[Main](tutorial-getting-started-with-signalr/samples/sample8.js)]

<a id="next"></a>

## <a name="next-steps"></a><span data-ttu-id="05fea-203">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="05fea-203">Next Steps</span></span>

<span data-ttu-id="05fea-204">Sie haben gelernt, dass es sich bei SignalR handelt es sich ein Framework zum Erstellen von Echtzeit-Webanwendungen.</span><span class="sxs-lookup"><span data-stu-id="05fea-204">You learned that SignalR is a framework for building real-time web applications.</span></span> <span data-ttu-id="05fea-205">Sie erfahren auch mehrere Aufgaben bei der Entwicklung von SignalR: wie eine ASP.NET-Anwendung SignalR hinzugefügt, wie zum Erstellen einer hubklasse und das Senden und Empfangen von Nachrichten aus dem Hub.</span><span class="sxs-lookup"><span data-stu-id="05fea-205">You also learned several SignalR development tasks: how to add SignalR to an ASP.NET application, how to create a hub class, and how to send and receive messages from the hub.</span></span>

<span data-ttu-id="05fea-206">Sie können die beispielanwendung in diesem Tutorial oder andere SignalR-Anwendungen verfügbar über das Internet, indem sie bei einem Hostinganbieter bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="05fea-206">You can make the sample application in this tutorial or other SignalR applications available over the Internet by deploying them to a hosting provider.</span></span> <span data-ttu-id="05fea-207">Microsoft bietet kostenloses Webhosting für bis zu 10 Websites in einem kostenlosen [Windows Azure-Testkonto](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604).</span><span class="sxs-lookup"><span data-stu-id="05fea-207">Microsoft offers free web hosting for up to 10 web sites in a free [Windows Azure trial account](https://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A443DD604).</span></span> <span data-ttu-id="05fea-208">Eine exemplarische Vorgehensweise zum Bereitstellen der beispielanwendung für die SignalR finden Sie unter [der SignalR Beispiel "Erste Schritte" als ein Windows Azure-Website veröffentlichen](https://blogs.msdn.com/b/timlee/archive/2013/02/27/deploy-the-signalr-getting-started-sample-as-a-windows-azure-web-site.aspx).</span><span class="sxs-lookup"><span data-stu-id="05fea-208">For a walkthrough on how to deploy the sample SignalR application, see [Publish the SignalR Getting Started Sample as a Windows Azure Web Site](https://blogs.msdn.com/b/timlee/archive/2013/02/27/deploy-the-signalr-getting-started-sample-as-a-windows-azure-web-site.aspx).</span></span> <span data-ttu-id="05fea-209">Ausführliche Informationen dazu, wie Sie ein Visual Studio-Webprojekt auf einer Windows Azure-Website bereitstellen, finden Sie unter [Bereitstellung einer ASP.NET-Anwendung auf einem Windows Azure-Website](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="05fea-209">For detailed information about how to deploy a Visual Studio web project to a Windows Azure Web Site, see [Deploying an ASP.NET Application to a Windows Azure Web Site](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet).</span></span> <span data-ttu-id="05fea-210">(Beachten Sie: Der WebSocket-Transport wird derzeit nicht für Windows Azure Web Sites unterstützt.</span><span class="sxs-lookup"><span data-stu-id="05fea-210">(Note: The WebSocket transport is not currently supported for Windows Azure Web Sites.</span></span> <span data-ttu-id="05fea-211">Bei der WebSocket-Transport ist nicht verfügbar, die SignalR verwendet die andere verfügbare Transporte wie beschrieben im Abschnitt Transporte, der die [Einführung zu SignalR Thema](index.md).)</span><span class="sxs-lookup"><span data-stu-id="05fea-211">When WebSocket transport is not available, SignalR uses the other available transports as described in the Transports section of the [Introduction to SignalR topic](index.md).)</span></span>

<span data-ttu-id="05fea-212">Erweiterte Konzepte der SignalR-Entwicklungen finden Sie unter den folgenden Websites für SignalR-Quellcode und Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="05fea-212">To learn more advanced SignalR developments concepts, visit the following sites for SignalR source code and resources:</span></span>

- [<span data-ttu-id="05fea-213">SignalR-Projekt</span><span class="sxs-lookup"><span data-stu-id="05fea-213">SignalR Project</span></span>](http://signalr.net)
- [<span data-ttu-id="05fea-214">SignalR Github und Beispiele</span><span class="sxs-lookup"><span data-stu-id="05fea-214">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="05fea-215">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="05fea-215">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)
