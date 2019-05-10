---
title: ASP.NET Core SignalR unterstützte Plattformen
author: bradygaster
description: Informationen Sie zu den unterstützten Plattformen für ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/06/2019
uid: signalr/supported-platforms
ms.openlocfilehash: fefaaf97de3f1fabf8f3154bf5b4ccb37195ccff
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892017"
---
# <a name="aspnet-core-signalr-supported-platforms"></a>ASP.NET Core SignalR unterstützte Plattformen

## <a name="server-system-requirements"></a>Server – Systemanforderungen

SignalR für ASP.NET Core unterstützt Serverplattform, die ASP.NET Core unterstützt.

## <a name="javascript-client"></a>JavaScript-Client

Die [JavaScript-Client](https://www.npmjs.com/package/@aspnet/signalr) auf NodeJS-8 und höheren Versionen und den folgenden Browsern ausgeführt wird:

| Browser                         | Version |
| ------------------------------- | ------- |
| Microsoft Edge                  | Aktuell |
| Mozilla Firefox                 | Aktuell |
| Google Chrome; Android enthält | Aktuell |
| Safari; iOS enthält            | Aktuell |
| Microsoft Internet Explorer     | 11      |
 
## <a name="net-client"></a>.NET-Client

Die [.NET Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) ausgeführt, die auf jeder Plattform, die von ASP.NET Core unterstützt wird. Z. B. [Xamarin-Entwickler können SignalR](https://github.com/aspnet/Announcements/issues/305) zum Erstellen von Android-apps mithilfe von Xamarin.Android 8.4.0.1 und höher sowie iOS-apps unter Verwendung von Xamarin.iOS 11.14.0.4 und höher.

Wenn der Server mit IIS ausgeführt wird, ist die WebSockets-Übertragung IIS 8.0 oder höher auf Windows Server 2012 oder höher erforderlich. Andere Transporte werden auf allen Plattformen unterstützt.

## <a name="java-client"></a>Java-Client

Die [Java-Client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) Java 8 und höheren Versionen unterstützt.

## <a name="unsupported-clients"></a>Nicht unterstützte clients

Die folgenden Clients sind verfügbar, aber es sind experimentelle oder inoffizielle. Sie werden derzeit nicht unterstützt und möglicherweise nie sein.

* [C++-client](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [SWIFT-client](https://github.com/moozzyk/SignalR-Client-Swift)
