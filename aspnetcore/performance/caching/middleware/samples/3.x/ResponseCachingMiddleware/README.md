# <a name="aspnet-core-response-caching-sample"></a>Beispiel zum Zwischenspeichern von ASP.net Core Antworten

Dieses Beispiel veranschaulicht die Verwendung der [Middleware für das Zwischenspeichern](https://docs.microsoft.com/aspnet/core/performance/caching/middleware)von ASP.net Core Antworten.

Die APP antwortet mit ihrer Index Seite, einschließlich eines `Cache-Control` Headers zum Konfigurieren des Cachingverhaltens. Die APP legt `Vary` den-Header auch so fest, dass der Cache so konfiguriert wird, `Accept-Encoding` dass die Antwort nur dann verarbeitet wird, wenn der Header der nachfolgenden Anforderungen mit der ursprünglichen Anforderung übereinstimmt

Wenn Sie das Beispiel ausführen, wird die Index Seite aus dem Cache bereitgestellt, wenn Sie für bis zu 10 Sekunden gespeichert und zwischengespeichert wird.

So testen Sie das Cachingverhalten:

* Verwenden Sie keinen Browser, um das zwischen Speicherungs Verhalten zu testen. Browser Fügen beim erneuten Laden häufig einen Cache Steuerungs Header hinzu, der verhindert, dass die Middleware eine zwischengespeicherte Seite bedient. Beispielsweise kann eine `Cache-Control` Kopfzeile mit dem `max-age=0`Wert) vom Browser hinzugefügt werden.
* Verwenden Sie ein Entwickler Tool, das das explizite Festlegen der Anforderungs Header zulässt, wie z. b. " <a href="https://www.telerik.com/fiddler">fddler</a> " oder <a href="https://www.getpostman.com/">Postman</a>.
