---
<span data-ttu-id="5a0a9-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-102">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-103">'Identity'</span></span>
- <span data-ttu-id="5a0a9-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-105">'Razor'</span></span>
- <span data-ttu-id="5a0a9-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-106">'SignalR' uid:</span></span> 

---
# <a name="response-caching-in-aspnet-core"></a><span data-ttu-id="5a0a9-107">Zwischenspeichern von Antworten in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="5a0a9-107">Response caching in ASP.NET Core</span></span>

<span data-ttu-id="5a0a9-108">Von [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5a0a9-108">By [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5a0a9-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5a0a9-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5a0a9-110">Beim Zwischenspeichern von Antworten wird die Anzahl von Anforderungen reduziert, die ein Client oder Proxy an einen Webserver sendet.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-110">Response caching reduces the number of requests a client or proxy makes to a web server.</span></span> <span data-ttu-id="5a0a9-111">Durch das Zwischenspeichern von Antworten wird auch der Arbeitsaufwand reduziert, der vom Webserver zum Generieren einer Antwort ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-111">Response caching also reduces the amount of work the web server performs to generate a response.</span></span> <span data-ttu-id="5a0a9-112">Das Zwischenspeichern von Antworten wird durch Header gesteuert, die angeben, wie die Client-, Proxy-und Middleware Antworten zwischenspeichern soll.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-112">Response caching is controlled by headers that specify how you want client, proxy, and middleware to cache responses.</span></span>

<span data-ttu-id="5a0a9-113">Das [responsecache-Attribut](#responsecache-attribute) nimmt an der Einstellung von Cache Headern für Antworten Teil.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-113">The [ResponseCache attribute](#responsecache-attribute) participates in setting response caching headers.</span></span> <span data-ttu-id="5a0a9-114">Clients und zwischen Proxys sollten die Header zum Zwischenspeichern von Antworten unter der [http 1,1-cachingspezifikation](https://tools.ietf.org/html/rfc7234)berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-114">Clients and intermediate proxies should honor the headers for caching responses under the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span>

<span data-ttu-id="5a0a9-115">Verwenden Sie für die serverseitige Zwischenspeicherung, die der HTTP 1,1-Cache Spezifikation folgt, die [Middleware zum Zwischenspeichern von Antworten](xref:performance/caching/middleware).</span><span class="sxs-lookup"><span data-stu-id="5a0a9-115">For server-side caching that follows the HTTP 1.1 Caching specification, use [Response Caching Middleware](xref:performance/caching/middleware).</span></span> <span data-ttu-id="5a0a9-116">Die Middleware kann die <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> Eigenschaften verwenden, um das serverseitige zwischen Speicherungs Verhalten zu beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-116">The middleware can use the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> properties to influence server-side caching behavior.</span></span>

## <a name="http-based-response-caching"></a><span data-ttu-id="5a0a9-117">HTTP-basiertes Zwischenspeichern von Antworten</span><span class="sxs-lookup"><span data-stu-id="5a0a9-117">HTTP-based response caching</span></span>

<span data-ttu-id="5a0a9-118">In der [http 1,1-cachingspezifikation](https://tools.ietf.org/html/rfc7234) wird beschrieben, wie sich die Internet Caches Verhalten.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-118">The [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234) describes how Internet caches should behave.</span></span> <span data-ttu-id="5a0a9-119">Der primäre HTTP-Header, der zum Zwischenspeichern verwendet wird, ist [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), der zum Angeben von Cache *Anweisungen*verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-119">The primary HTTP header used for caching is [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), which is used to specify cache *directives*.</span></span> <span data-ttu-id="5a0a9-120">Die Direktiven steuern das zwischen Speicherungs Verhalten, da Anforderungen von Clients auf Server und Antworten von Servern an Clients zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-120">The directives control caching behavior as requests make their way from clients to servers and as responses make their way from servers back to clients.</span></span> <span data-ttu-id="5a0a9-121">Anforderungen und Antworten werden durch Proxy Server verschoben, und Proxy Server müssen ebenfalls der HTTP 1,1-cachingspezifikation entsprechen.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-121">Requests and responses move through proxy servers, and proxy servers must also conform to the HTTP 1.1 Caching specification.</span></span>

<span data-ttu-id="5a0a9-122">Allgemeine `Cache-Control` Anweisungen sind in der folgenden Tabelle aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-122">Common `Cache-Control` directives are shown in the following table.</span></span>

| <span data-ttu-id="5a0a9-123">Direktive</span><span class="sxs-lookup"><span data-stu-id="5a0a9-123">Directive</span></span>                                                       | <span data-ttu-id="5a0a9-124">Aktion</span><span class="sxs-lookup"><span data-stu-id="5a0a9-124">Action</span></span> |
| ---
<span data-ttu-id="5a0a9-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-125">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-126">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-126">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-127">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-127">'Identity'</span></span>
- <span data-ttu-id="5a0a9-128">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-128">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-129">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-129">'Razor'</span></span>
- <span data-ttu-id="5a0a9-130">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-130">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-131">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-132">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-132">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-133">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-133">'Identity'</span></span>
- <span data-ttu-id="5a0a9-134">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-134">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-135">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-135">'Razor'</span></span>
- <span data-ttu-id="5a0a9-136">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-136">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-137">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-138">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-138">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-139">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-139">'Identity'</span></span>
- <span data-ttu-id="5a0a9-140">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-140">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-141">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-141">'Razor'</span></span>
- <span data-ttu-id="5a0a9-142">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-142">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-143">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-144">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-144">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-145">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-145">'Identity'</span></span>
- <span data-ttu-id="5a0a9-146">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-146">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-147">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-147">'Razor'</span></span>
- <span data-ttu-id="5a0a9-148">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-148">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-149">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-150">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-151">'Identity'</span></span>
- <span data-ttu-id="5a0a9-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-153">'Razor'</span></span>
- <span data-ttu-id="5a0a9-154">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-155">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-156">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-157">'Identity'</span></span>
- <span data-ttu-id="5a0a9-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-159">'Razor'</span></span>
- <span data-ttu-id="5a0a9-160">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-161">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-162">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-163">'Identity'</span></span>
- <span data-ttu-id="5a0a9-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-165">'Razor'</span></span>
- <span data-ttu-id="5a0a9-166">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-166">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-167">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-168">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-169">'Identity'</span></span>
- <span data-ttu-id="5a0a9-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-171">'Razor'</span></span>
- <span data-ttu-id="5a0a9-172">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-173">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-174">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-175">'Identity'</span></span>
- <span data-ttu-id="5a0a9-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-177">'Razor'</span></span>
- <span data-ttu-id="5a0a9-178">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-179">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-180">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-181">'Identity'</span></span>
- <span data-ttu-id="5a0a9-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-183">'Razor'</span></span>
- <span data-ttu-id="5a0a9-184">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-185">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-186">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-187">'Identity'</span></span>
- <span data-ttu-id="5a0a9-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-189">'Razor'</span></span>
- <span data-ttu-id="5a0a9-190">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-191">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-192">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-193">'Identity'</span></span>
- <span data-ttu-id="5a0a9-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-195">'Razor'</span></span>
- <span data-ttu-id="5a0a9-196">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-197">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-198">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-199">'Identity'</span></span>
- <span data-ttu-id="5a0a9-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-201">'Razor'</span></span>
- <span data-ttu-id="5a0a9-202">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-203">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-204">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-205">'Identity'</span></span>
- <span data-ttu-id="5a0a9-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-207">'Razor'</span></span>
- <span data-ttu-id="5a0a9-208">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-209">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-210">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-211">'Identity'</span></span>
- <span data-ttu-id="5a0a9-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-213">'Razor'</span></span>
- <span data-ttu-id="5a0a9-214">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-215">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-216">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-217">'Identity'</span></span>
- <span data-ttu-id="5a0a9-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-219">'Razor'</span></span>
- <span data-ttu-id="5a0a9-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-221">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-222">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-223">'Identity'</span></span>
- <span data-ttu-id="5a0a9-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-225">'Razor'</span></span>
- <span data-ttu-id="5a0a9-226">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-227">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-228">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-229">'Identity'</span></span>
- <span data-ttu-id="5a0a9-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-231">'Razor'</span></span>
- <span data-ttu-id="5a0a9-232">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-233">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-234">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-235">'Identity'</span></span>
- <span data-ttu-id="5a0a9-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-237">'Razor'</span></span>
- <span data-ttu-id="5a0a9-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-239">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-240">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-241">'Identity'</span></span>
- <span data-ttu-id="5a0a9-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-243">'Razor'</span></span>
- <span data-ttu-id="5a0a9-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-245">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-246">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-247">'Identity'</span></span>
- <span data-ttu-id="5a0a9-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-249">'Razor'</span></span>
- <span data-ttu-id="5a0a9-250">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-251">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-252">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-253">'Identity'</span></span>
- <span data-ttu-id="5a0a9-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-255">'Razor'</span></span>
- <span data-ttu-id="5a0a9-256">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-257">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-258">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-259">'Identity'</span></span>
- <span data-ttu-id="5a0a9-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-261">'Razor'</span></span>
- <span data-ttu-id="5a0a9-262">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-263">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-264">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-264">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-265">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-265">'Identity'</span></span>
- <span data-ttu-id="5a0a9-266">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-266">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-267">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-267">'Razor'</span></span>
- <span data-ttu-id="5a0a9-268">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-268">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-269">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-270">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-270">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-271">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-271">'Identity'</span></span>
- <span data-ttu-id="5a0a9-272">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-272">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-273">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-273">'Razor'</span></span>
- <span data-ttu-id="5a0a9-274">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-274">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-275">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-276">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-277">'Identity'</span></span>
- <span data-ttu-id="5a0a9-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-279">'Razor'</span></span>
- <span data-ttu-id="5a0a9-280">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-281">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-282">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-282">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-283">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-283">'Identity'</span></span>
- <span data-ttu-id="5a0a9-284">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-284">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-285">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-285">'Razor'</span></span>
- <span data-ttu-id="5a0a9-286">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-286">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-287">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-288">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-288">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-289">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-289">'Identity'</span></span>
- <span data-ttu-id="5a0a9-290">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-290">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-291">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-291">'Razor'</span></span>
- <span data-ttu-id="5a0a9-292">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-292">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-293">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-294">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-294">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-295">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-295">'Identity'</span></span>
- <span data-ttu-id="5a0a9-296">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-296">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-297">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-297">'Razor'</span></span>
- <span data-ttu-id="5a0a9-298">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-298">'SignalR' uid:</span></span> 

<span data-ttu-id="5a0a9-299">-------------------------------- | ---Titel: Author: Description: monikerrange: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-299">-------------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-300">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-301">'Identity'</span></span>
- <span data-ttu-id="5a0a9-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-303">'Razor'</span></span>
- <span data-ttu-id="5a0a9-304">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-304">'SignalR' uid:</span></span> 

<span data-ttu-id="5a0a9-305">--- | | [öffentlich](https://tools.ietf.org/html/rfc7234#section-5.2.2.5) | Die Antwort kann in einem Cache gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-305">--- | | [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | A cache may store the response.</span></span> <span data-ttu-id="5a0a9-306">| | [Privat](https://tools.ietf.org/html/rfc7234#section-5.2.2.6) | Die Antwort darf nicht von einem freigegebenen Cache gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-306">| | [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | The response must not be stored by a shared cache.</span></span> <span data-ttu-id="5a0a9-307">In einem privaten Cache kann die Antwort gespeichert und wieder verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-307">A private cache may store and reuse the response.</span></span> <span data-ttu-id="5a0a9-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1) | Der Client akzeptiert keine Antwort, deren Alter größer ist als die angegebene Anzahl von Sekunden.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-308">| | [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | The client doesn't accept a response whose age is greater than the specified number of seconds.</span></span> <span data-ttu-id="5a0a9-309">Beispiele: `max-age=60` (60 Sekunden), `max-age=2592000` (1 Monat) | | [No-Cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4)  |  **Bei Anforderungen**: ein Cache darf keine gespeicherte Antwort verwenden, um die Anforderung zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-309">Examples: `max-age=60` (60 seconds), `max-age=2592000` (1 month) | | [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **On requests**: A cache must not use a stored response to satisfy the request.</span></span> <span data-ttu-id="5a0a9-310">Der Ursprungsserver generiert die Antwort für den Client erneut, und die Middleware aktualisiert die gespeicherte Antwort im Cache.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-310">The origin server regenerates the response for the client, and the middleware updates the stored response in its cache.</span></span><br><br><span data-ttu-id="5a0a9-311">**Bei Antworten**: die Antwort darf nicht für eine nachfolgende Anforderung ohne Validierung auf dem Ursprungsserver verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-311">**On responses**: The response must not be used for a subsequent request without validation on the origin server.</span></span> <span data-ttu-id="5a0a9-312">| | [No-Store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5)  |  **Bei Anforderungen**: die Anforderung darf nicht in einem Cache gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-312">| | [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **On requests**: A cache must not store the request.</span></span><br><br><span data-ttu-id="5a0a9-313">**Bei Antworten**: ein Cache darf keinen Teil der Antwort speichern.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-313">**On responses**: A cache must not store any part of the response.</span></span> |

<span data-ttu-id="5a0a9-314">Andere Cache Header, die eine Rolle beim Caching spielen, sind in der folgenden Tabelle aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-314">Other cache headers that play a role in caching are shown in the following table.</span></span>

| <span data-ttu-id="5a0a9-315">Header</span><span class="sxs-lookup"><span data-stu-id="5a0a9-315">Header</span></span>                                                     | <span data-ttu-id="5a0a9-316">Funktion</span><span class="sxs-lookup"><span data-stu-id="5a0a9-316">Function</span></span> |
| ---
<span data-ttu-id="5a0a9-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-317">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-318">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-319">'Identity'</span></span>
- <span data-ttu-id="5a0a9-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-321">'Razor'</span></span>
- <span data-ttu-id="5a0a9-322">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-323">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-324">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-324">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-325">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-325">'Identity'</span></span>
- <span data-ttu-id="5a0a9-326">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-326">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-327">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-327">'Razor'</span></span>
- <span data-ttu-id="5a0a9-328">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-328">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-329">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-330">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-330">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-331">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-331">'Identity'</span></span>
- <span data-ttu-id="5a0a9-332">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-332">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-333">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-333">'Razor'</span></span>
- <span data-ttu-id="5a0a9-334">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-334">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-335">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-336">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-336">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-337">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-337">'Identity'</span></span>
- <span data-ttu-id="5a0a9-338">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-338">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-339">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-339">'Razor'</span></span>
- <span data-ttu-id="5a0a9-340">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-340">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-341">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-342">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-342">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-343">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-343">'Identity'</span></span>
- <span data-ttu-id="5a0a9-344">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-344">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-345">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-345">'Razor'</span></span>
- <span data-ttu-id="5a0a9-346">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-346">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-347">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-348">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-348">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-349">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-349">'Identity'</span></span>
- <span data-ttu-id="5a0a9-350">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-350">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-351">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-351">'Razor'</span></span>
- <span data-ttu-id="5a0a9-352">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-352">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-353">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-354">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-354">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-355">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-355">'Identity'</span></span>
- <span data-ttu-id="5a0a9-356">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-356">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-357">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-357">'Razor'</span></span>
- <span data-ttu-id="5a0a9-358">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-358">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-359">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-360">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-360">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-361">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-361">'Identity'</span></span>
- <span data-ttu-id="5a0a9-362">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-362">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-363">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-363">'Razor'</span></span>
- <span data-ttu-id="5a0a9-364">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-364">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-365">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-366">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-367">'Identity'</span></span>
- <span data-ttu-id="5a0a9-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-369">'Razor'</span></span>
- <span data-ttu-id="5a0a9-370">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-371">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-372">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-372">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-373">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-373">'Identity'</span></span>
- <span data-ttu-id="5a0a9-374">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-374">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-375">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-375">'Razor'</span></span>
- <span data-ttu-id="5a0a9-376">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-376">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-377">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-378">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-378">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-379">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-379">'Identity'</span></span>
- <span data-ttu-id="5a0a9-380">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-380">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-381">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-381">'Razor'</span></span>
- <span data-ttu-id="5a0a9-382">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-382">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-383">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-384">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-384">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-385">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-385">'Identity'</span></span>
- <span data-ttu-id="5a0a9-386">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-386">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-387">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-387">'Razor'</span></span>
- <span data-ttu-id="5a0a9-388">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-388">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-389">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-390">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-390">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-391">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-391">'Identity'</span></span>
- <span data-ttu-id="5a0a9-392">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-392">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-393">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-393">'Razor'</span></span>
- <span data-ttu-id="5a0a9-394">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-394">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-395">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-396">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-396">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-397">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-397">'Identity'</span></span>
- <span data-ttu-id="5a0a9-398">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-398">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-399">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-399">'Razor'</span></span>
- <span data-ttu-id="5a0a9-400">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-400">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-401">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-402">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-403">'Identity'</span></span>
- <span data-ttu-id="5a0a9-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-405">'Razor'</span></span>
- <span data-ttu-id="5a0a9-406">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-407">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-408">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-409">'Identity'</span></span>
- <span data-ttu-id="5a0a9-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-411">'Razor'</span></span>
- <span data-ttu-id="5a0a9-412">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-413">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-414">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-414">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-415">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-415">'Identity'</span></span>
- <span data-ttu-id="5a0a9-416">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-416">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-417">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-417">'Razor'</span></span>
- <span data-ttu-id="5a0a9-418">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-418">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-419">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-420">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-420">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-421">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-421">'Identity'</span></span>
- <span data-ttu-id="5a0a9-422">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-422">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-423">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-423">'Razor'</span></span>
- <span data-ttu-id="5a0a9-424">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-424">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-425">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-426">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-426">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-427">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-427">'Identity'</span></span>
- <span data-ttu-id="5a0a9-428">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-428">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-429">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-429">'Razor'</span></span>
- <span data-ttu-id="5a0a9-430">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-430">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-431">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-432">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-432">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-433">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-433">'Identity'</span></span>
- <span data-ttu-id="5a0a9-434">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-434">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-435">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-435">'Razor'</span></span>
- <span data-ttu-id="5a0a9-436">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-436">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-437">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-438">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-438">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-439">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-439">'Identity'</span></span>
- <span data-ttu-id="5a0a9-440">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-440">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-441">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-441">'Razor'</span></span>
- <span data-ttu-id="5a0a9-442">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-442">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-443">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-444">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-445">'Identity'</span></span>
- <span data-ttu-id="5a0a9-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-447">'Razor'</span></span>
- <span data-ttu-id="5a0a9-448">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-449">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-450">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-451">'Identity'</span></span>
- <span data-ttu-id="5a0a9-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-453">'Razor'</span></span>
- <span data-ttu-id="5a0a9-454">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-455">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-456">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-456">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-457">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-457">'Identity'</span></span>
- <span data-ttu-id="5a0a9-458">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-458">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-459">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-459">'Razor'</span></span>
- <span data-ttu-id="5a0a9-460">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-460">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-461">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-462">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-462">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-463">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-463">'Identity'</span></span>
- <span data-ttu-id="5a0a9-464">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-464">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-465">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-465">'Razor'</span></span>
- <span data-ttu-id="5a0a9-466">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-466">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-467">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-468">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-468">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-469">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-469">'Identity'</span></span>
- <span data-ttu-id="5a0a9-470">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-470">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-471">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-471">'Razor'</span></span>
- <span data-ttu-id="5a0a9-472">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-472">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-473">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-474">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-474">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-475">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-475">'Identity'</span></span>
- <span data-ttu-id="5a0a9-476">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-476">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-477">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-477">'Razor'</span></span>
- <span data-ttu-id="5a0a9-478">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-478">'SignalR' uid:</span></span> 

<span data-ttu-id="5a0a9-479">----------------------------- | ---Titel: Author: Description: monikerrange: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-479">----------------------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-480">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-480">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-481">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-481">'Identity'</span></span>
- <span data-ttu-id="5a0a9-482">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-482">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-483">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-483">'Razor'</span></span>
- <span data-ttu-id="5a0a9-484">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-484">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-485">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-486">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-486">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-487">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-487">'Identity'</span></span>
- <span data-ttu-id="5a0a9-488">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-488">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-489">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-489">'Razor'</span></span>
- <span data-ttu-id="5a0a9-490">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-490">'SignalR' uid:</span></span> 

<span data-ttu-id="5a0a9-491">---- | | [Alter](https://tools.ietf.org/html/rfc7234#section-5.1) | Eine Schätzung der Zeitspanne (in Sekunden), seit die die Antwort auf dem Ursprungsserver generiert oder erfolgreich überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-491">---- | | [Age](https://tools.ietf.org/html/rfc7234#section-5.1)     | An estimate of the amount of time in seconds since the response was generated or successfully validated at the origin server.</span></span> <span data-ttu-id="5a0a9-492">| | [Läuft](https://tools.ietf.org/html/rfc7234#section-5.3) ab | Die Zeit, nach der die Antwort als veraltet eingestuft wird.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-492">| | [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | The time after which the response is considered stale.</span></span> <span data-ttu-id="5a0a9-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4) | Besteht aus Gründen der Abwärtskompatibilität mit HTTP/1.0-Caches zum Festlegen des `no-cache` Verhaltens.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-493">| | [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Exists for backwards compatibility with HTTP/1.0 caches for setting `no-cache` behavior.</span></span> <span data-ttu-id="5a0a9-494">Wenn der `Cache-Control` Header vorhanden ist, `Pragma` wird der Header ignoriert.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-494">If the `Cache-Control` header is present, the `Pragma` header is ignored.</span></span> <span data-ttu-id="5a0a9-495">| | [Variieren](https://tools.ietf.org/html/rfc7231#section-7.1.4) | Gibt an, dass eine zwischengespeicherte Antwort nicht gesendet werden darf, es sei denn, alle `Vary` Header Felder stimmen mit der ursprünglichen Anforderung der zwischengespeicherten Antwort und der neuen Anforderung gleich.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-495">| | [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Specifies that a cached response must not be sent unless all of the `Vary` header fields match in both the cached response's original request and the new request.</span></span> |

## <a name="http-based-caching-respects-request-cache-control-directives"></a><span data-ttu-id="5a0a9-496">HTTP-basiertes Zwischenspeichern, Anforderungs Cache-Steuerungs Direktiven</span><span class="sxs-lookup"><span data-stu-id="5a0a9-496">HTTP-based caching respects request Cache-Control directives</span></span>

<span data-ttu-id="5a0a9-497">Die [http 1,1-cachingspezifikation für den Cache-Control-Header](https://tools.ietf.org/html/rfc7234#section-5.2) erfordert einen Cache, um einen gültigen Header zu berücksichtigen, der `Cache-Control` vom Client gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-497">The [HTTP 1.1 Caching specification for the Cache-Control header](https://tools.ietf.org/html/rfc7234#section-5.2) requires a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="5a0a9-498">Ein Client kann Anforderungen mit einem `no-cache` Header Wert senden und erzwingen, dass der Server eine neue Antwort für jede Anforderung generiert.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-498">A client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span>

<span data-ttu-id="5a0a9-499">`Cache-Control`Wenn Sie das Ziel der HTTP-Zwischenspeicherung in Erwägung gezogen haben, ist es sinnvoll, Client Anforderungs Header zu berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-499">Always honoring client `Cache-Control` request headers makes sense if you consider the goal of HTTP caching.</span></span> <span data-ttu-id="5a0a9-500">Unter der offiziellen Spezifikation soll das Caching die Latenz und den Netzwerk Aufwand bei der Erfüllung von Anforderungen in einem Netzwerk von Clients, Proxys und Servern reduzieren.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-500">Under the official specification, caching is meant to reduce the latency and network overhead of satisfying requests across a network of clients, proxies, and servers.</span></span> <span data-ttu-id="5a0a9-501">Es ist nicht notwendigerweise eine Möglichkeit, die Last auf einem Ursprungsserver zu steuern.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-501">It isn't necessarily a way to control the load on an origin server.</span></span>

<span data-ttu-id="5a0a9-502">Es gibt keine Entwickler Kontrolle über dieses zwischen Speicherungs Verhalten, wenn die [Middleware](xref:performance/caching/middleware) zum Zwischenspeichern von Antworten verwendet wird, da die Middleware die offizielle cachingspezifikation befolgt.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-502">There's no developer control over this caching behavior when using the [Response Caching Middleware](xref:performance/caching/middleware) because the middleware adheres to the official caching specification.</span></span> <span data-ttu-id="5a0a9-503">[Geplante Erweiterungen der Middleware](https://github.com/dotnet/AspNetCore/issues/2612) sind die Möglichkeit, die Middleware so zu konfigurieren, dass der Header einer Anforderung ignoriert wird, `Cache-Control` Wenn eine zwischengespeicherte Antwort verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-503">[Planned enhancements to the middleware](https://github.com/dotnet/AspNetCore/issues/2612) are an opportunity to configure the middleware to ignore a request's `Cache-Control` header when deciding to serve a cached response.</span></span> <span data-ttu-id="5a0a9-504">Geplante Erweiterungen bieten die Möglichkeit, die Serverlast besser zu steuern.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-504">Planned enhancements provide an opportunity to better control server load.</span></span>

## <a name="other-caching-technology-in-aspnet-core"></a><span data-ttu-id="5a0a9-505">Andere cachingtechnologie in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="5a0a9-505">Other caching technology in ASP.NET Core</span></span>

### <a name="in-memory-caching"></a><span data-ttu-id="5a0a9-506">Zwischenspeicherung im Speicher</span><span class="sxs-lookup"><span data-stu-id="5a0a9-506">In-memory caching</span></span>

<span data-ttu-id="5a0a9-507">In-Memory-Caching verwendet Server Arbeitsspeicher zum Speichern von zwischengespeicherten Daten.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-507">In-memory caching uses server memory to store cached data.</span></span> <span data-ttu-id="5a0a9-508">Diese Art der Zwischenspeicherung eignet sich für einen einzelnen Server oder mehrere Server, die persistente *Sitzungen*verwenden.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-508">This type of caching is suitable for a single server or multiple servers using *sticky sessions*.</span></span> <span data-ttu-id="5a0a9-509">Mit "persistente Sitzungen" können die Anforderungen von einem Client für die Verarbeitung immer an denselben Server weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-509">Sticky sessions means that the requests from a client are always routed to the same server for processing.</span></span>

<span data-ttu-id="5a0a9-510">Weitere Informationen finden Sie unter <xref:performance/caching/memory>.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-510">For more information, see <xref:performance/caching/memory>.</span></span>

### <a name="distributed-cache"></a><span data-ttu-id="5a0a9-511">Verteilter Cache</span><span class="sxs-lookup"><span data-stu-id="5a0a9-511">Distributed Cache</span></span>

<span data-ttu-id="5a0a9-512">Verwenden Sie einen verteilten Cache, um Daten im Arbeitsspeicher zu speichern, wenn die app in einer Cloud-oder Serverfarm gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-512">Use a distributed cache to store data in memory when the app is hosted in a cloud or server farm.</span></span> <span data-ttu-id="5a0a9-513">Der Cache wird auf den Servern freigegeben, die Anforderungen verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-513">The cache is shared across the servers that process requests.</span></span> <span data-ttu-id="5a0a9-514">Ein Client kann eine Anforderung übermitteln, die von einem beliebigen Server in der Gruppe verarbeitet wird, wenn zwischengespeicherte Daten für den Client verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-514">A client can submit a request that's handled by any server in the group if cached data for the client is available.</span></span> <span data-ttu-id="5a0a9-515">ASP.net Core funktioniert mit verteilten Caches SQL Server, [redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)und [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .</span><span class="sxs-lookup"><span data-stu-id="5a0a9-515">ASP.NET Core works with SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), and [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) distributed caches.</span></span>

<span data-ttu-id="5a0a9-516">Weitere Informationen finden Sie unter <xref:performance/caching/distributed>.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-516">For more information, see <xref:performance/caching/distributed>.</span></span>

### <a name="cache-tag-helper"></a><span data-ttu-id="5a0a9-517">Cache-Taghilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="5a0a9-517">Cache Tag Helper</span></span>

<span data-ttu-id="5a0a9-518">Zwischenspeichern des Inhalts aus einer MVC-Ansicht oder- Razor Seite mit dem cachetaghilfsprogramm.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-518">Cache the content from an MVC view or Razor Page with the Cache Tag Helper.</span></span> <span data-ttu-id="5a0a9-519">Das Cache-taghilfsprogramm verwendet das Zwischenspeichern im Arbeitsspeicher zum Speichern von Daten.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-519">The Cache Tag Helper uses in-memory caching to store data.</span></span>

<span data-ttu-id="5a0a9-520">Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-520">For more information, see <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.</span></span>

### <a name="distributed-cache-tag-helper"></a><span data-ttu-id="5a0a9-521">Taghilfsprogramm für verteilten Cache</span><span class="sxs-lookup"><span data-stu-id="5a0a9-521">Distributed Cache Tag Helper</span></span>

<span data-ttu-id="5a0a9-522">Zwischenspeichern des Inhalts aus einer MVC-Ansicht oder- Razor Seite in verteilten Cloud-oder Webfarm Szenarios mit dem taghilfsprogramm für verteilte Caches.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-522">Cache the content from an MVC view or Razor Page in distributed cloud or web farm scenarios with the Distributed Cache Tag Helper.</span></span> <span data-ttu-id="5a0a9-523">Das taghilfsprogramm für verteilte Caches verwendet SQL Server, [redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)oder [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) zum Speichern von Daten.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-523">The Distributed Cache Tag Helper uses SQL Server, [Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis), or [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) to store data.</span></span>

<span data-ttu-id="5a0a9-524">Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-524">For more information, see <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.</span></span>

## <a name="responsecache-attribute"></a><span data-ttu-id="5a0a9-525">Response Cache-Attribut</span><span class="sxs-lookup"><span data-stu-id="5a0a9-525">ResponseCache attribute</span></span>

<span data-ttu-id="5a0a9-526">Der <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> gibt die Parameter an, die zum Festlegen geeigneter Header beim Zwischenspeichern von Antworten erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-526">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> specifies the parameters necessary for setting appropriate headers in response caching.</span></span>

> [!WARNING]
> <span data-ttu-id="5a0a9-527">Deaktivieren Sie das Zwischenspeichern für Inhalte, die Informationen für authentifizierte Clients enthalten.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-527">Disable caching for content that contains information for authenticated clients.</span></span> <span data-ttu-id="5a0a9-528">Das Zwischenspeichern sollte nur für Inhalt aktiviert werden, der sich nicht auf der Grundlage der Identität eines Benutzers ändert, oder ob ein Benutzer angemeldet ist.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-528">Caching should only be enabled for content that doesn't change based on a user's identity or whether a user is signed in.</span></span>

<span data-ttu-id="5a0a9-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>variiert die gespeicherte Antwort anhand der Werte der angegebenen Liste von Abfrage Schlüsseln.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-529"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> varies the stored response by the values of the given list of query keys.</span></span> <span data-ttu-id="5a0a9-530">Wenn ein einzelner Wert `*` bereitgestellt wird, variiert die Middleware mit den Antworten aller Parameter der Anforderungs Abfrage Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-530">When a single value of `*` is provided, the middleware varies responses by all request query string parameters.</span></span>

<span data-ttu-id="5a0a9-531">Die [Middleware zum Zwischenspeichern von Antworten](xref:performance/caching/middleware) muss aktiviert sein, um die Eigenschaft festzulegen <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> .</span><span class="sxs-lookup"><span data-stu-id="5a0a9-531">[Response Caching Middleware](xref:performance/caching/middleware) must be enabled to set the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="5a0a9-532">Andernfalls wird eine Lauf Zeit Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-532">Otherwise, a runtime exception is thrown.</span></span> <span data-ttu-id="5a0a9-533">Es gibt keinen entsprechenden HTTP-Header für die- <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-533">There isn't a corresponding HTTP header for the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> property.</span></span> <span data-ttu-id="5a0a9-534">Die-Eigenschaft ist eine HTTP-Funktion, die von der zwischenware zum Zwischenspeichern von Antworten</span><span class="sxs-lookup"><span data-stu-id="5a0a9-534">The property is an HTTP feature handled by Response Caching Middleware.</span></span> <span data-ttu-id="5a0a9-535">Damit die Middleware eine zwischengespeicherte Antwort bereitstellt, müssen die Abfrage Zeichenfolge und der Abfrage Zeichenfolgen-Wert mit einer vorherigen Anforderung identisch sein.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-535">For the middleware to serve a cached response, the query string and query string value must match a previous request.</span></span> <span data-ttu-id="5a0a9-536">Stellen Sie sich z. b. die Abfolge der in der folgenden Tabelle gezeigten Anforderungen und Ergebnisse vor.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-536">For example, consider the sequence of requests and results shown in the following table.</span></span>

| <span data-ttu-id="5a0a9-537">Anforderung</span><span class="sxs-lookup"><span data-stu-id="5a0a9-537">Request</span></span>                          | <span data-ttu-id="5a0a9-538">Ergebnis</span><span class="sxs-lookup"><span data-stu-id="5a0a9-538">Result</span></span>                    |
| ---
<span data-ttu-id="5a0a9-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-539">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-540">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-540">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-541">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-541">'Identity'</span></span>
- <span data-ttu-id="5a0a9-542">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-542">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-543">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-543">'Razor'</span></span>
- <span data-ttu-id="5a0a9-544">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-544">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-545">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-546">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-546">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-547">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-547">'Identity'</span></span>
- <span data-ttu-id="5a0a9-548">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-548">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-549">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-549">'Razor'</span></span>
- <span data-ttu-id="5a0a9-550">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-550">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-551">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-552">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-553">'Identity'</span></span>
- <span data-ttu-id="5a0a9-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-555">'Razor'</span></span>
- <span data-ttu-id="5a0a9-556">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-557">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-558">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-558">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-559">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-559">'Identity'</span></span>
- <span data-ttu-id="5a0a9-560">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-560">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-561">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-561">'Razor'</span></span>
- <span data-ttu-id="5a0a9-562">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-562">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-563">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-564">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-564">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-565">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-565">'Identity'</span></span>
- <span data-ttu-id="5a0a9-566">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-566">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-567">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-567">'Razor'</span></span>
- <span data-ttu-id="5a0a9-568">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-568">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-569">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-570">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-570">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-571">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-571">'Identity'</span></span>
- <span data-ttu-id="5a0a9-572">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-572">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-573">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-573">'Razor'</span></span>
- <span data-ttu-id="5a0a9-574">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-574">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-575">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-576">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-576">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-577">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-577">'Identity'</span></span>
- <span data-ttu-id="5a0a9-578">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-578">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-579">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-579">'Razor'</span></span>
- <span data-ttu-id="5a0a9-580">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-580">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-581">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-582">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-582">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-583">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-583">'Identity'</span></span>
- <span data-ttu-id="5a0a9-584">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-584">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-585">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-585">'Razor'</span></span>
- <span data-ttu-id="5a0a9-586">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-586">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-587">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-588">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-588">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-589">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-589">'Identity'</span></span>
- <span data-ttu-id="5a0a9-590">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-590">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-591">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-591">'Razor'</span></span>
- <span data-ttu-id="5a0a9-592">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-592">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-593">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-594">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-595">'Identity'</span></span>
- <span data-ttu-id="5a0a9-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-597">'Razor'</span></span>
- <span data-ttu-id="5a0a9-598">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-599">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-600">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-600">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-601">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-601">'Identity'</span></span>
- <span data-ttu-id="5a0a9-602">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-602">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-603">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-603">'Razor'</span></span>
- <span data-ttu-id="5a0a9-604">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-604">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-605">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-606">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-606">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-607">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-607">'Identity'</span></span>
- <span data-ttu-id="5a0a9-608">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-608">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-609">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-609">'Razor'</span></span>
- <span data-ttu-id="5a0a9-610">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-610">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-611">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-612">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-612">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-613">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-613">'Identity'</span></span>
- <span data-ttu-id="5a0a9-614">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-614">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-615">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-615">'Razor'</span></span>
- <span data-ttu-id="5a0a9-616">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-616">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-617">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-618">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-618">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-619">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-619">'Identity'</span></span>
- <span data-ttu-id="5a0a9-620">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-620">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-621">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-621">'Razor'</span></span>
- <span data-ttu-id="5a0a9-622">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-622">'SignalR' uid:</span></span> 

<span data-ttu-id="5a0a9-623">---------------- | ---Titel: Author: Description: monikerrange: ms. Author: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-623">---------------- | --- title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-624">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-624">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-625">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-625">'Identity'</span></span>
- <span data-ttu-id="5a0a9-626">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-626">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-627">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-627">'Razor'</span></span>
- <span data-ttu-id="5a0a9-628">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-628">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-629">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-630">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-630">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-631">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-631">'Identity'</span></span>
- <span data-ttu-id="5a0a9-632">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-632">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-633">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-633">'Razor'</span></span>
- <span data-ttu-id="5a0a9-634">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-634">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-635">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-636">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-637">'Identity'</span></span>
- <span data-ttu-id="5a0a9-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-639">'Razor'</span></span>
- <span data-ttu-id="5a0a9-640">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-641">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-642">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-642">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-643">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-643">'Identity'</span></span>
- <span data-ttu-id="5a0a9-644">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-644">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-645">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-645">'Razor'</span></span>
- <span data-ttu-id="5a0a9-646">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-646">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-647">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-648">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-648">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-649">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-649">'Identity'</span></span>
- <span data-ttu-id="5a0a9-650">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-650">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-651">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-651">'Razor'</span></span>
- <span data-ttu-id="5a0a9-652">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-652">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-653">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-654">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-654">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-655">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-655">'Identity'</span></span>
- <span data-ttu-id="5a0a9-656">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-656">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-657">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-657">'Razor'</span></span>
- <span data-ttu-id="5a0a9-658">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-658">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-659">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-660">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-660">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-661">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-661">'Identity'</span></span>
- <span data-ttu-id="5a0a9-662">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-662">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-663">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-663">'Razor'</span></span>
- <span data-ttu-id="5a0a9-664">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-664">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-665">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-666">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-666">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-667">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-667">'Identity'</span></span>
- <span data-ttu-id="5a0a9-668">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-668">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-669">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-669">'Razor'</span></span>
- <span data-ttu-id="5a0a9-670">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-670">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-671">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-672">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-672">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-673">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-673">'Identity'</span></span>
- <span data-ttu-id="5a0a9-674">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-674">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-675">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-675">'Razor'</span></span>
- <span data-ttu-id="5a0a9-676">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-676">'SignalR' uid:</span></span> 

-
<span data-ttu-id="5a0a9-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-677">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="5a0a9-678">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-678">'Blazor'</span></span>
- <span data-ttu-id="5a0a9-679">'Identity'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-679">'Identity'</span></span>
- <span data-ttu-id="5a0a9-680">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-680">'Let's Encrypt'</span></span>
- <span data-ttu-id="5a0a9-681">'Razor'</span><span class="sxs-lookup"><span data-stu-id="5a0a9-681">'Razor'</span></span>
- <span data-ttu-id="5a0a9-682">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-682">'SignalR' uid:</span></span> 

<span data-ttu-id="5a0a9-683">------------- | | `http://example.com?key1=value1` | Vom Server zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-683">------------- | | `http://example.com?key1=value1` | Returned from the server.</span></span> <span data-ttu-id="5a0a9-684">| | `http://example.com?key1=value1` | Von der Middleware zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-684">| | `http://example.com?key1=value1` | Returned from middleware.</span></span> <span data-ttu-id="5a0a9-685">| | `http://example.com?key1=value2` | Vom Server zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-685">| | `http://example.com?key1=value2` | Returned from the server.</span></span> |

<span data-ttu-id="5a0a9-686">Die erste Anforderung wird vom Server zurückgegeben und in der Middleware zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-686">The first request is returned by the server and cached in middleware.</span></span> <span data-ttu-id="5a0a9-687">Die zweite Anforderung wird von Middleware zurückgegeben, da die Abfrage Zeichenfolge mit der vorherigen Anforderung übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-687">The second request is returned by middleware because the query string matches the previous request.</span></span> <span data-ttu-id="5a0a9-688">Die dritte Anforderung befindet sich nicht im middlewarecache, da der Wert der Abfrage Zeichenfolge mit einer vorherigen Anforderung nicht identisch ist.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-688">The third request isn't in the middleware cache because the query string value doesn't match a previous request.</span></span>

<span data-ttu-id="5a0a9-689"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Wird verwendet, um (via) zu konfigurieren und zu erstellen <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` .</span><span class="sxs-lookup"><span data-stu-id="5a0a9-689">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> is used to configure and create (via <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) a `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`.</span></span> <span data-ttu-id="5a0a9-690">Der `ResponseCacheFilter` führt die Aktualisierung der entsprechenden HTTP-Header und Features der Antwort aus.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-690">The `ResponseCacheFilter` performs the work of updating the appropriate HTTP headers and features of the response.</span></span> <span data-ttu-id="5a0a9-691">Der Filter:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-691">The filter:</span></span>

* <span data-ttu-id="5a0a9-692">Entfernt alle vorhandenen Header für `Vary` , `Cache-Control` und `Pragma` .</span><span class="sxs-lookup"><span data-stu-id="5a0a9-692">Removes any existing headers for `Vary`, `Cache-Control`, and `Pragma`.</span></span>
* <span data-ttu-id="5a0a9-693">Schreibt die entsprechenden Header auf der Grundlage der im festgelegten Eigenschaften <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .</span><span class="sxs-lookup"><span data-stu-id="5a0a9-693">Writes out the appropriate headers based on the properties set in the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.</span></span>
* <span data-ttu-id="5a0a9-694">Aktualisiert das HTTP-Feature zum Zwischenspeichern von Antworten, wenn <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> festgelegt ist</span><span class="sxs-lookup"><span data-stu-id="5a0a9-694">Updates the response caching HTTP feature if <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> is set.</span></span>

### <a name="vary"></a><span data-ttu-id="5a0a9-695">Abweichen</span><span class="sxs-lookup"><span data-stu-id="5a0a9-695">Vary</span></span>

<span data-ttu-id="5a0a9-696">Dieser Header wird nur geschrieben, wenn die- <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> Eigenschaft festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-696">This header is only written when the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property is set.</span></span> <span data-ttu-id="5a0a9-697">Die Eigenschaft, die auf den Wert der Eigenschaft festgelegt `Vary` ist.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-697">The property set to the `Vary` property's value.</span></span> <span data-ttu-id="5a0a9-698">Im folgenden Beispiel wird die- <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> Eigenschaft verwendet:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-698">The following sample uses the <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> property:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

<span data-ttu-id="5a0a9-699">Verwenden Sie die Beispiel-APP, um die Antwortheader mit den Netzwerk Tools des Browsers anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-699">Using the sample app, view the response headers with the browser's network tools.</span></span> <span data-ttu-id="5a0a9-700">Die folgenden Antwortheader werden mit der Cache1-Seiten Antwort gesendet:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-700">The following response headers are sent with the Cache1 page response:</span></span>

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a><span data-ttu-id="5a0a9-701">NoStore und Location. None</span><span class="sxs-lookup"><span data-stu-id="5a0a9-701">NoStore and Location.None</span></span>

<span data-ttu-id="5a0a9-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>überschreibt die meisten anderen Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-702"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> overrides most of the other properties.</span></span> <span data-ttu-id="5a0a9-703">Wenn diese Eigenschaft auf festgelegt ist `true` , `Cache-Control` wird der-Header auf festgelegt `no-store` .</span><span class="sxs-lookup"><span data-stu-id="5a0a9-703">When this property is set to `true`, the `Cache-Control` header is set to `no-store`.</span></span> <span data-ttu-id="5a0a9-704">Wenn <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> auf festgelegt ist `None` :</span><span class="sxs-lookup"><span data-stu-id="5a0a9-704">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is set to `None`:</span></span>

* <span data-ttu-id="5a0a9-705">`Cache-Control` ist auf `no-store,no-cache` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-705">`Cache-Control` is set to `no-store,no-cache`.</span></span>
* <span data-ttu-id="5a0a9-706">`Pragma` ist auf `no-cache` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-706">`Pragma` is set to `no-cache`.</span></span>

<span data-ttu-id="5a0a9-707">Wenn <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> `false` den Wert <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> hat und den Wert `None` , `Cache-Control` und `Pragma` auf festgelegt ist `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="5a0a9-707">If <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is `false` and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> is `None`, `Cache-Control`, and `Pragma` are set to `no-cache`.</span></span>

<span data-ttu-id="5a0a9-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>wird in der Regel `true` für Fehlerseiten auf festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-708"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> is typically set to `true` for error pages.</span></span> <span data-ttu-id="5a0a9-709">Die Cache2-Seite in der Beispiel-app erzeugt Antwortheader, die den Client anweisen, die Antwort nicht zu speichern.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-709">The Cache2 page in the sample app produces response headers that instruct the client not to store the response.</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

<span data-ttu-id="5a0a9-710">Die Beispiel-App gibt die Cache2-Seite mit den folgenden Headern zurück:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-710">The sample app returns the Cache2 page with the following headers:</span></span>

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a><span data-ttu-id="5a0a9-711">Speicherort und Dauer</span><span class="sxs-lookup"><span data-stu-id="5a0a9-711">Location and Duration</span></span>

<span data-ttu-id="5a0a9-712">Um das Zwischenspeichern zu aktivieren, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> muss auf einen positiven Wert festgelegt werden und <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> muss entweder `Any` (Standard) oder sein `Client` .</span><span class="sxs-lookup"><span data-stu-id="5a0a9-712">To enable caching, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> must be set to a positive value and <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> must be either `Any` (the default) or `Client`.</span></span> <span data-ttu-id="5a0a9-713">Das Framework legt den- `Cache-Control` Header auf den Speicherort Wert fest, gefolgt vom `max-age` der Antwort.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-713">The framework sets the `Cache-Control` header to the location value followed by the `max-age` of the response.</span></span>

<span data-ttu-id="5a0a9-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>die Optionen von `Any` und `Client` werden in `Cache-Control` Header Werte von `public` `private` bzw. übersetzt.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-714"><xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>'s options of `Any` and `Client` translate into `Cache-Control` header values of `public` and `private`, respectively.</span></span> <span data-ttu-id="5a0a9-715">Wie im Abschnitt [NoStore und Location. None](#nostore-and-locationnone) vermerkt, legt das Festlegen <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> von auf `None` sowohl `Cache-Control` -als auch- `Pragma` Header auf fest `no-cache` .</span><span class="sxs-lookup"><span data-stu-id="5a0a9-715">As noted in the [NoStore and Location.None](#nostore-and-locationnone) section, setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> to `None` sets both `Cache-Control` and `Pragma` headers to `no-cache`.</span></span>

<span data-ttu-id="5a0a9-716">`Location.Any`( `Cache-Control` festgelegt auf `public` ) gibt an, dass der Wert des *Clients oder eines beliebigen zwischen Proxys zwischen* gespeichert werden kann, einschließlich der Zwischenspeicherung von zwischen [Speichern](xref:performance/caching/middleware)</span><span class="sxs-lookup"><span data-stu-id="5a0a9-716">`Location.Any` (`Cache-Control` set to `public`) indicates that the *client or any intermediate proxy* may cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="5a0a9-717">`Location.Client`( `Cache-Control` festgelegt auf `private` ) gibt an, dass der Wert *nur vom Client* zwischengespeichert werden darf.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-717">`Location.Client` (`Cache-Control` set to `private`) indicates that *only the client* may cache the value.</span></span> <span data-ttu-id="5a0a9-718">Der Wert kann nicht zwischengespeichert werden, einschließlich der [Middleware](xref:performance/caching/middleware)zum Zwischenspeichern von Antworten.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-718">No intermediate cache should cache the value, including [Response Caching Middleware](xref:performance/caching/middleware).</span></span>

<span data-ttu-id="5a0a9-719">Cache Steuerungs Header bieten lediglich Anleitungen für Clients und Vermittler Proxys, wann und wie Antworten zwischengespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-719">Cache control headers merely provide guidance to clients and intermediary proxies when and how to cache responses.</span></span> <span data-ttu-id="5a0a9-720">Es gibt keine Garantie dafür, dass Clients und Proxys die [http 1,1-cachingspezifikation](https://tools.ietf.org/html/rfc7234)berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-720">There's no guarantee that clients and proxies will honor the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234).</span></span> <span data-ttu-id="5a0a9-721">Die [Middleware zum Zwischenspeichern von Antworten](xref:performance/caching/middleware) folgt immer den durch die Spezifikation vorgegebenen Cache Regeln.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-721">[Response Caching Middleware](xref:performance/caching/middleware) always follows the caching rules laid out by the specification.</span></span>

<span data-ttu-id="5a0a9-722">Das folgende Beispiel zeigt das Cache3 Page Model aus der Beispiel-APP und die über das Festlegen <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> und belassen des Standard <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> Werts erstellten Header:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-722">The following example shows the Cache3 page model from the sample app and the headers produced by setting <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> and leaving the default <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> value:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

<span data-ttu-id="5a0a9-723">Die Beispiel-App gibt die Cache3-Seite mit dem folgenden Header zurück:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-723">The sample app returns the Cache3 page with the following header:</span></span>

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a><span data-ttu-id="5a0a9-724">Cache profile</span><span class="sxs-lookup"><span data-stu-id="5a0a9-724">Cache profiles</span></span>

<span data-ttu-id="5a0a9-725">Anstatt die Einstellungen des Antwort Caches für viele Controller Aktions Attribute zu duplizieren, können Cache Profile beim Einrichten von MVC/pages in als Optionen konfiguriert werden Razor `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5a0a9-725">Instead of duplicating response cache settings on many controller action attributes, cache profiles can be configured as options when setting up MVC/Razor Pages in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5a0a9-726">Werte, die in einem Cache Profil gefunden werden, auf das verwiesen wird, werden als Standardwerte verwendet <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> und von allen Eigenschaften überschrieben, die für das Attribut angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-726">Values found in a referenced cache profile are used as the defaults by the <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> and are overridden by any properties specified on the attribute.</span></span>

<span data-ttu-id="5a0a9-727">Einrichten eines Cache Profils.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-727">Set up a cache profile.</span></span> <span data-ttu-id="5a0a9-728">Das folgende Beispiel zeigt ein Cache Profil mit 30 Sekunden in der Beispiel-APP `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5a0a9-728">The following example shows a 30 second cache profile in the sample app's `Startup.ConfigureServices`:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

<span data-ttu-id="5a0a9-729">Das Cache4-Seiten Modell der Beispiel-App verweist auf das `Default30` Cache Profil:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-729">The sample app's Cache4 page model references the `Default30` cache profile:</span></span>

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<span data-ttu-id="5a0a9-730"><xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Kann angewendet werden auf:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-730">The <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> can be applied to:</span></span>

* Razor<span data-ttu-id="5a0a9-731">Seiten Handler (Klassen): Attribute können nicht auf Handlermethoden angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-731"> Page handlers (classes): Attributes can't be applied to handler methods.</span></span>
* <span data-ttu-id="5a0a9-732">MVC-Controller (Klassen).</span><span class="sxs-lookup"><span data-stu-id="5a0a9-732">MVC controllers (classes).</span></span>
* <span data-ttu-id="5a0a9-733">MVC-Aktionen (Methoden): Attribute auf Methoden Ebene überschreiben die in Attributen auf Klassenebene angegebenen Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="5a0a9-733">MVC actions (methods): Method-level attributes override the settings specified in class-level attributes.</span></span>

<span data-ttu-id="5a0a9-734">Der resultierende Header, der für die Antwort auf die Cache4-Seite vom `Default30` Cache Profil übernommen wird:</span><span class="sxs-lookup"><span data-stu-id="5a0a9-734">The resulting header applied to the Cache4 page response by the `Default30` cache profile:</span></span>

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a><span data-ttu-id="5a0a9-735">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5a0a9-735">Additional resources</span></span>

* [<span data-ttu-id="5a0a9-736">Speichern von Antworten in Caches</span><span class="sxs-lookup"><span data-stu-id="5a0a9-736">Storing Responses in Caches</span></span>](https://tools.ietf.org/html/rfc7234#section-3)
* [<span data-ttu-id="5a0a9-737">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="5a0a9-737">Cache-Control</span></span>](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
