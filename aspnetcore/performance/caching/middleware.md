---
title: Antworten zwischenspeichernden Middleware in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Middleware für die Zwischenspeicherung von Antworten in ASP.NET Core konfigurieren und verwenden.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/20/2019
uid: performance/caching/middleware
ms.openlocfilehash: ddbd547ec0fdd09da1f3e3ce30f8fa35a34870c2
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897367"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Antworten zwischenspeichernden Middleware in ASP.NET Core

Durch [Luke Latham](https://github.com/guardrex) und [John Luo](https://github.com/JunTaoLuo)

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

In diesem Artikel erläutert die Antworten Zwischenspeichern von Middleware in ASP.NET Core-Apps zu konfigurieren. Die Middleware wird bestimmt, wenn Antworten zwischengespeichert werden, speichert Antworten und Antworten dient, aus dem Cache. Eine Einführung in die HTTP-Zwischenspeicherung und die `ResponseCache` Attribut, finden Sie unter [Zwischenspeichern von Antworten](xref:performance/caching/response).

## <a name="package"></a>Package

::: moniker range=">= aspnetcore-2.1"

Referenz der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) oder fügen Sie einen Paketverweis auf die [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) Paket.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Referenz der [metapaket "Microsoft.aspnetcore.All"](xref:fundamentals/metapackage) oder fügen Sie einen Paketverweis auf die [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) Paket.

::: moniker-end

::: moniker range="= aspnetcore-1.1"

Fügen Sie einen Paketverweis auf die [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) Paket.

::: moniker-end

## <a name="configuration"></a>Konfiguration

In `Startup.ConfigureServices`, die Middleware für die Sammlung von Diensten hinzufügen.

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=9)]

Konfigurieren Sie die app zur Verwendung der Middleware mit der `UseResponseCaching` Erweiterungsmethode, die die Middleware der Anforderungsverarbeitungspipeline hinzufügt. Die Beispiel-app Fügt eine [ `Cache-Control` ](https://tools.ietf.org/html/rfc7234#section-5.2) Header der Antwort, die von zwischenspeicherbaren Antworten bis zu 10 Sekunden lang zwischengespeichert. Das Beispiel sendet eine [ `Vary` ](https://tools.ietf.org/html/rfc7231#section-7.1.4) Header so konfigurieren Sie die Middleware, um nur, wenn eine zwischengespeicherte Antwort zu verarbeiten. die [ `Accept-Encoding` ](https://tools.ietf.org/html/rfc7231#section-5.3.4) Header nachfolgender Anforderungen entspricht, die der ursprünglichen Anforderung. Im Codebeispiel wird die folgende, [CacheControlHeaderValue](/dotnet/api/microsoft.net.http.headers.cachecontrolheadervalue) und [HeaderNames](/dotnet/api/microsoft.net.http.headers.headernames) erfordern eine `using` -Anweisung für die [Microsoft.Net.Http.Headers](/dotnet/api/microsoft.net.http.headers) Namespace.

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17,22-29)]

Antworten Zwischenspeichern Middleware speichert nur Serverantworten, die einen Statuscode "200 (OK)" führen. Alle anderen Antworten, einschließlich [Fehlerseiten](xref:fundamentals/error-handling), werden von der Middleware ignoriert.

> [!WARNING]
> Mit Inhalt für authentifizierte Clients Antworten müssen nicht zwischengespeichert werden kann, um zu verhindern, dass die Methode von speichern und Senden von diese Antworten markiert werden. Finden Sie unter [Bedingungen für die Zwischenspeicherung](#conditions-for-caching) Einzelheiten wie die Middleware bestimmt, ob eine Antwort zwischengespeichert werden können.

## <a name="options"></a>Optionen

Die Middleware bietet drei Optionen steuern das Zwischenspeichern von Antworten.

| Option                | Beschreibung |
| --------------------- | ----------- |
| UseCaseSensitivePaths | Bestimmt, ob die Groß-/Kleinschreibung Pfade Antworten zwischengespeichert werden. Der Standardwert ist `false`. |
| MaximumBodySize       | Die größte zwischenspeicherbar Größe für den Antworttext in Byte. Der Standardwert ist `64 * 1024 * 1024` (64 MB). |
| SizeLimit             | Die größenbeschränkung für die Antwort-Cache-Middleware in Byte. Der Standardwert ist `100 * 1024 * 1024` (100 MB). |

Im folgenden Beispiel wird die Middleware:

* Zwischenspeichern von Antworten kleiner als oder gleich 1024 Bytes.
* Die Antworten von Groß-/Kleinschreibung Pfaden Store (z. B. `/page1` und `/Page1` getrennt gespeichert werden).

```csharp
services.AddResponseCaching(options =>
{
    options.UseCaseSensitivePaths = true;
    options.MaximumBodySize = 1024;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Bei Verwendung von MVC-Web-API-Controllern oder Razor Pages-Seite-Modelle, die `ResponseCache` Attribut gibt an, die Parameter zum Festlegen der entsprechenden Header für das Zwischenspeichern von Antworten erforderlich sind. Der einzige Parameter der der `ResponseCache` Attribut, das genau die Middleware erfordert, ist `VaryByQueryKeys`, einen tatsächlichen HTTP-Header nicht entsprechen. Weitere Informationen finden Sie unter [ResponseCache-Attribut](xref:performance/caching/response#responsecache-attribute).

Wenn Sie nicht verwenden die `ResponseCache` -Attribut, das Zwischenspeichern von Antworten mit variiert werden kann die `VaryByQueryKeys` Feature. Verwenden der `ResponseCachingFeature` direkt aus der `IFeatureCollection` von der `HttpContext`:

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();
if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Verwenden einen einzelnen Wert gleich `*` in `VaryByQueryKeys` variiert den Cache von allen vorgangsanforderungs-Abfrageparameter.

## <a name="http-headers-used-by-response-caching-middleware"></a>HTTP-Header von Antworten Zwischenspeichern Middleware verwendet

Zwischenspeichern von Antworten von der Middleware ist so konfiguriert, die mithilfe von HTTP-Header.

| Header | Details |
| ------ | ------- |
| Autorisierung | Die Antwort nicht zwischengespeichert, wenn der Header vorhanden ist. |
| Cache-Control | Die Middleware berücksichtigt nur die Zwischenspeicherung von Antworten mit markiert die `public` mit cacheanweisungen. Steuerung der Zwischenspeicherung, die mit den folgenden Parametern:<ul><li>Max-age</li><li>Max-stale&#8224;</li><li>Min-neu</li><li>Must-revalidate</li><li>ohne-cache</li><li>ohne-store</li><li>only-if-cached</li><li>private</li><li>public</li><li>s-maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;Wenn keine Begrenzung, um angegeben wird `max-stale`, die Middleware führt keine Aktion aus.<br>&#8225;`proxy-revalidate`hat dieselbe Wirkung wie das `must-revalidate`.<br><br>Weitere Informationen finden Sie unter [RFC 7231: Fordern Sie Cachesteuerungsdirektiven](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| Pragma | Ein `Pragma: no-cache` -Header in der Anforderung generiert dieselbe Wirkung wie das `Cache-Control: no-cache`. Dieser Header wird überschrieben, indem die entsprechenden Anweisungen in der `Cache-Control` -Header, falls vorhanden. Für die Abwärtskompatibilität mit HTTP/1.0 berücksichtigt. |
| Set-Cookie | Die Antwort nicht zwischengespeichert, wenn der Header vorhanden ist. Middleware, die in der Anforderungsverarbeitungspipeline, die ein oder mehrere Cookies festlegt wird verhindert, dass die Antwort zwischenspeichern Middleware Zwischenspeichern der Antwort (z. B. die [cookiebasierte TempData-Anbieters](xref:fundamentals/app-state#tempdata)).  |
| Variieren | Die `Vary` Header wird zum variieren der zwischengespeicherten Antwort von einer anderen Spaltenüberschrift. Z. B. Zwischenspeichern von Antworten, indem Sie mit Codierung der `Vary: Accept-Encoding` -Header, der Anforderungen mit Headern speichert `Accept-Encoding: gzip` und `Accept-Encoding: text/plain` getrennt. Eine Antwort mit einem Headerwert, der `*` niemals gespeichert ist. |
| Läuft ab | Eine Antwort, die von diesen Header veraltete als nicht gespeichert oder abgerufen, es sei denn, die von anderen überschreiben `Cache-Control` Header. |
| If-None-Match | Die vollständige Antwort wird aus dem Cache bereitgestellt, wenn der Wert nicht `*` und `ETag` der Antwort entspricht keiner der Werte bereitgestellt. Andernfalls wird eine Antwort mit dem 304 (nicht geändert) bereitgestellt. |
| If-Modified-Since | Wenn die `If-None-Match` Header nicht vorhanden ist, wird eine vollständige Antwort aus dem Cache bereitgestellt, wenn die zwischengespeicherte Antwortdatum neuer ist als der angegebene Wert ist. Andernfalls wird eine Antwort mit dem 304 (nicht geändert) bereitgestellt. |
| Datum | Wenn aus dem Cache bedient die `Date` Header wird von der Middleware festgelegt, wenn diese Option wurde nicht auf die ursprüngliche Antwort angegeben. |
| Content-Length | Wenn aus dem Cache bedient die `Content-Length` Header wird von der Middleware festgelegt, wenn diese Option wurde nicht auf die ursprüngliche Antwort angegeben. |
| ALTER | Die `Age` Header in der ursprünglichen Antwort gesendet wird ignoriert. Die Middleware berechnet einen neuen Wert an, wenn eine zwischengespeicherte Antwort versorgt. |

## <a name="caching-respects-request-cache-control-directives"></a>Zwischenspeichern von berücksichtigt Cache-Control-Request-Richtlinien

Die Middleware berücksichtigt die Regeln für die [Zwischenspeichern von HTTP 1.1-Spezifikation](https://tools.ietf.org/html/rfc7234#section-5.2). Die Regeln ist erforderlich, einen Cache, der einen gültigen berücksichtigt `Cache-Control` Header, die vom Client gesendet werden. Unter der Spezifikation kann ein Client erstellt, Anforderungen mit einem `no-cache` Headerwert und erzwingen Sie den Server aus, um eine neue Antwort für jede Anforderung zu generieren. Derzeit besteht keine entwicklersteuerung dieses Verhalten beim Zwischenspeichern, wenn die Middleware verwendet werden, da die Middleware der offiziellen Spezifikation für die Zwischenspeicherung entspricht.

Zur besseren Steuerung des Verhaltens untersuchen Sie andere caching-Funktionen von ASP.NET Core. Informationen hierzu finden Sie in den folgenden Themen:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Problembehandlung

Wenn das Verhalten beim Zwischenspeichern, nicht wie erwartet ist, vergewissern Sie sich Antworten sind für zwischenspeicherbare als auch aus dem Cache bedient werden kann. Überprüfen Sie eingehenden Header der Anforderung und ausgehenden Header der Antwort. Aktivieren Sie [Protokollierung](xref:fundamentals/logging/index) , beim Debuggen hilfreich sein.

Beim Testen und Problembehandlung von Verhalten beim Zwischenspeichern kann in ein Browser Anforderungsheader festgelegt, die Auswirkungen der Zwischenspeicherung in unerwünschter Weise auf. Beispielsweise kann ein Browser festgelegt die `Cache-Control` Header `no-cache` oder `max-age=0` beim Aktualisieren von einer Seite. Die folgenden Tools Anforderungsheader können explizit festgelegt und werden bevorzugt, zum Testen der Zwischenspeicherung:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Bedingungen für die Zwischenspeicherung

* Die Anforderung muss zu einer Antwort des Servers mit einem 200 (OK) Statuscode führen.
* Die Anforderungsmethode muss GET oder HEAD.
* In `Startup.Configure`, Antworten Zwischenspeichern Middleware muss vor Middleware, die Komprimierung erfordern platziert werden. Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.
* Die `Authorization` Header darf nicht vorhanden sein.
* `Cache-Control` Header-Parameter müssen gültig sein, und die Antwort muss markiert sein `public` und nicht als markiert `private`.
* Die `Pragma: no-cache` Header darf nicht vorhanden sein wenn die `Cache-Control` -Header nicht vorhanden ist, als die `Cache-Control` Header überschreibt die `Pragma` Header, wenn vorhanden.
* Die `Set-Cookie` Header darf nicht vorhanden sein.
* `Vary` Header-Parameter müssen gültig und nicht gleich sein `*`.
* Die `Content-Length` Headerwert (falls festgelegt) müssen die Größe des Antworttexts übereinstimmen.
* Die [IHttpSendFileFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpsendfilefeature) wird nicht verwendet.
* Die Antwort darf nicht laut veraltet sein der `Expires` Header und die `max-age` und `s-maxage` cache-Anweisungen.
* Antwortpufferung muss erfolgreich sein, und die Größe der Antwort muss kleiner sein als die konfigurierte oder default `SizeLimit`.
* Die Antwort muss gemäß zwischengespeichert werden, die [RFC 7234](https://tools.ietf.org/html/rfc7234) Spezifikationen. Z. B. die `no-store` Richtlinie darf nicht in der Anforderung oder Antwort-Header-Felder vorhanden sein. Finden Sie unter *Abschnitt 3: Das Speichern von Antworten in Caches* von [RFC 7234](https://tools.ietf.org/html/rfc7234) Details.

> [!NOTE]
> Antiforgery System zum Generieren von sicheren Token zum Verhindern von Cross-Site Request Forgery (CSRF) attacks legt die `Cache-Control` und `Pragma` Header `no-cache` , damit die Antworten nicht zwischengespeichert. Informationen zum Deaktivieren der antiforgery Tokens für die HTML-Formularelemente, finden Sie unter [antiforgery ASP.NET Core-Konfiguration](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
