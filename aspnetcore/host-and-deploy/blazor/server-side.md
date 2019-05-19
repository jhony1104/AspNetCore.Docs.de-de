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
# <a name="host-and-deploy-blazor-server-side"></a>Hosten und Bereitstellen von serverseitigem Blazor

Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hostkonfigurationswerte

Bei serverseitigen Apps, für die das [serverseitige Hostingmodell](xref:blazor/hosting-models#server-side) verwendet wird, können [allgemeine Hostkonfigurationswerte](xref:fundamentals/host/generic-host#host-configuration) verwendet werden.

## <a name="deployment"></a>Bereitstellung

Mit dem [serverseitigen Hostingmodell](xref:blazor/hosting-models#server-side) wird Blazor über eine ASP.NET Core-App auf dem Server ausgeführt. Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.

Hierfür wird ein Webserver benötigt, der eine ASP.NET Core-App hosten kann. Visual Studio enthält die serverseitige **Blazor**-Projektvorlage (oder die `blazorserverside`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Bereitstellen der ASP.NET Core Vorschauversion für Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
