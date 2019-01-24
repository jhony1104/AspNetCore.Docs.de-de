---
uid: signalr/overview/testing-and-debugging/unit-testing-signalr-applications
title: Komponententests für SignalR-Anwendungen | Microsoft-Dokumentation
author: bradygaster
description: Dieser Artikel beschreibt, wie Sie die Unit Testing-Funktionen von SignalR 2.0 zu verwenden.
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: d1983524-e0d5-4ee6-9d87-1f552f7cb964
msc.legacyurl: /signalr/overview/testing-and-debugging/unit-testing-signalr-applications
msc.type: authoredcontent
ms.openlocfilehash: cb4eb25aeedfe31ac2606de9fe7d280eb95ce2e6
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836037"
---
<a name="unit-testing-signalr-applications"></a><span data-ttu-id="7b99d-103">Komponententests für SignalR-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="7b99d-103">Unit Testing SignalR Applications</span></span>
====================
<span data-ttu-id="7b99d-104">durch [Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="7b99d-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="7b99d-105">In diesem Artikel wird beschrieben, mit den Komponententest-Features von SignalR 2.</span><span class="sxs-lookup"><span data-stu-id="7b99d-105">This article describes using the Unit Testing features of SignalR 2.</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="7b99d-106">In diesem Thema verwendeten Softwareversionen</span><span class="sxs-lookup"><span data-stu-id="7b99d-106">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="7b99d-107">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="7b99d-107">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="7b99d-108">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="7b99d-108">.NET 4.5</span></span>
> - <span data-ttu-id="7b99d-109">SignalR-Version 2</span><span class="sxs-lookup"><span data-stu-id="7b99d-109">SignalR version 2</span></span>
>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="7b99d-110">Fragen und Kommentare</span><span class="sxs-lookup"><span data-stu-id="7b99d-110">Questions and comments</span></span>
>
> <span data-ttu-id="7b99d-111">Lassen Sie Feedback, auf wie Ihnen in diesem Tutorial gefallen hat und was wir in den Kommentaren am unteren Rand der Seite verbessern können.</span><span class="sxs-lookup"><span data-stu-id="7b99d-111">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="7b99d-112">Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie sie veröffentlichen das [ASP.NET SignalR-Forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) oder [StackOverflow.com](http://stackoverflow.com/).</span><span class="sxs-lookup"><span data-stu-id="7b99d-112">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<a id="unit"></a>
## <a name="unit-testing-signalr-applications"></a><span data-ttu-id="7b99d-113">Komponententests für SignalR-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="7b99d-113">Unit testing SignalR applications</span></span>

<span data-ttu-id="7b99d-114">Sie können die Komponententestfunktionen in SignalR 2 verwenden, um Komponententests für Ihre SignalR-Anwendung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7b99d-114">You can use the unit test features in SignalR 2 to create unit tests for your SignalR application.</span></span> <span data-ttu-id="7b99d-115">SignalR 2 enthält die [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) -Schnittstelle, die verwendet werden kann, um ein mock-Objekt zum Simulieren von Ihrem hubmethoden für das Testen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7b99d-115">SignalR 2 includes the [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) interface, which can be used to create a mock object to simulate your hub methods for testing.</span></span>

<span data-ttu-id="7b99d-116">In diesem Abschnitt fügen Sie Komponententests für die Anwendung erstellt, der [Tutorials: Erste Schritte](../getting-started/tutorial-getting-started-with-signalr.md) mit [XUnit.net](https://github.com/xunit/xunit) und [Moq](https://github.com/Moq/moq4).</span><span class="sxs-lookup"><span data-stu-id="7b99d-116">In this section, you'll add unit tests for the application created in the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md) using [XUnit.net](https://github.com/xunit/xunit) and [Moq](https://github.com/Moq/moq4).</span></span>

<span data-ttu-id="7b99d-117">XUnit.net dienen zum Steuern des Tests; Moq wird zum Erstellen einer [simulieren](http://en.wikipedia.org/wiki/Mock_object) Objekt zum Testen.</span><span class="sxs-lookup"><span data-stu-id="7b99d-117">XUnit.net will be used to control the test; Moq will be used to create a [mock](http://en.wikipedia.org/wiki/Mock_object) object for testing.</span></span> <span data-ttu-id="7b99d-118">Anderen Mockframeworks können verwendet werden, falls gewünscht. [NSubstitute](http://nsubstitute.github.io/) ist auch eine gute Wahl.</span><span class="sxs-lookup"><span data-stu-id="7b99d-118">Other mocking frameworks can be used if desired; [NSubstitute](http://nsubstitute.github.io/) is also a good choice.</span></span> <span data-ttu-id="7b99d-119">In diesem Tutorial wird veranschaulicht, wie Sie das mock-Objekt auf zwei Arten einrichten: Zunächst mithilfe einer `dynamic` Objekt (eingeführt in .NET Framework 4) und dann mithilfe einer Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="7b99d-119">This tutorial demonstrates how to set up the mock object in two ways: First, using a `dynamic` object (introduced in .NET Framework 4), and second, using an interface.</span></span>

### <a name="contents"></a><span data-ttu-id="7b99d-120">Inhalt</span><span class="sxs-lookup"><span data-stu-id="7b99d-120">Contents</span></span>

<span data-ttu-id="7b99d-121">In diesem Tutorial enthält die folgenden Abschnitte.</span><span class="sxs-lookup"><span data-stu-id="7b99d-121">This tutorial contains the following sections.</span></span>

- [<span data-ttu-id="7b99d-122">Komponententests mit dynamischen</span><span class="sxs-lookup"><span data-stu-id="7b99d-122">Unit testing with Dynamic</span></span>](#dynamic)
- [<span data-ttu-id="7b99d-123">Komponententests, die nach Typ</span><span class="sxs-lookup"><span data-stu-id="7b99d-123">Unit testing by type</span></span>](#type)

<a id="dynamic"></a>
### <a name="unit-testing-with-dynamic"></a><span data-ttu-id="7b99d-124">Komponententests mit dynamischen</span><span class="sxs-lookup"><span data-stu-id="7b99d-124">Unit testing with Dynamic</span></span>

<span data-ttu-id="7b99d-125">In diesem Abschnitt fügen Sie einen Komponententest für die Anwendung erstellt, der [Tutorials: Erste Schritte](../getting-started/tutorial-getting-started-with-signalr.md) ein dynamisches Objekt verwenden.</span><span class="sxs-lookup"><span data-stu-id="7b99d-125">In this section, you'll add a unit test for the application created in the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md) using a dynamic object.</span></span>

1. <span data-ttu-id="7b99d-126">Installieren Sie die [XUnit Runner Erweiterung](https://visualstudiogallery.msdn.microsoft.com/463c5987-f82b-46c8-a97e-b1cde42b9099) für Visual Studio 2013.</span><span class="sxs-lookup"><span data-stu-id="7b99d-126">Install the [XUnit Runner extension](https://visualstudiogallery.msdn.microsoft.com/463c5987-f82b-46c8-a97e-b1cde42b9099) for Visual Studio 2013.</span></span>
2. <span data-ttu-id="7b99d-127">Entweder ist zum Abschließen der [Tutorials: Erste Schritte](../getting-started/tutorial-getting-started-with-signalr.md), oder die fertige Anwendung herunterladen [MSDN Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).</span><span class="sxs-lookup"><span data-stu-id="7b99d-127">Either complete the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md), or download the completed application from [MSDN Code Gallery](https://code.msdn.microsoft.com/SignalR-Getting-Started-b9d18aa9).</span></span>
3. <span data-ttu-id="7b99d-128">Wenn Sie die Download-Version erste Schritte-Anwendung verwenden, öffnen Sie **-Paket-Manager-Konsole** , und klicken Sie auf **wiederherstellen** das SignalR-Paket zum Projekt hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="7b99d-128">If you are using the download version of the Getting Started application, open **Package Manager Console** and click **Restore** to add the SignalR package to the project.</span></span>

    ![Wiederherstellen von Paketen](unit-testing-signalr-applications/_static/image1.png)
4. <span data-ttu-id="7b99d-130">Fügen Sie ein Projekt der Projektmappe für den Komponententest aus.</span><span class="sxs-lookup"><span data-stu-id="7b99d-130">Add a project to the solution for the unit test.</span></span> <span data-ttu-id="7b99d-131">Mit der rechten Maustaste in der Projektmappe in **Projektmappen-Explorer** , und wählen Sie **hinzufügen**, **neues Projekt...** . Unter den **c#** Knoten die **Windows** Knoten.</span><span class="sxs-lookup"><span data-stu-id="7b99d-131">Right-click your solution in **Solution Explorer** and select **Add**, **New Project...**. Under the **C#** node, select the **Windows** node.</span></span> <span data-ttu-id="7b99d-132">Wählen Sie **Klassenbibliothek**.</span><span class="sxs-lookup"><span data-stu-id="7b99d-132">Select **Class Library**.</span></span> <span data-ttu-id="7b99d-133">Nennen Sie das neue Projekt **Testbibliothek** , und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="7b99d-133">Name the new project **TestLibrary** and click **OK**.</span></span>

    ![Erstellen Sie die Bibliothek für das Testen](unit-testing-signalr-applications/_static/image2.png)
5. <span data-ttu-id="7b99d-135">Fügen Sie einen Verweis auf das Projekt SignalRChat im Testprojekt-Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="7b99d-135">Add a reference in the test library project to the SignalRChat project.</span></span> <span data-ttu-id="7b99d-136">Mit der rechten Maustaste die **Testbibliothek** Projekt, und wählen **hinzufügen**, **Verweis...** . Wählen Sie die **Projekte** unter den Knoten der **Lösung** Knoten, und überprüfen Sie **SignalRChat**.</span><span class="sxs-lookup"><span data-stu-id="7b99d-136">Right-click the **TestLibrary** project and select **Add**, **Reference...**. Select the **Projects** node under the **Solution** node, and check **SignalRChat**.</span></span> <span data-ttu-id="7b99d-137">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="7b99d-137">Click **OK**.</span></span>

    ![Projektverweis hinzufügen](unit-testing-signalr-applications/_static/image3.png)
6. <span data-ttu-id="7b99d-139">Fügen Sie die SignalR, Moq und XUnit-Pakete, die **Testbibliothek** Projekt.</span><span class="sxs-lookup"><span data-stu-id="7b99d-139">Add the SignalR, Moq, and XUnit packages to the **TestLibrary** project.</span></span> <span data-ttu-id="7b99d-140">In der **-Paket-Manager-Konsole**legen die **Standardprojekt** Dropdownliste aus, um **Testbibliothek**.</span><span class="sxs-lookup"><span data-stu-id="7b99d-140">In the **Package Manager Console**, set the **Default Project** dropdown to **TestLibrary**.</span></span> <span data-ttu-id="7b99d-141">Führen Sie die folgenden Befehle im Konsolenfenster angezeigt:</span><span class="sxs-lookup"><span data-stu-id="7b99d-141">Run the following commands in the console window:</span></span>

   - `Install-Package Microsoft.AspNet.SignalR`
   - `Install-Package Moq`
   - `Install-Package XUnit`

     ![Installieren von Paketen](unit-testing-signalr-applications/_static/image4.png)
7. <span data-ttu-id="7b99d-143">Erstellen Sie die Testdatei.</span><span class="sxs-lookup"><span data-stu-id="7b99d-143">Create the test file.</span></span> <span data-ttu-id="7b99d-144">Mit der rechten Maustaste die **Testbibliothek** Projekt, und klicken Sie auf **hinzufügen...** , **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="7b99d-144">Right-click the **TestLibrary** project and click **Add...**, **Class**.</span></span> <span data-ttu-id="7b99d-145">Nennen Sie die neue Klasse **Tests.cs**.</span><span class="sxs-lookup"><span data-stu-id="7b99d-145">Name the new class **Tests.cs**.</span></span>
8. <span data-ttu-id="7b99d-146">Ersetzen Sie den Inhalt der Tests.cs durch den folgenden Code ein.</span><span class="sxs-lookup"><span data-stu-id="7b99d-146">Replace the contents of Tests.cs with the following code.</span></span>

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample1.cs)]

    <span data-ttu-id="7b99d-147">Im obigen Code wird ein Testclient erstellt, mit der `Mock` -Objekt aus der [Moq](https://github.com/Moq/moq4) Bibliothek, die vom Typ [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (weisen Sie in SignalR 2.1 `dynamic` für den Typ die Parameter.) Die `IHubCallerConnectionContext` Schnittstelle ist das Proxyobjekt, mit denen Sie die Methoden auf dem Client aufrufen.</span><span class="sxs-lookup"><span data-stu-id="7b99d-147">In the code above, a test client is created using the `Mock` object from the [Moq](https://github.com/Moq/moq4) library, of type [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (in SignalR 2.1, assign `dynamic` for the type parameter.) The `IHubCallerConnectionContext` interface is the proxy object with which you invoke methods on the client.</span></span> <span data-ttu-id="7b99d-148">Die `broadcastMessage` Funktion ist dann für den simulierten Client definiert, damit sie aufgerufen werden kann, indem die `ChatHub` Klasse.</span><span class="sxs-lookup"><span data-stu-id="7b99d-148">The `broadcastMessage` function is then defined for the mock client so that it can be called by the `ChatHub` class.</span></span> <span data-ttu-id="7b99d-149">Die Test-Engine ruft dann die `Send` Methode der `ChatHub` -Klasse, die wiederum die simulierte aufruft, `broadcastMessage` Funktion.</span><span class="sxs-lookup"><span data-stu-id="7b99d-149">The test engine then calls the `Send` method of the `ChatHub` class, which in turn calls the mocked `broadcastMessage` function.</span></span>
9. <span data-ttu-id="7b99d-150">Erstellen Sie die Projektmappe durch Drücken von **F6**.</span><span class="sxs-lookup"><span data-stu-id="7b99d-150">Build the solution by pressing **F6**.</span></span>
10. <span data-ttu-id="7b99d-151">Führen Sie den Komponententest aus.</span><span class="sxs-lookup"><span data-stu-id="7b99d-151">Run the unit test.</span></span> <span data-ttu-id="7b99d-152">Wählen Sie in Visual Studio **Test**, **Windows**, **Test-Explorer**.</span><span class="sxs-lookup"><span data-stu-id="7b99d-152">In Visual Studio, select **Test**, **Windows**, **Test Explorer**.</span></span> <span data-ttu-id="7b99d-153">Im Fenster Test-Explorers mit der Maustaste **HubsAreMockableViaDynamic** , und wählen Sie **ausgewählte Tests ausführen**.</span><span class="sxs-lookup"><span data-stu-id="7b99d-153">In the Test Explorer window, right-click **HubsAreMockableViaDynamic** and select **Run Selected Tests**.</span></span>

    ![Test-Explorer](unit-testing-signalr-applications/_static/image5.png)
11. <span data-ttu-id="7b99d-155">Stellen Sie sicher, dass der Test erfolgreich war, anhand der im unteren Bereich im Test-Explorer-Fenster.</span><span class="sxs-lookup"><span data-stu-id="7b99d-155">Verify that the test passed by checking the lower pane in the Test Explorer window.</span></span> <span data-ttu-id="7b99d-156">Das Fenster wird angezeigt, das erfolgreiche Ergebnis.</span><span class="sxs-lookup"><span data-stu-id="7b99d-156">The window will show that the test passed.</span></span>

    ![Bestandene Tests](unit-testing-signalr-applications/_static/image6.png)

<a id="type"></a>
### <a name="unit-testing-by-type"></a><span data-ttu-id="7b99d-158">Komponententests, die nach Typ</span><span class="sxs-lookup"><span data-stu-id="7b99d-158">Unit testing by type</span></span>

<span data-ttu-id="7b99d-159">In diesem Abschnitt fügen Sie einen Test für die Anwendung erstellt, der [Tutorials: Erste Schritte](../getting-started/tutorial-getting-started-with-signalr.md) mithilfe einer Schnittstelle, die die Methode, die getestet werden, enthält.</span><span class="sxs-lookup"><span data-stu-id="7b99d-159">In this section, you'll add a test for the application created in the [Getting Started tutorial](../getting-started/tutorial-getting-started-with-signalr.md) using an interface that contains the method to be tested.</span></span>

1. <span data-ttu-id="7b99d-160">Führen Sie die Schritte 1 bis 7 in der [Unit testing mit dynamischen](#dynamic) oben genannten Tutorial.</span><span class="sxs-lookup"><span data-stu-id="7b99d-160">Complete steps 1-7 in the [Unit testing with Dynamic](#dynamic) tutorial above.</span></span>
2. <span data-ttu-id="7b99d-161">Ersetzen Sie den Inhalt der Tests.cs durch den folgenden Code ein.</span><span class="sxs-lookup"><span data-stu-id="7b99d-161">Replace the contents of Tests.cs with the following code.</span></span>

    [!code-csharp[Main](unit-testing-signalr-applications/samples/sample2.cs)]

    <span data-ttu-id="7b99d-162">Im obigen Code wird eine Schnittstelle erstellt, die Signatur der Definition der `broadcastMessage` Methode, die für die die Test-Engine einen mock-Client erstellen.</span><span class="sxs-lookup"><span data-stu-id="7b99d-162">In the code above, an interface is created defining the signature of the `broadcastMessage` method for which the test engine will create a mock client.</span></span> <span data-ttu-id="7b99d-163">Ein mock Client erstellt dann mithilfe der `Mock` Objekt des Typs [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (weisen Sie in SignalR 2.1 `dynamic` für den Parameter.) Die `IHubCallerConnectionContext` Schnittstelle ist das Proxyobjekt, mit denen Sie die Methoden auf dem Client aufrufen.</span><span class="sxs-lookup"><span data-stu-id="7b99d-163">A mock client is then created using the `Mock` object, of type [IHubCallerConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.ihubcallerconnectioncontext(v=vs.118).aspx) (in SignalR 2.1, assign `dynamic` for the type parameter.) The `IHubCallerConnectionContext` interface is the proxy object with which you invoke methods on the client.</span></span>

    <span data-ttu-id="7b99d-164">Der Test erstellt dann eine Instanz des `ChatHub`, und erstellt dann eine Pseudoversion des der `broadcastMessage` -Methode, die wiederum, durch den Aufruf aufgerufen wird der `Send` Methode auf dem Hub.</span><span class="sxs-lookup"><span data-stu-id="7b99d-164">The test then creates an instance of `ChatHub`, and then creates a mock version of the `broadcastMessage` method, which in turn is invoked by calling the `Send` method on the hub.</span></span>
3. <span data-ttu-id="7b99d-165">Erstellen Sie die Projektmappe durch Drücken von **F6**.</span><span class="sxs-lookup"><span data-stu-id="7b99d-165">Build the solution by pressing **F6**.</span></span>
4. <span data-ttu-id="7b99d-166">Führen Sie den Komponententest aus.</span><span class="sxs-lookup"><span data-stu-id="7b99d-166">Run the unit test.</span></span> <span data-ttu-id="7b99d-167">Wählen Sie in Visual Studio **Test**, **Windows**, **Test-Explorer**.</span><span class="sxs-lookup"><span data-stu-id="7b99d-167">In Visual Studio, select **Test**, **Windows**, **Test Explorer**.</span></span> <span data-ttu-id="7b99d-168">Im Fenster Test-Explorers mit der Maustaste **HubsAreMockableViaDynamic** , und wählen Sie **ausgewählte Tests ausführen**.</span><span class="sxs-lookup"><span data-stu-id="7b99d-168">In the Test Explorer window, right-click **HubsAreMockableViaDynamic** and select **Run Selected Tests**.</span></span>

    ![Test-Explorer](unit-testing-signalr-applications/_static/image7.png)
5. <span data-ttu-id="7b99d-170">Stellen Sie sicher, dass der Test erfolgreich war, anhand der im unteren Bereich im Test-Explorer-Fenster.</span><span class="sxs-lookup"><span data-stu-id="7b99d-170">Verify that the test passed by checking the lower pane in the Test Explorer window.</span></span> <span data-ttu-id="7b99d-171">Das Fenster wird angezeigt, das erfolgreiche Ergebnis.</span><span class="sxs-lookup"><span data-stu-id="7b99d-171">The window will show that the test passed.</span></span>

    ![Bestandene Tests](unit-testing-signalr-applications/_static/image8.png)
