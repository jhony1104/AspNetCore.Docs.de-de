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
# <a name="host-and-deploy-blazor-server-side"></a>Hosten und Bereitstellen von serverseitigem Blazor

Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hostkonfigurationswerte

Bei serverseitigen Apps, für die das [serverseitige Hostingmodell](xref:blazor/hosting-models#server-side) verwendet wird, können [allgemeine Hostkonfigurationswerte](xref:fundamentals/host/generic-host#host-configuration) verwendet werden.

## <a name="deployment"></a>Bereitstellung

Mit dem [serverseitigen Hostingmodell](xref:blazor/hosting-models#server-side) wird Blazor über eine ASP.NET Core-App auf dem Server ausgeführt. Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.

Hierfür wird ein Webserver benötigt, der eine ASP.NET Core-App hosten kann. Visual Studio enthält die **Blazor-Server-App**-Projektvorlage (oder die `blazorserverside`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).

## <a name="connection-scale-out"></a>Horizontales Hochskalieren von Verbindungen

Serverseitige Blazor-Apps erfordern eine aktive SignalR-Verbindung für jeden Benutzer. Eine serverseitige Blazor-Produktionsbereitstellung erfordert eine Lösung, mit der so viele gleichzeitige Verbindungen unterstützt werden können, wie von der App angefordert werden. Der [Azure SignalR Service](/azure/azure-signalr/) verarbeitet die Skalierung von Verbindungen und wird als Skalierungslösung für serverseitige Blazor-Apps empfohlen. Weitere Informationen finden Sie unter <xref:signalr/publish-to-azure-web-app>.

## <a name="signalr-configuration"></a>SignalR-Konfiguration

SignalR wird von ASP.NET Core für die gängigsten serverseitigen Blazor-Szenarien konfiguriert. Informationen zu benutzerdefinierten und erweiterten Szenarien finden Sie in den Artikeln zu SignalR im Abschnitt [Zusätzliche Ressourcen](#additional-resources).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:signalr/introduction>
* [Dokumentation zu Azure SignalR Service](/azure/azure-signalr/)
* [Schnellstart: Erstellen eines Chatraums mit SignalR Service](/azure/azure-signalr/signalr-quickstart-dotnet-core)
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [Bereitstellen der ASP.NET Core Vorschauversion für Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
