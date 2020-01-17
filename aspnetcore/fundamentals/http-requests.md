---
title: Stellen von HTTP-Anforderungen mithilfe von IHttpClientFactory in ASP.NET Core
author: stevejgordon
description: Erfahren Sie mehr über die Verwendung der IHttpClientFactory-Schnittstelle, um logische HttpClient-Instanzen in ASP.NET Core zu verwalten.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
uid: fundamentals/http-requests
ms.openlocfilehash: 482f8e28c23c621cecaf9ce111d89e9166ea6d85
ms.sourcegitcommit: da2fb2d78ce70accdba903ccbfdcfffdd0112123
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722725"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="6a88a-103">Stellen von HTTP-Anforderungen mithilfe von IHttpClientFactory in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a88a-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6a88a-104">Von [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="6a88a-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="6a88a-105"><xref:System.Net.Http.IHttpClientFactory> kann registriert und zum Konfigurieren und Erstellen von <xref:System.Net.Http.HttpClient>-Instanzen in einer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="6a88a-106">`IHttpClientFactory` bietet die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="6a88a-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="6a88a-107">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="6a88a-108">Ein Client namens *github* kann beispielsweise registriert und für den Zugriff auf [GitHub](https://github.com/) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="6a88a-109">Ein Standardclient kann für den allgemeinen Zugriff registriert werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="6a88a-110">Das Konzept der ausgehenden Middleware wird über delegierende Handler in `HttpClient` in Code umgesetzt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="6a88a-111">Außerdem werden Erweiterungen für auf Polly basierende Middleware bereitgestellt, um die delegierenden Handler in `HttpClient` zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="6a88a-112">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="6a88a-113">Durch die automatische Verwaltung werden allgemeine DNS-Probleme (Domain Name System) vermieden, die bei der manuellen Verwaltung der Lebensdauer von `HttpClient` auftreten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="6a88a-114">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="6a88a-115">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="6a88a-115">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="6a88a-116">Der Beispielcode in dieser Version des Themas verwendet <xref:System.Text.Json>, um JSON-Inhalte zu deserialisieren, die in HTTP-Antworten zurückgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="6a88a-117">Verwenden Sie bei Beispielen, die `Json.NET` und `ReadAsAsync<T>` verwenden, die Versionsauswahl, um eine 2.x-Version dieses Themas auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="6a88a-118">Verbrauchsmuster</span><span class="sxs-lookup"><span data-stu-id="6a88a-118">Consumption patterns</span></span>

<span data-ttu-id="6a88a-119">Es gibt mehrere Möglichkeiten `IHttpClientFactory` in einer App zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="6a88a-120">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="6a88a-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="6a88a-121">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="6a88a-122">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="6a88a-123">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="6a88a-124">Der beste Ansatz richtet sich nach den Anforderungen der App.</span><span class="sxs-lookup"><span data-stu-id="6a88a-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="6a88a-125">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="6a88a-125">Basic usage</span></span>

<span data-ttu-id="6a88a-126">`IHttpClientFactory` kann durch Aufrufen von `AddHttpClient` registriert werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="6a88a-127">Eine `IHttpClientFactory`-Schnittstelle kann mithilfe der [Dependency Injection (DI)](xref:fundamentals/dependency-injection) angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6a88a-128">Im folgenden Code wird `IHttpClientFactory` verwendet, um eine `HttpClient`-Instanz zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="6a88a-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="6a88a-129">`IHttpClientFactory` wie im vorhergehenden Beispiel zu verwenden ist eine gute Möglichkeit zum Umgestalten einer vorhandene App.</span><span class="sxs-lookup"><span data-stu-id="6a88a-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="6a88a-130">Dies hat keine Auswirkung auf die Verwendung von `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="6a88a-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="6a88a-131">An Stellen, an denen `HttpClient`-Instanzen in einer vorhandenen App erstellt werden, können Sie diese Ereignisse mit Aufrufen von <xref:System.Net.Http.IHttpClientFactory.CreateClient*> ersetzen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="6a88a-132">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-132">Named clients</span></span>

<span data-ttu-id="6a88a-133">Benannte Clients sind in folgenden Fällen eine gute Wahl:</span><span class="sxs-lookup"><span data-stu-id="6a88a-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="6a88a-134">Die App erfordert viele verschiedene Verwendungen von `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="6a88a-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="6a88a-135">Viele `HttpClient`s verfügen über unterschiedliche Konfigurationen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="6a88a-136">Die Konfiguration eines benannten `HttpClient` kann während der Registrierung in `Startup.ConfigureServices` angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="6a88a-137">Im vorangehenden Code wird der Client mit Folgendem konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="6a88a-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="6a88a-138">der Basisadresse `https://api.github.com/`</span><span class="sxs-lookup"><span data-stu-id="6a88a-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="6a88a-139">zwei Header, die für die Arbeit mit der GitHub-API erforderlich sind</span><span class="sxs-lookup"><span data-stu-id="6a88a-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="6a88a-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="6a88a-140">CreateClient</span></span>

<span data-ttu-id="6a88a-141">Jedes Mal, wenn <xref:System.Net.Http.IHttpClientFactory.CreateClient*> aufgerufen wird, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6a88a-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="6a88a-142">Eine neue Instanz von `HttpClient` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="6a88a-143">Die Konfigurationsaktion wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-143">The configuration action is called.</span></span>

<span data-ttu-id="6a88a-144">Übergeben Sie für die Erstellung eines benannten Clients dessen Namen an `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="6a88a-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="6a88a-145">Im vorangehenden Code muss die Anforderung keinen Hostnamen angeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="6a88a-146">Der Code muss nur den Pfad übergeben, da die für den Client konfigurierte Basisadresse verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="6a88a-147">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-147">Typed clients</span></span>

<span data-ttu-id="6a88a-148">Typisierte Clients:</span><span class="sxs-lookup"><span data-stu-id="6a88a-148">Typed clients:</span></span>

* <span data-ttu-id="6a88a-149">Stellen dieselben Funktionen wie benannte Clients bereit, ohne Zeichenfolgen als Schlüssel verwenden zu müssen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="6a88a-150">Bieten Hilfe durch IntelliSense und Compiler beim Verarbeiten von Clients.</span><span class="sxs-lookup"><span data-stu-id="6a88a-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="6a88a-151">Stellen einen einzelnen Ort zum Konfigurieren und Interagieren mit einem bestimmten `HttpClient` bereit.</span><span class="sxs-lookup"><span data-stu-id="6a88a-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="6a88a-152">Beispielsweise kann ein einzelner typisierter Client für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="6a88a-153">für einen einzelnen Back-End-Endpunkt</span><span class="sxs-lookup"><span data-stu-id="6a88a-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="6a88a-154">um die gesamte Logik zu kapseln, die den Endpunkt behandelt</span><span class="sxs-lookup"><span data-stu-id="6a88a-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="6a88a-155">Funktionieren mit DI und können überall in der App eingefügt werden, wo sie benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="6a88a-156">Ein typisierter Client akzeptiert einen `HttpClient`-Parameter in seinem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="6a88a-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="6a88a-157">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="6a88a-157">In the preceding code:</span></span>

* <span data-ttu-id="6a88a-158">Die Konfiguration wird in den typisierten Client verschoben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="6a88a-159">Das `HttpClient`-Objekt wird als öffentliche Eigenschaft zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="6a88a-160">Es können API-spezifische Methoden erstellt werden, die die `HttpClient`-Funktionalität verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="6a88a-161">Die `GetAspNetDocsIssues`-Methode kapselt z. B. Code zum Abrufen offener Probleme.</span><span class="sxs-lookup"><span data-stu-id="6a88a-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="6a88a-162">Der folgende Code ruft <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` auf, um eine typisierte Clientklasse zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="6a88a-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="6a88a-163">Der typisierte Client wird mit DI als „vorübergehend“ registriert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="6a88a-164">Der typisierte Client kann direkt eingefügt und verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-164">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="6a88a-165">Die Konfiguration kann für einen typisierten Client während der Registrierung in `Startup.ConfigureServices` angegeben werden, anstatt im Konstruktor des typisierten Clients:</span><span class="sxs-lookup"><span data-stu-id="6a88a-165">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="6a88a-166">`HttpClient` kann in einem typisierten Client gekapselt werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-166">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="6a88a-167">Anstatt ihn als eine Eigenschaft zur Verfügung zu stellen, definieren Sie eine Methode, die die `HttpClient`-Instanz intern aufruft:</span><span class="sxs-lookup"><span data-stu-id="6a88a-167">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="6a88a-168">Im vorangehenden Code wird `HttpClient` in einem privaten Feld gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-168">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="6a88a-169">Der Zugriff auf `HttpClient` erfolgt durch die öffentliche `GetRepos`-Methode.</span><span class="sxs-lookup"><span data-stu-id="6a88a-169">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="6a88a-170">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-170">Generated clients</span></span>

<span data-ttu-id="6a88a-171">`IHttpClientFactory` kann in Verbindung mit Bibliotheken von Drittanbietern verwendet werden, z. B. [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="6a88a-171">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="6a88a-172">Refit ist eine REST-Bibliothek für .NET.</span><span class="sxs-lookup"><span data-stu-id="6a88a-172">Refit is a REST library for .NET.</span></span> <span data-ttu-id="6a88a-173">Sie konvertiert REST-APIs in Live-Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-173">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="6a88a-174">Eine Implementierung der Schnittstelle wird dynamisch von `RestService` generiert. `HttpClient` wird für die externen HTTP-Aufrufe verwendet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-174">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="6a88a-175">Eine Schnittstelle und eine Antwort werden definiert, um die externe API und die Antwort darzustellen:</span><span class="sxs-lookup"><span data-stu-id="6a88a-175">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="6a88a-176">Ein typisierter Client kann hinzugefügt werden. Refit wird zum Generieren der Implementierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="6a88a-176">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddControllers();
}
```

<span data-ttu-id="6a88a-177">Die definierte Schnittstelle kann bei Bedarf mit der von DI und Refit bereitgestellten Implementierung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-177">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="6a88a-178">Middleware für ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="6a88a-178">Outgoing request middleware</span></span>

<span data-ttu-id="6a88a-179">`HttpClient` enthält das Konzept, Handler zu delegieren, die für ausgehende HTTP-Anforderungen miteinander verknüpft werden können.</span><span class="sxs-lookup"><span data-stu-id="6a88a-179">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="6a88a-180">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="6a88a-180">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="6a88a-181">Erleichtert das Definieren der Handler, die für jeden benannten Client angewendet werden</span><span class="sxs-lookup"><span data-stu-id="6a88a-181">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="6a88a-182">Unterstützt die Registrierung und Verkettung von mehreren Handlern, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-182">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="6a88a-183">Jeder dieser Handler kann vor und nach der ausgehenden Anforderung Aufgaben ausführen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-183">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="6a88a-184">Dieses Muster:</span><span class="sxs-lookup"><span data-stu-id="6a88a-184">This pattern:</span></span>

  * <span data-ttu-id="6a88a-185">ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6a88a-185">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="6a88a-186">bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, z. B.:</span><span class="sxs-lookup"><span data-stu-id="6a88a-186">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="6a88a-187">Zwischenspeicherung</span><span class="sxs-lookup"><span data-stu-id="6a88a-187">caching</span></span>
    * <span data-ttu-id="6a88a-188">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="6a88a-188">error handling</span></span>
    * <span data-ttu-id="6a88a-189">Serialisierung</span><span class="sxs-lookup"><span data-stu-id="6a88a-189">serialization</span></span>
    * <span data-ttu-id="6a88a-190">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="6a88a-190">logging</span></span>

<span data-ttu-id="6a88a-191">So erstellen Sie einen delegierenden Handler:</span><span class="sxs-lookup"><span data-stu-id="6a88a-191">To create a delegating handler:</span></span>

* <span data-ttu-id="6a88a-192">Leiten Sie von <xref:System.Net.Http.DelegatingHandler> ab.</span><span class="sxs-lookup"><span data-stu-id="6a88a-192">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="6a88a-193">Überschreiben Sie <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="6a88a-193">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="6a88a-194">Führen Sie Code aus, bevor die Anforderung an den nächsten Handler in der Pipeline übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="6a88a-194">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="6a88a-195">Der vorangehende Code überprüft, ob die Anforderung einen `X-API-KEY`-Header enthält.</span><span class="sxs-lookup"><span data-stu-id="6a88a-195">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="6a88a-196">Wenn `X-API-KEY` fehlt, wird <xref:System.Net.HttpStatusCode.BadRequest> zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-196">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="6a88a-197">Für eine `HttpClient`-Klasse mit <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> kann mehr als ein Handler zur Konfiguration hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-197">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="6a88a-198">Im vorangehenden Code ist `ValidateHeaderHandler` mit DI registriert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-198">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="6a88a-199">Die `IHttpClientFactory` erstellt einen separaten DI-Bereich für jeden Handler.</span><span class="sxs-lookup"><span data-stu-id="6a88a-199">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="6a88a-200">Handler können von Diensten eines beliebigen Bereichs abhängen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-200">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="6a88a-201">Dienste, von denen Handler abhängig sind, werden verworfen, wenn der Handler verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-201">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="6a88a-202">Nach der Registrierung kann <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> aufgerufen werden, was den Typ für den Handler übergibt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-202">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="6a88a-203">Mehrere Handler können in der Reihenfolge registriert werden, in der sie ausgeführt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-203">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="6a88a-204">Jeder Handler umschließt den nächsten Handler, bis der endgültige `HttpClientHandler` die Anforderung ausführt:</span><span class="sxs-lookup"><span data-stu-id="6a88a-204">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="6a88a-205">Verwenden Sie einen der folgenden Ansätze, um den anforderungsspezifischen Zustand mit Meldungshandlern zu teilen:</span><span class="sxs-lookup"><span data-stu-id="6a88a-205">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="6a88a-206">Übergeben Sie mithilfe von [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties) Daten an den Handler.</span><span class="sxs-lookup"><span data-stu-id="6a88a-206">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="6a88a-207">Greifen Sie mit <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> auf die aktuelle Anforderung zu.</span><span class="sxs-lookup"><span data-stu-id="6a88a-207">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="6a88a-208">Erstellen Sie ein benutzerdefiniertes <xref:System.Threading.AsyncLocal`1>-Speicherobjekt, um die Daten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-208">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="6a88a-209">Verwenden von Polly-basierten Handlern</span><span class="sxs-lookup"><span data-stu-id="6a88a-209">Use Polly-based handlers</span></span>

<span data-ttu-id="6a88a-210">`IHttpClientFactory` ist mit der Drittanbieterbibliothek [Polly](https://github.com/App-vNext/Polly) integriert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-210">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="6a88a-211">Polly ist eine umfassende Bibliothek für die Behandlung von beständigen und vorübergehenden Fehlern für .NET.</span><span class="sxs-lookup"><span data-stu-id="6a88a-211">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="6a88a-212">Entwicklern wird damit ermöglicht, Richtlinien wie Wiederholungsrichtlinien, Trennschalterrichtlinien, Timeout-Richtlinien, Bulkhead Isolation-Richtlinien und Ausweichrichtlinien in einer flüssigen und threadsicheren Art und Weise auszudrücken.</span><span class="sxs-lookup"><span data-stu-id="6a88a-212">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="6a88a-213">Erweiterungsmethoden werden bereitgestellt, um die Verwendung von Polly-Richtlinien mit konfigurierten `HttpClient`-Instanzen zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="6a88a-213">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="6a88a-214">Die Polly-Erweiterungen unterstützen das Hinzufügen von Polly-basierten Handlern zu Clients.</span><span class="sxs-lookup"><span data-stu-id="6a88a-214">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="6a88a-215">Polly erfordert das [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/)-NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="6a88a-215">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="6a88a-216">Behandeln von vorrübergehenden Fehlern</span><span class="sxs-lookup"><span data-stu-id="6a88a-216">Handle transient faults</span></span>

<span data-ttu-id="6a88a-217">Fehler treten normalerweise auf, wenn externe HTTP-Aufrufe vorübergehend sind.</span><span class="sxs-lookup"><span data-stu-id="6a88a-217">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="6a88a-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> ermöglicht die Definition einer Richtlinie, um vorübergehende Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="6a88a-218"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="6a88a-219">Mit `AddTransientHttpErrorPolicy` konfigurierte Richtlinien behandeln die folgenden Antworten:</span><span class="sxs-lookup"><span data-stu-id="6a88a-219">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="6a88a-220">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="6a88a-220">HTTP 5xx</span></span>
* <span data-ttu-id="6a88a-221">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="6a88a-221">HTTP 408</span></span>

<span data-ttu-id="6a88a-222">`AddTransientHttpErrorPolicy` ermöglicht den Zugriff auf ein `PolicyBuilder`-Objekt, das für die Fehlerbehandlung konfiguriert wurde und mögliche vorübergehende Fehler darstellt:</span><span class="sxs-lookup"><span data-stu-id="6a88a-222">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="6a88a-223">Im vorangehenden Code wird eine `WaitAndRetryAsync`-Richtlinie definiert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-223">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="6a88a-224">Anforderungsfehler werden bis zu dreimal mit einer Verzögerung von 600 ms wiederholt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-224">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="6a88a-225">Dynamisches Auswählen von Richtlinien</span><span class="sxs-lookup"><span data-stu-id="6a88a-225">Dynamically select policies</span></span>

<span data-ttu-id="6a88a-226">Erweiterungsmethoden werden zum Hinzufügen von Polly-basierten Handlern bereitgestellt, z. B. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="6a88a-226">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="6a88a-227">Der folgende `AddPolicyHandler`-Überladung prüft die Anforderung, um zu entscheiden, welche Richtlinie angewendet werden soll:</span><span class="sxs-lookup"><span data-stu-id="6a88a-227">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="6a88a-228">Im vorangehenden Code wird ein 10 Sekunden langer Timeout angewendet, wenn die ausgehende Anforderung eine HTTP GET-Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="6a88a-228">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="6a88a-229">Für alle anderen HTTP-Methoden wird ein 30 Sekunden langer Timeout verwendet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-229">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="6a88a-230">Hinzufügen mehrerer Polly-Handler</span><span class="sxs-lookup"><span data-stu-id="6a88a-230">Add multiple Polly handlers</span></span>

<span data-ttu-id="6a88a-231">Es ist üblich, Polly-Richtlinien zu schachteln:</span><span class="sxs-lookup"><span data-stu-id="6a88a-231">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="6a88a-232">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6a88a-232">In the preceding example:</span></span>

* <span data-ttu-id="6a88a-233">Zwei Handler werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-233">Two handlers are added.</span></span>
* <span data-ttu-id="6a88a-234">Der erste Handler verwendet <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>, um eine Wiederholungsrichtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-234">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="6a88a-235">Fehlgeschlagene Anforderungen werden bis zu drei Mal wiederholt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-235">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="6a88a-236">Der zweite `AddTransientHttpErrorPolicy`-Aufruf fügt eine Trennschalterrichtlinie hinzu.</span><span class="sxs-lookup"><span data-stu-id="6a88a-236">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="6a88a-237">Weitere externe Anforderungen werden 30 Sekunden lang blockiert, wenn fünf Fehlversuche hintereinander stattfinden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-237">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="6a88a-238">Trennschalterrichtlinien sind zustandsbehaftet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-238">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="6a88a-239">Alle Aufrufe über diesen Client teilen den gleichen Schalterzustand.</span><span class="sxs-lookup"><span data-stu-id="6a88a-239">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="6a88a-240">Hinzufügen von Richtlinien aus der Polly-Registrierung</span><span class="sxs-lookup"><span data-stu-id="6a88a-240">Add policies from the Polly registry</span></span>

<span data-ttu-id="6a88a-241">Eine Methode zum Verwalten regelmäßig genutzter Richtlinien ist, Sie einmal zu definieren und mit `PolicyRegistry` zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="6a88a-241">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="6a88a-242">Im folgenden Code wird Folgendes ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="6a88a-242">In the following code:</span></span>

* <span data-ttu-id="6a88a-243">Die Richtlinien "regular" und "long" werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-243">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="6a88a-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> fügt die Richtlinien "regular" und "long" aus der Registrierung hinzu.</span><span class="sxs-lookup"><span data-stu-id="6a88a-244"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="6a88a-245">Weitere Informationen zu `IHttpClientFactory` und Polly-Integrationen finden Sie im [Polly-Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="6a88a-245">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="6a88a-246">HttpClient und die Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="6a88a-246">HttpClient and lifetime management</span></span>

<span data-ttu-id="6a88a-247">Bei jedem Aufruf von `CreateClient` in der `IHttpClientFactory` wird eine neue Instanz von `HttpClient` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-247">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="6a88a-248">Pro benannter Client wird ein <xref:System.Net.Http.HttpMessageHandler> erstellt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-248">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="6a88a-249">Die Factory verwaltet die Lebensdauer der `HttpMessageHandler`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-249">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="6a88a-250">`IHttpClientFactory` legt die `HttpMessageHandler`-Instanzen zusammen, die von der Factory zum Reduzieren des Ressourcenverbrauchs erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-250">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="6a88a-251">Eine `HttpMessageHandler`-Instanz kann aus dem Pool wiederverwendet werden, wenn eine neue `HttpClient`-Instanz erstellt wird und deren Lebensdauer noch nicht abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="6a88a-251">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="6a88a-252">Das Zusammenlegen von Handlern ist ein wünschenswerter Vorgang, da jeder Handler in der Regel seine zugrunde liegenden HTTP-Verbindungen selbst verwaltet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-252">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="6a88a-253">Wenn mehr Handler als nötig erstellt werden, können Verzögerungen bei Verbindungen entstehen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-253">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="6a88a-254">Einige Handler halten Verbindungen auch unbegrenzt offen, was verhindert, dass der Handler auf DNS-Änderungen (Domain Name System) reagiert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-254">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="6a88a-255">Die Standardlebensdauer von Handlern beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-255">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="6a88a-256">Der Standardwert kann für jeden benannten Client überschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-256">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="6a88a-257">`HttpClient`-Instanzen können im Allgemeinen als .NET-Objekte behandelt werden, die **nicht** verworfen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-257">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="6a88a-258">Beim Verwerfen werden ausgehende Anforderungen abgebrochen, und es wird sichergestellt, dass die angegebene `HttpClient`-Instanz nach dem Aufruf von <xref:System.IDisposable.Dispose*> nicht mehr verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="6a88a-258">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="6a88a-259">`IHttpClientFactory` verfolgt von `HttpClient`-Instanzen verwendete Ressourcen nach und verwirft sie.</span><span class="sxs-lookup"><span data-stu-id="6a88a-259">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="6a88a-260">Das Beibehalten einer einzelnen `HttpClient`-Instanz für einen langen Zeitraum ist ein allgemeines Muster, das vor der Einführung von `IHttpClientFactory` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="6a88a-260">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="6a88a-261">Dieses Muster wird nach der Migration zu `IHttpClientFactory` überflüssig.</span><span class="sxs-lookup"><span data-stu-id="6a88a-261">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="6a88a-262">Alternativen zu IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="6a88a-262">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="6a88a-263">Mit der Verwendung von `IHttpClientFactory` in einer DI-fähigen App wird Folgendes vermieden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-263">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="6a88a-264">Probleme mit der Ressourcenauslastung durch Zusammenlegen von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="6a88a-264">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="6a88a-265">Probleme durch veraltetes DNS durch regelmäßigen Wechsel von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="6a88a-265">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="6a88a-266">Es gibt alternative Möglichkeiten zum Lösen des vorangehenden Problems mithilfe einer langlebigen <xref:System.Net.Http.SocketsHttpHandler>-Instanz:</span><span class="sxs-lookup"><span data-stu-id="6a88a-266">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="6a88a-267">Erstellen Sie eine `SocketsHttpHandler`-Instanz, wenn die App gestartet wird, und verwenden Sie diese für die Lebensdauer der App.</span><span class="sxs-lookup"><span data-stu-id="6a88a-267">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="6a88a-268">Konfigurieren Sie einen geeigneten Wert für <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> basierend auf den DNS-Aktualisierungszeiten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-268">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="6a88a-269">Erstellen Sie bei Bedarf `HttpClient`-Instanzen mithilfe von `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="6a88a-269">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="6a88a-270">Diese Ansätze lösen die Ressourcenverwaltungsprobleme, die `IHttpClientFactory` auf ähnliche Weise löst.</span><span class="sxs-lookup"><span data-stu-id="6a88a-270">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="6a88a-271">`SocketsHttpHandler` gibt Verbindungen für `HttpClient`-Instanzen frei.</span><span class="sxs-lookup"><span data-stu-id="6a88a-271">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="6a88a-272">Durch diese Freigabe wird die Erschöpfung von Sockets vermieden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-272">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="6a88a-273">`SocketsHttpHandler` wechselt die Verbindungen anhand von `PooledConnectionLifetime`, um Probleme durch veraltetes DNS zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-273">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="6a88a-274">Cookies</span><span class="sxs-lookup"><span data-stu-id="6a88a-274">Cookies</span></span>

<span data-ttu-id="6a88a-275">Die zusammengelegten `HttpMessageHandler`-Instanzen resultieren in der Freigabe von `CookieContainer`-Objekten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-275">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="6a88a-276">Die unerwartete Freigabe von `CookieContainer`-Objekten führt oft zu fehlerhaftem Code.</span><span class="sxs-lookup"><span data-stu-id="6a88a-276">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="6a88a-277">Ziehen Sie für Apps, die Cookies erfordern, folgende Vorgehensweisen in Betracht:</span><span class="sxs-lookup"><span data-stu-id="6a88a-277">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="6a88a-278">Deaktivieren der automatischen Cookieverarbeitung</span><span class="sxs-lookup"><span data-stu-id="6a88a-278">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="6a88a-279">Vermeiden der Verwendung von `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="6a88a-279">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="6a88a-280">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> auf, um die automatische Cookieverarbeitung zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="6a88a-280">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="6a88a-281">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="6a88a-281">Logging</span></span>

<span data-ttu-id="6a88a-282">Über `IHttpClientFactory` erstellte Clients zeichnen Protokollmeldungen für alle Anforderungen auf.</span><span class="sxs-lookup"><span data-stu-id="6a88a-282">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="6a88a-283">Aktivieren Sie die entsprechende Informationsebene in der Protokollierungskonfiguration, um die Standardprotokollmeldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-283">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="6a88a-284">Zusätzliche Protokollierung, z.B. das Protokollieren von Anforderungsheadern, wird nur auf der Ablaufverfolgungsebene enthalten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-284">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="6a88a-285">Die Protokollierungskategorie für jeden Client enthält den Namen des Clients.</span><span class="sxs-lookup"><span data-stu-id="6a88a-285">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="6a88a-286">Ein Client namens *MyNamedClient* protokolliert z. B. Nachrichten mit der Kategorie „System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler“.</span><span class="sxs-lookup"><span data-stu-id="6a88a-286">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="6a88a-287">Meldungen mit dem Suffix *LogicalHandler* treten außerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="6a88a-287">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="6a88a-288">In der Anforderung werden Meldungen protokolliert, bevor andere Handler in der Pipeline sie verarbeitet haben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-288">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="6a88a-289">In der Antwort werden Meldungen protokolliert, nachdem andere Handler in der Pipeline die Antwort empfangen haben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-289">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="6a88a-290">Die Protokollierung tritt ebenfalls innerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="6a88a-290">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="6a88a-291">Im Beispiel von *MyNamedClient* werden diese Nachrichten mit der Protokollkategorie „System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler“ protokolliert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-291">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="6a88a-292">Für die Anforderung erfolgt dies, nachdem alle anderen Handler ausgeführt wurden und bevor die Anforderung gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-292">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="6a88a-293">Diese Protokollierung enthält den Zustand der Antwort in der Antwort, bevor sie an die Handlerpipeline zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-293">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="6a88a-294">Das Aktivieren der Protokollierung außerhalb und innerhalb der Pipeline ermöglicht die Überprüfung der Änderungen, die durch andere Handler in der Pipeline erfolgt sind.</span><span class="sxs-lookup"><span data-stu-id="6a88a-294">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="6a88a-295">Dies kann die Änderungen an Anforderungsheadern oder am Statuscode der Antwort enthalten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-295">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="6a88a-296">Das Einschließen des Namens des Clients in der Protokollkategorie aktiviert die Protokollfilterung für spezifische benannte Clients.</span><span class="sxs-lookup"><span data-stu-id="6a88a-296">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="6a88a-297">Konfigurieren von HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="6a88a-297">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="6a88a-298">Es kann notwendig sein, die Konfiguration des inneren von einem Client verwendeten `HttpMessageHandler` zu steuern.</span><span class="sxs-lookup"><span data-stu-id="6a88a-298">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="6a88a-299">`IHttpClientBuilder` wird zurückgegeben, wenn benannte oder typisierte Clients hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-299">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="6a88a-300">Die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> kann zum Definieren eines Delegaten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-300">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="6a88a-301">Der Delegat wird verwendet, um den primären `HttpMessageHandler` zu erstellen und konfigurieren, der von dem Client verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="6a88a-301">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="6a88a-302">Verwenden von IHttpClientFactory in einer Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="6a88a-302">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="6a88a-303">Fügen Sie in einer Konsolen-App dem Projekt die folgenden Paketverweise hinzu:</span><span class="sxs-lookup"><span data-stu-id="6a88a-303">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="6a88a-304">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="6a88a-304">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="6a88a-305">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="6a88a-305">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="6a88a-306">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6a88a-306">In the following example:</span></span>

* <span data-ttu-id="6a88a-307"><xref:System.Net.Http.IHttpClientFactory> ist im Dienstcontainer des [generischen Hosts](xref:fundamentals/host/generic-host) registriert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-307"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="6a88a-308">`MyService` erstellt eine Clientfactoryinstanz aus dem-Dienst, der zum Erstellen eines `HttpClient`verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-308">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="6a88a-309">`HttpClient` wird zum Abrufen einer Webseite verwendet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-309">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="6a88a-310">`Main` erstellt einen Bereich, um die `GetPage`-Methode des Diensts auszuführen und die ersten 500 Zeichen des Webseiteninhalts in die Konsole zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-310">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="6a88a-311">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6a88a-311">Additional resources</span></span>

* [<span data-ttu-id="6a88a-312">Verwenden von HttpClientFactory zur Implementierung robuster HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="6a88a-312">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="6a88a-313">Implementieren von Wiederholungen von HTTP-Aufrufen mit exponentiellem Backoff mit HttpClientFactory und Polly-Richtlinien</span><span class="sxs-lookup"><span data-stu-id="6a88a-313">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="6a88a-314">Implementieren des Trennschaltermusters</span><span class="sxs-lookup"><span data-stu-id="6a88a-314">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="6a88a-315">Serialisieren und Deserialisieren von JSON-Daten in .NET</span><span class="sxs-lookup"><span data-stu-id="6a88a-315">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6a88a-316">Von [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), und [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="6a88a-316">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="6a88a-317"><xref:System.Net.Http.IHttpClientFactory> kann registriert und zum Konfigurieren und Erstellen von <xref:System.Net.Http.HttpClient>-Instanzen in einer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-317">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="6a88a-318">Dies hat folgende Vorteile:</span><span class="sxs-lookup"><span data-stu-id="6a88a-318">It offers the following benefits:</span></span>

* <span data-ttu-id="6a88a-319">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-319">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="6a88a-320">Ein *GitHub*-Client kann beispielsweise registriert werden und so konfiguriert werden, um auf [GitHub](https://github.com/) zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-320">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="6a88a-321">Ein Standard-Client kann für andere Zwecke registriert werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-321">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="6a88a-322">Das Konzept der ausgehenden Middleware wird über delegierende Handler in `HttpClient` in Code umgesetzt. Außerdem werden Erweiterungen für auf Polly basierende Middleware bereitgestellt, die dies nutzen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-322">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="6a88a-323">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-323">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="6a88a-324">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-324">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="6a88a-325">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6a88a-325">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="6a88a-326">Verbrauchsmuster</span><span class="sxs-lookup"><span data-stu-id="6a88a-326">Consumption patterns</span></span>

<span data-ttu-id="6a88a-327">Es gibt mehrere Möglichkeiten `IHttpClientFactory` in einer App zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-327">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="6a88a-328">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="6a88a-328">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="6a88a-329">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-329">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="6a88a-330">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-330">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="6a88a-331">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-331">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="6a88a-332">Keine dieser Möglichkeiten ist einer anderen überlegen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-332">None of them are strictly superior to another.</span></span> <span data-ttu-id="6a88a-333">Der beste Ansatz richtet sich nach den Einschränkungen der App.</span><span class="sxs-lookup"><span data-stu-id="6a88a-333">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="6a88a-334">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="6a88a-334">Basic usage</span></span>

<span data-ttu-id="6a88a-335">`IHttpClientFactory` kann durch Aufrufen der Erweiterungsmethode `AddHttpClient` auf `IServiceCollection` in der Methode `Startup.ConfigureServices` registriert werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-335">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="6a88a-336">Nach der Registrierung kann Code `IHttpClientFactory` überall akzeptieren, wo Dienste mithilfe der [Dependency Injection](xref:fundamentals/dependency-injection) (DI) eingefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="6a88a-336">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6a88a-337">`IHttpClientFactory` kann zum Erstellen einer `HttpClient`-Instanz verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-337">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="6a88a-338">`IHttpClientFactory` auf diese Weise zu verwenden, ist eine gute Möglichkeit zum Umgestalten einer vorhandenen App.</span><span class="sxs-lookup"><span data-stu-id="6a88a-338">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="6a88a-339">Dies hat keine Auswirkung auf die Verwendung von `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="6a88a-339">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="6a88a-340">An Stellen, in denen `HttpClient`-Instanzen derzeit erstellt werden, können Sie diese Ereignisse mit einem Aufruf von <xref:System.Net.Http.IHttpClientFactory.CreateClient*> ersetzen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-340">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="6a88a-341">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-341">Named clients</span></span>

<span data-ttu-id="6a88a-342">Wenn eine App mehrere verschiedene Verwendungen von `HttpClient` mit jeweils unterschiedlicher Konfiguration erfordert, ist die Verwendung von **benannten Clients** eine Option.</span><span class="sxs-lookup"><span data-stu-id="6a88a-342">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="6a88a-343">Die Konfiguration einer benannten `HttpClient` kann während der Registrierung in `Startup.ConfigureServices` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-343">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="6a88a-344">Im oben stehenden Code wird `AddHttpClient` aufgerufen, und dabei wird der Name *github* angegeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-344">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="6a88a-345">Für diesen Client wird Standardkonfiguration angewendet, d.h. die Basisadresse und zwei Header, die erforderlich sind, um mit der GitHub-API zu funktionieren.</span><span class="sxs-lookup"><span data-stu-id="6a88a-345">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="6a88a-346">Wenn `CreateClient` aufgerufen wird, wird jedes Mal eine neue Instanz von `HttpClient` erstellt und die Konfigurationsaktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-346">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="6a88a-347">Zum Verarbeiten eines benannten Clients kann ein Zeichenfolgenparameter an `CreateClient` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-347">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="6a88a-348">Geben Sie den Namen des zu erstellenden Clients an:</span><span class="sxs-lookup"><span data-stu-id="6a88a-348">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="6a88a-349">Im vorangehenden Code muss die Anforderung keinen Hostnamen angeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-349">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="6a88a-350">Sie muss nur den Pfad übergeben, da die für den Client konfigurierte Basisadresse verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-350">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="6a88a-351">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-351">Typed clients</span></span>

<span data-ttu-id="6a88a-352">Typisierte Clients:</span><span class="sxs-lookup"><span data-stu-id="6a88a-352">Typed clients:</span></span>

* <span data-ttu-id="6a88a-353">Stellen dieselben Funktionen wie benannte Clients bereit, ohne Zeichenfolgen als Schlüssel verwenden zu müssen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-353">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="6a88a-354">Bieten Hilfe durch IntelliSense und Compiler beim Verarbeiten von Clients.</span><span class="sxs-lookup"><span data-stu-id="6a88a-354">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="6a88a-355">Stellen einen einzelnen Ort zum Konfigurieren und Interagieren mit einem bestimmten `HttpClient` bereit.</span><span class="sxs-lookup"><span data-stu-id="6a88a-355">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="6a88a-356">Zum Beispiel kann ein einzelner typisierter Client für einen einzelnen Back-End-Endpunkt verwendet werden, der jegliche Logik kapselt, die mit diesem Endpunkt interagiert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-356">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="6a88a-357">Funktionieren mit DI und können überall in Ihrer App eingefügt werden, wo sie benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-357">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="6a88a-358">Ein typisierter Client akzeptiert einen `HttpClient`-Parameter in seinem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="6a88a-358">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="6a88a-359">Im vorangehenden Code wird die Konfiguration in den typisierten Client verschoben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-359">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="6a88a-360">Das `HttpClient`-Objekt wird als öffentliche Eigenschaft zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-360">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="6a88a-361">Es ist möglich, API-spezifische Methoden zu definieren, die die `HttpClient`-Funktionalität zur Verfügung stellen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-361">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="6a88a-362">Die Methode `GetAspNetDocsIssues` kapselt den Code, der für die Abfrage und Analyse des neuesten offenen Problems aus dem GitHub-Repository erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="6a88a-362">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="6a88a-363">Zum Registrieren eines typisierten Clients kann die generische Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> innerhalb von `Startup.ConfigureServices` verwendet werden, die die typisierte Klasse angeben:</span><span class="sxs-lookup"><span data-stu-id="6a88a-363">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="6a88a-364">Der typisierte Client wird mit DI als „vorübergehend“ registriert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-364">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="6a88a-365">Der typisierte Client kann direkt eingefügt und verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-365">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="6a88a-366">Die Konfiguration kann für einen typisierten Client während der Registrierung in `Startup.ConfigureServices` angegeben werden, anstatt im Konstruktor des typisierten Clients, falls dies bevorzugt wird:</span><span class="sxs-lookup"><span data-stu-id="6a88a-366">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="6a88a-367">Es ist möglich, `HttpClient` vollständig in einem typisierten Client zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="6a88a-367">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="6a88a-368">Anstatt ihn als eine Eigenschaft zur Verfügung zu stellen, können öffentliche Methoden bereitgestellt werden, die die `HttpClient`-Instanz intern aufrufen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-368">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="6a88a-369">Im vorangehenden Code wird `HttpClient` als ein privates Feld gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-369">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="6a88a-370">Alle Zugriffe, die externe Aufrufe durchführen, durchlaufen die Methode `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="6a88a-370">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="6a88a-371">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-371">Generated clients</span></span>

<span data-ttu-id="6a88a-372">`IHttpClientFactory` kann in Verbindung mit anderen Bibliotheken von Drittanbietern verwendet werden, z.B. [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="6a88a-372">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="6a88a-373">Refit ist eine REST-Bibliothek für .NET.</span><span class="sxs-lookup"><span data-stu-id="6a88a-373">Refit is a REST library for .NET.</span></span> <span data-ttu-id="6a88a-374">Sie konvertiert REST-APIs in Live-Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-374">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="6a88a-375">Eine Implementierung der Schnittstelle wird dynamisch von `RestService` generiert. `HttpClient` wird für die externen HTTP-Aufrufe verwendet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-375">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="6a88a-376">Eine Schnittstelle und eine Antwort werden definiert, um die externe API und die Antwort darzustellen:</span><span class="sxs-lookup"><span data-stu-id="6a88a-376">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="6a88a-377">Ein typisierter Client kann hinzugefügt werden. Refit wird zum Generieren der Implementierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="6a88a-377">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("https://localhost:5001");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="6a88a-378">Die definierte Schnittstelle kann bei Bedarf mit der von DI und Refit bereitgestellten Implementierung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-378">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="6a88a-379">Middleware für ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="6a88a-379">Outgoing request middleware</span></span>

<span data-ttu-id="6a88a-380">`HttpClient` enthält bereits das Konzept, Handler zu delegieren, die für ausgehende HTTP-Anforderungen miteinander verknüpft werden können.</span><span class="sxs-lookup"><span data-stu-id="6a88a-380">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="6a88a-381">`IHttpClientFactory` erleichtert das Definieren der Handler, die für jeden benannten Client angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-381">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="6a88a-382">Die Registrierung und Verkettung von mehreren Handlern wird unterstützt, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-382">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="6a88a-383">Jeder dieser Handler kann vor und nach der ausgehenden Anforderung Aufgaben ausführen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-383">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="6a88a-384">Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6a88a-384">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="6a88a-385">Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="6a88a-385">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="6a88a-386">Definieren Sie zum Erstellen eines Handlers eine von <xref:System.Net.Http.DelegatingHandler> abgeleitete Klasse.</span><span class="sxs-lookup"><span data-stu-id="6a88a-386">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="6a88a-387">Überschreiben Sie die Methode `SendAsync`, um Code auszuführen, bevor die Anforderung an den nächsten Handler in der Pipeline übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="6a88a-387">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="6a88a-388">Der vorangehende Code definiert einen einfachen Handler.</span><span class="sxs-lookup"><span data-stu-id="6a88a-388">The preceding code defines a basic handler.</span></span> <span data-ttu-id="6a88a-389">Er überprüft, ob ein `X-API-KEY`-Header in die Anforderung eingefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="6a88a-389">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="6a88a-390">Wenn der Header fehlt, kann er den HTTP-Aufruf vermeiden und eine geeignete Antwort zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-390">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="6a88a-391">Während der Registrierung kann mindestens ein Handler der Konfiguration eines `HttpClient` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-391">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="6a88a-392">Dieser Vorgang wird über die Erweiterungsmethoden von <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="6a88a-392">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="6a88a-393">Im vorangehenden Code ist `ValidateHeaderHandler` mit DI registriert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-393">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="6a88a-394">Die `IHttpClientFactory` erstellt einen separaten DI-Bereich für jeden Handler.</span><span class="sxs-lookup"><span data-stu-id="6a88a-394">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="6a88a-395">Handler können von Diensten eines beliebigen Bereichs abhängen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-395">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="6a88a-396">Dienste, von denen Handler abhängig sind, werden verworfen, wenn der Handler verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-396">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="6a88a-397">Nach der Registrierung kann <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> aufgerufen werden, was den Typ für den Handler übergibt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-397">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="6a88a-398">Mehrere Handler können in der Reihenfolge registriert werden, in der sie ausgeführt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-398">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="6a88a-399">Jeder Handler umschließt den nächsten Handler, bis der endgültige `HttpClientHandler` die Anforderung ausführt:</span><span class="sxs-lookup"><span data-stu-id="6a88a-399">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="6a88a-400">Verwenden Sie einen der folgenden Ansätze, um den anforderungsspezifischen Zustand mit Meldungshandlern zu teilen:</span><span class="sxs-lookup"><span data-stu-id="6a88a-400">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="6a88a-401">Übergeben Sie Daten mit `HttpRequestMessage.Properties` an den Handler.</span><span class="sxs-lookup"><span data-stu-id="6a88a-401">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="6a88a-402">Greifen Sie mit `IHttpContextAccessor` auf die aktuelle Anforderung zu.</span><span class="sxs-lookup"><span data-stu-id="6a88a-402">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="6a88a-403">Erstellen Sie ein benutzerdefiniertes `AsyncLocal`-Speicherobjekt, um die Daten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-403">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="6a88a-404">Verwenden von Polly-basierten Handlern</span><span class="sxs-lookup"><span data-stu-id="6a88a-404">Use Polly-based handlers</span></span>

<span data-ttu-id="6a88a-405">`IHttpClientFactory` integriert mit der beliebten Drittanbieter-Bibliothek namens [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="6a88a-405">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="6a88a-406">Polly ist eine umfassende Bibliothek für die Behandlung von beständigen und vorübergehenden Fehlern für .NET.</span><span class="sxs-lookup"><span data-stu-id="6a88a-406">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="6a88a-407">Entwicklern wird damit ermöglicht, Richtlinien wie Wiederholungsrichtlinien, Trennschalterrichtlinien, Timeout-Richtlinien, Bulkhead Isolation-Richtlinien und Ausweichrichtlinien in einer flüssigen und threadsicheren Art und Weise auszudrücken.</span><span class="sxs-lookup"><span data-stu-id="6a88a-407">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="6a88a-408">Erweiterungsmethoden werden bereitgestellt, um die Verwendung von Polly-Richtlinien mit konfigurierten `HttpClient`-Instanzen zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="6a88a-408">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="6a88a-409">Die Polly-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="6a88a-409">The Polly extensions:</span></span>

* <span data-ttu-id="6a88a-410">Unterstützen das Hinzufügen von Polly-basierten Handlern zu Clients.</span><span class="sxs-lookup"><span data-stu-id="6a88a-410">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="6a88a-411">Können nach Installation des [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/)-NuGet-Pakets verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-411">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="6a88a-412">Das Paket ist nicht im freigegebenen ASP.NET Core-Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-412">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="6a88a-413">Behandeln von vorrübergehenden Fehlern</span><span class="sxs-lookup"><span data-stu-id="6a88a-413">Handle transient faults</span></span>

<span data-ttu-id="6a88a-414">Am häufigsten treten Fehler auf, wenn externe HTTP-Aufrufe vorübergehend sind.</span><span class="sxs-lookup"><span data-stu-id="6a88a-414">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="6a88a-415">Eine praktische Erweiterungsmethode namens `AddTransientHttpErrorPolicy` ist enthalten. Sie ermöglicht das Definieren von Richtlinien zum Behandeln vorübergehender Fehler.</span><span class="sxs-lookup"><span data-stu-id="6a88a-415">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="6a88a-416">Richtlinien, die mit dieser Erweiterungsmethode konfiguriert wurden, behandeln `HttpRequestException`, HTTP 5xx-Antworten und HTTP 408-Antworten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-416">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="6a88a-417">Die `AddTransientHttpErrorPolicy`-Erweiterung kann in `Startup.ConfigureServices` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-417">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6a88a-418">Die Erweiterung ermöglicht den Zugriff auf ein `PolicyBuilder`-Objekt, das für die Fehlerbehandlung konfiguriert wurde und mögliche vorübergehende Fehler darstellt:</span><span class="sxs-lookup"><span data-stu-id="6a88a-418">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="6a88a-419">Im vorangehenden Code wird eine `WaitAndRetryAsync`-Richtlinie definiert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-419">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="6a88a-420">Anforderungsfehler werden bis zu dreimal mit einer Verzögerung von 600 ms wiederholt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-420">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="6a88a-421">Dynamisches Auswählen von Richtlinien</span><span class="sxs-lookup"><span data-stu-id="6a88a-421">Dynamically select policies</span></span>

<span data-ttu-id="6a88a-422">Es gibt zusätzliche Erweiterungsmethoden, die zum Hinzufügen von Polly-basierten Handlern verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="6a88a-422">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="6a88a-423">Eine dieser Erweiterungen ist `AddPolicyHandler`, die über mehrere Überladungen verfügt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-423">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="6a88a-424">Eine Überladung ermöglicht, dass die Anforderung beim Definieren der zu verwendenden Richtlinie überprüft werden kann:</span><span class="sxs-lookup"><span data-stu-id="6a88a-424">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="6a88a-425">Im vorangehenden Code wird ein 10 Sekunden langer Timeout angewendet, wenn die ausgehende Anforderung eine HTTP GET-Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="6a88a-425">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="6a88a-426">Für alle anderen HTTP-Methoden wird ein 30 Sekunden langer Timeout verwendet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-426">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="6a88a-427">Hinzufügen mehrerer Polly-Handler</span><span class="sxs-lookup"><span data-stu-id="6a88a-427">Add multiple Polly handlers</span></span>

<span data-ttu-id="6a88a-428">Es ist üblich, Polly-Richtlinien zu schachteln, um erweiterte Funktionen bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="6a88a-428">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="6a88a-429">Im vorangehenden Beispiel werden zwei Handler hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-429">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="6a88a-430">Der Erste verwendet die `AddTransientHttpErrorPolicy`-Erweiterung, um eine Wiederholungsrichtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-430">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="6a88a-431">Fehlgeschlagene Anforderungen werden bis zu drei Mal wiederholt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-431">Failed requests are retried up to three times.</span></span> <span data-ttu-id="6a88a-432">Der zweite Aufruf von `AddTransientHttpErrorPolicy` fügt eine Trennschalterrichtlinie hinzu.</span><span class="sxs-lookup"><span data-stu-id="6a88a-432">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="6a88a-433">Weitere externe Anforderungen werden 30 Sekunden lang blockiert, wenn fünf Fehlversuche hintereinander stattfinden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-433">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="6a88a-434">Trennschalterrichtlinien sind zustandsbehaftet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-434">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="6a88a-435">Alle Aufrufe über diesen Client teilen den gleichen Schalterzustand.</span><span class="sxs-lookup"><span data-stu-id="6a88a-435">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="6a88a-436">Hinzufügen von Richtlinien aus der Polly-Registrierung</span><span class="sxs-lookup"><span data-stu-id="6a88a-436">Add policies from the Polly registry</span></span>

<span data-ttu-id="6a88a-437">Eine Methode zum Verwalten regelmäßig genutzter Richtlinien ist, Sie einmal zu definieren und mit `PolicyRegistry` zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="6a88a-437">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="6a88a-438">Eine Erweiterungsmethode wird bereitgestellt, die einem Handler ermöglicht, mithilfe einer Richtlinie aus der Registrierung hinzugefügt zu werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-438">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="6a88a-439">Im oben stehenden Code werden zwei Richtlinien registriert, wenn `PolicyRegistry` zu `ServiceCollection` hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-439">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="6a88a-440">Damit eine Richtlinie aus der Registrierung verwendet werden kann, wird die Methode `AddPolicyHandlerFromRegistry` verwendet. Dabei wird der Name der anzuwendenden Richtlinie übergeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-440">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="6a88a-441">Weitere Informationen zu `IHttpClientFactory` und Polly-Integrationen finden Sie im [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="6a88a-441">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="6a88a-442">HttpClient und die Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="6a88a-442">HttpClient and lifetime management</span></span>

<span data-ttu-id="6a88a-443">Bei jedem Aufruf von `CreateClient` in der `IHttpClientFactory` wird eine neue Instanz von `HttpClient` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-443">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="6a88a-444">Es gibt einen <xref:System.Net.Http.HttpMessageHandler> pro benanntem Client.</span><span class="sxs-lookup"><span data-stu-id="6a88a-444">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="6a88a-445">Die Factory verwaltet die Lebensdauer der `HttpMessageHandler`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-445">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="6a88a-446">`IHttpClientFactory` legt die `HttpMessageHandler`-Instanzen zusammen, die von der Factory zum Reduzieren des Ressourcenverbrauchs erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-446">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="6a88a-447">Eine `HttpMessageHandler`-Instanz kann aus dem Pool wiederverwendet werden, wenn eine neue `HttpClient`-Instanz erstellt wird und deren Lebensdauer noch nicht abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="6a88a-447">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="6a88a-448">Das Zusammenlegen von Handlern ist ein wünschenswerter Vorgang, da jeder Handler in der Regel seine zugrunde liegenden HTTP-Verbindungen selbst verwaltet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-448">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="6a88a-449">Wenn mehr Handler als nötig erstellt werden, können Verzögerungen bei Verbindungen entstehen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-449">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="6a88a-450">Einige Handler halten Verbindungen auch unbegrenzt offen, was verhindert, dass der Handler auf DNS-Änderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-450">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="6a88a-451">Die Standardlebensdauer von Handlern beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-451">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="6a88a-452">Der Standardwert kann für jeden benannten Client überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-452">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="6a88a-453">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> auf dem bei der Erstellung des Clients zurückgegebenen `IHttpClientBuilder` auf, um den Wert zu überschreiben:</span><span class="sxs-lookup"><span data-stu-id="6a88a-453">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="6a88a-454">Das Verwerfen des Client ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="6a88a-454">Disposal of the client isn't required.</span></span> <span data-ttu-id="6a88a-455">Beim Verwerfen werden ausgehende Anforderungen abgebrochen, und es wird sichergestellt, dass die angegebene `HttpClient`-Instanz nach dem Aufruf von <xref:System.IDisposable.Dispose*> nicht mehr verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="6a88a-455">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="6a88a-456">`IHttpClientFactory` verfolgt von `HttpClient`-Instanzen verwendete Ressourcen nach und verwirft sie.</span><span class="sxs-lookup"><span data-stu-id="6a88a-456">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="6a88a-457">Die `HttpClient`-Instanzen können im Allgemeinen als .NET-Objekte behandelt werden, die nicht verworfen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-457">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="6a88a-458">Das Beibehalten einer einzelnen `HttpClient`-Instanz für einen langen Zeitraum ist ein allgemeines Muster, das vor der Einführung von `IHttpClientFactory` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="6a88a-458">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="6a88a-459">Dieses Muster wird nach der Migration zu `IHttpClientFactory` überflüssig.</span><span class="sxs-lookup"><span data-stu-id="6a88a-459">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="6a88a-460">Alternativen zu IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="6a88a-460">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="6a88a-461">Mit der Verwendung von `IHttpClientFactory` in einer DI-fähigen App wird Folgendes vermieden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-461">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="6a88a-462">Probleme mit der Ressourcenauslastung durch Zusammenlegen von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="6a88a-462">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="6a88a-463">Probleme durch veraltetes DNS durch regelmäßigen Wechsel von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="6a88a-463">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="6a88a-464">Es gibt alternative Möglichkeiten zum Lösen des vorangehenden Problems mithilfe einer langlebigen <xref:System.Net.Http.SocketsHttpHandler>-Instanz:</span><span class="sxs-lookup"><span data-stu-id="6a88a-464">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="6a88a-465">Erstellen Sie eine `SocketsHttpHandler`-Instanz, wenn die App gestartet wird, und verwenden Sie diese für die Lebensdauer der App.</span><span class="sxs-lookup"><span data-stu-id="6a88a-465">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="6a88a-466">Konfigurieren Sie einen geeigneten Wert für <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> basierend auf den DNS-Aktualisierungszeiten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-466">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="6a88a-467">Erstellen Sie bei Bedarf `HttpClient`-Instanzen mithilfe von `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="6a88a-467">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="6a88a-468">Diese Ansätze lösen die Ressourcenverwaltungsprobleme, die `IHttpClientFactory` auf ähnliche Weise löst.</span><span class="sxs-lookup"><span data-stu-id="6a88a-468">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="6a88a-469">`SocketsHttpHandler` gibt Verbindungen für `HttpClient`-Instanzen frei.</span><span class="sxs-lookup"><span data-stu-id="6a88a-469">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="6a88a-470">Durch diese Freigabe wird die Erschöpfung von Sockets vermieden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-470">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="6a88a-471">`SocketsHttpHandler` wechselt die Verbindungen anhand von `PooledConnectionLifetime`, um Probleme durch veraltetes DNS zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-471">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="6a88a-472">Cookies</span><span class="sxs-lookup"><span data-stu-id="6a88a-472">Cookies</span></span>

<span data-ttu-id="6a88a-473">Die zusammengelegten `HttpMessageHandler`-Instanzen resultieren in der Freigabe von `CookieContainer`-Objekten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-473">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="6a88a-474">Die unerwartete Freigabe von `CookieContainer`-Objekten führt oft zu fehlerhaftem Code.</span><span class="sxs-lookup"><span data-stu-id="6a88a-474">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="6a88a-475">Ziehen Sie für Apps, die Cookies erfordern, folgende Vorgehensweisen in Betracht:</span><span class="sxs-lookup"><span data-stu-id="6a88a-475">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="6a88a-476">Deaktivieren der automatischen Cookieverarbeitung</span><span class="sxs-lookup"><span data-stu-id="6a88a-476">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="6a88a-477">Vermeiden der Verwendung von `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="6a88a-477">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="6a88a-478">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> auf, um die automatische Cookieverarbeitung zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="6a88a-478">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="6a88a-479">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="6a88a-479">Logging</span></span>

<span data-ttu-id="6a88a-480">Über `IHttpClientFactory` erstellte Clients zeichnen Protokollmeldungen für alle Anforderungen auf.</span><span class="sxs-lookup"><span data-stu-id="6a88a-480">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="6a88a-481">Aktivieren Sie die entsprechende Informationsebene in Ihrer Protokollierungskonfiguration, um die Standardprotokollmeldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-481">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="6a88a-482">Zusätzliche Protokollierung, z.B. das Protokollieren von Anforderungsheadern, wird nur auf der Ablaufverfolgungsebene enthalten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-482">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="6a88a-483">Die Protokollierungskategorie für jeden Client enthält den Namen des Clients.</span><span class="sxs-lookup"><span data-stu-id="6a88a-483">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="6a88a-484">Ein Client namens *MyNamedClient* protokolliert beispielsweise Meldungen mit der Kategorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="6a88a-484">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="6a88a-485">Meldungen mit dem Suffix *LogicalHandler* treten außerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="6a88a-485">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="6a88a-486">In der Anforderung werden Meldungen protokolliert, bevor andere Handler in der Pipeline sie verarbeitet haben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-486">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="6a88a-487">In der Antwort werden Meldungen protokolliert, nachdem andere Handler in der Pipeline die Antwort empfangen haben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-487">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="6a88a-488">Die Protokollierung tritt ebenfalls innerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="6a88a-488">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="6a88a-489">Im Beispiel *MyNamedClient* werden diese Meldungen für die Protokollkategorie `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` protokolliert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-489">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="6a88a-490">Dies findet für die Anforderung statt, nachdem alle anderen Handler ausgeführt wurden und bevor die Anforderung an das Netzwerk gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-490">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="6a88a-491">Diese Protokollierung enthält den Zustand der Antwort in der Antwort, bevor sie an die Handlerpipeline zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-491">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="6a88a-492">Das Aktivieren der Protokollierung außerhalb und innerhalb der Pipeline ermöglicht die Überprüfung der Änderungen, die durch andere Handler in der Pipeline erfolgt sind.</span><span class="sxs-lookup"><span data-stu-id="6a88a-492">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="6a88a-493">Dies kann beispielsweise die Änderungen an Anforderungsheadern oder am Statuscode der Antwort enthalten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-493">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="6a88a-494">Das Einschließen des Namens des Clients in der Protokollkategorie aktiviert bei Bedarf die Protokollfilterung für spezifische benannte Clients.</span><span class="sxs-lookup"><span data-stu-id="6a88a-494">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="6a88a-495">Konfigurieren von HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="6a88a-495">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="6a88a-496">Es kann notwendig sein, die Konfiguration des inneren von einem Client verwendeten `HttpMessageHandler` zu steuern.</span><span class="sxs-lookup"><span data-stu-id="6a88a-496">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="6a88a-497">`IHttpClientBuilder` wird zurückgegeben, wenn benannte oder typisierte Clients hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-497">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="6a88a-498">Die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> kann zum Definieren eines Delegaten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-498">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="6a88a-499">Der Delegat wird verwendet, um den primären `HttpMessageHandler` zu erstellen und konfigurieren, der von dem Client verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="6a88a-499">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="6a88a-500">Verwenden von IHttpClientFactory in einer Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="6a88a-500">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="6a88a-501">Fügen Sie in einer Konsolen-App dem Projekt die folgenden Paketverweise hinzu:</span><span class="sxs-lookup"><span data-stu-id="6a88a-501">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="6a88a-502">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="6a88a-502">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="6a88a-503">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="6a88a-503">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="6a88a-504">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6a88a-504">In the following example:</span></span>

* <span data-ttu-id="6a88a-505"><xref:System.Net.Http.IHttpClientFactory> ist im Dienstcontainer des [generischen Hosts](xref:fundamentals/host/generic-host) registriert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-505"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="6a88a-506">`MyService` erstellt eine Clientfactoryinstanz aus dem-Dienst, der zum Erstellen eines `HttpClient`verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-506">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="6a88a-507">`HttpClient` wird zum Abrufen einer Webseite verwendet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-507">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="6a88a-508">`Main` erstellt einen Bereich, um die `GetPage`-Methode des Diensts auszuführen und die ersten 500 Zeichen des Webseiteninhalts in die Konsole zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-508">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="6a88a-509">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6a88a-509">Additional resources</span></span>

* [<span data-ttu-id="6a88a-510">Verwenden von HttpClientFactory zur Implementierung robuster HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="6a88a-510">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="6a88a-511">Implementieren von Wiederholungen von HTTP-Aufrufen mit exponentiellem Backoff mit HttpClientFactory und Polly-Richtlinien</span><span class="sxs-lookup"><span data-stu-id="6a88a-511">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="6a88a-512">Implementieren des Trennschaltermusters</span><span class="sxs-lookup"><span data-stu-id="6a88a-512">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="6a88a-513">Von [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), und [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="6a88a-513">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="6a88a-514"><xref:System.Net.Http.IHttpClientFactory> kann registriert und zum Konfigurieren und Erstellen von <xref:System.Net.Http.HttpClient>-Instanzen in einer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-514">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="6a88a-515">Dies hat folgende Vorteile:</span><span class="sxs-lookup"><span data-stu-id="6a88a-515">It offers the following benefits:</span></span>

* <span data-ttu-id="6a88a-516">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-516">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="6a88a-517">Ein *GitHub*-Client kann beispielsweise registriert werden und so konfiguriert werden, um auf [GitHub](https://github.com/) zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-517">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="6a88a-518">Ein Standard-Client kann für andere Zwecke registriert werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-518">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="6a88a-519">Das Konzept der ausgehenden Middleware wird über delegierende Handler in `HttpClient` in Code umgesetzt. Außerdem werden Erweiterungen für auf Polly basierende Middleware bereitgestellt, die dies nutzen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-519">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="6a88a-520">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-520">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="6a88a-521">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-521">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="6a88a-522">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6a88a-522">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="6a88a-523">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="6a88a-523">Prerequisites</span></span>

<span data-ttu-id="6a88a-524">Für Projekte mit der Zielplattform .NET Framework muss das NuGet-Paket [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) installiert werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-524">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="6a88a-525">Projekte für .NET Core, die auf das [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) verweisen, enthalten bereits das `Microsoft.Extensions.Http`-Paket.</span><span class="sxs-lookup"><span data-stu-id="6a88a-525">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="6a88a-526">Verbrauchsmuster</span><span class="sxs-lookup"><span data-stu-id="6a88a-526">Consumption patterns</span></span>

<span data-ttu-id="6a88a-527">Es gibt mehrere Möglichkeiten `IHttpClientFactory` in einer App zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-527">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="6a88a-528">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="6a88a-528">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="6a88a-529">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-529">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="6a88a-530">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-530">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="6a88a-531">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-531">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="6a88a-532">Keine dieser Möglichkeiten ist einer anderen überlegen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-532">None of them are strictly superior to another.</span></span> <span data-ttu-id="6a88a-533">Der beste Ansatz richtet sich nach den Einschränkungen der App.</span><span class="sxs-lookup"><span data-stu-id="6a88a-533">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="6a88a-534">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="6a88a-534">Basic usage</span></span>

<span data-ttu-id="6a88a-535">`IHttpClientFactory` kann durch Aufrufen der Erweiterungsmethode `AddHttpClient` auf `IServiceCollection` in der Methode `Startup.ConfigureServices` registriert werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-535">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="6a88a-536">Nach der Registrierung kann Code `IHttpClientFactory` überall akzeptieren, wo Dienste mithilfe der [Dependency Injection](xref:fundamentals/dependency-injection) (DI) eingefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="6a88a-536">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="6a88a-537">`IHttpClientFactory` kann zum Erstellen einer `HttpClient`-Instanz verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-537">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="6a88a-538">`IHttpClientFactory` auf diese Weise zu verwenden, ist eine gute Möglichkeit zum Umgestalten einer vorhandenen App.</span><span class="sxs-lookup"><span data-stu-id="6a88a-538">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="6a88a-539">Dies hat keine Auswirkung auf die Verwendung von `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="6a88a-539">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="6a88a-540">An Stellen, in denen `HttpClient`-Instanzen derzeit erstellt werden, können Sie diese Ereignisse mit einem Aufruf von <xref:System.Net.Http.IHttpClientFactory.CreateClient*> ersetzen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-540">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="6a88a-541">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-541">Named clients</span></span>

<span data-ttu-id="6a88a-542">Wenn eine App mehrere verschiedene Verwendungen von `HttpClient` mit jeweils unterschiedlicher Konfiguration erfordert, ist die Verwendung von **benannten Clients** eine Option.</span><span class="sxs-lookup"><span data-stu-id="6a88a-542">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="6a88a-543">Die Konfiguration einer benannten `HttpClient` kann während der Registrierung in `Startup.ConfigureServices` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-543">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="6a88a-544">Im oben stehenden Code wird `AddHttpClient` aufgerufen, und dabei wird der Name *github* angegeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-544">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="6a88a-545">Für diesen Client wird Standardkonfiguration angewendet, d.h. die Basisadresse und zwei Header, die erforderlich sind, um mit der GitHub-API zu funktionieren.</span><span class="sxs-lookup"><span data-stu-id="6a88a-545">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="6a88a-546">Wenn `CreateClient` aufgerufen wird, wird jedes Mal eine neue Instanz von `HttpClient` erstellt und die Konfigurationsaktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-546">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="6a88a-547">Zum Verarbeiten eines benannten Clients kann ein Zeichenfolgenparameter an `CreateClient` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-547">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="6a88a-548">Geben Sie den Namen des zu erstellenden Clients an:</span><span class="sxs-lookup"><span data-stu-id="6a88a-548">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="6a88a-549">Im vorangehenden Code muss die Anforderung keinen Hostnamen angeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-549">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="6a88a-550">Sie muss nur den Pfad übergeben, da die für den Client konfigurierte Basisadresse verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-550">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="6a88a-551">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-551">Typed clients</span></span>

<span data-ttu-id="6a88a-552">Typisierte Clients:</span><span class="sxs-lookup"><span data-stu-id="6a88a-552">Typed clients:</span></span>

* <span data-ttu-id="6a88a-553">Stellen dieselben Funktionen wie benannte Clients bereit, ohne Zeichenfolgen als Schlüssel verwenden zu müssen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-553">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="6a88a-554">Bieten Hilfe durch IntelliSense und Compiler beim Verarbeiten von Clients.</span><span class="sxs-lookup"><span data-stu-id="6a88a-554">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="6a88a-555">Stellen einen einzelnen Ort zum Konfigurieren und Interagieren mit einem bestimmten `HttpClient` bereit.</span><span class="sxs-lookup"><span data-stu-id="6a88a-555">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="6a88a-556">Zum Beispiel kann ein einzelner typisierter Client für einen einzelnen Back-End-Endpunkt verwendet werden, der jegliche Logik kapselt, die mit diesem Endpunkt interagiert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-556">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="6a88a-557">Funktionieren mit DI und können überall in Ihrer App eingefügt werden, wo sie benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-557">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="6a88a-558">Ein typisierter Client akzeptiert einen `HttpClient`-Parameter in seinem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="6a88a-558">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="6a88a-559">Im vorangehenden Code wird die Konfiguration in den typisierten Client verschoben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-559">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="6a88a-560">Das `HttpClient`-Objekt wird als öffentliche Eigenschaft zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-560">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="6a88a-561">Es ist möglich, API-spezifische Methoden zu definieren, die die `HttpClient`-Funktionalität zur Verfügung stellen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-561">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="6a88a-562">Die Methode `GetAspNetDocsIssues` kapselt den Code, der für die Abfrage und Analyse des neuesten offenen Problems aus dem GitHub-Repository erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="6a88a-562">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="6a88a-563">Zum Registrieren eines typisierten Clients kann die generische Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> innerhalb von `Startup.ConfigureServices` verwendet werden, die die typisierte Klasse angeben:</span><span class="sxs-lookup"><span data-stu-id="6a88a-563">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="6a88a-564">Der typisierte Client wird mit DI als „vorübergehend“ registriert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-564">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="6a88a-565">Der typisierte Client kann direkt eingefügt und verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-565">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="6a88a-566">Die Konfiguration kann für einen typisierten Client während der Registrierung in `Startup.ConfigureServices` angegeben werden, anstatt im Konstruktor des typisierten Clients, falls dies bevorzugt wird:</span><span class="sxs-lookup"><span data-stu-id="6a88a-566">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="6a88a-567">Es ist möglich, `HttpClient` vollständig in einem typisierten Client zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="6a88a-567">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="6a88a-568">Anstatt ihn als eine Eigenschaft zur Verfügung zu stellen, können öffentliche Methoden bereitgestellt werden, die die `HttpClient`-Instanz intern aufrufen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-568">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="6a88a-569">Im vorangehenden Code wird `HttpClient` als ein privates Feld gespeichert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-569">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="6a88a-570">Alle Zugriffe, die externe Aufrufe durchführen, durchlaufen die Methode `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="6a88a-570">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="6a88a-571">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="6a88a-571">Generated clients</span></span>

<span data-ttu-id="6a88a-572">`IHttpClientFactory` kann in Verbindung mit anderen Bibliotheken von Drittanbietern verwendet werden, z.B. [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="6a88a-572">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="6a88a-573">Refit ist eine REST-Bibliothek für .NET.</span><span class="sxs-lookup"><span data-stu-id="6a88a-573">Refit is a REST library for .NET.</span></span> <span data-ttu-id="6a88a-574">Sie konvertiert REST-APIs in Live-Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-574">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="6a88a-575">Eine Implementierung der Schnittstelle wird dynamisch von `RestService` generiert. `HttpClient` wird für die externen HTTP-Aufrufe verwendet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-575">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="6a88a-576">Eine Schnittstelle und eine Antwort werden definiert, um die externe API und die Antwort darzustellen:</span><span class="sxs-lookup"><span data-stu-id="6a88a-576">An interface and a reply are defined to represent the external API and its response:</span></span>

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="6a88a-577">Ein typisierter Client kann hinzugefügt werden. Refit wird zum Generieren der Implementierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="6a88a-577">A typed client can be added, using Refit to generate the implementation:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

<span data-ttu-id="6a88a-578">Die definierte Schnittstelle kann bei Bedarf mit der von DI und Refit bereitgestellten Implementierung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-578">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a><span data-ttu-id="6a88a-579">Middleware für ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="6a88a-579">Outgoing request middleware</span></span>

<span data-ttu-id="6a88a-580">`HttpClient` enthält bereits das Konzept, Handler zu delegieren, die für ausgehende HTTP-Anforderungen miteinander verknüpft werden können.</span><span class="sxs-lookup"><span data-stu-id="6a88a-580">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="6a88a-581">`IHttpClientFactory` erleichtert das Definieren der Handler, die für jeden benannten Client angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-581">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="6a88a-582">Die Registrierung und Verkettung von mehreren Handlern wird unterstützt, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-582">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="6a88a-583">Jeder dieser Handler kann vor und nach der ausgehenden Anforderung Aufgaben ausführen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-583">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="6a88a-584">Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="6a88a-584">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="6a88a-585">Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="6a88a-585">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="6a88a-586">Definieren Sie zum Erstellen eines Handlers eine von <xref:System.Net.Http.DelegatingHandler> abgeleitete Klasse.</span><span class="sxs-lookup"><span data-stu-id="6a88a-586">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="6a88a-587">Überschreiben Sie die Methode `SendAsync`, um Code auszuführen, bevor die Anforderung an den nächsten Handler in der Pipeline übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="6a88a-587">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="6a88a-588">Der vorangehende Code definiert einen einfachen Handler.</span><span class="sxs-lookup"><span data-stu-id="6a88a-588">The preceding code defines a basic handler.</span></span> <span data-ttu-id="6a88a-589">Er überprüft, ob ein `X-API-KEY`-Header in die Anforderung eingefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="6a88a-589">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="6a88a-590">Wenn der Header fehlt, kann er den HTTP-Aufruf vermeiden und eine geeignete Antwort zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-590">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="6a88a-591">Während der Registrierung kann mindestens ein Handler der Konfiguration eines `HttpClient` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-591">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="6a88a-592">Dieser Vorgang wird über die Erweiterungsmethoden von <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="6a88a-592">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="6a88a-593">Im vorangehenden Code ist `ValidateHeaderHandler` mit DI registriert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-593">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="6a88a-594">Der Handler **muss** in DI als vorübergehender Dienst registriert sein, niemals bereichsbezogen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-594">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="6a88a-595">Wenn der Handler als bereichsbezogener Dienst registriert ist und alle Dienste, von denen der Handler abhängig ist, gelöscht werden können:</span><span class="sxs-lookup"><span data-stu-id="6a88a-595">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="6a88a-596">Die Dienste des Handlers können verworfen werden, bevor der Handler den Gültigkeitsbereich verlässt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-596">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="6a88a-597">Der verworfenen Handlerdienst bewirken, dass der Handler fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-597">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="6a88a-598">Nach der Registrierung kann <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> aufgerufen werden, wobei der Typ für den Handler übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-598">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="6a88a-599">Mehrere Handler können in der Reihenfolge registriert werden, in der sie ausgeführt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-599">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="6a88a-600">Jeder Handler umschließt den nächsten Handler, bis der endgültige `HttpClientHandler` die Anforderung ausführt:</span><span class="sxs-lookup"><span data-stu-id="6a88a-600">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="6a88a-601">Verwenden Sie einen der folgenden Ansätze, um den anforderungsspezifischen Zustand mit Meldungshandlern zu teilen:</span><span class="sxs-lookup"><span data-stu-id="6a88a-601">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="6a88a-602">Übergeben Sie Daten mit `HttpRequestMessage.Properties` an den Handler.</span><span class="sxs-lookup"><span data-stu-id="6a88a-602">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="6a88a-603">Greifen Sie mit `IHttpContextAccessor` auf die aktuelle Anforderung zu.</span><span class="sxs-lookup"><span data-stu-id="6a88a-603">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="6a88a-604">Erstellen Sie ein benutzerdefiniertes `AsyncLocal`-Speicherobjekt, um die Daten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-604">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="6a88a-605">Verwenden von Polly-basierten Handlern</span><span class="sxs-lookup"><span data-stu-id="6a88a-605">Use Polly-based handlers</span></span>

<span data-ttu-id="6a88a-606">`IHttpClientFactory` integriert mit der beliebten Drittanbieter-Bibliothek namens [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="6a88a-606">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="6a88a-607">Polly ist eine umfassende Bibliothek für die Behandlung von beständigen und vorübergehenden Fehlern für .NET.</span><span class="sxs-lookup"><span data-stu-id="6a88a-607">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="6a88a-608">Entwicklern wird damit ermöglicht, Richtlinien wie Wiederholungsrichtlinien, Trennschalterrichtlinien, Timeout-Richtlinien, Bulkhead Isolation-Richtlinien und Ausweichrichtlinien in einer flüssigen und threadsicheren Art und Weise auszudrücken.</span><span class="sxs-lookup"><span data-stu-id="6a88a-608">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="6a88a-609">Erweiterungsmethoden werden bereitgestellt, um die Verwendung von Polly-Richtlinien mit konfigurierten `HttpClient`-Instanzen zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="6a88a-609">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="6a88a-610">Die Polly-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="6a88a-610">The Polly extensions:</span></span>

* <span data-ttu-id="6a88a-611">Unterstützen das Hinzufügen von Polly-basierten Handlern zu Clients.</span><span class="sxs-lookup"><span data-stu-id="6a88a-611">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="6a88a-612">Können nach Installation des [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/)-NuGet-Pakets verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-612">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="6a88a-613">Das Paket ist nicht im freigegebenen ASP.NET Core-Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-613">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="6a88a-614">Behandeln von vorrübergehenden Fehlern</span><span class="sxs-lookup"><span data-stu-id="6a88a-614">Handle transient faults</span></span>

<span data-ttu-id="6a88a-615">Am häufigsten treten Fehler auf, wenn externe HTTP-Aufrufe vorübergehend sind.</span><span class="sxs-lookup"><span data-stu-id="6a88a-615">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="6a88a-616">Eine praktische Erweiterungsmethode namens `AddTransientHttpErrorPolicy` ist enthalten. Sie ermöglicht das Definieren von Richtlinien zum Behandeln vorübergehender Fehler.</span><span class="sxs-lookup"><span data-stu-id="6a88a-616">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="6a88a-617">Richtlinien, die mit dieser Erweiterungsmethode konfiguriert wurden, behandeln `HttpRequestException`, HTTP 5xx-Antworten und HTTP 408-Antworten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-617">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="6a88a-618">Die `AddTransientHttpErrorPolicy`-Erweiterung kann in `Startup.ConfigureServices` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-618">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6a88a-619">Die Erweiterung ermöglicht den Zugriff auf ein `PolicyBuilder`-Objekt, das für die Fehlerbehandlung konfiguriert wurde und mögliche vorübergehende Fehler darstellt:</span><span class="sxs-lookup"><span data-stu-id="6a88a-619">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="6a88a-620">Im vorangehenden Code wird eine `WaitAndRetryAsync`-Richtlinie definiert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-620">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="6a88a-621">Anforderungsfehler werden bis zu dreimal mit einer Verzögerung von 600 ms wiederholt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-621">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="6a88a-622">Dynamisches Auswählen von Richtlinien</span><span class="sxs-lookup"><span data-stu-id="6a88a-622">Dynamically select policies</span></span>

<span data-ttu-id="6a88a-623">Es gibt zusätzliche Erweiterungsmethoden, die zum Hinzufügen von Polly-basierten Handlern verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="6a88a-623">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="6a88a-624">Eine dieser Erweiterungen ist `AddPolicyHandler`, die über mehrere Überladungen verfügt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-624">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="6a88a-625">Eine Überladung ermöglicht, dass die Anforderung beim Definieren der zu verwendenden Richtlinie überprüft werden kann:</span><span class="sxs-lookup"><span data-stu-id="6a88a-625">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="6a88a-626">Im vorangehenden Code wird ein 10 Sekunden langer Timeout angewendet, wenn die ausgehende Anforderung eine HTTP GET-Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="6a88a-626">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="6a88a-627">Für alle anderen HTTP-Methoden wird ein 30 Sekunden langer Timeout verwendet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-627">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="6a88a-628">Hinzufügen mehrerer Polly-Handler</span><span class="sxs-lookup"><span data-stu-id="6a88a-628">Add multiple Polly handlers</span></span>

<span data-ttu-id="6a88a-629">Es ist üblich, Polly-Richtlinien zu schachteln, um erweiterte Funktionen bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="6a88a-629">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="6a88a-630">Im vorangehenden Beispiel werden zwei Handler hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-630">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="6a88a-631">Der Erste verwendet die `AddTransientHttpErrorPolicy`-Erweiterung, um eine Wiederholungsrichtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-631">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="6a88a-632">Fehlgeschlagene Anforderungen werden bis zu drei Mal wiederholt.</span><span class="sxs-lookup"><span data-stu-id="6a88a-632">Failed requests are retried up to three times.</span></span> <span data-ttu-id="6a88a-633">Der zweite Aufruf von `AddTransientHttpErrorPolicy` fügt eine Trennschalterrichtlinie hinzu.</span><span class="sxs-lookup"><span data-stu-id="6a88a-633">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="6a88a-634">Weitere externe Anforderungen werden 30 Sekunden lang blockiert, wenn fünf Fehlversuche hintereinander stattfinden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-634">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="6a88a-635">Trennschalterrichtlinien sind zustandsbehaftet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-635">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="6a88a-636">Alle Aufrufe über diesen Client teilen den gleichen Schalterzustand.</span><span class="sxs-lookup"><span data-stu-id="6a88a-636">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="6a88a-637">Hinzufügen von Richtlinien aus der Polly-Registrierung</span><span class="sxs-lookup"><span data-stu-id="6a88a-637">Add policies from the Polly registry</span></span>

<span data-ttu-id="6a88a-638">Eine Methode zum Verwalten regelmäßig genutzter Richtlinien ist, Sie einmal zu definieren und mit `PolicyRegistry` zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="6a88a-638">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="6a88a-639">Eine Erweiterungsmethode wird bereitgestellt, die einem Handler ermöglicht, mithilfe einer Richtlinie aus der Registrierung hinzugefügt zu werden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-639">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="6a88a-640">Im oben stehenden Code werden zwei Richtlinien registriert, wenn `PolicyRegistry` zu `ServiceCollection` hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-640">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="6a88a-641">Damit eine Richtlinie aus der Registrierung verwendet werden kann, wird die Methode `AddPolicyHandlerFromRegistry` verwendet. Dabei wird der Name der anzuwendenden Richtlinie übergeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-641">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="6a88a-642">Weitere Informationen zu `IHttpClientFactory` und Polly-Integrationen finden Sie im [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="6a88a-642">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="6a88a-643">HttpClient und die Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="6a88a-643">HttpClient and lifetime management</span></span>

<span data-ttu-id="6a88a-644">Bei jedem Aufruf von `CreateClient` in der `IHttpClientFactory` wird eine neue Instanz von `HttpClient` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-644">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="6a88a-645">Es gibt einen <xref:System.Net.Http.HttpMessageHandler> pro benanntem Client.</span><span class="sxs-lookup"><span data-stu-id="6a88a-645">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="6a88a-646">Die Factory verwaltet die Lebensdauer der `HttpMessageHandler`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-646">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="6a88a-647">`IHttpClientFactory` legt die `HttpMessageHandler`-Instanzen zusammen, die von der Factory zum Reduzieren des Ressourcenverbrauchs erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-647">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="6a88a-648">Eine `HttpMessageHandler`-Instanz kann aus dem Pool wiederverwendet werden, wenn eine neue `HttpClient`-Instanz erstellt wird und deren Lebensdauer noch nicht abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="6a88a-648">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="6a88a-649">Das Zusammenlegen von Handlern ist ein wünschenswerter Vorgang, da jeder Handler in der Regel seine zugrunde liegenden HTTP-Verbindungen selbst verwaltet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-649">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="6a88a-650">Wenn mehr Handler als nötig erstellt werden, können Verzögerungen bei Verbindungen entstehen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-650">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="6a88a-651">Einige Handler halten Verbindungen auch unbegrenzt offen, was verhindert, dass der Handler auf DNS-Änderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-651">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="6a88a-652">Die Standardlebensdauer von Handlern beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-652">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="6a88a-653">Der Standardwert kann für jeden benannten Client überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-653">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="6a88a-654">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> auf dem bei der Erstellung des Clients zurückgegebenen `IHttpClientBuilder` auf, um den Wert zu überschreiben:</span><span class="sxs-lookup"><span data-stu-id="6a88a-654">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="6a88a-655">Das Verwerfen des Client ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="6a88a-655">Disposal of the client isn't required.</span></span> <span data-ttu-id="6a88a-656">Beim Verwerfen werden ausgehende Anforderungen abgebrochen, und es wird sichergestellt, dass die angegebene `HttpClient`-Instanz nach dem Aufruf von <xref:System.IDisposable.Dispose*> nicht mehr verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="6a88a-656">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="6a88a-657">`IHttpClientFactory` verfolgt von `HttpClient`-Instanzen verwendete Ressourcen nach und verwirft sie.</span><span class="sxs-lookup"><span data-stu-id="6a88a-657">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="6a88a-658">Die `HttpClient`-Instanzen können im Allgemeinen als .NET-Objekte behandelt werden, die nicht verworfen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-658">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="6a88a-659">Das Beibehalten einer einzelnen `HttpClient`-Instanz für einen langen Zeitraum ist ein allgemeines Muster, das vor der Einführung von `IHttpClientFactory` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="6a88a-659">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="6a88a-660">Dieses Muster wird nach der Migration zu `IHttpClientFactory` überflüssig.</span><span class="sxs-lookup"><span data-stu-id="6a88a-660">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="6a88a-661">Alternativen zu IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="6a88a-661">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="6a88a-662">Mit der Verwendung von `IHttpClientFactory` in einer DI-fähigen App wird Folgendes vermieden:</span><span class="sxs-lookup"><span data-stu-id="6a88a-662">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="6a88a-663">Probleme mit der Ressourcenauslastung durch Zusammenlegen von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="6a88a-663">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="6a88a-664">Probleme durch veraltetes DNS durch regelmäßigen Wechsel von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="6a88a-664">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="6a88a-665">Es gibt alternative Möglichkeiten zum Lösen des vorangehenden Problems mithilfe einer langlebigen <xref:System.Net.Http.SocketsHttpHandler>-Instanz:</span><span class="sxs-lookup"><span data-stu-id="6a88a-665">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="6a88a-666">Erstellen Sie eine `SocketsHttpHandler`-Instanz, wenn die App gestartet wird, und verwenden Sie diese für die Lebensdauer der App.</span><span class="sxs-lookup"><span data-stu-id="6a88a-666">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="6a88a-667">Konfigurieren Sie einen geeigneten Wert für <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> basierend auf den DNS-Aktualisierungszeiten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-667">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="6a88a-668">Erstellen Sie bei Bedarf `HttpClient`-Instanzen mithilfe von `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="6a88a-668">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="6a88a-669">Diese Ansätze lösen die Ressourcenverwaltungsprobleme, die `IHttpClientFactory` auf ähnliche Weise löst.</span><span class="sxs-lookup"><span data-stu-id="6a88a-669">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="6a88a-670">`SocketsHttpHandler` gibt Verbindungen für `HttpClient`-Instanzen frei.</span><span class="sxs-lookup"><span data-stu-id="6a88a-670">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="6a88a-671">Durch diese Freigabe wird die Erschöpfung von Sockets vermieden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-671">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="6a88a-672">`SocketsHttpHandler` wechselt die Verbindungen anhand von `PooledConnectionLifetime`, um Probleme durch veraltetes DNS zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-672">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="6a88a-673">Cookies</span><span class="sxs-lookup"><span data-stu-id="6a88a-673">Cookies</span></span>

<span data-ttu-id="6a88a-674">Die zusammengelegten `HttpMessageHandler`-Instanzen resultieren in der Freigabe von `CookieContainer`-Objekten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-674">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="6a88a-675">Die unerwartete Freigabe von `CookieContainer`-Objekten führt oft zu fehlerhaftem Code.</span><span class="sxs-lookup"><span data-stu-id="6a88a-675">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="6a88a-676">Ziehen Sie für Apps, die Cookies erfordern, folgende Vorgehensweisen in Betracht:</span><span class="sxs-lookup"><span data-stu-id="6a88a-676">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="6a88a-677">Deaktivieren der automatischen Cookieverarbeitung</span><span class="sxs-lookup"><span data-stu-id="6a88a-677">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="6a88a-678">Vermeiden der Verwendung von `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="6a88a-678">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="6a88a-679">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> auf, um die automatische Cookieverarbeitung zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="6a88a-679">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="6a88a-680">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="6a88a-680">Logging</span></span>

<span data-ttu-id="6a88a-681">Über `IHttpClientFactory` erstellte Clients zeichnen Protokollmeldungen für alle Anforderungen auf.</span><span class="sxs-lookup"><span data-stu-id="6a88a-681">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="6a88a-682">Aktivieren Sie die entsprechende Informationsebene in Ihrer Protokollierungskonfiguration, um die Standardprotokollmeldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="6a88a-682">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="6a88a-683">Zusätzliche Protokollierung, z.B. das Protokollieren von Anforderungsheadern, wird nur auf der Ablaufverfolgungsebene enthalten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-683">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="6a88a-684">Die Protokollierungskategorie für jeden Client enthält den Namen des Clients.</span><span class="sxs-lookup"><span data-stu-id="6a88a-684">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="6a88a-685">Ein Client namens *MyNamedClient* protokolliert beispielsweise Meldungen mit der Kategorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="6a88a-685">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="6a88a-686">Meldungen mit dem Suffix *LogicalHandler* treten außerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="6a88a-686">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="6a88a-687">In der Anforderung werden Meldungen protokolliert, bevor andere Handler in der Pipeline sie verarbeitet haben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-687">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="6a88a-688">In der Antwort werden Meldungen protokolliert, nachdem andere Handler in der Pipeline die Antwort empfangen haben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-688">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="6a88a-689">Die Protokollierung tritt ebenfalls innerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="6a88a-689">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="6a88a-690">Im Beispiel *MyNamedClient* werden diese Meldungen für die Protokollkategorie `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` protokolliert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-690">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="6a88a-691">Dies findet für die Anforderung statt, nachdem alle anderen Handler ausgeführt wurden und bevor die Anforderung an das Netzwerk gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-691">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="6a88a-692">Diese Protokollierung enthält den Zustand der Antwort in der Antwort, bevor sie an die Handlerpipeline zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-692">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="6a88a-693">Das Aktivieren der Protokollierung außerhalb und innerhalb der Pipeline ermöglicht die Überprüfung der Änderungen, die durch andere Handler in der Pipeline erfolgt sind.</span><span class="sxs-lookup"><span data-stu-id="6a88a-693">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="6a88a-694">Dies kann beispielsweise die Änderungen an Anforderungsheadern oder am Statuscode der Antwort enthalten.</span><span class="sxs-lookup"><span data-stu-id="6a88a-694">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="6a88a-695">Das Einschließen des Namens des Clients in der Protokollkategorie aktiviert bei Bedarf die Protokollfilterung für spezifische benannte Clients.</span><span class="sxs-lookup"><span data-stu-id="6a88a-695">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="6a88a-696">Konfigurieren von HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="6a88a-696">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="6a88a-697">Es kann notwendig sein, die Konfiguration des inneren von einem Client verwendeten `HttpMessageHandler` zu steuern.</span><span class="sxs-lookup"><span data-stu-id="6a88a-697">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="6a88a-698">`IHttpClientBuilder` wird zurückgegeben, wenn benannte oder typisierte Clients hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-698">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="6a88a-699">Die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> kann zum Definieren eines Delegaten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6a88a-699">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="6a88a-700">Der Delegat wird verwendet, um den primären `HttpMessageHandler` zu erstellen und konfigurieren, der von dem Client verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="6a88a-700">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="6a88a-701">Verwenden von IHttpClientFactory in einer Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="6a88a-701">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="6a88a-702">Fügen Sie in einer Konsolen-App dem Projekt die folgenden Paketverweise hinzu:</span><span class="sxs-lookup"><span data-stu-id="6a88a-702">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="6a88a-703">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="6a88a-703">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="6a88a-704">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="6a88a-704">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="6a88a-705">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6a88a-705">In the following example:</span></span>

* <span data-ttu-id="6a88a-706"><xref:System.Net.Http.IHttpClientFactory> ist im Dienstcontainer des [generischen Hosts](xref:fundamentals/host/generic-host) registriert.</span><span class="sxs-lookup"><span data-stu-id="6a88a-706"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="6a88a-707">`MyService` erstellt eine Clientfactoryinstanz aus dem-Dienst, der zum Erstellen eines `HttpClient`verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6a88a-707">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="6a88a-708">`HttpClient` wird zum Abrufen einer Webseite verwendet.</span><span class="sxs-lookup"><span data-stu-id="6a88a-708">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="6a88a-709">`Main` erstellt einen Bereich, um die `GetPage`-Methode des Diensts auszuführen und die ersten 500 Zeichen des Webseiteninhalts in die Konsole zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="6a88a-709">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="6a88a-710">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6a88a-710">Additional resources</span></span>

* [<span data-ttu-id="6a88a-711">Verwenden von HttpClientFactory zur Implementierung robuster HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="6a88a-711">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="6a88a-712">Implementieren von Wiederholungen von HTTP-Aufrufen mit exponentiellem Backoff mit HttpClientFactory und Polly-Richtlinien</span><span class="sxs-lookup"><span data-stu-id="6a88a-712">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="6a88a-713">Implementieren des Trennschaltermusters</span><span class="sxs-lookup"><span data-stu-id="6a88a-713">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
