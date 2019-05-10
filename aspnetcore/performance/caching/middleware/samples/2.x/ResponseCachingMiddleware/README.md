# <a name="aspnet-core-response-caching-sample"></a>Beispiel: ASP.NET Core-Antwort zwischenspeichern

Dieses Beispiel veranschaulicht die Verwendung von ASP.NET Core [Antworten Zwischenspeichern Middleware](https://docs.microsoft.com/aspnet/core/performance/caching/middleware).

Die app reagiert, mit der Indexseite, einschließlich einer `Cache-Control` -Header auf das Verhalten beim Zwischenspeichern zu konfigurieren. Legt auch fest, die app die `Vary` Header zum Konfigurieren des Caches zum Verarbeiten der Antwort nur, wenn die `Accept-Encoding` Header nachfolgender Anforderungen entspricht, die aus der ursprünglichen Anforderung.

Beim Ausführen des Beispiels wird die Seite "Index" bereitgestellt, aus dem Cache gespeichert und bis zu 10 Sekunden lang zwischengespeichert.
