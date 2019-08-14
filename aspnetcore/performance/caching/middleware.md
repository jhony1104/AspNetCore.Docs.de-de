---
title: Zwischen Speicherungs Middleware für Antworten in ASP.net Core
author: guardrex
description: Erfahren Sie, wie Sie Middleware für die Zwischenspeicherung von Antworten in ASP.NET Core konfigurieren und verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/09/2019
uid: performance/caching/middleware
ms.openlocfilehash: 838a08c12316d218501f26d5905f9e31ab93dfc9
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994227"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Zwischen Speicherungs Middleware für Antworten in ASP.net Core

Von [Luke Latham](https://github.com/guardrex) und [John Luo](https://github.com/JunTaoLuo)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

In diesem Artikel wird erläutert, wie Sie die Zwischenspeicherung von Antwort Caching in einer ASP.net Core-App konfigurieren. Die Middleware bestimmt, wann Antworten zwischengespeichert werden können, speichert Antworten und verarbeitet Antworten aus dem Cache. Eine Einführung in das http-Caching und das [[responsecache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) -Attribut finden Sie unter zwischen [Speichern von Antworten](xref:performance/caching/response).

## <a name="configuration"></a>Konfiguration

::: moniker range=">= aspnetcore-3.0"

Die Middleware zum Zwischenspeichern von Antworten wird durch das [Microsoft. aspnetcore. responsecaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) -Paket ermöglicht, das ASP.net Core-apps implizit hinzugefügt wird.

Fügen `Startup.ConfigureServices`Sie in der Dienst Sammlung die Middleware zum Zwischenspeichern von Antworten hinzu:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Konfigurieren Sie die APP für die Verwendung der Middleware <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> mit der-Erweiterungsmethode, mit der die Middleware der Pipeline für `Startup.Configure`die Anforderungs Verarbeitung in hinzugefügt wird:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

Die Beispiel-App Fügt Header zum Steuern der Zwischenspeicherung bei nachfolgenden Anforderungen hinzu:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Speichert zwischen speicherbare Antworten bis zu 10 Sekunden.
* [Variieren](https://tools.ietf.org/html/rfc7231#section-7.1.4) Konfiguriert die Middleware so, dass eine zwischengespeicherte Antwort nur dann [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) bereitgestellt wird, wenn der Header der nachfolgenden Anforderungen mit der der ursprünglichen Anforderung übereinstimmt. &ndash;

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

Die Middleware zum Zwischenspeichern von Antworten speichert nur Server Antworten zwischen, die zu einem 200 (OK)-Statuscode führen. Alle anderen Antworten, einschließlich der [Fehlerseiten](xref:fundamentals/error-handling), werden von der Middleware ignoriert.

> [!WARNING]
> Antworten, die Inhalte für authentifizierte Clients enthalten, müssen als nicht zwischen speicherbar gekennzeichnet werden, um zu verhindern, dass die Middleware diese Antworten speichert und bedient. Ausführliche Informationen dazu, wie die Middleware festlegt, ob eine Antwort zwischengespeichert werden kann, finden Sie unter [Bedingungen für das Caching](#conditions-for-caching) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Verwenden Sie das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) ", oder fügen Sie dem Paket " [Microsoft. aspnetcore. responsecaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) " einen Paket Verweis hinzu.

Fügen `Startup.ConfigureServices`Sie in der Dienst Sammlung die Middleware zum Zwischenspeichern von Antworten hinzu:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Konfigurieren Sie die APP für die Verwendung der Middleware <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> mit der-Erweiterungsmethode, mit der die Middleware der Pipeline für `Startup.Configure`die Anforderungs Verarbeitung in hinzugefügt wird:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

Die Beispiel-App Fügt Header zum Steuern der Zwischenspeicherung bei nachfolgenden Anforderungen hinzu:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash; Speichert zwischen speicherbare Antworten bis zu 10 Sekunden.
* [Variieren](https://tools.ietf.org/html/rfc7231#section-7.1.4) Konfiguriert die Middleware so, dass eine zwischengespeicherte Antwort nur dann [`Accept-Encoding`](https://tools.ietf.org/html/rfc7231#section-5.3.4) bereitgestellt wird, wenn der Header der nachfolgenden Anforderungen mit der der ursprünglichen Anforderung übereinstimmt. &ndash;

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

Die Middleware zum Zwischenspeichern von Antworten speichert nur Server Antworten zwischen, die zu einem 200 (OK)-Statuscode führen. Alle anderen Antworten, einschließlich der [Fehlerseiten](xref:fundamentals/error-handling), werden von der Middleware ignoriert.

> [!WARNING]
> Antworten, die Inhalte für authentifizierte Clients enthalten, müssen als nicht zwischen speicherbar gekennzeichnet werden, um zu verhindern, dass die Middleware diese Antworten speichert und bedient. Ausführliche Informationen dazu, wie die Middleware festlegt, ob eine Antwort zwischengespeichert werden kann, finden Sie unter [Bedingungen für das Caching](#conditions-for-caching) .

::: moniker-end

## <a name="options"></a>Optionen

Die Optionen zum Zwischenspeichern von Antworten sind in der folgenden Tabelle aufgeführt.

| Option | Beschreibung |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Die größte zwischen speicherbare Größe für den Antworttext in Bytes. Der Standardwert ist `64 * 1024 * 1024` (64 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Die Größenbeschränkung für die Antwort Cache-Middleware in Bytes. Der Standardwert ist `100 * 1024 * 1024` (100 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Bestimmt, ob Antworten bei Pfaden zwischen Groß-und Kleinschreibung zwischengespeichert werden. Der Standardwert ist `false`. |

Im folgenden Beispiel wird die Middleware wie folgt konfiguriert:

* Zwischenspeichern von Antworten mit einer Textgröße, die kleiner oder gleich 1.024 Bytes ist.
* Speichert die Antworten nach Pfaden mit Unterscheidung nach Groß-/Kleinschreibung Beispielsweise `/page1` werden und `/Page1` separat gespeichert.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

Bei der Verwendung von MVC/Web-API-Controllern oder Razor Pages Seiten Modellen gibt das Attribut [[responsecache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) die Parameter an, die zum Festlegen der entsprechenden Header für das Zwischenspeichern von Antworten erforderlich sind. Der einzige Parameter des `[ResponseCache]` Attributs, der die Middleware strikt erfordert, ist <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, was keinem tatsächlichen HTTP-Header entspricht. Weitere Informationen finden Sie unter <xref:performance/caching/response#responsecache-attribute>.

Wenn das-Attribut `[ResponseCache]` nicht verwendet wird, kann das Zwischenspeichern `VaryByQueryKeys`von Antworten mit variiert werden. Verwenden Sie <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> das direkt aus [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Durch die Verwendung eines einzelnen Werts `*` von `VaryByQueryKeys` in variiert der Cache von allen Anforderungs Abfrage Parametern.

## <a name="http-headers-used-by-response-caching-middleware"></a>HTTP-Header, die von der zwischenware zum Zwischenspeichern

Die folgende Tabelle enthält Informationen über HTTP-Header, die sich auf das Zwischenspeichern von Antworten auswirken

| Header | Details |
| ------ | ------- |
| `Authorization` | Die Antwort wird nicht zwischengespeichert, wenn der Header vorhanden ist. |
| `Cache-Control` | Die Middleware berücksichtigt nur zwischen Speicherungs Antworten, `public` die mit der Cache Direktive gekennzeichnet sind. Steuern Sie die Zwischenspeicherung mit den folgenden Parametern:<ul><li>Max-age</li><li>Max-veraltet&#8224;</li><li>min-Fresh</li><li>muss neu validiert werden</li><li>No-Cache</li><li>No-Store</li><li>nur-if-Cache</li><li>private</li><li>public</li><li>s-maxage</li><li>Proxy-erneut validieren&#8225;</li></ul>&#8224;Wenn für `max-stale`keine Einschränkung festgelegt ist, führt die Middleware keine Aktion aus.<br>&#8225;`proxy-revalidate`hat denselben Effekt wie `must-revalidate`.<br><br>Weitere Informationen finden [Sie unter RFC 7231: Cache Steuerungs Direktiven](https://tools.ietf.org/html/rfc7234#section-5.2.1)anfordern. |
| `Pragma` | Ein `Pragma: no-cache` -Header in der Anforderung erzeugt denselben Effekt wie `Cache-Control: no-cache`. Dieser Header wird durch die relevanten Direktiven im `Cache-Control` Header überschrieben, sofern vorhanden. Wird aus Gründen der Abwärtskompatibilität mit HTTP/1.0 berücksichtigt. |
| `Set-Cookie` | Die Antwort wird nicht zwischengespeichert, wenn der Header vorhanden ist. Jede Middleware in der Anforderungs Verarbeitungs Pipeline, mit der mindestens ein Cookie festgelegt wird, verhindert, dass die Antwort Cache-Middleware die Antwort zwischenspeichert (z. b. den cookiebasierten [TempData-Anbieter](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | Der `Vary` -Header wird verwendet, um die zwischengespeicherte Antwort von einem anderen Header zu verändern. Beispielsweise können Sie Antworten durch die Codierung Zwischenspeichern `Vary: Accept-Encoding` , indem Sie den-Header einschließen, der `Accept-Encoding: gzip` Antworten `Accept-Encoding: text/plain` für Anforderungen mit Headern und separat zwischenspeichert. Eine Antwort mit dem-Header Wert `*` wird nie gespeichert. |
| `Expires` | Eine Antwort, die von diesem Header als veraltet eingestuft wird, wird nur dann gespeichert `Cache-Control` oder abgerufen, wenn Sie von anderen Headern |
| `If-None-Match` | Die vollständige Antwort wird aus dem Cache bereitgestellt, `*` wenn der `ETag` Wert nicht ist, und die der Antwort entspricht keinem der angegebenen Werte. Andernfalls wird eine 304-Antwort (nicht geändert) bereitgestellt. |
| `If-Modified-Since` | Wenn der `If-None-Match` Header nicht vorhanden ist, wird eine vollständige Antwort aus dem Cache bereitgestellt, wenn das zwischengespeicherte Antwort Datum neuer als der angegebene Wert ist. Andernfalls wird eine *304-nicht geänderte* Antwort verarbeitet. |
| `Date` | Bei der Bereitstellung aus dem `Date` Cache wird der Header von der Middleware festgelegt, wenn er nicht in der ursprünglichen Antwort angegeben wurde. |
| `Content-Length` | Bei der Bereitstellung aus dem `Content-Length` Cache wird der Header von der Middleware festgelegt, wenn er nicht in der ursprünglichen Antwort angegeben wurde. |
| `Age` | Der `Age` in der ursprünglichen Antwort gesendete Header wird ignoriert. Die Middleware berechnet einen neuen Wert, wenn eine zwischengespeicherte Antwort bedient wird. |

## <a name="caching-respects-request-cache-control-directives"></a>Caching-Anweisungs Anforderungen Cache-Control-Direktiven

Die Middleware respektiert die Regeln der [http 1,1-cachingspezifikation](https://tools.ietf.org/html/rfc7234#section-5.2). Die Regeln erfordern einen Cache, um einen gültigen `Cache-Control` Header zu berücksichtigen, der vom Client gesendet wird. Unter der Spezifikation kann ein Client Anforderungen mit einem `no-cache` Header Wert senden und erzwingen, dass der Server eine neue Antwort für jede Anforderung generiert. Derzeit gibt es keine Entwickler Kontrolle über dieses zwischen Speicherungs Verhalten, wenn die Middleware verwendet wird, da die Middleware die offizielle zwischen Speicherungs Spezifikation befolgt.

Weitere Informationen zum zwischen Speicherungs Verhalten finden Sie unter untersuchen anderer zwischen Speicherungs Funktionen von ASP.net Core. Informationen hierzu finden Sie in den folgenden Themen:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Problembehandlung

Wenn das Cachingverhalten nicht erwartungsgemäß ist, vergewissern Sie sich, dass die Antworten zwischengespeichert werden können und aus dem Cache bedient werden können. Überprüfen der eingehenden Header der Anforderung und der ausgehenden Header der Antwort. Aktivieren der [Protokollierung](xref:fundamentals/logging/index) zum unterstützen des Debuggens.

Beim Testen und behandeln von Problemen zwischen Speicherungs Verhalten kann ein Browser Anforderungs Header festlegen, die das Caching auf unerwünschte Weise beeinflussen. Ein Browser kann beispielsweise den `Cache-Control` -Header auf `no-cache` oder `max-age=0` festlegen, wenn eine Seite aktualisiert wird. Die folgenden Tools können Anforderungs Header explizit festlegen und werden für das Testen der Zwischenspeicherung bevorzugt:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Bedingungen für das Zwischenspeichern

* Die Anforderung muss eine Serverantwort mit dem Statuscode 200 (OK) ergeben.
* Die Anforderungs Methode muss "Get" oder "Head" lauten.
* In `Startup.Configure`muss die Zwischenspeicherung von Antwort Caching vor Middleware platziert werden, die Zwischenspeichern erfordert. Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.
* Der `Authorization` Header darf nicht vorhanden sein.
* `Cache-Control`Header Parameter müssen gültig sein, und die Antwort muss markiert `public` und nicht gekennzeichnet `private`sein.
* Der `Pragma: no-cache` Header darf nicht vorhanden sein, wenn `Cache-Control` der Header nicht vorhanden ist, `Cache-Control` da der Header den `Pragma` Header überschreibt, wenn er vorhanden ist.
* Der `Set-Cookie` Header darf nicht vorhanden sein.
* `Vary`Header Parameter müssen gültig und nicht gleich `*`sein.
* Der `Content-Length` Header Wert (falls festgelegt) muss mit der Größe des Antwort Texts identisch sein.
* Wird <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> nicht verwendet.
* Die Antwort darf nicht veraltet sein, wie von der `Expires` -Header- `max-age` und `s-maxage` der-und-Cache Direktive angegeben.
* Die Antwort Pufferung muss erfolgreich sein. Die Größe der Antwort muss kleiner als der konfigurierte oder der Standard <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>Wert sein. Die Textgröße der Antwort muss kleiner als der konfigurierte oder der Standard <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>Wert sein.
* Die Antwort muss gemäß den Spezifikationen von [RFC 7234](https://tools.ietf.org/html/rfc7234) zwischengespeichert werden können. Beispielsweise darf die `no-store` -Direktive nicht in den Feldern der Anforderungs-oder Antwortheader vorhanden sein. Siehe *Abschnitt 3: Speichern von Antworten in* Caches von [RFC 7234](https://tools.ietf.org/html/rfc7234) für Details.

> [!NOTE]
> Das antifälschungs System zum Erstellen von sicheren Token, um Angriffe durch Website übergreifende Anforderungs Fälschung (CSRF) `Cache-Control` zu `Pragma` verhindern, `no-cache` legt die-und-Header auf fest, sodass Antworten nicht zwischengespeichert werden. Informationen zum Deaktivieren von antifälschungstoken für HTML-Formularelemente finden <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>Sie unter.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
