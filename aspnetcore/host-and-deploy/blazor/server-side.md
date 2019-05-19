---
title: Hosten und Bereitstellen von serverseitigem Blazor
author: guardrex
description: Erfahren Sie, wie Sie eine serverseitige Blazor-App mit ASP.NET Core hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/26/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 8e44be09a4cceba2509f3e86abf3ce5fd2d077bd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887765"
---
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="d8c9c-103">Hosten und Bereitstellen von serverseitigem Blazor</span><span class="sxs-lookup"><span data-stu-id="d8c9c-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="d8c9c-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d8c9c-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="d8c9c-105">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="d8c9c-105">Host configuration values</span></span>

<span data-ttu-id="d8c9c-106">Bei serverseitigen Apps, für die das [serverseitige Hostingmodell](xref:blazor/hosting-models#server-side) verwendet wird, können [allgemeine Hostkonfigurationswerte](xref:fundamentals/host/generic-host#host-configuration) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d8c9c-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="d8c9c-107">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="d8c9c-107">Deployment</span></span>

<span data-ttu-id="d8c9c-108">Mit dem [serverseitigen Hostingmodell](xref:blazor/hosting-models#server-side) wird Blazor über eine ASP.NET Core-App auf dem Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d8c9c-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="d8c9c-109">Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="d8c9c-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="d8c9c-110">Hierfür wird ein Webserver benötigt, der eine ASP.NET Core-App hosten kann.</span><span class="sxs-lookup"><span data-stu-id="d8c9c-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="d8c9c-111">Visual Studio enthält die serverseitige **Blazor**-Projektvorlage (oder die `blazorserverside`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="d8c9c-111">Visual Studio includes the **Blazor (server-side)** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

## <a name="additional-resources"></a><span data-ttu-id="d8c9c-112">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d8c9c-112">Additional resources</span></span>

* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="d8c9c-113">Bereitstellen der ASP.NET Core Vorschauversion für Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d8c9c-113">Deploy ASP.NET Core preview release to Azure App Service</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
