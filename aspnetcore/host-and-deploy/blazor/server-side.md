---
title: Serverseitiges Hosten und Bereitstellen von ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie eine serverseitige Blazor-App mit ASP.NET Core hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 56a03ff583bf85497e2b3bacc70123845a046e3d
ms.sourcegitcommit: 040aedca220ed24ee1726e6886daf6906f95a028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2019
ms.locfileid: "67892694"
---
# <a name="host-and-deploy-blazor-server-side"></a><span data-ttu-id="59a1a-103">Hosten und Bereitstellen von serverseitigem Blazor</span><span class="sxs-lookup"><span data-stu-id="59a1a-103">Host and deploy Blazor server-side</span></span>

<span data-ttu-id="59a1a-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="59a1a-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="59a1a-105">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="59a1a-105">Host configuration values</span></span>

<span data-ttu-id="59a1a-106">Bei serverseitigen Apps, für die das [serverseitige Hostingmodell](xref:blazor/hosting-models#server-side) verwendet wird, können [allgemeine Hostkonfigurationswerte](xref:fundamentals/host/generic-host#host-configuration) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="59a1a-106">Server-side apps that use the [server-side hosting model](xref:blazor/hosting-models#server-side) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="59a1a-107">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="59a1a-107">Deployment</span></span>

<span data-ttu-id="59a1a-108">Mit dem [serverseitigen Hostingmodell](xref:blazor/hosting-models#server-side) wird Blazor über eine ASP.NET Core-App auf dem Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="59a1a-108">With the [server-side hosting model](xref:blazor/hosting-models#server-side), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="59a1a-109">Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="59a1a-109">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="59a1a-110">Hierfür wird ein Webserver benötigt, der eine ASP.NET Core-App hosten kann.</span><span class="sxs-lookup"><span data-stu-id="59a1a-110">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="59a1a-111">Visual Studio enthält die **Blazor-Server-App**-Projektvorlage (oder die `blazorserverside`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="59a1a-111">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="connection-scale-out"></a><span data-ttu-id="59a1a-112">Horizontales Hochskalieren von Verbindungen</span><span class="sxs-lookup"><span data-stu-id="59a1a-112">Connection scale out</span></span>

<span data-ttu-id="59a1a-113">Serverseitige Blazor-Apps erfordern eine aktive SignalR-Verbindung für jeden Benutzer.</span><span class="sxs-lookup"><span data-stu-id="59a1a-113">Blazor server-side apps require one active SignalR connection for each user.</span></span> <span data-ttu-id="59a1a-114">Eine serverseitige Blazor-Produktionsbereitstellung erfordert eine Lösung, mit der so viele gleichzeitige Verbindungen unterstützt werden können, wie von der App angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="59a1a-114">A production Blazor server-side deployment requires a solution for supporting as many concurrent connections as required by the app.</span></span> <span data-ttu-id="59a1a-115">Der [Azure SignalR Service](/azure/azure-signalr/) verarbeitet die Skalierung von Verbindungen und wird als Skalierungslösung für serverseitige Blazor-Apps empfohlen.</span><span class="sxs-lookup"><span data-stu-id="59a1a-115">The [Azure SignalR Service](/azure/azure-signalr/) handles the scaling of connections and is recommended as a scaling solution for Blazor server-side apps.</span></span> <span data-ttu-id="59a1a-116">Weitere Informationen finden Sie unter <xref:signalr/publish-to-azure-web-app>.</span><span class="sxs-lookup"><span data-stu-id="59a1a-116">For more information, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="signalr-configuration"></a><span data-ttu-id="59a1a-117">SignalR-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="59a1a-117">SignalR configuration</span></span>

<span data-ttu-id="59a1a-118">SignalR wird von ASP.NET Core für die gängigsten serverseitigen Blazor-Szenarien konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="59a1a-118">SignalR is configured by ASP.NET Core for the most common Blazor server-side scenarios.</span></span> <span data-ttu-id="59a1a-119">Informationen zu benutzerdefinierten und erweiterten Szenarien finden Sie in den Artikeln zu SignalR im Abschnitt [Zusätzliche Ressourcen](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="59a1a-119">For custom and advanced scenarios, consult the SignalR articles in the [Additional resources](#additional-resources) section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="59a1a-120">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="59a1a-120">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="59a1a-121">Dokumentation zu Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="59a1a-121">Azure SignalR Service Documentation</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="59a1a-122">Schnellstart: Erstellen eines Chatraums mit SignalR Service</span><span class="sxs-lookup"><span data-stu-id="59a1a-122">Quickstart: Create a chat room by using SignalR Service</span></span>](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="59a1a-123">Bereitstellen der ASP.NET Core Vorschauversion für Azure App Service</span><span class="sxs-lookup"><span data-stu-id="59a1a-123">Deploy ASP.NET Core preview release to Azure App Service</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
