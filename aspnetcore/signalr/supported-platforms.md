---
title: ASP.net Core SignalR unterstützten Plattformen
author: bradygaster
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.net Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 9b9cf1d57d61c333c485f23b7ab952c66814d2aa
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317470"
---
# <a name="aspnet-core-opno-locsignalr-supported-platforms"></a>ASP.net Core SignalR unterstützten Plattformen

## <a name="server-system-requirements"></a>Systemanforderungen für Server

SignalR für ASP.net Core unterstützt jede Server Plattform, die ASP.net Core unterstützt.

## <a name="javascript-client"></a>JavaScript-Client

Der [JavaScript-Client](xref:signalr/javascript-client) wird auf nodejs 8 und höheren Versionen und den folgenden Browsern ausgeführt:

| Browser                         | Version         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | Aktuelle&dagger; |
| Mozilla Firefox                 | Aktuelle&dagger; |
| Google Chrome; umfasst Android | Aktuelle&dagger; |
| SK umfasst IOS            | Aktuelle&dagger; |
| Microsoft Internet Explorer     | 11              |

&dagger;*aktuelle* bezieht sich auf die neueste Version des Browsers.

## <a name="net-client"></a>.NET-Client

Der [.NET-Client](xref:signalr/dotnet-client) wird auf jeder von ASP.net Core unterstützten Plattform ausgeführt. [Xamarin-Entwickler können](https://github.com/aspnet/Announcements/issues/305) z. b. SignalRzum Entwickeln von Android-Apps mit xamarin. Android 8.4.0.1 und höher und IOS-Apps verwenden, die xamarin. IOS 11.14.0.4 und höher verwenden.

Wenn der Server IIS ausführt, erfordert der websockets-Transport IIS 8,0 oder höher unter Windows Server 2012 oder höher. Andere Transporte werden auf allen Plattformen unterstützt.

## <a name="java-client"></a>Java-Client

Der [Java-Client](xref:signalr/java-client) unterstützt Java 8 und höhere Versionen.

## <a name="unsupported-clients"></a>Nicht unterstützte Clients

Die folgenden Clients sind verfügbar, aber experimentell oder inoffiziell. Sie werden derzeit nicht unterstützt und sind möglicherweise nie.

* [C++ent](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [SWIFT-Client](https://github.com/moozzyk/SignalR-Client-Swift)
