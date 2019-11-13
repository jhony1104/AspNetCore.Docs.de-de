---
title: ASP.net Core SignalR unterstützten Plattformen
author: bradygaster
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.net Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 86ba5b1aec230d78c1a0e1709187e129df6cb4cc
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963728"
---
# <a name="aspnet-core-opno-locsignalr-supported-platforms"></a>ASP.net Core SignalR unterstützten Plattformen

## <a name="server-system-requirements"></a>Systemanforderungen für Server

SignalR für ASP.net Core unterstützt jede Server Plattform, die ASP.net Core unterstützt.

## <a name="javascript-client"></a>JavaScript-Client

Der [JavaScript-Client](https://www.npmjs.com/package/@aspnet/signalr) wird auf nodejs 8 und höheren Versionen und den folgenden Browsern ausgeführt:

| Browser                         | Version         |
| ------------------------------- | --------------- |
| Microsoft Edge                  | Aktuelle&dagger; |
| Mozilla Firefox                 | Aktuelle&dagger; |
| Google Chrome; umfasst Android | Aktuelle&dagger; |
| SK umfasst IOS            | Aktuelle&dagger; |
| Microsoft Internet Explorer     | 11              |

&dagger;*aktuelle* bezieht sich auf die neueste Version des Browsers.

## <a name="net-client"></a>.NET-Client

Der [.NET-Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) wird auf jeder von ASP.net Core unterstützten Plattform ausgeführt. [Xamarin-Entwickler können](https://github.com/aspnet/Announcements/issues/305) z. b. SignalRzum Entwickeln von Android-Apps mit xamarin. Android 8.4.0.1 und höher und IOS-Apps verwenden, die xamarin. IOS 11.14.0.4 und höher verwenden.

Wenn der Server IIS ausführt, erfordert der websockets-Transport IIS 8,0 oder höher unter Windows Server 2012 oder höher. Andere Transporte werden auf allen Plattformen unterstützt.

## <a name="java-client"></a>Java-Client

Der [Java-Client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) unterstützt Java 8 und höhere Versionen.

## <a name="unsupported-clients"></a>Nicht unterstützte Clients

Die folgenden Clients sind verfügbar, aber experimentell oder inoffiziell. Sie werden derzeit nicht unterstützt und sind möglicherweise nie.

* [C++ent](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [SWIFT-Client](https://github.com/moozzyk/SignalR-Client-Swift)
