---
title: Überlegungen zur Sicherheit in ASP.NET Core SignalR
author: bradygaster
description: Erfahren Sie, wie Sie Authentifizierung und Autorisierung in ASP.NET Core SignalR verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/security
ms.openlocfilehash: 6e9f849ed856cf1cbf989b8b16cab5209c465471
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56743786"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a>Überlegungen zur Sicherheit in ASP.NET Core SignalR

Durch [Andrew Stanton-Nurse](https://twitter.com/anurse)

Dieser Artikel enthält Informationen zum Sichern von SignalR.

## <a name="cross-origin-resource-sharing"></a>Cross-Origin Resource sharing

[Cross-Origin Resource sharing (CORS)](https://www.w3.org/TR/cors/) können verwendet werden, um die SignalR-Verbindungen zwischen verschiedenen Ursprüngen im Browser zulassen. Wenn JavaScript-Code auf einer anderen Domäne als der SignalR-app gehostet wird [CORS-Middleware](xref:security/cors) muss aktiviert sein, um die JavaScript für die Verbindung zur SignalR-app zu ermöglichen. Zulassen, dass Cross-Origin-Anforderungen nur von Domänen, denen, die Sie vertrauen, oder Steuerelement. Zum Beispiel:

* Ihre Website wird auf gehostet. `http://www.example.com`
* Der SignalR-app wird auf gehostet. `http://signalr.example.com`

CORS konfiguriert werden sollte, in der SignalR-app, um nur den Ursprung zuzulassen `www.example.com`.

Weitere Informationen zum Konfigurieren von CORS finden Sie unter [aktivieren Ursprungsübergreifender Anforderungen (CORS)](xref:security/cors). SignalR **erfordert** die folgenden CORS-Richtlinien:

* Ermöglichen Sie die bestimmten erwarteten Ursprünge. Ermöglicht einem beliebigen Ursprung ist möglich ist aber **nicht** sichere oder empfohlen.
* HTTP-Methoden `GET` und `POST` müssen zulässig sein.
* Anmeldeinformationen müssen aktiviert werden, auch wenn keine Authentifizierung verwendet wird.

Beispielsweise kann die folgende CORS-Richtlinie einer SignalR-Browser-Client auf gehosteten `https://example.com` Zugriff auf die SignalR-app, die auf gehosteten `https://signalr.example.com`:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

> [!NOTE]
> SignalR ist nicht kompatibel mit dem integrierten CORS-Feature in Azure App Service.

## <a name="websocket-origin-restriction"></a>Ursprung der WebSocket-Einschränkung

::: moniker range=">= aspnetcore-2.2"

Der von CORS erzeugte Schutz gilt nicht für WebSockets. Origin-Einschränkung für WebSockets, finden Sie in [WebSockets Ursprung Einschränkung](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Der von CORS erzeugte Schutz gilt nicht für WebSockets. Für Browser gilt Folgendes **nicht**:

* Ausführen von CORS-Preflightanforderungen
* Berücksichtigen der Einschränkungen, die in den `Access-Control`-Headern bei der Erstellung von WebSocket-Anforderungen angegeben sind

Allerdings senden Browser den `Origin`-Header, wenn die WebSocket-Anforderungen ausgegeben werden. Anwendungen sollten zur Überprüfung dieser Header konfiguriert werden, um sicherzustellen, dass nur WebSockets von den erwarteten Ursprüngen zulässig sind.

In ASP.NET Core 2.1 und höher headerüberprüfung erfolgt über eine benutzerdefinierte Middleware platziert **vor `UseSignalR`, und die authentifizierungsmiddleware** in `Configure`:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> Der `Origin`-Header wird vom Client gesteuert und kann wie der `Referer`-Header überlistet werden. Dieser Header sollte **nicht** als Authentifizierungsmechanismus verwendet werden.

::: moniker-end

## <a name="access-token-logging"></a>Access-token-Protokollierung

Bei der Verwendung von WebSockets oder Server-Sent Ereignisse sendet der Browserclient das Zugriffstoken in der Abfragezeichenfolge an. Empfangen des Zugriffstokens über die Abfragezeichenfolge in der Regel so sicher wie die Verwendung des Standards ist `Authorization` Header. Sie sollten immer HTTPS verwenden, um sicherzustellen, dass eine sichere End-to-End-Verbindung zwischen dem Client und Server. Viele Webserver melden Sie sich die URL für jede Anforderung, einschließlich der Abfragezeichenfolge. Protokollieren die URLs möglicherweise melden Sie sich das Zugriffstoken. ASP.NET Core protokolliert die URL für jede Anforderung standardmäßig die Abfragezeichenfolge enthält. Zum Beispiel:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

Wenn Sie Bedenken in Bezug auf diese Daten mit Ihrem Server-Protokolle protokolliert haben, können Sie diese Protokollierung deaktivieren, indem konfigurieren die `Microsoft.AspNetCore.Hosting` Protokollierung der `Warning` Ebene oder höher (diese Meldungen werden geschrieben, auf `Info` Ebene). Finden Sie in der Dokumentation auf [Protokollfilterung](xref:fundamentals/logging/index#log-filtering) für Weitere Informationen. Wenn Sie weiterhin bestimmte Anforderungsinformationen protokollieren möchten, können Sie [schreiben Sie eine Middleware](xref:fundamentals/middleware/write) Protokollierung der Daten an, Sie benötigen, und filtern, der `access_token` Wert der Abfragezeichenfolge (falls vorhanden).

## <a name="exceptions"></a>Ausnahmen

Ausnahmemeldungen gelten im Allgemeinen sensible Daten, die für einen Client offen gelegt werden sollte nicht. In der Standardeinstellung senden nicht SignalR die Details der Ausnahme, die von einer hubmethode ausgelöst wird, an dem Client. Stattdessen erhält der Client eine generische Meldung angezeigt, dass ein Fehler aufgetreten ist. Mit Ausnahme der Nachrichtenübermittlung an den Client (z. B. in Entwicklungs- oder testumgebung) überschrieben werden kann [ `EnableDetailedErrors` ](xref:signalr/configuration#configure-server-options). Ausnahmemeldungen sollte nicht an den Client in Produktions-apps verfügbar gemacht werden.

## <a name="buffer-management"></a>Pufferverwaltung

SignalR verwendet pro Verbindung Puffer zum Verwalten von eingehender und ausgehender Nachrichten. Standardmäßig beschränkt SignalR diese Puffer auf 32 KB. Die größte Nachricht, die einem Client oder Server senden kann, beträgt 32 KB. Der maximale Arbeitsspeicher genutzt werden, indem Sie eine Verbindung für Nachrichten beträgt 32 KB. Wenn Ihre Nachrichten immer kleiner als 32 KB sind, können Sie die maximale Anzahl reduzieren die:

* Verhindert, dass einen Client eine größere Nachricht senden.
* Der Server muss nie zuweisen großer Puffer Nachrichten zu akzeptieren.

Wenn Ihre Nachrichten größer als 32 KB sind, können Sie den Grenzwert erhöhen. Erhöhen diese Beschränkung bedeutet:

* Der Client kann den Server an große Speicherpuffer zuzuordnen.
* Serverzuordnung großen Puffern kann die Anzahl von gleichzeitigen Verbindungen reduzieren.

Es sind die Grenzwerte für eingehende und ausgehende Nachrichten, sowohl die konfiguriert werden können, auf die [ `HttpConnectionDispatcherOptions` ](xref:signalr/configuration#configure-server-options) Objekt im konfigurierten `MapHub`:

* `ApplicationMaxBufferSize` die maximale Anzahl von Bytes aus dem Client darstellt, die die Server-Puffer. Wenn der Client versucht, eine Nachricht zu senden, die diesen Wert überschreitet, kann die Verbindung geschlossen.
* `TransportMaxBufferSize` Stellt die maximale Anzahl von Bytes, die der Server senden kann. Wenn der Server versucht, die eine Nachricht (einschließlich Rückgabewerte von Hub-Methoden) zu senden, die diesen Wert überschreitet, wird eine Ausnahme ausgelöst werden.

Festlegen der Beschränkung auf `0` deaktiviert den Grenzwert. Entfernen das Limit kann ein Client zum Senden einer Nachricht beliebiger Größe. Böswillige Clients Senden großer Nachrichten können dazu führen, dass überschüssigen Arbeitsspeicher zugeordnet werden. Übermäßige speicherauslastung kann die Anzahl von gleichzeitigen Verbindungen erheblich reduzieren.
