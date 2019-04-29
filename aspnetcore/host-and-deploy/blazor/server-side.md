---
title: Hosten und Bereitstellen von serverseitigem Blazor
author: guardrex
description: Erfahren Sie, wie Sie eine serverseitige Blazor-App mit ASP.NET Core hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/server-side
ms.openlocfilehash: 940020ee44d72d50395aad64bc924413c1bbecfb
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614716"
---
# <a name="host-and-deploy-blazor-server-side"></a>Hosten und Bereitstellen von serverseitigem Blazor

Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hostkonfigurationswerte

Bei serverseitigen Apps, für die das [serverseitige Hostingmodell](xref:blazor/hosting-models#server-side-hosting-model) verwendet wird, können [allgemeine Hostkonfigurationswerte](xref:fundamentals/host/generic-host#host-configuration) verwendet werden.

## <a name="deployment"></a>Bereitstellung

Mit dem [serverseitigen Hostingmodell](xref:blazor/hosting-models#server-side-hosting-model) wird Blazor über eine ASP.NET Core-App auf dem Server ausgeführt. Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.

Die App ist mit der ASP.NET Core-App in der veröffentlichen Ausgabe enthalten, sodass die beiden Apps zusammen bereitgestellt werden. Hierfür wird ein Webserver benötigt, der eine ASP.NET Core-App hosten kann. Bei einer serverseitigen Bereitstellung enthält Visual Studio die Projektvorlage **Razor-Komponenten** (oder die `razorcomponents`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

Weitere Informationen zum Hosten und Bereitstellen von ASP.NET Core-Apps finden Sie unter <xref:host-and-deploy/index>.

Informationen zum Bereitstellen für Azure App Service finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>.
