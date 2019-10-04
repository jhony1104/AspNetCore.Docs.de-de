---
title: SignalR-Clientfunktionen
author: bradygaster
description: Erfahren Sie, welche Funktionen von den verschiedenen ASP.net Core signalr-Clients unterstützt werden.
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 6718722cdbcfae500026fcd429ca6b9de8f0f103
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925355"
---
# <a name="aspnet-core-signalr-client-features"></a>ASP.net Core signalr-Client Funktionen

## <a name="feature-distribution"></a>Funktionsverteilung

In der folgenden Tabelle sind die Features und die Unterstützung für Clients aufgeführt, die Echtzeitunterstützung anbieten. Für jede Funktion wird die *Mindestversion* aufgelistet, die dieses Feature unterstützt. Wenn keine Version aufgeführt ist, wird die Funktion nicht unterstützt.

| Feature | .NET | JavaScript | Java |
| ---- | :-: | :-: | :-: |
| Azure signalr Service-Unterstützung |1.0.0|1.0.0|1.0.0|
| [Streaming zwischen Server und Client](xref:signalr/streaming)          |1.0.0|1.0.0|1.0.0|
| [Client-zu-Server-Streaming](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|
| Automatische erneute Verbindung ([.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|❌|
| Websockets-Transport |1.0.0|1.0.0|1.0.0|
| Transport von Server gesendeten Ereignissen |1.0.0|1.0.0|❌|
| Langer Abruf Transport |1.0.0|1.0.0|3.0.0|
| JSON Hub-Protokoll |1.0.0|1.0.0|1.0.0|
| Hub-Protokoll „MessagePack“ |1.0.0|1.0.0|❌|

Die Unterstützung für die automatische erneute Verbindungs Herstellung im Java-Client wird in [unserer Problem](https://github.com/aspnet/AspNetCore/issues/8711)Verfolgung nachverfolgt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Beginnen Sie mit signalr für ASP.net Core](xref:tutorials/signalr)
* [Unterstützte Plattformen](xref:signalr/supported-platforms)
* [Hubs](xref:signalr/hubs)
* [JavaScript-Client](xref:signalr/javascript-client)
