---
title: Antwort Komprimierung in ASP.net Core
author: guardrex
description: Informationen zur Antwortkomprimierung und zum Verwenden von Antworten komprimierender Middleware in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
uid: performance/response-compression
ms.openlocfilehash: b8a84418a3258e9ac43b4eadd8564c0708590bce
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726963"
---
# <a name="response-compression-in-aspnet-core"></a><span data-ttu-id="2da62-103">Antwort Komprimierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="2da62-103">Response compression in ASP.NET Core</span></span>

<span data-ttu-id="2da62-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2da62-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2da62-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2da62-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="2da62-106">Die Netzwerkbandbreite ist eine begrenzte Ressource.</span><span class="sxs-lookup"><span data-stu-id="2da62-106">Network bandwidth is a limited resource.</span></span> <span data-ttu-id="2da62-107">Die Reduzierung der Antwort Größe erhöht in der Regel die Reaktionsfähigkeit einer APP, oft erheblich.</span><span class="sxs-lookup"><span data-stu-id="2da62-107">Reducing the size of the response usually increases the responsiveness of an app, often dramatically.</span></span> <span data-ttu-id="2da62-108">Eine Möglichkeit, die Nutz Last Größen zu reduzieren, ist das Komprimieren der Antworten einer App.</span><span class="sxs-lookup"><span data-stu-id="2da62-108">One way to reduce payload sizes is to compress an app's responses.</span></span>

## <a name="when-to-use-response-compression-middleware"></a><span data-ttu-id="2da62-109">Verwendung der Middleware für die Antwort Komprimierung</span><span class="sxs-lookup"><span data-stu-id="2da62-109">When to use Response Compression Middleware</span></span>

<span data-ttu-id="2da62-110">Verwenden Sie serverbasierte Antwort Komprimierungs Technologien in IIS, Apache oder nginx.</span><span class="sxs-lookup"><span data-stu-id="2da62-110">Use server-based response compression technologies in IIS, Apache, or Nginx.</span></span> <span data-ttu-id="2da62-111">Die Leistung der Middleware stimmt wahrscheinlich nicht mit der Leistung der Server Module zusammen.</span><span class="sxs-lookup"><span data-stu-id="2da62-111">The performance of the middleware probably won't match that of the server modules.</span></span> <span data-ttu-id="2da62-112">Der [http. sys-Server](xref:fundamentals/servers/httpsys) Server und der [Kestrel](xref:fundamentals/servers/kestrel) -Server bieten derzeit keine integrierte Komprimierungs Unterstützung.</span><span class="sxs-lookup"><span data-stu-id="2da62-112">[HTTP.sys server](xref:fundamentals/servers/httpsys) server and [Kestrel](xref:fundamentals/servers/kestrel) server don't currently offer built-in compression support.</span></span>

<span data-ttu-id="2da62-113">Verwenden Sie die Middleware für die Antwort Komprimierung, wenn Sie:</span><span class="sxs-lookup"><span data-stu-id="2da62-113">Use Response Compression Middleware when you're:</span></span>

* <span data-ttu-id="2da62-114">Die folgenden serverbasierten Komprimierungs Technologien können nicht verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="2da62-114">Unable to use the following server-based compression technologies:</span></span>
  * [<span data-ttu-id="2da62-115">Modul für dynamische IIS-Komprimierung</span><span class="sxs-lookup"><span data-stu-id="2da62-115">IIS Dynamic Compression module</span></span>](https://www.iis.net/overview/reliability/dynamiccachingandcompression)
  * [<span data-ttu-id="2da62-116">Apache mod_deflate-Modul</span><span class="sxs-lookup"><span data-stu-id="2da62-116">Apache mod_deflate module</span></span>](https://httpd.apache.org/docs/current/mod/mod_deflate.html)
  * [<span data-ttu-id="2da62-117">Nginx-Komprimierung und-Komprimierung</span><span class="sxs-lookup"><span data-stu-id="2da62-117">Nginx Compression and Decompression</span></span>](https://www.nginx.com/resources/admin-guide/compression-and-decompression/)
* <span data-ttu-id="2da62-118">Direktes Hosting:</span><span class="sxs-lookup"><span data-stu-id="2da62-118">Hosting directly on:</span></span>
  * <span data-ttu-id="2da62-119">[Http. sys-Server](xref:fundamentals/servers/httpsys) (früher als Weblistener bezeichnet)</span><span class="sxs-lookup"><span data-stu-id="2da62-119">[HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener)</span></span>
  * [<span data-ttu-id="2da62-120">Kestrel-Server</span><span class="sxs-lookup"><span data-stu-id="2da62-120">Kestrel server</span></span>](xref:fundamentals/servers/kestrel)

## <a name="response-compression"></a><span data-ttu-id="2da62-121">Antwortkomprimierung</span><span class="sxs-lookup"><span data-stu-id="2da62-121">Response compression</span></span>

<span data-ttu-id="2da62-122">Normalerweise kann jede nicht System intern komprimierte Antwort von der Antwort Komprimierung profitieren.</span><span class="sxs-lookup"><span data-stu-id="2da62-122">Usually, any response not natively compressed can benefit from response compression.</span></span> <span data-ttu-id="2da62-123">Antworten, die nicht nativ komprimiert werden, umfassen in der Regel: CSS, JavaScript, HTML, XML und JSON.</span><span class="sxs-lookup"><span data-stu-id="2da62-123">Responses not natively compressed typically include: CSS, JavaScript, HTML, XML, and JSON.</span></span> <span data-ttu-id="2da62-124">Sie sollten nativ komprimierte Assets, z. b. png-Dateien, nicht komprimieren.</span><span class="sxs-lookup"><span data-stu-id="2da62-124">You shouldn't compress natively compressed assets, such as PNG files.</span></span> <span data-ttu-id="2da62-125">Wenn Sie versuchen, eine nativ komprimierte Antwort weiter zu komprimieren, werden alle kleinen zusätzlichen Reduzierungs-und Übertragungszeiten wahrscheinlich durch die Zeit, die für die Verarbeitung der Komprimierung benötigt wird, überschattet.</span><span class="sxs-lookup"><span data-stu-id="2da62-125">If you attempt to further compress a natively compressed response, any small additional reduction in size and transmission time will likely be overshadowed by the time it took to process the compression.</span></span> <span data-ttu-id="2da62-126">Komprimieren Sie keine Dateien, die kleiner sind als ungefähr 150-1000 Byte (abhängig vom Inhalt der Datei und der Effizienz der Komprimierung).</span><span class="sxs-lookup"><span data-stu-id="2da62-126">Don't compress files smaller than about 150-1000 bytes (depending on the file's content and the efficiency of compression).</span></span> <span data-ttu-id="2da62-127">Der Aufwand für die Komprimierung kleiner Dateien kann eine komprimierte Datei erzeugen, die größer als die nicht komprimierte Datei ist.</span><span class="sxs-lookup"><span data-stu-id="2da62-127">The overhead of compressing small files may produce a compressed file larger than the uncompressed file.</span></span>

<span data-ttu-id="2da62-128">Wenn ein Client komprimierte Inhalte verarbeiten kann, muss der Client den Server über seine Funktionen informieren, indem er den `Accept-Encoding`-Header mit der Anforderung sendet.</span><span class="sxs-lookup"><span data-stu-id="2da62-128">When a client can process compressed content, the client must inform the server of its capabilities by sending the `Accept-Encoding` header with the request.</span></span> <span data-ttu-id="2da62-129">Wenn ein Server komprimierten Inhalt sendet, muss er Informationen im `Content-Encoding`-Header angeben, wie die komprimierte Antwort codiert wird.</span><span class="sxs-lookup"><span data-stu-id="2da62-129">When a server sends compressed content, it must include information in the `Content-Encoding` header on how the compressed response is encoded.</span></span> <span data-ttu-id="2da62-130">In der folgenden Tabelle sind die von der Middleware unterstützten Inhalts Codierungs Bezeichnungen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="2da62-130">Content encoding designations supported by the middleware are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-2.2"

| <span data-ttu-id="2da62-131">`Accept-Encoding` Header Werte</span><span class="sxs-lookup"><span data-stu-id="2da62-131">`Accept-Encoding` header values</span></span> | <span data-ttu-id="2da62-132">Unterstützte Middleware</span><span class="sxs-lookup"><span data-stu-id="2da62-132">Middleware Supported</span></span> | <span data-ttu-id="2da62-133">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="2da62-133">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="2da62-134">Ja (Standard)</span><span class="sxs-lookup"><span data-stu-id="2da62-134">Yes (default)</span></span>        | [<span data-ttu-id="2da62-135">Brotli-komprimiertes Datenformat</span><span class="sxs-lookup"><span data-stu-id="2da62-135">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="2da62-136">Nein</span><span class="sxs-lookup"><span data-stu-id="2da62-136">No</span></span>                   | [<span data-ttu-id="2da62-137">Komprimiertes Datenformat deflate</span><span class="sxs-lookup"><span data-stu-id="2da62-137">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="2da62-138">Nein</span><span class="sxs-lookup"><span data-stu-id="2da62-138">No</span></span>                   | [<span data-ttu-id="2da62-139">Der XML-Austausch (effizient)</span><span class="sxs-lookup"><span data-stu-id="2da62-139">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="2da62-140">Ja</span><span class="sxs-lookup"><span data-stu-id="2da62-140">Yes</span></span>                  | [<span data-ttu-id="2da62-141">Gzip-Dateiformat</span><span class="sxs-lookup"><span data-stu-id="2da62-141">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="2da62-142">Ja</span><span class="sxs-lookup"><span data-stu-id="2da62-142">Yes</span></span>                  | <span data-ttu-id="2da62-143">Bezeichner "keine Codierung": die Antwort darf nicht codiert werden.</span><span class="sxs-lookup"><span data-stu-id="2da62-143">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="2da62-144">Nein</span><span class="sxs-lookup"><span data-stu-id="2da62-144">No</span></span>                   | [<span data-ttu-id="2da62-145">Netzwerk Übertragungs Format für Java-Archive</span><span class="sxs-lookup"><span data-stu-id="2da62-145">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="2da62-146">Ja</span><span class="sxs-lookup"><span data-stu-id="2da62-146">Yes</span></span>                  | <span data-ttu-id="2da62-147">Alle verfügbaren Inhalts Codierungen, die nicht explizit angefordert werden</span><span class="sxs-lookup"><span data-stu-id="2da62-147">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

| <span data-ttu-id="2da62-148">`Accept-Encoding` Header Werte</span><span class="sxs-lookup"><span data-stu-id="2da62-148">`Accept-Encoding` header values</span></span> | <span data-ttu-id="2da62-149">Unterstützte Middleware</span><span class="sxs-lookup"><span data-stu-id="2da62-149">Middleware Supported</span></span> | <span data-ttu-id="2da62-150">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="2da62-150">Description</span></span> |
| ------------------------------- | :------------------: | ----------- |
| `br`                            | <span data-ttu-id="2da62-151">Nein</span><span class="sxs-lookup"><span data-stu-id="2da62-151">No</span></span>                   | [<span data-ttu-id="2da62-152">Brotli-komprimiertes Datenformat</span><span class="sxs-lookup"><span data-stu-id="2da62-152">Brotli compressed data format</span></span>](https://tools.ietf.org/html/rfc7932) |
| `deflate`                       | <span data-ttu-id="2da62-153">Nein</span><span class="sxs-lookup"><span data-stu-id="2da62-153">No</span></span>                   | [<span data-ttu-id="2da62-154">Komprimiertes Datenformat deflate</span><span class="sxs-lookup"><span data-stu-id="2da62-154">DEFLATE compressed data format</span></span>](https://tools.ietf.org/html/rfc1951) |
| `exi`                           | <span data-ttu-id="2da62-155">Nein</span><span class="sxs-lookup"><span data-stu-id="2da62-155">No</span></span>                   | [<span data-ttu-id="2da62-156">Der XML-Austausch (effizient)</span><span class="sxs-lookup"><span data-stu-id="2da62-156">W3C Efficient XML Interchange</span></span>](https://tools.ietf.org/id/draft-varga-netconf-exi-capability-00.html) |
| `gzip`                          | <span data-ttu-id="2da62-157">Ja (Standard)</span><span class="sxs-lookup"><span data-stu-id="2da62-157">Yes (default)</span></span>        | [<span data-ttu-id="2da62-158">Gzip-Dateiformat</span><span class="sxs-lookup"><span data-stu-id="2da62-158">Gzip file format</span></span>](https://tools.ietf.org/html/rfc1952) |
| `identity`                      | <span data-ttu-id="2da62-159">Ja</span><span class="sxs-lookup"><span data-stu-id="2da62-159">Yes</span></span>                  | <span data-ttu-id="2da62-160">Bezeichner "keine Codierung": die Antwort darf nicht codiert werden.</span><span class="sxs-lookup"><span data-stu-id="2da62-160">"No encoding" identifier: The response must not be encoded.</span></span> |
| `pack200-gzip`                  | <span data-ttu-id="2da62-161">Nein</span><span class="sxs-lookup"><span data-stu-id="2da62-161">No</span></span>                   | [<span data-ttu-id="2da62-162">Netzwerk Übertragungs Format für Java-Archive</span><span class="sxs-lookup"><span data-stu-id="2da62-162">Network Transfer Format for Java Archives</span></span>](https://jcp.org/aboutJava/communityprocess/review/jsr200/index.html) |
| `*`                             | <span data-ttu-id="2da62-163">Ja</span><span class="sxs-lookup"><span data-stu-id="2da62-163">Yes</span></span>                  | <span data-ttu-id="2da62-164">Alle verfügbaren Inhalts Codierungen, die nicht explizit angefordert werden</span><span class="sxs-lookup"><span data-stu-id="2da62-164">Any available content encoding not explicitly requested</span></span> |

::: moniker-end

<span data-ttu-id="2da62-165">Weitere Informationen finden Sie in der [offiziellen Programmier Liste der IANA-Inhalte](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span><span class="sxs-lookup"><span data-stu-id="2da62-165">For more information, see the [IANA Official Content Coding List](https://www.iana.org/assignments/http-parameters/http-parameters.xml#http-content-coding-registry).</span></span>

<span data-ttu-id="2da62-166">Die Middleware ermöglicht das Hinzufügen zusätzlicher Komprimierungs Anbieter für benutzerdefinierte `Accept-Encoding` Header Werte.</span><span class="sxs-lookup"><span data-stu-id="2da62-166">The middleware allows you to add additional compression providers for custom `Accept-Encoding` header values.</span></span> <span data-ttu-id="2da62-167">Weitere Informationen finden Sie unten unter [benutzerdefinierte Anbieter](#custom-providers) .</span><span class="sxs-lookup"><span data-stu-id="2da62-167">For more information, see [Custom Providers](#custom-providers) below.</span></span>

<span data-ttu-id="2da62-168">Die Middleware ist in der Lage, auf die Gewichtung des Qualitäts Werts (qvalue, `q`) zu reagieren, wenn Sie vom Client zum Priorisieren von Komprimierungs Schemas gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="2da62-168">The middleware is capable of reacting to quality value (qvalue, `q`) weighting when sent by the client to prioritize compression schemes.</span></span> <span data-ttu-id="2da62-169">Weitere Informationen finden Sie unter [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span><span class="sxs-lookup"><span data-stu-id="2da62-169">For more information, see [RFC 7231: Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4).</span></span>

<span data-ttu-id="2da62-170">Komprimierungs Algorithmen unterliegen einem Kompromiss zwischen der Komprimierungs Geschwindigkeit und der Effektivität der Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="2da62-170">Compression algorithms are subject to a tradeoff between compression speed and the effectiveness of the compression.</span></span> <span data-ttu-id="2da62-171">*Effektivität* in diesem Kontext bezieht sich auf die Größe der Ausgabe nach der Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="2da62-171">*Effectiveness* in this context refers to the size of the output after compression.</span></span> <span data-ttu-id="2da62-172">Die kleinste Größe wird durch die *optimale* Komprimierung erreicht.</span><span class="sxs-lookup"><span data-stu-id="2da62-172">The smallest size is achieved by the most *optimal* compression.</span></span>

<span data-ttu-id="2da62-173">Die Header, die beim anfordern, senden, Zwischenspeichern und empfangen von komprimiertem Inhalt beteiligt sind, werden in der folgenden Tabelle beschrieben.</span><span class="sxs-lookup"><span data-stu-id="2da62-173">The headers involved in requesting, sending, caching, and receiving compressed content are described in the table below.</span></span>

| <span data-ttu-id="2da62-174">Header</span><span class="sxs-lookup"><span data-stu-id="2da62-174">Header</span></span>             | <span data-ttu-id="2da62-175">Role</span><span class="sxs-lookup"><span data-stu-id="2da62-175">Role</span></span> |
| ------------------ | ---- |
| `Accept-Encoding`  | <span data-ttu-id="2da62-176">Wird vom Client an den Server gesendet, um die Inhalts Codierungs Schemas anzugeben, die für den Client zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="2da62-176">Sent from the client to the server to indicate the content encoding schemes acceptable to the client.</span></span> |
| `Content-Encoding` | <span data-ttu-id="2da62-177">Wird vom Server an den Client gesendet, um die Codierung des Inhalts in der Nutzlast anzugeben.</span><span class="sxs-lookup"><span data-stu-id="2da62-177">Sent from the server to the client to indicate the encoding of the content in the payload.</span></span> |
| `Content-Length`   | <span data-ttu-id="2da62-178">Wenn die Komprimierung erfolgt, wird der `Content-Length`-Header entfernt, da sich der Text Inhalt ändert, wenn die Antwort komprimiert wird.</span><span class="sxs-lookup"><span data-stu-id="2da62-178">When compression occurs, the `Content-Length` header is removed, since the body content changes when the response is compressed.</span></span> |
| `Content-MD5`      | <span data-ttu-id="2da62-179">Wenn die Komprimierung erfolgt, wird der `Content-MD5`-Header entfernt, da sich der Text Inhalt geändert hat und der Hash nicht mehr gültig ist.</span><span class="sxs-lookup"><span data-stu-id="2da62-179">When compression occurs, the `Content-MD5` header is removed, since the body content has changed and the hash is no longer valid.</span></span> |
| `Content-Type`     | <span data-ttu-id="2da62-180">Gibt den MIME-Typ des Inhalts an.</span><span class="sxs-lookup"><span data-stu-id="2da62-180">Specifies the MIME type of the content.</span></span> <span data-ttu-id="2da62-181">Jede Antwort sollte die `Content-Type`angeben.</span><span class="sxs-lookup"><span data-stu-id="2da62-181">Every response should specify its `Content-Type`.</span></span> <span data-ttu-id="2da62-182">Die Middleware überprüft diesen Wert, um zu bestimmen, ob die Antwort komprimiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="2da62-182">The middleware checks this value to determine if the response should be compressed.</span></span> <span data-ttu-id="2da62-183">Die Middleware gibt eine Reihe von [Standard-MIME-Typen](#mime-types) an, die codiert werden können, aber Sie können MIME-Typen ersetzen oder hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="2da62-183">The middleware specifies a set of [default MIME types](#mime-types) that it can encode, but you can replace or add MIME types.</span></span> |
| `Vary`             | <span data-ttu-id="2da62-184">Beim Senden durch den Server mit dem Wert `Accept-Encoding` an Clients und Proxys zeigt der `Vary`-Header dem Client oder dem Proxy an, dass er Antworten auf der Grundlage des Werts des `Accept-Encoding`-Headers der Anforderung Zwischenspeichern (variieren) soll.</span><span class="sxs-lookup"><span data-stu-id="2da62-184">When sent by the server with a value of `Accept-Encoding` to clients and proxies, the `Vary` header indicates to the client or proxy that it should cache (vary) responses based on the value of the `Accept-Encoding` header of the request.</span></span> <span data-ttu-id="2da62-185">Das Ergebnis der Rückgabe von Inhalt mit dem `Vary: Accept-Encoding`-Header besteht darin, dass sowohl komprimierte als auch nicht komprimierte Antworten separat zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="2da62-185">The result of returning content with the `Vary: Accept-Encoding` header is that both compressed and uncompressed responses are cached separately.</span></span> |

<span data-ttu-id="2da62-186">Erkunden Sie die Features der Middleware für die Antwort Komprimierung mit der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span><span class="sxs-lookup"><span data-stu-id="2da62-186">Explore the features of the Response Compression Middleware with the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/response-compression/samples).</span></span> <span data-ttu-id="2da62-187">Das Beispiel veranschaulicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2da62-187">The sample illustrates:</span></span>

* <span data-ttu-id="2da62-188">Die Komprimierung von App-Antworten mithilfe von gzip und benutzerdefinierten Komprimierungs Anbietern.</span><span class="sxs-lookup"><span data-stu-id="2da62-188">The compression of app responses using Gzip and custom compression providers.</span></span>
* <span data-ttu-id="2da62-189">Vorgehensweise beim Hinzufügen eines MIME-Typs zur Standardliste von MIME-Typen für die Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="2da62-189">How to add a MIME type to the default list of MIME types for compression.</span></span>

## <a name="package"></a><span data-ttu-id="2da62-190">Paket</span><span class="sxs-lookup"><span data-stu-id="2da62-190">Package</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2da62-191">Die Middleware für die Antwort Komprimierung wird vom Paket [Microsoft. aspnetcore. Response Compression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) bereitgestellt, das implizit in ASP.net Core-Apps enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="2da62-191">Response Compression Middleware is provided by the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package, which is implicitly included in ASP.NET Core apps.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2da62-192">Fügen Sie einen Verweis auf das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)hinzu, das das Paket [Microsoft. aspnetcore. Response Compression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) enthält, um die Middleware in ein Projekt einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="2da62-192">To include the middleware in a project, add a reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), which includes the [Microsoft.AspNetCore.ResponseCompression](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCompression/) package.</span></span>

::: moniker-end

## <a name="configuration"></a><span data-ttu-id="2da62-193">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="2da62-193">Configuration</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2da62-194">Der folgende Code zeigt, wie Sie die Middleware für die Antwort Komprimierung für Standard-MIME-Typen und Komprimierungs Anbieter ([brotli](#brotli-compression-provider) und [gzip](#gzip-compression-provider)) aktivieren:</span><span class="sxs-lookup"><span data-stu-id="2da62-194">The following code shows how to enable the Response Compression Middleware for default MIME types and compression providers ([Brotli](#brotli-compression-provider) and [Gzip](#gzip-compression-provider)):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2da62-195">Der folgende Code zeigt, wie Sie die Middleware für die Antwort Komprimierung für Standard-MIME-Typen und den [GZip-Komprimierungs Anbieter](#gzip-compression-provider)aktivieren:</span><span class="sxs-lookup"><span data-stu-id="2da62-195">The following code shows how to enable the Response Compression Middleware for default MIME types and the [Gzip Compression Provider](#gzip-compression-provider):</span></span>

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

<span data-ttu-id="2da62-196">Hinweise:</span><span class="sxs-lookup"><span data-stu-id="2da62-196">Notes:</span></span>

* <span data-ttu-id="2da62-197">`app.UseResponseCompression` muss vor allen Middleware aufgerufen werden, die Antworten komprimiert.</span><span class="sxs-lookup"><span data-stu-id="2da62-197">`app.UseResponseCompression` must be called before any middleware that compresses responses.</span></span> <span data-ttu-id="2da62-198">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index#middleware-order>.</span><span class="sxs-lookup"><span data-stu-id="2da62-198">For more information, see <xref:fundamentals/middleware/index#middleware-order>.</span></span>
* <span data-ttu-id="2da62-199">Verwenden Sie ein Tool wie z. b. " [fddler](https://www.telerik.com/fiddler)", " [Firebug](https://getfirebug.com/)" oder [Postman](https://www.getpostman.com/) , um den `Accept-Encoding` Anforderungs Header festzulegen und die Antwortheader, die Größe und den Text zu überprüfen</span><span class="sxs-lookup"><span data-stu-id="2da62-199">Use a tool such as [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/) to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span>

<span data-ttu-id="2da62-200">Senden Sie eine Anforderung an die Beispiel-App ohne den `Accept-Encoding`-Header, und beobachten Sie, dass die Antwort nicht komprimiert ist.</span><span class="sxs-lookup"><span data-stu-id="2da62-200">Submit a request to the sample app without the `Accept-Encoding` header and observe that the response is uncompressed.</span></span> <span data-ttu-id="2da62-201">Die Header "`Content-Encoding`" und "`Vary`" sind in der Antwort nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="2da62-201">The `Content-Encoding` and `Vary` headers aren't present on the response.</span></span>

![Das Anforderungsfenster zeigt das Ergebnis einer Anforderung ohne den Accept-Encoding-Header an.](response-compression/_static/request-uncompressed.png)

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2da62-204">Senden Sie eine Anforderung an die Beispiel-App mit dem `Accept-Encoding: br`-Header (brotli-Komprimierung), und beobachten Sie, dass die Antwort komprimiert ist.</span><span class="sxs-lookup"><span data-stu-id="2da62-204">Submit a request to the sample app with the `Accept-Encoding: br` header (Brotli compression) and observe that the response is compressed.</span></span> <span data-ttu-id="2da62-205">Die Header "`Content-Encoding`" und "`Vary`" sind in der Antwort vorhanden.</span><span class="sxs-lookup"><span data-stu-id="2da62-205">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Das Fenster "dateiddler" zeigt das Ergebnis einer Anforderung mit dem Accept-Encoding-Header und dem Wert "br" an.](response-compression/_static/request-compressed-br.png)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2da62-209">Senden Sie eine Anforderung an die Beispiel-App mit dem `Accept-Encoding: gzip`-Header, und beobachten Sie, dass die Antwort komprimiert ist.</span><span class="sxs-lookup"><span data-stu-id="2da62-209">Submit a request to the sample app with the `Accept-Encoding: gzip` header and observe that the response is compressed.</span></span> <span data-ttu-id="2da62-210">Die Header "`Content-Encoding`" und "`Vary`" sind in der Antwort vorhanden.</span><span class="sxs-lookup"><span data-stu-id="2da62-210">The `Content-Encoding` and `Vary` headers are present on the response.</span></span>

![Das Fenster "dateiddler" zeigt das Ergebnis einer Anforderung mit dem Accept-Encoding-Header und dem Wert "gzip" an.](response-compression/_static/request-compressed.png)

::: moniker-end

## <a name="providers"></a><span data-ttu-id="2da62-214">Anbieter</span><span class="sxs-lookup"><span data-stu-id="2da62-214">Providers</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="brotli-compression-provider"></a><span data-ttu-id="2da62-215">Brotli-Komprimierungs Anbieter</span><span class="sxs-lookup"><span data-stu-id="2da62-215">Brotli Compression Provider</span></span>

<span data-ttu-id="2da62-216">Verwenden Sie die <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider>, um Antworten mit dem von [brotli komprimierten Datenformat](https://tools.ietf.org/html/rfc7932)zu komprimieren.</span><span class="sxs-lookup"><span data-stu-id="2da62-216">Use the <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProvider> to compress responses with the [Brotli compressed data format](https://tools.ietf.org/html/rfc7932).</span></span>

<span data-ttu-id="2da62-217">Wenn der <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>keine Komprimierungs Anbieter explizit hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="2da62-217">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

* <span data-ttu-id="2da62-218">Der brotli-Komprimierungs Anbieter wird dem Array von Komprimierungs Anbietern zusammen mit dem [GZip-Komprimierungs](#gzip-compression-provider)Anbieter standardmäßig hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2da62-218">The Brotli Compression Provider is added by default to the array of compression providers along with the [Gzip compression provider](#gzip-compression-provider).</span></span>
* <span data-ttu-id="2da62-219">Bei der Komprimierung wird standardmäßig die brotli-Komprimierung verwendet, wenn das von der Client komprimierte brotli-Datenformat</span><span class="sxs-lookup"><span data-stu-id="2da62-219">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="2da62-220">Wenn brotli nicht vom Client unterstützt wird, wird standardmäßig gzip verwendet, wenn der Client die gzip-Komprimierung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2da62-220">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="2da62-221">Der broesli-Komprimierungs Anbieter muss hinzugefügt werden, wenn Komprimierungs Anbieter explizit hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="2da62-221">The Brotoli Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2da62-222">Legen Sie die Komprimierungs Ebene mit <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>fest.</span><span class="sxs-lookup"><span data-stu-id="2da62-222">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.BrotliCompressionProviderOptions>.</span></span> <span data-ttu-id="2da62-223">Der brotli-Komprimierungs Anbieter verwendet standardmäßig den schnellsten Komprimierungs Grad ([CompressionLevel. schnellste](xref:System.IO.Compression.CompressionLevel)), der möglicherweise nicht die effizienteste Komprimierung erzeugt.</span><span class="sxs-lookup"><span data-stu-id="2da62-223">The Brotli Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="2da62-224">Wenn die effizienteste Komprimierung erwünscht ist, konfigurieren Sie die Middleware für die optimale Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="2da62-224">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="2da62-225">Compression Level</span><span class="sxs-lookup"><span data-stu-id="2da62-225">Compression Level</span></span> | <span data-ttu-id="2da62-226">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="2da62-226">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="2da62-227">CompressionLevel. schnellste</span><span class="sxs-lookup"><span data-stu-id="2da62-227">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="2da62-228">Die Komprimierung sollte so schnell wie möglich durchgeführt werden, auch wenn die resultierende Ausgabe nicht optimal komprimiert ist.</span><span class="sxs-lookup"><span data-stu-id="2da62-228">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="2da62-229">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="2da62-229">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="2da62-230">Es sollte keine Komprimierung durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="2da62-230">No compression should be performed.</span></span> |
| [<span data-ttu-id="2da62-231">CompressionLevel. optimal</span><span class="sxs-lookup"><span data-stu-id="2da62-231">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="2da62-232">Antworten sollten optimal komprimiert werden, auch wenn die Komprimierung mehr Zeit in Anspruch nimmt.</span><span class="sxs-lookup"><span data-stu-id="2da62-232">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="gzip-compression-provider"></a><span data-ttu-id="2da62-233">GZip-Komprimierungs Anbieter</span><span class="sxs-lookup"><span data-stu-id="2da62-233">Gzip Compression Provider</span></span>

<span data-ttu-id="2da62-234">Verwenden Sie die <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider>, um Antworten mit dem [gzip-Dateiformat](https://tools.ietf.org/html/rfc1952)zu komprimieren.</span><span class="sxs-lookup"><span data-stu-id="2da62-234">Use the <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProvider> to compress responses with the [Gzip file format](https://tools.ietf.org/html/rfc1952).</span></span>

<span data-ttu-id="2da62-235">Wenn der <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>keine Komprimierungs Anbieter explizit hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="2da62-235">If no compression providers are explicitly added to the <xref:Microsoft.AspNetCore.ResponseCompression.CompressionProviderCollection>:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="2da62-236">Der GZip-Komprimierungs Anbieter wird dem Array von Komprimierungs Anbietern standardmäßig zusammen mit dem [brotli-Komprimierungs Anbieter](#brotli-compression-provider)hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2da62-236">The Gzip Compression Provider is added by default to the array of compression providers along with the [Brotli Compression Provider](#brotli-compression-provider).</span></span>
* <span data-ttu-id="2da62-237">Bei der Komprimierung wird standardmäßig die brotli-Komprimierung verwendet, wenn das von der Client komprimierte brotli-Datenformat</span><span class="sxs-lookup"><span data-stu-id="2da62-237">Compression defaults to Brotli compression when the Brotli compressed data format is supported by the client.</span></span> <span data-ttu-id="2da62-238">Wenn brotli nicht vom Client unterstützt wird, wird standardmäßig gzip verwendet, wenn der Client die gzip-Komprimierung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2da62-238">If Brotli isn't supported by the client, compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="2da62-239">Der GZip-Komprimierungs Anbieter wird standardmäßig dem Array von Komprimierungs Anbietern hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2da62-239">The Gzip Compression Provider is added by default to the array of compression providers.</span></span>
* <span data-ttu-id="2da62-240">Standardmäßig wird gzip verwendet, wenn der Client die gzip-Komprimierung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2da62-240">Compression defaults to Gzip when the client supports Gzip compression.</span></span>

::: moniker-end

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddResponseCompression();
}
```

<span data-ttu-id="2da62-241">Der GZip-Komprimierungs Anbieter muss hinzugefügt werden, wenn Komprimierungs Anbieter explizit hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="2da62-241">The Gzip Compression Provider must be added when any compression providers are explicitly added:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6)]

::: moniker-end

<span data-ttu-id="2da62-242">Legen Sie die Komprimierungs Ebene mit <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>fest.</span><span class="sxs-lookup"><span data-stu-id="2da62-242">Set the compression level with <xref:Microsoft.AspNetCore.ResponseCompression.GzipCompressionProviderOptions>.</span></span> <span data-ttu-id="2da62-243">Der GZip-Komprimierungs Anbieter verwendet standardmäßig den schnellsten Komprimierungs Grad ([CompressionLevel. schnellste](xref:System.IO.Compression.CompressionLevel)), der möglicherweise nicht die effizienteste Komprimierung erzeugt.</span><span class="sxs-lookup"><span data-stu-id="2da62-243">The Gzip Compression Provider defaults to the fastest compression level ([CompressionLevel.Fastest](xref:System.IO.Compression.CompressionLevel)), which might not produce the most efficient compression.</span></span> <span data-ttu-id="2da62-244">Wenn die effizienteste Komprimierung erwünscht ist, konfigurieren Sie die Middleware für die optimale Komprimierung.</span><span class="sxs-lookup"><span data-stu-id="2da62-244">If the most efficient compression is desired, configure the middleware for optimal compression.</span></span>

| <span data-ttu-id="2da62-245">Compression Level</span><span class="sxs-lookup"><span data-stu-id="2da62-245">Compression Level</span></span> | <span data-ttu-id="2da62-246">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="2da62-246">Description</span></span> |
| ----------------- | ----------- |
| [<span data-ttu-id="2da62-247">CompressionLevel. schnellste</span><span class="sxs-lookup"><span data-stu-id="2da62-247">CompressionLevel.Fastest</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="2da62-248">Die Komprimierung sollte so schnell wie möglich durchgeführt werden, auch wenn die resultierende Ausgabe nicht optimal komprimiert ist.</span><span class="sxs-lookup"><span data-stu-id="2da62-248">Compression should complete as quickly as possible, even if the resulting output isn't optimally compressed.</span></span> |
| [<span data-ttu-id="2da62-249">CompressionLevel. NoCompression</span><span class="sxs-lookup"><span data-stu-id="2da62-249">CompressionLevel.NoCompression</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="2da62-250">Es sollte keine Komprimierung durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="2da62-250">No compression should be performed.</span></span> |
| [<span data-ttu-id="2da62-251">CompressionLevel. optimal</span><span class="sxs-lookup"><span data-stu-id="2da62-251">CompressionLevel.Optimal</span></span>](xref:System.IO.Compression.CompressionLevel) | <span data-ttu-id="2da62-252">Antworten sollten optimal komprimiert werden, auch wenn die Komprimierung mehr Zeit in Anspruch nimmt.</span><span class="sxs-lookup"><span data-stu-id="2da62-252">Responses should be optimally compressed, even if the compression takes more time to complete.</span></span> |

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

### <a name="custom-providers"></a><span data-ttu-id="2da62-253">Benutzerdefinierte Anbieter</span><span class="sxs-lookup"><span data-stu-id="2da62-253">Custom providers</span></span>

<span data-ttu-id="2da62-254">Erstellen Sie benutzerdefinierte Komprimierungs Implementierungen mit <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span><span class="sxs-lookup"><span data-stu-id="2da62-254">Create custom compression implementations with <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider>.</span></span> <span data-ttu-id="2da62-255">Der <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> der die von diesem `ICompressionProvider` erzeugte Inhalts Codierung darstellt.</span><span class="sxs-lookup"><span data-stu-id="2da62-255">The <xref:Microsoft.AspNetCore.ResponseCompression.ICompressionProvider.EncodingName*> represents the content encoding that this `ICompressionProvider` produces.</span></span> <span data-ttu-id="2da62-256">Die Middleware verwendet diese Informationen, um den Anbieter auf der Grundlage der im `Accept-Encoding`-Header der Anforderung angegebenen Liste auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="2da62-256">The middleware uses this information to choose the provider based on the list specified in the `Accept-Encoding` header of the request.</span></span>

<span data-ttu-id="2da62-257">Mithilfe der Beispiel-App übermittelt der Client eine Anforderung mit dem `Accept-Encoding: mycustomcompression`-Header.</span><span class="sxs-lookup"><span data-stu-id="2da62-257">Using the sample app, the client submits a request with the `Accept-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="2da62-258">Die Middleware verwendet die Implementierung der benutzerdefinierten Komprimierung und gibt die Antwort mit einem `Content-Encoding: mycustomcompression`-Header zurück.</span><span class="sxs-lookup"><span data-stu-id="2da62-258">The middleware uses the custom compression implementation and returns the response with a `Content-Encoding: mycustomcompression` header.</span></span> <span data-ttu-id="2da62-259">Der Client muss in der Lage sein, die benutzerdefinierte Codierung zu decodieren, damit eine benutzerdefinierte Komprimierungs Implementierung funktioniert.</span><span class="sxs-lookup"><span data-stu-id="2da62-259">The client must be able to decompress the custom encoding in order for a custom compression implementation to work.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/3.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=7)]

[!code-csharp[](response-compression/samples/2.x/SampleApp/CustomCompressionProvider.cs?name=snippet1)]

::: moniker-end

<span data-ttu-id="2da62-260">Senden Sie eine Anforderung an die Beispiel-App mit dem `Accept-Encoding: mycustomcompression`-Header, und beobachten Sie die Antwortheader.</span><span class="sxs-lookup"><span data-stu-id="2da62-260">Submit a request to the sample app with the `Accept-Encoding: mycustomcompression` header and observe the response headers.</span></span> <span data-ttu-id="2da62-261">Die Header "`Vary`" und "`Content-Encoding`" sind in der Antwort vorhanden.</span><span class="sxs-lookup"><span data-stu-id="2da62-261">The `Vary` and `Content-Encoding` headers are present on the response.</span></span> <span data-ttu-id="2da62-262">Der Antworttext (nicht angezeigt) wird nicht durch das Beispiel komprimiert.</span><span class="sxs-lookup"><span data-stu-id="2da62-262">The response body (not shown) isn't compressed by the sample.</span></span> <span data-ttu-id="2da62-263">In der `CustomCompressionProvider`-Klasse des Beispiels ist keine Komprimierungs Implementierung vorhanden.</span><span class="sxs-lookup"><span data-stu-id="2da62-263">There isn't a compression implementation in the `CustomCompressionProvider` class of the sample.</span></span> <span data-ttu-id="2da62-264">Das Beispiel zeigt jedoch, wo Sie einen solchen Komprimierungs Algorithmus implementieren würden.</span><span class="sxs-lookup"><span data-stu-id="2da62-264">However, the sample shows where you would implement such a compression algorithm.</span></span>

![Das Fenster "dateiddler" zeigt das Ergebnis einer Anforderung mit dem Accept-Encoding-Header und einem Wert von "mycustomcompression" an.](response-compression/_static/request-custom-compression.png)

## <a name="mime-types"></a><span data-ttu-id="2da62-267">MIME-Typen</span><span class="sxs-lookup"><span data-stu-id="2da62-267">MIME types</span></span>

<span data-ttu-id="2da62-268">Die Middleware gibt einen Standardsatz von MIME-Typen für die Komprimierung an:</span><span class="sxs-lookup"><span data-stu-id="2da62-268">The middleware specifies a default set of MIME types for compression:</span></span>

* `application/javascript`
* `application/json`
* `application/xml`
* `text/css`
* `text/html`
* `text/json`
* `text/plain`
* `text/xml`

<span data-ttu-id="2da62-269">Ersetzen Sie MIME-Typen, oder fügen Sie Sie durch die Optionen für die Antwort Komprimierungs Middleware</span><span class="sxs-lookup"><span data-stu-id="2da62-269">Replace or append MIME types with the Response Compression Middleware options.</span></span> <span data-ttu-id="2da62-270">Beachten Sie, dass Platzhalter-MIME-Typen wie `text/*` nicht unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="2da62-270">Note that wildcard MIME types, such as `text/*` aren't supported.</span></span> <span data-ttu-id="2da62-271">Die Beispiel-App Fügt einen MIME-Typ für `image/svg+xml` hinzu und komprimiert das ASP.net Core Banner Bild (*Banner. SVG*).</span><span class="sxs-lookup"><span data-stu-id="2da62-271">The sample app adds a MIME type for `image/svg+xml` and compresses and serves the ASP.NET Core banner image (*banner.svg*).</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response-compression/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response-compression/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=8-10)]

::: moniker-end

## <a name="compression-with-secure-protocol"></a><span data-ttu-id="2da62-272">Komprimierung mit sicherem Protokoll</span><span class="sxs-lookup"><span data-stu-id="2da62-272">Compression with secure protocol</span></span>

<span data-ttu-id="2da62-273">Komprimierte Antworten über sichere Verbindungen können mit der Option `EnableForHttps` gesteuert werden, die standardmäßig deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="2da62-273">Compressed responses over secure connections can be controlled with the `EnableForHttps` option, which is disabled by default.</span></span> <span data-ttu-id="2da62-274">[Die Verwendung](https://wikipedia.org/wiki/CRIME_(security_exploit)) der Komprimierung mit dynamisch generierten Seiten kann zu Sicherheitsproblemen führen, wie z. b. den Angriffen gegen Angriffe und [Verletzungen](https://wikipedia.org/wiki/BREACH_(security_exploit)) .</span><span class="sxs-lookup"><span data-stu-id="2da62-274">Using compression with dynamically generated pages can lead to security problems such as the [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) and [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) attacks.</span></span>

## <a name="adding-the-vary-header"></a><span data-ttu-id="2da62-275">Hinzufügen des Vary-Headers</span><span class="sxs-lookup"><span data-stu-id="2da62-275">Adding the Vary header</span></span>

<span data-ttu-id="2da62-276">Beim Komprimieren von Antworten, die auf dem `Accept-Encoding`-Header basieren, gibt es potenziell mehrere komprimierte Versionen der Antwort und eine nicht komprimierte Version.</span><span class="sxs-lookup"><span data-stu-id="2da62-276">When compressing responses based on the `Accept-Encoding` header, there are potentially multiple compressed versions of the response and an uncompressed version.</span></span> <span data-ttu-id="2da62-277">Um Client-und Proxy Caches anzuweisen, dass mehrere Versionen vorhanden sind und gespeichert werden sollten, wird der `Vary`-Header mit einem `Accept-Encoding` Wert hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2da62-277">In order to instruct client and proxy caches that multiple versions exist and should be stored, the `Vary` header is added with an `Accept-Encoding` value.</span></span> <span data-ttu-id="2da62-278">In ASP.net Core 2,0 oder höher fügt die Middleware den `Vary` Header automatisch hinzu, wenn die Antwort komprimiert wird.</span><span class="sxs-lookup"><span data-stu-id="2da62-278">In ASP.NET Core 2.0 or later, the middleware adds the `Vary` header automatically when the response is compressed.</span></span>

## <a name="middleware-issue-when-behind-an-nginx-reverse-proxy"></a><span data-ttu-id="2da62-279">Middleware-Problem, wenn hinter einem nginx-Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="2da62-279">Middleware issue when behind an Nginx reverse proxy</span></span>

<span data-ttu-id="2da62-280">Wenn eine Anforderung von nginx bereitgestellt wird, wird der `Accept-Encoding`-Header entfernt.</span><span class="sxs-lookup"><span data-stu-id="2da62-280">When a request is proxied by Nginx, the `Accept-Encoding` header is removed.</span></span> <span data-ttu-id="2da62-281">Durch das Entfernen der `Accept-Encoding` Kopfzeile wird verhindert, dass die Middleware die Antwort komprimiert.</span><span class="sxs-lookup"><span data-stu-id="2da62-281">Removal of the `Accept-Encoding` header prevents the middleware from compressing the response.</span></span> <span data-ttu-id="2da62-282">Weitere Informationen finden Sie unter [nginx: Komprimierung und Dekomprimierung](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span><span class="sxs-lookup"><span data-stu-id="2da62-282">For more information, see [NGINX: Compression and Decompression](https://www.nginx.com/resources/admin-guide/compression-and-decompression/).</span></span> <span data-ttu-id="2da62-283">Dieses Problem wird nachverfolgt [, indem Sie die Pass-Through-Komprimierung für nginx (ASPNET/basicmiddleware #123) ermitteln](https://github.com/aspnet/BasicMiddleware/issues/123).</span><span class="sxs-lookup"><span data-stu-id="2da62-283">This issue is tracked by [Figure out pass-through compression for Nginx (aspnet/BasicMiddleware #123)](https://github.com/aspnet/BasicMiddleware/issues/123).</span></span>

## <a name="working-with-iis-dynamic-compression"></a><span data-ttu-id="2da62-284">Arbeiten mit dynamischer IIS-Komprimierung</span><span class="sxs-lookup"><span data-stu-id="2da62-284">Working with IIS dynamic compression</span></span>

<span data-ttu-id="2da62-285">Wenn Sie ein aktives Modul für die dynamische IIS-Komprimierung auf Serverebene konfiguriert haben, die Sie für eine App deaktivieren möchten, deaktivieren Sie das Modul mit einer Addition zur Datei " *Web. config* ".</span><span class="sxs-lookup"><span data-stu-id="2da62-285">If you have an active IIS Dynamic Compression Module configured at the server level that you would like to disable for an app, disable the module with an addition to the *web.config* file.</span></span> <span data-ttu-id="2da62-286">Weitere Informationen finden Sie unter [Disabling IIS modules (Deaktivieren von IIS-Modulen)](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="2da62-286">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="2da62-287">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="2da62-287">Troubleshooting</span></span>

<span data-ttu-id="2da62-288">Verwenden Sie ein Tool wie z. b. " [fddler](https://www.telerik.com/fiddler)", " [Firebug](https://getfirebug.com/)" oder [Postman](https://www.getpostman.com/), mit dem Sie den `Accept-Encoding` Anforderungs Header festlegen und die Antwortheader, die Größe und den Text überprüfen können.</span><span class="sxs-lookup"><span data-stu-id="2da62-288">Use a tool like [Fiddler](https://www.telerik.com/fiddler), [Firebug](https://getfirebug.com/), or [Postman](https://www.getpostman.com/), which allow you to set the `Accept-Encoding` request header and study the response headers, size, and body.</span></span> <span data-ttu-id="2da62-289">Standardmäßig komprimiert die Middleware für die Antwort Komprimierung Antworten, die die folgenden Bedingungen erfüllen:</span><span class="sxs-lookup"><span data-stu-id="2da62-289">By default, Response Compression Middleware compresses responses that meet the following conditions:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="2da62-290">Der `Accept-Encoding`-Header ist mit einem Wert `br`, `gzip`, `*`oder benutzerdefinierten Codierung vorhanden, der mit einem benutzerdefinierten Komprimierungs Anbieter übereinstimmt, den Sie eingerichtet haben.</span><span class="sxs-lookup"><span data-stu-id="2da62-290">The `Accept-Encoding` header is present with a value of `br`, `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="2da62-291">Der Wert darf nicht `identity` sein oder einen Qualitäts Wert (qvalue, `q`) der Einstellung 0 (null) aufweisen.</span><span class="sxs-lookup"><span data-stu-id="2da62-291">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="2da62-292">Der MIME-Typ (`Content-Type`) muss festgelegt werden und einem MIME-Typ entsprechen, der für die <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="2da62-292">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="2da62-293">Die Anforderung darf nicht den `Content-Range`-Header enthalten.</span><span class="sxs-lookup"><span data-stu-id="2da62-293">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="2da62-294">Die Anforderung muss das unsichere Protokoll (http) verwenden, es sei denn, das sichere Protokoll (HTTPS) ist in den Optionen der Middleware für die Antwort Komprimierung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="2da62-294">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="2da62-295">*Beachten Sie die [oben beschriebene](#compression-with-secure-protocol) Gefahr beim Aktivieren der Komprimierung von sicherem Inhalt.*</span><span class="sxs-lookup"><span data-stu-id="2da62-295">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="2da62-296">Der `Accept-Encoding`-Header ist mit einem Wert `gzip`, `*`oder benutzerdefinierter Codierung vorhanden, der mit einem benutzerdefinierten Komprimierungs Anbieter übereinstimmt, den Sie eingerichtet haben.</span><span class="sxs-lookup"><span data-stu-id="2da62-296">The `Accept-Encoding` header is present with a value of `gzip`, `*`, or custom encoding that matches a custom compression provider that you've established.</span></span> <span data-ttu-id="2da62-297">Der Wert darf nicht `identity` sein oder einen Qualitäts Wert (qvalue, `q`) der Einstellung 0 (null) aufweisen.</span><span class="sxs-lookup"><span data-stu-id="2da62-297">The value must not be `identity` or have a quality value (qvalue, `q`) setting of 0 (zero).</span></span>
* <span data-ttu-id="2da62-298">Der MIME-Typ (`Content-Type`) muss festgelegt werden und einem MIME-Typ entsprechen, der für die <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="2da62-298">The MIME type (`Content-Type`) must be set and must match a MIME type configured on the <xref:Microsoft.AspNetCore.ResponseCompression.ResponseCompressionOptions>.</span></span>
* <span data-ttu-id="2da62-299">Die Anforderung darf nicht den `Content-Range`-Header enthalten.</span><span class="sxs-lookup"><span data-stu-id="2da62-299">The request must not include the `Content-Range` header.</span></span>
* <span data-ttu-id="2da62-300">Die Anforderung muss das unsichere Protokoll (http) verwenden, es sei denn, das sichere Protokoll (HTTPS) ist in den Optionen der Middleware für die Antwort Komprimierung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="2da62-300">The request must use insecure protocol (http), unless secure protocol (https) is configured in the Response Compression Middleware options.</span></span> <span data-ttu-id="2da62-301">*Beachten Sie die [oben beschriebene](#compression-with-secure-protocol) Gefahr beim Aktivieren der Komprimierung von sicherem Inhalt.*</span><span class="sxs-lookup"><span data-stu-id="2da62-301">*Note the danger [described above](#compression-with-secure-protocol) when enabling secure content compression.*</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="2da62-302">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2da62-302">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="2da62-303">Mozilla Developer Network: Accept-Encoding</span><span class="sxs-lookup"><span data-stu-id="2da62-303">Mozilla Developer Network: Accept-Encoding</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Accept-Encoding)
* [<span data-ttu-id="2da62-304">RFC 7231, Abschnitt 3.1.2.1: Inhalts Codierungen</span><span class="sxs-lookup"><span data-stu-id="2da62-304">RFC 7231 Section 3.1.2.1: Content Codings</span></span>](https://tools.ietf.org/html/rfc7231#section-3.1.2.1)
* [<span data-ttu-id="2da62-305">RFC 7230 section 4.2.3: gzip-Codierung</span><span class="sxs-lookup"><span data-stu-id="2da62-305">RFC 7230 Section 4.2.3: Gzip Coding</span></span>](https://tools.ietf.org/html/rfc7230#section-4.2.3)
* [<span data-ttu-id="2da62-306">Gzip-Dateiformat Spezifikation, Version 4,3</span><span class="sxs-lookup"><span data-stu-id="2da62-306">GZIP file format specification version 4.3</span></span>](https://www.ietf.org/rfc/rfc1952.txt)
