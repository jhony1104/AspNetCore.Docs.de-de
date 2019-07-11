---
title: Antwortkomprimierung in ASP.NET Core
author: guardrex
description: Informationen zur Antwortkomprimierung und zum Verwenden von Antworten komprimierender Middleware in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: performance/response-compression
ms.openlocfilehash: d5d2da3dc0a8a452de97d98161d429389d2f7638
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815613"
---
# <a name="response-compression-in-aspnet-core"></a>Antwortkomprimierung in ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Die Netzwerkbandbreite ist eine eingeschränkte Ressource. Verringern der Größe der Antwort in der Regel erhöht die Reaktionsfähigkeit einer App, häufig erheblich. Eine Möglichkeit zum Reduzieren der Größe der Nutzlast ist zum Komprimieren von Antworten von der app.

## <a name="when-to-use-response-compression-middleware"></a>Antworten komprimierende Middleware verwenden

Verwenden Sie Server-basierte Antwort komprimierungstechnologien in IIS, Apache oder Nginx. Die Leistung der Middleware wird nicht mit dem der Servermodule wahrscheinlich überein. [HTTP.sys-Server](xref:fundamentals/servers/httpsys) Server und [Kestrel](xref:fundamentals/servers/kestrel) Server keine integrierte komprimierungsunterstützung derzeit anbieten.

Verwenden Sie Antworten komprimierende Middleware, wenn Sie sich befinden:

* Kann nicht die folgenden Server-basierten komprimierungstechnologien verwendet werden:
  * [Dynamische Komprimierung in IIS-Modul](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [Apache Mod_deflate-Modul](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [Nginx-Komprimierung und Dekomprimierung](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* Hosten direkt auf:
  * [HTTP.sys-Server](xref:fundamentals/servers/httpsys) (früher als "WebListener" bezeichnet)
  * [Kestrel-server](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a>Antwortkomprimierung

In der Regel kann alle Antworten, die nicht komprimierte antwortkomprimierung nutzen. Antworten, die in der Regel nicht komprimiert sind: CSS, JavaScript, HTML, XML und JSON. Sie sollten nicht systemintern komprimierter Assets, wie z. B. PNG-Dateien komprimieren. Wenn Sie versuchen, eine systemintern komprimierte Antwort weiter komprimiert, wird keine zusätzliche kleine Verringerung Größe und die Übertragung wahrscheinlich mit der Zeit, die zum Verarbeiten der Komprimierung benötigten überholt. Komprimieren Sie Dateien, die kleiner als ungefähr 150 – 1000 Bytes (je nach Inhalt der Datei und die Effizienz der Komprimierung) nicht. Der Mehraufwand für das Komprimieren von kleinen Dateien kann es sich um eine komprimierte Datei, die größer als die nicht komprimierte Datei führen.

Wenn ein Client komprimierten Inhalte verarbeiten kann, muss der Client den Server seiner Funktionen per informieren die `Accept-Encoding` Header mit der Anforderung. Wenn ein Server komprimierten Inhalte sendet, muss Informationen enthalten die `Content-Encoding` Header wie die komprimierte Antwort codiert wird. In der folgenden Tabelle werden die Inhalte Codierung Bezeichnungen, die von der Middleware unterstützt angezeigt.

::: moniker range=">= aspnetcore-2.2"

| `Accept-Encoding` Headerwerte | Middleware unterstützt | Beschreibung |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Ja (Standard)        | [Format der Brotli-komprimierte Daten](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Nein                   | [Komprimierte Daten DEFLATE-format](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Nein                   | [W3C effiziente XML-Austausch](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Ja                  | [GZIP-Dateiformat](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Ja                  | "Keine Codierung"-ID: Die Antwort muss nicht codiert werden. |
| `pack200-gzip`                  | Nein                   | [Netzwerk-Übertragungsformat für Java-Archive](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Ja                  | Alle verfügbaren Inhalte, die Codierung wird nicht explizit angefordert |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| `Accept-Encoding` Headerwerte | Middleware unterstützt | Beschreibung |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | Nein                   | [Format der Brotli-komprimierte Daten](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | Nein                   | [Komprimierte Daten DEFLATE-format](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | Nein                   | [W3C effiziente XML-Austausch](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | Ja (Standard)        | [GZIP-Dateiformat](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | Ja                  | "Keine Codierung"-ID: Die Antwort muss nicht codiert werden. |
| `pack200-gzip`                  | Nein                   | [Netzwerk-Übertragungsformat für Java-Archive](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | Ja                  | Alle verfügbaren Inhalte, die Codierung wird nicht explizit angefordert |

::: moniker-end

Weitere Informationen finden Sie unter den [IANA offizielle Codierung Inhaltsliste](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).

Die Middleware können Sie zusätzliche Komprimierung-Anbieter für benutzerdefinierte hinzufügen `Accept-Encoding` Headerwerte. Weitere Informationen finden Sie unter [benutzerdefinierte Anbieter](#custom-providers) unten.

Die Middleware der Reaktion auf Qualitätswert fähig ist (Qvalue, `q`) Gewichtung, wenn vom Client gesendet werden, um Komprimierungsschemas zu priorisieren. Weitere Informationen finden Sie unter [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).

Komprimierungsalgorithmen unterliegen bildet einen Kompromiss zwischen Geschwindigkeit von Komprimierung und die Effektivität der Komprimierung aus. *Effektivität* in diesem Kontext bezieht sich auf die Größe der Ausgabe nach der Komprimierung. Die kleinste Größe wird erreicht, indem die am häufigsten *optimale* Komprimierung.

Die Header, die beim anfordern, werden das Senden und Zwischenspeichern von komprimierten Inhalten empfangen in der folgenden Tabelle beschrieben.

| Header             | Rolle |
| ------------------ | ---- |
| `Accept-Encoding`  | Vom Client an den Server an, dass die inhaltscodierung Schemas zulässig ist, an den Client gesendet. |
| `Content-Encoding` | Vom Server an den Client, um anzugeben, die Codierung des Inhalts in der Nutzlast gesendet. |
| `Content-Length`   | Bei der Komprimierung werden die `Content-Length` Header entfernt werden, da der Inhalt Text geändert, wenn die Antwort komprimiert wird. |
| `Content-MD5`      | Bei der Komprimierung werden die `Content-MD5` Header entfernt, da der Inhalt des Nachrichtentexts geändert hat und der Hash nicht mehr gültig ist. |
| `Content-Type`     | Gibt den MIME-Typ des Inhalts an. Jede Antwort geben sollte seine `Content-Type`. Die Middleware überprüft diesen Wert, um zu bestimmen, ob die Antwort, komprimiert werden sollen. Die Middleware gibt einen Satz von [Standard-MIME-Typen](#mime-types) , die codiert werden können, aber Sie können ersetzen oder MIME-Typen hinzufügen. |
| `Vary`             | Wenn vom Server mit dem Wert gesendet `Accept-Encoding` für Clients und -Proxys der `Vary` Header gibt an, an den Client oder Proxy, der zwischengespeichert werden soll (variieren) Antworten basierend auf den Wert der `Accept-Encoding` -Header der Anforderung. Das Ergebnis der Rückgabe von Inhalten mit der `Vary: Accept-Encoding` -Header ist, dass sowohl komprimierte und nicht komprimierte Antworten getrennt zwischengespeichert werden. |

Erkunden Sie die Funktionen des die Antworten komprimierende Middleware mit der [Beispiel-app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples). Das Beispiel veranschaulicht:

* Die Komprimierung von app-Antworten, die mithilfe von Gzip und benutzerdefinierte Komprimierung-Anbietern.
* Wie Sie die Standardliste der MIME-Typen für die Komprimierung einen MIME-Typ hinzufügen.

## <a name="package"></a>Package

::: moniker range=">= aspnetcore-2.1"

Um die Middleware in einem Projekt einzuschließen, fügen Sie einen Verweis auf die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app), wozu die [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) Paket.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Um die Middleware in einem Projekt einzuschließen, fügen Sie einen Verweis auf die [metapaket "Microsoft.aspnetcore.All"](xref:fundamentals/metapackage), wozu die [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) Paket.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Um die Middleware in einem Projekt einzuschließen, fügen Sie einen Verweis auf die [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) Paket.

::: moniker-end

## <a name="configuration"></a>Konfiguration

::: moniker range=">= aspnetcore-2.2"

Der folgende Code zeigt, wie Sie die Antworten komprimierende Middleware für die Standard-MIME-Typen und Komprimierung Anbieter aktivieren ([Brotli](#brotli-compression-provider) und [Gzip](#gzip-compression-provider)):

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Der folgende Code zeigt, wie Sie die Antworten komprimierende Middleware für Standard-MIME-Typen zu aktivieren und die [Gzip-Komprimierung Anbieter](#gzip-compression-provider):

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

* `app.UseResponseCompression` muss aufgerufen werden, bevor `app.UseMvc`.
* Verwenden Sie ein Tool wie z. B. [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), oder [Postman](https://www.getpostman.com/) Festlegen der `Accept-Encoding` Anforderungsheader und Untersuchen der Antwortheader, Größe und Text.

Senden Sie eine Anforderung zur Beispiel-app ohne die `Accept-Encoding` Header und beobachten Sie, dass die Antwort nicht komprimiert. Die `Content-Encoding` und `Vary` Header nicht in der Antwort vorhanden sind.

![Fiddler-Fenster und Ergebnis einer Anforderung ohne den Accept-Encoding-Header. Die Antwort nicht komprimiert.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

Senden Sie eine Anforderung für die Beispielapp mit der `Accept-Encoding: br` Header (Brotli-Komprimierung), und beobachten Sie, dass die Antwort komprimiert werden. Die `Content-Encoding` und `Vary` -Header in der Antwort vorhanden sind.

![Fiddler-Fenster mit Ergebnis einer Anforderung mit dem Accept-Encoding-Header und den Br-Wert. Die Vary und Content-Encoding-Header werden an die Antwort hinzugefügt. Die Antwort wird komprimiert.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Senden Sie eine Anforderung für die Beispielapp mit der `Accept-Encoding: gzip` Header und beobachten Sie, dass die Antwort komprimiert werden. Die `Content-Encoding` und `Vary` -Header in der Antwort vorhanden sind.

![Fiddler-Fenster mit Ergebnis einer Anforderung mit dem Accept-Encoding-Header und einem Wert von Gzip. Die Vary und Content-Encoding-Header werden an die Antwort hinzugefügt. Die Antwort wird komprimiert.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a>Anbieter

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a>Anbieter der Brotli-Komprimierung

Verwenden der <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> zum Komprimieren von Antworten mit dem die [Brotli komprimiertes Datenformat](https://tools.ietf.org/html/rfc7932).

Wenn keine Komprimierung Anbieter explizit hinzugefügt werden die <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

* Der Anbieter der Brotli-Komprimierung wird standardmäßig hinzugefügt, in das Array der Komprimierung Anbieter zusammen mit den [Gzip-Komprimierung Anbieter](#gzip-compression-provider).
* Komprimierung standardmäßig auf Brotli-Komprimierung auf, wenn das Format der Brotli-komprimierte Daten vom Client unterstützt wird. Wenn Brotli vom Client unterstützt wird, standardmäßig Komprimierung Gzip an, wenn der Client die Gzip-Komprimierung unterstützt wird.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Der Brotoli Komprimierung-Anbieter muss hinzugefügt werden, wenn alle Anbieter Komprimierung explizit hinzugefügt werden:

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=5)]

Stellen Sie die Komprimierung mit <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>. Der Anbieter der Brotli-Komprimierung ist standardmäßig die schnellste Komprimierung ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), die möglicherweise nicht die effizienteste Komprimierung erzeugen. Wenn die effizienteste Komprimierung gewünscht ist, konfigurieren Sie die Middleware für die optimale Komprimierung.

| Komprimierungsgrad | Beschreibung |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | Komprimierung sollte so schnell wie möglich abgeschlossen werden, auch wenn die resultierende Ausgabe optimal komprimiert ist nicht. |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | Es sollte keine Komprimierung ausgeführt werden. |
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

### <a name="gzip-compression-provider"></a>Anbieter der Gzip-Komprimierung

Verwenden der <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> zum Komprimieren von Antworten mit dem die [Gzip-Dateiformat](https://tools.ietf.org/html/rfc1952).

Wenn keine Komprimierung Anbieter explizit hinzugefügt werden die <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:

::: moniker range=">= aspnetcore-2.2"

* Der Anbieter der Gzip-Komprimierung wird standardmäßig hinzugefügt, in das Array der Komprimierung Anbieter zusammen mit den [Brotli-Komprimierung Anbieter](#brotli-compression-provider).
* Komprimierung standardmäßig auf Brotli-Komprimierung auf, wenn das Format der Brotli-komprimierte Daten vom Client unterstützt wird. Wenn Brotli vom Client unterstützt wird, standardmäßig Komprimierung Gzip an, wenn der Client die Gzip-Komprimierung unterstützt wird.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Der Anbieter der Gzip-Komprimierung wird standardmäßig in das Array der Komprimierung Anbieter hinzugefügt.
* Komprimierung wird standardmäßig auf Gzip, wenn der Client die Gzip-Komprimierung unterstützt.

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

Der Anbieter der Gzip-Komprimierung muss hinzugefügt werden, wenn alle Anbieter Komprimierung explizit hinzugefügt werden:

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=5)]

::: moniker-end

Stellen Sie die Komprimierung mit <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>. Der Anbieter der Gzip-Komprimierung ist standardmäßig die schnellste Komprimierung ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), die möglicherweise nicht die effizienteste Komprimierung erzeugen. Wenn die effizienteste Komprimierung gewünscht ist, konfigurieren Sie die Middleware für die optimale Komprimierung.

| Komprimierungsgrad | Beschreibung |
| ----------------- | ----------- |
| [CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel) | Komprimierung sollte so schnell wie möglich abgeschlossen werden, auch wenn die resultierende Ausgabe optimal komprimiert ist nicht. |
| [CompressionLevel.NoCompression](xref:System.IO.Compression.CompressionLevel) | Es sollte keine Komprimierung ausgeführt werden. |
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

Erstellen von benutzerdefinierten Komprimierung Implementierungen mit <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>. Die <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> stellt den Inhalt, Codierung, das von diesem `ICompressionProvider` erzeugt. Die Middleware verwendet diese Informationen, die basierend auf der Liste, die im angegebenen Anbieter auswählen der `Accept-Encoding` -Header der Anforderung.

Verwenden die Beispiel-app, die der Client sendet einer Anforderung mit der `Accept-Encoding: mycustomcompression` Header. Die Middleware die Implementierung von benutzerdefinierten Komprimierung verwendet und gibt die Antwort mit einem `Content-Encoding: mycustomcompression` Header. Der Client muss dekomprimiert werden, die benutzerdefinierte Codierung in der Reihenfolge für die Implementierung eines benutzerdefinierten Komprimierung arbeiten können.

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=6)]

[!code-csharp[](response-compression/samples/1.x/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

Senden Sie eine Anforderung für die Beispielapp mit der `Accept-Encoding: mycustomcompression` Header, und beobachten Sie die Header der Antwort. Die `Vary` und `Content-Encoding` -Header in der Antwort vorhanden sind. Der Text der Antwort (nicht gezeigt) wird nicht durch das Beispiel komprimiert. Es ist nicht in eine Implementierung von Komprimierung das `CustomCompressionProvider` Klasse des Beispiels. Das Beispiel zeigt jedoch, in denen Sie solche einen Komprimierungsalgorithmus implementieren würden.

![Fiddler-Fenster mit Ergebnis einer Anforderung mit dem Accept-Encoding-Header und einem Wert von Mycustomcompression. Die Vary und Content-Encoding-Header werden an die Antwort hinzugefügt.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a>MIME-Typen

Die Middleware gibt eine Reihe von MIME-Typen für die Komprimierung:

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

Ersetzen Sie oder fügen Sie die MIME-Typen mit den Antworten komprimierende Middleware-Optionen. Beachten Sie diese Platzhalter-MIME-Typen, z. B. `text/*` werden nicht unterstützt. Die Beispiel-app Fügt einen MIME-Typ für `image/svg+xml` und komprimiert und dient dem ASP.NET Core Bannerbild (*banner.svg*).

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](response-compression/samples/2.x/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet2&highlight=7-9)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a>Komprimierung mit sicheren Protokolls

Komprimierte Antworten über sichere Verbindungen können gesteuert werden, mit der `EnableForHttps` Option, die standardmäßig deaktiviert ist. Dynamisch generierten Seiten mit Komprimierung Sicherheitsprobleme führen kann, wie z. B. die [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) und [SICHERHEITSVERLETZUNG](https://wikipedia.org/wiki/BREACH_(security_exploit)) Angriffe.

## <a name="adding-the-vary-header"></a>Den Vary-Header hinzufügen

::: moniker range=">= aspnetcore-2.0"

Beim Komprimieren von Antworten basierend auf den `Accept-Encoding` -Header, es gibt potenziell mehrere komprimierte Versionen der Antwort und eine nicht komprimierte Version. Um den Client und Proxy-Caches anzuweisen, dass mehrere Versionen vorhanden sind und gespeichert werden soll, die `Vary` Header hinzugefügt, um mit einem `Accept-Encoding` Wert. In ASP.NET Core 2.0 oder höher, fügt die Middleware die `Vary` Header automatisch, wenn die Antwort komprimiert wird.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Beim Komprimieren von Antworten basierend auf den `Accept-Encoding` -Header, es gibt potenziell mehrere komprimierte Versionen der Antwort und eine nicht komprimierte Version. Um den Client und Proxy-Caches anzuweisen, dass mehrere Versionen vorhanden sind und gespeichert werden soll, die `Vary` Header hinzugefügt, um mit einem `Accept-Encoding` Wert. In ASP.NET Core 1.x, Hinzufügen der `Vary` Header in die Antwort erfolgt manuell:

[!code-csharp[](response-compression/samples/1.x/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a>Middleware-Problem, wenn der Server einen Nginx-reverse-proxy

Wenn eine Anforderung über einen Proxy von Nginx, ist die `Accept-Encoding` Header entfernt wird. Entfernen der `Accept-Encoding` Header wird verhindert, dass die Middleware die Antwort zu komprimieren. Weitere Informationen finden Sie unter [NGINX: Komprimierung und Dekomprimierung](https://www.nginx.com/resources/admin-guide/compression-and-decompression/). Dieses Problem wird nachverfolgt, indem [herausfinden, Pass-Through-Komprimierung für Nginx (Aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).

## <a name="working-with-iis-dynamic-compression"></a>Arbeiten mit dynamische Komprimierung in IIS

Wenn Sie ein aktives IIS dynamische Komprimierung Modul konfiguriert werden, auf der Serverebene, die Sie für eine app deaktivieren möchten haben, deaktivieren Sie das Modul mit der eine Ergänzung der *"Web.config"* Datei. Weitere Informationen finden Sie unter [Disabling IIS modules (Deaktivieren von IIS-Modulen)](xref:host-and-deploy/iis/modules#disabling-iis-modules).

## <a name="troubleshooting"></a>Problembehandlung

Mit einem Tool wie [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), oder [Postman](https://www.getpostman.com/), mit denen Sie festlegen, die `Accept-Encoding` Anforderungsheader und Untersuchen der Antwortheader, Größe und Text. Antworten komprimierende Middleware komprimiert standardmäßig Antworten, die die folgenden Bedingungen erfüllen:

::: moniker range=">= aspnetcore-2.2"

* Die `Accept-Encoding` Header mit dem Wert vorhanden ist `br`, `gzip`, `*`, oder benutzerdefinierte Codierung, die einen benutzerdefinierten Komprimierung Anbieter entspricht, die Sie eingerichtet haben. Der Wert darf nicht sein `identity` oder über einen Qualitätswert (Qvalue, `q`) von 0 (null) festlegen.
* Der MIME-Typ (`Content-Type`) muss festgelegt sein und muss einen MIME-Typ auf konfiguriert entsprechen den <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.
* Die Anforderung dürfen keine der `Content-Range` Header.
* Die Anforderung muss unsicheres Protokoll (http) verwenden, es sei denn, sicheres Protokoll (Https) in den Antworten komprimierende Middleware-Optionen konfiguriert ist. *Beachten Sie die Gefahr [oben beschriebenen](#compression-with-secure-protocol) beim Aktivieren der Komprimierung für sichere Inhalte.*

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* Die `Accept-Encoding` Header mit dem Wert vorhanden ist `gzip`, `*`, oder benutzerdefinierte Codierung, die einen benutzerdefinierten Komprimierung Anbieter entspricht, die Sie eingerichtet haben. Der Wert darf nicht sein `identity` oder über einen Qualitätswert (Qvalue, `q`) von 0 (null) festlegen.
* Der MIME-Typ (`Content-Type`) muss festgelegt sein und muss einen MIME-Typ auf konfiguriert entsprechen den <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.
* Die Anforderung dürfen keine der `Content-Range` Header.
* Die Anforderung muss unsicheres Protokoll (http) verwenden, es sei denn, sicheres Protokoll (Https) in den Antworten komprimierende Middleware-Optionen konfiguriert ist. *Beachten Sie die Gefahr [oben beschriebenen](#compression-with-secure-protocol) beim Aktivieren der Komprimierung für sichere Inhalte.*

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [Mozilla Developer Network: Accept-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [RFC 7231 Abschnitt 3.1.2.1: Inhalt Codings](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [RFC 7230 Abschnitt 4.2.3: Gzip-Codierung](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [GZIP-Datei-Format Specification Version 4.3](https://www.ietf.org/rfc/rfc1952.txt)
