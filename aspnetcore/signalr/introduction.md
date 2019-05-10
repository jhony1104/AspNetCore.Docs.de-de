---
title: Einführung in ASP.NET Core SignalR
author: bradygaster
description: Erfahren Sie, wie die ASP.NET Core SignalR-Bibliothek vereinfacht das Hinzufügen von Echtzeitfunktionalität für apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/25/2018
uid: signalr/introduction
ms.openlocfilehash: 673efafce60dfa46cb99f9537fda2bca42bf9822
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892247"
---
# <a name="introduction-to-aspnet-core-signalr"></a><span data-ttu-id="abff7-103">Einführung in ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="abff7-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-signalr"></a><span data-ttu-id="abff7-104">Was ist SignalR?</span><span class="sxs-lookup"><span data-stu-id="abff7-104">What is SignalR?</span></span>

<span data-ttu-id="abff7-105">ASP.NET Core SignalR handelt es sich um ein Open Source-Bibliothek, die Hinzufügen von echtzeitwebfunktionalität zu apps vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="abff7-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="abff7-106">Echtzeit-Webfunktionen ermöglicht serverseitiger Code Inhalt per Pushvorgang an Clients sofort an.</span><span class="sxs-lookup"><span data-stu-id="abff7-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="abff7-107">Gute Kandidaten für SignalR:</span><span class="sxs-lookup"><span data-stu-id="abff7-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="abff7-108">Apps, die eine hohe Frequenz von Updates auf dem Server erfordern.</span><span class="sxs-lookup"><span data-stu-id="abff7-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="abff7-109">Beispiele sind Gaming, soziale Netzwerke, voting, Auktion, Karten und GPS-apps.</span><span class="sxs-lookup"><span data-stu-id="abff7-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="abff7-110">Dashboards und überwachungs-apps.</span><span class="sxs-lookup"><span data-stu-id="abff7-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="abff7-111">Beispiele sind Unternehmensdashboards, sofortupdates von Verkaufszahlen oder reisehinweise.</span><span class="sxs-lookup"><span data-stu-id="abff7-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="abff7-112">Apps für die Zusammenarbeit.</span><span class="sxs-lookup"><span data-stu-id="abff7-112">Collaborative apps.</span></span> <span data-ttu-id="abff7-113">Whiteboard-apps und Software für teambesprechungen sind Beispiele von apps für die Zusammenarbeit.</span><span class="sxs-lookup"><span data-stu-id="abff7-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="abff7-114">Apps, die Benachrichtigungen benötigt.</span><span class="sxs-lookup"><span data-stu-id="abff7-114">Apps that require notifications.</span></span> <span data-ttu-id="abff7-115">Verwenden Benachrichtigungen, soziale Netzwerke, e-Mail, Chat, Games, reisehinweise und viele andere apps.</span><span class="sxs-lookup"><span data-stu-id="abff7-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="abff7-116">SignalR stellt eine API zum Erstellen von Server-zu-Client [Remoteprozeduraufrufe (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span><span class="sxs-lookup"><span data-stu-id="abff7-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="abff7-117">Die RPCs rufen JavaScript-Funktionen auf Clients von serverseitigen .NET Core-Code.</span><span class="sxs-lookup"><span data-stu-id="abff7-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="abff7-118">Hier sind einige Features von SignalR für ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="abff7-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="abff7-119">Verbindungsverwaltung behandelt automatisch.</span><span class="sxs-lookup"><span data-stu-id="abff7-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="abff7-120">Sendet Nachrichten gleichzeitig an alle verbundenen Clients.</span><span class="sxs-lookup"><span data-stu-id="abff7-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="abff7-121">Z. B. einem Chatraum.</span><span class="sxs-lookup"><span data-stu-id="abff7-121">For example, a chat room.</span></span>
* <span data-ttu-id="abff7-122">Sendet Nachrichten an bestimmte Clients oder Clientgruppen.</span><span class="sxs-lookup"><span data-stu-id="abff7-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="abff7-123">Skaliert, um Datenverkehr zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="abff7-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="abff7-124">Die Quelle befindet sich einem [SignalR-Repository auf GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span><span class="sxs-lookup"><span data-stu-id="abff7-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="abff7-125">Transportprotokolle</span><span class="sxs-lookup"><span data-stu-id="abff7-125">Transports</span></span>

<span data-ttu-id="abff7-126">SignalR unterstützt verschiedene Techniken für die Behandlung von Echtzeitkommunikation:</span><span class="sxs-lookup"><span data-stu-id="abff7-126">SignalR supports several techniques for handling real-time communications:</span></span>

* [<span data-ttu-id="abff7-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="abff7-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="abff7-128">Vom Server gesendeten Ereignisse</span><span class="sxs-lookup"><span data-stu-id="abff7-128">Server-Sent Events</span></span>
* <span data-ttu-id="abff7-129">Lange Abrufvorgänge</span><span class="sxs-lookup"><span data-stu-id="abff7-129">Long Polling</span></span>

<span data-ttu-id="abff7-130">SignalR wählt automatisch die beste Transportmethode, die innerhalb der Funktionen von Server und Client liegt.</span><span class="sxs-lookup"><span data-stu-id="abff7-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="abff7-131">Hubs</span><span class="sxs-lookup"><span data-stu-id="abff7-131">Hubs</span></span>

<span data-ttu-id="abff7-132">SignalR verwendet *Hubs* für die Kommunikation zwischen Clients und Servern.</span><span class="sxs-lookup"><span data-stu-id="abff7-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="abff7-133">Ein Hub ist eine allgemeine Pipeline, die ermöglicht einem Client und Server, zum Aufrufen von Methoden voneinander.</span><span class="sxs-lookup"><span data-stu-id="abff7-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="abff7-134">SignalR übernimmt die Verteilung über Computergrenzen hinweg automatisch, sodass Clients zum Aufrufen von Methoden, die auf dem Server und umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="abff7-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="abff7-135">Sie können Methoden, stark typisierten Parametern übergeben, um die modellbindung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="abff7-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="abff7-136">SignalR enthält zwei integrierte Hub-Protokolle: ein Text-Protokoll, anhand von JSON- und ein binäres Protokoll, das basierend auf [MessagePack](https://msgpack.org/).</span><span class="sxs-lookup"><span data-stu-id="abff7-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="abff7-137">MessagePack erstellt in der Regel über kleinere Nachrichten, die im Vergleich zu JSON.</span><span class="sxs-lookup"><span data-stu-id="abff7-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="abff7-138">Müssen ältere Browser unterstützen [XHR-Ebene 2](https://caniuse.com/#feat=xhr2) MessagePack protokollunterstützung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="abff7-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="abff7-139">Hubs Aufrufen der clientseitige Code durch Senden von Nachrichten, die den Namen und die Parameter der Methode der clientseitigen enthalten.</span><span class="sxs-lookup"><span data-stu-id="abff7-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="abff7-140">Objekte, die als Parameter gesendet werden deserialisiert das konfigurierte Protokoll verwenden.</span><span class="sxs-lookup"><span data-stu-id="abff7-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="abff7-141">Der Client versucht, die mit dem Namen einer Methode im clientseitigen Code übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="abff7-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="abff7-142">Wenn der Client eine Übereinstimmung findet, die Methode aufruft und die deserialisierte Parameterdaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="abff7-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="abff7-143">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="abff7-143">Additional resources</span></span>

* [<span data-ttu-id="abff7-144">Erste Schritte mit SignalR für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="abff7-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="abff7-145">Unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="abff7-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="abff7-146">Hubs</span><span class="sxs-lookup"><span data-stu-id="abff7-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="abff7-147">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="abff7-147">JavaScript client</span></span>](xref:signalr/javascript-client)
