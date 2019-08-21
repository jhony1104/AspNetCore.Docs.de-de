---
title: Antwort Komprimierung in ASP.net Core
author: guardrex
description: Informationen zur Antwortkomprimierung und zum Verwenden von Antworten komprimierender Middleware in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/09/2019
uid: performance/response-compression
ms.openlocfilehash: e320e87179f9f1b9773a55c380684a3f3f712632
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993464"
---
# <a name="response-compression-in-aspnet-core"></a>Antwort Komprimierung in ASP.net Core

Von [Luke Latham](https://github.com/guardrex)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Die Netzwerkbandbreite ist eine begrenzte Ressource. Die Reduzierung der Antwort Größe erhöht in der Regel die Reaktionsfähigkeit einer APP, oft erheblich. Eine Möglichkeit, die Nutz Last Größen zu reduzieren, ist das Komprimieren der Antworten einer App.

## <a name="when-to-use-response-compression-middleware"></a>Verwendung der Middleware für die Antwort Komprimierung

Verwenden Sie serverbasierte Antwort Komprimierungs Technologien in IIS, Apache oder nginx. Die Leistung der Middleware stimmt wahrscheinlich nicht mit der Leistung der Server Module zusammen. Der [http. sys-Server](xref:fundamentals/servers/httpsys) Server und der [Kestrel](xref:fundamentals/servers/kestrel) -Server bieten derzeit keine integrierte Komprimierungs Unterstützung.

Verwenden Sie die Middleware für die Antwort Komprimierung, wenn Sie:

* Die folgenden serverbasierten Komprimierungs Technologien können nicht verwendet werden:
  * [Modul für dynamische IIS-Komprimierung](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache mod_deflate-Modul](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx-Komprimierung und-Komprimierung](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Direktes Hosting:
  * [Http. sys-Server](xref:fundamentals/servers/httpsys) (früher als Weblistener bezeichnet)
  * [Kestrel-Server](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Antwortkomprimierung

Normalerweise kann jede nicht System intern komprimierte Antwort von der Antwort Komprimierung profitieren. Nicht nativ komprimierte Antworten umfassen in der Regel Folgendes: CSS, JavaScript, HTML, XML und JSON. Sie sollten nativ komprimierte Assets, z. b. png-Dateien, nicht komprimieren. Wenn Sie versuchen, eine nativ komprimierte Antwort weiter zu komprimieren, werden alle kleinen zusätzlichen Reduzierungs-und Übertragungszeiten wahrscheinlich durch die Zeit, die für die Verarbeitung der Komprimierung benötigt wird, überschattet. Komprimieren Sie keine Dateien, die kleiner sind als ungefähr 150-1000 Byte (abhängig vom Inhalt der Datei und der Effizienz der Komprimierung). Der Aufwand für die Komprimierung kleiner Dateien kann eine komprimierte Datei erzeugen, die größer als die nicht komprimierte Datei ist.

Wenn ein Client komprimierte Inhalte verarbeiten kann, muss der Client den Server über seine Funktionen informieren, indem `Accept-Encoding` er die-Kopfzeile mit der Anforderung sendet. Wenn ein Server komprimierten Inhalt sendet, muss er Informationen in der `Content-Encoding` Kopfzeile enthalten, in der die komprimierte Antwort codiert wird. In der folgenden Tabelle sind die von der Middleware unterstützten Inhalts Codierungs Bezeichnungen aufgeführt.

::: moniker range=">= aspnetcore-2.2"

| `Accept-Encoding`Header Werte | Unterstützte Middleware | Beschreibung |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Ja (Standard)        | [Brotli-komprimiertes Datenformat](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Nein                   | [Komprimiertes Datenformat deflate](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Nein                   | [Der XML-Austausch (effizient)](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Ja                  | [Gzip-Dateiformat](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Ja                  | Bezeichner "keine Codierung": Die Antwort darf nicht codiert werden. |
| `pack200-gzip`                  | Nein                   | [Netzwerk Übertragungs Format für Java-Archive](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Ja                  | Alle verfügbaren Inhalts Codierungen, die nicht explizit angefordert werden |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| `Accept-Encoding`Header Werte | Unterstützte Middleware | Beschreibung |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Nein                   | [Brotli-komprimiertes Datenformat](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Nein                   | [Komprimiertes Datenformat deflate](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Nein                   | [Der XML-Austausch (effizient)](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Ja (Standard)        | [Gzip-Dateiformat](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Ja                  | Bezeichner "keine Codierung": Die Antwort darf nicht codiert werden. |
| `pack200-gzip`                  | Nein                   | [Netzwerk Übertragungs Format für Java-Archive](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Ja                  | Alle verfügbaren Inhalts Codierungen, die nicht explizit angefordert werden |

::: moniker-end

Weitere Informationen finden Sie in der [offiziellen Programmier Liste der IANA-Inhalte](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

Die Middleware ermöglicht das Hinzufügen zusätzlicher Komprimierungs Anbieter für `Accept-Encoding` benutzerdefinierte Header Werte. Weitere Informationen finden Sie unten unter [benutzerdefinierte Anbieter](#custom-providers) .

Die Middleware ist in der Lage, auf die Gewichtung des Qualitäts Werts `q`(qvalue) zu reagieren, wenn Sie vom Client gesendet wird, um Komprimierungs Schemas zu priorisieren Weitere Informationen finden [Sie unter RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Komprimierungs Algorithmen unterliegen einem Kompromiss zwischen der Komprimierungs Geschwindigkeit und der Effektivität der Komprimierung. *Effektivität* in diesem Kontext bezieht sich auf die Größe der Ausgabe nach der Komprimierung. Die kleinste Größe wird durch die *optimale* Komprimierung erreicht.

Die Header, die beim anfordern, senden, Zwischenspeichern und empfangen von komprimiertem Inhalt beteiligt sind, werden in der folgenden Tabelle beschrieben.

| Header             | Rolle |
| ------------------ | ---- |
| `Accept-Encoding`  | Wird vom Client an den Server gesendet, um die Inhalts Codierungs Schemas anzugeben, die für den Client zulässig sind. |
| `Content-Encoding` | Wird vom Server an den Client gesendet, um die Codierung des Inhalts in der Nutzlast anzugeben. |
| `Content-Length`   | Wenn die Komprimierung erfolgt `Content-Length` , wird der-Header entfernt, da sich der Text Inhalt ändert, wenn die Antwort komprimiert wird. |
| `Content-MD5`      | Wenn die Komprimierung erfolgt `Content-MD5` , wird der Header entfernt, da sich der Text Inhalt geändert hat und der Hash nicht mehr gültig ist. |
| `Content-Type`     | Gibt den MIME-Typ des Inhalts an. Jede Antwort sollte die `Content-Type`zugehörige angeben. Die Middleware überprüft diesen Wert, um zu bestimmen, ob die Antwort komprimiert werden soll. Die Middleware gibt eine Reihe von [Standard-MIME-Typen](#mime-types) an, die codiert werden können, aber Sie können MIME-Typen ersetzen oder hinzufügen. |
| `Vary`             | Beim Senden durch den Server mit dem Wert `Accept-Encoding` an Clients und Proxys gibt der `Vary` -Header dem Client oder dem Proxy an, dass er Antworten basierend `Accept-Encoding` auf dem Wert des-Headers der Anforderung Zwischenspeichern (variieren) soll. Das Ergebnis der Rückgabe von Inhalt mit `Vary: Accept-Encoding` dem-Header besteht darin, dass sowohl komprimierte als auch nicht komprimierte Antworten separat zwischengespeichert werden. |

Erkunden Sie die Features der Middleware für die Antwort Komprimierung mit der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples). Das Beispiel veranschaulicht Folgendes:

* Die Komprimierung von App-Antworten mithilfe von gzip und benutzerdefinierten Komprimierungs Anbietern.
* Vorgehensweise beim Hinzufügen eines MIME-Typs zur Standardliste von MIME-Typen für die Komprimierung.

## <a name="package"></a>Package

::: moniker range=">= aspnetcore-3.0"

Die Middleware für die Antwort Komprimierung wird vom Paket [Microsoft. aspnetcore. Response Compression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) bereitgestellt, das implizit in ASP.net Core-Apps enthalten ist.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Fügen Sie einen Verweis auf das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)hinzu, das das Paket [Microsoft. aspnetcore. Response Compression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) enthält, um die Middleware in ein Projekt einzubeziehen.

::: moniker-end

## <a name="configuration"></a>Konfiguration

::: moniker range=">= aspnetcore-2.2"

Der folgende Code zeigt, wie Sie die Middleware für die Antwort Komprimierung für Standard-MIME-Typen und Komprimierungs Anbieter ([brotli](#brotli-compression-provider) und [gzip](#gzip-compression-provider)) aktivieren:

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Der folgende Code zeigt, wie Sie die Middleware für die Antwort Komprimierung für Standard-MIME-Typen und den [GZip-Komprimierungs Anbieter](#gzip-compression-provider)aktivieren:

::: moniker-end

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddResponseCompression();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseResponseCompression();
    }
}
```

Notizen:

* `app.UseResponseCompression`muss vor `app.UseMvc`aufgerufen werden.
* Verwenden Sie ein Tool wie z. b. " [fddler](https://www.telerik.com/fiddler)", " [Firebug](https://getfirebug.com/)" oder " [Postman](https://www.getpostman.com/) ", um den `Accept-Encoding` Anforderungs Header festzulegen und die Antwortheader, die Größe und den Text

Senden Sie eine Anforderung an die Beispiel-APP `Accept-Encoding` ohne den-Header, und beobachten Sie, dass die Antwort nicht komprimiert ist. Der `Content-Encoding` - `Vary` Header und der-Header sind in der Antwort nicht vorhanden.

![Das Anforderungsfenster zeigt das Ergebnis einer Anforderung ohne den Accept-Encoding-Header an. Die Antwort ist nicht komprimiert.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

Senden Sie eine Anforderung an die Beispiel-APP `Accept-Encoding: br` mit dem-Header (brotli-Komprimierung), und beobachten Sie, dass die Antwort komprimiert ist. Der `Content-Encoding` - `Vary` Header und der-Header sind in der Antwort vorhanden.

![Das Fenster "dateiddler" zeigt das Ergebnis einer Anforderung mit dem Accept-Encoding-Header und dem Wert "br" an. Die variierenden und Content-Encoding-Header werden der Antwort hinzugefügt. Die Antwort ist komprimiert.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Senden Sie eine Anforderung an die Beispiel-APP `Accept-Encoding: gzip` mit dem-Header, und beobachten Sie, dass die Antwort komprimiert ist. Der `Content-Encoding` - `Vary` Header und der-Header sind in der Antwort vorhanden.

![Das Fenster "dateiddler" zeigt das Ergebnis einer Anforderung mit dem Accept-Encoding-Header und dem Wert "gzip" an. Die variierenden und Content-Encoding-Header werden der Antwort hinzugefügt. Die Antwort ist komprimiert.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a>Anbieter

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a>Brotli-Komprimierungs Anbieter

Verwenden Sie, um Antworten mit dem von [brotli komprimierten Datenformat](https://tools.ietf.org/html/rfc7932)zu komprimieren. <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>

Wenn keine Komprimierungs Anbieter explizit zum <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>hinzugefügt werden:

* Der brotli-Komprimierungs Anbieter wird dem Array von Komprimierungs Anbietern zusammen mit dem [gzip](#gzip-compression-provider)-Komprimierungs Anbieter standardmäßig hinzugefügt.
* Bei der Komprimierung wird standardmäßig die brotli-Komprimierung verwendet, wenn das von der Client komprimierte brotli-Datenformat Wenn brotli nicht vom Client unterstützt wird, wird standardmäßig gzip verwendet, wenn der Client die gzip-Komprimierung unterstützt.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Der broesli-Komprimierungs Anbieter muss hinzugefügt werden, wenn Komprimierungs Anbieter explizit hinzugefügt werden:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Legen Sie die Komprimierungs <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>Ebene mit fest. Der brotli-Komprimierungs Anbieter verwendet standardmäßig den schnellsten Komprimierungs Grad ([CompressionLevel. schnellste](xref:System.IO.Compression.CompressionLevel)), der möglicherweise nicht die effizienteste Komprimierung erzeugt. Wenn die effizienteste Komprimierung erwünscht ist, konfigurieren Sie die Middleware für die optimale Komprimierung.

| Komprimierungs Ebene | Beschreibung |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | Die Komprimierung sollte so schnell wie möglich durchgeführt werden, auch wenn die resultierende Ausgabe nicht optimal komprimiert ist. |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | Es sollte keine Komprimierung durchgeführt werden. |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | Antworten sollten optimal komprimiert werden, auch wenn die Komprimierung mehr Zeit in Anspruch nimmt. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<BrotliCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

::: moniker-end

### <a name="gzip-compression-provider"></a>GZip-Komprimierungs Anbieter

Verwenden Sie, um Antworten mit dem [gzip-Dateiformat](https://tools.ietf.org/html/rfc1952)zu komprimieren. <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>

Wenn keine Komprimierungs Anbieter explizit zum <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>hinzugefügt werden:

::: moniker range=">= aspnetcore-2.2"

* Der GZip-Komprimierungs Anbieter wird dem Array von Komprimierungs Anbietern standardmäßig zusammen mit dem [brotli-Komprimierungs Anbieter](#brotli-compression-provider)hinzugefügt.
* Bei der Komprimierung wird standardmäßig die brotli-Komprimierung verwendet, wenn das von der Client komprimierte brotli-Datenformat Wenn brotli nicht vom Client unterstützt wird, wird standardmäßig gzip verwendet, wenn der Client die gzip-Komprimierung unterstützt.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Der GZip-Komprimierungs Anbieter wird standardmäßig dem Array von Komprimierungs Anbietern hinzugefügt.
* Standardmäßig wird gzip verwendet, wenn der Client die gzip-Komprimierung unterstützt.

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Der GZip-Komprimierungs Anbieter muss hinzugefügt werden, wenn Komprimierungs Anbieter explizit hinzugefügt werden:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

Legen Sie die Komprimierungs <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>Ebene mit fest. Der GZip-Komprimierungs Anbieter verwendet standardmäßig den schnellsten Komprimierungs Grad ([CompressionLevel. schnellste](xref:System.IO.Compression.CompressionLevel)), der möglicherweise nicht die effizienteste Komprimierung erzeugt. Wenn die effizienteste Komprimierung erwünscht ist, konfigurieren Sie die Middleware für die optimale Komprimierung.

| Komprimierungs Ebene | Beschreibung |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | Die Komprimierung sollte so schnell wie möglich durchgeführt werden, auch wenn die resultierende Ausgabe nicht optimal komprimiert ist. |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | Es sollte keine Komprimierung durchgeführt werden. |
| [CompressionLevel.Optimal](xref:System.IO.Compression.CompressionLevel) | Antworten sollten optimal komprimiert werden, auch wenn die Komprimierung mehr Zeit in Anspruch nimmt. |

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();

    services.Configure<GzipCompressionProviderOptions>(options => 
    {
        options.Level = CompressionLevel.Fastest;
    });
}
```

### <a name="custom-providers"></a>Benutzerdefinierte Anbieter

Erstellen Sie benutzerdefinierte Komprimierungs Implementierungen mit <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>. Stellt die von diesem `ICompressionProvider` erzeugte Inhalts Codierung dar. <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> Die Middleware verwendet diese Informationen, um den Anbieter auf der Grundlage der im `Accept-Encoding` Header der Anforderung angegebenen Liste auszuwählen.

Mithilfe der Beispiel-App übermittelt der Client eine Anforderung mit `Accept-Encoding: mycustomcompression` dem-Header. Die Middleware verwendet die Implementierung der benutzerdefinierten Komprimierung und gibt die `Content-Encoding: mycustomcompression` Antwort mit einem-Header zurück. Der Client muss in der Lage sein, die benutzerdefinierte Codierung zu decodieren, damit eine benutzerdefinierte Komprimierungs Implementierung funktioniert.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

Senden Sie eine Anforderung an die Beispiel-APP `Accept-Encoding: mycustomcompression` mit dem-Header, und beobachten Sie die Antwortheader. Der `Vary` - `Content-Encoding` Header und der-Header sind in der Antwort vorhanden. Der Antworttext (nicht angezeigt) wird nicht durch das Beispiel komprimiert. In der `CustomCompressionProvider` -Klasse des Beispiels ist keine Komprimierungs Implementierung vorhanden. Das Beispiel zeigt jedoch, wo Sie einen solchen Komprimierungs Algorithmus implementieren würden.

![Das Fenster "dateiddler" zeigt das Ergebnis einer Anforderung mit dem Accept-Encoding-Header und einem Wert von "mycustomcompression" an. Die variierenden und Content-Encoding-Header werden der Antwort hinzugefügt.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME-Typen

Die Middleware gibt einen Standardsatz von MIME-Typen für die Komprimierung an:

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

Ersetzen Sie MIME-Typen, oder fügen Sie Sie durch die Optionen für die Antwort Komprimierungs Middleware Beachten Sie, dass Platzhalter-MIME `text/*` -Typen wie nicht unterstützt werden. Die Beispiel-App Fügt einen MIME- `image/svg+xml` Typ für hinzu und komprimiert das ASP.net Core Banner Bild (*Banner. SVG*).

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a>Komprimierung mit sicherem Protokoll

Komprimierte Antworten über sichere Verbindungen können mit der `EnableForHttps` Option gesteuert werden, die standardmäßig deaktiviert ist. Die Verwendung der Komprimierung mit dynamisch generierten Seiten kann zu Sicherheitsproblemen führen, wie z. B. [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit))- und [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit))-Angriffen.

## <a name="adding-the-vary-header"></a>Hinzufügen des Vary-Headers

Beim Komprimieren von Antworten auf der Grundlage `Accept-Encoding` des Headers gibt es potenziell mehrere komprimierte Versionen der Antwort und eine nicht komprimierte Version. Um Client-und Proxy Caches anzuweisen, dass mehrere Versionen vorhanden sind und gespeichert werden sollten `Vary` , wird der-Header `Accept-Encoding` mit einem-Wert hinzugefügt. In ASP.net Core 2,0 oder höher fügt die Middleware den `Vary` Header automatisch hinzu, wenn die Antwort komprimiert wird.

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Middleware-Problem, wenn hinter einem nginx-Reverseproxy

Wenn eine Anforderung von nginx bereitgestellt wird, wird `Accept-Encoding` der Header entfernt. Durch das Entfernen `Accept-Encoding` des Headers wird verhindert, dass die Middleware die Antwort komprimiert. Weitere Informationen finden Sie unter [NGINX: Komprimierung und Komprimierung](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Dieses Problem wird nachverfolgt [, indem Sie die Pass-Through-Komprimierung für nginx (ASPNET/ \#basicmiddleware 123) ermitteln](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Arbeiten mit dynamischer IIS-Komprimierung

Wenn Sie ein aktives Modul für die dynamische IIS-Komprimierung auf Serverebene konfiguriert haben, die Sie für eine App deaktivieren möchten, deaktivieren Sie das Modul mit einer Addition zur Datei " *Web. config* ". Weitere Informationen finden Sie unter [Disabling IIS modules (Deaktivieren von IIS-Modulen)](xref:host-and-deploy/iis/modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Problembehandlung

Verwenden Sie ein Tool wie z. b. " [fddler](https://www.telerik.com/fiddler)", " [Firebug](https://getfirebug.com/)" oder [Postman](https://www.getpostman.com/), mit dem Sie den `Accept-Encoding` Anforderungs Header festlegen und die Antwortheader, die Größe und den Text überprüfen können. Standardmäßig komprimiert die Middleware für die Antwort Komprimierung Antworten, die die folgenden Bedingungen erfüllen:

::: moniker range=">= aspnetcore-2.2"

* Der `Accept-Encoding` -Header ist mit dem `br`Wert, `gzip`, `*`oder der benutzerdefinierten Codierung vorhanden, die mit einem benutzerdefinierten Komprimierungs Anbieter übereinstimmt, den Sie eingerichtet haben. Der Wert darf nicht sein `identity` oder einen Qualitäts Wert (qvalue, `q`) der Einstellung 0 (null) aufweisen.
* Der MIME-Typ`Content-Type`() muss festgelegt werden und mit einem MIME-Typ, <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>der im konfiguriert ist, identisch sein.
* Die Anforderung darf den `Content-Range` -Header nicht enthalten.
* Die Anforderung muss das unsichere Protokoll (http) verwenden, es sei denn, das sichere Protokoll (HTTPS) ist in den Optionen der Middleware für die Antwort Komprimierung konfiguriert. *Beachten Sie die [oben beschriebene](#compression-with-secure-protocol) Gefahr beim Aktivieren der Komprimierung von sicherem Inhalt.*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Der `Accept-Encoding` -Header ist mit dem `gzip`Wert, `*`oder der benutzerdefinierten Codierung vorhanden, die mit einem benutzerdefinierten Komprimierungs Anbieter übereinstimmt, den Sie eingerichtet haben. Der Wert darf nicht sein `identity` oder einen Qualitäts Wert (qvalue, `q`) der Einstellung 0 (null) aufweisen.
* Der MIME-Typ`Content-Type`() muss festgelegt werden und mit einem MIME-Typ, <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>der im konfiguriert ist, identisch sein.
* Die Anforderung darf den `Content-Range` -Header nicht enthalten.
* Die Anforderung muss das unsichere Protokoll (http) verwenden, es sei denn, das sichere Protokoll (HTTPS) ist in den Optionen der Middleware für die Antwort Komprimierung konfiguriert. *Beachten Sie die [oben beschriebene](#compression-with-secure-protocol) Gefahr beim Aktivieren der Komprimierung von sicherem Inhalt.*

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla Developer Network: Accept-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231, Abschnitt 3.1.2.1: Inhalts Codierungen](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230, Abschnitt 4.2.3: Gzip-Codierung](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [Gzip-Dateiformat Spezifikation, Version 4,3](https://www.ietf.org/rfc/rfc1952.txt)
