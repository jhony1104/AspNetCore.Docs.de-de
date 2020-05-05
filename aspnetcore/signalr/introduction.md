---
title: Einführung in ASP.NET Core SignalR
author: bradygaster
description: Erfahren Sie, wie SignalR die ASP.net Core Bibliothek das Hinzufügen von Echtzeitfunktionen zu apps vereinfacht.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 4e968659d23330c4b1403ab998dda9c9e6ebe080
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775777"
---
# <a name="introduction-to-aspnet-core-signalr"></a><span data-ttu-id="62179-103">Einführung in ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="62179-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-signalr"></a><span data-ttu-id="62179-104">Was ist SignalR?</span><span class="sxs-lookup"><span data-stu-id="62179-104">What is SignalR?</span></span>

<span data-ttu-id="62179-105">ASP.net Core SignalR ist eine Open-Source-Bibliothek, die das Hinzufügen von Echt Zeit Webfunktionen zu apps vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="62179-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="62179-106">Die Echt Zeit webolle ermöglicht es dem serverseitigen Code, Inhalte sofort per Push an Clients zu übersetzen.</span><span class="sxs-lookup"><span data-stu-id="62179-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="62179-107">Gute Kandidaten für SignalR:</span><span class="sxs-lookup"><span data-stu-id="62179-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="62179-108">Apps, für die eine hohe Frequenz von Updates vom Server benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="62179-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="62179-109">Beispiele hierfür sind Apps aus den Bereichen Gaming, soziale Netzwerke, Voting, Auktionen, Karten und GPS.</span><span class="sxs-lookup"><span data-stu-id="62179-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="62179-110">Dashboards und Überwachungs-Apps.</span><span class="sxs-lookup"><span data-stu-id="62179-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="62179-111">Beispiele hierfür sind Unternehmensdashboards, Sofortupdates von Verkaufszahlen oder Reisehinweise.</span><span class="sxs-lookup"><span data-stu-id="62179-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="62179-112">Apps für die Zusammenarbeit.</span><span class="sxs-lookup"><span data-stu-id="62179-112">Collaborative apps.</span></span> <span data-ttu-id="62179-113">Whiteboard-Apps und Software für Teambesprechungen sind Beispiele für Apps für die Zusammenarbeit.</span><span class="sxs-lookup"><span data-stu-id="62179-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="62179-114">Apps, für die Benachrichtigungen benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="62179-114">Apps that require notifications.</span></span> <span data-ttu-id="62179-115">Soziale Netzwerke, E-Mail, Chat, Games, Reisehinweise und viele andere Arten von Apps nutzen Benachrichtigungen.</span><span class="sxs-lookup"><span data-stu-id="62179-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

SignalR<span data-ttu-id="62179-116">stellt eine API zum Erstellen von [Remote Prozedur aufrufen (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)für Server-zu-Client bereit.</span><span class="sxs-lookup"><span data-stu-id="62179-116"> provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="62179-117">Die RPCs wenden JavaScript-Funktionen auf Clients aus dem serverseitigen .net Core-Code an.</span><span class="sxs-lookup"><span data-stu-id="62179-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="62179-118">Im folgenden sind einige Features SignalR von für ASP.net Core aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="62179-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="62179-119">Behandelt die Verbindungs Verwaltung automatisch.</span><span class="sxs-lookup"><span data-stu-id="62179-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="62179-120">Sendet Nachrichten gleichzeitig an alle verbundenen Clients.</span><span class="sxs-lookup"><span data-stu-id="62179-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="62179-121">Beispielsweise ein Chatraum.</span><span class="sxs-lookup"><span data-stu-id="62179-121">For example, a chat room.</span></span>
* <span data-ttu-id="62179-122">Sendet Nachrichten an bestimmte Clients oder Client Gruppen.</span><span class="sxs-lookup"><span data-stu-id="62179-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="62179-123">Skaliert, um zunehmenden Datenverkehr zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="62179-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="62179-124">Die Quelle wird in einem [ SignalR Repository auf GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)gehostet.</span><span class="sxs-lookup"><span data-stu-id="62179-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="62179-125">Transportprotokolle</span><span class="sxs-lookup"><span data-stu-id="62179-125">Transports</span></span>

SignalR<span data-ttu-id="62179-126">unterstützt die folgenden Verfahren für die Verarbeitung der Echtzeitkommunikation (in der Reihenfolge des ordnungsgemäßen Fallbacks):</span><span class="sxs-lookup"><span data-stu-id="62179-126"> supports the following techniques for handling real-time communication (in order of graceful fallback):</span></span>

* [<span data-ttu-id="62179-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="62179-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="62179-128">Vom Server gesendete Ereignisse</span><span class="sxs-lookup"><span data-stu-id="62179-128">Server-Sent Events</span></span>
* <span data-ttu-id="62179-129">Langer Abruf</span><span class="sxs-lookup"><span data-stu-id="62179-129">Long Polling</span></span>

SignalR<span data-ttu-id="62179-130">wählt automatisch die beste Transportmethode aus, die sich innerhalb der Funktionen des Servers und des Clients befindet.</span><span class="sxs-lookup"><span data-stu-id="62179-130"> automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="62179-131">Hubs</span><span class="sxs-lookup"><span data-stu-id="62179-131">Hubs</span></span>

SignalR<span data-ttu-id="62179-132">verwendet *Hubs* für die Kommunikation zwischen Clients und Servern.</span><span class="sxs-lookup"><span data-stu-id="62179-132"> uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="62179-133">Ein Hub ist eine Pipeline auf hoher Ebene, die es einem Client und einem Server ermöglicht, Methoden untereinander aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="62179-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> SignalR<span data-ttu-id="62179-134">verarbeitet die Verteilung über die Computer Grenzen hinweg automatisch, sodass Clients Methoden auf dem Server aufrufen können und umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="62179-134"> handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="62179-135">Sie können stark typisierte Parameter an Methoden übergeben, die die Modell Bindung ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="62179-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> SignalR<span data-ttu-id="62179-136">stellt zwei integrierte Hub-Protokolle bereit: ein auf JSON basierendes Text Protokoll und ein auf [messagepack](https://msgpack.org/)basierendes binäres Protokoll.</span><span class="sxs-lookup"><span data-stu-id="62179-136"> provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="62179-137">Im Allgemeinen erstellt messagepack kleinere Nachrichten im Vergleich zu JSON.</span><span class="sxs-lookup"><span data-stu-id="62179-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="62179-138">Ältere Browser müssen [XHR Level 2](https://caniuse.com/#feat=xhr2) unterstützen, um Unterstützung für das messagepack-Protokoll bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="62179-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="62179-139">Hubs wenden Client seitigen Code an, indem Sie Nachrichten senden, die den Namen und die Parameter der Client seitigen Methode enthalten.</span><span class="sxs-lookup"><span data-stu-id="62179-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="62179-140">Objekte, die als Methoden Parameter gesendet werden, werden mithilfe des konfigurierten Protokolls deserialisiert.</span><span class="sxs-lookup"><span data-stu-id="62179-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="62179-141">Der Client versucht, den Namen einer Methode im Client seitigen Code zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="62179-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="62179-142">Wenn der Client eine Entsprechung findet, ruft er die-Methode auf und übergibt die deserialisierten Parameterdaten an ihn.</span><span class="sxs-lookup"><span data-stu-id="62179-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="62179-143">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="62179-143">Additional resources</span></span>

* <span data-ttu-id="62179-144">[Beginnen Sie mit SignalR der ASP.net Core](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="62179-144">[Get started with SignalR for ASP.NET Core](xref:tutorials/signalr)</span></span>
* [<span data-ttu-id="62179-145">Unterstützte Plattformen</span><span class="sxs-lookup"><span data-stu-id="62179-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="62179-146">Hubs</span><span class="sxs-lookup"><span data-stu-id="62179-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="62179-147">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="62179-147">JavaScript client</span></span>](xref:signalr/javascript-client)
