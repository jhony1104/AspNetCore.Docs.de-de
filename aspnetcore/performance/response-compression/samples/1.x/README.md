# <a name="response-compression-sample-application-aspnet-core-1x"></a>Antwort-Komprimierung-beispielanwendung (ASP.NET Core 1.x)

Dieses Beispiel veranschaulicht die Verwendung von ASP.NET Core 1.x Antworten komprimierende Middleware zum Komprimieren von HTTP-Antworten für. Im Beispiel wird veranschaulicht, Gzip und benutzerdefinierte Komprimierung-Anbieter für Text- und Image-Antworten und veranschaulicht das Hinzufügen ein MIME-Typs für die Komprimierung. Das ASP.NET Core 2.x-Beispiel finden Sie [Antwort Komprimierung-beispielanwendung (ASP.NET Core 2.x)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x).

## <a name="examples-in-this-sample"></a>Beispiele

* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Datei Lorem Ipsum-Textantwort auf 2,044 Bytes, die auf 927 Bytes komprimiert werden
    * **/testfile1kb.txt** -Text-Datei Antwort 1,033 Bytes, die auf 47 Bytes komprimiert zu werden
    * **/ Durchlassen** -Antwort, die als einzelne Zeichen ausgegeben werden, in 1-Sekunden-Intervallen
  * `image/svg+xml`
    * **/Banner.SVG** -Anforderung am 9,707 Bytes, die auf 4,459 Bytes komprimiert werden A Scalable Vector Graphics (SVG)
* `CustomCompressionProvider`<br>Veranschaulicht das Implementieren eines benutzerdefinierten Komprimierung-Anbieters für die Verwendung mit der middleware

Wenn die Anforderung enthält die `Accept-Encoding` -Header, die im Beispiel wird eine `Vary: Accept-Encoding` Header der Antwort. Die `Vary` Header anweist, Caches, verwalten Sie mehrere Kopien der Antwort basierend auf alternative Werte `Accept-Encoding`, sodass eine komprimierte (Gzip) und die nicht komprimierte Version Caches gespeichert werden, für Systeme, auf denen können entweder die komprimierten akzeptieren oder die nicht komprimierte Antwort.

## <a name="using-the-sample"></a>Verwenden des Beispiels

1. Stellen Sie eine Anforderung mit [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), oder [Postman](https://www.getpostman.com/) auf die Anwendung ohne ein `Accept-Encoding` Header und notieren Sie die antwortnutzlast Antwortgröße, und Antwortheader.
1. Hinzufügen einer `Accept-Encoding: gzip` Header und beachten Sie die Größe der komprimierte Antwort und -Antwortheader. Sie sehen, dass die Antwortgröße zu löschen, und die `Content-Encoding: gzip` -Header der Antwort ist enthalten, von der Beispiel-app. Wenn Sie den Antworttext für die Lorem Ipsum ansehen oder **testfile1kb.txt** Antwort wird ersichtlich, dass der Text, komprimierte und nicht gelesen werden ist.
1. Hinzufügen einer `Accept-Encoding: mycustomcompression` Header und notieren Sie sich die Header der Antwort. Die `CustomCompressionProvider` ist eine leere Implementierung, die tatsächlich die Antwort komprimieren nicht, aber Sie können einen benutzerdefinierten Komprimierung Stream-Wrapper für erstellen, die `CreateStream()` Methode.
