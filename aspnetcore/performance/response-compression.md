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
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="e3988-103">Antwort Komprimierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="e3988-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="e3988-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e3988-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e3988-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e3988-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="e3988-106">Die Netzwerkbandbreite ist eine begrenzte Ressource.</span><span class="sxs-lookup"><span data-stu-id="e3988-106">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="e3988-107">Die Reduzierung der Antwort Größe erhöht in der Regel die Reaktionsfähigkeit einer APP, oft erheblich.</span><span class="sxs-lookup"><span data-stu-id="e3988-107">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="e3988-108">Eine Möglichkeit, die Nutz Last Größen zu reduzieren, ist das Komprimieren der Antworten einer App.</span><span class="sxs-lookup"><span data-stu-id="e3988-108">One way to reduce payload sizes is to compress an app's responses.</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="e3988-109">Verwendung der Middleware für die Antwort Komprimierung</span><span class="sxs-lookup"><span data-stu-id="e3988-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="e3988-110">Verwenden Sie serverbasierte Antwort Komprimierungs Technologien in IIS, Apache oder nginx.</span><span class="sxs-lookup"><span data-stu-id="e3988-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="e3988-111">Die Leistung der Middleware stimmt wahrscheinlich nicht mit der Leistung der Server Module zusammen.</span><span class="sxs-lookup"><span data-stu-id="e3988-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="e3988-112">Der [http. sys-Server](xref:fundamentals/servers/httpsys) Server und der [Kestrel](xref:fundamentals/servers/kestrel) -Server bieten derzeit keine integrierte Komprimierungs Unterstützung.</span><span class="sxs-lookup"><span data-stu-id="e3988-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="e3988-113">Verwenden Sie die Middleware für die Antwort Komprimierung, wenn Sie:</span><span class="sxs-lookup"><span data-stu-id="e3988-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="e3988-114">Die folgenden serverbasierten Komprimierungs Technologien können nicht verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="e3988-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="e3988-115">Modul für dynamische IIS-Komprimierung</span><span class="sxs-lookup"><span data-stu-id="e3988-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="e3988-116">Apache mod_deflate-Modul</span><span class="sxs-lookup"><span data-stu-id="e3988-116">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="e3988-117">Nginx-Komprimierung und-Komprimierung</span><span class="sxs-lookup"><span data-stu-id="e3988-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="e3988-118">Direktes Hosting:</span><span class="sxs-lookup"><span data-stu-id="e3988-118">Hosting directly on:</span></span>
  * <span data-ttu-id="e3988-119">[Http. sys-Server](xref:fundamentals/servers/httpsys) (früher als Weblistener bezeichnet)</span><span class="sxs-lookup"><span data-stu-id="e3988-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="e3988-120">Kestrel-Server</span><span class="sxs-lookup"><span data-stu-id="e3988-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="e3988-121">Antwortkomprimierung</span><span class="sxs-lookup"><span data-stu-id="e3988-121">Response compression</span></span>

<span data-ttu-id="e3988-122">Normalerweise kann jede nicht System intern komprimierte Antwort von der Antwort Komprimierung profitieren.</span><span class="sxs-lookup"><span data-stu-id="e3988-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="e3988-123">Nicht nativ komprimierte Antworten umfassen in der Regel Folgendes: CSS, JavaScript, HTML, XML und JSON.</span><span class="sxs-lookup"><span data-stu-id="e3988-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="e3988-124">Sie sollten nativ komprimierte Assets, z. b. png-Dateien, nicht komprimieren.</span><span class="sxs-lookup"><span data-stu-id="e3988-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="e3988-125">Wenn Sie versuchen, eine nativ komprimierte Antwort weiter zu komprimieren, werden alle kleinen zusätzlichen Reduzierungs-und Übertragungszeiten wahrscheinlich durch die Zeit, die für die Verarbeitung der Komprimierung benötigt wird, überschattet.</span><span class="sxs-lookup"><span data-stu-id="e3988-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="e3988-126">Komprimieren Sie keine Dateien, die kleiner sind als ungefähr 150-1000 Byte (abhängig vom Inhalt der Datei und der Effizienz der Komprimierung).</span><span class="sxs-lookup"><span data-stu-id="e3988-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="e3988-127">Der Aufwand für die Komprimierung kleiner Dateien kann eine komprimierte Datei erzeugen, die größer als die nicht komprimierte Datei ist.</span><span class="sxs-lookup"><span data-stu-id="e3988-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="e3988-128">Wenn ein Client komprimierte Inhalte verarbeiten kann, muss der Client den Server über seine Funktionen informieren, indem `Accept-Encoding` er die-Kopfzeile mit der Anforderung sendet.</span><span class="sxs-lookup"><span data-stu-id="e3988-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="e3988-129">Wenn ein Server komprimierten Inhalt sendet, muss er Informationen in der `Content-Encoding` Kopfzeile enthalten, in der die komprimierte Antwort codiert wird.</span><span class="sxs-lookup"><span data-stu-id="e3988-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="e3988-130">In der folgenden Tabelle sind die von der Middleware unterstützten Inhalts Codierungs Bezeichnungen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="e3988-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="e3988-131">`Accept-Encoding`Header Werte</span><span class="sxs-lookup"><span data-stu-id="e3988-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="e3988-132">Unterstützte Middleware</span><span class="sxs-lookup"><span data-stu-id="e3988-132">Middleware Supported</span></span> | <span data-ttu-id="e3988-133">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e3988-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="e3988-134">Ja (Standard)</span><span class="sxs-lookup"><span data-stu-id="e3988-134">Yes (default)</span></span>        | [<span data-ttu-id="e3988-135">Brotli-komprimiertes Datenformat</span><span class="sxs-lookup"><span data-stu-id="e3988-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="e3988-136">Nein</span><span class="sxs-lookup"><span data-stu-id="e3988-136">No</span></span>                   | [<span data-ttu-id="e3988-137">Komprimiertes Datenformat deflate</span><span class="sxs-lookup"><span data-stu-id="e3988-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="e3988-138">Nein</span><span class="sxs-lookup"><span data-stu-id="e3988-138">No</span></span>                   | [<span data-ttu-id="e3988-139">Der XML-Austausch (effizient)</span><span class="sxs-lookup"><span data-stu-id="e3988-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="e3988-140">Ja</span><span class="sxs-lookup"><span data-stu-id="e3988-140">Yes</span></span>                  | [<span data-ttu-id="e3988-141">Gzip-Dateiformat</span><span class="sxs-lookup"><span data-stu-id="e3988-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="e3988-142">Ja</span><span class="sxs-lookup"><span data-stu-id="e3988-142">Yes</span></span>                  | <span data-ttu-id="e3988-143">Bezeichner "keine Codierung": Die Antwort darf nicht codiert werden.</span><span class="sxs-lookup"><span data-stu-id="e3988-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="e3988-144">Nein</span><span class="sxs-lookup"><span data-stu-id="e3988-144">No</span></span>                   | [<span data-ttu-id="e3988-145">Netzwerk Übertragungs Format für Java-Archive</span><span class="sxs-lookup"><span data-stu-id="e3988-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="e3988-146">Ja</span><span class="sxs-lookup"><span data-stu-id="e3988-146">Yes</span></span>                  | <span data-ttu-id="e3988-147">Alle verfügbaren Inhalts Codierungen, die nicht explizit angefordert werden</span><span class="sxs-lookup"><span data-stu-id="e3988-147">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="e3988-148">`Accept-Encoding`Header Werte</span><span class="sxs-lookup"><span data-stu-id="e3988-148">`Accept-Encoding` header values</span></span> | <span data-ttu-id="e3988-149">Unterstützte Middleware</span><span class="sxs-lookup"><span data-stu-id="e3988-149">Middleware Supported</span></span> | <span data-ttu-id="e3988-150">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e3988-150">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="e3988-151">Nein</span><span class="sxs-lookup"><span data-stu-id="e3988-151">No</span></span>                   | [<span data-ttu-id="e3988-152">Brotli-komprimiertes Datenformat</span><span class="sxs-lookup"><span data-stu-id="e3988-152">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="e3988-153">Nein</span><span class="sxs-lookup"><span data-stu-id="e3988-153">No</span></span>                   | [<span data-ttu-id="e3988-154">Komprimiertes Datenformat deflate</span><span class="sxs-lookup"><span data-stu-id="e3988-154">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="e3988-155">Nein</span><span class="sxs-lookup"><span data-stu-id="e3988-155">No</span></span>                   | [<span data-ttu-id="e3988-156">Der XML-Austausch (effizient)</span><span class="sxs-lookup"><span data-stu-id="e3988-156">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="e3988-157">Ja (Standard)</span><span class="sxs-lookup"><span data-stu-id="e3988-157">Yes (default)</span></span>        | [<span data-ttu-id="e3988-158">Gzip-Dateiformat</span><span class="sxs-lookup"><span data-stu-id="e3988-158">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="e3988-159">Ja</span><span class="sxs-lookup"><span data-stu-id="e3988-159">Yes</span></span>                  | <span data-ttu-id="e3988-160">Bezeichner "keine Codierung": Die Antwort darf nicht codiert werden.</span><span class="sxs-lookup"><span data-stu-id="e3988-160">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="e3988-161">Nein</span><span class="sxs-lookup"><span data-stu-id="e3988-161">No</span></span>                   | [<span data-ttu-id="e3988-162">Netzwerk Übertragungs Format für Java-Archive</span><span class="sxs-lookup"><span data-stu-id="e3988-162">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="e3988-163">Ja</span><span class="sxs-lookup"><span data-stu-id="e3988-163">Yes</span></span>                  | <span data-ttu-id="e3988-164">Alle verfügbaren Inhalts Codierungen, die nicht explizit angefordert werden</span><span class="sxs-lookup"><span data-stu-id="e3988-164">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

<span data-ttu-id="e3988-165">Weitere Informationen finden Sie in der [offiziellen Programmier Liste der IANA-Inhalte](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span><span class="sxs-lookup"><span data-stu-id="e3988-165">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="e3988-166">Die Middleware ermöglicht das Hinzufügen zusätzlicher Komprimierungs Anbieter für `Accept-Encoding` benutzerdefinierte Header Werte.</span><span class="sxs-lookup"><span data-stu-id="e3988-166">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="e3988-167">Weitere Informationen finden Sie unten unter [benutzerdefinierte Anbieter](#custom-providers) .</span><span class="sxs-lookup"><span data-stu-id="e3988-167">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="e3988-168">Die Middleware ist in der Lage, auf die Gewichtung des Qualitäts Werts `q`(qvalue) zu reagieren, wenn Sie vom Client gesendet wird, um Komprimierungs Schemas zu priorisieren</span><span class="sxs-lookup"><span data-stu-id="e3988-168">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="e3988-169">Weitere Informationen finden [Sie unter RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span><span class="sxs-lookup"><span data-stu-id="e3988-169">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="e3988-170">Komprimierungs Algorithmen unterliegen einem Kompromiss zwischen der Komprimierungs Geschwindigkeit und der Effektivität der Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="e3988-170">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="e3988-171">*Effektivität* in diesem Kontext bezieht sich auf die Größe der Ausgabe nach der Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="e3988-171">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="e3988-172">Die kleinste Größe wird durch die *optimale* Komprimierung erreicht.</span><span class="sxs-lookup"><span data-stu-id="e3988-172">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="e3988-173">Die Header, die beim anfordern, senden, Zwischenspeichern und empfangen von komprimiertem Inhalt beteiligt sind, werden in der folgenden Tabelle beschrieben.</span><span class="sxs-lookup"><span data-stu-id="e3988-173">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="e3988-174">Header</span><span class="sxs-lookup"><span data-stu-id="e3988-174">Header</span></span>             | <span data-ttu-id="e3988-175">Rolle</span><span class="sxs-lookup"><span data-stu-id="e3988-175">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="e3988-176">Wird vom Client an den Server gesendet, um die Inhalts Codierungs Schemas anzugeben, die für den Client zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="e3988-176">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="e3988-177">Wird vom Server an den Client gesendet, um die Codierung des Inhalts in der Nutzlast anzugeben.</span><span class="sxs-lookup"><span data-stu-id="e3988-177">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="e3988-178">Wenn die Komprimierung erfolgt `Content-Length` , wird der-Header entfernt, da sich der Text Inhalt ändert, wenn die Antwort komprimiert wird.</span><span class="sxs-lookup"><span data-stu-id="e3988-178">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="e3988-179">Wenn die Komprimierung erfolgt `Content-MD5` , wird der Header entfernt, da sich der Text Inhalt geändert hat und der Hash nicht mehr gültig ist.</span><span class="sxs-lookup"><span data-stu-id="e3988-179">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="e3988-180">Gibt den MIME-Typ des Inhalts an.</span><span class="sxs-lookup"><span data-stu-id="e3988-180">Specifies the MIME type of the content.</span></span> <span data-ttu-id="e3988-181">Jede Antwort sollte die `Content-Type`zugehörige angeben.</span><span class="sxs-lookup"><span data-stu-id="e3988-181">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="e3988-182">Die Middleware überprüft diesen Wert, um zu bestimmen, ob die Antwort komprimiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="e3988-182">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="e3988-183">Die Middleware gibt eine Reihe von [Standard-MIME-Typen](#mime-types) an, die codiert werden können, aber Sie können MIME-Typen ersetzen oder hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="e3988-183">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="e3988-184">Beim Senden durch den Server mit dem Wert `Accept-Encoding` an Clients und Proxys gibt der `Vary` -Header dem Client oder dem Proxy an, dass er Antworten basierend `Accept-Encoding` auf dem Wert des-Headers der Anforderung Zwischenspeichern (variieren) soll.</span><span class="sxs-lookup"><span data-stu-id="e3988-184">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="e3988-185">Das Ergebnis der Rückgabe von Inhalt mit `Vary: Accept-Encoding` dem-Header besteht darin, dass sowohl komprimierte als auch nicht komprimierte Antworten separat zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="e3988-185">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="e3988-186">Erkunden Sie die Features der Middleware für die Antwort Komprimierung mit der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span><span class="sxs-lookup"><span data-stu-id="e3988-186">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="e3988-187">Das Beispiel veranschaulicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e3988-187">The sample illustrates:</span></span>

* <span data-ttu-id="e3988-188">Die Komprimierung von App-Antworten mithilfe von gzip und benutzerdefinierten Komprimierungs Anbietern.</span><span class="sxs-lookup"><span data-stu-id="e3988-188">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="e3988-189">Vorgehensweise beim Hinzufügen eines MIME-Typs zur Standardliste von MIME-Typen für die Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="e3988-189">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="e3988-190">Package</span><span class="sxs-lookup"><span data-stu-id="e3988-190">Package</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e3988-191">Die Middleware für die Antwort Komprimierung wird vom Paket [Microsoft. aspnetcore. Response Compression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) bereitgestellt, das implizit in ASP.net Core-Apps enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="e3988-191">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e3988-192">Fügen Sie einen Verweis auf das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)hinzu, das das Paket [Microsoft. aspnetcore. Response Compression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) enthält, um die Middleware in ein Projekt einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="e3988-192">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="e3988-193">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="e3988-193">Configuration</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e3988-194">Der folgende Code zeigt, wie Sie die Middleware für die Antwort Komprimierung für Standard-MIME-Typen und Komprimierungs Anbieter ([brotli](#brotli-compression-provider) und [gzip](#gzip-compression-provider)) aktivieren:</span><span class="sxs-lookup"><span data-stu-id="e3988-194">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e3988-195">Der folgende Code zeigt, wie Sie die Middleware für die Antwort Komprimierung für Standard-MIME-Typen und den [GZip-Komprimierungs Anbieter](#gzip-compression-provider)aktivieren:</span><span class="sxs-lookup"><span data-stu-id="e3988-195">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="e3988-196">Notizen:</span><span class="sxs-lookup"><span data-stu-id="e3988-196">Notes:</span></span>

* <span data-ttu-id="e3988-197">`app.UseResponseCompression`muss vor `app.UseMvc`aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="e3988-197">`app.UseResponseCompression` must be called before `app.UseMvc`.</span></span>
* <span data-ttu-id="e3988-198">Verwenden Sie ein Tool wie z. b. " [fddler](https://www.telerik.com/fiddler)", " [Firebug](https://getfirebug.com/)" oder " [Postman](https://www.getpostman.com/) ", um den `Accept-Encoding` Anforderungs Header festzulegen und die Antwortheader, die Größe und den Text</span><span class="sxs-lookup"><span data-stu-id="e3988-198">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="e3988-199">Senden Sie eine Anforderung an die Beispiel-APP `Accept-Encoding` ohne den-Header, und beobachten Sie, dass die Antwort nicht komprimiert ist.</span><span class="sxs-lookup"><span data-stu-id="e3988-199">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="e3988-200">Der `Content-Encoding` - `Vary` Header und der-Header sind in der Antwort nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="e3988-200">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Das Anforderungsfenster zeigt das Ergebnis einer Anforderung ohne den Accept-Encoding-Header an.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e3988-203">Senden Sie eine Anforderung an die Beispiel-APP `Accept-Encoding: br` mit dem-Header (brotli-Komprimierung), und beobachten Sie, dass die Antwort komprimiert ist.</span><span class="sxs-lookup"><span data-stu-id="e3988-203">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="e3988-204">Der `Content-Encoding` - `Vary` Header und der-Header sind in der Antwort vorhanden.</span><span class="sxs-lookup"><span data-stu-id="e3988-204">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Das Fenster "dateiddler" zeigt das Ergebnis einer Anforderung mit dem Accept-Encoding-Header und dem Wert "br" an.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="e3988-208">Senden Sie eine Anforderung an die Beispiel-APP `Accept-Encoding: gzip` mit dem-Header, und beobachten Sie, dass die Antwort komprimiert ist.</span><span class="sxs-lookup"><span data-stu-id="e3988-208">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="e3988-209">Der `Content-Encoding` - `Vary` Header und der-Header sind in der Antwort vorhanden.</span><span class="sxs-lookup"><span data-stu-id="e3988-209">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Das Fenster "dateiddler" zeigt das Ergebnis einer Anforderung mit dem Accept-Encoding-Header und dem Wert "gzip" an.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a><span data-ttu-id="e3988-213">Anbieter</span><span class="sxs-lookup"><span data-stu-id="e3988-213">Providers</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a><span data-ttu-id="e3988-214">Brotli-Komprimierungs Anbieter</span><span class="sxs-lookup"><span data-stu-id="e3988-214">Brotli Compression Provider</span></span>

<span data-ttu-id="e3988-215">Verwenden Sie, um Antworten mit dem von [brotli komprimierten Datenformat](https://tools.ietf.org/html/rfc7932)zu komprimieren. <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider></span><span class="sxs-lookup"><span data-stu-id="e3988-215">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="e3988-216">Wenn keine Komprimierungs Anbieter explizit zum <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="e3988-216">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="e3988-217">Der brotli-Komprimierungs Anbieter wird dem Array von Komprimierungs Anbietern zusammen mit dem [gzip](#gzip-compression-provider)-Komprimierungs Anbieter standardmäßig hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e3988-217">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="e3988-218">Bei der Komprimierung wird standardmäßig die brotli-Komprimierung verwendet, wenn das von der Client komprimierte brotli-Datenformat</span><span class="sxs-lookup"><span data-stu-id="e3988-218">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="e3988-219">Wenn brotli nicht vom Client unterstützt wird, wird standardmäßig gzip verwendet, wenn der Client die gzip-Komprimierung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e3988-219">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="e3988-220">Der broesli-Komprimierungs Anbieter muss hinzugefügt werden, wenn Komprimierungs Anbieter explizit hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="e3988-220">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="e3988-221">Legen Sie die Komprimierungs <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>Ebene mit fest.</span><span class="sxs-lookup"><span data-stu-id="e3988-221">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="e3988-222">Der brotli-Komprimierungs Anbieter verwendet standardmäßig den schnellsten Komprimierungs Grad ([CompressionLevel. schnellste](xref:System.IO.Compression.CompressionLevel)), der möglicherweise nicht die effizienteste Komprimierung erzeugt.</span><span class="sxs-lookup"><span data-stu-id="e3988-222">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="e3988-223">Wenn die effizienteste Komprimierung erwünscht ist, konfigurieren Sie die Middleware für die optimale Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="e3988-223">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="e3988-224">Komprimierungs Ebene</span><span class="sxs-lookup"><span data-stu-id="e3988-224">Compression Level</span></span> | <span data-ttu-id="e3988-225">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e3988-225">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="e3988-226">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="e3988-226">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="e3988-227">Die Komprimierung sollte so schnell wie möglich durchgeführt werden, auch wenn die resultierende Ausgabe nicht optimal komprimiert ist.</span><span class="sxs-lookup"><span data-stu-id="e3988-227">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="e3988-228">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="e3988-228">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="e3988-229">Es sollte keine Komprimierung durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e3988-229">No compression should be performed.</span></span> |
| [<span data-ttu-id="e3988-230">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="e3988-230">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="e3988-231">Antworten sollten optimal komprimiert werden, auch wenn die Komprimierung mehr Zeit in Anspruch nimmt.</span><span class="sxs-lookup"><span data-stu-id="e3988-231">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="e3988-232">GZip-Komprimierungs Anbieter</span><span class="sxs-lookup"><span data-stu-id="e3988-232">Gzip Compression Provider</span></span>

<span data-ttu-id="e3988-233">Verwenden Sie, um Antworten mit dem [gzip-Dateiformat](https://tools.ietf.org/html/rfc1952)zu komprimieren. <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider></span><span class="sxs-lookup"><span data-stu-id="e3988-233">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="e3988-234">Wenn keine Komprimierungs Anbieter explizit zum <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="e3988-234">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="e3988-235">Der GZip-Komprimierungs Anbieter wird dem Array von Komprimierungs Anbietern standardmäßig zusammen mit dem [brotli-Komprimierungs Anbieter](#brotli-compression-provider)hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e3988-235">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="e3988-236">Bei der Komprimierung wird standardmäßig die brotli-Komprimierung verwendet, wenn das von der Client komprimierte brotli-Datenformat</span><span class="sxs-lookup"><span data-stu-id="e3988-236">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="e3988-237">Wenn brotli nicht vom Client unterstützt wird, wird standardmäßig gzip verwendet, wenn der Client die gzip-Komprimierung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e3988-237">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="e3988-238">Der GZip-Komprimierungs Anbieter wird standardmäßig dem Array von Komprimierungs Anbietern hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e3988-238">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="e3988-239">Standardmäßig wird gzip verwendet, wenn der Client die gzip-Komprimierung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e3988-239">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="e3988-240">Der GZip-Komprimierungs Anbieter muss hinzugefügt werden, wenn Komprimierungs Anbieter explizit hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="e3988-240">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

<span data-ttu-id="e3988-241">Legen Sie die Komprimierungs <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>Ebene mit fest.</span><span class="sxs-lookup"><span data-stu-id="e3988-241">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="e3988-242">Der GZip-Komprimierungs Anbieter verwendet standardmäßig den schnellsten Komprimierungs Grad ([CompressionLevel. schnellste](xref:System.IO.Compression.CompressionLevel)), der möglicherweise nicht die effizienteste Komprimierung erzeugt.</span><span class="sxs-lookup"><span data-stu-id="e3988-242">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="e3988-243">Wenn die effizienteste Komprimierung erwünscht ist, konfigurieren Sie die Middleware für die optimale Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="e3988-243">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="e3988-244">Komprimierungs Ebene</span><span class="sxs-lookup"><span data-stu-id="e3988-244">Compression Level</span></span> | <span data-ttu-id="e3988-245">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e3988-245">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="e3988-246">CompressionLevel.Fastest</span><span class="sxs-lookup"><span data-stu-id="e3988-246">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="e3988-247">Die Komprimierung sollte so schnell wie möglich durchgeführt werden, auch wenn die resultierende Ausgabe nicht optimal komprimiert ist.</span><span class="sxs-lookup"><span data-stu-id="e3988-247">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="e3988-248">CompressionLevel.NoCompression</span><span class="sxs-lookup"><span data-stu-id="e3988-248">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="e3988-249">Es sollte keine Komprimierung durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e3988-249">No compression should be performed.</span></span> |
| [<span data-ttu-id="e3988-250">CompressionLevel.Optimal</span><span class="sxs-lookup"><span data-stu-id="e3988-250">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="e3988-251">Antworten sollten optimal komprimiert werden, auch wenn die Komprimierung mehr Zeit in Anspruch nimmt.</span><span class="sxs-lookup"><span data-stu-id="e3988-251">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="e3988-252">Benutzerdefinierte Anbieter</span><span class="sxs-lookup"><span data-stu-id="e3988-252">Custom providers</span></span>

<span data-ttu-id="e3988-253">Erstellen Sie benutzerdefinierte Komprimierungs Implementierungen mit <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span><span class="sxs-lookup"><span data-stu-id="e3988-253">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="e3988-254">Stellt die von diesem `ICompressionProvider` erzeugte Inhalts Codierung dar. <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*></span><span class="sxs-lookup"><span data-stu-id="e3988-254">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="e3988-255">Die Middleware verwendet diese Informationen, um den Anbieter auf der Grundlage der im `Accept-Encoding` Header der Anforderung angegebenen Liste auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="e3988-255">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="e3988-256">Mithilfe der Beispiel-App übermittelt der Client eine Anforderung mit `Accept-Encoding: mycustomcompression` dem-Header.</span><span class="sxs-lookup"><span data-stu-id="e3988-256">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="e3988-257">Die Middleware verwendet die Implementierung der benutzerdefinierten Komprimierung und gibt die `Content-Encoding: mycustomcompression` Antwort mit einem-Header zurück.</span><span class="sxs-lookup"><span data-stu-id="e3988-257">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="e3988-258">Der Client muss in der Lage sein, die benutzerdefinierte Codierung zu decodieren, damit eine benutzerdefinierte Komprimierungs Implementierung funktioniert.</span><span class="sxs-lookup"><span data-stu-id="e3988-258">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="e3988-259">Senden Sie eine Anforderung an die Beispiel-APP `Accept-Encoding: mycustomcompression` mit dem-Header, und beobachten Sie die Antwortheader.</span><span class="sxs-lookup"><span data-stu-id="e3988-259">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="e3988-260">Der `Vary` - `Content-Encoding` Header und der-Header sind in der Antwort vorhanden.</span><span class="sxs-lookup"><span data-stu-id="e3988-260">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="e3988-261">Der Antworttext (nicht angezeigt) wird nicht durch das Beispiel komprimiert.</span><span class="sxs-lookup"><span data-stu-id="e3988-261">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="e3988-262">In der `CustomCompressionProvider` -Klasse des Beispiels ist keine Komprimierungs Implementierung vorhanden.</span><span class="sxs-lookup"><span data-stu-id="e3988-262">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="e3988-263">Das Beispiel zeigt jedoch, wo Sie einen solchen Komprimierungs Algorithmus implementieren würden.</span><span class="sxs-lookup"><span data-stu-id="e3988-263">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Das Fenster "dateiddler" zeigt das Ergebnis einer Anforderung mit dem Accept-Encoding-Header und einem Wert von "mycustomcompression" an.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="e3988-266">MIME-Typen</span><span class="sxs-lookup"><span data-stu-id="e3988-266">MIME types</span></span>

<span data-ttu-id="e3988-267">Die Middleware gibt einen Standardsatz von MIME-Typen für die Komprimierung an:</span><span class="sxs-lookup"><span data-stu-id="e3988-267">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="e3988-268">Ersetzen Sie MIME-Typen, oder fügen Sie Sie durch die Optionen für die Antwort Komprimierungs Middleware</span><span class="sxs-lookup"><span data-stu-id="e3988-268">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="e3988-269">Beachten Sie, dass Platzhalter-MIME `text/*` -Typen wie nicht unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="e3988-269">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="e3988-270">Die Beispiel-App Fügt einen MIME- `image/svg+xml` Typ für hinzu und komprimiert das ASP.net Core Banner Bild (*Banner. SVG*).</span><span class="sxs-lookup"><span data-stu-id="e3988-270">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="e3988-271">Komprimierung mit sicherem Protokoll</span><span class="sxs-lookup"><span data-stu-id="e3988-271">Compression with secure protocol</span></span>

<span data-ttu-id="e3988-272">Komprimierte Antworten über sichere Verbindungen können mit der `EnableForHttps` Option gesteuert werden, die standardmäßig deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="e3988-272">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="e3988-273">Die Verwendung der Komprimierung mit dynamisch generierten Seiten kann zu Sicherheitsproblemen führen, wie z. B. [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit))- und [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit))-Angriffen.</span><span class="sxs-lookup"><span data-stu-id="e3988-273">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="e3988-274">Hinzufügen des Vary-Headers</span><span class="sxs-lookup"><span data-stu-id="e3988-274">Adding the Vary header</span></span>

<span data-ttu-id="e3988-275">Beim Komprimieren von Antworten auf der Grundlage `Accept-Encoding` des Headers gibt es potenziell mehrere komprimierte Versionen der Antwort und eine nicht komprimierte Version.</span><span class="sxs-lookup"><span data-stu-id="e3988-275">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="e3988-276">Um Client-und Proxy Caches anzuweisen, dass mehrere Versionen vorhanden sind und gespeichert werden sollten `Vary` , wird der-Header `Accept-Encoding` mit einem-Wert hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e3988-276">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="e3988-277">In ASP.net Core 2,0 oder höher fügt die Middleware den `Vary` Header automatisch hinzu, wenn die Antwort komprimiert wird.</span><span class="sxs-lookup"><span data-stu-id="e3988-277">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="e3988-278">Middleware-Problem, wenn hinter einem nginx-Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="e3988-278">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="e3988-279">Wenn eine Anforderung von nginx bereitgestellt wird, wird `Accept-Encoding` der Header entfernt.</span><span class="sxs-lookup"><span data-stu-id="e3988-279">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="e3988-280">Durch das Entfernen `Accept-Encoding` des Headers wird verhindert, dass die Middleware die Antwort komprimiert.</span><span class="sxs-lookup"><span data-stu-id="e3988-280">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="e3988-281">Weitere Informationen finden Sie unter [NGINX: Komprimierung und Komprimierung](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span><span class="sxs-lookup"><span data-stu-id="e3988-281">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="e3988-282">Dieses Problem wird nachverfolgt [, indem Sie die Pass-Through-Komprimierung für nginx (ASPNET/ \#basicmiddleware 123) ermitteln](https://github.com/aspnet/BasicMiddleware/issues/123).</span><span class="sxs-lookup"><span data-stu-id="e3988-282">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware \#123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="e3988-283">Arbeiten mit dynamischer IIS-Komprimierung</span><span class="sxs-lookup"><span data-stu-id="e3988-283">Working with IIS dynamic compression</span></span>

<span data-ttu-id="e3988-284">Wenn Sie ein aktives Modul für die dynamische IIS-Komprimierung auf Serverebene konfiguriert haben, die Sie für eine App deaktivieren möchten, deaktivieren Sie das Modul mit einer Addition zur Datei " *Web. config* ".</span><span class="sxs-lookup"><span data-stu-id="e3988-284">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="e3988-285">Weitere Informationen finden Sie unter [Disabling IIS modules (Deaktivieren von IIS-Modulen)](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="e3988-285">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="e3988-286">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="e3988-286">Troubleshooting</span></span>

<span data-ttu-id="e3988-287">Verwenden Sie ein Tool wie z. b. " [fddler](https://www.telerik.com/fiddler)", " [Firebug](https://getfirebug.com/)" oder [Postman](https://www.getpostman.com/), mit dem Sie den `Accept-Encoding` Anforderungs Header festlegen und die Antwortheader, die Größe und den Text überprüfen können.</span><span class="sxs-lookup"><span data-stu-id="e3988-287">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="e3988-288">Standardmäßig komprimiert die Middleware für die Antwort Komprimierung Antworten, die die folgenden Bedingungen erfüllen:</span><span class="sxs-lookup"><span data-stu-id="e3988-288">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="e3988-289">Der `Accept-Encoding` -Header ist mit dem `br`Wert, `gzip`, `*`oder der benutzerdefinierten Codierung vorhanden, die mit einem benutzerdefinierten Komprimierungs Anbieter übereinstimmt, den Sie eingerichtet haben.</span><span class="sxs-lookup"><span data-stu-id="e3988-289">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="e3988-290">Der Wert darf nicht sein `identity` oder einen Qualitäts Wert (qvalue, `q`) der Einstellung 0 (null) aufweisen.</span><span class="sxs-lookup"><span data-stu-id="e3988-290">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="e3988-291">Der MIME-Typ`Content-Type`() muss festgelegt werden und mit einem MIME-Typ, <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>der im konfiguriert ist, identisch sein.</span><span class="sxs-lookup"><span data-stu-id="e3988-291">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="e3988-292">Die Anforderung darf den `Content-Range` -Header nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="e3988-292">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="e3988-293">Die Anforderung muss das unsichere Protokoll (http) verwenden, es sei denn, das sichere Protokoll (HTTPS) ist in den Optionen der Middleware für die Antwort Komprimierung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="e3988-293">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="e3988-294">*Beachten Sie die [oben beschriebene](#compression-with-secure-protocol) Gefahr beim Aktivieren der Komprimierung von sicherem Inhalt.*</span><span class="sxs-lookup"><span data-stu-id="e3988-294">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="e3988-295">Der `Accept-Encoding` -Header ist mit dem `gzip`Wert, `*`oder der benutzerdefinierten Codierung vorhanden, die mit einem benutzerdefinierten Komprimierungs Anbieter übereinstimmt, den Sie eingerichtet haben.</span><span class="sxs-lookup"><span data-stu-id="e3988-295">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="e3988-296">Der Wert darf nicht sein `identity` oder einen Qualitäts Wert (qvalue, `q`) der Einstellung 0 (null) aufweisen.</span><span class="sxs-lookup"><span data-stu-id="e3988-296">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="e3988-297">Der MIME-Typ`Content-Type`() muss festgelegt werden und mit einem MIME-Typ, <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>der im konfiguriert ist, identisch sein.</span><span class="sxs-lookup"><span data-stu-id="e3988-297">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="e3988-298">Die Anforderung darf den `Content-Range` -Header nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="e3988-298">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="e3988-299">Die Anforderung muss das unsichere Protokoll (http) verwenden, es sei denn, das sichere Protokoll (HTTPS) ist in den Optionen der Middleware für die Antwort Komprimierung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="e3988-299">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="e3988-300">*Beachten Sie die [oben beschriebene](#compression-with-secure-protocol) Gefahr beim Aktivieren der Komprimierung von sicherem Inhalt.*</span><span class="sxs-lookup"><span data-stu-id="e3988-300">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="e3988-301">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e3988-301">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="e3988-302">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="e3988-302">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="e3988-303">RFC 7231, Abschnitt 3.1.2.1: Inhalts Codierungen</span><span class="sxs-lookup"><span data-stu-id="e3988-303">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="e3988-304">RFC 7230, Abschnitt 4.2.3: Gzip-Codierung</span><span class="sxs-lookup"><span data-stu-id="e3988-304">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="e3988-305">Gzip-Dateiformat Spezifikation, Version 4,3</span><span class="sxs-lookup"><span data-stu-id="e3988-305">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)
