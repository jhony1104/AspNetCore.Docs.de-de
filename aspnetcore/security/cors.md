---
title: Aktivieren von Cross-Origin-Anforderungen (cors) in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie cors als Standard zum Zulassen oder ablehnen von Ursprungs übergreifenden Anforderungen in einer ASP.net Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: 57098be73164c71d1b0d1fe2f3aee7ec41a32346
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727313"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Aktivieren von Cross-Origin-Anforderungen (cors) in ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Artikel wird gezeigt, wie Sie cors in einer ASP.net Core-App aktivieren.

Die Browser Sicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne sendet, als die, die die Webseite bereitgestellt hat. Diese Einschränkung wird als *Richtlinie für denselben Ursprung*bezeichnet. Die Richtlinie für denselben Ursprung verhindert, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest. Manchmal möchten Sie möglicherweise, dass andere Websites Ursprungs übergreifende Anforderungen an Ihre APP stellen. Weitere Informationen finden Sie im [Artikel zu Mozilla cors](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).

[Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) (cors):

* Ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.
* Bei handelt es sich **nicht** um ein Sicherheits Feature, sondern um die Sicherheit von cors. Eine API wird durch das Zulassen von cors nicht sicherer. Weitere Informationen finden Sie unter [Funktionsweise von cors](#how-cors).
* Ermöglicht einem Server das explizite zulassen einiger Ursprungs übergreifender Anforderungen, während andere abgelehnt werden.
* Ist sicherer und flexibler als frühere Techniken, z. b. [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Gleicher Ursprung

Zwei URLs haben denselben Ursprung, wenn Sie identische Schemas, Hosts und Ports aufweisen ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Diese beiden URLs haben denselben Ursprung:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:

* `https://example.net` &ndash; andere Domäne
* `https://www.example.com/foo.html` &ndash; andere Unterdomäne
* `http://example.com/foo.html` &ndash; anderes Schema
* `https://example.com:9000/foo.html` &ndash; anderer Port

Internet Explorer berücksichtigt den Port nicht, wenn Ursprünge verglichen werden.

## <a name="cors-with-named-policy-and-middleware"></a>Cors mit benannten Richtlinie und Middleware

Cors-Middleware verarbeitet Ursprungs übergreifende Anforderungen. Der folgende Code aktiviert cors für die gesamte App mit dem angegebenen Ursprung:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Der vorangehende Code:

* Legt den Richtlinien Namen auf "\_myallowspecificorigins" fest. Der Richtlinien Name ist willkürlich.
* Ruft die <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>-Erweiterungsmethode auf, die cors aktiviert.
* Ruft <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)auf. Der Lambda-Ausdruck nimmt ein <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Objekt an. [Konfigurationsoptionen](#cors-policy-options), wie z. b. `WithOrigins`, werden weiter unten in diesem Artikel beschrieben.

Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>-Methodenaufrufe fügt cors-Dienste zum Dienst Container der APP hinzu:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Weitere Informationen finden Sie unter [cors-Richtlinien Optionen](#cpo) in diesem Dokument.

Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>-Methode kann Methoden verketten, wie im folgenden Code gezeigt:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Hinweis: die URL darf **keinen** nachgestellten Schrägstrich (`/`) enthalten. Wenn die URL mit `/`beendet wird, gibt der Vergleich `false` zurück, und es wird kein-Header zurückgegeben.

::: moniker range=">= aspnetcore-3.0"

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a>Anwenden von cors-Richtlinien auf alle Endpunkte

Mit dem folgenden Code werden cors-Richtlinien auf alle App-Endpunkte über cors-Middleware angewendet:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code ommitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code ommited.
}
```

> [!WARNING]
> Beim Endpunkt Routing muss die cors-Middleware für die Ausführung zwischen den Aufrufen von `UseRouting` und `UseEndpoints`konfiguriert werden. Eine falsche Konfiguration führt dazu, dass die Middleware nicht mehr ordnungsgemäß funktioniert.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
Mit dem folgenden Code werden cors-Richtlinien auf alle App-Endpunkte über cors-Middleware angewendet:
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

::: moniker-end

Weitere Informationen finden Sie unter [Aktivieren von cors in Razor Pages, Controllern und Aktionsmethoden](#ecors) zum Anwenden der cors-Richtlinie auf Seiten-/Controller-/Aktions

Anweisungen zum Testen des vorangehenden Codes finden Sie unter [Test-cors](#test) .

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a>Aktivieren von cors mit Endpunkt Routing

Mit dem Endpunkt Routing kann cors pro Endpunkt mithilfe des `RequireCors` Satzes von Erweiterungs Methoden aktiviert werden.

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

Ebenso kann cors auch für alle Controller aktiviert werden:

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a>Aktivieren von cors mit Attributen

Das [&lbrack;enablecors-&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) Attribut bietet eine Alternative zum globalen Anwenden von cors. Das `[EnableCors]`-Attribut aktiviert cors für ausgewählte Endpunkte anstelle aller Endpunkte.

Verwenden Sie `[EnableCors]`, um die Standard Richtlinie anzugeben, und `[EnableCors("{Policy String}")]`, um eine Richtlinie anzugeben.

Das `[EnableCors]`-Attribut kann auf Folgendes angewendet werden:

* Razor Page `PageModel`
* Controller
* Controller-Aktionsmethode

Mit dem `[EnableCors]`-Attribut können Sie verschiedene Richtlinien auf Controller/Seiten Modell/Aktion anwenden. Wenn das `[EnableCors]`-Attribut auf eine Controller-/Seiten-/Aktionsmethode angewendet wird und cors in der Middleware aktiviert ist, werden beide Richtlinien angewendet. Es wird empfohlen, Richtlinien zu kombinieren. Verwenden Sie das `[EnableCors]` Attribut oder die Middleware, nicht beide in der gleichen app.

Der folgende Code wendet eine andere Richtlinie auf jede Methode an:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Der folgende Code erstellt eine cors-Standard Richtlinie und eine Richtlinie mit dem Namen `"AnotherPolicy"`:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Cors deaktivieren

Das [&lbrack;disablecors-&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) Attribut deaktiviert cors für Controller/Seiten Modell/Aktion.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Cors-Richtlinien Optionen

In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer cors-Richtlinie festgelegt werden können:

* [Festlegen der zulässigen Ursprünge](#set-the-allowed-origins)
* [Festlegen der zulässigen HTTP-Methoden](#set-the-allowed-http-methods)
* [Festlegen der zulässigen Anforderungs Header](#set-the-allowed-request-headers)
* [Festlegen der verfügbar gemachten Antwortheader](#set-the-exposed-response-headers)
* [Anmelde Informationen in Ursprungs übergreifenden Anforderungen](#credentials-in-cross-origin-requests)
* [Festlegen der Preflight-Ablaufzeit](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> wird in `Startup.ConfigureServices`aufgerufen. Für einige Optionen kann es hilfreich sein, zuerst den Abschnitt [wie cors Works](#how-cors) zu lesen.

## <a name="set-the-allowed-origins"></a>Festlegen der zulässigen Ursprünge

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; ermöglicht die Verwendung von cors-Anforderungen von allen Ursprüngen mit einem beliebigen Schema (`http` oder `https`). `AllowAnyOrigin` ist unsicher, weil *jede Website* Ursprungs übergreifende Anforderungen an die APP stellen kann.

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> Das Angeben von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu einer Website übergreifenden Anforderungs Fälschung führen. Der cors-Dienst gibt eine ungültige cors-Antwort zurück, wenn eine APP mit beiden Methoden konfiguriert ist.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> Das Angeben von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu einer Website übergreifenden Anforderungs Fälschung führen. Geben Sie für eine sichere App eine exakte Liste von Ursprüngen an, wenn sich der Client für den Zugriff auf Server Ressourcen selbst autorisieren muss.

::: moniker-end

`AllowAnyOrigin` wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Origin`-Header aus. Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; legt die Eigenschaft <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> der Richtlinie auf eine Funktion fest, mit der Ursprünge einer konfigurierten Platzhalter Domäne zugeordnet werden können, wenn ausgewertet wird, ob der Ursprung zulässig ist.

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a>Festlegen der zulässigen HTTP-Methoden

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Lässt jede HTTP-Methode zu:
* Wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Methods`-Header aus. Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).

### <a name="set-the-allowed-request-headers"></a>Festlegen der zulässigen Anforderungs Header

Um zu ermöglichen, dass bestimmte Header in einer cors-Anforderung gesendet werden, die als *Autor Request Headers*bezeichnet wird, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> aufrufen, und geben Sie die zulässigen Header an:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Um alle Autoren Anforderungs Header zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>an:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Diese Einstellung wirkt sich auf Preflight-Anforderungen und den `Access-Control-Request-Headers`-Header aus. Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).

::: moniker range=">= aspnetcore-2.2"

Eine cors-Middleware-Richtlinie ist mit bestimmten Headern übereinstimmen, die in `WithHeaders` angegeben sind, ist nur möglich, wenn die in `Access-Control-Request-Headers` gesendeten Header exakt mit den in `WithHeaders`angegebenen

Nehmen Sie beispielsweise an, dass eine APP wie folgt konfiguriert ist:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

Cors-Middleware lehnt eine Preflight-Anforderung mit dem folgenden Anforderungs Header ab, weil `Content-Language` ([headernames. contentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nicht in `WithHeaders`aufgeführt ist:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Die APP gibt eine *200 OK* -Antwort zurück, sendet jedoch keine cors-Header zurück. Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Cors-Middleware lässt immer zu, dass vier Header im `Access-Control-Request-Headers` unabhängig von den in corspolicy. Headers konfigurierten Werten gesendet werden. Diese Liste von Headern umfasst Folgendes:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Nehmen Sie beispielsweise an, dass eine APP wie folgt konfiguriert ist:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

Cors-Middleware antwortet erfolgreich auf eine Preflight-Anforderung mit dem folgenden Anforderungs Header, weil `Content-Language` immer in der Whitelist enthalten ist:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a>Festlegen der verfügbar gemachten Antwortheader

Standardmäßig macht der Browser nicht alle Antwortheader für die app verfügbar. Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).

Die standardmäßig verfügbaren Antwortheader lauten wie folgt:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Mit der cors-Spezifikation werden die Header für *einfache Antworten*aufgerufen. Um andere Header für die app verfügbar zu machen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>an:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Anmelde Informationen in Ursprungs übergreifenden Anforderungen

Anmelde Informationen erfordern eine spezielle Behandlung in einer cors-Anforderung. Standardmäßig sendet der Browser keine Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung. Anmelde Informationen umfassen Cookies und http-Authentifizierungs Schemas. Um Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung zu senden, muss der Client `XMLHttpRequest.withCredentials` auf `true`festlegen.

Direkte Verwendung von `XMLHttpRequest`:

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

Verwenden der [Fetch-API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Der Server muss die Anmelde Informationen zulassen. Um Ursprungs übergreifende Anmelde Informationen zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>an:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

Die HTTP-Antwort enthält einen `Access-Control-Allow-Credentials`-Header, der dem Browser mitteilt, dass der Server Anmelde Informationen für eine Ursprungs übergreifende Anforderung zulässt.

Wenn der Browser Anmelde Informationen sendet, die Antwort jedoch keine gültige `Access-Control-Allow-Credentials` Kopfzeile enthält, macht der Browser die Antwort nicht an die app verfügbar, und die Ursprungs übergreifende Anforderung schlägt fehl.

Das Zulassen von Ursprungs übergreifenden Anmelde Informationen stellt ein Sicherheitsrisiko dar. Eine Website in einer anderen Domäne kann die Anmelde Informationen des angemeldeten Benutzers ohne das Wissen des Benutzers an die APP im Auftrag des Benutzers senden. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

In der cors-Spezifikation wird auch festgelegt, dass das Festlegen von Ursprüngen auf `"*"` (alle Ursprünge) ungültig ist, wenn der `Access-Control-Allow-Credentials` Header vorhanden ist.

### <a name="preflight-requests"></a>Preflight-Anforderungen

Bei einigen cors-Anforderungen sendet der Browser eine zusätzliche Anforderung, bevor die tatsächliche Anforderung gesendet wird. Diese Anforderung wird als *Preflight-Anforderung*bezeichnet. Der Browser kann die Preflight-Anforderung überspringen, wenn die folgenden Bedingungen zutreffen:

* Die Anforderungs Methode ist Get, Head oder Post.
* Von der App werden keine anderen Anforderungs Header als `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`oder `Last-Event-ID`festgelegt.
* Wenn festgelegt, hat der `Content-Type`-Header einen der folgenden Werte:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Die Regel für Anforderungs Header, die für die Client Anforderung festgelegt wurde, gilt für Header, die die APP festlegt, indem Sie `setRequestHeader` für das `XMLHttpRequest`-Objekt aufrufen. In der cors-Spezifikation werden diese Header *Anforderungs Header für Autoren*aufgerufen. Die Regel gilt nicht für Header, die vom Browser festgelegt werden können, z. b. `User-Agent`, `Host`oder `Content-Length`.

Im folgenden finden Sie ein Beispiel für eine Preflight-Anforderung:

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

* `Access-Control-Request-Method`: die HTTP-Methode, die für die tatsächliche Anforderung verwendet wird.
* `Access-Control-Request-Headers`: eine Liste mit Anforderungs Headern, die von der APP für die tatsächliche Anforderung festgelegt werden. Wie bereits erwähnt, enthält dies keine Header, die vom Browser festgelegt werden, z. b. `User-Agent`.

Eine cors-Preflight-Anforderung kann einen `Access-Control-Request-Headers`-Header enthalten, der dem Server die Header angibt, die mit der tatsächlichen Anforderung gesendet werden.

Um bestimmte Header zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>an:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Um alle Autoren Anforderungs Header zuzulassen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>an:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Browser sind nicht vollständig konsistent, wenn Sie `Access-Control-Request-Headers`festlegen. Wenn Sie Header auf einen anderen Wert als `"*"` festlegen (oder <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>verwenden), sollten Sie mindestens `Accept`, `Content-Type`und `Origin`sowie alle benutzerdefinierten Header einschließen, die Sie unterstützen möchten.

Im folgenden finden Sie ein Beispiel für eine Antwort auf die Preflight-Anforderung (vorausgesetzt, der Server lässt die Anforderung zu):

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

Die Antwort enthält einen `Access-Control-Allow-Methods`-Header, der die zulässigen Methoden auflistet, und optional einen `Access-Control-Allow-Headers`-Header, der die zulässigen Header auflistet. Wenn die Preflight-Anforderung erfolgreich ist, sendet der Browser die tatsächliche Anforderung.

Wenn die Preflight-Anforderung verweigert wird, gibt die APP eine *200 OK* -Antwort zurück, sendet jedoch keine cors-Header zurück. Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.

### <a name="set-the-preflight-expiration-time"></a>Festlegen der Preflight-Ablaufzeit

Der `Access-Control-Max-Age`-Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann. Um diesen Header festzulegen, wenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>an:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Funktionsweise von cors

In diesem Abschnitt wird beschrieben, was in einer [cors](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) -Anforderung auf der Ebene der HTTP-Nachrichten geschieht.

* Cors ist **kein** Sicherheits Feature. Cors ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.
  * Beispielsweise könnte ein böswilliger Akteur die Verwendung von [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website verhindern und eine Website übergreifende Anforderung an Ihre cors-aktivierte Website ausführen, um Informationen zu stehlen.
* Ihre API ist nicht sicherer, da Sie cors zulässt.
  * Es liegt an dem Client (Browser), cors zu erzwingen. Der Server führt die Anforderung aus und gibt die Antwort zurück. dabei handelt es sich um den Client, der einen Fehler zurückgibt und die Antwort blockiert. Eines der folgenden Tools zeigt z. b. die Serverantwort an:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.Net httpclient](/dotnet/csharp/tutorials/console-webapiclient)
    * Einen Webbrowser, indem Sie die URL in die Adressleiste eingeben.
* Es ist eine Möglichkeit für einen Server, Browser das Ausführen einer Ursprungs übergreifenden [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) -oder [Fetch-API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) -Anforderung zu gestatten, die andernfalls unzulässig wäre.
  * Browser (ohne cors) können keine Ursprungs übergreifenden Anforderungen ausführen. Vor cors wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen. JSONP verwendet nicht XHR, sondern das `<script>`-Tag, um die Antwort zu empfangen. Skripts können Ursprungs übergreifend geladen werden.

In der [cors-Spezifikation](https://www.w3.org/TR/cors/) wurden mehrere neue HTTP-Header eingeführt, die Ursprungs übergreifende Anforderungen ermöglichen. Wenn ein Browser cors unterstützt, werden diese Header automatisch für Ursprungs übergreifende Anforderungen festgelegt. Benutzerdefinierter JavaScript-Code ist nicht erforderlich, um cors zu aktivieren.

Im folgenden finden Sie ein Beispiel für eine Ursprungs übergreifende Anforderung. Der `Origin`-Header stellt die Domäne der Site bereit, von der die Anforderung stammt. Der `Origin`-Header ist erforderlich und muss sich vom Host unterscheiden.

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

Wenn der Server die Anforderung zulässt, wird der `Access-Control-Allow-Origin`-Header in der Antwort festgelegt. Der Wert dieses Headers stimmt entweder mit dem `Origin`-Header aus der Anforderung überein, oder ist der Platzhalter Wert `"*"`. Dies bedeutet, dass ein beliebiger Ursprung zulässig ist:

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

Wenn die Antwort nicht den `Access-Control-Allow-Origin`-Header enthält, schlägt die Ursprungs übergreifende Anforderung fehl. Der Browser lässt die Anforderung insbesondere nicht zu. Auch wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App zur Verfügung.

<a name="test"></a>

## <a name="test-cors"></a>Testen von CORS

So testen Sie cors:

1. [Erstellen Sie ein API-Projekt](xref:tutorials/first-web-api). Alternativ können Sie [das Beispiel herunterladen](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Aktivieren Sie cors mithilfe eines der Vorgehensweisen in diesem Dokument. Beispiel:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` sollten nur zum Testen einer Beispiel-App verwendet werden, die dem [Download Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)ähnelt.

1. Erstellen Sie ein Web-App-Projekt (Razor Pages oder MVC). Im Beispiel wird Razor Pages verwendet. Sie können die Web-App in der gleichen Projekt Mappe wie das API-Projekt erstellen.
1. Fügen Sie der Datei " *Index. cshtml* " den folgenden hervorgehobenen Code hinzu:

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. Ersetzen Sie im vorangehenden Code `url: 'https://<web app>.azurewebsites.net/api/values/1',` durch die URL zur bereitgestellten app.
1. Stellen Sie das API-Projekt bereit. Nehmen Sie beispielsweise die Bereitstellung [in Azure vor](xref:host-and-deploy/azure-apps/index).
1. Führen Sie die Razor Pages-oder MVC-App über den Desktop aus, und klicken Sie auf die Schaltfläche **Testen** . Verwenden Sie die F12-Tools, um Fehlermeldungen zu überprüfen.
1. Entfernen Sie den localhost-Ursprung aus `WithOrigins`, und stellen Sie die APP bereit. Alternativ können Sie die Client-App mit einem anderen Port ausführen. Führen Sie z. b. in Visual Studio aus.
1. Testen Sie mit der Client-App. Cors-Fehler geben einen Fehler zurück, aber die Fehlermeldung ist für JavaScript nicht verfügbar. Verwenden Sie die Registerkarte Konsole in den F12-Tools, um den Fehler anzuzeigen. Abhängig vom Browser erhalten Sie eine Fehlermeldung (in der F12-toolkonsole) ähnlich der folgenden:

   * Verwenden von Microsoft Edge:

     **SEC7120: [cors] der Ursprung `https://localhost:44375` `https://localhost:44375` im Antwortheader "Access-Control-Allow-Origin" für eine Ursprungs übergreifende Ressource in `https://webapi.azurewebsites.net/api/values/1` nicht gefunden wurde.**

   * Verwenden von Chrome:

     **Der Zugriff auf XMLHttpRequest auf `https://webapi.azurewebsites.net/api/values/1` vom Ursprungs `https://localhost:44375` wurde durch cors-Richtlinie blockiert: Es ist kein "Access-Control-Allow-Origin"-Header für die angeforderte Ressource vorhanden.**
     
Cors-aktivierte Endpunkte können mit einem Tool wie z. b. " [fddler](https://www.telerik.com/fiddler) " oder [Postman](https://www.getpostman.com/)getestet werden. Wenn Sie ein Tool verwenden, muss sich der Ursprung der vom `Origin`-Header angegebenen Anforderung von dem Host unterscheiden, der die Anforderung empfängt. Wenn die Anforderung nicht *über einen Ursprungs* Standort basierend auf dem Wert der `Origin`-Kopfzeile ist:

* Es ist nicht erforderlich, dass cors-Middleware die Anforderung verarbeitet.
* Cors-Header werden in der Antwort nicht zurückgegeben.

## <a name="cors-in-iis"></a>Cors in IIS

Beim Bereitstellen in IIS muss cors vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist. Zur Unterstützung dieses Szenarios muss das [IIS cors-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die APP installiert und konfiguriert werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Cross-Origin Resource Sharing (cors)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Einführung in das IIS cors-Modul](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)
