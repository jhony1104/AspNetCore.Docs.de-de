---
title: Antworten zwischenspeichernden Middleware in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Middleware für die Zwischenspeicherung von Antworten in ASP.NET Core konfigurieren und verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/05/2019
uid: performance/caching/middleware
ms.openlocfilehash: d6756ce16396133da643cc08ca0f48369479ad3a
ms.sourcegitcommit: b9e914ef274b5ec359582f299724af6234dce135
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67596153"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Antworten zwischenspeichernden Middleware in ASP.NET Core

Durch [Luke Latham](https://github.com/guardrex) und [John Luo](https://github.com/JunTaoLuo)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

In diesem Artikel erläutert die Antworten Zwischenspeichern von Middleware in ASP.NET Core-Apps zu konfigurieren. Die Middleware wird bestimmt, wenn Antworten zwischengespeichert werden, speichert Antworten und Antworten dient, aus dem Cache. Eine Einführung in die HTTP-Zwischenspeicherung und die [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) Attribut, finden Sie unter [Zwischenspeichern von Antworten](xref:performance/caching/response).

## <a name="configuration"></a>Konfiguration

Verwenden der [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) oder fügen Sie einen Paketverweis auf die [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) Paket.

In `Startup.ConfigureServices`, fügen Sie die Middleware für die Antwort-Caching auf die Sammlung von Diensten:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Konfigurieren Sie die app zur Verwendung der Middleware mit der <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> Erweiterungsmethode, die die Middleware der Pipeline zur anforderungsverarbeitung in hinzufügt `Startup.Configure`:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

Die Beispiel-app fügt die Header, um die Steuerung der Zwischenspeicherung bei nachfolgenden Anforderungen hinzu:

* [Cache-Control-](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; zwischenspeicherbaren Antworten bis zu 10 Sekunden lang zwischenspeichert.
* [Variieren](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash; dienen nur eine zwischengespeicherte Antwort, wenn die Middleware konfiguriert die [ `Accept-Encoding` ](https://tools.ietf.org/html/rfc7231#section-5.3.4) Header nachfolgender Anforderungen entspricht, die der ursprünglichen Anforderung.

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

Antworten Zwischenspeichern Middleware speichert nur Serverantworten, die einen Statuscode "200 (OK)" führen. Alle anderen Antworten, einschließlich [Fehlerseiten](xref:fundamentals/error-handling), werden von der Middleware ignoriert.

> [!WARNING]
> Mit Inhalt für authentifizierte Clients Antworten müssen nicht zwischengespeichert werden kann, um zu verhindern, dass die Methode von speichern und Senden von diese Antworten markiert werden. Finden Sie unter [Bedingungen für die Zwischenspeicherung](#conditions-for-caching) Einzelheiten wie die Middleware bestimmt, ob eine Antwort zwischengespeichert werden können.

## <a name="options"></a>Optionen

In der folgenden Tabelle werden die Optionen für die Zwischenspeicherung der Antwort angezeigt.

| Option | Beschreibung |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Die größte zwischenspeicherbar Größe für den Antworttext in Byte. Der Standardwert ist `64 * 1024 * 1024` (64 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Die größenbeschränkung für die Antwort-Cache-Middleware in Byte. Der Standardwert ist `100 * 1024 * 1024` (100 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Bestimmt, ob die Groß-/Kleinschreibung Pfade Antworten zwischengespeichert werden. Der Standardwert ist `false`. |

Im folgenden Beispiel wird die Middleware:

* Zwischenspeichern von Antworten mit dem eine Textgröße kleiner als oder gleich 1024 Bytes.
* Store die Antworten von Pfaden der Groß-/Kleinschreibung beachtet. Z. B. `/page1` und `/Page1` getrennt gespeichert werden.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Bei Verwendung von MVC / web-API-Controllern oder Razor Pages-Seite-Modelle, die [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) Attribut gibt an, die Parameter zum Festlegen der entsprechenden Header für das Zwischenspeichern von Antworten erforderlich sind. Der einzige Parameter der der `[ResponseCache]` Attribut, das genau die Middleware erfordert, ist <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, einen tatsächlichen HTTP-Header nicht entsprechen. Weitere Informationen finden Sie unter <xref:performance/caching/response#responsecache-attribute>.

Wenn Sie nicht verwenden die `[ResponseCache]` -Attribut, das Zwischenspeichern von Antworten mit geändert werden kann `VaryByQueryKeys`. Verwenden der <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> direkt aus der [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Verwenden einen einzelnen Wert gleich `*` in `VaryByQueryKeys` variiert den Cache von allen vorgangsanforderungs-Abfrageparameter.

## <a name="http-headers-used-by-response-caching-middleware"></a>HTTP-Header von Antworten Zwischenspeichern Middleware verwendet

Die folgende Tabelle enthält Informationen zu HTTP-Headern, die das Zwischenspeichern von Antworten zu beeinflussen.

| Header | Details |
| ------ | ------- |
| `Authorization` | Die Antwort nicht zwischengespeichert, wenn der Header vorhanden ist. |
| `Cache-Control` | Die Middleware berücksichtigt nur die Zwischenspeicherung von Antworten mit markiert die `public` mit cacheanweisungen. Steuerung der Zwischenspeicherung, die mit den folgenden Parametern:<ul><li>Max-age</li><li>Max-stale&#8224;</li><li>Min-neu</li><li>Must-revalidate</li><li>ohne-cache</li><li>ohne-store</li><li>only-if-cached</li><li>private</li><li>public</li><li>s-maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;Wenn keine Begrenzung, um angegeben wird `max-stale`, die Middleware führt keine Aktion aus.<br>&#8225;`proxy-revalidate`hat dieselbe Wirkung wie das `must-revalidate`.<br><br>Weitere Informationen finden Sie unter [RFC 7231: Fordern Sie Cachesteuerungsdirektiven](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | Ein `Pragma: no-cache` -Header in der Anforderung generiert dieselbe Wirkung wie das `Cache-Control: no-cache`. Dieser Header wird überschrieben, indem die entsprechenden Anweisungen in der `Cache-Control` -Header, falls vorhanden. Für die Abwärtskompatibilität mit HTTP/1.0 berücksichtigt. |
| `Set-Cookie` | Die Antwort nicht zwischengespeichert, wenn der Header vorhanden ist. Middleware, die in der Anforderungsverarbeitungspipeline, die ein oder mehrere Cookies festlegt wird verhindert, dass die Antwort zwischenspeichern Middleware Zwischenspeichern der Antwort (z. B. die [cookiebasierte TempData-Anbieters](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | Die `Vary` Header wird zum variieren der zwischengespeicherten Antwort von einer anderen Spaltenüberschrift. Z. B. Zwischenspeichern von Antworten, indem Sie mit Codierung der `Vary: Accept-Encoding` -Header, der Anforderungen mit Headern speichert `Accept-Encoding: gzip` und `Accept-Encoding: text/plain` getrennt. Eine Antwort mit einem Headerwert, der `*` niemals gespeichert ist. |
| `Expires` | Eine Antwort, die von diesen Header veraltete als nicht gespeichert oder abgerufen, es sei denn, die von anderen überschreiben `Cache-Control` Header. |
| `If-None-Match` | Die vollständige Antwort wird aus dem Cache bereitgestellt, wenn der Wert nicht `*` und `ETag` der Antwort entspricht keiner der Werte bereitgestellt. Andernfalls wird eine Antwort mit dem 304 (nicht geändert) bereitgestellt. |
| `If-Modified-Since` | Wenn die `If-None-Match` Header nicht vorhanden ist, wird eine vollständige Antwort aus dem Cache bereitgestellt, wenn die zwischengespeicherte Antwortdatum neuer ist als der angegebene Wert ist. Andernfalls ein *304 - nicht geändert* Antwort bereitgestellt wird. |
| `Date` | Wenn aus dem Cache bedient die `Date` Header wird von der Middleware festgelegt, wenn diese Option wurde nicht auf die ursprüngliche Antwort angegeben. |
| `Content-Length` | Wenn aus dem Cache bedient die `Content-Length` Header wird von der Middleware festgelegt, wenn diese Option wurde nicht auf die ursprüngliche Antwort angegeben. |
| `Age` | Die `Age` Header in der ursprünglichen Antwort gesendet wird ignoriert. Die Middleware berechnet einen neuen Wert an, wenn eine zwischengespeicherte Antwort versorgt. |

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
* In `Startup.Configure`, Antworten Zwischenspeichern Middleware muss platziert werden, vor der Middleware, die caching erforderlich ist. Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.
* Die `Authorization` Header darf nicht vorhanden sein.
* `Cache-Control` Header-Parameter müssen gültig sein, und die Antwort muss markiert sein `public` und nicht als markiert `private`.
* Die `Pragma: no-cache` Header darf nicht vorhanden sein wenn die `Cache-Control` -Header nicht vorhanden ist, als die `Cache-Control` Header überschreibt die `Pragma` Header, wenn vorhanden.
* Die `Set-Cookie` Header darf nicht vorhanden sein.
* `Vary` Header-Parameter müssen gültig und nicht gleich sein `*`.
* Die `Content-Length` Headerwert (falls festgelegt) müssen die Größe des Antworttexts übereinstimmen.
* Die <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> wird nicht verwendet.
* Die Antwort darf nicht laut veraltet sein der `Expires` Header und die `max-age` und `s-maxage` cache-Anweisungen.
* Antwortpufferung muss erfolgreich sein. Die Größe der Antwort muss kleiner sein als die konfigurierte oder default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>. Die Größe des Hauptteils der Antwort muss kleiner sein als die konfigurierte oder default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.
* Die Antwort muss gemäß zwischengespeichert werden, die [RFC 7234](https://tools.ietf.org/html/rfc7234) Spezifikationen. Z. B. die `no-store` Richtlinie darf nicht in der Anforderung oder Antwort-Header-Felder vorhanden sein. Finden Sie unter *Abschnitt 3: Das Speichern von Antworten in Caches* von [RFC 7234](https://tools.ietf.org/html/rfc7234) Details.

> [!NOTE]
> Antiforgery System zum Generieren von sicheren Token zum Verhindern von Cross-Site Request Forgery (CSRF) attacks legt die `Cache-Control` und `Pragma` Header `no-cache` , damit die Antworten nicht zwischengespeichert. Informationen zum Deaktivieren der antiforgery Tokens für die HTML-Formularelemente, finden Sie unter <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
