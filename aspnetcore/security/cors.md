---
title: Aktivieren von Cross-Origin-Anforderungen (CROSS-Origin Requests, CORS) in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie CORS als Standard ursprungsübergreifende Anforderungen in einer ASP.NET Core-App zugelassen oder abgelehnt werden kann.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642700"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Aktivieren von Cross-Origin-Anforderungen (CROSS-Origin Requests, CORS) in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und Kirk [Larkin](https://twitter.com/serpent5)

Dieser Artikel zeigt, wie CORS in einer ASP.NET Core-App aktiviert wird.

Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als die, die die Webseite bereitgestellt hat, stellt. Diese Einschränkung wird als *Richtlinie mit gleichem Ursprung*bezeichnet. Die Richtlinie mit demselben Ursprung verhindert, dass eine böswillige Website vertrauliche Daten von einer anderen Website liest. Manchmal möchten Sie möglicherweise zulassen, dass andere Websites ursprungsübergreifende Anforderungen an Ihre App stellen. Weitere Informationen finden Sie im [Mozilla CORS-Artikel](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):

* Ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie mit demselben Ursprung zu entspannen.
* Ist **kein** Sicherheitsmerkmal, CORS entspannt die Sicherheit. Eine API ist nicht sicherer, wenn CORS zugelassen wird. Weitere Informationen finden Sie unter Funktionsweise von [CORS](#how-cors).
* Ermöglicht einem Server, einige ursprungsübergreifende Anforderungen explizit zuzulassen, während andere abgelehnt werden.
* Ist sicherer und flexibler als frühere Techniken, wie z. B. [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Gleicher Ursprung

Zwei URLs haben denselben Ursprung, wenn sie identische Schemata, Hosts und Ports haben ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Diese beiden URLs haben den gleichen Ursprung:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:

* `https://example.net`&ndash; Verschiedene Domäne
* `https://www.example.com/foo.html`&ndash; Verschiedene Subdomain
* `http://example.com/foo.html`&ndash; Unterschiedlicheregelung
* `https://example.com:9000/foo.html`&ndash; Verschiedene randder Hafen

## <a name="enable-cors"></a>Aktivieren von CORS

Es gibt drei Möglichkeiten, CORS zu aktivieren:

* In Middleware mit einer [benannten Richtlinie](#np) oder [Standardrichtlinie](#dp).
* Verwenden von [Endpunktrouting](#ecors).
* Mit dem [Attribut [EnableCors].](#attr)

Die Verwendung des [[EnableCors]-Attributs](#attr) mit einer benannten Richtlinie bietet die beste Kontrolle beim Einschränken von Endpunkten, die CORS unterstützen.

Jeder Ansatz wird in den folgenden Abschnitten ausführlich beschrieben.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>CORS mit benannter Richtlinie und Middleware

CORS Middleware verarbeitet ursprungsübergreifende Anforderungen. Der folgende Code wendet eine CORS-Richtlinie auf alle Endpunkte der App mit den angegebenen Ursprüngen an:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

Der vorangehende Code:

* Legt den Richtliniennamen auf fest. `_myAllowSpecificOrigins` Der Richtlinienname ist willkürlich.
* Ruft <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> die Erweiterungsmethode `_myAllowSpecificOrigins` auf und gibt die CORS-Richtlinie an. `UseCors`fügt die CORS-Middleware hinzu. Der Aufruf `UseCors` an muss `UseRouting`nach `UseAuthorization`platziert werden, aber vor . Weitere Informationen finden Sie unter [Middleware-Bestellung](xref:fundamentals/middleware/index#middleware-order).
* Aufrufe <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Der Lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> nimmt ein Objekt. [Konfigurationsoptionen](#cors-policy-options), `WithOrigins`z. B. , werden weiter unten in diesem Artikel beschrieben.
* Aktiviert `_myAllowSpecificOrigins` die CORS-Richtlinie für alle Controllerendpunkte. Siehe [Endpunktrouting,](#ecors) um eine CORS-Richtlinie auf bestimmte Endpunkte anzuwenden.

Beim Endpunktrouting ***muss*** die CORS-Middleware so konfiguriert `UseRouting` werden, dass sie zwischen den Aufrufen von und `UseEndpoints`ausgeführt wird.

Anweisungen zum Testen von Code ähnlich dem vorherigen Code finden Sie unter [Test-CORS.](#testc)

Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Methodenaufruf fügt DEM Dienstcontainer der App CORS-Dienste hinzu:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Weitere Informationen finden Sie unter [CORS-Richtlinienoptionen](#cpo) in diesem Dokument.

Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methoden können verkettet werden, wie im folgenden Code gezeigt:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Hinweis: Die angegebene URL darf **keinen** nachgestellten Schrägstrich (`/`) enthalten. Wenn die URL `/`mit beendet `false` wird, wird der Vergleich zurückgegeben, und es wird kein Header zurückgegeben.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>CORS mit Standardrichtlinie und Middleware

Der folgende hervorgehobene Code aktiviert die Standardmäßige CORS-Richtlinie:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

Der vorherige Code wendet die Standard-CORS-Richtlinie auf alle Controllerendpunkte an.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Aktivieren von Cors mit Endpunktrouting

Das Aktivieren von CORS pro `RequireCors` Endpunkt mithilfe der aktuellen Verwendung unterstützt ***keine*** [automatischen Preflight-Anforderungen](#apf). Weitere Informationen finden Sie unter [dieses GitHub-Problem](https://github.com/dotnet/aspnetcore/issues/20709) und Testen von [CORS mit Endpunktrouting und [HttpOptions]](#tcer).

Mit Endpunktrouting kann CORS pro Endpunkt mithilfe der <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> Erweiterungsmethoden aktiviert werden:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

Für den Code oben gilt:

* `app.UseCors`ermöglicht die CORS Middleware. Da eine Standardrichtlinie nicht konfiguriert wurde, `app.UseCors()` aktiviert ALLEIN CORS nicht.
* Die `/echo` und controller-Endpunkte erlauben ursprungsübergreifende Anforderungen mithilfe der angegebenen Richtlinie.
* Die `/echo2` und Razor Pages-Endpunkte lassen ***keine*** ursprungsübergreifenden Anforderungen zu, da keine Standardrichtlinie angegeben wurde.

Das [Attribut [DisableCors]](#dc) deaktiviert CORS ***nicht,*** das durch `RequireCors`Endpunktrouting mit aktiviert wurde.

Anweisungen zum Testen von Code ähnlich dem vorherigen finden Sie unter [Test-CORS mit Endpunktrouting und [HttpOptions].](#tcer)

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>CORS mit Attributen aktivieren

Das Aktivieren von CORS mit dem [Attribut [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) und das Anwenden einer benannten Richtlinie auf die Endpunkte, die CORS erfordern, bietet die beste Kontrolle.

Das [Attribut [EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) bietet eine Alternative zum globalen Anwenden von CORS. Das `[EnableCors]` Attribut aktiviert CORS für ausgewählte Endpunkte und nicht für alle Endpunkte:

* `[EnableCors]`gibt die Standardrichtlinie an.
* `[EnableCors("{Policy String}")]`gibt eine benannte Richtlinie an.

Das `[EnableCors]` Attribut kann angewendet werden auf:

* Razor Page`PageModel`
* Controller
* Controller-Aktionsmethode

Verschiedene Richtlinien können auf Controller, Seitenmodelle oder `[EnableCors]` Aktionsmethoden mit dem Attribut angewendet werden. Wenn `[EnableCors]` das Attribut auf einen Controller, ein Seitenmodell oder eine Aktionsmethode angewendet wird und CORS in Middleware aktiviert ist, werden ***beide*** Richtlinien angewendet. ***Wir empfehlen, Richtlinien nicht zu kombinieren. Verwenden Sie das*** `[EnableCors]` Attribut oder die ***Middleware, nicht beide in derselben App.***

Der folgende Code wendet für jede Methode eine andere Richtlinie an:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Der folgende Code erstellt zwei CORS-Richtlinien:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Für die beste Kontrolle der Begrenzung von CORS-Anforderungen:

* Wird `[EnableCors("MyPolicy")]` mit einer benannten Richtlinie verwendet.
* Definieren Sie keine Standardrichtlinie.
* Verwenden Sie kein [Endpunktrouting](#ecors).

Der Code im nächsten Abschnitt entspricht der vorherigen Liste.

Anweisungen zum Testen von Code ähnlich dem vorherigen Code finden Sie unter [Test-CORS.](#testc)

<a name="dc"></a>

### <a name="disable-cors"></a>DEAKTIVIEREN von CORS

Das [Attribut [DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) deaktiviert CORS ***nicht,*** das durch [Endpunktrouting](#ecors)aktiviert wurde.

Der folgende Code definiert die `"MyPolicy"`CORS-Richtlinie:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

Der folgende Code deaktiviert CORS für die `GetValues2` Aktion:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

Der vorangehende Code:

* Aktiviert CORS nicht mit [Endpunktrouting](#ecors).
* Definiert keine [CORS-Standardrichtlinie](#dp).
* Verwendet [[EnableCors("MyPolicy")],](#attr) `"MyPolicy"` um die CORS-Richtlinie für den Controller zu aktivieren.
* Deaktiviert CORS für `GetValues2` die Methode.

Anweisungen zum Testen des vorherigen Codes finden Sie unter [Test-CORS.](#testc)

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS-Richtlinienoptionen

In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer CORS-Richtlinie festgelegt werden können:

* [Festlegen der zulässigen Ursprünge](#set-the-allowed-origins)
* [Festlegen der zulässigen HTTP-Methoden](#set-the-allowed-http-methods)
* [Festlegen der zulässigen Anforderungsheader](#set-the-allowed-request-headers)
* [Festlegen der freiliegenden Antwortheader](#set-the-exposed-response-headers)
* [Anmeldeinformationen in ursprungsübergreifenden Anforderungen](#credentials-in-cross-origin-requests)
* [Festlegen der Ablaufzeit vor dem Flug](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>wird in `Startup.ConfigureServices`aufgerufen. Bei einigen Optionen kann es hilfreich sein, zuerst den Abschnitt ["Funktionsweise von CORS"](#how-cors) zu lesen.

## <a name="set-the-allowed-origins"></a>Festlegen der zulässigen Ursprünge

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Ermöglicht CORS-Anforderungen aller Herkunftsherkunft mit einem beliebigen Schema (`http` oder `https`). `AllowAnyOrigin`ist unsicher, da *jede Website* ursprungsübergreifende Anforderungen an die App stellen kann.

> [!NOTE]
> Angeben `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu standortübergreifenden Anforderungsfälschungen führen. Der CORS-Dienst gibt eine ungültige CORS-Antwort zurück, wenn eine App mit beiden Methoden konfiguriert ist.

`AllowAnyOrigin`wirkt sich auf `Access-Control-Allow-Origin` Preflight-Anforderungen und den Header aus. Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Legt <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> die Eigenschaft der Richtlinie als funktion fest, die es dem Ursprung ermöglicht, eine konfigurierte Platzhalterdomäne abzugleichen, wenn ausgewertet wird, ob der Ursprung zulässig ist.

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Festlegen der zulässigen HTTP-Methoden

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Erlaubt jede HTTP-Methode:
* Beeinflusst Preflight-Anforderungen `Access-Control-Allow-Methods` und den Header. Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Festlegen der zulässigen Anforderungsheader

Um das Senden bestimmter Header in einer CORS-Anforderung zuzulassen, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> rufen Sie die [zulässigen](https://xhr.spec.whatwg.org/#request)Header an, um das Senden bestimmter Header in einer CORS-Anforderung zu ermöglichen:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Um alle [Autorenanforderungsheader](https://www.w3.org/TR/cors/#author-request-headers) <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>zuzulassen, rufen Sie :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`wirkt sich auf Preflight-Anforderungen und den [Access-Control-Request-Headers-Header aus.](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)

Eine CORS Middleware-Richtlinie, die `WithHeaders` mit bestimmten Headern übereinstimmt, `Access-Control-Request-Headers` die von angegeben `WithHeaders`werden, ist nur möglich, wenn die in .

Betrachten Sie beispielsweise eine App, die wie folgt konfiguriert ist:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

CORS Middleware lehnt eine Preflight-Anforderung mit `Content-Language` dem folgenden Anforderungsheader ab, da `WithHeaders`([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nicht in aufgeführt ist:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Die App gibt eine *200 OK-Antwort* zurück, sendet die CORS-Header jedoch nicht zurück. Daher versucht der Browser nicht, die ursprungsübergreifende Anforderung zu verwenden.

### <a name="set-the-exposed-response-headers"></a>Festlegen der freiliegenden Antwortheader

Standardmäßig macht der Browser nicht alle Antwortheader für die App verfügbar. Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).

Die standardmäßig verfügbaren Antwortheader sind:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Die CORS-Spezifikation ruft diese Header *auf einfache Antwortheader*. Um andere Header für die App <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>verfügbar zu machen, rufen Sie :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Anmeldeinformationen in ursprungsübergreifenden Anforderungen

Anmeldeinformationen erfordern eine spezielle Behandlung in einer CORS-Anforderung. Standardmäßig sendet der Browser keine Anmeldeinformationen mit einer ursprungsübergreifenden Anforderung. Anmeldeinformationen umfassen Cookies und HTTP-Authentifizierungsschemata. Um Anmeldeinformationen mit einer ursprungsübergreifenden Anforderung `XMLHttpRequest.withCredentials` zu `true`senden, muss der Client auf festlegen.

Direkt `XMLHttpRequest` verwenden:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Verwenden von jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Verwenden der [Abruf-API](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Der Server muss die Anmeldeinformationen zulassen. Um ursprungsübergreifende Anmeldeinformationen zuzulassen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

Die HTTP-Antwort `Access-Control-Allow-Credentials` enthält einen Header, der dem Browser mitteilt, dass der Server Anmeldeinformationen für eine ursprungsübergreifende Anforderung zulässt.

Wenn der Browser Anmeldeinformationen sendet, die Antwort `Access-Control-Allow-Credentials` jedoch keinen gültigen Header enthält, macht der Browser die Antwort für die App nicht verfügbar, und die ursprungsübergreifende Anforderung schlägt fehl.

Das Zulassen ursprungsübergreifender Anmeldeinformationen stellt ein Sicherheitsrisiko dar. Eine Website in einer anderen Domäne kann die Anmeldeinformationen eines angemeldeten Benutzers im Namen des Benutzers ohne Wissen des Benutzers an die App senden. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Die CORS-Spezifikation gibt auch `"*"` an, dass das Festlegen `Access-Control-Allow-Credentials` von Ursprüngen auf (alle Ursprünge) ungültig ist, wenn der Header vorhanden ist.

<a name="pref"></a>

## <a name="preflight-requests"></a>Preflight-Anfragen

Bei einigen CORS-Anforderungen sendet [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) der Browser eine zusätzliche OPTIONS-Anforderung, bevor er die eigentliche Anforderung stellt. Diese Anforderung wird als [Preflight-Anforderung](https://developer.mozilla.org/docs/Glossary/Preflight_request)bezeichnet. Der Browser kann die Preflight-Anforderung überspringen, wenn ***alle*** folgenden Bedingungen erfüllt sind:

* Die Anforderungsmethode ist GET, HEAD oder POST.
* Die App stellt keine anderen Anforderungsheader `Content-Language` `Content-Type`als `Accept`, `Accept-Language`, , oder `Last-Event-ID`fest.
* Der `Content-Type` Header, sofern festgelegt, hat einen der folgenden Werte:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Die Regel für Anforderungsheader, die für die Clientanforderung festgelegt `setRequestHeader` wurden, gilt für Header, die die App durch Aufrufen des `XMLHttpRequest` Objekts festlegt. Die CORS-Spezifikation ruft diese [Header-Author-Anforderungsheader](https://www.w3.org/TR/cors/#author-request-headers)auf. Die Regel gilt nicht für Header, die der `User-Agent` `Host`Browser `Content-Length`festlegen kann, z. B. , oder .

Im Folgenden finden Sie eine Beispielantwort, die der Preflight-Anforderung ähnelt, die von der Schaltfläche **[Test test]** im Abschnitt [Test CORS](#testc) dieses Dokuments gestellt wurde.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

Die Preflight-Anforderung verwendet die [HTTP OPTIONS-Methode.](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) Es kann die folgenden Header enthalten:

* [Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Die HTTP-Methode, die für die eigentliche Anforderung verwendet wird.
* [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Eine Liste von Anforderungsheadern, die die App für die eigentliche Anforderung festlegt. Wie bereits erwähnt, enthält dies keine Header, die `User-Agent`der Browser festlegt, z. B. .
* [Access-Control-Allow-Methoden](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Wenn die Preflight-Anforderung abgelehnt wird, gibt die App eine `200 OK` Antwort zurück, legt jedoch die CORS-Header nicht fest. Daher versucht der Browser nicht, die ursprungsübergreifende Anforderung zu verwenden. Ein Beispiel für eine abgelehnte Preflight-Anforderung finden Sie im Abschnitt [TEST CORS](#testc) dieses Dokuments.

Mit den F12-Tools zeigt die Konsolen-App je nach Browser einen ähnlichen Fehler wie einen der folgenden Optionen an:

* Firefox: Cross-Origin-Anforderung blockiert: Die Richtlinie für denselben `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`Ursprung verbietet das Lesen der Remoteressource bei . (Grund: CORS-Anforderung war nicht erfolgreich). [Weitere Informationen](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Chromium-basiert: Der Zugriffhttps://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5auf abruf en ' from origin 'https://cors3.azurewebsites.netwurde durch die CORS-Richtlinie blockiert: Die Antwort auf die Preflight-Anforderung besteht keine Zugriffssteuerungsüberprüfung: Es ist kein 'Access-Control-Allow-Origin'-Header auf der angeforderten Ressource vorhanden. Falls eine opake Antwort Ihre Anforderungen erfüllt, legen Sie den Modus der Anforderung auf 'no-cors' fest, um CORS für den Ressourcenabruf zu deaktivieren.

Um bestimmte Header zuzulassen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Um alle [Autorenanforderungsheader](https://www.w3.org/TR/cors/#author-request-headers) <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>zuzulassen, rufen Sie :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Browser sind nicht konsistent, wie `Access-Control-Request-Headers`sie festlegen. Wenn beides:

* Header werden auf etwas anderes als`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>heißt: Schließen Sie `Accept` `Content-Type`mindestens `Origin`, und ein, sowie alle benutzerdefinierten Header, die Sie unterstützen möchten.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Automatischer Preflight-Anforderungscode

Wenn die CORS-Richtlinie angewendet wird, wird:

* Global durch `app.UseCors` Aufrufen `Startup.Configure`von .
* Verwenden `[EnableCors]` des Attributs.

ASP.NET Core reagiert auf die Preflight OPTIONS-Anforderung.

Das Aktivieren von CORS pro `RequireCors` Endpunkt mithilfe der aktuellen Verwendung unterstützt ***keine*** automatischen Preflight-Anforderungen.

Der Abschnitt [TEST CORS](#testc) dieses Dokuments veranschaulicht dieses Verhalten.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[HttpOptions]-Attribut für Preflight-Anforderungen

Wenn CORS mit der entsprechenden Richtlinie aktiviert ist, reagiert ASP.NET Core in der Regel automatisch auf CORS-Preflight-Anforderungen. In einigen Szenarien ist dies möglicherweise nicht der Fall. Verwenden von [CORS mit Endpunktrouting](#ecors).

Der folgende Code verwendet das [Attribut [HttpOptions],](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) um Endpunkte für OPTIONS-Anforderungen zu erstellen:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Anweisungen zum Testen des vorherigen Codes finden Sie unter Testen von [CORS mit Endpunktrouting und [HttpOptions].](#tcer)

### <a name="set-the-preflight-expiration-time"></a>Festlegen der Ablaufzeit vor dem Flug

Der `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann. Um diesen Header <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>festzulegen, rufen Sie :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Funktionsweise von CORS

In diesem Abschnitt wird beschrieben, was in einer [CORS-Anforderung](https://developer.mozilla.org/docs/Web/HTTP/CORS) auf der Ebene der HTTP-Nachrichten geschieht.

* CORS ist **kein** Sicherheitsfeature. CORS ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie mit demselben Ursprung zu entspannen.
  * Beispielsweise könnte ein böswilliger Akteur [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website verwenden und eine websiteübergreifende Anforderung an seine CORS-fähige Website ausführen, um Informationen zu stehlen.
* Eine API ist nicht sicherer, wenn CORS zugelassen wird.
  * Es liegt am Client (Browser), CORS zu erzwingen. Der Server führt die Anforderung aus und gibt die Antwort zurück, es ist der Client, der einen Fehler zurückgibt und die Antwort blockiert. Eines der folgenden Tools zeigt z. B. die Serverantwort an:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Ein Webbrowser, indem er die URL in die Adressleiste eingibt.
* Es ist eine Möglichkeit für einen Server, Browsern die Ausführung einer [ursprungsübergreifenden XHR-](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) oder [Fetch-API-Anforderung](https://developer.mozilla.org/docs/Web/API/Fetch_API) zu ermöglichen, die andernfalls verboten wäre.
  * Browser ohne CORS können keine ursprungsübergreifenden Anforderungen verarbeiten. Vor CORS wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen. JSONP verwendet XHR nicht, es `<script>` verwendet das Tag, um die Antwort zu empfangen. Skripts dürfen ursprungsübergreifend geladen werden.

Die [CORS-Spezifikation](https://www.w3.org/TR/cors/) führte mehrere neue HTTP-Header ein, die ursprungsübergreifende Anforderungen ermöglichen. Wenn ein Browser CORS unterstützt, werden diese Header automatisch für ursprungsübergreifende Anforderungen festgelegt. Benutzerdefinierter JavaScript-Code ist zum Aktivieren von CORS nicht erforderlich.

Die [PUT-Testschaltfläche](https://cors3.azurewebsites.net/test) auf dem [bereitgestellten Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)

Im Folgenden finden Sie ein Beispiel für eine ursprungsübergreifende Anforderung von der [Schaltfläche Wertetest](https://cors3.azurewebsites.net/) an `https://cors1.azurewebsites.net/api/values`. Der `Origin` Header:

* Stellt die Domäne der Site bereit, die die Anforderung stellt.
* Ist erforderlich und muss sich vom Host unterscheiden.

**Allgemeine Kopfzeilen**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**Antwortheader**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**Anforderungsheader**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

Bei `OPTIONS` Anforderungen legt der Server den **Headerheader "Antwort"** `Access-Control-Allow-Origin: {allowed origin}` in der Antwort fest. Die Schaltflächenanforderung `OPTIONS` "Das bereitgestellte [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [[EnableCors]](https://cors1.azurewebsites.net/test?number=2) löschen enthält beispielsweise die folgenden Header:

**Allgemeine Kopfzeilen**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**Antwortheader**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**Anforderungsheader**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

In den vorherigen **Response-Headern**legt der Server den [Access-Control-Allow-Origin-Header](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) in der Antwort fest. Der `https://cors1.azurewebsites.net` Wert dieses Headers entspricht dem `Origin` Header aus der Anforderung.

Wenn <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> aufgerufen wird, wird der `Access-Control-Allow-Origin: *`, der Platzhalterwert, zurückgegeben. `AllowAnyOrigin`ermöglicht jede Herkunft.

Wenn die Antwort den `Access-Control-Allow-Origin` Header nicht enthält, schlägt die ursprungsübergreifende Anforderung fehl. Insbesondere lässt der Browser die Anforderung nicht zu. Selbst wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App bereit.

<a name="options"></a>

### <a name="display-options-requests"></a>OPTIONS-Anforderungen anzeigen

Standardmäßig zeigen die Chrome- und Edge-Browser keine OPTIONS-Anforderungen auf der Registerkarte Netzwerk der F12-Tools an. So zeigen Sie OPTIONS-Anforderungen in diesen Browsern an:

* `chrome://flags/#out-of-blink-cors` oder `edge://flags/#out-of-blink-cors`
* deaktivieren Sie das Flag.
* „Neu starten“.

Firefox zeigt OPTIONS-Anforderungen standardmäßig an.

## <a name="cors-in-iis"></a>CORS in IIS

Bei der Bereitstellung in IIS muss CORS vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist. Um dieses Szenario zu unterstützen, muss das [IIS CORS-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die App installiert und konfiguriert werden.

<a name="testc"></a>

## <a name="test-cors"></a>Testen von CORS

Der [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) enthält Code zum Testen von CORS. Informationen [zum Herunterladen](xref:index#how-to-download-a-sample). Das Beispiel ist ein API-Projekt mit Razor Pages hinzugefügt:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`sollte nur zum Testen einer Beispiel-App verwendet werden, die dem [Download-Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)ähnelt.

Im `ValuesController` Folgenden werden die Endpunkte zum Testen angezeigt:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) wird vom [Paket Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet bereitgestellt und zeigt Routeninformationen an.

Testen Sie den vorherigen Beispielcode mit einem der folgenden Ansätze:

* Verwenden Sie die [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)bereitgestellte Beispiel-App unter . Es ist nicht erforderlich, das Beispiel herunterzuladen.
* Führen Sie `dotnet run` das Beispiel mit `https://localhost:5001`der Standard-URL von aus.
* Führen Sie das Beispiel aus Visual Studio aus, wobei der `https://localhost:44398`Port für eine URL von auf 44398 festgelegt ist.

Verwenden eines Browsers mit den F12-Tools:

* Wählen Sie die Schaltfläche **Werte aus,** und überprüfen Sie die Kopfzeilen auf der Registerkarte **Netzwerk.**
* Wählen Sie die **PUT-Testschaltfläche** aus. Siehe Anzeigen von [OPTIONS-Anforderungen](#options) für Anweisungen zum Anzeigen der OPTIONS-Anforderung. Der **PUT-Test** erstellt zwei Anforderungen, eine OPTIONS-Preflight-Anforderung und die PUT-Anforderung.
* Wählen **`GetValues2 [DisableCors]`** Sie die Schaltfläche aus, um eine fehlgeschlagene CORS-Anforderung auszulösen. Wie im Dokument erwähnt, gibt die Antwort 200 Erfolge zurück, aber die CORS-Anforderung wird nicht gestellt. Wählen Sie die Registerkarte **Konsole** aus, um den CORS-Fehler anzuzeigen. Je nach Browser wird ein ähnlicher Fehler wie folgt angezeigt:

     Der Zugriff `'https://cors1.azurewebsites.net/api/values/GetValues2'` auf `'https://cors3.azurewebsites.net'` abrufen vom Ursprung wurde durch die CORS-Richtlinie blockiert: In der angeforderten Ressource ist kein 'Access-Control-Allow-Origin'-Header vorhanden. Falls eine opake Antwort Ihre Anforderungen erfüllt, legen Sie den Modus der Anforderung auf 'no-cors' fest, um CORS für den Ressourcenabruf zu deaktivieren.
     
CORS-fähige Endpunkte können mit einem Tool getestet werden, z. B. [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)oder [Postman](https://www.getpostman.com/). Bei Verwendung eines Tools muss der Ursprung `Origin` der vom Header angegebenen Anforderung vom Host, der die Anforderung empfängt, abweichen. Wenn die Anforderung nicht *ursprungsübergreifend* ist, basiert `Origin` sie auf dem Wert des Headers:

* CORS Middleware muss die Anforderung nicht verarbeiten.
* CORS-Header werden in der Antwort nicht zurückgegeben.

Der folgende `curl` Befehl verwendet, um eine OPTIONS-Anforderung mit Informationen ausstellen:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Testen von CORS mit Endpunktrouting und [HttpOptions]

Das Aktivieren von CORS pro `RequireCors` Endpunkt mithilfe der aktuellen Verwendung unterstützt ***keine*** [automatischen Preflight-Anforderungen](#apf). Berücksichtigen Sie den folgenden Code, der [Endpunktrouting verwendet, um CORS zu aktivieren:](#ecors)

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

Im `TodoItems1Controller` Folgenden finden Sie Endpunkte zum Testen:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Testen Sie den vorherigen Code auf der [Testseite](https://cors1.azurewebsites.net/test?number=1) des bereitgestellten [Beispiels](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).

Die Schaltflächen **[EnableCors]** und **GET [EnableCors]** sind `[EnableCors]` erfolgreich, da die Endpunkte preflight-Anforderungen haben und darauf reagieren. Die anderen Endpunkte schlägt fehl. Die **GET-Schaltfläche** schlägt fehl, da [JavaScript:](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Das `TodoItems2Controller` Folgende stellt ähnliche Endpunkte bereit, enthält jedoch expliziten Code, um auf OPTIONS-Anforderungen zu reagieren:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Testen Sie den vorherigen Code auf der [Testseite](https://cors1.azurewebsites.net/test?number=2) des bereitgestellten Beispiels. Wählen Sie in der **Dropdown-Liste Controller** **Preflight** und set **Controller**aus. Alle CORS-Aufrufe `TodoItems2Controller` an die Endpunkte sind erfolgreich.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Erste Schritte mit dem IIS CORS-Modul](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Dieser Artikel zeigt, wie CORS in einer ASP.NET Core-App aktiviert wird.

Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als die, die die Webseite bereitgestellt hat, stellt. Diese Einschränkung wird als *Richtlinie mit gleichem Ursprung*bezeichnet. Die Richtlinie mit demselben Ursprung verhindert, dass eine böswillige Website vertrauliche Daten von einer anderen Website liest. Manchmal möchten Sie möglicherweise zulassen, dass andere Websites ursprungsübergreifende Anforderungen an Ihre App stellen. Weitere Informationen finden Sie im [Mozilla CORS-Artikel](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):

* Ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie mit demselben Ursprung zu entspannen.
* Ist **kein** Sicherheitsmerkmal, CORS entspannt die Sicherheit. Eine API ist nicht sicherer, wenn CORS zugelassen wird. Weitere Informationen finden Sie unter Funktionsweise von [CORS](#how-cors).
* Ermöglicht einem Server, einige ursprungsübergreifende Anforderungen explizit zuzulassen, während andere abgelehnt werden.
* Ist sicherer und flexibler als frühere Techniken, wie z. B. [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Gleicher Ursprung

Zwei URLs haben denselben Ursprung, wenn sie identische Schemata, Hosts und Ports haben ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Diese beiden URLs haben den gleichen Ursprung:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:

* `https://example.net`&ndash; Verschiedene Domäne
* `https://www.example.com/foo.html`&ndash; Verschiedene Subdomain
* `http://example.com/foo.html`&ndash; Unterschiedlicheregelung
* `https://example.com:9000/foo.html`&ndash; Verschiedene randder Hafen

Internet Explorer berücksichtigt den Port nicht beim Vergleich der Ursprünge.

## <a name="cors-with-named-policy-and-middleware"></a>CORS mit benannter Richtlinie und Middleware

CORS Middleware verarbeitet ursprungsübergreifende Anforderungen. Der folgende Code aktiviert CORS für die gesamte App mit dem angegebenen Ursprung:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Der vorangehende Code:

* Legt den Richtliniennamen\_auf "myAllowSpecificOrigins" fest. Der Richtlinienname ist willkürlich.
* Ruft <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> die Erweiterungsmethode auf, die CORS aktiviert.
* Aufrufe <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Der Lambda <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> nimmt ein Objekt. [Konfigurationsoptionen](#cors-policy-options), `WithOrigins`z. B. , werden weiter unten in diesem Artikel beschrieben.

Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Methodenaufruf fügt DEM Dienstcontainer der App CORS-Dienste hinzu:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Weitere Informationen finden Sie unter [CORS-Richtlinienoptionen](#cpo) in diesem Dokument .

Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methode kann Methoden verketten, wie im folgenden Code gezeigt:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Hinweis: Die URL darf **keinen** nachgestellten Schrägstrich (`/`) enthalten. Wenn die URL `/`mit beendet `false` wird, wird der Vergleich zurückgegeben, und es wird kein Header zurückgegeben.

Der folgende Code wendet CORS-Richtlinien auf alle App-Endpunkte über CORS Middleware an:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Hinweis: `UseCors` muss vor `UseMvc`aufgerufen werden.

Weitere Informationen finden Sie unter Aktivieren von [CORS in Razor Pages, Controllern und Aktionsmethoden](#ecors) zum Anwenden der CORS-Richtlinie auf Seiten-/Controller-/Aktionsebene.

Anweisungen zum Testen von Code ähnlich dem vorherigen Code finden Sie unter [Test-CORS.](#test)

## <a name="enable-cors-with-attributes"></a>CORS mit Attributen aktivieren

Das [ &lbrack;&rbrack; EnableCors-Attribut](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) bietet eine Alternative zum globalen Anwenden von CORS. Das `[EnableCors]` Attribut aktiviert CORS für ausgewählte Endpunkte und nicht für alle Endpunkte.

Verwenden `[EnableCors]` Sie diese Option, um die Standardrichtlinie und `[EnableCors("{Policy String}")]` eine Richtlinie anzugeben.

Das `[EnableCors]` Attribut kann angewendet werden auf:

* Razor Page`PageModel`
* Controller
* Controller-Aktionsmethode

Sie können mit dem `[EnableCors]` Attribut unterschiedliche Richtlinien auf Controller/Seitenmodell/Aktion anwenden. Wenn `[EnableCors]` das Attribut auf eine Controller-/Seitenmodell-/Aktionsmethode angewendet wird und CORS in Middleware aktiviert ist, werden ***beide*** Richtlinien angewendet. Es wird empfohlen, richtlinien ***nicht*** zu kombinieren. Verwenden `[EnableCors]` Sie das Attribut oder die Middleware, ***nicht beides**. Bei `[EnableCors]`Verwendung von definieren Sie **keine** Standardrichtlinie.

Der folgende Code wendet für jede Methode eine andere Richtlinie an:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Der folgende Code erstellt eine CORS-Standardrichtlinie und eine Richtlinie mit dem Namen: `"AnotherPolicy"`

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>DEAKTIVIEREN von CORS

Das [ &lbrack;&rbrack; DisableCors-Attribut](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) deaktiviert CORS für den Controller/Seitenmodell/die Aktion.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS-Richtlinienoptionen

In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer CORS-Richtlinie festgelegt werden können:

* [Festlegen der zulässigen Ursprünge](#set-the-allowed-origins)
* [Festlegen der zulässigen HTTP-Methoden](#set-the-allowed-http-methods)
* [Festlegen der zulässigen Anforderungsheader](#set-the-allowed-request-headers)
* [Festlegen der freiliegenden Antwortheader](#set-the-exposed-response-headers)
* [Anmeldeinformationen in ursprungsübergreifenden Anforderungen](#credentials-in-cross-origin-requests)
* [Festlegen der Ablaufzeit vor dem Flug](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>wird in `Startup.ConfigureServices`aufgerufen. Bei einigen Optionen kann es hilfreich sein, zuerst den Abschnitt ["Funktionsweise von CORS"](#how-cors) zu lesen.

## <a name="set-the-allowed-origins"></a>Festlegen der zulässigen Ursprünge

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Ermöglicht CORS-Anforderungen aller Herkunftsherkunft mit einem beliebigen Schema (`http` oder `https`). `AllowAnyOrigin`ist unsicher, da *jede Website* ursprungsübergreifende Anforderungen an die App stellen kann.

> [!NOTE]
> Angeben `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu standortübergreifenden Anforderungsfälschungen führen. Geben Sie für eine sichere App eine genaue Liste der Ursprünge an, wenn der Client sich selbst für den Zugriff auf Serverressourcen autorisieren muss.

`AllowAnyOrigin`wirkt sich auf `Access-Control-Allow-Origin` Preflight-Anforderungen und den Header aus. Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; Legt <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> die Eigenschaft der Richtlinie als funktion fest, die es dem Ursprung ermöglicht, eine konfigurierte Platzhalterdomäne abzugleichen, wenn ausgewertet wird, ob der Ursprung zulässig ist.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Festlegen der zulässigen HTTP-Methoden

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Erlaubt jede HTTP-Methode:
* Beeinflusst Preflight-Anforderungen `Access-Control-Allow-Methods` und den Header. Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)

### <a name="set-the-allowed-request-headers"></a>Festlegen der zulässigen Anforderungsheader

Um das Senden bestimmter Header in einer CORS-Anforderung zuzulassen, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> rufen Sie die *zulässigen*Header an, um das Senden bestimmter Header in einer CORS-Anforderung zu ermöglichen:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Um alle Autorenanforderungsheader <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>zuzulassen, rufen Sie :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Diese Einstellung wirkt sich `Access-Control-Request-Headers` auf Preflight-Anforderungen und den Header aus. Weitere Informationen finden Sie im Abschnitt [Preflight-Anforderungen.](#preflight-requests)

CORS Middleware ermöglicht immer, `Access-Control-Request-Headers` dass vier Header in der gesendet werden, unabhängig von den in CorsPolicy.Headers konfigurierten Werten. Diese Liste der Header enthält:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Betrachten Sie beispielsweise eine App, die wie folgt konfiguriert ist:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS Middleware reagiert erfolgreich auf eine Preflight-Anforderung `Content-Language` mit dem folgenden Anforderungsheader, da immer auf der Whitelist steht:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Festlegen der freiliegenden Antwortheader

Standardmäßig macht der Browser nicht alle Antwortheader für die App verfügbar. Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).

Die standardmäßig verfügbaren Antwortheader sind:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Die CORS-Spezifikation ruft diese Header *auf einfache Antwortheader*. Um andere Header für die App <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>verfügbar zu machen, rufen Sie :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Anmeldeinformationen in ursprungsübergreifenden Anforderungen

Anmeldeinformationen erfordern eine spezielle Behandlung in einer CORS-Anforderung. Standardmäßig sendet der Browser keine Anmeldeinformationen mit einer ursprungsübergreifenden Anforderung. Anmeldeinformationen umfassen Cookies und HTTP-Authentifizierungsschemata. Um Anmeldeinformationen mit einer ursprungsübergreifenden Anforderung `XMLHttpRequest.withCredentials` zu `true`senden, muss der Client auf festlegen.

Direkt `XMLHttpRequest` verwenden:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Verwenden von jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Verwenden der [Abruf-API](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Der Server muss die Anmeldeinformationen zulassen. Um ursprungsübergreifende Anmeldeinformationen zuzulassen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

Die HTTP-Antwort `Access-Control-Allow-Credentials` enthält einen Header, der dem Browser mitteilt, dass der Server Anmeldeinformationen für eine ursprungsübergreifende Anforderung zulässt.

Wenn der Browser Anmeldeinformationen sendet, die Antwort `Access-Control-Allow-Credentials` jedoch keinen gültigen Header enthält, macht der Browser die Antwort für die App nicht verfügbar, und die ursprungsübergreifende Anforderung schlägt fehl.

Das Zulassen ursprungsübergreifender Anmeldeinformationen stellt ein Sicherheitsrisiko dar. Eine Website in einer anderen Domäne kann die Anmeldeinformationen eines angemeldeten Benutzers im Namen des Benutzers ohne Wissen des Benutzers an die App senden. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

Die CORS-Spezifikation gibt auch `"*"` an, dass das Festlegen `Access-Control-Allow-Credentials` von Ursprüngen auf (alle Ursprünge) ungültig ist, wenn der Header vorhanden ist.

### <a name="preflight-requests"></a>Preflight-Anfragen

Bei einigen CORS-Anforderungen sendet der Browser eine zusätzliche Anforderung, bevor er die eigentliche Anforderung stellt. Diese Anforderung wird als *Preflight-Anforderung*bezeichnet. Der Browser kann die Preflight-Anforderung überspringen, wenn die folgenden Bedingungen zutreffen:

* Die Anforderungsmethode ist GET, HEAD oder POST.
* Die App stellt keine anderen Anforderungsheader `Content-Language` `Content-Type`als `Accept`, `Accept-Language`, , oder `Last-Event-ID`fest.
* Der `Content-Type` Header, sofern festgelegt, hat einen der folgenden Werte:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Die Regel für Anforderungsheader, die für die Clientanforderung festgelegt `setRequestHeader` wurden, gilt für Header, die die App durch Aufrufen des `XMLHttpRequest` Objekts festlegt. Die CORS-Spezifikation ruft diese *Header-Author-Anforderungsheader*auf. Die Regel gilt nicht für Header, die der `User-Agent` `Host`Browser `Content-Length`festlegen kann, z. B. , oder .

Im Folgenden finden Sie ein Beispiel für eine Preflight-Anforderung:

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

Die Pre-Flight-Anforderung verwendet die HTTP OPTIONS-Methode. Es enthält zwei spezielle Header:

* `Access-Control-Request-Method`: Die HTTP-Methode, die für die eigentliche Anforderung verwendet wird.
* `Access-Control-Request-Headers`: Eine Liste der Anforderungsheader, die die App für die eigentliche Anforderung festlegt. Wie bereits erwähnt, enthält dies keine Header, die `User-Agent`der Browser festlegt, z. B. .

<!-- I think this needs to be removed, was put here accidently -->

Wenn CORS mit der entsprechenden Richtlinie aktiviert ist, reagiert ASP.NET Core in der Regel automatisch auf CORS-Preflight-Anforderungen. Siehe [[HttpOptions]-Attribut für Preflight-Anforderungen](#pro).

Eine CORS-Preflight-Anforderung `Access-Control-Request-Headers` kann einen Header enthalten, der dem Server die Header angibt, die mit der eigentlichen Anforderung gesendet werden.

Um bestimmte Header zuzulassen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Um alle Autorenanforderungsheader <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>zuzulassen, rufen Sie :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Browser sind nicht vollständig konsistent in `Access-Control-Request-Headers`der Art und Weise, wie sie festlegen. Wenn Sie Header auf etwas `"*"` anderes <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>als (oder verwenden `Accept` `Content-Type`) `Origin`festlegen, sollten Sie mindestens , und , sowie alle benutzerdefinierten Header einschließen, die Sie unterstützen möchten.

Im Folgenden finden Sie eine Beispielantwort auf die Preflight-Anforderung (vorausgesetzt, der Server lässt die Anforderung zu):

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

Die Antwort `Access-Control-Allow-Methods` enthält einen Header, der die `Access-Control-Allow-Headers` zulässigen Methoden auflistet, und optional einen Header, der die zulässigen Header auflistet. Wenn die Preflight-Anforderung erfolgreich ist, sendet der Browser die eigentliche Anforderung.

Wenn die Preflight-Anforderung abgelehnt wird, gibt die App eine *200 OK-Antwort* zurück, sendet die CORS-Header jedoch nicht zurück. Daher versucht der Browser nicht, die ursprungsübergreifende Anforderung zu verwenden.

### <a name="set-the-preflight-expiration-time"></a>Festlegen der Ablaufzeit vor dem Flug

Der `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann. Um diesen Header <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>festzulegen, rufen Sie :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Funktionsweise von CORS

In diesem Abschnitt wird beschrieben, was in einer [CORS-Anforderung](https://developer.mozilla.org/docs/Web/HTTP/CORS) auf der Ebene der HTTP-Nachrichten geschieht.

* CORS ist **kein** Sicherheitsfeature. CORS ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie mit demselben Ursprung zu entspannen.
  * Beispielsweise könnte ein böswilliger Akteur [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website verhindern und eine websiteübergreifende Anforderung an seine CORS-fähige Website ausführen, um Informationen zu stehlen.
* Ihre API ist nicht sicherer, wenn CORS zugelassen wird.
  * Es liegt am Client (Browser), CORS zu erzwingen. Der Server führt die Anforderung aus und gibt die Antwort zurück, es ist der Client, der einen Fehler zurückgibt und die Antwort blockiert. Eines der folgenden Tools zeigt z. B. die Serverantwort an:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Ein Webbrowser, indem er die URL in die Adressleiste eingibt.
* Es ist eine Möglichkeit für einen Server, Browsern die Ausführung einer [ursprungsübergreifenden XHR-](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) oder [Fetch-API-Anforderung](https://developer.mozilla.org/docs/Web/API/Fetch_API) zu ermöglichen, die andernfalls verboten wäre.
  * Browser (ohne CORS) können keine ursprungsübergreifenden Anforderungen erfüllen. Vor CORS wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen. JSONP verwendet XHR nicht, es `<script>` verwendet das Tag, um die Antwort zu empfangen. Skripts dürfen ursprungsübergreifend geladen werden.

Die [CORS-Spezifikation](https://www.w3.org/TR/cors/) führte mehrere neue HTTP-Header ein, die ursprungsübergreifende Anforderungen ermöglichen. Wenn ein Browser CORS unterstützt, werden diese Header automatisch für ursprungsübergreifende Anforderungen festgelegt. Benutzerdefinierter JavaScript-Code ist zum Aktivieren von CORS nicht erforderlich.

Im Folgenden finden Sie ein Beispiel für eine ursprungsübergreifende Anforderung. Der `Origin` Header stellt die Domäne der Site bereit, die die Anforderung stellt. Der `Origin` Header ist erforderlich und muss sich vom Host unterscheiden.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Wenn der Server die Anforderung `Access-Control-Allow-Origin` zulässt, legt er den Header in der Antwort fest. Der Wert dieses Headers `Origin` entspricht entweder dem Header `"*"`aus der Anforderung oder ist der Platzhalterwert , was bedeutet, dass jeder Ursprung zulässig ist:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Wenn die Antwort den `Access-Control-Allow-Origin` Header nicht enthält, schlägt die ursprungsübergreifende Anforderung fehl. Insbesondere lässt der Browser die Anforderung nicht zu. Selbst wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App bereit.

<a name="test"></a>

## <a name="test-cors"></a>Testen von CORS

So testen Sie CORS:

1. [Erstellen Sie ein API-Projekt](xref:tutorials/first-web-api). Alternativ können Sie [das Beispiel herunterladen.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)
1. Aktivieren Sie CORS mithilfe eines der Ansätze in diesem Dokument. Beispiel:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`sollte nur zum Testen einer Beispiel-App verwendet werden, die dem [Download-Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)ähnelt.

1. Erstellen Sie ein Web-App-Projekt (Razor Pages oder MVC). Im Beispiel werden Razor-Seiten verwendet. Sie können die Web-App in derselben Lösung wie das API-Projekt erstellen.
1. Fügen Sie der Datei *Index.cshtml* den folgenden hervorgehobenen Code hinzu:

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. Ersetzen Sie im `url: 'https://<web app>.azurewebsites.net/api/values/1',` vorherigen Code durch die URL zur bereitgestellten App.
1. Stellen Sie das API-Projekt bereit. Stellen Sie z. [B. in Azure bereit.](xref:host-and-deploy/azure-apps/index)
1. Führen Sie die Razor Pages oder MVC-App vom Desktop aus aus und klicken Sie auf die Schaltfläche **Testen.** Verwenden Sie die F12-Tools, um Fehlermeldungen zu überprüfen.
1. Entfernen Sie den `WithOrigins` Ursprung des lokalen Hosts aus, und stellen Sie die App bereit. Alternativ können Sie die Client-App mit einem anderen Port ausführen. Führen Sie z. B. von Visual Studio aus.
1. Testen Sie mit der Client-App. CORS-Fehler geben einen Fehler zurück, aber die Fehlermeldung ist für JavaScript nicht verfügbar. Verwenden Sie die Registerkarte Konsole in den F12-Tools, um den Fehler anzuzeigen. Je nach Browser erhalten Sie einen Fehler (in der F12-Tools-Konsole), der den folgenden ähnelt:

   * Verwenden von Microsoft Edge:

     **SEC7120: [CORS] `https://localhost:44375` Der Ursprung `https://localhost:44375` wurde im Access-Control-Allow-Origin-Antwortheader für ursprungsübergreifende Ressource unter`https://webapi.azurewebsites.net/api/values/1`**

   * Verwenden von Chrome:

     **Der Zugriff auf `https://webapi.azurewebsites.net/api/values/1` XMLHttpRequest vom Ursprung `https://localhost:44375` wurde durch die CORS-Richtlinie blockiert: In der angeforderten Ressource ist kein 'Access-Control-Allow-Origin'-Header vorhanden.**
     
CORS-fähige Endpunkte können mit einem Tool getestet werden, z. B. [Fiddler](https://www.telerik.com/fiddler) oder [Postman](https://www.getpostman.com/). Bei Verwendung eines Tools muss der Ursprung `Origin` der vom Header angegebenen Anforderung vom Host, der die Anforderung empfängt, abweichen. Wenn die Anforderung nicht *ursprungsübergreifend* ist, basiert `Origin` sie auf dem Wert des Headers:

* CORS Middleware muss die Anforderung nicht verarbeiten.
* CORS-Header werden in der Antwort nicht zurückgegeben.

## <a name="cors-in-iis"></a>CORS in IIS

Bei der Bereitstellung in IIS muss CORS vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist. Um dieses Szenario zu unterstützen, muss das [IIS CORS-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die App installiert und konfiguriert werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Erste Schritte mit dem IIS CORS-Modul](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
