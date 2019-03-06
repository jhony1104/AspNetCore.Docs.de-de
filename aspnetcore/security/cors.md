---
title: Aktivieren Sie Ursprungsübergreifender Anforderungen (CORS) in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie CORS als Standard zum Zulassen oder ablehnen von ursprungsübergreifenden Anforderungen in einer ASP.NET Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 02/27/2019
uid: security/cors
ms.openlocfilehash: eb8dd3b1c96d9060b0164dcd4d0fbe004ed4af84
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57346371"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Aktivieren Sie Ursprungsübergreifender Anforderungen (CORS) in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Artikel wird das Aktivieren von CORS in einer ASP.NET Core-app veranschaulicht.

Browsersicherheit verhindert, dass eine Webseite auf Anfragen zu einer anderen Domäne als der, die die Webseite verarbeitet. Diese Einschränkung wird aufgerufen, die *Richtlinie desselben Ursprungs*. Die Richtlinie desselben Ursprungs wird verhindert, dass eine schädliche Website sensible Daten von einem anderen Standort lesen. In einigen Fällen empfiehlt es sich um zuzulassen, dass andere Standorte Cross-Origin-Anforderungen zu Ihrer app durchführen. Weitere Informationen finden Sie unter den [Mozilla CORS Artikel](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS).

[Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):

* Ist ein W3C standard, die auf einem Server zu lockern die Richtlinie des gleichen Ursprungs ermöglicht.
* Ist **nicht** Sicherheitsfeature CORS lockert die Sicherheit. Eine API ist nicht vom CORS zulassen sicherer. Weitere Informationen finden Sie unter [funktioniert wie CORS](#how-cors).
* Kann einen Server explizit einige ursprungsübergreifende Anforderungen zulassen, während andere abgelehnt.
* Ist sicherer und flexibler als frühere Techniken wie z. B. [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/2.2-stage-samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Denselben Ursprung

Zwei URLs haben denselben Ursprung ggf. identische Schemas, Hosts und -Ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Diese zwei URLs haben denselben Ursprung an:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Diese URLs haben verschiedene Ursprünge als die vorherigen beiden URLs:

* `https://example.net` &ndash; Andere Domäne
* `https://www.example.com/foo.html` &ndash; Andere Unterdomäne
* `http://example.com/foo.html` &ndash; Anderes Schema
* `https://example.com:9000/foo.html` &ndash; Portnummer

Internet Explorer berücksichtigen nicht den Port Ursprünge zu vergleichen.

## <a name="cors-with-named-policy-and-middleware"></a>CORS mithilfe der genannten Richtlinie und middleware

CORS-Middleware verarbeitet Cross-Origin-Anforderungen. Der folgende Code aktiviert CORS für die gesamte app mit dem angegebenen Ursprung an:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Der vorangehende Code:

* Legt den Namen der Richtlinie auf "\_MyAllowSpecificOrigins". Der Richtlinienname ist willkürlich.
* Ruft die <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> Erweiterungsmethode, die Kerne ermöglicht.
* Aufrufe <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions). Das Lambda akzeptiert ein <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Objekt. [Optionen für die Konfiguration](#cors-policy-options), z. B. `WithOrigins`, werden weiter unten in diesem Artikel beschrieben.

Die <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> Methodenaufruf der app Service-Containers CORS-Dienste hinzugefügt:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Weitere Informationen finden Sie unter [CORS-Richtlinienoptionen](#cpo) in diesem Dokument.

Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methode kann Methoden, verketten, wie im folgenden Code gezeigt:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Der folgende hervorgehobene Code gilt CORS-Richtlinien für alle apps-Endpunkte über CORS-Middleware:

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

Finden Sie unter [Aktivieren von CORS in Razor-Seiten, Controller und Aktionsmethoden](#ecors) anzuwendende CORS-Richtlinie auf der Seite/Controller/Aktion-Ebene.

Hinweis:

* `UseCors` muss aufgerufen werden, bevor `UseMvc`.
* Die URL muss **nicht** keinen nachgestellten umgekehrten Schrägstrich enthalten (`/`). Wenn die URL mit beendet `/`, gibt der Vergleich `false` und es wird kein Header zurückgegeben.

Finden Sie unter [Test CORS](#test) Anleitungen zum Testen des obigen Codes.

<a name="ecors"></a>

## <a name="enable-cors-with-attributes"></a>Aktivieren von CORS mit Attributen

Die [ &lbrack;EnableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) Attribut bietet eine Alternative zum Anwenden von CORS Global. Die `[EnableCors]` Attribut aktiviert CORS für den ausgewählten Endpunkt, anstatt alle Endpunkte.

Verwendung `[EnableCors]` an die Standardrichtlinie und `[EnableCors("{Policy String}")]` eine Richtlinie angeben.

Die `[EnableCors]` Attribut kann auf angewendet werden:

* Razor-Seite `PageModel`
* Controller
* Aktionsmethode des Controllers

Sie können unterschiedliche Richtlinien anwenden, auf der Seite "-Modell/Controlleraktion mit der `[EnableCors]` Attribut. Wenn die `[EnableCors]` Attribut einer Controller/Seite / Modell/Aktion-Methode angewendet wird und CORS in Middleware aktiviert ist, werden beide Richtlinien angewendet. Es wird empfohlen, für das Kombinieren von Richtlinien. Verwenden der `[EnableCors]` Attribut oder nicht beide in der gleichen app-Middleware.

Der folgende Code gilt eine andere Richtlinie für jede Methode an:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Der folgende Code erstellt eine Standardrichtlinie für CORS und eine Richtlinie, die mit dem Namen `"AnotherPolicy"`:

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Deaktivieren von CORS

Die [ &lbrack;DisableCors&rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) Attribut deaktiviert CORS für die Seite "-Modell/Controlleraktion.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS-Richtlinie-Optionen

Dieser Abschnitt beschreibt die verschiedenen Optionen, die in einer CORS-Richtlinie festgelegt werden können:

* [Legen Sie die zulässigen Ursprünge](#set-the-allowed-origins)
* [Legen Sie die zulässigen HTTP-Methoden](#set-the-allowed-http-methods)
* [Legt die zulässigen Anforderungsheader fest.](#set-the-allowed-request-headers)
* [Legen Sie die verfügbar gemachten Antwortheader](#set-the-exposed-response-headers)
* [Anmeldeinformationen im Cross-Origin-Anforderungen](#credentials-in-cross-origin-requests)
* [Die Preflight-Ablaufzeit festlegen](#set-the-preflight-expiration-time)

 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> wird aufgerufen, `Startup.ConfigureServices`. Für einige Optionen, es kann hilfreich sein, lesen Sie die [funktioniert wie CORS](#how-cors) im ersten Abschnitt.

## <a name="set-the-allowed-origins"></a>Legen Sie die zulässigen Ursprünge

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; CORS-Anforderungen von der alle Ursprünge mit einem beliebigen Schema zulässig (`http` oder `https`). `AllowAnyOrigin` ist unsicher, da *eine beliebige Website* Cross-Origin-Anfragen an die app vornehmen können.

  ::: moniker range=">= aspnetcore-2.2"

  > [!NOTE]
  > Angeben von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann dazu führen, siteübergreifende anforderungsfälschung. Der CORS-Dienst gibt eine ungültige CORS-Antwort zurück, wenn eine app mit beiden Methoden konfiguriert ist.

  ::: moniker-end

  ::: moniker range="< aspnetcore-2.2"

  > [!NOTE]
  > Angeben von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann dazu führen, siteübergreifende anforderungsfälschung. Geben Sie für eine sichere-app eine genaue Liste der Ursprünge, wenn der Client selbst auf Serverressourcen autorisieren muss.

  ::: moniker-end

<!-- REVIEW required
I changed from
Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration. **This** setting affects preflight requests and the ...
to
**`AllowAnyOrigin`** affects preflight requests and the

to remove the ambiguous **This**. 
-->

  `AllowAnyOrigin` wirkt sich auf preflight-Anforderungen und die `Access-Control-Allow-Origin` Header. Weitere Informationen finden Sie unter den [Preflight-Anforderungen](#preflight-requests) Abschnitt.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Legt die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Eigenschaft der Richtlinie, die eine Funktion sein, ermöglicht Ursprünge mit eine konfigurierten Platzhalterdomäne übereinstimmen, bei der Auswertung, wenn der Ursprung zulässig ist.

  [!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-104&highlight=4)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a>Legen Sie die zulässigen HTTP-Methoden

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Können alle HTTP-Methode:
* Wirkt sich auf preflight-Anforderungen und die `Access-Control-Allow-Methods` Header. Weitere Informationen finden Sie unter den [Preflight-Anforderungen](#preflight-requests) Abschnitt.

### <a name="set-the-allowed-request-headers"></a>Legt die zulässigen Anforderungsheader fest.

Wird aufgerufen, um bestimmte Header in einer CORS-Anforderung gesendet werden können *erstellen Anforderungsheader*, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> , und geben Sie die erlaubten Header:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Um zuzulassen, die alle erstellen Anforderungsheader, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Diese Einstellung wirkt sich auf die preflight-Anforderungen und die `Access-Control-Request-Headers` Header. Weitere Informationen finden Sie unter den [Preflight-Anforderungen](#preflight-requests) Abschnitt.

::: moniker range=">= aspnetcore-2.2"

Eine Übereinstimmung der CORS-Middleware-Richtlinien auf bestimmte Header gemäß `WithHeaders` ist nur möglich, wenn die Header gesendet wird, `Access-Control-Request-Headers` genau die Header in angegebenen `WithHeaders`.

Betrachten Sie beispielsweise eine app wie folgt konfiguriert:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS-Middleware eine preflight-Anforderung mit der folgenden Anforderungsheader ablehnt, da `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) ist nicht aufgeführt, `WithHeaders`:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Die app-gibt ein *200 OK* Antwort jedoch nicht wieder die CORS-Header sendet. Aus diesem Grund versucht der Browser nicht die cors-Anforderung.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS-Middleware können immer vier Header in der `Access-Control-Request-Headers` unabhängig von den Werten in CorsPolicy.Headers konfiguriert gesendet werden. Diese Liste der Header enthält:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Betrachten Sie beispielsweise eine app wie folgt konfiguriert:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS-Middleware erfolgreich beantwortet eine preflight-Anforderung mit der folgenden Anforderungsheader da `Content-Language` ist immer in der Whitelist enthalten:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a>Legen Sie die verfügbar gemachten Antwortheader

Standardmäßig nicht im Browser alle Antwortheader für die app verfügbar machen. Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminologie): Einfache Antwortheader](https://www.w3.org/TR/cors/#simple-response-header).

Die Antwortheader, die standardmäßig verfügbar sind:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS-Spezifikation ruft diese Header *Header für die einfache Antwort*. Um weitere Header für die app verfügbar zu machen, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Anmeldeinformationen im Cross-Origin-Anforderungen

Anmeldeinformationen erfordern besondere Behandlung in eine CORS-Anforderung. Standardmäßig sendet der Browser keine Anmeldeinformationen mit einer cors-Anforderung. Anmeldeinformationen enthalten, Cookies und HTTP-Authentifizierungsschemas. Um die Anmeldeinformationen mit einer cors-Anforderung zu senden, muss der Client festgelegt `XMLHttpRequest.withCredentials` zu `true`.

Mithilfe von `XMLHttpRequest` direkt:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Unter Verwendung von jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Mithilfe der [API abrufen](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Der Server muss es sich um die Anmeldeinformationen zulassen. Um ursprungsübergreifende Anmeldeinformationen ermöglichen möchten, rufen <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

Die HTTP-Antwort enthält ein `Access-Control-Allow-Credentials` -Header, der dem Browser darüber informiert, dass der Server die Anmeldeinformationen für eine Anforderung zwischen verschiedenen Ursprüngen zulässt.

Wenn der Browser sendet die Anmeldeinformationen, aber die Antwort auf eine gültige beinhaltet keine `Access-Control-Allow-Credentials` -Header, der Browser nicht die Antwort auf die app verfügbar zu machen, und die cors-Anforderung schlägt fehl.

Genehmigung von Cross-Origin-Anmeldeinformationen ist ein Sicherheitsrisiko dar. Eine Website in einer anderen Domäne kann die Anmeldeinformationen eines angemeldeten Benutzers an die app auf den Namen des Benutzers, ohne das Wissen des Benutzers senden. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS-Spezifikation gibt auch an dieser Einstellung Ursprüngen `"*"` (alle Ursprünge) ist ungültig. wenn die `Access-Control-Allow-Credentials` Header vorhanden ist.

### <a name="preflight-requests"></a>Preflight-Anforderungen

Für einige CORS-Anforderungen sendet der Browser eine zusätzliche Anforderung vor der tatsächlichen Anforderung an. Diese Anforderung wird aufgerufen, eine *preflight-Anforderung*. Der Browser kann die preflight-Anforderung überspringen, wenn die folgenden Bedingungen erfüllt sind:

* Die Anforderungsmethode ist GET, HEAD oder POST.
* Die app keine Anforderungsheader außer festlegen `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, oder `Last-Event-ID`.
* Die `Content-Type` -Header, wenn festgelegt ist, verfügt über mindestens einen der folgenden Werte:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Die Regel auf die Anforderungsheader festzulegen, für die Header die Anforderung des Clients gilt, die die app durch den Aufruf festlegt `setRequestHeader` auf die `XMLHttpRequest` Objekt. CORS-Spezifikation ruft diese Header *erstellen Anforderungsheader*. Die Regel trifft nicht auf der Browser, wie z. B. festlegen kann Header `User-Agent`, `Host`, oder `Content-Length`.

Im folgenden finden ein Beispiel für eine preflight-Anforderung:

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

* `Access-Control-Request-Method`: Die HTTP-Methode, die für die tatsächliche Anforderung verwendet werden.
* `Access-Control-Request-Headers`: Eine Liste der Anforderungsheader, die die app für die tatsächliche Anforderung festlegt. Wie bereits erwähnt, Dies beinhaltet keine Header, die der Browser legt, wie z. B. fest `User-Agent`.

Eine CORS-preflight-Anforderung sind zum Beispiel eine `Access-Control-Request-Headers` -Header, der auf dem Server die Header gibt an, die mit der tatsächlichen Anforderung gesendet werden.

Um bestimmte Header zu ermöglichen, rufen <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Um zuzulassen, die alle erstellen Anforderungsheader, rufen Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Browser sind nicht vollständig konsistent in diese Festlegung `Access-Control-Request-Headers`. Wenn Sie Header nicht festgelegt `"*"` (oder verwenden Sie <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), Sie sollten mindestens einschließen `Accept`, `Content-Type`, und `Origin`, sowie alle benutzerdefinierten Header, die Sie unterstützen möchten.

Im folgenden finden eine Beispielantwort auf die preflight-Anforderung (vorausgesetzt, dass der Server die Anforderung zulässt):

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

Die Antwort enthält ein `Access-Control-Allow-Methods` Header, der die zulässigen Methoden enthält und optional eine `Access-Control-Allow-Headers` -Header, der die erlaubten Header aufgeführt sind. Wenn die preflight-Anforderung erfolgreich ist, sendet der Browser die tatsächliche Anforderung an.

Wenn die preflight-Anforderung abgelehnt wird, gibt die app eine *200 OK* Antwort jedoch nicht wieder die CORS-Header sendet. Aus diesem Grund versucht der Browser nicht die cors-Anforderung.

### <a name="set-the-preflight-expiration-time"></a>Die Preflight-Ablaufzeit festlegen

Die `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die preflight-Anforderung zwischengespeichert werden kann. Rufen Sie zum Festlegen dieser Header <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Funktionsweise von CORS

In diesem Abschnitt wird beschrieben, was geschieht, in einem [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) Anforderung auf der Ebene der HTTP-Nachrichten.

* CORS ist **nicht** eine Sicherheitsfunktion. CORS ist ein W3C-standard, der einem Server zu lockern die Richtlinie des gleichen Ursprungs ermöglicht.
  * Beispielsweise können von ein böswilliger Akteur [zu verhindern, dass Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website, und führen Sie eine standortübergreifende-Anforderung an den Standort der CORS-Aktivierung, Informationen zu stehlen.
* Ihre API ist nicht von CORS zulassen sicherer.
  * Es liegt an den Client (Browser) um CORS zu erzwingen. Der Server führt die Anforderung und gibt die Antwort zurück, sondern der Client, der ein Fehler auftritt und blockiert die Antwort zurückgibt. Beispielsweise wird eines der folgenden Tools die Serverantwort angezeigt:
     * [Fiddler](https://www.telerik.com/fiddler)
     * [Postman](https://www.getpostman.com/)
     * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
     * Ein Webbrowser, indem Sie die URL in die Adressleiste eingeben.
* Dies ist eine Möglichkeit für einen Server zum Zulassen von Browsern zum Ausführen einer Cross-Origin [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) oder [API abrufen](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) -Anforderung, die andernfalls nicht zulässig sein sollen.
  * Browser (ohne CORS), die Cross-Origin-Anfragen nicht möglich. Bevor Sie CORS [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) wurde verwendet, um diese Einschränkung zu umgehen. JSONP XHR nicht verwenden kann, verwendet er die `<script>` Tag zum Empfangen der Antwort. Skripts sind zulässig, um ursprungsübergreifende geladen werden.

Die [CORS-Spezifikation]() eingeführt wurden mehrere neue HTTP-Header, die Cross-Origin-Anforderungen zu ermöglichen. Wenn ein Browser CORS unterstützt, wird diese Header automatisch für Cross-Origin-Anforderungen. Benutzerdefinierte JavaScript-Code ist nicht erforderlich, um CORS zu aktivieren.

Folgendes ist ein Beispiel für eine cors-Anforderung. Die `Origin` Header enthält, die Domäne der Website, die die Anforderung gesendet wird:

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

Wenn der Server die Anforderung zulässt, wird die `Access-Control-Allow-Origin` Header in der Antwort. Entweder mit dem Wert dieses Headers übereinstimmt der `Origin` Header aus der Anforderung oder der Platzhalterwert `"*"`, Bedeutung, die einen beliebigen Ursprung zulässig ist:

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

Wenn die Antwort enthalten nicht die `Access-Control-Allow-Origin` -Header, die Cross-Origin-Anforderung schlägt fehl. Der Browser lässt, die die Anforderung. Selbst wenn der Server eine erfolgreiche Antwort gibt zurück, machen nicht im Browser die Antwort für die Client-app verfügbar.

<a name="test"></a>

## <a name="test-cors"></a>Testen von CORS

Um CORS zu testen:

1. [Erstellen Sie ein API-Projekt](xref:tutorials/first-web-api). Alternativ können Sie [Herunterladen des Beispiels](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Aktivieren von CORS mit einer der Methoden in diesem Dokument. Zum Beispiel:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]
  
  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` sollte nur verwendet werden, für das Testen einer Beispiel-app, die ähnlich wie die [Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/live/aspnetcore/security/cors/sample/Cors).

1. Erstellen Sie eine Web-app-Projekt (Razor-Seiten oder MVC). Das Beispiel verwendet die Razor-Seiten. Sie können die Web-app in der gleichen Projektmappe wie das API-Projekt erstellen.
1. Fügen Sie den folgenden hervorgehobenen Code in die *"Index.cshtml"* Datei:

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. Ersetzen Sie im vorangehenden Code `url: 'https://<web app>.azurewebsites.net/api/values/1',` mit der URL für die bereitgestellte app.
1. Bereitstellen des API-Projekts. Z. B. [Bereitstellen in Azure](xref:host-and-deploy/azure-apps/index).
1. Führen Sie die Razor-Seiten oder MVC-app über den Desktop, und klicken Sie auf die **Test** Schaltfläche. Verwenden Sie die F12-Tools, um Fehlermeldungen zu überprüfen.
1. Entfernen Sie "localhost" Ursprung von `WithOrigins` und Bereitstellen der app. Führen Sie alternativ die Client-app mit einem anderen Port. Führen Sie z. B. in Visual Studio.
1. Testen Sie die Client-app. CORS-Fehler ein Fehler zurückgegeben, aber die Fehlermeldung nicht für JavaScript verfügbar. Verwenden Sie die Registerkarte "Console" in den F12-Tools, um den Fehler anzuzeigen. Je nach Browser erhalten Sie eine Fehlermeldung (in der F12-Toolskonsole) ähnelt dem folgenden:

  * Verwenden von Microsoft Edge:

    **SEC7120: [CORS] den Ursprung "https://localhost:44375"wurde nicht gefunden"https://localhost:44375"in der Access-Control-Allow-Origin-Antwortheader für Cross-Origin Resource unter"https://webapi.azurewebsites.net/api/values/1".**

  * Verwenden von Chrome:

    **Zugriff auf XMLHttpRequest unter "https://webapi.azurewebsites.net/api/values/1"von"Origin"https://localhost:44375"wurde blockiert von CORS-Richtlinie: Kein 'Access-Control-Allow-Origin' Header, die für die angeforderte Ressource vorhanden ist.**

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
