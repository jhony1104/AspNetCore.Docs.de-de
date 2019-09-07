---
title: Sicherheitsüberlegungen in ASP.net Core signalr
author: bradygaster
description: Erfahren Sie, wie Sie die Authentifizierung und Autorisierung in ASP.net Core signalr verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/security
ms.openlocfilehash: a52db2ff51c55f7299d63aa3c7398f99727e0694
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746560"
---
# <a name="security-considerations-in-aspnet-core-signalr"></a>Sicherheitsüberlegungen in ASP.net Core signalr

Von [Andrew Stanton-Nurse](https://twitter.com/anurse)

Dieser Artikel enthält Informationen zum Sichern von signalr.

## <a name="cross-origin-resource-sharing"></a>Cross-Origin Resource Sharing

[Cross-Origin Resource Sharing (cors)](https://www.w3.org/TR/cors/) kann verwendet werden, um Ursprungs übergreifende signalr-Verbindungen im Browser zuzulassen. Wenn JavaScript-Code in einer anderen Domäne als der signalr-App gehostet wird, muss [cors-Middleware](xref:security/cors) aktiviert werden, damit JavaScript eine Verbindung mit der signalr-App herstellen kann. Lassen Sie Ursprungs übergreifende Anforderungen nur von vertrauenswürdigen Domänen zu. Beispiel:

* Ihre Website wird gehostet auf`http://www.example.com`
* Ihre signalr-APP wird auf`http://signalr.example.com`

Cors sollte in der signalr-APP so konfiguriert werden, dass Sie `www.example.com`nur den Ursprung zulässt.

Weitere Informationen zum Konfigurieren von cors finden Sie unter [Aktivieren von Cross-Origin-Anforderungen (cors)](xref:security/cors). Signalr **erfordert** die folgenden cors-Richtlinien:

* Hiermit werden bestimmte erwartete Ursprünge zugelassen. Es ist möglich, einen beliebigen Ursprung zuzulassen, aber er ist **nicht** sicher oder wird empfohlen.
* HTTP- `GET` Methoden `POST` und müssen zulässig sein.
* Anmelde Informationen müssen auch dann aktiviert werden, wenn keine Authentifizierung verwendet wird.

Mit der folgenden cors-Richtlinie kann beispielsweise ein auf gehosteter `https://example.com` signalr-Browser Client auf die signalr-App zugreifen, die auf `https://signalr.example.com`gehostet ist:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chatHub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

> [!NOTE]
> Signalr ist nicht kompatibel mit der integrierten cors-Funktion in Azure App Service.

## <a name="websocket-origin-restriction"></a>WebSocket-Ursprungs Einschränkung

::: moniker range=">= aspnetcore-2.2"

Der von CORS erzeugte Schutz gilt nicht für WebSockets. Informationen zur Ursprungs Einschränkung für websockets finden Sie unter [websockets Origin-Einschränkung](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Der von CORS erzeugte Schutz gilt nicht für WebSockets. Für Browser gilt Folgendes **nicht**:

* Ausführen von CORS-Preflightanforderungen
* Berücksichtigen der Einschränkungen, die in den `Access-Control`-Headern bei der Erstellung von WebSocket-Anforderungen angegeben sind

Allerdings senden Browser den `Origin`-Header, wenn die WebSocket-Anforderungen ausgegeben werden. Anwendungen sollten zur Überprüfung dieser Header konfiguriert werden, um sicherzustellen, dass nur WebSockets von den erwarteten Ursprüngen zulässig sind.

In ASP.net Core 2,1 und höher kann die Header Validierung mithilfe einer benutzerdefinierten Middleware **vor `UseSignalR`und der Authentifizierungs Middleware** in `Configure`erreicht werden:

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> Der `Origin`-Header wird vom Client gesteuert und kann wie der `Referer`-Header überlistet werden. Diese Header sollten **nicht** als Authentifizierungsmechanismus verwendet werden.

::: moniker-end

## <a name="access-token-logging"></a>Protokollierung von Zugriffs Token

Wenn websockets oder vom Server gesendete Ereignisse verwendet werden, sendet der Browser Client das Zugriffs Token in der Abfrage Zeichenfolge. Der Empfang des Zugriffs Tokens über eine Abfrage Zeichenfolge ist im Allgemeinen so `Authorization` sicher wie die Verwendung des Standard-Headers. Sie sollten immer HTTPS verwenden, um eine sichere End-to-End-Verbindung zwischen dem Client und dem Server sicherzustellen. Viele Webserver protokollieren die URL für jede Anforderung, einschließlich der Abfrage Zeichenfolge. Durch das Protokollieren der URLs kann das Zugriffs Token protokolliert werden. ASP.net Core protokolliert standardmäßig die URL für jede Anforderung, die die Abfrage Zeichenfolge enthält. Beispiel:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

Wenn Sie Bedenken haben, dass Sie diese Daten mit ihren Serverprotokollen protokollieren möchten, können Sie diese Protokollierung `Microsoft.AspNetCore.Hosting` vollständig deaktivieren `Warning` , indem Sie die Protokollierung auf die Ebene `Info` oder höher konfigurieren (diese Nachrichten werden auf der Ebene geschrieben). Weitere Informationen finden Sie in der Dokumentation zur [Protokollfilterung](xref:fundamentals/logging/index#log-filtering) . Wenn Sie trotzdem bestimmte Anforderungs Informationen protokollieren möchten, können Sie [eine Middleware schreiben](xref:fundamentals/middleware/write) , um die benötigten Daten zu protokollieren und den `access_token` Wert der Abfrage Zeichenfolge (sofern vorhanden) herauszufiltern.

## <a name="exceptions"></a>Ausnahmen

Ausnahme Meldungen werden im Allgemeinen als sensible Daten betrachtet, die für einen Client nicht offengelegt werden sollen. Standardmäßig sendet signalr nicht die Details einer Ausnahme, die von einer Hub-Methode ausgelöst wird, an den Client. Stattdessen empfängt der Client eine generische Meldung, die angibt, dass ein Fehler aufgetreten ist. Die Übermittlung von Ausnahme Meldungen an den Client kann überschrieben werden (z [`EnableDetailedErrors`](xref:signalr/configuration#configure-server-options). b. in "Entwicklung" oder "Test"). Ausnahme Meldungen sollten in Produktions-apps nicht für den Client verfügbar gemacht werden.

## <a name="buffer-management"></a>Puffer Verwaltung

Signalr verwendet Verbindungs Puffer pro Verbindung, um eingehende und ausgehende Nachrichten zu verwalten. Standardmäßig schränkt signalr diese Puffer auf 32 KB ein. Die größte Nachricht, die ein Client oder Server senden kann, ist 32 KB. Der maximale Arbeitsspeicher, der von einer Verbindung für Nachrichten belegt wird, beträgt 32 KB. Wenn die Nachrichten immer kleiner als 32 KB sind, können Sie den Grenzwert verringern:

* Verhindert, dass ein Client eine größere Nachricht senden kann.
* Der Server muss niemals große Puffer zuordnen, um Nachrichten zu akzeptieren.

Wenn die Nachrichten größer als 32 KB sind, können Sie den Grenzwert erhöhen. Das Erhöhen dieses Limits bedeutet Folgendes:

* Der-Client kann bewirken, dass der Server große Speicherpuffer zuweist.
* Die Server Zuordnung großer Puffer verringert möglicherweise die Anzahl der gleichzeitigen Verbindungen.

Es gibt Grenzwerte für eingehende und ausgehende Nachrichten. beide können [`HttpConnectionDispatcherOptions`](xref:signalr/configuration#configure-server-options) für das in `MapHub`konfigurierte Objekt konfiguriert werden:

* `ApplicationMaxBufferSize`stellt die maximale Anzahl von Bytes vom Client dar, die der Server puffert. Wenn der Client versucht, eine Nachricht zu senden, die über diesen Grenzwert hinausgeht, wird die Verbindung möglicherweise geschlossen.
* `TransportMaxBufferSize`stellt die maximale Anzahl von Bytes dar, die vom Server gesendet werden können. Wenn der Server versucht, eine Nachricht (einschließlich Rückgabewerte von hubmethoden) zu senden, die größer als dieser Grenzwert ist, wird eine Ausnahme ausgelöst.

Durch Festlegen des Limits `0` auf wird der Grenzwert deaktiviert. Durch das Entfernen des Limits kann ein Client eine beliebige Größe einer Nachricht senden. Böswillige Clients, die große Nachrichten senden, können dazu führen, dass mehr Speicherplatz zugewiesen Durch eine übermäßige Speicherauslastung kann die Anzahl gleichzeitiger Verbindungen erheblich reduziert werden.
