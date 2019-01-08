---
uid: signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
title: 'Tutorial: Serverübertragung mit SignalR 2 | Microsoft-Dokumentation'
author: tdykstra
description: Dieses Tutorial veranschaulicht, wie eine Webanwendung erstellen, die ASP.NET SignalR 2 verwendet, um Server-broadcast-Funktionalität bereitzustellen.
ms.author: riande
ms.date: 01/02/2019
ms.topic: tutorial
ms.assetid: 1568247f-60b5-4eca-96e0-e661fbb2b273
msc.legacyurl: /signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: a6014e604613492db91b2dc6f846c3c73d938d99
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099298"
---
# <a name="tutorial-server-broadcast-with-signalr-2"></a><span data-ttu-id="6144f-103">Tutorial: Server senden, die mit SignalR 2</span><span class="sxs-lookup"><span data-stu-id="6144f-103">Tutorial: Server broadcast with SignalR 2</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="6144f-104">Dieses Tutorial veranschaulicht, wie eine Webanwendung erstellen, die ASP.NET SignalR 2 verwendet, um Server-broadcast-Funktionalität bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="6144f-104">This tutorial shows how to create a web application that uses ASP.NET SignalR 2 to provide server broadcast functionality.</span></span> <span data-ttu-id="6144f-105">Serverübertragung bedeutet, dass der Server die Kommunikation an Clients gesendeten gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="6144f-105">Server broadcast means that the server starts the communications sent to clients.</span></span>

<span data-ttu-id="6144f-106">Die Anwendung, die Sie in diesem Tutorial erstellen simuliert einen Börsenticker erstellen, ein typisches Szenario für die Funktion zum Server senden.</span><span class="sxs-lookup"><span data-stu-id="6144f-106">The application that you'll create in this tutorial simulates a stock ticker, a typical scenario for server broadcast functionality.</span></span> <span data-ttu-id="6144f-107">Der Server wird in regelmäßigen Abständen nach dem Zufallsprinzip Aktienkurse aktualisiert, und die Updates an alle verbundenen Clients übertragen.</span><span class="sxs-lookup"><span data-stu-id="6144f-107">Periodically, the server randomly updates stock prices and broadcast the updates to all connected clients.</span></span> <span data-ttu-id="6144f-108">In den Browser, der Zahlen und Symbole in der **ändern** und **%** Spalten dynamisch zu ändern, als Reaktion auf Benachrichtigungen vom Server.</span><span class="sxs-lookup"><span data-stu-id="6144f-108">In the browser, the numbers and symbols in the **Change** and **%** columns dynamically change in response to notifications from the server.</span></span> <span data-ttu-id="6144f-109">Wenn Sie weitere Browser auf die gleiche URL öffnen, werden sie alle die gleichen Daten und die gleichen Änderungen auf die Daten gleichzeitig.</span><span class="sxs-lookup"><span data-stu-id="6144f-109">If you open additional browsers to the same URL, they all show the same data and the same changes to the data simultaneously.</span></span>

![Erstellen Sie web](tutorial-server-broadcast-with-signalr/_static/image1.png)

<span data-ttu-id="6144f-111">In diesem Tutorial haben Sie:</span><span class="sxs-lookup"><span data-stu-id="6144f-111">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6144f-112">Erstellen eines Projekts</span><span class="sxs-lookup"><span data-stu-id="6144f-112">Create the project</span></span>
> * <span data-ttu-id="6144f-113">Richten Sie den Server-code</span><span class="sxs-lookup"><span data-stu-id="6144f-113">Set up the server code</span></span>
> * <span data-ttu-id="6144f-114">Überprüfen Sie den Servercode</span><span class="sxs-lookup"><span data-stu-id="6144f-114">Examine the server code</span></span>
> * <span data-ttu-id="6144f-115">Richten Sie den Clientcode</span><span class="sxs-lookup"><span data-stu-id="6144f-115">Set up the client code</span></span>
> * <span data-ttu-id="6144f-116">Überprüfen Sie den Clientcode</span><span class="sxs-lookup"><span data-stu-id="6144f-116">Examine the client code</span></span>
> * <span data-ttu-id="6144f-117">Testen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="6144f-117">Test the application</span></span>
> * <span data-ttu-id="6144f-118">Protokollierung aktivieren</span><span class="sxs-lookup"><span data-stu-id="6144f-118">Enable logging</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6144f-119">Wenn Sie nicht über die Schritte zum Erstellen der Anwendung arbeiten möchten, können Sie das Paket SignalR.Sample in einem neuen leeren ASP.NET Web Application-Projekt installieren.</span><span class="sxs-lookup"><span data-stu-id="6144f-119">If you don't want to work through the steps of building the application, you can install the SignalR.Sample package in a new Empty ASP.NET Web Application project.</span></span> <span data-ttu-id="6144f-120">Wenn Sie das NuGet-Paket installieren, ohne die Schritte in diesem Tutorial ausführen, müssen Sie die Anweisungen unter Befolgen der *"Readme.txt"* Datei.</span><span class="sxs-lookup"><span data-stu-id="6144f-120">If you install the NuGet package without performing the steps in this tutorial, you must follow the instructions in the *readme.txt* file.</span></span> <span data-ttu-id="6144f-121">Zum Ausführen des Pakets müssen Sie eine OWIN-Startup hinzufügen wird Klasse die `ConfigureSignalR` -Methode in der das installierte Paket.</span><span class="sxs-lookup"><span data-stu-id="6144f-121">To run the package you need to add an OWIN startup class which calls the `ConfigureSignalR` method in the installed package.</span></span> <span data-ttu-id="6144f-122">Sie erhalten einen Fehler, wenn Sie nicht die OWIN-Startklasse hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="6144f-122">You will receive an error if you do not add the OWIN startup class.</span></span> <span data-ttu-id="6144f-123">Finden Sie unter den [Installieren des Beispiels StockTicker](#install-the-stockticker-sample) Abschnitt dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="6144f-123">See the [Install the StockTicker sample](#install-the-stockticker-sample) section of this article.</span></span>


## <a name="prerequisites"></a><span data-ttu-id="6144f-124">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="6144f-124">Prerequisites</span></span>

 * <span data-ttu-id="6144f-125">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) mit der **ASP.NET und Webentwicklung** arbeitsauslastung.</span><span class="sxs-lookup"><span data-stu-id="6144f-125">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="6144f-126">Erstellen eines Projekts</span><span class="sxs-lookup"><span data-stu-id="6144f-126">Create the project</span></span>

<span data-ttu-id="6144f-127">In diesem Abschnitt veranschaulicht, wie Visual Studio 2017 verwenden, um eine leere ASP.NET-Webanwendung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6144f-127">This section shows how to use Visual Studio 2017 to create an empty ASP.NET Web Application.</span></span>

1. <span data-ttu-id="6144f-128">Erstellen Sie eine ASP.NET-Webanwendung in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6144f-128">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![Erstellen Sie web](tutorial-server-broadcast-with-signalr/_static/image2.png)

1. <span data-ttu-id="6144f-130">In der **neue ASP.NET-Webanwendung – SignalR.StockTicker** Fenster verlassen **leere** ausgewählt, und wählen Sie **OK**.</span><span class="sxs-lookup"><span data-stu-id="6144f-130">In the **New ASP.NET Web Application - SignalR.StockTicker** window, leave **Empty** selected and select **OK**.</span></span>

## <a name="set-up-the-server-code"></a><span data-ttu-id="6144f-131">Richten Sie den Server-code</span><span class="sxs-lookup"><span data-stu-id="6144f-131">Set up the server code</span></span>

<span data-ttu-id="6144f-132">In diesem Abschnitt richten Sie den Code, der auf dem Server ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6144f-132">In this section, you set up the code that runs on the server.</span></span>

### <a name="create-the-stock-class"></a><span data-ttu-id="6144f-133">Erstellen Sie die Stock-Klasse</span><span class="sxs-lookup"><span data-stu-id="6144f-133">Create the Stock class</span></span>

<span data-ttu-id="6144f-134">Zunächst erstellen Sie die *Stock* Modellklasse, die Sie verwenden zum Speichern und Übertragen von Informationen zu einer Aktie.</span><span class="sxs-lookup"><span data-stu-id="6144f-134">You begin by creating the *Stock* model class that you'll use to store and transmit information about a stock.</span></span>

1. <span data-ttu-id="6144f-135">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="6144f-135">In **Solution Explorer**, right-click the project and select **Add** > **Class**.</span></span>

1. <span data-ttu-id="6144f-136">Nennen Sie die Klasse *Stock* und fügen sie dem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="6144f-136">Name the class *Stock* and add it to the project.</span></span>

1. <span data-ttu-id="6144f-137">Ersetzen Sie den Code in die *Stock.cs* Datei durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="6144f-137">Replace the code in the *Stock.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample1.cs)]

    <span data-ttu-id="6144f-138">Die beiden Eigenschaften, die Sie bei der Erstellung von Aktien festlegen `Symbol` (z. B. "MSFT" bei Microsoft) und `Price`.</span><span class="sxs-lookup"><span data-stu-id="6144f-138">The two properties that you'll set when you create stocks are `Symbol` (for example, MSFT for Microsoft) and `Price`.</span></span> <span data-ttu-id="6144f-139">Hängen Sie, dass die anderen Eigenschaften wie und wann Sie festlegen, `Price`.</span><span class="sxs-lookup"><span data-stu-id="6144f-139">The other properties depend on how and when you set `Price`.</span></span> <span data-ttu-id="6144f-140">Beim ersten festlegen `Price`, ruft der Wert an weitergegeben `DayOpen`.</span><span class="sxs-lookup"><span data-stu-id="6144f-140">The first time you set `Price`, the value gets propagated to `DayOpen`.</span></span> <span data-ttu-id="6144f-141">Danach festlegen `Price`, berechnet die app die `Change` und `PercentChange` Eigenschaftswerte auf Grundlage der Unterschied zwischen `Price` und `DayOpen`.</span><span class="sxs-lookup"><span data-stu-id="6144f-141">After that, when you set `Price`, the app calculates the `Change` and `PercentChange` property values based on the difference between `Price` and `DayOpen`.</span></span>

### <a name="create-the-stocktickerhub-and-stockticker-classes"></a><span data-ttu-id="6144f-142">Erstellen Sie die StockTickerHub und StockTicker-Klasse</span><span class="sxs-lookup"><span data-stu-id="6144f-142">Create the StockTickerHub and StockTicker classes</span></span>

<span data-ttu-id="6144f-143">Sie verwenden die SignalR-Hub-API, um die Server-zu-Client-Interaktion.</span><span class="sxs-lookup"><span data-stu-id="6144f-143">You'll use the SignalR Hub API to handle server-to-client interaction.</span></span> <span data-ttu-id="6144f-144">Ein `StockTickerHub` abgeleitete Klasse die `SignalRHub` Klasse übernimmt die Verbindungen und Methodenaufrufen von Clients empfangen.</span><span class="sxs-lookup"><span data-stu-id="6144f-144">A `StockTickerHub` class that derives from the `SignalRHub` class will handle receiving connections and method calls from clients.</span></span> <span data-ttu-id="6144f-145">Sie müssen auch vordefinierte Daten beizubehalten, und führen Sie eine `Timer` Objekt.</span><span class="sxs-lookup"><span data-stu-id="6144f-145">You also need to maintain stock data and run a `Timer` object.</span></span> <span data-ttu-id="6144f-146">Die `Timer` Objekt löst in regelmäßigen Abständen preisaktualisierungen unabhängig von Clientverbindungen.</span><span class="sxs-lookup"><span data-stu-id="6144f-146">The `Timer` object will periodically trigger price updates independent of client connections.</span></span> <span data-ttu-id="6144f-147">Diese Funktionen können nicht eingefügt werden, einem `Hub` Klasse, da Hubs vorübergehend sind.</span><span class="sxs-lookup"><span data-stu-id="6144f-147">You can't put these functions in a `Hub` class, because Hubs are transient.</span></span> <span data-ttu-id="6144f-148">Die app erstellt ein `Hub` Klasseninstanz für jeden Vorgang im Hub wie Verbindungen und Aufrufe vom Client an den Server.</span><span class="sxs-lookup"><span data-stu-id="6144f-148">The app creates a `Hub` class instance for each task on the hub, like connections and calls from the client to the server.</span></span> <span data-ttu-id="6144f-149">Daher ist der Mechanismus, der aktualisiert Preise, bleiben die vorhandene Daten und überträgt die preisaktualisierungen in einer separaten Klasse ausführen.</span><span class="sxs-lookup"><span data-stu-id="6144f-149">So the mechanism that keeps stock data, updates prices, and broadcasts the price updates has to run in a separate class.</span></span> <span data-ttu-id="6144f-150">Sie werden benennen Sie die Klasse `StockTicker`.</span><span class="sxs-lookup"><span data-stu-id="6144f-150">You'll name the class `StockTicker`.</span></span>

![Übertragungen von StockTicker](tutorial-server-broadcast-with-signalr/_static/image3.png)

<span data-ttu-id="6144f-152">Sollen nur eine Instanz der `StockTicker` Klasse, die auf dem Server ausgeführt wird, daher Sie einen Verweis aus jedem einrichten müssen `StockTickerHub` Instanz auf das Singleton `StockTicker` Instanz.</span><span class="sxs-lookup"><span data-stu-id="6144f-152">You only want one instance of the `StockTicker` class to run on the server, so you'll need to set up a reference from each `StockTickerHub` instance to the singleton `StockTicker` instance.</span></span> <span data-ttu-id="6144f-153">Die `StockTicker` -Klasse verfügt über an Clients übertragen werden, da es die vorhandenen Daten und Updates, löst aber `StockTicker` wird keine `Hub` Klasse.</span><span class="sxs-lookup"><span data-stu-id="6144f-153">The `StockTicker` class has to broadcast to clients because it has the stock data and triggers updates, but `StockTicker` isn't a `Hub` class.</span></span> <span data-ttu-id="6144f-154">Die `StockTicker` -Klasse verfügt über einen Verweis auf das Kontextobjekt für SignalR-Hub-Verbindung abrufen.</span><span class="sxs-lookup"><span data-stu-id="6144f-154">The `StockTicker` class has to get a reference to the SignalR Hub connection context object.</span></span> <span data-ttu-id="6144f-155">Sie können dann das Kontextobjekt für SignalR-Verbindung verwenden, an Clients senden.</span><span class="sxs-lookup"><span data-stu-id="6144f-155">It can then use the SignalR connection context object to broadcast to clients.</span></span>

#### <a name="create-stocktickerhubcs"></a><span data-ttu-id="6144f-156">Erstellen von StockTickerHub.cs</span><span class="sxs-lookup"><span data-stu-id="6144f-156">Create StockTickerHub.cs</span></span>

1. <span data-ttu-id="6144f-157">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.</span><span class="sxs-lookup"><span data-stu-id="6144f-157">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="6144f-158">In **neues Element hinzufügen - SignalR.StockTicker**Option **installiert** > **Visual C#**   >  **Web**  >  **SignalR** und wählen Sie dann **SignalR-Hubklasse (v2)**.</span><span class="sxs-lookup"><span data-stu-id="6144f-158">In **Add New Item - SignalR.StockTicker**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="6144f-159">Nennen Sie die Klasse *StockTickerHub* und fügen sie dem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="6144f-159">Name the class *StockTickerHub* and add it to the project.</span></span>

    <span data-ttu-id="6144f-160">Dieser Schritt erstellt der *StockTickerHub.cs* Klassendatei.</span><span class="sxs-lookup"><span data-stu-id="6144f-160">This step creates the *StockTickerHub.cs* class file.</span></span> <span data-ttu-id="6144f-161">Gleichzeitig wird eine Reihe von Skriptdateien und Assemblyverweise, die von SignalR zum Projekt unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6144f-161">Simultaneously, it adds a set of script files and assembly references that supports SignalR to the project.</span></span>

1. <span data-ttu-id="6144f-162">Ersetzen Sie den Code in die *StockTickerHub.cs* Datei durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="6144f-162">Replace the code in the *StockTickerHub.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample2.cs)]

1. <span data-ttu-id="6144f-163">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="6144f-163">Save the file.</span></span>

<span data-ttu-id="6144f-164">Die app verwendet die [Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) Klasse, um Methoden zu definieren, die Clients auf dem Server aufrufen können.</span><span class="sxs-lookup"><span data-stu-id="6144f-164">The app uses the [Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) class to define methods the clients can call on the server.</span></span> <span data-ttu-id="6144f-165">Definieren Sie eine Methode: `GetAllStocks()`.</span><span class="sxs-lookup"><span data-stu-id="6144f-165">You're defining one method: `GetAllStocks()`.</span></span> <span data-ttu-id="6144f-166">Wenn ein Client zunächst mit dem Server verbunden ist, nenne sie diese Methode, um eine Liste aller die Aktien mit ihren aktuellen Preisen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="6144f-166">When a client initially connects to the server, it will call this method to get a list of all of the stocks with their current prices.</span></span> <span data-ttu-id="6144f-167">Die Methode synchron ausgeführt und zurückgeben kann `IEnumerable<Stock>` , da sie Daten aus dem Arbeitsspeicher zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="6144f-167">The method can run synchronously and return `IEnumerable<Stock>` because it's returning data from memory.</span></span>

<span data-ttu-id="6144f-168">Wenn die Methode die Daten abrufen, indem Sie die Aktionen ausgeführt werden, die dabei warten, z. B. einer Datenbanksuche oder einen Aufruf des Webdiensts, würden Sie angeben `Task<IEnumerable<Stock>>` als den Rückgabewert in die asynchrone Verarbeitung zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="6144f-168">If the method had to get the data by doing something that would involve waiting, like a database lookup or a web service call, you would specify `Task<IEnumerable<Stock>>` as the return value to enable asynchronous processing.</span></span> <span data-ttu-id="6144f-169">Weitere Informationen finden Sie unter [für die ASP.NET SignalR-Hubs-API - Server - beim asynchron ausführen](../guide-to-the-api/hubs-api-guide-server.md#asyncmethods).</span><span class="sxs-lookup"><span data-stu-id="6144f-169">For more information, see [ASP.NET SignalR Hubs API Guide - Server - When to execute asynchronously](../guide-to-the-api/hubs-api-guide-server.md#asyncmethods).</span></span>

<span data-ttu-id="6144f-170">Die `HubName` Attribut gibt an, wie die app Hub im JavaScript-Code auf dem Client verweist.</span><span class="sxs-lookup"><span data-stu-id="6144f-170">The `HubName` attribute specifies how the app will reference the Hub in JavaScript code on the client.</span></span> <span data-ttu-id="6144f-171">Der Standardname auf dem Client, wenn Sie dieses Attribut nicht verwenden, ist Sie eine CamelCase-Version des Klassennamens, der in diesem Fall würden `stockTickerHub`.</span><span class="sxs-lookup"><span data-stu-id="6144f-171">The default name on the client if you don't use this attribute, is a camelCase version of the class name, which in this case would be `stockTickerHub`.</span></span>

<span data-ttu-id="6144f-172">Wie Sie später sehen werden bei der Erstellung der `StockTicker` -Klasse, die app erstellt eine Singleton-Instanz dieser Klasse in die statische `Instance` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="6144f-172">As you'll see later when you create the `StockTicker` class, the app creates a singleton instance of that class in its static `Instance` property.</span></span> <span data-ttu-id="6144f-173">Diese Singletoninstanz des `StockTicker` befindet sich im Speicher, unabhängig davon, wie viele Clients eine Verbindung herstellen oder trennen.</span><span class="sxs-lookup"><span data-stu-id="6144f-173">That singleton instance of `StockTicker` is in memory no matter how many clients connect or disconnect.</span></span> <span data-ttu-id="6144f-174">Diese Instanz ist, was die `GetAllStocks()` Methode verwendet, um die aktuellen vordefinierten Informationen zurückgegeben werden sollen.</span><span class="sxs-lookup"><span data-stu-id="6144f-174">That instance is what the `GetAllStocks()` method uses to return current stock information.</span></span>

#### <a name="create-stocktickercs"></a><span data-ttu-id="6144f-175">Erstellen von StockTicker.cs</span><span class="sxs-lookup"><span data-stu-id="6144f-175">Create StockTicker.cs</span></span>

1. <span data-ttu-id="6144f-176">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="6144f-176">In **Solution Explorer**, right-click the project and select **Add** > **Class**.</span></span>

1. <span data-ttu-id="6144f-177">Nennen Sie die Klasse *StockTicker* und fügen sie dem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="6144f-177">Name the class *StockTicker* and add it to the project.</span></span>

1. <span data-ttu-id="6144f-178">Ersetzen Sie den Code in die *StockTicker.cs* Datei durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="6144f-178">Replace the code in the *StockTicker.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample3.cs)]

<span data-ttu-id="6144f-179">Da alle Threads dieselbe Instanz der StockTicker-Code ausgeführt werden, muss die StockTicker-Klasse threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="6144f-179">Since all threads will be running the same instance of StockTicker code, the StockTicker class has to be thread-safe.</span></span>

### <a name="examine-the-server-code"></a><span data-ttu-id="6144f-180">Überprüfen Sie den Servercode</span><span class="sxs-lookup"><span data-stu-id="6144f-180">Examine the server code</span></span>

<span data-ttu-id="6144f-181">Wenn Sie den Server-Code untersuchen, wird sie Ihnen das Verständnis der Funktionsweise der app helfen.</span><span class="sxs-lookup"><span data-stu-id="6144f-181">If you examine the server code, it will help you understand how the app works.</span></span>

#### <a name="storing-the-singleton-instance-in-a-static-field"></a><span data-ttu-id="6144f-182">Speichern die Singleton-Instanz in einem statischen Feld</span><span class="sxs-lookup"><span data-stu-id="6144f-182">Storing the singleton instance in a static field</span></span>

<span data-ttu-id="6144f-183">Der Code initialisiert die statische `_instance` Feld, das sichert die `Instance` Eigenschaft mit einer Instanz der Klasse.</span><span class="sxs-lookup"><span data-stu-id="6144f-183">The code initializes the static `_instance` field that backs the `Instance` property with an instance of the class.</span></span> <span data-ttu-id="6144f-184">Da der Konstruktor privat ist, ist es die einzige Instanz der Klasse, die die app erstellen kann.</span><span class="sxs-lookup"><span data-stu-id="6144f-184">Because the constructor is private, it's the only instance of the class that the app can create.</span></span> <span data-ttu-id="6144f-185">Die app verwendet [verzögerte Initialisierung](/dotnet/framework/performance/lazy-initialization) für die `_instance` Feld.</span><span class="sxs-lookup"><span data-stu-id="6144f-185">The app uses [Lazy initialization](/dotnet/framework/performance/lazy-initialization) for the `_instance` field.</span></span> <span data-ttu-id="6144f-186">Es ist nicht zur Verbesserung der Leistung.</span><span class="sxs-lookup"><span data-stu-id="6144f-186">It's not for performance reasons.</span></span> <span data-ttu-id="6144f-187">Es ist, stellen Sie sicher, dass die Erstellung der Instanz threadsicher ist.</span><span class="sxs-lookup"><span data-stu-id="6144f-187">It's to make sure the instance creation is thread-safe.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample4.cs)]

<span data-ttu-id="6144f-188">Jedes Mal, die ein Client eine mit dem Server Verbindung eine neue Instanz der Ausführung in einem separaten Thread StockTickerHub-Klasse ruft die Singleton-Instanz besteht aus den `StockTicker.Instance` statische Eigenschaft ist, wie Sie gesehen haben werden weiter oben in der `StockTickerHub` Klasse.</span><span class="sxs-lookup"><span data-stu-id="6144f-188">Each time a client connects to the server, a new instance of the StockTickerHub class running in a separate thread gets the StockTicker singleton instance from the `StockTicker.Instance` static property, as you saw earlier in the `StockTickerHub` class.</span></span>

#### <a name="storing-stock-data-in-a-concurrentdictionary"></a><span data-ttu-id="6144f-189">Das Speichern von vorhandenen Daten in einem ConcurrentDictionary</span><span class="sxs-lookup"><span data-stu-id="6144f-189">Storing stock data in a ConcurrentDictionary</span></span>

<span data-ttu-id="6144f-190">Der Konstruktor initialisiert die `_stocks` Auflistung mit einigen vordefinierten Beispieldaten und `GetAllStocks` gibt die Aktien.</span><span class="sxs-lookup"><span data-stu-id="6144f-190">The constructor initializes the `_stocks` collection with some sample stock data, and `GetAllStocks` returns the stocks.</span></span> <span data-ttu-id="6144f-191">Wie Sie gesehen haben, wird diese Sammlung von Daten durch zurückgegeben `StockTickerHub.GetAllStocks`, dies ist eine Servermethode in der `Hub` -Klasse, die Clients aufrufen können.</span><span class="sxs-lookup"><span data-stu-id="6144f-191">As you saw earlier, this collection of stocks is returned by `StockTickerHub.GetAllStocks`, which is a server method in the `Hub` class that clients can call.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample5.cs)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample6.cs)]

<span data-ttu-id="6144f-192">Die Aktien-Sammlung ist definiert als eine [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) Typ für die Threadsicherheit.</span><span class="sxs-lookup"><span data-stu-id="6144f-192">The stocks collection is defined as a [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) type for thread safety.</span></span> <span data-ttu-id="6144f-193">Als Alternative können Sie eine [Wörterbuch](https://msdn.microsoft.com/library/xfhwa508.aspx) -Objekt und das Wörterbuch explizit sperren, wenn Sie Änderungen vornehmen.</span><span class="sxs-lookup"><span data-stu-id="6144f-193">As an alternative, you could use a [Dictionary](https://msdn.microsoft.com/library/xfhwa508.aspx) object and explicitly lock the dictionary when you make changes to it.</span></span>

<span data-ttu-id="6144f-194">Für diese beispielanwendung ist es gut zum Speichern von Anwendungsdaten im Arbeitsspeicher und Daten zu verlieren, wenn die app freigibt, die `StockTicker` Instanz.</span><span class="sxs-lookup"><span data-stu-id="6144f-194">For this sample application, it's OK to store application data in memory and to lose the data when the app disposes of the  `StockTicker` instance.</span></span> <span data-ttu-id="6144f-195">In einer echten Anwendung würden Sie mit einem Back-End-Datenspeicher, z. B. eine Datenbank arbeiten.</span><span class="sxs-lookup"><span data-stu-id="6144f-195">In a real application, you would work with a back-end data store like a database.</span></span>

#### <a name="periodically-updating-stock-prices"></a><span data-ttu-id="6144f-196">Aktienkurse wird in regelmäßigen Abständen aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="6144f-196">Periodically updating stock prices</span></span>

<span data-ttu-id="6144f-197">Der Konstruktor gestartet wird, wird eine `Timer` -Objekt, das in regelmäßigen Abständen Methoden aufruft, die Aktienkurse nach dem Zufallsprinzip zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="6144f-197">The constructor starts up a `Timer` object that periodically calls methods that update stock prices on a random basis.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample7.cs)]

 <span data-ttu-id="6144f-198">`Timer` Aufrufe `UpdateStockPrices`, die NULL-Wert in den State-Parameter übergibt.</span><span class="sxs-lookup"><span data-stu-id="6144f-198">`Timer` calls `UpdateStockPrices`, which passes in null in the state parameter.</span></span> <span data-ttu-id="6144f-199">Bevor Sie aktualisieren die Preise, akzeptiert die app eine Sperre für die `_updateStockPricesLock` Objekt.</span><span class="sxs-lookup"><span data-stu-id="6144f-199">Before updating prices, the app takes a lock on the `_updateStockPricesLock` object.</span></span> <span data-ttu-id="6144f-200">Der Code überprüft, ob ein anderer Thread wird bereits Preise aktualisiert, und klicken Sie dann ruft `TryUpdateStockPrice` für jede Aktie in der Liste.</span><span class="sxs-lookup"><span data-stu-id="6144f-200">The code checks if another thread is already updating prices, and then it calls `TryUpdateStockPrice` on each stock in the list.</span></span> <span data-ttu-id="6144f-201">Die `TryUpdateStockPrice` Methode entschieden, ob den Aktienkurs zu ändern und wie viel, um ihn zu ändern.</span><span class="sxs-lookup"><span data-stu-id="6144f-201">The `TryUpdateStockPrice` method decides whether to change the stock price, and how much to change it.</span></span> <span data-ttu-id="6144f-202">Wenn der Aktienkurs geändert wird, wird die app ruft `BroadcastStockPrice` senden, die die Änderung der Aktienkurs für alle verbundenen Clients.</span><span class="sxs-lookup"><span data-stu-id="6144f-202">If the stock price changes, the app calls `BroadcastStockPrice` to broadcast the stock price change to all connected clients.</span></span>

<span data-ttu-id="6144f-203">Die `_updatingStockPrices` Flag festgelegt [flüchtige](https://msdn.microsoft.com/library/x13ttww7.aspx) um sicherzustellen, dass es die Thread-sicher ist.</span><span class="sxs-lookup"><span data-stu-id="6144f-203">The `_updatingStockPrices` flag designated [volatile](https://msdn.microsoft.com/library/x13ttww7.aspx) to make sure it is thread-safe.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample8.cs)]

<span data-ttu-id="6144f-204">In einer realen Anwendung die `TryUpdateStockPrice` Methodenaufruf einen Webdienst, um den Preis zu suchen.</span><span class="sxs-lookup"><span data-stu-id="6144f-204">In a real application, the `TryUpdateStockPrice` method would call a web service to look up the price.</span></span> <span data-ttu-id="6144f-205">In diesem Code verwendet die app eine Zufallszahlen-Generators nach dem Zufallsprinzip ändern.</span><span class="sxs-lookup"><span data-stu-id="6144f-205">In this code, the app uses a random number generator to make changes randomly.</span></span>

#### <a name="getting-the-signalr-context-so-that-the-stockticker-class-can-broadcast-to-clients"></a><span data-ttu-id="6144f-206">Abrufen von SignalR Kontext, damit die StockTicker-Klasse, die an Clients übertragen können</span><span class="sxs-lookup"><span data-stu-id="6144f-206">Getting the SignalR context so that the StockTicker class can broadcast to clients</span></span>

<span data-ttu-id="6144f-207">Da die Preise variieren hier aus kommen die `StockTicker` Objekt, es ist das Objekt, das aufgerufen werden muss ein `updateStockPrice` Methode für alle verbundenen Clients.</span><span class="sxs-lookup"><span data-stu-id="6144f-207">Because the price changes originate here in the `StockTicker` object, it's the object that needs to call an `updateStockPrice` method on all connected clients.</span></span> <span data-ttu-id="6144f-208">In einer `Hub` -Klasse haben Sie eine API zum Aufrufen von Clientmethoden, aber `StockTicker` nicht abgeleitet der `Hub` Klasse und verfügt nicht über einen Verweis auf einen `Hub` Objekt.</span><span class="sxs-lookup"><span data-stu-id="6144f-208">In a `Hub` class, you have an API for calling client methods, but `StockTicker` doesn't derive from the `Hub` class and doesn't have a reference to any `Hub` object.</span></span> <span data-ttu-id="6144f-209">Übertragung für verbundene Clients, die `StockTicker` -Klasse, die zum Abrufen der Instanz des SignalR-Kontext für verfügt über die `StockTickerHub` Klasse, und verwenden, die zum Aufrufen von Methoden auf Clients.</span><span class="sxs-lookup"><span data-stu-id="6144f-209">To broadcast to connected clients, the `StockTicker` class has to get the SignalR context instance for the `StockTickerHub` class and use that to call methods on clients.</span></span>

<span data-ttu-id="6144f-210">Der Code Ruft einen Verweis auf den SignalR-Kontext ab, wenn es erstellt die Klasse Singletoninstanz übergibt, die auf verweisen an den Konstruktor und der Konstruktor fügt sie in der `Clients` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="6144f-210">The code gets a reference to the SignalR context when it creates the singleton class instance, passes that reference to the constructor, and the constructor puts it in the `Clients` property.</span></span>

<span data-ttu-id="6144f-211">Es gibt zwei Gründe, warum den Kontext nur einmal abgerufen werden soll: Abrufen des Kontexts wird als teuer, und es abrufen, sobald sorgt dafür, dass die app die vorgesehene Reihenfolge der Nachrichten an die Clients beibehält.</span><span class="sxs-lookup"><span data-stu-id="6144f-211">There are two reasons why you want to get the context only once: getting the context is an expensive task, and getting it once makes sure the app preserves the intended order of messages sent to the clients.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample9.cs)]

<span data-ttu-id="6144f-212">Abrufen der `Clients` Eigenschaft des Kontexts und der Implementierung der `StockTickerClient` Eigenschaft können Sie Code zum Client Aufrufen von Methoden schreiben, der genauso aussieht wie in einer `Hub` Klasse.</span><span class="sxs-lookup"><span data-stu-id="6144f-212">Getting the `Clients` property of the context and putting it in the `StockTickerClient` property lets you write code to call client methods that looks the same as it would in a `Hub` class.</span></span> <span data-ttu-id="6144f-213">Z. B. für alle Clients, die Sie senden können schreiben `Clients.All.updateStockPrice(stock)`.</span><span class="sxs-lookup"><span data-stu-id="6144f-213">For instance, to broadcast to all clients you can write `Clients.All.updateStockPrice(stock)`.</span></span>

<span data-ttu-id="6144f-214">Die `updateStockPrice` -Methode, die Sie in Aufrufen `BroadcastStockPrice` noch nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="6144f-214">The `updateStockPrice` method that you're calling in `BroadcastStockPrice` doesn't exist yet.</span></span> <span data-ttu-id="6144f-215">Sie werden ihn später hinzufügen, wenn Sie Code schreiben, die auf dem Client ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6144f-215">You'll add it later when you write code that runs on the client.</span></span> <span data-ttu-id="6144f-216">Sehen Sie sich `updateStockPrice` hier da `Clients.All` "Dynamic", d. h., die app wird den Ausdruck zur Laufzeit ausgewertet wird.</span><span class="sxs-lookup"><span data-stu-id="6144f-216">You can refer to `updateStockPrice` here because `Clients.All` is dynamic, which means the app will evaluate the expression at runtime.</span></span> <span data-ttu-id="6144f-217">Wenn dieser Methodenaufruf ausgeführt wird, SignalR wird der Name der Methode und der Wert des Parameters an den Client gesendet, und wenn der Client eine Methode namens hat `updateStockPrice`, die app diese Methode aufzurufen und der Wert des Parameters an es übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="6144f-217">When this method call executes, SignalR will send the method name and the parameter value to the client, and if the client has a method named `updateStockPrice`, the app will call that method and pass the parameter value to it.</span></span>

<span data-ttu-id="6144f-218">`Clients.All` bedeutet, dass für alle Clients senden.</span><span class="sxs-lookup"><span data-stu-id="6144f-218">`Clients.All` means send to all clients.</span></span> <span data-ttu-id="6144f-219">SignalR bietet Ihnen weitere Optionen an die Clients oder Gruppen von Clients zu senden.</span><span class="sxs-lookup"><span data-stu-id="6144f-219">SignalR gives you other options to specify which clients or groups of clients to send to.</span></span> <span data-ttu-id="6144f-220">Weitere Informationen finden Sie unter [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).</span><span class="sxs-lookup"><span data-stu-id="6144f-220">For more information, see [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).</span></span>

### <a name="register-the-signalr-route"></a><span data-ttu-id="6144f-221">Registrieren Sie sich die SignalR-route</span><span class="sxs-lookup"><span data-stu-id="6144f-221">Register the SignalR route</span></span>

<span data-ttu-id="6144f-222">Der Server muss wissen, welche URL zum Abfangen und direkt an SignalR.</span><span class="sxs-lookup"><span data-stu-id="6144f-222">The server needs to know which URL to intercept and direct to SignalR.</span></span> <span data-ttu-id="6144f-223">Zu diesem Zweck fügen Sie eine OWIN-Startup-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="6144f-223">To do that, add an OWIN startup class:</span></span>

1. <span data-ttu-id="6144f-224">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **neues Element**.</span><span class="sxs-lookup"><span data-stu-id="6144f-224">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="6144f-225">In **neues Element hinzufügen - SignalR.StockTicker** wählen **installiert** > **Visual C#**   >  **Web** und Wählen Sie dann **OWIN-Startklasse**.</span><span class="sxs-lookup"><span data-stu-id="6144f-225">In **Add New Item - SignalR.StockTicker** select **Installed** > **Visual C#** > **Web** and then select **OWIN Startup Class**.</span></span>

1. <span data-ttu-id="6144f-226">Nennen Sie die Klasse *Start* , und wählen Sie **OK**.</span><span class="sxs-lookup"><span data-stu-id="6144f-226">Name the class *Startup* and select **OK**.</span></span>

1. <span data-ttu-id="6144f-227">Ersetzen Sie den Standardcode in der *"Startup.cs"* Datei durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="6144f-227">Replace the default code in the *Startup.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample10.cs)]

<span data-ttu-id="6144f-228">Sie haben nun das Einrichten der Servercode abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="6144f-228">You have now finished setting up the server code.</span></span> <span data-ttu-id="6144f-229">Im nächsten Abschnitt richten Sie den Client.</span><span class="sxs-lookup"><span data-stu-id="6144f-229">In the next section, you'll set up the client.</span></span>

## <a name="set-up-the-client-code"></a><span data-ttu-id="6144f-230">Richten Sie den Clientcode</span><span class="sxs-lookup"><span data-stu-id="6144f-230">Set up the client code</span></span>

<span data-ttu-id="6144f-231">In diesem Abschnitt richten Sie den Code, der auf dem Client ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6144f-231">In this section, you set up the code that runs on the client.</span></span>

### <a name="create-the-html-page-and-javascript-file"></a><span data-ttu-id="6144f-232">Erstellen Sie die HTML-Seite und die JavaScript-Datei</span><span class="sxs-lookup"><span data-stu-id="6144f-232">Create the HTML page and JavaScript file</span></span>

<span data-ttu-id="6144f-233">Die HTML-Seite zeigt die Daten und die JavaScript-Datei werden die Daten zu organisieren.</span><span class="sxs-lookup"><span data-stu-id="6144f-233">The HTML page will display the data and the JavaScript file will organize the data.</span></span>

#### <a name="create-stocktickerhtml"></a><span data-ttu-id="6144f-234">Erstellen von StockTicker.html</span><span class="sxs-lookup"><span data-stu-id="6144f-234">Create StockTicker.html</span></span>

<span data-ttu-id="6144f-235">Zunächst fügen Sie den HTML-Client.</span><span class="sxs-lookup"><span data-stu-id="6144f-235">First, you'll add the HTML client.</span></span>

1. <span data-ttu-id="6144f-236">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **HTML-Seite**.</span><span class="sxs-lookup"><span data-stu-id="6144f-236">In **Solution Explorer**, right-click the project and select **Add** > **HTML Page**.</span></span>

1. <span data-ttu-id="6144f-237">Nennen Sie die Datei *StockTicker* , und wählen Sie **OK**.</span><span class="sxs-lookup"><span data-stu-id="6144f-237">Name the file *StockTicker* and select **OK**.</span></span>

1. <span data-ttu-id="6144f-238">Ersetzen Sie den Standardcode in der *StockTicker.html* Datei durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="6144f-238">Replace the default code in the *StockTicker.html* file with this code:</span></span>

    [!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample11.html?highlight=40-43)]

    <span data-ttu-id="6144f-239">Der HTML-Code erstellt eine Tabelle mit fünf Spalten, eine Kopfzeile und eine Datenzeile mit einer einzigen Zelle, die alle fünf Spalten umfasst.</span><span class="sxs-lookup"><span data-stu-id="6144f-239">The HTML creates a table with five columns, a header row, and a data row with a single cell that spans all five columns.</span></span> <span data-ttu-id="6144f-240">Die Datenzeile zeigt "laden" vorübergehend auf, wenn die app gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="6144f-240">The data row shows "loading..." momentarily when the app starts.</span></span> <span data-ttu-id="6144f-241">JavaScript-Code wird diese Zeile zu entfernen und in seinen Zeilen direkt hinzugefügt werden, mit vordefinierten Daten, die vom Server abgerufen.</span><span class="sxs-lookup"><span data-stu-id="6144f-241">JavaScript code will remove that row and add in its place rows with stock data retrieved from the server.</span></span>

    <span data-ttu-id="6144f-242">Geben Sie die Skripttags:</span><span class="sxs-lookup"><span data-stu-id="6144f-242">The script tags specify:</span></span>

    * <span data-ttu-id="6144f-243">Die jQuery-Skriptdatei.</span><span class="sxs-lookup"><span data-stu-id="6144f-243">The jQuery script file.</span></span>

    * <span data-ttu-id="6144f-244">Die SignalR Core-Skriptdatei.</span><span class="sxs-lookup"><span data-stu-id="6144f-244">The SignalR core script file.</span></span>

    * <span data-ttu-id="6144f-245">Die Skriptdatei für den SignalR-Proxys.</span><span class="sxs-lookup"><span data-stu-id="6144f-245">The SignalR proxies script file.</span></span>

    * <span data-ttu-id="6144f-246">Eine StockTicker-Skriptdatei, die Sie später erstellen.</span><span class="sxs-lookup"><span data-stu-id="6144f-246">A StockTicker script file that you'll create later.</span></span>

    <span data-ttu-id="6144f-247">Die app generiert dynamisch die Skriptdatei für SignalR-Proxys.</span><span class="sxs-lookup"><span data-stu-id="6144f-247">The app dynamically generates the SignalR proxies script file.</span></span> <span data-ttu-id="6144f-248">Es gibt die URL "/ Signalr/Hubs", und definiert Methoden für die Methoden für die Hub-Klasse, in diesem Fall Proxy für `StockTickerHub.GetAllStocks`.</span><span class="sxs-lookup"><span data-stu-id="6144f-248">It specifies the "/signalr/hubs" URL and defines proxy methods for the methods on the Hub class, in this case, for `StockTickerHub.GetAllStocks`.</span></span> <span data-ttu-id="6144f-249">Wenn Sie es vorziehen, können Sie diese JavaScript-Datei manuell generieren, mit [SignalR-Dienstprogramme](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/).</span><span class="sxs-lookup"><span data-stu-id="6144f-249">If you prefer, you can generate this JavaScript file manually by using [SignalR Utilities](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/).</span></span> <span data-ttu-id="6144f-250">Vergessen Sie nicht, deaktivieren Sie die dynamische Erstellung in der `MapHubs` Methodenaufruf.</span><span class="sxs-lookup"><span data-stu-id="6144f-250">Don't forget to disable dynamic file creation in the `MapHubs` method call.</span></span>

1. <span data-ttu-id="6144f-251">In **Projektmappen-Explorer**, erweitern Sie **Skripts**.</span><span class="sxs-lookup"><span data-stu-id="6144f-251">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="6144f-252">Skriptbibliotheken für jQuery und SignalR werden in das Projekt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="6144f-252">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="6144f-253">Der Paket-Manager wird eine höhere Version von SignalR-Skripts installieren.</span><span class="sxs-lookup"><span data-stu-id="6144f-253">The package manager will install a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="6144f-254">Aktualisieren Sie die Skriptverweise im Codeblock auf die Versionen der Skriptdateien im Projekt entsprechen.</span><span class="sxs-lookup"><span data-stu-id="6144f-254">Update the script references in the code block to correspond to the versions of the script files in the project.</span></span>

1. <span data-ttu-id="6144f-255">In **Projektmappen-Explorer**, mit der rechten Maustaste *StockTicker.html*, und wählen Sie dann **als Startseite festlegen**.</span><span class="sxs-lookup"><span data-stu-id="6144f-255">In **Solution Explorer**, right-click *StockTicker.html*, and then select **Set as Start Page**.</span></span>

#### <a name="create-stocktickerjs"></a><span data-ttu-id="6144f-256">Erstellen von StockTicker.js</span><span class="sxs-lookup"><span data-stu-id="6144f-256">Create StockTicker.js</span></span>

<span data-ttu-id="6144f-257">Erstellen Sie jetzt die JavaScript-Datei.</span><span class="sxs-lookup"><span data-stu-id="6144f-257">Now create the JavaScript file.</span></span>

1. <span data-ttu-id="6144f-258">In **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, und wählen Sie **hinzufügen** > **JavaScript-Datei**.</span><span class="sxs-lookup"><span data-stu-id="6144f-258">In **Solution Explorer**, right-click the project and select **Add** > **JavaScript File**.</span></span>

1. <span data-ttu-id="6144f-259">Nennen Sie die Datei *StockTicker* , und wählen Sie **OK**.</span><span class="sxs-lookup"><span data-stu-id="6144f-259">Name the file *StockTicker* and select **OK**.</span></span>

1. <span data-ttu-id="6144f-260">Fügen Sie folgenden Code, der *StockTicker.js* Datei:</span><span class="sxs-lookup"><span data-stu-id="6144f-260">Add this code to the *StockTicker.js* file:</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample12.js)]

### <a name="examine-the-client-code"></a><span data-ttu-id="6144f-261">Überprüfen Sie den Clientcode</span><span class="sxs-lookup"><span data-stu-id="6144f-261">Examine the client code</span></span>

<span data-ttu-id="6144f-262">Wenn Sie die Client-Code untersuchen, können es Sie erfahren Sie, wie der Clientcode mit den Servercode, um die app funktioniert stellen interagiert.</span><span class="sxs-lookup"><span data-stu-id="6144f-262">If you examine the client code, it will help you learn how the client code interacts with the server code to make the app work.</span></span>

#### <a name="starting-the-connection"></a><span data-ttu-id="6144f-263">Die Verbindung starten</span><span class="sxs-lookup"><span data-stu-id="6144f-263">Starting the connection</span></span>

<span data-ttu-id="6144f-264">`$.connection` bezieht sich auf die SignalR-Proxys.</span><span class="sxs-lookup"><span data-stu-id="6144f-264">`$.connection` refers to the SignalR proxies.</span></span> <span data-ttu-id="6144f-265">Der Code Ruft einen Verweis auf den Proxy für die `StockTickerHub` -Klasse und fügt es in der `ticker` Variable.</span><span class="sxs-lookup"><span data-stu-id="6144f-265">The code gets a reference to the proxy for the `StockTickerHub` class and puts it in the `ticker` variable.</span></span> <span data-ttu-id="6144f-266">Die Proxyname ist der Name, die festgelegt wurde, indem die `HubName` Attribut:</span><span class="sxs-lookup"><span data-stu-id="6144f-266">The proxy name is the name that was set by the `HubName` attribute:</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample13.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample14.cs)]

<span data-ttu-id="6144f-267">Nachdem Sie die Variablen und Funktionen definieren, initialisiert die letzte Zeile des Codes in der Datei die SignalR-Verbindung durch Aufrufen von SignalR `start` Funktion.</span><span class="sxs-lookup"><span data-stu-id="6144f-267">After you define all the variables and functions, the last line of code in the file initializes the SignalR connection by calling the SignalR `start` function.</span></span> <span data-ttu-id="6144f-268">Die `start` Funktion wird asynchron ausgeführt und gibt eine [jQuery verzögerten Objekt](http://api.jquery.com/category/deferred-object/).</span><span class="sxs-lookup"><span data-stu-id="6144f-268">The `start` function executes asynchronously and returns a [jQuery Deferred object](http://api.jquery.com/category/deferred-object/).</span></span> <span data-ttu-id="6144f-269">Sie können zum Angeben der Funktion aufrufen, wenn die Anwendung die asynchrone Aktion wurde die done-Funktion aufrufen.</span><span class="sxs-lookup"><span data-stu-id="6144f-269">You can call the done function to specify the function to call when the app finishes the asynchronous action.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample15.js)]

#### <a name="getting-all-the-stocks"></a><span data-ttu-id="6144f-270">Abrufen aller die Aktien</span><span class="sxs-lookup"><span data-stu-id="6144f-270">Getting all the stocks</span></span>

<span data-ttu-id="6144f-271">Die `init` Funktionsaufrufe der `getAllStocks` Funktionen auf dem Server und verwendet die Informationen, die zum Aktualisieren der vorhandenen Tabelle gibt der Server zurück.</span><span class="sxs-lookup"><span data-stu-id="6144f-271">The `init` function calls the `getAllStocks` function on the server and uses the information that the server returns to update the stock table.</span></span> <span data-ttu-id="6144f-272">Beachten Sie, dass, in der Standardeinstellung CamelCasing auf dem Client verwendet, obwohl der Methodenname Pascal-Schreibweise auf dem Server ist.</span><span class="sxs-lookup"><span data-stu-id="6144f-272">Notice that, by default, you have to use camelCasing on the client even though the method name is pascal-cased on the server.</span></span> <span data-ttu-id="6144f-273">Die Regel CamelCasing gilt nur für Methoden, Objekte nicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6144f-273">The camelCasing rule only applies to methods, not objects.</span></span> <span data-ttu-id="6144f-274">Beispielsweise finden Sie `stock.Symbol` und `stock.Price`, nicht `stock.symbol` oder `stock.price`.</span><span class="sxs-lookup"><span data-stu-id="6144f-274">For example, you refer to `stock.Symbol` and `stock.Price`, not `stock.symbol` or `stock.price`.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample16.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample17.cs)]

<span data-ttu-id="6144f-275">In der `init` -Methode, die app erstellt HTML für eine Tabellenzeile für jedes Bestandsobjekt durch den Aufruf vom Server empfangen `formatStock` Format Eigenschaften der `stock` Objekt aus, und klicken Sie dann durch den Aufruf `supplant` ersetzen Sie Platzhalter in der `rowTemplate` Variable mit dem die `stock` objekteigenschaftswerte.</span><span class="sxs-lookup"><span data-stu-id="6144f-275">In the `init` method, the app creates HTML for a table row for each stock object received from the server by calling `formatStock` to format properties of the `stock` object, and then by calling `supplant` to replace placeholders in the `rowTemplate` variable with the `stock` object property values.</span></span> <span data-ttu-id="6144f-276">Die resultierende HTML wird dann an die Lagerbestandstabelle angefügt.</span><span class="sxs-lookup"><span data-stu-id="6144f-276">The resulting HTML is then appended to the stock table.</span></span>

> [!NOTE]
> <span data-ttu-id="6144f-277">Rufen Sie `init` durch Übergabe in als eine `callback` -Funktion, die nach dem asynchronen führt `start` Funktion abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="6144f-277">You call `init` by passing it in as a `callback` function that executes after the asynchronous `start` function finishes.</span></span> <span data-ttu-id="6144f-278">Wenn Sie aufgerufen `init` als separate JavaScript-Anweisung nach dem Aufruf `start`, die Funktion würde fehlschlagen, da sie sofort ausführen würde, ohne zu warten, für die Startfunktion, um den Vorgang abzuschließen, Herstellen der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="6144f-278">If you called `init` as a separate JavaScript statement after calling `start`, the function would fail because it would run immediately without waiting for the start function to finish establishing the connection.</span></span> <span data-ttu-id="6144f-279">In diesem Fall die `init` Funktion aufrufen, versucht der `getAllStocks` funktionieren, bevor die app eine Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="6144f-279">In that case, the `init` function would try to call the `getAllStocks` function before the app establishes a server connection.</span></span>

#### <a name="getting-updated-stock-prices"></a><span data-ttu-id="6144f-280">Abrufen von aktualisierten Aktienkurse</span><span class="sxs-lookup"><span data-stu-id="6144f-280">Getting updated stock prices</span></span>

<span data-ttu-id="6144f-281">Wenn der Server einen Kurs geändert wird, ruft er die `updateStockPrice` auf verbundenen Clients.</span><span class="sxs-lookup"><span data-stu-id="6144f-281">When the server changes a stock's price, it calls the `updateStockPrice` on connected clients.</span></span> <span data-ttu-id="6144f-282">Die app fügt die Funktion hinzu, um die Clienteigenschaft des der `stockTicker` Proxy für diese Aufrufe vom Server zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="6144f-282">The app adds the function to the client property of the `stockTicker` proxy to make it available to calls from the server.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample18.js)]

<span data-ttu-id="6144f-283">Die `updateStockPrice` -Funktion formatiert, die ein vordefiniertes Objekt, das in eine Tabelle vom Server empfangene Zeile die gleiche Weise wie in der `init` Funktion.</span><span class="sxs-lookup"><span data-stu-id="6144f-283">The `updateStockPrice` function formats a stock object received from the server into a table row the same way as in the `init` function.</span></span> <span data-ttu-id="6144f-284">Anstelle die Zeile an der Tabelle angefügt wird, sucht die Aktie der aktuellen Zeile in der Tabelle, und diese Zeile durch die neue ersetzt.</span><span class="sxs-lookup"><span data-stu-id="6144f-284">Instead of appending the row to the table, it finds the stock's current row in the table and replaces that row with the new one.</span></span>

## <a name="test-the-application"></a><span data-ttu-id="6144f-285">Testen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="6144f-285">Test the application</span></span>

<span data-ttu-id="6144f-286">Sie können testen, die app, um sicherzustellen, dass es funktioniert.</span><span class="sxs-lookup"><span data-stu-id="6144f-286">You can test the app to make sure it's working.</span></span> <span data-ttu-id="6144f-287">Sie sehen, dass alle Browserfenster die live Lagerbestandstabelle mit Aktienkurse schwankt angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="6144f-287">You'll see all browser windows display the live stock table with stock prices fluctuating.</span></span>

1. <span data-ttu-id="6144f-288">In der Symbolleiste aktivieren **Skriptdebugging** und wählen Sie dann auf die entsprechende Schaltfläche, um die app im Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="6144f-288">In the toolbar, turn on **Script Debugging** and then select the play button to run the app in Debug mode.</span></span>

    ![Screenshot der Benutzer das Aktivieren von debugging-Modus und Play auswählen.](tutorial-server-broadcast-with-signalr/_static/image4.png)

    <span data-ttu-id="6144f-290">Ein Browserfenster wird geöffnet und zeigt die **Live Stock Tabelle**.</span><span class="sxs-lookup"><span data-stu-id="6144f-290">A browser window will open displaying the **Live Stock Table**.</span></span> <span data-ttu-id="6144f-291">Die vordefinierte Tabelle zeigt anfänglich der Zeile "laden", klicken Sie dann nach kurzer Zeit, die app zeigt die vordefinierten Anfangsdaten, und starten Sie die Aktienkurse ändern.</span><span class="sxs-lookup"><span data-stu-id="6144f-291">The stock table initially shows the "loading..." line, then, after a short time, the app shows the initial stock data, and then the stock prices start to change.</span></span>

1. <span data-ttu-id="6144f-292">Kopieren Sie die URL aus dem Browser, öffnen Sie zwei andere Browser, und fügen Sie die URLs in die Adresse leisten.</span><span class="sxs-lookup"><span data-stu-id="6144f-292">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

    <span data-ttu-id="6144f-293">Die ursprüngliche Anzeige des vordefinierte entspricht dem ersten Browser, und Änderungen gleichzeitig vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="6144f-293">The initial stock display is the same as the first browser and changes happen simultaneously.</span></span>

1. <span data-ttu-id="6144f-294">Schließen Sie alle Browser, öffnen Sie einen neuen Browser und navigieren Sie zu der gleichen URL.</span><span class="sxs-lookup"><span data-stu-id="6144f-294">Close all browsers, open a new browser, and go to the same URL.</span></span>

    <span data-ttu-id="6144f-295">Der StockTicker-Singleton-Objekt weiterhin auf dem Server ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="6144f-295">The StockTicker singleton object continued to run in the server.</span></span> <span data-ttu-id="6144f-296">Die **Live Stock Tabelle** zeigt an, die die Aktien weiterhin geändert haben.</span><span class="sxs-lookup"><span data-stu-id="6144f-296">The **Live Stock Table** shows that the stocks have continued to change.</span></span> <span data-ttu-id="6144f-297">Daraufhin nicht die ersten Tabelle mit 0 (null), die Zahlen ändern.</span><span class="sxs-lookup"><span data-stu-id="6144f-297">You don't see the initial table with zero change figures.</span></span>

1. <span data-ttu-id="6144f-298">Schließen Sie den Browser.</span><span class="sxs-lookup"><span data-stu-id="6144f-298">Close the browser.</span></span>

## <a name="enable-logging"></a><span data-ttu-id="6144f-299">Protokollierung aktivieren</span><span class="sxs-lookup"><span data-stu-id="6144f-299">Enable logging</span></span>

<span data-ttu-id="6144f-300">SignalR ist eine integrierte Protokollierung-Funktion, die Sie auf dem Client aktivieren können, um die Problembehandlung zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="6144f-300">SignalR has a built-in logging function that you can enable on the client to aid in troubleshooting.</span></span> <span data-ttu-id="6144f-301">In diesem Abschnitt aktivieren Sie die Protokollierung und finden Sie Beispiele, die zeigen, wie Protokolle Sie feststellen, welche der folgenden Transportmethoden SignalR verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="6144f-301">In this section, you enable logging and see examples that show how logs tell you which of the following transport methods SignalR is using:</span></span>

* <span data-ttu-id="6144f-302">[WebSockets](http://en.wikipedia.org/wiki/WebSocket), von IIS 8- und derzeit gebräuchlichen Browsern unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6144f-302">[WebSockets](http://en.wikipedia.org/wiki/WebSocket), supported by IIS 8 and current browsers.</span></span>

* <span data-ttu-id="6144f-303">[Vom Server gesendeten Ereignisse](http://en.wikipedia.org/wiki/Server-sent_events), von Browsern als Internet Explorer unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6144f-303">[Server-sent events](http://en.wikipedia.org/wiki/Server-sent_events), supported by browsers other than Internet Explorer.</span></span>

* <span data-ttu-id="6144f-304">[Forever frame](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe), wird von Internet Explorer unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6144f-304">[Forever frame](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe), supported by Internet Explorer.</span></span>

* <span data-ttu-id="6144f-305">[AJAX langer Abruf](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling), wird von allen Browsern unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6144f-305">[Ajax long polling](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling), supported by all browsers.</span></span>

<span data-ttu-id="6144f-306">Für eine bestimmte Verbindung wählt SignalR die beste Transportmethode, die dem Server und dem Client zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="6144f-306">For any given connection, SignalR chooses the best transport method that both the server and the client support.</span></span>

1. <span data-ttu-id="6144f-307">Open *StockTicker.js*.</span><span class="sxs-lookup"><span data-stu-id="6144f-307">Open *StockTicker.js*.</span></span>

1. <span data-ttu-id="6144f-308">Fügen Sie zum Aktivieren der Protokollierung unmittelbar vor dem Code, der die Verbindung am Ende der Datei initialisiert diese hervorgehobene Codezeile hinzu:</span><span class="sxs-lookup"><span data-stu-id="6144f-308">Add this highlighted line of code to enable logging immediately before the code that initializes the connection at the end of the file:</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample19.js?highlight=2)]

1. <span data-ttu-id="6144f-309">Drücken Sie **F5** um das Projekt auszuführen.</span><span class="sxs-lookup"><span data-stu-id="6144f-309">Press **F5** to run the project.</span></span>

1. <span data-ttu-id="6144f-310">Öffnen Sie Ihren Browser die Developer-Tools-Fenster, und wählen Sie die Konsole, um die Protokolle anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="6144f-310">Open your browser's developer tools window, and select the Console to see the logs.</span></span> <span data-ttu-id="6144f-311">Möglicherweise müssen die Seite zum Anzeigen der Protokolle von SignalR, die die Transportmethode für eine neue Verbindung aushandeln aktualisieren zu können.</span><span class="sxs-lookup"><span data-stu-id="6144f-311">You might have to refresh the page to see the logs of SignalR negotiating the transport method for a new connection.</span></span>

    * <span data-ttu-id="6144f-312">Wenn Sie Internet Explorer 10 unter Windows 8 (IIS 8) ausführen, wird die Transportmethode die **WebSockets**.</span><span class="sxs-lookup"><span data-stu-id="6144f-312">If you're running Internet Explorer 10 on Windows 8 (IIS 8), the transport method is **WebSockets**.</span></span>

    * <span data-ttu-id="6144f-313">Wenn Sie Internet Explorer 10 unter Windows 7 (IIS 7.5) ausführen, wird die Transportmethode die **Iframe**.</span><span class="sxs-lookup"><span data-stu-id="6144f-313">If you're running Internet Explorer 10 on Windows 7 (IIS 7.5), the transport method is **iframe**.</span></span>

    * <span data-ttu-id="6144f-314">Wenn Sie Firefox 19 unter Windows 8 (IIS 8) ausführen, wird die Transportmethode die **WebSockets**.</span><span class="sxs-lookup"><span data-stu-id="6144f-314">If you're running Firefox 19 on Windows 8 (IIS 8), the transport method is **WebSockets**.</span></span>

        > [!TIP]
        > <span data-ttu-id="6144f-315">Installieren Sie die Firebug-add-Ins rufen Sie ein Konsolenfenster, in Firefox.</span><span class="sxs-lookup"><span data-stu-id="6144f-315">In Firefox, install the Firebug add-in to get a Console window.</span></span>

    * <span data-ttu-id="6144f-316">Wenn Sie Firefox 19 unter Windows 7 (IIS 7.5) ausführen, wird die Transportmethode die **vom Server gesendeten** Ereignisse.</span><span class="sxs-lookup"><span data-stu-id="6144f-316">If you're running Firefox 19 on Windows 7 (IIS 7.5), the transport method is **server-sent** events.</span></span>

## <a name="install-the-stockticker-sample"></a><span data-ttu-id="6144f-317">Installieren des Beispiels StockTicker</span><span class="sxs-lookup"><span data-stu-id="6144f-317">Install the StockTicker sample</span></span>

<span data-ttu-id="6144f-318">Die [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) installiert die Anwendung besteht.</span><span class="sxs-lookup"><span data-stu-id="6144f-318">The [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) installs the StockTicker application.</span></span> <span data-ttu-id="6144f-319">Das NuGet-Paket umfasst mehr Funktionen als die vereinfachte Version an, der Sie von Grund auf neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="6144f-319">The NuGet package includes more features than the simplified version that you created from scratch.</span></span> <span data-ttu-id="6144f-320">In diesem Abschnitt des Tutorials müssen Sie das NuGet-Paket zu installieren und überprüfen Sie die neuen Features und den Code, der sie implementiert.</span><span class="sxs-lookup"><span data-stu-id="6144f-320">In this section of the tutorial, you install the NuGet package and review the new features and the code that implements them.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6144f-321">Wenn Sie das Paket installieren, ohne die vorherigen Schritten in diesem Tutorial auszuführen, müssen Sie eine OWIN-Startklasse zu Ihrem Projekt hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="6144f-321">If you install the package without performing the earlier steps of this tutorial, you must add an OWIN startup class to your project.</span></span> <span data-ttu-id="6144f-322">Diese Datei "Readme.txt" des NuGet-Pakets wird dieser Schritt erläutert.</span><span class="sxs-lookup"><span data-stu-id="6144f-322">This readme.txt file for the NuGet package explains this step.</span></span>

### <a name="install-the-signalrsample-nuget-package"></a><span data-ttu-id="6144f-323">Installieren Sie das SignalR.Sample NuGet-Paket</span><span class="sxs-lookup"><span data-stu-id="6144f-323">Install the SignalR.Sample NuGet package</span></span>

1. <span data-ttu-id="6144f-324">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="6144f-324">In **Solution Explorer**, right-click the project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="6144f-325">In **NuGet-Paket-Manager: SignalR.StockTicker**Option **Durchsuchen**.</span><span class="sxs-lookup"><span data-stu-id="6144f-325">In **NuGet Package manager: SignalR.StockTicker**, select **Browse**.</span></span>

1. <span data-ttu-id="6144f-326">Von **Paketquelle**Option **nuget.org**.</span><span class="sxs-lookup"><span data-stu-id="6144f-326">From **Package source**, select **nuget.org**.</span></span>

1. <span data-ttu-id="6144f-327">Geben Sie *SignalR.Sample* in das Suchfeld ein, und wählen **Microsoft.AspNet.SignalR.Sample** > **installieren**.</span><span class="sxs-lookup"><span data-stu-id="6144f-327">Enter *SignalR.Sample* in the search box and select **Microsoft.AspNet.SignalR.Sample** > **Install**.</span></span>

1. <span data-ttu-id="6144f-328">In **Projektmappen-Explorer**, erweitern Sie die *SignalR.Sample* Ordner.</span><span class="sxs-lookup"><span data-stu-id="6144f-328">In **Solution Explorer**, expand the *SignalR.Sample* folder.</span></span>

    <span data-ttu-id="6144f-329">Installieren des Pakets SignalR.Sample erstellt den Ordner und seinen Inhalt.</span><span class="sxs-lookup"><span data-stu-id="6144f-329">Installing the SignalR.Sample package created the folder and its contents.</span></span>

1. <span data-ttu-id="6144f-330">In der *SignalR.Sample* Ordner mit der rechten Maustaste *StockTicker.html*, und wählen Sie dann **als Startseite festlegen**.</span><span class="sxs-lookup"><span data-stu-id="6144f-330">In the *SignalR.Sample* folder, right-click *StockTicker.html*, and then select **Set As Start Page**.</span></span>

    > [!NOTE]
    > <span data-ttu-id="6144f-331">Installieren das SignalR.Sample NuGet Paket möglicherweise ändern, die Version von jQuery, die Sie in Ihrem *Skripts* Ordner.</span><span class="sxs-lookup"><span data-stu-id="6144f-331">Installing The SignalR.Sample NuGet package might change the version of jQuery that you have in your *Scripts* folder.</span></span> <span data-ttu-id="6144f-332">Die neue *StockTicker.html* -Datei, die in das Paket installiert die *SignalR.Sample* Ordner werden synchron mit der jQuery-Version, die das Paket installiert, aber wenn Sie, führen Sie den ursprünglichen möchten,*StockTicker.html* -Datei erneut, müssen Sie möglicherweise zuerst die jQuery-Verweises im Skripttag zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="6144f-332">The new *StockTicker.html* file that the package installs in the *SignalR.Sample* folder will be in sync with the jQuery version that the package installs, but if you want to run your original *StockTicker.html* file again, you might have to update the jQuery reference in the script tag first.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="6144f-333">Ausführen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="6144f-333">Run the application</span></span>

 <span data-ttu-id="6144f-334">Die Tabelle, die Sie in der ersten app gesehen haben, mussten nützliche Features.</span><span class="sxs-lookup"><span data-stu-id="6144f-334">The table that you saw in the first app had useful features.</span></span> <span data-ttu-id="6144f-335">Die vollständige Börsenticker-Anwendung zeigt die neuen Features: ein horizontalem Bildlauf Fenster, das zeigt die vordefinierten Daten und die Aktien, die Farbe zu ändern, da sie steigen und fallen.</span><span class="sxs-lookup"><span data-stu-id="6144f-335">The full stock ticker application shows new features: a horizontally scrolling window that shows the stock data and stocks that change color as they rise and fall.</span></span>

1. <span data-ttu-id="6144f-336">Drücken Sie **F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="6144f-336">Press **F5** to run the app.</span></span>

     <span data-ttu-id="6144f-337">Wenn Sie die app zum ersten Mal ausführen, die "Markt" ist "geschlossen", und eine statische Tabelle und ein Tickerfenster, das Durchführen eines Bildlaufs wird nicht angezeigt.</span><span class="sxs-lookup"><span data-stu-id="6144f-337">When you run the app for the first time, the "market" is "closed" and you see a static table and a ticker window that isn't scrolling.</span></span>

1. <span data-ttu-id="6144f-338">Wählen Sie **Open Market**.</span><span class="sxs-lookup"><span data-stu-id="6144f-338">Select **Open Market**.</span></span>

    ![Screenshot von der live-Ticker.](tutorial-server-broadcast-with-signalr/_static/image5.png)

    * <span data-ttu-id="6144f-340">Die **Börsenticker Live** Kästchen startet einen horizontalen Bildlauf durchführen, und der Server startet Aktienkurs Änderungen nach dem Zufallsprinzip in regelmäßigen Abständen zu senden.</span><span class="sxs-lookup"><span data-stu-id="6144f-340">The **Live Stock Ticker** box starts to scroll horizontally, and the server starts to periodically broadcast stock price changes on a random basis.</span></span>

    * <span data-ttu-id="6144f-341">Jedes Mal ein Aktienkurs geändert wird, die app aktualisiert werden sowohl die **Stock-Live-Tabelle** und **Börsenticker Live**.</span><span class="sxs-lookup"><span data-stu-id="6144f-341">Each time a stock price changes, the app updates both the **Live Stock Table** and the **Live Stock Ticker**.</span></span>

    * <span data-ttu-id="6144f-342">Bei einer Aktie Preisänderung positiv ist, zeigt die app die Aktie mit einem grünen Hintergrund an.</span><span class="sxs-lookup"><span data-stu-id="6144f-342">When a stock's price change is positive, the app shows the stock with a green background.</span></span>

    * <span data-ttu-id="6144f-343">Wenn die Änderung negativ ist, zeigt die app die Aktie mit einem roten Hintergrund.</span><span class="sxs-lookup"><span data-stu-id="6144f-343">When the change is negative, the app shows the stock with a red background.</span></span>

1. <span data-ttu-id="6144f-344">Wählen Sie **schließen Markt**.</span><span class="sxs-lookup"><span data-stu-id="6144f-344">Select **Close Market**.</span></span>

    * <span data-ttu-id="6144f-345">In der Tabelle aktualisiert beenden.</span><span class="sxs-lookup"><span data-stu-id="6144f-345">The table updates stop.</span></span>

    * <span data-ttu-id="6144f-346">Der Ticker Bildlauf beendet wird.</span><span class="sxs-lookup"><span data-stu-id="6144f-346">The ticker stops scrolling.</span></span>

1. <span data-ttu-id="6144f-347">Wählen Sie **zurücksetzen**.</span><span class="sxs-lookup"><span data-stu-id="6144f-347">Select **Reset**.</span></span>

    * <span data-ttu-id="6144f-348">Alle vorhandene Daten wird zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="6144f-348">All stock data is reset.</span></span>

    * <span data-ttu-id="6144f-349">Die app wird den anfänglichen Zustand vor dem ersten preisänderungen wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="6144f-349">The app restores the initial state before price changes started.</span></span>

1. <span data-ttu-id="6144f-350">Kopieren Sie die URL aus dem Browser, öffnen Sie zwei andere Browser, und fügen Sie die URLs in die Adresse leisten.</span><span class="sxs-lookup"><span data-stu-id="6144f-350">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

1. <span data-ttu-id="6144f-351">Sie sehen die gleichen Daten, die zur gleichen Zeit in jedem Browser dynamisch aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="6144f-351">You see the same data dynamically updated at the same time in each browser.</span></span>

1. <span data-ttu-id="6144f-352">Wenn Sie eines der Steuerelemente auswählen, Antworten von allen Browsern die gleiche Weise zur gleichen Zeit.</span><span class="sxs-lookup"><span data-stu-id="6144f-352">When you select any of the controls, all browsers respond the same way at the same time.</span></span>

### <a name="live-stock-ticker-display"></a><span data-ttu-id="6144f-353">Live Börsenticker-Anzeige</span><span class="sxs-lookup"><span data-stu-id="6144f-353">Live Stock Ticker display</span></span>

<span data-ttu-id="6144f-354">Die **Börsenticker Live** Anzeige ist eine ungeordnete Liste in eine `<div>` Element in einer einzelnen Zeile CSS-Formatvorlagen formatiert.</span><span class="sxs-lookup"><span data-stu-id="6144f-354">The **Live Stock Ticker** display is an unordered list in a `<div>` element formatted into a single line by CSS styles.</span></span> <span data-ttu-id="6144f-355">Die app initialisiert und aktualisiert den Ticker die gleiche Weise wie in der Tabelle: durch ersetzen die Platzhalter in einer `<li>` Vorlagenzeichenfolge und Dynamisches Hinzufügen von der `<li>` Elementen, die die `<ul>` Element.</span><span class="sxs-lookup"><span data-stu-id="6144f-355">The app initializes and updates the ticker the same way as the table: by replacing placeholders in an `<li>` template string and dynamically adding the `<li>` elements to the `<ul>` element.</span></span> <span data-ttu-id="6144f-356">Die app enthält, Durchführen eines Bildlaufs durch mithilfe der jQuery `animate` Funktion variieren die Margin-Left, von der unsortierten Liste innerhalb der `<div>`.</span><span class="sxs-lookup"><span data-stu-id="6144f-356">The app includes  scrolling by using the jQuery `animate` function to vary the margin-left of the unordered list within the `<div>`.</span></span>

#### <a name="signalrsample-stocktickerhtml"></a><span data-ttu-id="6144f-357">SignalR.Sample StockTicker.html</span><span class="sxs-lookup"><span data-stu-id="6144f-357">SignalR.Sample StockTicker.html</span></span>

<span data-ttu-id="6144f-358">Die Börsenticker HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="6144f-358">The stock ticker HTML code:</span></span>

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample20.html)]

#### <a name="signalrsample-stocktickercss"></a><span data-ttu-id="6144f-359">SignalR.Sample StockTicker.css</span><span class="sxs-lookup"><span data-stu-id="6144f-359">SignalR.Sample StockTicker.css</span></span>

<span data-ttu-id="6144f-360">Die Börsenticker CSS-Code:</span><span class="sxs-lookup"><span data-stu-id="6144f-360">The stock ticker CSS code:</span></span>

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample21.html)]

#### <a name="signalrsample-signalrstocktickerjs"></a><span data-ttu-id="6144f-361">SignalR.Sample SignalR.StockTicker.js</span><span class="sxs-lookup"><span data-stu-id="6144f-361">SignalR.Sample SignalR.StockTicker.js</span></span>

<span data-ttu-id="6144f-362">Scrollen Sie die jQuery-Code, der es macht:</span><span class="sxs-lookup"><span data-stu-id="6144f-362">The jQuery code that makes it scroll:</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample22.js)]

### <a name="additional-methods-on-the-server-that-the-client-can-call"></a><span data-ttu-id="6144f-363">Zusätzliche Methoden auf dem Server, den der Client aufrufen kann</span><span class="sxs-lookup"><span data-stu-id="6144f-363">Additional methods on the server that the client can call</span></span>

<span data-ttu-id="6144f-364">Es gibt weitere Methoden, die die app aufrufen kann, zur Steigerung der Flexibilität für die app.</span><span class="sxs-lookup"><span data-stu-id="6144f-364">To add flexibility to the app, there are additional methods the app can call.</span></span>

#### <a name="signalrsample-stocktickerhubcs"></a><span data-ttu-id="6144f-365">SignalR.Sample StockTickerHub.cs</span><span class="sxs-lookup"><span data-stu-id="6144f-365">SignalR.Sample StockTickerHub.cs</span></span>

<span data-ttu-id="6144f-366">Die `StockTickerHub` -Klasse definiert vier zusätzliche Methoden, die der Client aufrufen kann:</span><span class="sxs-lookup"><span data-stu-id="6144f-366">The `StockTickerHub` class defines four additional methods that the client can call:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample23.cs)]

<span data-ttu-id="6144f-367">Die app ruft `OpenMarket`, `CloseMarket`, und `Reset` als Reaktion auf die Schaltflächen am oberen Rand der Seite.</span><span class="sxs-lookup"><span data-stu-id="6144f-367">The app calls `OpenMarket`, `CloseMarket`, and `Reset` in response to the buttons at the top of the page.</span></span> <span data-ttu-id="6144f-368">Sie veranschaulichen das Muster der ein Client eine Änderung am Zustand sofort an alle Clients weitergegeben auslösen.</span><span class="sxs-lookup"><span data-stu-id="6144f-368">They demonstrate the pattern of one client triggering a change in state immediately propagated to all clients.</span></span> <span data-ttu-id="6144f-369">Jede dieser Methoden Ruft eine Methode in der `StockTicker` Klasse, die bewirkt, dass die statusänderung Markt und überträgt dann den neuen Zustand.</span><span class="sxs-lookup"><span data-stu-id="6144f-369">Each of these methods calls a method in the `StockTicker` class that causes the market state change and then broadcasts the new state.</span></span>

#### <a name="signalrsample-stocktickercs"></a><span data-ttu-id="6144f-370">SignalR.Sample StockTicker.cs</span><span class="sxs-lookup"><span data-stu-id="6144f-370">SignalR.Sample StockTicker.cs</span></span>

<span data-ttu-id="6144f-371">In der `StockTicker` -Klasse, die app verwaltet den Zustand des Markts mit einem `MarketState` Eigenschaft, die zurückgibt eine `MarketState` Enum-Wert:</span><span class="sxs-lookup"><span data-stu-id="6144f-371">In the `StockTicker` class, the app maintains the state of the market with a `MarketState` property that returns a `MarketState` enum value:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample24.cs)]

<span data-ttu-id="6144f-372">Jede der Methoden, die den Markt-Status zu ändern ist dies innerhalb eines Blocks für die Sperre der `StockTicker` Klasse threadsicher sein muss:</span><span class="sxs-lookup"><span data-stu-id="6144f-372">Each of the methods that change the market state do so inside a lock block because the `StockTicker` class has to be thread-safe:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample25.cs)]

<span data-ttu-id="6144f-373">Um sicherzustellen, dass dieser Code ist threadsicher, die `_marketState` Feld, das sichert die `MarketState` bezeichneten `volatile`:</span><span class="sxs-lookup"><span data-stu-id="6144f-373">To make sure this code is thread-safe, the `_marketState` field that backs the `MarketState` property designated `volatile`:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample26.cs)]

<span data-ttu-id="6144f-374">Die `BroadcastMarketStateChange` und `BroadcastMarketReset` Methoden ähneln die BroadcastStockPrice-Methode, die Sie bereits gesehen haben, außer sie unterschiedliche auf dem Client definierte Methoden aufrufen:</span><span class="sxs-lookup"><span data-stu-id="6144f-374">The `BroadcastMarketStateChange` and `BroadcastMarketReset` methods are similar to the BroadcastStockPrice method that you already saw, except they call different methods defined at the client:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample27.cs)]

### <a name="additional-functions-on-the-client-that-the-server-can-call"></a><span data-ttu-id="6144f-375">Zusätzliche Funktionen auf dem Client, den der Server aufrufen kann</span><span class="sxs-lookup"><span data-stu-id="6144f-375">Additional functions on the client that the server can call</span></span>

<span data-ttu-id="6144f-376">Die `updateStockPrice` Funktion verarbeitet jetzt sowohl in der Tabelle und die Ticker-Anzeige, und es verwendet `jQuery.Color` rote und grüne Farben Flash.</span><span class="sxs-lookup"><span data-stu-id="6144f-376">The `updateStockPrice` function now handles both the table and the ticker display, and it uses `jQuery.Color` to flash red and green colors.</span></span>

<span data-ttu-id="6144f-377">Neue Funktionen in *SignalR.StockTicker.js* aktivieren und deaktivieren Sie die Schaltflächen, die basierend auf den Markt Zustand.</span><span class="sxs-lookup"><span data-stu-id="6144f-377">New functions in *SignalR.StockTicker.js* enable and disable the buttons based on market state.</span></span> <span data-ttu-id="6144f-378">Sie starten oder beenden Sie auch die **Börsenticker Live** horizontalen Bildlauf.</span><span class="sxs-lookup"><span data-stu-id="6144f-378">They also stop or start the **Live Stock Ticker** horizontal scrolling.</span></span> <span data-ttu-id="6144f-379">Da viele Funktionen hinzugefügt werden `ticker.client`, die app verwendet die [jQuery erweitern Funktion](http://api.jquery.com/jQuery.extend/) hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="6144f-379">Since many functions are being added to `ticker.client`, the app uses the [jQuery extend function](http://api.jquery.com/jQuery.extend/) to add them.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample28.js)]

### <a name="additional-client-setup-after-establishing-the-connection"></a><span data-ttu-id="6144f-380">Zusätzliche Client-Setup nach dem Herstellen der Verbindung</span><span class="sxs-lookup"><span data-stu-id="6144f-380">Additional client setup after establishing the connection</span></span>

<span data-ttu-id="6144f-381">Nachdem der Client die Verbindung herstellt, hat es einige zusätzliche Schritte zu führen:</span><span class="sxs-lookup"><span data-stu-id="6144f-381">After the client establishes the connection, it has some additional work to do:</span></span>

* <span data-ttu-id="6144f-382">Ermitteln, ob der Markt offen oder geschlossen ist, rufen Sie die entsprechende ist `marketOpened` oder `marketClosed` Funktion.</span><span class="sxs-lookup"><span data-stu-id="6144f-382">Find out if the market is open or closed to call the appropriate `marketOpened` or `marketClosed` function.</span></span>

* <span data-ttu-id="6144f-383">Fügen Sie die Server-Methodenaufrufe auf Schaltflächen an.</span><span class="sxs-lookup"><span data-stu-id="6144f-383">Attach the server method calls to the buttons.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample29.js)]

<span data-ttu-id="6144f-384">Die Servermethoden sind nicht mit den Schaltflächen bis verknüpft, nachdem die app die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="6144f-384">The server methods aren't wired up to the buttons until after the app establishes the connection.</span></span> <span data-ttu-id="6144f-385">Dies ist der Code die Servermethoden aufrufen kann, bevor sie verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="6144f-385">It's so the code can't call the server methods before they're available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6144f-386">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6144f-386">Additional resources</span></span>

<span data-ttu-id="6144f-387">In diesem Tutorial haben Sie gelernt, wie Sie eine SignalR-Anwendung programmieren, die Nachrichten vom Server an alle verbundenen Clients zu übertragen.</span><span class="sxs-lookup"><span data-stu-id="6144f-387">In this tutorial you've learned how to program a SignalR application that broadcasts messages from the server to all connected clients.</span></span> <span data-ttu-id="6144f-388">Jetzt können Sie Nachrichten in regelmäßigen Abständen und als Reaktion auf Benachrichtigungen von beliebigen Clients übertragen.</span><span class="sxs-lookup"><span data-stu-id="6144f-388">Now you can broadcast messages on a periodic basis and in response to notifications from any client.</span></span> <span data-ttu-id="6144f-389">Sie können das Konzept der Multithread-Singleton-Instanz verwenden, zur Beibehaltung des Zustands der Server in Multiplayer online game-Szenarien.</span><span class="sxs-lookup"><span data-stu-id="6144f-389">You can use the concept of multi-threaded singleton instance to maintain server state in multi-player online game scenarios.</span></span> <span data-ttu-id="6144f-390">Ein Beispiel finden Sie unter [ShootR Spiel auf der Grundlage von SignalR](https://github.com/NTaylorMullen/ShootR).</span><span class="sxs-lookup"><span data-stu-id="6144f-390">For an example, see [the ShootR game based on SignalR](https://github.com/NTaylorMullen/ShootR).</span></span>

<span data-ttu-id="6144f-391">Tutorials, die Peer-zu-Peer-Kommunikationsszenarien veranschaulichen, finden Sie unter [erste Schritte mit SignalR](introduction-to-signalr.md) und [in Echtzeit mit SignalR aktualisieren](tutorial-high-frequency-realtime-with-signalr.md).</span><span class="sxs-lookup"><span data-stu-id="6144f-391">For tutorials that show peer-to-peer communication scenarios, see [Getting Started with SignalR](introduction-to-signalr.md) and [Real-Time Updating with SignalR](tutorial-high-frequency-realtime-with-signalr.md).</span></span>

<span data-ttu-id="6144f-392">Weitere Informationen zu SignalR finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="6144f-392">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="6144f-393">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="6144f-393">ASP.NET SignalR</span></span>](../../index.md)
* [<span data-ttu-id="6144f-394">SignalR-Projekt</span><span class="sxs-lookup"><span data-stu-id="6144f-394">SignalR Project</span></span>](http://signalr.net/)
* [<span data-ttu-id="6144f-395">SignalR GitHub und Beispiele</span><span class="sxs-lookup"><span data-stu-id="6144f-395">SignalR GitHub and Samples</span></span>](https://github.com/SignalR/SignalR)
* [<span data-ttu-id="6144f-396">SignalR-Wiki</span><span class="sxs-lookup"><span data-stu-id="6144f-396">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="6144f-397">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="6144f-397">Next steps</span></span>

<span data-ttu-id="6144f-398">In diesem Tutorial haben Sie:</span><span class="sxs-lookup"><span data-stu-id="6144f-398">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6144f-399">Erstellen des Projekts</span><span class="sxs-lookup"><span data-stu-id="6144f-399">Created the project</span></span>
> * <span data-ttu-id="6144f-400">Richten Sie den Server-code</span><span class="sxs-lookup"><span data-stu-id="6144f-400">Set up the server code</span></span>
> * <span data-ttu-id="6144f-401">Überprüft den Servercode</span><span class="sxs-lookup"><span data-stu-id="6144f-401">Examined the server code</span></span>
> * <span data-ttu-id="6144f-402">Richten Sie den Clientcode</span><span class="sxs-lookup"><span data-stu-id="6144f-402">Set up the client code</span></span>
> * <span data-ttu-id="6144f-403">Überprüft den Client-code</span><span class="sxs-lookup"><span data-stu-id="6144f-403">Examined the client code</span></span>
> * <span data-ttu-id="6144f-404">Testen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="6144f-404">Tested the application</span></span>
> * <span data-ttu-id="6144f-405">Aktiviert die Protokollierung</span><span class="sxs-lookup"><span data-stu-id="6144f-405">Enabled logging</span></span>

<span data-ttu-id="6144f-406">Wechseln Sie zum nächsten Artikel erfahren Sie, wie eine Echtzeit-Anwendung zu erstellen, die ASP.NET SignalR 2 verwendet.</span><span class="sxs-lookup"><span data-stu-id="6144f-406">Advance to the next article to learn how to create a real-time web application that uses ASP.NET SignalR 2.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="6144f-407">Erstellen von Echtzeit-Web-app mit SignalR</span><span class="sxs-lookup"><span data-stu-id="6144f-407">Create real-time web app with SignalR</span></span>](real-time-web-applications-with-signalr.md)