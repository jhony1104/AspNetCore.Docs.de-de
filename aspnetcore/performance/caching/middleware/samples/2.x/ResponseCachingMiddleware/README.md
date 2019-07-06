# <a name="aspnet-core-response-caching-sample"></a>Beispiel: ASP.NET Core-Antwort zwischenspeichern

Dieses Beispiel veranschaulicht die Verwendung von ASP.NET Core [Antworten Zwischenspeichern Middleware](https://docs.microsoft.com/aspnet/core/performance/caching/middleware).

Die app reagiert, mit der Indexseite, einschließlich einer `Cache-Control` -Header auf das Verhalten beim Zwischenspeichern zu konfigurieren. Legt auch fest, die app die `Vary` Header zum Konfigurieren des Caches zum Verarbeiten der Antwort nur, wenn die `Accept-Encoding` Header nachfolgender Anforderungen entspricht, die aus der ursprünglichen Anforderung.

Beim Ausführen des Beispiels wird die Seite "Index" bereitgestellt, aus dem Cache gespeichert und bis zu 10 Sekunden lang zwischengespeichert.

So testen Sie das Verhalten beim Zwischenspeichern:

* Verwenden Sie einen Browser nicht Verhalten beim Zwischenspeichern zu testen. Browser hinzufügen häufig einen Cache-Control-Header beim erneuten Laden, die verhindern, dass die Middleware eine zwischengespeicherte Seite dient. Z. B. eine `Cache-Control` Header mit einem Wert von `max-age=0`) vom Browser hinzugefügt werden kann.
* Verwenden Sie ein Tool für Entwickler, die zulässt, die Anforderungsheader explizit festlegen wie z. B. <a href="https://www.telerik.com/fiddler">Fiddler</a> oder <a href="https://www.getpostman.com/">Postman</a>.
