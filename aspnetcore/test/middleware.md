---
title: Testen von ASP.NET Core-Middleware
author: tratcher
description: Erfahren Sie, wie ASP.NET Core-Middleware mit TestServer testen.
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/middleware
ms.openlocfilehash: 06ff7167e32fbd613c18709e31ecd078b3dfc926
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876422"
---
# <a name="test-aspnet-core-middleware"></a><span data-ttu-id="eb152-103">Testen von ASP.NET Core-Middleware</span><span class="sxs-lookup"><span data-stu-id="eb152-103">Test ASP.NET Core middleware</span></span>

<span data-ttu-id="eb152-104">Von [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="eb152-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="eb152-105">Middleware kann mit <xref:Microsoft.AspNetCore.TestHost.TestServer> isoliert getestet werden.</span><span class="sxs-lookup"><span data-stu-id="eb152-105">Middleware can be tested in isolation with <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span> <span data-ttu-id="eb152-106">Die Funktion ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="eb152-106">It allows you to:</span></span>

* <span data-ttu-id="eb152-107">Instanziieren Sie eine App-Pipeline, die nur die Komponenten enthält, die Sie testen müssen.</span><span class="sxs-lookup"><span data-stu-id="eb152-107">Instantiate an app pipeline containing only the components that you need to test.</span></span>
* <span data-ttu-id="eb152-108">Senden Sie benutzerdefinierte Anforderungen, um das Verhalten der Middleware zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="eb152-108">Send custom requests to verify middleware behavior.</span></span>

<span data-ttu-id="eb152-109">Vorteile:</span><span class="sxs-lookup"><span data-stu-id="eb152-109">Advantages:</span></span>

* <span data-ttu-id="eb152-110">Anforderungen werden arbeitsspeicherintern gesendet und nicht über das Netzwerk serialisiert.</span><span class="sxs-lookup"><span data-stu-id="eb152-110">Requests are sent in-memory rather than being serialized over the network.</span></span>
* <span data-ttu-id="eb152-111">Dadurch werden zusätzliche Aspekte wie Portverwaltung und HTTPS-Zertifikate vermieden.</span><span class="sxs-lookup"><span data-stu-id="eb152-111">This avoids additional concerns, such as port management and HTTPS certificates.</span></span>
* <span data-ttu-id="eb152-112">Ausnahmen in der Middleware können direkt an den aufrufenden Test zurückfließen.</span><span class="sxs-lookup"><span data-stu-id="eb152-112">Exceptions in the middleware can flow directly back to the calling test.</span></span>
* <span data-ttu-id="eb152-113">Es ist möglich, Serverdatenstrukturen, wie z. B. <xref:Microsoft.AspNetCore.Http.HttpContext>, direkt im Test anzupassen.</span><span class="sxs-lookup"><span data-stu-id="eb152-113">It's possible to customize server data structures, such as <xref:Microsoft.AspNetCore.Http.HttpContext>, directly in the test.</span></span>

## <a name="set-up-the-testserver"></a><span data-ttu-id="eb152-114">Einrichten von TestServer</span><span class="sxs-lookup"><span data-stu-id="eb152-114">Set up the TestServer</span></span>

<span data-ttu-id="eb152-115">Erstellen Sie im Testprojekt einen Test:</span><span class="sxs-lookup"><span data-stu-id="eb152-115">In the test project, create a test:</span></span>

* <span data-ttu-id="eb152-116">Erstellen und starten Sie einen Host, der <xref:Microsoft.AspNetCore.TestHost.TestServer> verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb152-116">Build and start a host that uses <xref:Microsoft.AspNetCore.TestHost.TestServer>.</span></span>
* <span data-ttu-id="eb152-117">Fügen Sie alle benötigten Dienste hinzu, die die Middleware verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb152-117">Add any required services that the middleware uses.</span></span>
* <span data-ttu-id="eb152-118">Konfigurieren Sie die Verarbeitungspipeline für die Nutzung der Middleware für den Test.</span><span class="sxs-lookup"><span data-stu-id="eb152-118">Configure the processing pipeline to use the middleware for the test.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/setup.cs?highlight=4-18)]

## <a name="send-requests-with-httpclient"></a><span data-ttu-id="eb152-119">Senden von Anforderungen mit HttpClient</span><span class="sxs-lookup"><span data-stu-id="eb152-119">Send requests with HttpClient</span></span>
<span data-ttu-id="eb152-120">Senden Sie eine Anforderung mit <xref:System.Net.Http.HttpClient>:</span><span class="sxs-lookup"><span data-stu-id="eb152-120">Send a request using <xref:System.Net.Http.HttpClient>:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/request.cs?highlight=20)]

<span data-ttu-id="eb152-121">Bestätigen Sie das Ergebnis.</span><span class="sxs-lookup"><span data-stu-id="eb152-121">Assert the result.</span></span> <span data-ttu-id="eb152-122">Machen Sie zunächst eine Assertion, die das Gegenteil des von Ihnen erwarteten Ergebnisses darstellt.</span><span class="sxs-lookup"><span data-stu-id="eb152-122">First, make an assertion the opposite of the result that you expect.</span></span> <span data-ttu-id="eb152-123">Ein erster Lauf mit einer falsch-positiven Assertion bestätigt, dass der Test fehlschlägt, wenn die Middleware einwandfrei funktioniert.</span><span class="sxs-lookup"><span data-stu-id="eb152-123">An initial run with a false positive assertion confirms that the test fails when the middleware is performing correctly.</span></span> <span data-ttu-id="eb152-124">Führen Sie den Test aus, und bestätigen Sie, dass der Test fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="eb152-124">Run the test and confirm that the test fails.</span></span>

<span data-ttu-id="eb152-125">Im folgenden Beispiel sollte die Middleware den Statuscode 404 (*Nicht gefunden*) zurückgeben, wenn der Stammendpunkt angefordert wird.</span><span class="sxs-lookup"><span data-stu-id="eb152-125">In the following example, the middleware should return a 404 status code (*Not Found*) when the root endpoint is requested.</span></span> <span data-ttu-id="eb152-126">Führen Sie den ersten Testlauf mit `Assert.NotEqual( ... );` durch, der fehlschlagen sollte:</span><span class="sxs-lookup"><span data-stu-id="eb152-126">Make the first test run with `Assert.NotEqual( ... );`, which should fail:</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/false-failure-check.cs?highlight=22)]

<span data-ttu-id="eb152-127">Ändern Sie die Assertion so, dass die Middleware unter normalen Betriebsbedingungen getestet wird.</span><span class="sxs-lookup"><span data-stu-id="eb152-127">Change the assertion to test the middleware under normal operating conditions.</span></span> <span data-ttu-id="eb152-128">Im letzten Test wird `Assert.Equal( ... );` verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb152-128">The final test uses `Assert.Equal( ... );`.</span></span> <span data-ttu-id="eb152-129">Führen Sie den Test erneut aus, um zu bestätigen, dass er bestanden wird.</span><span class="sxs-lookup"><span data-stu-id="eb152-129">Run the test again to confirm that it passes.</span></span>

[!code-csharp[](middleware/samples_snapshot/3.x/final-test.cs?highlight=22)]

## <a name="send-requests-with-httpcontext"></a><span data-ttu-id="eb152-130">Senden von Anforderungen mit HttpContext</span><span class="sxs-lookup"><span data-stu-id="eb152-130">Send requests with HttpContext</span></span>

<span data-ttu-id="eb152-131">Eine Test-App kann eine Anforderung auch mit [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A) senden.</span><span class="sxs-lookup"><span data-stu-id="eb152-131">A test app can also send a request using [SendAsync(Action\<HttpContext>, CancellationToken)](xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A).</span></span> <span data-ttu-id="eb152-132">Im folgenden Beispiel erfolgen mehrere Prüfungen, wenn `https://example.com/A/Path/?and=query` von der Middleware verarbeitet wird:</span><span class="sxs-lookup"><span data-stu-id="eb152-132">In the following example, several checks are made when `https://example.com/A/Path/?and=query` is processed by the middleware:</span></span>

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

<span data-ttu-id="eb152-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> erlaubt die Direktkonfiguration eines <xref:Microsoft.AspNetCore.Http.HttpContext>-Objekts, anstatt die <xref:System.Net.Http.HttpClient>-Abstraktionen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="eb152-133"><xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> permits direct configuration of an <xref:Microsoft.AspNetCore.Http.HttpContext> object rather than using the <xref:System.Net.Http.HttpClient> abstractions.</span></span> <span data-ttu-id="eb152-134">Verwenden Sie <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A>, um Strukturen zu manipulieren, die nur auf dem Server verfügbar sind, wie z. B. [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) oder [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span><span class="sxs-lookup"><span data-stu-id="eb152-134">Use <xref:Microsoft.AspNetCore.TestHost.TestServer.SendAsync%2A> to manipulate structures only available on the server, such as [HttpContext.Items](xref:Microsoft.AspNetCore.Http.HttpContext.Items) or [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features).</span></span>

<span data-ttu-id="eb152-135">Wie im früheren Beispiel, das auf eine Antwort des Typs *404 – nicht gefunden* getestet wurde, überprüfen Sie das Gegenteil für jede `Assert`-Anweisung im vorhergehenden Test.</span><span class="sxs-lookup"><span data-stu-id="eb152-135">As with the earlier example that tested for a *404 - Not Found* response, check the opposite for each `Assert` statement in the preceding test.</span></span> <span data-ttu-id="eb152-136">Die Überprüfung bestätigt, dass der Test bei normalem Betrieb der Middleware ordnungsgemäß fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="eb152-136">The check confirms that the test fails correctly when the middleware is operating normally.</span></span> <span data-ttu-id="eb152-137">Nachdem Sie bestätigt haben, dass der falsch positive Test funktioniert, legen Sie die endgültigen `Assert`-Anweisungen für die erwarteten Bedingungen und Werte des Tests fest.</span><span class="sxs-lookup"><span data-stu-id="eb152-137">After you've confirmed that the false positive test works, set the final `Assert` statements for the expected conditions and values of the test.</span></span> <span data-ttu-id="eb152-138">Führen Sie ihn erneut aus, um zu bestätigen, dass er bestanden wird.</span><span class="sxs-lookup"><span data-stu-id="eb152-138">Run it again to confirm that the test passes.</span></span>
