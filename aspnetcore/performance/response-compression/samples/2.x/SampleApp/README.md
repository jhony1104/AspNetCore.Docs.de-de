# <a name="response-compression-sample-application-aspnet-core-2x"></a>Beispielanwendung für die Antwort Komprimierung (ASP.net Core 2. x)

Dieses Beispiel veranschaulicht die Verwendung der ASP.net Core 2. x-Antwort Komprimierungs Middleware zum Komprimieren von HTTP-Antworten. Das Beispiel veranschaulicht gzip-, brotli-und benutzerdefinierte Komprimierungs Anbieter für Text-und Bild Antworten und zeigt, wie ein MIME-Typ für die Komprimierung hinzugefügt wird. Das ASP.net Core 1. x-Beispiel finden Sie unter [Beispielanwendung für die Antwort Komprimierung (ASP.net Core 1. x)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/1.x).

## <a name="examples-in-this-sample"></a>Beispiele

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -Lorem ipsum-Textdatei Antwort bei 2.044 bytes, die auf ~ 979 bytes komprimiert.
    * **/testfile1kb.txt** -Text Datei Antwort bei 1.033 Bytes, die auf ~ 36 Bytes komprimiert.
    * **/trickle** -Response wird als einzelne Zeichen in 1-Sekunden-Intervallen ausgegeben.
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Lorem ipsum-Textdatei Antwort bei 2.044 bytes, die auf ~ 927 bytes komprimiert.
    * **/testfile1kb.txt** -Text Datei Antwort bei 1.033 Bytes, die auf ~ 47 Bytes komprimiert.
    * **/trickle** -Response wird als einzelne Zeichen in 1-Sekunden-Intervallen ausgegeben.
  * `image/svg+xml`
    * **/Banner.SVG** : eine SVG-Bild Antwort (Scalable Vector Graphics) bei 9.707 bytes, die auf ~ 4.459 Bytes komprimiert.
* `CustomCompressionProvider`<br>Zeigt, wie ein benutzerdefinierter Komprimierungs Anbieter für die Verwendung mit der Middleware implementiert wird.

Wenn die Anforderung den `Accept-Encoding`-Header enthält und die Antwort Komprimierung erfolgreich ist, fügt die Middleware der Antwort automatisch einen `Vary: Accept-Encoding`-Header hinzu. Der `Vary`-Header weist Caches an, mehrere Kopien der Antwort auf der Grundlage alternativer Werte `Accept-Encoding`beizubehalten, sodass sowohl eine komprimierte (gzip-oder brotli) als auch eine nicht komprimierte Version in Caches für Systeme gespeichert werden, die entweder die komprimierte oder die nicht komprimierte Antwort akzeptieren können.

## <a name="use-the-sample"></a>Verwenden des Beispiels

1. Stellen Sie mit " [fddler](https://www.telerik.com/fiddler)", " [Firebug](https://getfirebug.com/)" oder " [Postman](https://www.getpostman.com/) " eine Anforderung ohne einen `Accept-Encoding`-Header an die Anwendung, und notieren Sie sich die Antwort Nutzlast, die Antwort Größe und die Antwortheader.
1. Fügen Sie einen `Accept-Encoding: br` oder `Accept-Encoding: gzip`-Header hinzu, und notieren Sie sich die komprimierte Antwort Größe und die Antwortheader Die Antwort Größe wird verworfen, und der `Content-Encoding`-Antwortheader wird von der Middleware eingeschlossen, um anzugeben, dass die Komprimierung mit gzip oder brotli aufgetreten ist. Wenn Sie sich den Antworttext für die "Lorem ipsum"-oder " **testfile1kb. txt** "-Antwort ansehen, sehen Sie, dass der Text komprimiert und unlesbar ist.
1. Fügen Sie einen `Accept-Encoding: mycustomcompression` Header hinzu, und notieren Sie sich die Antwortheader. Der `CustomCompressionProvider` ist eine leere Implementierung, die die Antwort nicht tatsächlich komprimiert, aber Sie können einen benutzerdefinierten komprimierungstreamwrapper für die `CreateStream()`-Methode erstellen.
