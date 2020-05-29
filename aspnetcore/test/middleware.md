---
title: Testen von ASP.NET Core-Middleware
author: tratcher
description: Erfahren Sie, wie ASP.NET Core-Middleware mit TestServer testen.
ms.author: riande
ms.custom: mvc
ms.date: 5/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: ea7fc0e889ab32cbaf23257b3e866519af0727aa
ms.sourcegitcommit: 69e1a79a572b0af17d08e81af12c594b7316f2e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/15/2020
ms.locfileid: "83424538"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="e3b6a-103">Testen von ASP.NET Core-Middleware</span><span class="sxs-lookup"><span data-stu-id="e3b6a-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="e3b6a-104">Von [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="e3b6a-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="e3b6a-105">Middleware kann mit <xref:Microsoft.AspNetCore.TestHost.TestServer> isoliert getestet werden.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="e3b6a-106">Die Funktion ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e3b6a-106">It allows you to:</span></span>

* <span data-ttu-id="e3b6a-107">Instanziieren Sie eine App-Pipeline, die nur die Komponenten enthält, die Sie testen müssen.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="e3b6a-108">Senden Sie benutzerdefinierte Anforderungen, um das Verhalten der Middleware zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="e3b6a-109">Vorteile:</span><span class="sxs-lookup"><span data-stu-id="e3b6a-109">Advantages:</span></span>

* <span data-ttu-id="e3b6a-110">Anforderungen werden arbeitsspeicherintern gesendet und nicht über das Netzwerk serialisiert.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="e3b6a-111">Dadurch werden zusätzliche Aspekte wie Portverwaltung und HTTPS-Zertifikate vermieden.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="e3b6a-112">Ausnahmen in der Middleware können direkt an den aufrufenden Test zurückfließen.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="e3b6a-113">Es ist möglich, Serverdatenstrukturen, wie z. B. <xref:Microsoft.AspNetCore.Http.HttpContext>, direkt im Test anzupassen.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="e3b6a-114">Einrichten von TestServer</span><span class="sxs-lookup"><span data-stu-id="e3b6a-114">Set up the TestServer</span></span>

<span data-ttu-id="e3b6a-115">Erstellen Sie im Testprojekt einen Test:</span><span class="sxs-lookup"><span data-stu-id="e3b6a-115">In the test project, create a test:</span></span>

* <span data-ttu-id="e3b6a-116">Erstellen und starten Sie einen Host, der <xref:Microsoft.AspNetCore.TestHost.TestServer> verwendet.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="e3b6a-117">Fügen Sie alle benötigten Dienste hinzu, die die Middleware verwendet.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="e3b6a-118">Konfigurieren Sie die Verarbeitungspipeline für die Nutzung der Middleware für den Test.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="e3b6a-119">Senden von Anforderungen mit HttpClient</span><span class="sxs-lookup"><span data-stu-id="e3b6a-119">Send requests with HttpClient</span></span>
<span data-ttu-id="e3b6a-120">Senden Sie eine Anforderung mit <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="e3b6a-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="e3b6a-121">Bestätigen Sie das Ergebnis.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-121">Assert the result.</span></span> <span data-ttu-id="e3b6a-122">Machen Sie zunächst eine Assertion, die das Gegenteil des von Ihnen erwarteten Ergebnisses darstellt.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="e3b6a-123">Ein erster Lauf mit einer falsch-positiven Assertion bestätigt, dass der Test fehlschlägt, wenn die Middleware einwandfrei funktioniert.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="e3b6a-124">Führen Sie den Test aus, und bestätigen Sie, dass der Test fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="e3b6a-125">Im folgenden Beispiel sollte die Middleware den Statuscode 404 (*Nicht gefunden*) zurückgeben, wenn der Stammendpunkt angefordert wird.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="e3b6a-126">Führen Sie den ersten Testlauf mit `Assert.NotEqual( ... );` durch, der fehlschlagen sollte:</span><span class="sxs-lookup"><span data-stu-id="e3b6a-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="e3b6a-127">Ändern Sie die Assertion so, dass die Middleware unter normalen Betriebsbedingungen getestet wird.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="e3b6a-128">Im letzten Test wird `Assert.Equal( ... );` verwendet.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="e3b6a-129">Führen Sie den Test erneut aus, um zu bestätigen, dass er bestanden wird.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="e3b6a-130">Senden von Anforderungen mit HttpContext</span><span class="sxs-lookup"><span data-stu-id="e3b6a-130">Send requests with HttpContext</span></span>

<span data-ttu-id="e3b6a-131">Eine Test-App kann eine Anforderung auch mit [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A) senden.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="e3b6a-132">Im folgenden Beispiel erfolgen mehrere Prüfungen, wenn `https://example.com/A/Path/?and=query` von der Middleware verarbeitet wird:</span><span class="sxs-lookup"><span data-stu-id="e3b6a-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

```csharp
[Fact]
public async Task TestMiddleware_ExpectedResponse()
{
    using var host = await new HostBuilder()
        .ConfigureWebHost(webBuilder =>
        {
            webBuilder
                .UseTestServer()
                .ConfigureServices(services =>
                {
                    services.AddMyServices();
                })
                .Configure(app =>
                {
                    app.UseMiddleware<MyMiddleware>();
                });
        })
        .StartAsync();

    var server = host.GetTestServer();
    server.BaseAddress = new Uri("https://example.com/A/Path/");

    var context = await server.SendAsync(c =>
    {
        c.Request.Method = HttpMethods.Post;
        c.Request.Path = "/and/file.txt";
        c.Request.QueryString = new QueryString("?and=query");
    });

    Assert.True(context.RequestAborted.CanBeCanceled);
    Assert.Equal(HttpProtocol.Http11, context.Request.Protocol);
    Assert.Equal("POST", context.Request.Method);
    Assert.Equal("https", context.Request.Scheme);
    Assert.Equal("example.com", context.Request.Host.Value);
    Assert.Equal("/A/Path", context.Request.PathBase.Value);
    Assert.Equal("/and/file.txt", context.Request.Path.Value);
    Assert.Equal("?and=query", context.Request.QueryString.Value);
    Assert.NotNull(context.Request.Body);
    Assert.NotNull(context.Request.Headers);
    Assert.NotNull(context.Response.Headers);
    Assert.NotNull(context.Response.Body);
    Assert.Equal(404, context.Response.StatusCode);
    Assert.Null(context.Features.Get<IHttpResponseFeature>().ReasonPhrase);
}
```

<span data-ttu-id="e3b6a-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> erlaubt die Direktkonfiguration eines <xref:Microsoft.AspNetCore.Http.HttpContext>-Objekts, anstatt die <xref:System.Net.Http.HttpClient>-Abstraktionen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="e3b6a-134">Verwenden Sie <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>, um Strukturen zu manipulieren, die nur auf dem Server verfügbar sind, wie z. B. [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) oder [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="e3b6a-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="e3b6a-135">Wie im früheren Beispiel, das auf eine Antwort des Typs *404 – nicht gefunden* getestet wurde, überprüfen Sie das Gegenteil für jede `Assert`-Anweisung im vorhergehenden Test.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="e3b6a-136">Die Überprüfung bestätigt, dass der Test bei normalem Betrieb der Middleware ordnungsgemäß fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="e3b6a-137">Nachdem Sie bestätigt haben, dass der falsch positive Test funktioniert, legen Sie die endgültigen `Assert`-Anweisungen für die erwarteten Bedingungen und Werte des Tests fest.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="e3b6a-138">Führen Sie ihn erneut aus, um zu bestätigen, dass er bestanden wird.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-138">Run it again to confirm that the test passes.</span></span>

## <a name="testserver-limitations"></a><span data-ttu-id="e3b6a-139">TestServer-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="e3b6a-139">TestServer limitations</span></span>

<span data-ttu-id="e3b6a-140">TestServer:</span><span class="sxs-lookup"><span data-stu-id="e3b6a-140">TestServer:</span></span>

* <span data-ttu-id="e3b6a-141">Wurde erstellt, um Serververhalten zum Testen von Middleware zu replizieren.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-141">Was created to replicate server behaviors to test middleware.</span></span>
* <span data-ttu-id="e3b6a-142">Versucht ***nicht***, alle <xref:System.Net.Http.HttpClient>-Verhalten zu replizieren.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-142">Does ***not*** try to replicate all <xref:System.Net.Http.HttpClient> behaviors.</span></span>
* <span data-ttu-id="e3b6a-143">Es wird versucht, dem Client so viel Kontrolle über den Server wie möglich zu geben, mit weitestgehendem Einblick in die Vorgänge auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-143">Attempts to give the client access to as much control over the server as possible, and with as much visibility into what's happening on the server as possible.</span></span> <span data-ttu-id="e3b6a-144">Beispielsweise können Ausnahmen ausgelöst werden, die normalerweise nicht von `HttpClient` ausgelöst werden, um den Serverzustand direkt zu kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-144">For example it may throw exceptions not normally thrown by `HttpClient` in order to directly communicate server state.</span></span>
* <span data-ttu-id="e3b6a-145">Standardmäßig werden einige transportspezifische Header nicht festgelegt, da diese in der Regel für Middleware nicht relevant sind.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-145">Doesn't set some transport specific headers by default as those are not usually relevant to middleware.</span></span> <span data-ttu-id="e3b6a-146">Weitere Informationen finden Sie im nächsten Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-146">For more information, see the next section.</span></span>

### <a name="content-length-and-transfer-encoding-headers"></a><span data-ttu-id="e3b6a-147">Content-Length- und Transfer-Encoding-Header</span><span class="sxs-lookup"><span data-stu-id="e3b6a-147">Content-Length and Transfer-Encoding headers</span></span>

<span data-ttu-id="e3b6a-148">TestServer legt ***keine*** transportbezogenen Anforderungs- oder Antwortheader wie [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) oder [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding) fest.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-148">TestServer does ***not*** set transport related request or response headers such as [Content-Length](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Length) or [Transfer-Encoding](https://developer.mozilla.org/docs/Web/HTTP/Headers/Transfer-Encoding).</span></span> <span data-ttu-id="e3b6a-149">In Anwendungen sollten Abhängigkeiten von diesen Headern vermieden werden, weil ihre Verwendung je nach Client, Szenario und Protokoll variiert.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-149">Applications should avoid depending on these headers because their usage varies by client, scenario, and protocol.</span></span> <span data-ttu-id="e3b6a-150">Wenn `Content-Length` und `Transfer-Encoding` erforderlich sind, um ein bestimmtes Szenario zu testen, können sie im Test angegeben werden, wenn <xref:System.Net.Http.HttpRequestMessage> oder <xref:Microsoft.AspNetCore.Http.HttpContext> verfasst wird.</span><span class="sxs-lookup"><span data-stu-id="e3b6a-150">If `Content-Length` and `Transfer-Encoding` are necessary to test a specific scenario, they can be specified in the test when composing the <xref:System.Net.Http.HttpRequestMessage> or <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span> <span data-ttu-id="e3b6a-151">Weitere Informationen finden Sie in den folgenden GitHub-Issues:</span><span class="sxs-lookup"><span data-stu-id="e3b6a-151">For more information, see the following GitHub issues:</span></span>

* [<span data-ttu-id="e3b6a-152">dotnet/aspnetcore#21677</span><span class="sxs-lookup"><span data-stu-id="e3b6a-152">dotnet/aspnetcore#21677</span></span>](https://github.com/dotnet/aspnetcore/issues/21677)
* [<span data-ttu-id="e3b6a-153">dotnet/aspnetcore#18463</span><span class="sxs-lookup"><span data-stu-id="e3b6a-153">dotnet/aspnetcore#18463</span></span>](https://github.com/dotnet/aspnetcore/issues/18463)
* [<span data-ttu-id="e3b6a-154">dotnet/aspnetcore#13273</span><span class="sxs-lookup"><span data-stu-id="e3b6a-154">dotnet/aspnetcore#13273</span></span>](https://github.com/dotnet/aspnetcore/issues/13273)
