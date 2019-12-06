---
title: Sicherheitsüberlegungen in ASP.net Core SignalR
author: bradygaster
description: Erfahren Sie, wie Sie die Authentifizierung und Autorisierung in ASP.net Core-SignalRverwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- SignalR
uid: signalr/security
ms.openlocfilehash: f443fe0fbaaa1facd09edc0878c048772895ecff
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74881183"
---
# <a name="security-considerations-in-aspnet-core-opno-locsignalr"></a><span data-ttu-id="9b297-103">Sicherheitsüberlegungen in ASP.net Core SignalR</span><span class="sxs-lookup"><span data-stu-id="9b297-103">Security considerations in ASP.NET Core SignalR</span></span>

<span data-ttu-id="9b297-104">Von [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="9b297-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="9b297-105">Dieser Artikel enthält Informationen zum Sichern von SignalR.</span><span class="sxs-lookup"><span data-stu-id="9b297-105">This article provides information on securing SignalR.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="9b297-106">Cross-Origin Resource Sharing</span><span class="sxs-lookup"><span data-stu-id="9b297-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="9b297-107">[Cross-Origin Resource Sharing (cors)](https://www.w3.org/TR/cors/) kann verwendet werden, um Ursprungs übergreifende SignalR Verbindungen im Browser zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="9b297-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin SignalR connections in the browser.</span></span> <span data-ttu-id="9b297-108">Wenn JavaScript-Code in einer anderen Domäne als der SignalR-App gehostet wird, muss [cors-Middleware](xref:security/cors) aktiviert werden, damit JavaScript eine Verbindung mit der SignalR-App herstellen kann.</span><span class="sxs-lookup"><span data-stu-id="9b297-108">If JavaScript code is hosted on a different domain from the SignalR app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the SignalR app.</span></span> <span data-ttu-id="9b297-109">Lassen Sie Ursprungs übergreifende Anforderungen nur von vertrauenswürdigen Domänen zu.</span><span class="sxs-lookup"><span data-stu-id="9b297-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="9b297-110">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9b297-110">For example:</span></span>

* <span data-ttu-id="9b297-111">Ihre Website wird auf `http://www.example.com` gehostet</span><span class="sxs-lookup"><span data-stu-id="9b297-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="9b297-112">Ihre SignalR-APP wird auf `http://signalr.example.com` gehostet</span><span class="sxs-lookup"><span data-stu-id="9b297-112">Your SignalR app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="9b297-113">Cors sollte in der SignalR-APP so konfiguriert werden, dass nur der Ursprung `www.example.com`zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="9b297-113">CORS should be configured in the SignalR app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="9b297-114">Weitere Informationen zum Konfigurieren von cors finden Sie unter [Aktivieren von Cross-Origin-Anforderungen (cors)](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="9b297-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> SignalR<span data-ttu-id="9b297-115"> **erfordert** die folgenden cors-Richtlinien:</span><span class="sxs-lookup"><span data-stu-id="9b297-115"> **requires** the following CORS policies:</span></span>

* <span data-ttu-id="9b297-116">Hiermit werden bestimmte erwartete Ursprünge zugelassen.</span><span class="sxs-lookup"><span data-stu-id="9b297-116">Allow the specific expected origins.</span></span> <span data-ttu-id="9b297-117">Es ist möglich, einen beliebigen Ursprung zuzulassen, aber er ist **nicht** sicher oder wird empfohlen.</span><span class="sxs-lookup"><span data-stu-id="9b297-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="9b297-118">HTTP-Methoden `GET` und `POST` müssen zulässig sein.</span><span class="sxs-lookup"><span data-stu-id="9b297-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="9b297-119">Anmelde Informationen müssen auch dann aktiviert werden, wenn keine Authentifizierung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9b297-119">Credentials must be enabled, even when authentication is not used.</span></span>

<span data-ttu-id="9b297-120">Mit der folgenden cors-Richtlinie kann z. b. ein SignalR Browser-Client auf `https://example.com` auf die SignalR-App zugreifen, die auf `https://signalr.example.com`gehostet wird:</span><span class="sxs-lookup"><span data-stu-id="9b297-120">For example, the following CORS policy allows a SignalR browser client hosted on `https://example.com` to access the SignalR app hosted on `https://signalr.example.com`:</span></span>

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
> SignalR<span data-ttu-id="9b297-121"> ist nicht kompatibel mit der integrierten cors-Funktion in Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="9b297-121"> is not compatible with the built-in CORS feature in Azure App Service.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="9b297-122">WebSocket-Ursprungs Einschränkung</span><span class="sxs-lookup"><span data-stu-id="9b297-122">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="9b297-123">Der von CORS erzeugte Schutz gilt nicht für WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9b297-123">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="9b297-124">Informationen zur Ursprungs Einschränkung für websockets finden Sie unter [websockets Origin-Einschränkung](xref:fundamentals/websockets#websocket-origin-restriction).</span><span class="sxs-lookup"><span data-stu-id="9b297-124">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="9b297-125">Der von CORS erzeugte Schutz gilt nicht für WebSockets.</span><span class="sxs-lookup"><span data-stu-id="9b297-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="9b297-126">Für Browser gilt Folgendes **nicht**:</span><span class="sxs-lookup"><span data-stu-id="9b297-126">Browsers do **not**:</span></span>

* <span data-ttu-id="9b297-127">Ausführen von CORS-Preflightanforderungen</span><span class="sxs-lookup"><span data-stu-id="9b297-127">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="9b297-128">Berücksichtigen der Einschränkungen, die in den `Access-Control`-Headern bei der Erstellung von WebSocket-Anforderungen angegeben sind</span><span class="sxs-lookup"><span data-stu-id="9b297-128">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="9b297-129">Allerdings senden Browser den `Origin`-Header, wenn die WebSocket-Anforderungen ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="9b297-129">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="9b297-130">Anwendungen sollten zur Überprüfung dieser Header konfiguriert werden, um sicherzustellen, dass nur WebSockets von den erwarteten Ursprüngen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="9b297-130">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="9b297-131">In ASP.net Core 2,1 und höher kann die Header Validierung mithilfe einer benutzerdefinierten Middleware **vor `UseSignalR`und der Authentifizierungs Middleware** in `Configure`erreicht werden:</span><span class="sxs-lookup"><span data-stu-id="9b297-131">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `UseSignalR`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="9b297-132">Der `Origin`-Header wird vom Client gesteuert und kann wie der `Referer`-Header überlistet werden.</span><span class="sxs-lookup"><span data-stu-id="9b297-132">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="9b297-133">Diese Header sollten **nicht** als Authentifizierungsmechanismus verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9b297-133">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="access-token-logging"></a><span data-ttu-id="9b297-134">Protokollierung von Zugriffs Token</span><span class="sxs-lookup"><span data-stu-id="9b297-134">Access token logging</span></span>

<span data-ttu-id="9b297-135">Wenn websockets oder vom Server gesendete Ereignisse verwendet werden, sendet der Browser Client das Zugriffs Token in der Abfrage Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="9b297-135">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="9b297-136">Der Empfang des Zugriffs Tokens über eine Abfrage Zeichenfolge ist im Allgemeinen so sicher wie die Verwendung des Standard-`Authorization` Headers.</span><span class="sxs-lookup"><span data-stu-id="9b297-136">Receiving the access token via query string is generally as secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="9b297-137">Sie sollten immer HTTPS verwenden, um eine sichere End-to-End-Verbindung zwischen dem Client und dem Server sicherzustellen.</span><span class="sxs-lookup"><span data-stu-id="9b297-137">You should always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="9b297-138">Viele Webserver protokollieren die URL für jede Anforderung, einschließlich der Abfrage Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="9b297-138">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="9b297-139">Durch das Protokollieren der URLs kann das Zugriffs Token protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="9b297-139">Logging the URLs may log the access token.</span></span> <span data-ttu-id="9b297-140">ASP.net Core protokolliert standardmäßig die URL für jede Anforderung, die die Abfrage Zeichenfolge enthält.</span><span class="sxs-lookup"><span data-stu-id="9b297-140">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="9b297-141">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="9b297-141">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/myhub?access_token=1234
```

<span data-ttu-id="9b297-142">Wenn Sie Bedenken haben, dass Sie diese Daten mit ihren Serverprotokollen protokollieren möchten, können Sie diese Protokollierung vollständig deaktivieren, indem Sie die `Microsoft.AspNetCore.Hosting` Protokollierung auf `Warning` Ebene oder höher konfigurieren (diese Nachrichten werden auf `Info` Ebene geschrieben).</span><span class="sxs-lookup"><span data-stu-id="9b297-142">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="9b297-143">Weitere Informationen finden Sie in der Dokumentation zur [Protokollfilterung](xref:fundamentals/logging/index#log-filtering) .</span><span class="sxs-lookup"><span data-stu-id="9b297-143">See the documentation on [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="9b297-144">Wenn Sie trotzdem bestimmte Anforderungs Informationen protokollieren möchten, können Sie [eine Middleware schreiben](xref:fundamentals/middleware/write) , um die benötigten Daten zu protokollieren, und den `access_token` Abfrage Zeichenfolgen-Wert herausfiltern (sofern vorhanden).</span><span class="sxs-lookup"><span data-stu-id="9b297-144">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="9b297-145">Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="9b297-145">Exceptions</span></span>

<span data-ttu-id="9b297-146">Ausnahme Meldungen werden im Allgemeinen als sensible Daten betrachtet, die für einen Client nicht offengelegt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="9b297-146">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="9b297-147">Standardmäßig sendet SignalR nicht die Details einer Ausnahme, die von einer Hub-Methode ausgelöst wird, an den Client.</span><span class="sxs-lookup"><span data-stu-id="9b297-147">By default, SignalR doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="9b297-148">Stattdessen empfängt der Client eine generische Meldung, die angibt, dass ein Fehler aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="9b297-148">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="9b297-149">Die Übermittlung von Ausnahme Meldungen an den Client kann überschrieben werden (z. b. in "Entwicklung" oder "Test") mit [enabledetailederrors](xref:signalr/configuration#configure-server-options).</span><span class="sxs-lookup"><span data-stu-id="9b297-149">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="9b297-150">Ausnahme Meldungen sollten in Produktions-apps nicht für den Client verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="9b297-150">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="9b297-151">Pufferverwaltung</span><span class="sxs-lookup"><span data-stu-id="9b297-151">Buffer management</span></span>

SignalR<span data-ttu-id="9b297-152"> verwendet Verbindungs Puffer, um eingehende und ausgehende Nachrichten zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="9b297-152"> uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="9b297-153">Standardmäßig beschränkt SignalR diese Puffer auf 32 KB.</span><span class="sxs-lookup"><span data-stu-id="9b297-153">By default, SignalR limits these buffers to 32 KB.</span></span> <span data-ttu-id="9b297-154">Die größte Nachricht, die ein Client oder Server senden kann, ist 32 KB.</span><span class="sxs-lookup"><span data-stu-id="9b297-154">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="9b297-155">Der maximale Arbeitsspeicher, der von einer Verbindung für Nachrichten belegt wird, beträgt 32 KB.</span><span class="sxs-lookup"><span data-stu-id="9b297-155">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="9b297-156">Wenn die Nachrichten immer kleiner als 32 KB sind, können Sie den Grenzwert verringern:</span><span class="sxs-lookup"><span data-stu-id="9b297-156">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="9b297-157">Verhindert, dass ein Client eine größere Nachricht senden kann.</span><span class="sxs-lookup"><span data-stu-id="9b297-157">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="9b297-158">Der Server muss niemals große Puffer zuordnen, um Nachrichten zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="9b297-158">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="9b297-159">Wenn die Nachrichten größer als 32 KB sind, können Sie den Grenzwert erhöhen.</span><span class="sxs-lookup"><span data-stu-id="9b297-159">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="9b297-160">Das Erhöhen dieses Limits bedeutet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9b297-160">Increasing this limit means:</span></span>

* <span data-ttu-id="9b297-161">Der-Client kann bewirken, dass der Server große Speicherpuffer zuweist.</span><span class="sxs-lookup"><span data-stu-id="9b297-161">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="9b297-162">Die Server Zuordnung großer Puffer verringert möglicherweise die Anzahl der gleichzeitigen Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="9b297-162">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="9b297-163">Es gibt Grenzwerte für eingehende und ausgehende Nachrichten. beide können für das [httpconnectiondispatcheroptions](xref:signalr/configuration#configure-server-options) -Objekt konfiguriert werden, das in `MapHub`konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="9b297-163">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="9b297-164">`ApplicationMaxBufferSize` die die maximale Anzahl von Bytes vom Client darstellt, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="9b297-164">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="9b297-165">Wenn der Client versucht, eine Nachricht zu senden, die über diesen Grenzwert hinausgeht, wird die Verbindung möglicherweise geschlossen.</span><span class="sxs-lookup"><span data-stu-id="9b297-165">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="9b297-166">`TransportMaxBufferSize` die die maximale Anzahl von Bytes darstellt, die vom Server gesendet werden können.</span><span class="sxs-lookup"><span data-stu-id="9b297-166">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="9b297-167">Wenn der Server versucht, eine Nachricht (einschließlich Rückgabewerte von hubmethoden) zu senden, die größer als dieser Grenzwert ist, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="9b297-167">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="9b297-168">Wenn Sie das Limit auf `0` festlegen, wird das Limit deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="9b297-168">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="9b297-169">Durch das Entfernen des Limits kann ein Client eine beliebige Größe einer Nachricht senden.</span><span class="sxs-lookup"><span data-stu-id="9b297-169">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="9b297-170">Böswillige Clients, die große Nachrichten senden, können dazu führen, dass mehr Speicherplatz zugewiesen</span><span class="sxs-lookup"><span data-stu-id="9b297-170">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="9b297-171">Durch eine übermäßige Speicherauslastung kann die Anzahl gleichzeitiger Verbindungen erheblich reduziert werden.</span><span class="sxs-lookup"><span data-stu-id="9b297-171">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
