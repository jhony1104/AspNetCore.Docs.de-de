# <a name="response-compression-sample-application-aspnet-core-2x"></a>Antwort-Komprimierung-beispielanwendung (ASP.NET Core 2.x)

Dieses Beispiel veranschaulicht die Verwendung von ASP.NET Core 2.x Antworten komprimierende Middleware zum Komprimieren von HTTP-Antworten. Im Beispiel wird veranschaulicht, Gzip, Brotli und benutzerdefinierte Komprimierung-Anbieter für Text- und Image-Antworten und veranschaulicht das Hinzufügen ein MIME-Typs für die Komprimierung. Das ASP.NET Core 1.x-Beispiel finden Sie [Antwort Komprimierung-beispielanwendung (ASP.NET Core 1.x)](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples/1.x).

## <a name="examples-in-this-sample"></a>Beispiele

* `BrotliCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum Datei Textantwort auf 2,044 Bytes, die auf ~ 979 Bytes komprimiert.
    * **/testfile1kb.txt** -Datei Textantwort auf 1,033 Bytes, die auf etwa 36 Bytes komprimiert.
    * **/ Durchlassen** -Antwort, die als einzelne Zeichen ausgegeben werden, in 1-Sekunden-Intervallen.
* `GzipCompressionProvider`
  * `text/plain`
    * **/** -Lorem Ipsum Datei Textantwort auf 2,044 Bytes, die auf ~ 927 Bytes komprimiert.
    * **/testfile1kb.txt** -Datei Textantwort auf 1,033 Bytes, die auf ~ 47 Bytes komprimiert.
    * **/ Durchlassen** -Antwort, die als einzelne Zeichen ausgegeben werden, in 1-Sekunden-Intervallen.
  * `image/svg+xml`
    * **/Banner.SVG** – A Scalable Vector Graphics (SVG) Bildausgabe bei 9,707 Bytes, die auf ~ 4,459 Bytes komprimiert.
* `CustomCompressionProvider`<br>Zeigt, wie einen benutzerdefinierter Komprimierung-Anbieter für die Verwendung mit der Middleware implementiert.

Wenn die Anforderung enthält die `Accept-Encoding` die Middleware-Header und -Antwort-Komprimierung ist erfolgreich, automatisch hinzugefügt werden. eine `Vary: Accept-Encoding` Header der Antwort. Die `Vary` Header anweist, Caches, verwalten Sie mehrere Kopien der Antwort basierend auf alternative Werte `Accept-Encoding`, sodass jeweils einen komprimierten (Gzip oder Brotli) und nicht komprimierte Version in Caches gespeichert werden, Systeme, auf denen können entweder annehmen der komprimierte oder unkomprimierte Antwort.

## <a name="use-the-sample"></a>Verwenden Sie das Beispiel

1. Stellen Sie eine Anforderung mit [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), oder [Postman](https://www.getpostman.com/) auf die Anwendung ohne ein `Accept-Encoding` Header und notieren Sie die antwortnutzlast Antwortgröße, und Antwortheader.
1. Hinzufügen einer `Accept-Encoding: br` oder `Accept-Encoding: gzip` Header und beachten Sie die Größe der komprimierte Antwort und -Antwortheader. Die Antwortgröße fällt, und die `Content-Encoding` -Header der Antwort ist enthalten, von der Middleware, der angibt, der diese Komprimierung mit beiden Gzip oder Brotli aufgetreten ist. Wenn Sie den Antworttext für die Lorem Ipsum ansehen oder **testfile1kb.txt** Antwort wird ersichtlich, dass der Text, komprimierte und nicht gelesen werden ist.
1. Hinzufügen einer `Accept-Encoding: mycustomcompression` Header und notieren Sie sich die Header der Antwort. Die `CustomCompressionProvider` ist eine leere Implementierung, die tatsächlich die Antwort komprimieren nicht, aber Sie können einen benutzerdefinierten Komprimierung Stream-Wrapper für erstellen, die `CreateStream()` Methode.
