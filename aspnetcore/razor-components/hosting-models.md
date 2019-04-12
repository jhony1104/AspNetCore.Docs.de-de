---
title: Hostingmodelle Razor-Komponenten
author: guardrex
description: Verstehen der clientseitigen Blazor und ASP.NET Core Razor Serverkomponenten Hostingmodelle.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/28/2019
uid: razor-components/hosting-models
ms.openlocfilehash: 8ed70117c94bf1a3e4c208f70310bbf0473bae44
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515526"
---
# <a name="razor-components-hosting-models"></a><span data-ttu-id="b4ae0-103">Hostingmodelle Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="b4ae0-103">Razor Components hosting models</span></span>

<span data-ttu-id="b4ae0-104">Durch [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b4ae0-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="b4ae0-105">Razor-Komponenten ist ein Webframework, das für die clientseitige Ausführung vorgesehen im Browser auf eine [WebAssembly](http://webassembly.org/)-basierte .NET Common Language Runtime (*Blazor*) oder serverseitige in ASP.NET Core (*ASP.NET Core Razor Komponenten*).</span><span class="sxs-lookup"><span data-stu-id="b4ae0-105">Razor Components is a web framework designed to run client-side in the browser on a [WebAssembly](http://webassembly.org/)-based .NET runtime (*Blazor*) or server-side in ASP.NET Core (*ASP.NET Core Razor Components*).</span></span> <span data-ttu-id="b4ae0-106">Unabhängig von dem Modell, die app und die Komponente Hostingmodelle *unverändert*.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-106">Regardless of the hosting model, the app and component models *remain the same*.</span></span> <span data-ttu-id="b4ae0-107">In diesem Artikel werden die verfügbaren Hostingmodelle erläutert:</span><span class="sxs-lookup"><span data-stu-id="b4ae0-107">This article discusses the available hosting models:</span></span>

* [<span data-ttu-id="b4ae0-108">Clientseitiges Blazor</span><span class="sxs-lookup"><span data-stu-id="b4ae0-108">Client-side Blazor</span></span>](#client-side-hosting-model)
* [<span data-ttu-id="b4ae0-109">Serverseitige .NET Core-Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="b4ae0-109">Server-side ASP.NET Core Razor Components</span></span>](#server-side-hosting-model)

## <a name="client-side-hosting-model"></a><span data-ttu-id="b4ae0-110">Clientseitiges Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="b4ae0-110">Client-side hosting model</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="b4ae0-111">Das Dienstprinzipale Hostingmodell für Blazor ist im Browser auf WebAssembly clientseitige ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-111">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="b4ae0-112">Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser herunterladen.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-112">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="b4ae0-113">Die App wird direkt im UI-Thread des Browsers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-113">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="b4ae0-114">Aktualisierungen der Benutzeroberfläche und Behandlung von Ereignissen treten innerhalb des gleichen Prozesses.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-114">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="b4ae0-115">Die app Ressourcen werden als statische Dateien an einen Webserver oder ein Dienst kann das Verarbeiten von statischen Inhalten an Clients bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-115">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor clientseitig: Die Blazor-Anwendung, die in einem UI-Thread im Browser ausgeführt werden.](hosting-models/_static/client-side.png)

<span data-ttu-id="b4ae0-117">Um eine Blazor-app, die über das Client-Side-hosting-Modell erstellen, verwenden Sie eine der folgenden Vorlagen:</span><span class="sxs-lookup"><span data-stu-id="b4ae0-117">To create a Blazor app using the client-side hosting model, use either of the following templates:</span></span>

* <span data-ttu-id="b4ae0-118">**Blazor** ([Dotnet neue Blazor](/dotnet/core/tools/dotnet-new)) &ndash; als einen Satz von statischen Dateien bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-118">**Blazor** ([dotnet new blazor](/dotnet/core/tools/dotnet-new)) &ndash; Deployed as a set of static files.</span></span>
* <span data-ttu-id="b4ae0-119">**(ASP.NET Core gehostet) Blazor** ([Dotnet neue Blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; von einem ASP.NET Core-Server gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-119">**Blazor (ASP.NET Core Hosted)** ([dotnet new blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; Hosted by an ASP.NET Core server.</span></span> <span data-ttu-id="b4ae0-120">Die ASP.NET Core-app dient die app Blazor Clients.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-120">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="b4ae0-121">Die clientseitige Blazor-app kann über das Netzwerk mithilfe von Web-API-Aufrufen mit dem Server interagieren oder [SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="b4ae0-121">The client-side Blazor app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="b4ae0-122">Die Vorlagen enthalten die *components.webassembly.js* -Skript, das behandelt:</span><span class="sxs-lookup"><span data-stu-id="b4ae0-122">The templates include the *components.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="b4ae0-123">Herunterladen von .NET Runtime, die app und die app Abhängigkeiten.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-123">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="b4ae0-124">Die Initialisierung der Runtime, um die app ausführen.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-124">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="b4ae0-125">Das Hostingmodell für die clientseitige bietet mehrere Vorteile.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-125">The client-side hosting model offers several benefits.</span></span> <span data-ttu-id="b4ae0-126">Die clientseitige Blazor:</span><span class="sxs-lookup"><span data-stu-id="b4ae0-126">Client-side Blazor:</span></span>

* <span data-ttu-id="b4ae0-127">Verfügt über keine serverseitige-Abhängigkeit von .NET.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-127">Has no .NET server-side dependency.</span></span>
* <span data-ttu-id="b4ae0-128">Vollständig nutzt die Clientressourcen und Fähigkeiten.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-128">Fully leverages client resources and capabilities.</span></span>
* <span data-ttu-id="b4ae0-129">Abladungen Arbeit vom Server an den Client.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-129">Offloads work from the server to the client.</span></span>
* <span data-ttu-id="b4ae0-130">Unterstützt die offline-Szenarien.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-130">Supports offline scenarios.</span></span>

<span data-ttu-id="b4ae0-131">Einige Nachteile der clientseitigen hosten.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-131">There are downsides to client-side hosting.</span></span> <span data-ttu-id="b4ae0-132">Die clientseitige Blazor:</span><span class="sxs-lookup"><span data-stu-id="b4ae0-132">Client-side Blazor:</span></span>

* <span data-ttu-id="b4ae0-133">Wird die app auf die Funktionen des Browsers beschränkt.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-133">Restricts the app to the capabilities of the browser.</span></span>
* <span data-ttu-id="b4ae0-134">Erfordert, kann der Clienthardware und Software (z. B. WebAssembly unterstützt).</span><span class="sxs-lookup"><span data-stu-id="b4ae0-134">Requires capable client hardware and software (for example, WebAssembly support).</span></span>
* <span data-ttu-id="b4ae0-135">Verfügt über eine größere Downloadgröße und länger Ladevorgang der app.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-135">Has a larger download size and longer app load time.</span></span>
* <span data-ttu-id="b4ae0-136">Hat weniger Reife der .NET Common Language Runtime und die Unterstützung von Tools (z. B. Einschränkungen in [.NET Standard](/dotnet/standard/net-standard) Unterstützung und Debuggen).</span><span class="sxs-lookup"><span data-stu-id="b4ae0-136">Has less mature .NET runtime and tooling support (for example, limitations in [.NET Standard](/dotnet/standard/net-standard) support and debugging).</span></span>

## <a name="server-side-hosting-model"></a><span data-ttu-id="b4ae0-137">Serverseitiges Hostingmodell</span><span class="sxs-lookup"><span data-stu-id="b4ae0-137">Server-side hosting model</span></span>

<span data-ttu-id="b4ae0-138">Mit dem ASP.NET Core-Razor-Komponenten serverseitige hosting-Modell wird die app auf dem Server in einer ASP.NET Core-app ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-138">With the ASP.NET Core Razor Components server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="b4ae0-139">Aktualisierungen der Benutzeroberfläche, Ereignisbehandlung und JavaScript-Aufrufe erfolgt über eine [SignalR](xref:signalr/introduction) Verbindung.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-139">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![ASP.NET Core-Razor-Komponenten Serverseitig: Der Browser interagiert mit der app, die (gehostet in einer ASP.NET Core-app) auf dem Server über eine SignalR-Verbindung.](hosting-models/_static/server-side.png)

<span data-ttu-id="b4ae0-141">Verwenden Sie zum Erstellen einer Razor-Komponenten-app, die mit das Hostingmodell für die serverseitige die ASP.NET Core **Razor-Komponenten** Vorlage ([Dotnet neue Razorcomponents](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="b4ae0-141">To create a Razor Components app using the server-side hosting model, use the ASP.NET Core **Razor Components** template ([dotnet new razorcomponents](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="b4ae0-142">Die ASP.NET Core-app hostet die serverseitige app Razor-Komponenten und richtet den SignalR-Endpunkt, in denen Clients verbinden.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-142">The ASP.NET Core app hosts the Razor Components server-side app and sets up the SignalR endpoint where clients connect.</span></span> <span data-ttu-id="b4ae0-143">Die ASP.NET Core-app verweist auf der app `Startup` Klasse hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="b4ae0-143">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="b4ae0-144">Serverseitige Komponenten der Razor-Dienste.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-144">Server-side Razor Components services.</span></span>
* <span data-ttu-id="b4ae0-145">Die app die Anforderungspipeline.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-145">The app to the request handling pipeline.</span></span>

[!code-csharp[](hosting-models/samples_snapshot/Startup.cs?highlight=5,27)]

<span data-ttu-id="b4ae0-146">Die *components.server.js* Skript&dagger; stellt die Clientverbindung her.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-146">The *components.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="b4ae0-147">Es ist der Zuständigkeit der app zum beibehalten und Wiederherstellen von app-Status nach Bedarf (z. B. im Falle einer Netzwerkverbindung verloren).</span><span class="sxs-lookup"><span data-stu-id="b4ae0-147">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="b4ae0-148">Das Hostingmodell für die serverseitige bietet mehrere Vorteile.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-148">The server-side hosting model offers several benefits.</span></span> <span data-ttu-id="b4ae0-149">Serverseitige Razor-Komponenten:</span><span class="sxs-lookup"><span data-stu-id="b4ae0-149">Server-side Razor Components:</span></span>

* <span data-ttu-id="b4ae0-150">Verfügen über eine erheblich kleinere app-Größe als eine clientseitige Blazor-app und geladen Sie sehr viel schneller.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-150">Have a significantly smaller app size than a client-side Blazor app and load much faster.</span></span>
* <span data-ttu-id="b4ae0-151">Können vollständig von Serverfunktionen, einschließlich der Verwendung von .NET Core kompatible APIs nutzen.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-151">Can take full advantage of server capabilities, including using any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="b4ae0-152">In .NET Core auf dem Server ausgeführt, damit vorhandene .NET Tools, z. B. Debuggen, wie erwartet funktioniert.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-152">Run on .NET Core on the server, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="b4ae0-153">Funktioniert mit thin Clients (z. B. Browser, WebAssembly und die Ressourcengruppe nicht unterstützen, Geräten eingeschränkt).</span><span class="sxs-lookup"><span data-stu-id="b4ae0-153">Works with thin clients (for example, browsers that don't support WebAssembly and resource constrained devices).</span></span>
* <span data-ttu-id="b4ae0-154">.NET /C# Codebasis, einschließlich der Code der app Komponente ist nicht für Clients bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-154">.NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="b4ae0-155">Einige Nachteile: serverseitige hosten.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-155">There are downsides to server-side hosting.</span></span> <span data-ttu-id="b4ae0-156">Serverseitige Razor-Komponenten:</span><span class="sxs-lookup"><span data-stu-id="b4ae0-156">Server-side Razor Components:</span></span>

* <span data-ttu-id="b4ae0-157">Haben Sie die höhere Latenz: Jedes Eingreifen des Benutzers umfasst ein Netzwerkhop.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-157">Have higher latency: Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="b4ae0-158">Bieten Sie keine offline-Unterstützung: Wenn die Clientverbindung fehlschlägt, funktioniert die app nicht mehr.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-158">Offer no offline support: If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="b4ae0-159">Eingeschränkte Skalierbarkeit: Der Server muss mehrere Clientverbindungen verwalten und Clientstatus behandeln.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-159">Have reduced scalability: The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="b4ae0-160">ASP.NET Core Server zum Bereitstellen der app erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-160">Require an ASP.NET Core server to serve the app.</span></span> <span data-ttu-id="b4ae0-161">Bereitstellung ohne Server (z. B. von einem CDN) ist nicht möglich.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-161">Deployment without a server (for example, from a CDN) isn't possible.</span></span>

<span data-ttu-id="b4ae0-162">&dagger;Die *components.server.js* Skript wird in folgendem Pfad veröffentlicht: *"bin" / {Debuggen | Das Release} / {ZIELFRAMEWORK} /publish/ {ANWENDUNGSNAME}. App/Dist/_framework*.</span><span class="sxs-lookup"><span data-stu-id="b4ae0-162">&dagger;The *components.server.js* script is published to the following path: *bin/{Debug|Release}/{TARGET FRAMEWORK}/publish/{APPLICATION NAME}.App/dist/_framework*.</span></span>
