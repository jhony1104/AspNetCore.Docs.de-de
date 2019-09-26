---
title: SignalR-Clientfunktionen
author: bradygaster
description: Erfahren Sie, welche Funktionen von den verschiedenen ASP.net Core signalr-Clients unterstützt werden.
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 2d6759a5484c37aee6db3d22b3127414231605ae
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301188"
---
# <a name="aspnet-core-signalr-client-features"></a>ASP.net Core signalr-Client Funktionen

## <a name="feature-distribution"></a>Funktionsverteilung

In der folgenden Tabelle sind die Features und die Unterstützung für Clients aufgeführt, die Echtzeitunterstützung anbieten.

| Feature | .NET | JavaScript | Java |
| ---- | :-: | :-: | :-: |
| Azure signalr Service-Unterstützung |✔|✔|✔|
| [Streaming zwischen Server und Client](xref:signalr/streaming)          |✔|✔|✔|
| [Client-zu-Server-Streaming](xref:signalr/streaming)          |✔|✔|✔|
| Automatische erneute Verbindung ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |✔|✔| |
| Websockets-Transport |✔|✔|✔|
| Transport von Server gesendeten Ereignissen |✔|✔| |
| Langer Abruf Transport |✔|✔|✔|
| JSON Hub-Protokoll |✔|✔|✔|
| Hub-Protokoll „MessagePack“ |✔|✔| |

Die Unterstützung für die automatische erneute Verbindungs Herstellung im Java-Client wird in [unserer Problem](https://github.com/aspnet/AspNetCore/issues/8711)Verfolgung nachverfolgt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Beginnen Sie mit signalr für ASP.net Core](xref:tutorials/signalr)
* [Unterstützte Plattformen](xref:signalr/supported-platforms)
* [Hubs](xref:signalr/hubs)
* [JavaScript-Client](xref:signalr/javascript-client)
