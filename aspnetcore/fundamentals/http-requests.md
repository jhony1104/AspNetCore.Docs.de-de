---
title: Stellen von HTTP-Anforderungen mithilfe von IHttpClientFactory in ASP.NET Core
author: stevejgordon
description: Erfahren Sie mehr über die Verwendung der IHttpClientFactory-Schnittstelle, um logische HttpClient-Instanzen in ASP.NET Core zu verwalten.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/09/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/http-requests
ms.openlocfilehash: ae33218d6944c62a08e677592ac0c66f9026b15f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766549"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a><span data-ttu-id="99b9d-103">Stellen von HTTP-Anforderungen mithilfe von IHttpClientFactory in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99b9d-103">Make HTTP requests using IHttpClientFactory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="99b9d-104">Von [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="99b9d-104">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak),  [Steve Gordon](https://github.com/stevejgordon), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="99b9d-105"><xref:System.Net.Http.IHttpClientFactory> kann registriert und zum Konfigurieren und Erstellen von <xref:System.Net.Http.HttpClient>-Instanzen in einer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-105">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="99b9d-106">`IHttpClientFactory` bietet die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="99b9d-106">`IHttpClientFactory` offers the following benefits:</span></span>

* <span data-ttu-id="99b9d-107">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-107">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="99b9d-108">Ein Client namens *github* kann beispielsweise registriert und für den Zugriff auf [GitHub](https://github.com/) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-108">For example, a client named  *github* could be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="99b9d-109">Ein Standardclient kann für den allgemeinen Zugriff registriert werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-109">A default client can be registered for general access.</span></span>
* <span data-ttu-id="99b9d-110">Das Konzept der ausgehenden Middleware wird über delegierende Handler in `HttpClient` in Code umgesetzt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-110">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient`.</span></span> <span data-ttu-id="99b9d-111">Außerdem werden Erweiterungen für auf Polly basierende Middleware bereitgestellt, um die delegierenden Handler in `HttpClient` zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-111">Provides extensions for Polly-based middleware to take advantage of delegating handlers in `HttpClient`.</span></span>
* <span data-ttu-id="99b9d-112">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-112">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances.</span></span> <span data-ttu-id="99b9d-113">Durch die automatische Verwaltung werden allgemeine DNS-Probleme (Domain Name System) vermieden, die bei der manuellen Verwaltung der Lebensdauer von `HttpClient` auftreten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-113">Automatic management avoids common DNS (Domain Name System) problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="99b9d-114">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-114">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="99b9d-115">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="99b9d-115">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="99b9d-116">Der Beispielcode in dieser Version des Themas verwendet <xref:System.Text.Json>, um JSON-Inhalte zu deserialisieren, die in HTTP-Antworten zurückgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-116">The sample code in this topic version uses <xref:System.Text.Json> to deserialize JSON content returned in HTTP responses.</span></span> <span data-ttu-id="99b9d-117">Verwenden Sie bei Beispielen, die `Json.NET` und `ReadAsAsync<T>` verwenden, die Versionsauswahl, um eine 2.x-Version dieses Themas auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-117">For samples that use `Json.NET` and `ReadAsAsync<T>`, use the version selector to select a 2.x version of this topic.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="99b9d-118">Verbrauchsmuster</span><span class="sxs-lookup"><span data-stu-id="99b9d-118">Consumption patterns</span></span>

<span data-ttu-id="99b9d-119">Es gibt mehrere Möglichkeiten `IHttpClientFactory` in einer App zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-119">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="99b9d-120">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="99b9d-120">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="99b9d-121">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-121">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="99b9d-122">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-122">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="99b9d-123">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-123">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="99b9d-124">Der beste Ansatz richtet sich nach den Anforderungen der App.</span><span class="sxs-lookup"><span data-stu-id="99b9d-124">The best approach depends upon the app's requirements.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="99b9d-125">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="99b9d-125">Basic usage</span></span>

<span data-ttu-id="99b9d-126">`IHttpClientFactory` kann durch Aufrufen von `AddHttpClient` registriert werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-126">`IHttpClientFactory` can be registered by calling `AddHttpClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="99b9d-127">Eine `IHttpClientFactory`-Schnittstelle kann mithilfe der [Dependency Injection (DI)](xref:fundamentals/dependency-injection) angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-127">An `IHttpClientFactory` can be requested using [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="99b9d-128">Im folgenden Code wird `IHttpClientFactory` verwendet, um eine `HttpClient`-Instanz zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="99b9d-128">The following code uses `IHttpClientFactory` to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="99b9d-129">`IHttpClientFactory` wie im vorhergehenden Beispiel zu verwenden ist eine gute Möglichkeit zum Umgestalten einer vorhandene App.</span><span class="sxs-lookup"><span data-stu-id="99b9d-129">Using `IHttpClientFactory` like in the preceding example is a good way to refactor an existing app.</span></span> <span data-ttu-id="99b9d-130">Dies hat keine Auswirkung auf die Verwendung von `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="99b9d-130">It has no impact on how `HttpClient` is used.</span></span> <span data-ttu-id="99b9d-131">An Stellen, an denen `HttpClient`-Instanzen in einer vorhandenen App erstellt werden, können Sie diese Ereignisse mit Aufrufen von <xref:System.Net.Http.IHttpClientFactory.CreateClient*> ersetzen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-131">In places where `HttpClient` instances are created in an existing app, replace those occurrences with calls to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="99b9d-132">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-132">Named clients</span></span>

<span data-ttu-id="99b9d-133">Benannte Clients sind in folgenden Fällen eine gute Wahl:</span><span class="sxs-lookup"><span data-stu-id="99b9d-133">Named clients are a good choice when:</span></span>

* <span data-ttu-id="99b9d-134">Die App erfordert viele verschiedene Verwendungen von `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="99b9d-134">The app requires many distinct uses of `HttpClient`.</span></span>
* <span data-ttu-id="99b9d-135">Viele `HttpClient`s verfügen über unterschiedliche Konfigurationen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-135">Many `HttpClient`s have different configuration.</span></span>

<span data-ttu-id="99b9d-136">Die Konfiguration eines benannten `HttpClient` kann während der Registrierung in `Startup.ConfigureServices` angegeben werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-136">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="99b9d-137">Im vorangehenden Code wird der Client mit Folgendem konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="99b9d-137">In the preceding code the client is configured with:</span></span>

* <span data-ttu-id="99b9d-138">der Basisadresse `https://api.github.com/`</span><span class="sxs-lookup"><span data-stu-id="99b9d-138">The base address `https://api.github.com/`.</span></span>
* <span data-ttu-id="99b9d-139">zwei Header, die für die Arbeit mit der GitHub-API erforderlich sind</span><span class="sxs-lookup"><span data-stu-id="99b9d-139">Two headers required to work with the GitHub API.</span></span>

#### <a name="createclient"></a><span data-ttu-id="99b9d-140">CreateClient</span><span class="sxs-lookup"><span data-stu-id="99b9d-140">CreateClient</span></span>

<span data-ttu-id="99b9d-141">Jedes Mal, wenn <xref:System.Net.Http.IHttpClientFactory.CreateClient*> aufgerufen wird, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="99b9d-141">Each time <xref:System.Net.Http.IHttpClientFactory.CreateClient*> is called:</span></span>

* <span data-ttu-id="99b9d-142">Eine neue Instanz von `HttpClient` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-142">A new instance of `HttpClient` is created.</span></span>
* <span data-ttu-id="99b9d-143">Die Konfigurationsaktion wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-143">The configuration action is called.</span></span>

<span data-ttu-id="99b9d-144">Übergeben Sie für die Erstellung eines benannten Clients dessen Namen an `CreateClient`:</span><span class="sxs-lookup"><span data-stu-id="99b9d-144">To create a named client, pass its name into `CreateClient`:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="99b9d-145">Im vorangehenden Code muss die Anforderung keinen Hostnamen angeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-145">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="99b9d-146">Der Code muss nur den Pfad übergeben, da die für den Client konfigurierte Basisadresse verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-146">The code can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="99b9d-147">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-147">Typed clients</span></span>

<span data-ttu-id="99b9d-148">Typisierte Clients:</span><span class="sxs-lookup"><span data-stu-id="99b9d-148">Typed clients:</span></span>

* <span data-ttu-id="99b9d-149">Stellen dieselben Funktionen wie benannte Clients bereit, ohne Zeichenfolgen als Schlüssel verwenden zu müssen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-149">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="99b9d-150">Bieten Hilfe durch IntelliSense und Compiler beim Verarbeiten von Clients.</span><span class="sxs-lookup"><span data-stu-id="99b9d-150">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="99b9d-151">Stellen einen einzelnen Ort zum Konfigurieren und Interagieren mit einem bestimmten `HttpClient` bereit.</span><span class="sxs-lookup"><span data-stu-id="99b9d-151">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="99b9d-152">Beispielsweise kann ein einzelner typisierter Client für Folgendes verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-152">For example, a single typed client might be used:</span></span>
  * <span data-ttu-id="99b9d-153">für einen einzelnen Back-End-Endpunkt</span><span class="sxs-lookup"><span data-stu-id="99b9d-153">For a single backend endpoint.</span></span>
  * <span data-ttu-id="99b9d-154">um die gesamte Logik zu kapseln, die den Endpunkt behandelt</span><span class="sxs-lookup"><span data-stu-id="99b9d-154">To encapsulate all logic dealing with the endpoint.</span></span>
* <span data-ttu-id="99b9d-155">Funktionieren mit DI und können überall in der App eingefügt werden, wo sie benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-155">Work with DI and can be injected where required in the app.</span></span>

<span data-ttu-id="99b9d-156">Ein typisierter Client akzeptiert einen `HttpClient`-Parameter in seinem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="99b9d-156">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="99b9d-157">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="99b9d-157">In the preceding code:</span></span>

* <span data-ttu-id="99b9d-158">Die Konfiguration wird in den typisierten Client verschoben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-158">The configuration is moved into the typed client.</span></span>
* <span data-ttu-id="99b9d-159">Das `HttpClient`-Objekt wird als öffentliche Eigenschaft zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-159">The `HttpClient` object is exposed as a public property.</span></span>

<span data-ttu-id="99b9d-160">Es können API-spezifische Methoden erstellt werden, die die `HttpClient`-Funktionalität verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-160">API-specific methods can be created that expose `HttpClient` functionality.</span></span> <span data-ttu-id="99b9d-161">Die `GetAspNetDocsIssues`-Methode kapselt z. B. Code zum Abrufen offener Probleme.</span><span class="sxs-lookup"><span data-stu-id="99b9d-161">For example, the `GetAspNetDocsIssues` method encapsulates code to retrieve open issues.</span></span>

<span data-ttu-id="99b9d-162">Der folgende Code ruft <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` auf, um eine typisierte Clientklasse zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="99b9d-162">The following code calls <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> in `Startup.ConfigureServices` to register a typed client class:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="99b9d-163">Der typisierte Client wird mit DI als „vorübergehend“ registriert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-163">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="99b9d-164">Im vorherigen Code registriert `AddHttpClient` `GitHubService` als vorübergehenden Dienst.</span><span class="sxs-lookup"><span data-stu-id="99b9d-164">In the preceding code, `AddHttpClient` registers `GitHubService` as a transient service.</span></span> <span data-ttu-id="99b9d-165">Diese Registrierung verwendet eine Factorymethode für folgende Aktionen:</span><span class="sxs-lookup"><span data-stu-id="99b9d-165">This registration uses a factory method to:</span></span>

1. <span data-ttu-id="99b9d-166">Erstellen Sie eine Instanz von `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="99b9d-166">Create an instance of `HttpClient`.</span></span>
1. <span data-ttu-id="99b9d-167">Erstellen einer `GitHubService`-Instanz, wobei die Instanz von `HttpClient` an ihren Konstrukt übergeben wird</span><span class="sxs-lookup"><span data-stu-id="99b9d-167">Create an instance of `GitHubService`, passing in the instance of `HttpClient` to its constructor.</span></span>

<span data-ttu-id="99b9d-168">Der typisierte Client kann direkt eingefügt und verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-168">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="99b9d-169">Die Konfiguration kann für einen typisierten Client während der Registrierung in `Startup.ConfigureServices` angegeben werden, anstatt im Konstruktor des typisierten Clients:</span><span class="sxs-lookup"><span data-stu-id="99b9d-169">The configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="99b9d-170">`HttpClient` kann in einem typisierten Client gekapselt werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-170">The `HttpClient` can be encapsulated within a typed client.</span></span> <span data-ttu-id="99b9d-171">Anstatt ihn als eine Eigenschaft zur Verfügung zu stellen, definieren Sie eine Methode, die die `HttpClient`-Instanz intern aufruft:</span><span class="sxs-lookup"><span data-stu-id="99b9d-171">Rather than exposing it as a property, define a method which calls the `HttpClient` instance internally:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="99b9d-172">Im vorangehenden Code wird `HttpClient` in einem privaten Feld gespeichert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-172">In the preceding code, the `HttpClient` is stored in a private field.</span></span> <span data-ttu-id="99b9d-173">Der Zugriff auf `HttpClient` erfolgt durch die öffentliche `GetRepos`-Methode.</span><span class="sxs-lookup"><span data-stu-id="99b9d-173">Access to the `HttpClient` is by the public `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="99b9d-174">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-174">Generated clients</span></span>

<span data-ttu-id="99b9d-175">`IHttpClientFactory` kann in Verbindung mit Bibliotheken von Drittanbietern verwendet werden, z. B. [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="99b9d-175">`IHttpClientFactory` can be used in combination with third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="99b9d-176">Refit ist eine REST-Bibliothek für .NET.</span><span class="sxs-lookup"><span data-stu-id="99b9d-176">Refit is a REST library for .NET.</span></span> <span data-ttu-id="99b9d-177">Sie konvertiert REST-APIs in Live-Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-177">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="99b9d-178">Eine Implementierung der Schnittstelle wird dynamisch von `RestService` generiert. `HttpClient` wird für die externen HTTP-Aufrufe verwendet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-178">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="99b9d-179">Eine Schnittstelle und eine Antwort werden definiert, um die externe API und die Antwort darzustellen:</span><span class="sxs-lookup"><span data-stu-id="99b9d-179">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="99b9d-180">Ein typisierter Client kann hinzugefügt werden. Refit wird zum Generieren der Implementierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="99b9d-180">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="99b9d-181">Die definierte Schnittstelle kann bei Bedarf mit der von DI und Refit bereitgestellten Implementierung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-181">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="99b9d-182">Middleware für ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="99b9d-182">Outgoing request middleware</span></span>

<span data-ttu-id="99b9d-183">`HttpClient` enthält das Konzept, Handler zu delegieren, die für ausgehende HTTP-Anforderungen miteinander verknüpft werden können.</span><span class="sxs-lookup"><span data-stu-id="99b9d-183">`HttpClient` has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="99b9d-184">`IHttpClientFactory`:</span><span class="sxs-lookup"><span data-stu-id="99b9d-184">`IHttpClientFactory`:</span></span>

* <span data-ttu-id="99b9d-185">Erleichtert das Definieren der Handler, die für jeden benannten Client angewendet werden</span><span class="sxs-lookup"><span data-stu-id="99b9d-185">Simplifies defining the handlers to apply for each named client.</span></span>
* <span data-ttu-id="99b9d-186">Unterstützt die Registrierung und Verkettung von mehreren Handlern, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-186">Supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="99b9d-187">Jeder dieser Handler kann vor und nach der ausgehenden Anforderung Aufgaben ausführen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-187">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="99b9d-188">Dieses Muster:</span><span class="sxs-lookup"><span data-stu-id="99b9d-188">This pattern:</span></span>

  * <span data-ttu-id="99b9d-189">ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="99b9d-189">Is similar to the inbound middleware pipeline in ASP.NET Core.</span></span>
  * <span data-ttu-id="99b9d-190">bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, z. B.:</span><span class="sxs-lookup"><span data-stu-id="99b9d-190">Provides a mechanism to manage cross-cutting concerns around HTTP requests, such as:</span></span>

    * <span data-ttu-id="99b9d-191">Zwischenspeicherung</span><span class="sxs-lookup"><span data-stu-id="99b9d-191">caching</span></span>
    * <span data-ttu-id="99b9d-192">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="99b9d-192">error handling</span></span>
    * <span data-ttu-id="99b9d-193">Serialisierung</span><span class="sxs-lookup"><span data-stu-id="99b9d-193">serialization</span></span>
    * <span data-ttu-id="99b9d-194">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="99b9d-194">logging</span></span>

<span data-ttu-id="99b9d-195">So erstellen Sie einen delegierenden Handler:</span><span class="sxs-lookup"><span data-stu-id="99b9d-195">To create a delegating handler:</span></span>

* <span data-ttu-id="99b9d-196">Leiten Sie von <xref:System.Net.Http.DelegatingHandler> ab.</span><span class="sxs-lookup"><span data-stu-id="99b9d-196">Derive from <xref:System.Net.Http.DelegatingHandler>.</span></span>
* <span data-ttu-id="99b9d-197">Überschreiben Sie <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span><span class="sxs-lookup"><span data-stu-id="99b9d-197">Override <xref:System.Net.Http.DelegatingHandler.SendAsync*>.</span></span> <span data-ttu-id="99b9d-198">Führen Sie Code aus, bevor die Anforderung an den nächsten Handler in der Pipeline übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="99b9d-198">Execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="99b9d-199">Der vorangehende Code überprüft, ob die Anforderung einen `X-API-KEY`-Header enthält.</span><span class="sxs-lookup"><span data-stu-id="99b9d-199">The preceding code checks if the `X-API-KEY` header is in the request.</span></span> <span data-ttu-id="99b9d-200">Wenn `X-API-KEY` fehlt, wird <xref:System.Net.HttpStatusCode.BadRequest> zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-200">If `X-API-KEY` is missing, <xref:System.Net.HttpStatusCode.BadRequest> is returned.</span></span>

<span data-ttu-id="99b9d-201">Für eine `HttpClient`-Klasse mit <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName> kann mehr als ein Handler zur Konfiguration hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-201">More than one handler can be added to the configuration for an `HttpClient` with <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*?displayProperty=fullName>:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup2.cs?name=snippet1)]

<span data-ttu-id="99b9d-202">Im vorangehenden Code ist `ValidateHeaderHandler` mit DI registriert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-202">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="99b9d-203">Die `IHttpClientFactory` erstellt einen separaten DI-Bereich für jeden Handler.</span><span class="sxs-lookup"><span data-stu-id="99b9d-203">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="99b9d-204">Handler können von Diensten eines beliebigen Bereichs abhängen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-204">Handlers can depend upon services of any scope.</span></span> <span data-ttu-id="99b9d-205">Dienste, von denen Handler abhängig sind, werden verworfen, wenn der Handler verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-205">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="99b9d-206">Nach der Registrierung kann <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> aufgerufen werden, was den Typ für den Handler übergibt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-206">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="99b9d-207">Mehrere Handler können in der Reihenfolge registriert werden, in der sie ausgeführt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-207">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="99b9d-208">Jeder Handler umschließt den nächsten Handler, bis der endgültige `HttpClientHandler` die Anforderung ausführt:</span><span class="sxs-lookup"><span data-stu-id="99b9d-208">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="99b9d-209">Verwenden Sie einen der folgenden Ansätze, um den anforderungsspezifischen Zustand mit Meldungshandlern zu teilen:</span><span class="sxs-lookup"><span data-stu-id="99b9d-209">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="99b9d-210">Übergeben Sie mithilfe von [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties) Daten an den Handler.</span><span class="sxs-lookup"><span data-stu-id="99b9d-210">Pass data into the handler using [HttpRequestMessage.Properties](xref:System.Net.Http.HttpRequestMessage.Properties).</span></span>
* <span data-ttu-id="99b9d-211">Greifen Sie mit <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> auf die aktuelle Anforderung zu.</span><span class="sxs-lookup"><span data-stu-id="99b9d-211">Use <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> to access the current request.</span></span>
* <span data-ttu-id="99b9d-212">Erstellen Sie ein benutzerdefiniertes <xref:System.Threading.AsyncLocal`1>-Speicherobjekt, um die Daten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-212">Create a custom <xref:System.Threading.AsyncLocal`1> storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="99b9d-213">Verwenden von Polly-basierten Handlern</span><span class="sxs-lookup"><span data-stu-id="99b9d-213">Use Polly-based handlers</span></span>

<span data-ttu-id="99b9d-214">`IHttpClientFactory` ist mit der Drittanbieterbibliothek [Polly](https://github.com/App-vNext/Polly) integriert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-214">`IHttpClientFactory` integrates with the third-party library [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="99b9d-215">Polly ist eine umfassende Bibliothek für die Behandlung von beständigen und vorübergehenden Fehlern für .NET.</span><span class="sxs-lookup"><span data-stu-id="99b9d-215">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="99b9d-216">Entwicklern wird damit ermöglicht, Richtlinien wie Wiederholungsrichtlinien, Trennschalterrichtlinien, Timeout-Richtlinien, Bulkhead Isolation-Richtlinien und Ausweichrichtlinien in einer flüssigen und threadsicheren Art und Weise auszudrücken.</span><span class="sxs-lookup"><span data-stu-id="99b9d-216">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="99b9d-217">Erweiterungsmethoden werden bereitgestellt, um die Verwendung von Polly-Richtlinien mit konfigurierten `HttpClient`-Instanzen zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="99b9d-217">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="99b9d-218">Die Polly-Erweiterungen unterstützen das Hinzufügen von Polly-basierten Handlern zu Clients.</span><span class="sxs-lookup"><span data-stu-id="99b9d-218">The Polly extensions support adding Polly-based handlers to clients.</span></span> <span data-ttu-id="99b9d-219">Polly erfordert das [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/)-NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="99b9d-219">Polly requires the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="99b9d-220">Behandeln von vorrübergehenden Fehlern</span><span class="sxs-lookup"><span data-stu-id="99b9d-220">Handle transient faults</span></span>

<span data-ttu-id="99b9d-221">Fehler treten normalerweise auf, wenn externe HTTP-Aufrufe vorübergehend sind.</span><span class="sxs-lookup"><span data-stu-id="99b9d-221">Faults typically occur when external HTTP calls are transient.</span></span> <span data-ttu-id="99b9d-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> ermöglicht die Definition einer Richtlinie, um vorübergehende Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="99b9d-222"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="99b9d-223">Mit `AddTransientHttpErrorPolicy` konfigurierte Richtlinien behandeln die folgenden Antworten:</span><span class="sxs-lookup"><span data-stu-id="99b9d-223">Policies configured with `AddTransientHttpErrorPolicy` handle the following responses:</span></span>

* <xref:System.Net.Http.HttpRequestException>
* <span data-ttu-id="99b9d-224">HTTP 5xx</span><span class="sxs-lookup"><span data-stu-id="99b9d-224">HTTP 5xx</span></span>
* <span data-ttu-id="99b9d-225">HTTP 408</span><span class="sxs-lookup"><span data-stu-id="99b9d-225">HTTP 408</span></span>

<span data-ttu-id="99b9d-226">`AddTransientHttpErrorPolicy` ermöglicht den Zugriff auf ein `PolicyBuilder`-Objekt, das für die Fehlerbehandlung konfiguriert wurde und mögliche vorübergehende Fehler darstellt:</span><span class="sxs-lookup"><span data-stu-id="99b9d-226">`AddTransientHttpErrorPolicy` provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup3.cs?name=snippet1)]

<span data-ttu-id="99b9d-227">Im vorangehenden Code wird eine `WaitAndRetryAsync`-Richtlinie definiert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-227">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="99b9d-228">Anforderungsfehler werden bis zu dreimal mit einer Verzögerung von 600 ms wiederholt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-228">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="99b9d-229">Dynamisches Auswählen von Richtlinien</span><span class="sxs-lookup"><span data-stu-id="99b9d-229">Dynamically select policies</span></span>

<span data-ttu-id="99b9d-230">Erweiterungsmethoden werden zum Hinzufügen von Polly-basierten Handlern bereitgestellt, z. B. <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span><span class="sxs-lookup"><span data-stu-id="99b9d-230">Extension methods are provided to add Polly-based handlers, for example, <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandler*>.</span></span> <span data-ttu-id="99b9d-231">Der folgende `AddPolicyHandler`-Überladung prüft die Anforderung, um zu entscheiden, welche Richtlinie angewendet werden soll:</span><span class="sxs-lookup"><span data-stu-id="99b9d-231">The following `AddPolicyHandler` overload inspects the request to decide which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="99b9d-232">Im vorangehenden Code wird ein 10 Sekunden langer Timeout angewendet, wenn die ausgehende Anforderung eine HTTP GET-Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="99b9d-232">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="99b9d-233">Für alle anderen HTTP-Methoden wird ein 30 Sekunden langer Timeout verwendet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-233">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="99b9d-234">Hinzufügen mehrerer Polly-Handler</span><span class="sxs-lookup"><span data-stu-id="99b9d-234">Add multiple Polly handlers</span></span>

<span data-ttu-id="99b9d-235">Es ist üblich, Polly-Richtlinien zu schachteln:</span><span class="sxs-lookup"><span data-stu-id="99b9d-235">It's common to nest Polly policies:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="99b9d-236">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="99b9d-236">In the preceding example:</span></span>

* <span data-ttu-id="99b9d-237">Zwei Handler werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-237">Two handlers are added.</span></span>
* <span data-ttu-id="99b9d-238">Der erste Handler verwendet <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*>, um eine Wiederholungsrichtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-238">The first handler uses <xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddTransientHttpErrorPolicy*> to add a retry policy.</span></span> <span data-ttu-id="99b9d-239">Fehlgeschlagene Anforderungen werden bis zu drei Mal wiederholt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-239">Failed requests are retried up to three times.</span></span>
* <span data-ttu-id="99b9d-240">Der zweite `AddTransientHttpErrorPolicy`-Aufruf fügt eine Trennschalterrichtlinie hinzu.</span><span class="sxs-lookup"><span data-stu-id="99b9d-240">The second `AddTransientHttpErrorPolicy` call adds a circuit breaker policy.</span></span> <span data-ttu-id="99b9d-241">Weitere externe Anforderungen werden 30 Sekunden lang blockiert, wenn fünf Fehlversuche hintereinander stattfinden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-241">Further external requests are blocked for 30 seconds if 5 failed attempts occur sequentially.</span></span> <span data-ttu-id="99b9d-242">Trennschalterrichtlinien sind zustandsbehaftet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-242">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="99b9d-243">Alle Aufrufe über diesen Client teilen den gleichen Schalterzustand.</span><span class="sxs-lookup"><span data-stu-id="99b9d-243">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="99b9d-244">Hinzufügen von Richtlinien aus der Polly-Registrierung</span><span class="sxs-lookup"><span data-stu-id="99b9d-244">Add policies from the Polly registry</span></span>

<span data-ttu-id="99b9d-245">Eine Methode zum Verwalten regelmäßig genutzter Richtlinien ist, Sie einmal zu definieren und mit `PolicyRegistry` zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="99b9d-245">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span>

<span data-ttu-id="99b9d-246">Im folgenden Code wird Folgendes ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="99b9d-246">In the following code:</span></span>

* <span data-ttu-id="99b9d-247">Die Richtlinien "regular" und "long" werden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-247">The "regular" and "long" polices are added.</span></span>
* <span data-ttu-id="99b9d-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*> fügt die Richtlinien "regular" und "long" aus der Registrierung hinzu.</span><span class="sxs-lookup"><span data-stu-id="99b9d-248"><xref:Microsoft.Extensions.DependencyInjection.PollyHttpClientBuilderExtensions.AddPolicyHandlerFromRegistry*>  adds the "regular" and "long" policies from the registry.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup4.cs?name=snippet1)]

<span data-ttu-id="99b9d-249">Weitere Informationen zu `IHttpClientFactory` und Polly-Integrationen finden Sie im [Polly-Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="99b9d-249">For more information on `IHttpClientFactory` and Polly integrations, see the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="99b9d-250">HttpClient und die Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="99b9d-250">HttpClient and lifetime management</span></span>

<span data-ttu-id="99b9d-251">Bei jedem Aufruf von `CreateClient` in der `IHttpClientFactory` wird eine neue Instanz von `HttpClient` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-251">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="99b9d-252">Pro benannter Client wird ein <xref:System.Net.Http.HttpMessageHandler> erstellt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-252">An <xref:System.Net.Http.HttpMessageHandler> is created per named client.</span></span> <span data-ttu-id="99b9d-253">Die Factory verwaltet die Lebensdauer der `HttpMessageHandler`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-253">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="99b9d-254">`IHttpClientFactory` legt die `HttpMessageHandler`-Instanzen zusammen, die von der Factory zum Reduzieren des Ressourcenverbrauchs erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-254">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="99b9d-255">Eine `HttpMessageHandler`-Instanz kann aus dem Pool wiederverwendet werden, wenn eine neue `HttpClient`-Instanz erstellt wird und deren Lebensdauer noch nicht abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="99b9d-255">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="99b9d-256">Das Zusammenlegen von Handlern ist ein wünschenswerter Vorgang, da jeder Handler in der Regel seine zugrunde liegenden HTTP-Verbindungen selbst verwaltet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-256">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="99b9d-257">Wenn mehr Handler als nötig erstellt werden, können Verzögerungen bei Verbindungen entstehen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-257">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="99b9d-258">Einige Handler halten Verbindungen auch unbegrenzt offen, was verhindert, dass der Handler auf DNS-Änderungen (Domain Name System) reagiert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-258">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS (Domain Name System) changes.</span></span>

<span data-ttu-id="99b9d-259">Die Standardlebensdauer von Handlern beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-259">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="99b9d-260">Der Standardwert kann für jeden benannten Client überschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-260">The default value can be overridden on a per named client basis:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup5.cs?name=snippet1)]

<span data-ttu-id="99b9d-261">`HttpClient`-Instanzen können im Allgemeinen als .NET-Objekte behandelt werden, die **nicht** verworfen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-261">`HttpClient` instances can generally be treated as .NET objects **not** requiring disposal.</span></span> <span data-ttu-id="99b9d-262">Beim Verwerfen werden ausgehende Anforderungen abgebrochen, und es wird sichergestellt, dass die angegebene `HttpClient`-Instanz nach dem Aufruf von <xref:System.IDisposable.Dispose*> nicht mehr verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="99b9d-262">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="99b9d-263">`IHttpClientFactory` verfolgt von `HttpClient`-Instanzen verwendete Ressourcen nach und verwirft sie.</span><span class="sxs-lookup"><span data-stu-id="99b9d-263">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span>

<span data-ttu-id="99b9d-264">Das Beibehalten einer einzelnen `HttpClient`-Instanz für einen langen Zeitraum ist ein allgemeines Muster, das vor der Einführung von `IHttpClientFactory` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="99b9d-264">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="99b9d-265">Dieses Muster wird nach der Migration zu `IHttpClientFactory` überflüssig.</span><span class="sxs-lookup"><span data-stu-id="99b9d-265">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="99b9d-266">Alternativen zu IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="99b9d-266">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="99b9d-267">Mit der Verwendung von `IHttpClientFactory` in einer DI-fähigen App wird Folgendes vermieden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-267">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="99b9d-268">Probleme mit der Ressourcenauslastung durch Zusammenlegen von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="99b9d-268">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="99b9d-269">Probleme durch veraltetes DNS durch regelmäßigen Wechsel von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="99b9d-269">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="99b9d-270">Es gibt alternative Möglichkeiten zum Lösen des vorangehenden Problems mithilfe einer langlebigen <xref:System.Net.Http.SocketsHttpHandler>-Instanz:</span><span class="sxs-lookup"><span data-stu-id="99b9d-270">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="99b9d-271">Erstellen Sie eine `SocketsHttpHandler`-Instanz, wenn die App gestartet wird, und verwenden Sie diese für die Lebensdauer der App.</span><span class="sxs-lookup"><span data-stu-id="99b9d-271">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="99b9d-272">Konfigurieren Sie einen geeigneten Wert für <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> basierend auf den DNS-Aktualisierungszeiten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-272">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="99b9d-273">Erstellen Sie bei Bedarf `HttpClient`-Instanzen mithilfe von `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="99b9d-273">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="99b9d-274">Diese Ansätze lösen die Ressourcenverwaltungsprobleme, die `IHttpClientFactory` auf ähnliche Weise löst.</span><span class="sxs-lookup"><span data-stu-id="99b9d-274">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="99b9d-275">`SocketsHttpHandler` gibt Verbindungen für `HttpClient`-Instanzen frei.</span><span class="sxs-lookup"><span data-stu-id="99b9d-275">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="99b9d-276">Durch diese Freigabe wird die Erschöpfung von Sockets vermieden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-276">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="99b9d-277">`SocketsHttpHandler` wechselt die Verbindungen anhand von `PooledConnectionLifetime`, um Probleme durch veraltetes DNS zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-277">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="99b9d-278">Cookies</span><span class="sxs-lookup"><span data-stu-id="99b9d-278">Cookies</span></span>

<span data-ttu-id="99b9d-279">Die zusammengelegten `HttpMessageHandler`-Instanzen resultieren in der Freigabe von `CookieContainer`-Objekten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-279">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="99b9d-280">Die unerwartete Freigabe von `CookieContainer`-Objekten führt oft zu fehlerhaftem Code.</span><span class="sxs-lookup"><span data-stu-id="99b9d-280">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="99b9d-281">Ziehen Sie für Apps, die Cookies erfordern, folgende Vorgehensweisen in Betracht:</span><span class="sxs-lookup"><span data-stu-id="99b9d-281">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="99b9d-282">Deaktivieren der automatischen Cookieverarbeitung</span><span class="sxs-lookup"><span data-stu-id="99b9d-282">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="99b9d-283">Vermeiden der Verwendung von `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="99b9d-283">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="99b9d-284">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> auf, um die automatische Cookieverarbeitung zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="99b9d-284">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="99b9d-285">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="99b9d-285">Logging</span></span>

<span data-ttu-id="99b9d-286">Über `IHttpClientFactory` erstellte Clients zeichnen Protokollmeldungen für alle Anforderungen auf.</span><span class="sxs-lookup"><span data-stu-id="99b9d-286">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="99b9d-287">Aktivieren Sie die entsprechende Informationsebene in der Protokollierungskonfiguration, um die Standardprotokollmeldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-287">Enable the appropriate information level in the logging configuration to see the default log messages.</span></span> <span data-ttu-id="99b9d-288">Zusätzliche Protokollierung, z.B. das Protokollieren von Anforderungsheadern, wird nur auf der Ablaufverfolgungsebene enthalten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-288">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="99b9d-289">Die Protokollierungskategorie für jeden Client enthält den Namen des Clients.</span><span class="sxs-lookup"><span data-stu-id="99b9d-289">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="99b9d-290">Ein Client namens *MyNamedClient* protokolliert z. B. Nachrichten mit der Kategorie „System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler“.</span><span class="sxs-lookup"><span data-stu-id="99b9d-290">A client named *MyNamedClient*, for example, logs messages with a category of "System.Net.Http.HttpClient.**MyNamedClient**.LogicalHandler".</span></span> <span data-ttu-id="99b9d-291">Meldungen mit dem Suffix *LogicalHandler* treten außerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="99b9d-291">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="99b9d-292">In der Anforderung werden Meldungen protokolliert, bevor andere Handler in der Pipeline sie verarbeitet haben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-292">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="99b9d-293">In der Antwort werden Meldungen protokolliert, nachdem andere Handler in der Pipeline die Antwort empfangen haben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-293">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="99b9d-294">Die Protokollierung tritt ebenfalls innerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="99b9d-294">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="99b9d-295">Im Beispiel von *MyNamedClient* werden diese Nachrichten mit der Protokollkategorie „System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler“ protokolliert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-295">In the *MyNamedClient* example, those messages are logged with the log category "System.Net.Http.HttpClient.**MyNamedClient**.ClientHandler".</span></span> <span data-ttu-id="99b9d-296">Für die Anforderung erfolgt dies, nachdem alle anderen Handler ausgeführt wurden und bevor die Anforderung gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-296">For the request, this occurs after all other handlers have run and immediately before the request is sent.</span></span> <span data-ttu-id="99b9d-297">Diese Protokollierung enthält den Zustand der Antwort in der Antwort, bevor sie an die Handlerpipeline zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-297">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="99b9d-298">Das Aktivieren der Protokollierung außerhalb und innerhalb der Pipeline ermöglicht die Überprüfung der Änderungen, die durch andere Handler in der Pipeline erfolgt sind.</span><span class="sxs-lookup"><span data-stu-id="99b9d-298">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="99b9d-299">Dies kann die Änderungen an Anforderungsheadern oder am Statuscode der Antwort enthalten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-299">This may include changes to request headers or to the response status code.</span></span>

<span data-ttu-id="99b9d-300">Das Einschließen des Namens des Clients in der Protokollkategorie aktiviert die Protokollfilterung für spezifische benannte Clients.</span><span class="sxs-lookup"><span data-stu-id="99b9d-300">Including the name of the client in the log category enables log filtering for specific named clients.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="99b9d-301">Konfigurieren von HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="99b9d-301">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="99b9d-302">Es kann notwendig sein, die Konfiguration des inneren von einem Client verwendeten `HttpMessageHandler` zu steuern.</span><span class="sxs-lookup"><span data-stu-id="99b9d-302">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="99b9d-303">`IHttpClientBuilder` wird zurückgegeben, wenn benannte oder typisierte Clients hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-303">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="99b9d-304">Die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> kann zum Definieren eines Delegaten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-304">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="99b9d-305">Der Delegat wird verwendet, um den primären `HttpMessageHandler` zu erstellen und konfigurieren, der von dem Client verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="99b9d-305">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactorySample/Startup6.cs?name=snippet1)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="99b9d-306">Verwenden von IHttpClientFactory in einer Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="99b9d-306">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="99b9d-307">Fügen Sie in einer Konsolen-App dem Projekt die folgenden Paketverweise hinzu:</span><span class="sxs-lookup"><span data-stu-id="99b9d-307">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="99b9d-308">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="99b9d-308">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="99b9d-309">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="99b9d-309">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="99b9d-310">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="99b9d-310">In the following example:</span></span>

* <span data-ttu-id="99b9d-311"><xref:System.Net.Http.IHttpClientFactory> ist im Dienstcontainer des [generischen Hosts](xref:fundamentals/host/generic-host) registriert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-311"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="99b9d-312">`MyService` erstellt eine Clientfactoryinstanz aus dem-Dienst, der zum Erstellen eines `HttpClient`verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-312">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="99b9d-313">`HttpClient` wird zum Abrufen einer Webseite verwendet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-313">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="99b9d-314">`Main` erstellt einen Bereich, um die `GetPage`-Methode des Diensts auszuführen und die ersten 500 Zeichen des Webseiteninhalts in die Konsole zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-314">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="99b9d-315">Middleware für Headerweitergabe</span><span class="sxs-lookup"><span data-stu-id="99b9d-315">Header propagation middleware</span></span>

<span data-ttu-id="99b9d-316">Für die Headerweitergabe wird eine ASP.NET Core-Middleware verwendet, um HTTP-Header von eingehenden Anforderungen an ausgehende HTTP-Clientanforderungen weiterzugeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-316">Header propagation is an ASP.NET Core middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="99b9d-317">So verwenden Sie die Headerweitergabe:</span><span class="sxs-lookup"><span data-stu-id="99b9d-317">To use header propagation:</span></span>

* <span data-ttu-id="99b9d-318">Erstellen Sie einen Verweis auf das [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation)-Paket.</span><span class="sxs-lookup"><span data-stu-id="99b9d-318">Reference the [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation) package.</span></span>
* <span data-ttu-id="99b9d-319">Konfigurieren Sie in `Startup` die Middleware und `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="99b9d-319">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/3.x/Startup.cs?highlight=5-9,21&name=snippet)]

* <span data-ttu-id="99b9d-320">Der Client schließt die konfigurierten Header für ausgehende Anforderungen ein:</span><span class="sxs-lookup"><span data-stu-id="99b9d-320">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="99b9d-321">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="99b9d-321">Additional resources</span></span>

* [<span data-ttu-id="99b9d-322">Verwenden von HttpClientFactory zur Implementierung robuster HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="99b9d-322">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="99b9d-323">Implementieren von Wiederholungen von HTTP-Aufrufen mit exponentiellem Backoff mit HttpClientFactory und Polly-Richtlinien</span><span class="sxs-lookup"><span data-stu-id="99b9d-323">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="99b9d-324">Implementieren des Trennschaltermusters</span><span class="sxs-lookup"><span data-stu-id="99b9d-324">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
* [<span data-ttu-id="99b9d-325">Serialisieren und Deserialisieren von JSON-Daten in .NET</span><span class="sxs-lookup"><span data-stu-id="99b9d-325">How to serialize and deserialize JSON in .NET</span></span>](/dotnet/standard/serialization/system-text-json-how-to)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="99b9d-326">Von [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), und [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="99b9d-326">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="99b9d-327"><xref:System.Net.Http.IHttpClientFactory> kann registriert und zum Konfigurieren und Erstellen von <xref:System.Net.Http.HttpClient>-Instanzen in einer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-327">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="99b9d-328">Dies hat folgende Vorteile:</span><span class="sxs-lookup"><span data-stu-id="99b9d-328">It offers the following benefits:</span></span>

* <span data-ttu-id="99b9d-329">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-329">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="99b9d-330">Ein *GitHub*-Client kann beispielsweise registriert werden und so konfiguriert werden, um auf [GitHub](https://github.com/) zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-330">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="99b9d-331">Ein Standard-Client kann für andere Zwecke registriert werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-331">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="99b9d-332">Das Konzept der ausgehenden Middleware wird über delegierende Handler in `HttpClient` in Code umgesetzt. Außerdem werden Erweiterungen für auf Polly basierende Middleware bereitgestellt, die dies nutzen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-332">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="99b9d-333">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-333">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="99b9d-334">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-334">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="99b9d-335">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="99b9d-335">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="99b9d-336">Verbrauchsmuster</span><span class="sxs-lookup"><span data-stu-id="99b9d-336">Consumption patterns</span></span>

<span data-ttu-id="99b9d-337">Es gibt mehrere Möglichkeiten `IHttpClientFactory` in einer App zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-337">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="99b9d-338">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="99b9d-338">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="99b9d-339">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-339">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="99b9d-340">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-340">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="99b9d-341">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-341">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="99b9d-342">Keine dieser Möglichkeiten ist einer anderen überlegen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-342">None of them are strictly superior to another.</span></span> <span data-ttu-id="99b9d-343">Der beste Ansatz richtet sich nach den Einschränkungen der App.</span><span class="sxs-lookup"><span data-stu-id="99b9d-343">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="99b9d-344">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="99b9d-344">Basic usage</span></span>

<span data-ttu-id="99b9d-345">`IHttpClientFactory` kann durch Aufrufen der Erweiterungsmethode `AddHttpClient` auf `IServiceCollection` in der Methode `Startup.ConfigureServices` registriert werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-345">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="99b9d-346">Nach der Registrierung kann Code `IHttpClientFactory` überall akzeptieren, wo Dienste mithilfe der [Dependency Injection](xref:fundamentals/dependency-injection) (DI) eingefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="99b9d-346">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="99b9d-347">`IHttpClientFactory` kann zum Erstellen einer `HttpClient`-Instanz verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-347">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="99b9d-348">`IHttpClientFactory` auf diese Weise zu verwenden, ist eine gute Möglichkeit zum Umgestalten einer vorhandenen App.</span><span class="sxs-lookup"><span data-stu-id="99b9d-348">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="99b9d-349">Dies hat keine Auswirkung auf die Verwendung von `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="99b9d-349">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="99b9d-350">An Stellen, in denen `HttpClient`-Instanzen derzeit erstellt werden, können Sie diese Ereignisse mit einem Aufruf von <xref:System.Net.Http.IHttpClientFactory.CreateClient*> ersetzen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-350">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="99b9d-351">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-351">Named clients</span></span>

<span data-ttu-id="99b9d-352">Wenn eine App mehrere verschiedene Verwendungen von `HttpClient` mit jeweils unterschiedlicher Konfiguration erfordert, ist die Verwendung von **benannten Clients** eine Option.</span><span class="sxs-lookup"><span data-stu-id="99b9d-352">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="99b9d-353">Die Konfiguration einer benannten `HttpClient` kann während der Registrierung in `Startup.ConfigureServices` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-353">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="99b9d-354">Im oben stehenden Code wird `AddHttpClient` aufgerufen, und dabei wird der Name *github* angegeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-354">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="99b9d-355">Für diesen Client wird Standardkonfiguration angewendet, d.h. die Basisadresse und zwei Header, die erforderlich sind, um mit der GitHub-API zu funktionieren.</span><span class="sxs-lookup"><span data-stu-id="99b9d-355">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="99b9d-356">Wenn `CreateClient` aufgerufen wird, wird jedes Mal eine neue Instanz von `HttpClient` erstellt und die Konfigurationsaktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-356">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="99b9d-357">Zum Verarbeiten eines benannten Clients kann ein Zeichenfolgenparameter an `CreateClient` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-357">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="99b9d-358">Geben Sie den Namen des zu erstellenden Clients an:</span><span class="sxs-lookup"><span data-stu-id="99b9d-358">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="99b9d-359">Im vorangehenden Code muss die Anforderung keinen Hostnamen angeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-359">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="99b9d-360">Sie muss nur den Pfad übergeben, da die für den Client konfigurierte Basisadresse verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-360">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="99b9d-361">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-361">Typed clients</span></span>

<span data-ttu-id="99b9d-362">Typisierte Clients:</span><span class="sxs-lookup"><span data-stu-id="99b9d-362">Typed clients:</span></span>

* <span data-ttu-id="99b9d-363">Stellen dieselben Funktionen wie benannte Clients bereit, ohne Zeichenfolgen als Schlüssel verwenden zu müssen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-363">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="99b9d-364">Bieten Hilfe durch IntelliSense und Compiler beim Verarbeiten von Clients.</span><span class="sxs-lookup"><span data-stu-id="99b9d-364">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="99b9d-365">Stellen einen einzelnen Ort zum Konfigurieren und Interagieren mit einem bestimmten `HttpClient` bereit.</span><span class="sxs-lookup"><span data-stu-id="99b9d-365">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="99b9d-366">Zum Beispiel kann ein einzelner typisierter Client für einen einzelnen Back-End-Endpunkt verwendet werden, der jegliche Logik kapselt, die mit diesem Endpunkt interagiert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-366">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="99b9d-367">Funktionieren mit DI und können überall in Ihrer App eingefügt werden, wo sie benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-367">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="99b9d-368">Ein typisierter Client akzeptiert einen `HttpClient`-Parameter in seinem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="99b9d-368">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="99b9d-369">Im vorangehenden Code wird die Konfiguration in den typisierten Client verschoben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-369">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="99b9d-370">Das `HttpClient`-Objekt wird als öffentliche Eigenschaft zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-370">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="99b9d-371">Es ist möglich, API-spezifische Methoden zu definieren, die die `HttpClient`-Funktionalität zur Verfügung stellen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-371">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="99b9d-372">Die Methode `GetAspNetDocsIssues` kapselt den Code, der für die Abfrage und Analyse des neuesten offenen Problems aus dem GitHub-Repository erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="99b9d-372">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="99b9d-373">Zum Registrieren eines typisierten Clients kann die generische Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> innerhalb von `Startup.ConfigureServices` verwendet werden, die die typisierte Klasse angeben:</span><span class="sxs-lookup"><span data-stu-id="99b9d-373">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="99b9d-374">Der typisierte Client wird mit DI als „vorübergehend“ registriert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-374">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="99b9d-375">Der typisierte Client kann direkt eingefügt und verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-375">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="99b9d-376">Die Konfiguration kann für einen typisierten Client während der Registrierung in `Startup.ConfigureServices` angegeben werden, anstatt im Konstruktor des typisierten Clients, falls dies bevorzugt wird:</span><span class="sxs-lookup"><span data-stu-id="99b9d-376">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="99b9d-377">Es ist möglich, `HttpClient` vollständig in einem typisierten Client zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="99b9d-377">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="99b9d-378">Anstatt ihn als eine Eigenschaft zur Verfügung zu stellen, können öffentliche Methoden bereitgestellt werden, die die `HttpClient`-Instanz intern aufrufen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-378">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="99b9d-379">Im vorangehenden Code wird `HttpClient` als ein privates Feld gespeichert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-379">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="99b9d-380">Alle Zugriffe, die externe Aufrufe durchführen, durchlaufen die Methode `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="99b9d-380">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="99b9d-381">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-381">Generated clients</span></span>

<span data-ttu-id="99b9d-382">`IHttpClientFactory` kann in Verbindung mit anderen Bibliotheken von Drittanbietern verwendet werden, z.B. [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="99b9d-382">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="99b9d-383">Refit ist eine REST-Bibliothek für .NET.</span><span class="sxs-lookup"><span data-stu-id="99b9d-383">Refit is a REST library for .NET.</span></span> <span data-ttu-id="99b9d-384">Sie konvertiert REST-APIs in Live-Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-384">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="99b9d-385">Eine Implementierung der Schnittstelle wird dynamisch von `RestService` generiert. `HttpClient` wird für die externen HTTP-Aufrufe verwendet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-385">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="99b9d-386">Eine Schnittstelle und eine Antwort werden definiert, um die externe API und die Antwort darzustellen:</span><span class="sxs-lookup"><span data-stu-id="99b9d-386">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="99b9d-387">Ein typisierter Client kann hinzugefügt werden. Refit wird zum Generieren der Implementierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="99b9d-387">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="99b9d-388">Die definierte Schnittstelle kann bei Bedarf mit der von DI und Refit bereitgestellten Implementierung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-388">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="99b9d-389">Middleware für ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="99b9d-389">Outgoing request middleware</span></span>

<span data-ttu-id="99b9d-390">`HttpClient` enthält bereits das Konzept, Handler zu delegieren, die für ausgehende HTTP-Anforderungen miteinander verknüpft werden können.</span><span class="sxs-lookup"><span data-stu-id="99b9d-390">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="99b9d-391">`IHttpClientFactory` erleichtert das Definieren der Handler, die für jeden benannten Client angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-391">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="99b9d-392">Die Registrierung und Verkettung von mehreren Handlern wird unterstützt, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-392">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="99b9d-393">Jeder dieser Handler kann vor und nach der ausgehenden Anforderung Aufgaben ausführen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-393">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="99b9d-394">Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99b9d-394">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="99b9d-395">Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="99b9d-395">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="99b9d-396">Definieren Sie zum Erstellen eines Handlers eine von <xref:System.Net.Http.DelegatingHandler> abgeleitete Klasse.</span><span class="sxs-lookup"><span data-stu-id="99b9d-396">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="99b9d-397">Überschreiben Sie die Methode `SendAsync`, um Code auszuführen, bevor die Anforderung an den nächsten Handler in der Pipeline übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="99b9d-397">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="99b9d-398">Der vorangehende Code definiert einen einfachen Handler.</span><span class="sxs-lookup"><span data-stu-id="99b9d-398">The preceding code defines a basic handler.</span></span> <span data-ttu-id="99b9d-399">Er überprüft, ob ein `X-API-KEY`-Header in die Anforderung eingefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="99b9d-399">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="99b9d-400">Wenn der Header fehlt, kann er den HTTP-Aufruf vermeiden und eine geeignete Antwort zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-400">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="99b9d-401">Während der Registrierung kann mindestens ein Handler der Konfiguration eines `HttpClient` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-401">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="99b9d-402">Dieser Vorgang wird über die Erweiterungsmethoden von <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="99b9d-402">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="99b9d-403">Im vorangehenden Code ist `ValidateHeaderHandler` mit DI registriert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-403">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="99b9d-404">Die `IHttpClientFactory` erstellt einen separaten DI-Bereich für jeden Handler.</span><span class="sxs-lookup"><span data-stu-id="99b9d-404">The `IHttpClientFactory` creates a separate DI scope for each handler.</span></span> <span data-ttu-id="99b9d-405">Handler können von Diensten eines beliebigen Bereichs abhängen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-405">Handlers are free to depend upon services of any scope.</span></span> <span data-ttu-id="99b9d-406">Dienste, von denen Handler abhängig sind, werden verworfen, wenn der Handler verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-406">Services that handlers depend upon are disposed when the handler is disposed.</span></span>

<span data-ttu-id="99b9d-407">Nach der Registrierung kann <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> aufgerufen werden, was den Typ für den Handler übergibt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-407">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the type for the handler.</span></span>

<span data-ttu-id="99b9d-408">Mehrere Handler können in der Reihenfolge registriert werden, in der sie ausgeführt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-408">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="99b9d-409">Jeder Handler umschließt den nächsten Handler, bis der endgültige `HttpClientHandler` die Anforderung ausführt:</span><span class="sxs-lookup"><span data-stu-id="99b9d-409">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="99b9d-410">Verwenden Sie einen der folgenden Ansätze, um den anforderungsspezifischen Zustand mit Meldungshandlern zu teilen:</span><span class="sxs-lookup"><span data-stu-id="99b9d-410">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="99b9d-411">Übergeben Sie Daten mit `HttpRequestMessage.Properties` an den Handler.</span><span class="sxs-lookup"><span data-stu-id="99b9d-411">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="99b9d-412">Greifen Sie mit `IHttpContextAccessor` auf die aktuelle Anforderung zu.</span><span class="sxs-lookup"><span data-stu-id="99b9d-412">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="99b9d-413">Erstellen Sie ein benutzerdefiniertes `AsyncLocal`-Speicherobjekt, um die Daten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-413">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="99b9d-414">Verwenden von Polly-basierten Handlern</span><span class="sxs-lookup"><span data-stu-id="99b9d-414">Use Polly-based handlers</span></span>

<span data-ttu-id="99b9d-415">`IHttpClientFactory` integriert mit der beliebten Drittanbieter-Bibliothek namens [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="99b9d-415">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="99b9d-416">Polly ist eine umfassende Bibliothek für die Behandlung von beständigen und vorübergehenden Fehlern für .NET.</span><span class="sxs-lookup"><span data-stu-id="99b9d-416">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="99b9d-417">Entwicklern wird damit ermöglicht, Richtlinien wie Wiederholungsrichtlinien, Trennschalterrichtlinien, Timeout-Richtlinien, Bulkhead Isolation-Richtlinien und Ausweichrichtlinien in einer flüssigen und threadsicheren Art und Weise auszudrücken.</span><span class="sxs-lookup"><span data-stu-id="99b9d-417">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="99b9d-418">Erweiterungsmethoden werden bereitgestellt, um die Verwendung von Polly-Richtlinien mit konfigurierten `HttpClient`-Instanzen zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="99b9d-418">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="99b9d-419">Die Polly-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="99b9d-419">The Polly extensions:</span></span>

* <span data-ttu-id="99b9d-420">Unterstützen das Hinzufügen von Polly-basierten Handlern zu Clients.</span><span class="sxs-lookup"><span data-stu-id="99b9d-420">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="99b9d-421">Können nach Installation des [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/)-NuGet-Pakets verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-421">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="99b9d-422">Das Paket ist nicht im freigegebenen ASP.NET Core-Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-422">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="99b9d-423">Behandeln von vorrübergehenden Fehlern</span><span class="sxs-lookup"><span data-stu-id="99b9d-423">Handle transient faults</span></span>

<span data-ttu-id="99b9d-424">Am häufigsten treten Fehler auf, wenn externe HTTP-Aufrufe vorübergehend sind.</span><span class="sxs-lookup"><span data-stu-id="99b9d-424">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="99b9d-425">Eine praktische Erweiterungsmethode namens `AddTransientHttpErrorPolicy` ist enthalten. Sie ermöglicht das Definieren von Richtlinien zum Behandeln vorübergehender Fehler.</span><span class="sxs-lookup"><span data-stu-id="99b9d-425">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="99b9d-426">Richtlinien, die mit dieser Erweiterungsmethode konfiguriert wurden, behandeln `HttpRequestException`, HTTP 5xx-Antworten und HTTP 408-Antworten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-426">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="99b9d-427">Die `AddTransientHttpErrorPolicy`-Erweiterung kann in `Startup.ConfigureServices` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-427">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="99b9d-428">Die Erweiterung ermöglicht den Zugriff auf ein `PolicyBuilder`-Objekt, das für die Fehlerbehandlung konfiguriert wurde und mögliche vorübergehende Fehler darstellt:</span><span class="sxs-lookup"><span data-stu-id="99b9d-428">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="99b9d-429">Im vorangehenden Code wird eine `WaitAndRetryAsync`-Richtlinie definiert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-429">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="99b9d-430">Anforderungsfehler werden bis zu dreimal mit einer Verzögerung von 600 ms wiederholt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-430">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="99b9d-431">Dynamisches Auswählen von Richtlinien</span><span class="sxs-lookup"><span data-stu-id="99b9d-431">Dynamically select policies</span></span>

<span data-ttu-id="99b9d-432">Es gibt zusätzliche Erweiterungsmethoden, die zum Hinzufügen von Polly-basierten Handlern verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="99b9d-432">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="99b9d-433">Eine dieser Erweiterungen ist `AddPolicyHandler`, die über mehrere Überladungen verfügt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-433">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="99b9d-434">Eine Überladung ermöglicht, dass die Anforderung beim Definieren der zu verwendenden Richtlinie überprüft werden kann:</span><span class="sxs-lookup"><span data-stu-id="99b9d-434">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="99b9d-435">Im vorangehenden Code wird ein 10 Sekunden langer Timeout angewendet, wenn die ausgehende Anforderung eine HTTP GET-Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="99b9d-435">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="99b9d-436">Für alle anderen HTTP-Methoden wird ein 30 Sekunden langer Timeout verwendet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-436">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="99b9d-437">Hinzufügen mehrerer Polly-Handler</span><span class="sxs-lookup"><span data-stu-id="99b9d-437">Add multiple Polly handlers</span></span>

<span data-ttu-id="99b9d-438">Es ist üblich, Polly-Richtlinien zu schachteln, um erweiterte Funktionen bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="99b9d-438">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="99b9d-439">Im vorangehenden Beispiel werden zwei Handler hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-439">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="99b9d-440">Der Erste verwendet die `AddTransientHttpErrorPolicy`-Erweiterung, um eine Wiederholungsrichtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-440">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="99b9d-441">Fehlgeschlagene Anforderungen werden bis zu drei Mal wiederholt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-441">Failed requests are retried up to three times.</span></span> <span data-ttu-id="99b9d-442">Der zweite Aufruf von `AddTransientHttpErrorPolicy` fügt eine Trennschalterrichtlinie hinzu.</span><span class="sxs-lookup"><span data-stu-id="99b9d-442">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="99b9d-443">Weitere externe Anforderungen werden 30 Sekunden lang blockiert, wenn fünf Fehlversuche hintereinander stattfinden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-443">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="99b9d-444">Trennschalterrichtlinien sind zustandsbehaftet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-444">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="99b9d-445">Alle Aufrufe über diesen Client teilen den gleichen Schalterzustand.</span><span class="sxs-lookup"><span data-stu-id="99b9d-445">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="99b9d-446">Hinzufügen von Richtlinien aus der Polly-Registrierung</span><span class="sxs-lookup"><span data-stu-id="99b9d-446">Add policies from the Polly registry</span></span>

<span data-ttu-id="99b9d-447">Eine Methode zum Verwalten regelmäßig genutzter Richtlinien ist, Sie einmal zu definieren und mit `PolicyRegistry` zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="99b9d-447">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="99b9d-448">Eine Erweiterungsmethode wird bereitgestellt, die einem Handler ermöglicht, mithilfe einer Richtlinie aus der Registrierung hinzugefügt zu werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-448">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="99b9d-449">Im oben stehenden Code werden zwei Richtlinien registriert, wenn `PolicyRegistry` zu `ServiceCollection` hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-449">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="99b9d-450">Damit eine Richtlinie aus der Registrierung verwendet werden kann, wird die Methode `AddPolicyHandlerFromRegistry` verwendet. Dabei wird der Name der anzuwendenden Richtlinie übergeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-450">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="99b9d-451">Weitere Informationen zu `IHttpClientFactory` und Polly-Integrationen finden Sie im [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="99b9d-451">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="99b9d-452">HttpClient und die Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="99b9d-452">HttpClient and lifetime management</span></span>

<span data-ttu-id="99b9d-453">Bei jedem Aufruf von `CreateClient` in der `IHttpClientFactory` wird eine neue Instanz von `HttpClient` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-453">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="99b9d-454">Es gibt einen <xref:System.Net.Http.HttpMessageHandler> pro benanntem Client.</span><span class="sxs-lookup"><span data-stu-id="99b9d-454">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="99b9d-455">Die Factory verwaltet die Lebensdauer der `HttpMessageHandler`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-455">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="99b9d-456">`IHttpClientFactory` legt die `HttpMessageHandler`-Instanzen zusammen, die von der Factory zum Reduzieren des Ressourcenverbrauchs erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-456">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="99b9d-457">Eine `HttpMessageHandler`-Instanz kann aus dem Pool wiederverwendet werden, wenn eine neue `HttpClient`-Instanz erstellt wird und deren Lebensdauer noch nicht abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="99b9d-457">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="99b9d-458">Das Zusammenlegen von Handlern ist ein wünschenswerter Vorgang, da jeder Handler in der Regel seine zugrunde liegenden HTTP-Verbindungen selbst verwaltet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-458">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="99b9d-459">Wenn mehr Handler als nötig erstellt werden, können Verzögerungen bei Verbindungen entstehen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-459">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="99b9d-460">Einige Handler halten Verbindungen auch unbegrenzt offen, was verhindert, dass der Handler auf DNS-Änderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-460">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="99b9d-461">Die Standardlebensdauer von Handlern beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-461">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="99b9d-462">Der Standardwert kann für jeden benannten Client überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-462">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="99b9d-463">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> auf dem bei der Erstellung des Clients zurückgegebenen `IHttpClientBuilder` auf, um den Wert zu überschreiben:</span><span class="sxs-lookup"><span data-stu-id="99b9d-463">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="99b9d-464">Das Verwerfen des Client ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="99b9d-464">Disposal of the client isn't required.</span></span> <span data-ttu-id="99b9d-465">Beim Verwerfen werden ausgehende Anforderungen abgebrochen, und es wird sichergestellt, dass die angegebene `HttpClient`-Instanz nach dem Aufruf von <xref:System.IDisposable.Dispose*> nicht mehr verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="99b9d-465">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="99b9d-466">`IHttpClientFactory` verfolgt von `HttpClient`-Instanzen verwendete Ressourcen nach und verwirft sie.</span><span class="sxs-lookup"><span data-stu-id="99b9d-466">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="99b9d-467">Die `HttpClient`-Instanzen können im Allgemeinen als .NET-Objekte behandelt werden, die nicht verworfen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-467">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="99b9d-468">Das Beibehalten einer einzelnen `HttpClient`-Instanz für einen langen Zeitraum ist ein allgemeines Muster, das vor der Einführung von `IHttpClientFactory` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="99b9d-468">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="99b9d-469">Dieses Muster wird nach der Migration zu `IHttpClientFactory` überflüssig.</span><span class="sxs-lookup"><span data-stu-id="99b9d-469">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="99b9d-470">Alternativen zu IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="99b9d-470">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="99b9d-471">Mit der Verwendung von `IHttpClientFactory` in einer DI-fähigen App wird Folgendes vermieden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-471">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="99b9d-472">Probleme mit der Ressourcenauslastung durch Zusammenlegen von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="99b9d-472">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="99b9d-473">Probleme durch veraltetes DNS durch regelmäßigen Wechsel von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="99b9d-473">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="99b9d-474">Es gibt alternative Möglichkeiten zum Lösen des vorangehenden Problems mithilfe einer langlebigen <xref:System.Net.Http.SocketsHttpHandler>-Instanz:</span><span class="sxs-lookup"><span data-stu-id="99b9d-474">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="99b9d-475">Erstellen Sie eine `SocketsHttpHandler`-Instanz, wenn die App gestartet wird, und verwenden Sie diese für die Lebensdauer der App.</span><span class="sxs-lookup"><span data-stu-id="99b9d-475">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="99b9d-476">Konfigurieren Sie einen geeigneten Wert für <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> basierend auf den DNS-Aktualisierungszeiten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-476">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="99b9d-477">Erstellen Sie bei Bedarf `HttpClient`-Instanzen mithilfe von `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="99b9d-477">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="99b9d-478">Diese Ansätze lösen die Ressourcenverwaltungsprobleme, die `IHttpClientFactory` auf ähnliche Weise löst.</span><span class="sxs-lookup"><span data-stu-id="99b9d-478">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="99b9d-479">`SocketsHttpHandler` gibt Verbindungen für `HttpClient`-Instanzen frei.</span><span class="sxs-lookup"><span data-stu-id="99b9d-479">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="99b9d-480">Durch diese Freigabe wird die Erschöpfung von Sockets vermieden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-480">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="99b9d-481">`SocketsHttpHandler` wechselt die Verbindungen anhand von `PooledConnectionLifetime`, um Probleme durch veraltetes DNS zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-481">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="99b9d-482">Cookies</span><span class="sxs-lookup"><span data-stu-id="99b9d-482">Cookies</span></span>

<span data-ttu-id="99b9d-483">Die zusammengelegten `HttpMessageHandler`-Instanzen resultieren in der Freigabe von `CookieContainer`-Objekten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-483">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="99b9d-484">Die unerwartete Freigabe von `CookieContainer`-Objekten führt oft zu fehlerhaftem Code.</span><span class="sxs-lookup"><span data-stu-id="99b9d-484">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="99b9d-485">Ziehen Sie für Apps, die Cookies erfordern, folgende Vorgehensweisen in Betracht:</span><span class="sxs-lookup"><span data-stu-id="99b9d-485">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="99b9d-486">Deaktivieren der automatischen Cookieverarbeitung</span><span class="sxs-lookup"><span data-stu-id="99b9d-486">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="99b9d-487">Vermeiden der Verwendung von `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="99b9d-487">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="99b9d-488">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> auf, um die automatische Cookieverarbeitung zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="99b9d-488">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="99b9d-489">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="99b9d-489">Logging</span></span>

<span data-ttu-id="99b9d-490">Über `IHttpClientFactory` erstellte Clients zeichnen Protokollmeldungen für alle Anforderungen auf.</span><span class="sxs-lookup"><span data-stu-id="99b9d-490">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="99b9d-491">Aktivieren Sie die entsprechende Informationsebene in Ihrer Protokollierungskonfiguration, um die Standardprotokollmeldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-491">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="99b9d-492">Zusätzliche Protokollierung, z.B. das Protokollieren von Anforderungsheadern, wird nur auf der Ablaufverfolgungsebene enthalten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-492">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="99b9d-493">Die Protokollierungskategorie für jeden Client enthält den Namen des Clients.</span><span class="sxs-lookup"><span data-stu-id="99b9d-493">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="99b9d-494">Ein Client namens *MyNamedClient* protokolliert beispielsweise Meldungen mit der Kategorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="99b9d-494">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="99b9d-495">Meldungen mit dem Suffix *LogicalHandler* treten außerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="99b9d-495">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="99b9d-496">In der Anforderung werden Meldungen protokolliert, bevor andere Handler in der Pipeline sie verarbeitet haben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-496">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="99b9d-497">In der Antwort werden Meldungen protokolliert, nachdem andere Handler in der Pipeline die Antwort empfangen haben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-497">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="99b9d-498">Die Protokollierung tritt ebenfalls innerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="99b9d-498">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="99b9d-499">Im Beispiel *MyNamedClient* werden diese Meldungen für die Protokollkategorie `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` protokolliert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-499">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="99b9d-500">Dies findet für die Anforderung statt, nachdem alle anderen Handler ausgeführt wurden und bevor die Anforderung an das Netzwerk gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-500">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="99b9d-501">Diese Protokollierung enthält den Zustand der Antwort in der Antwort, bevor sie an die Handlerpipeline zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-501">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="99b9d-502">Das Aktivieren der Protokollierung außerhalb und innerhalb der Pipeline ermöglicht die Überprüfung der Änderungen, die durch andere Handler in der Pipeline erfolgt sind.</span><span class="sxs-lookup"><span data-stu-id="99b9d-502">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="99b9d-503">Dies kann beispielsweise die Änderungen an Anforderungsheadern oder am Statuscode der Antwort enthalten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-503">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="99b9d-504">Das Einschließen des Namens des Clients in der Protokollkategorie aktiviert bei Bedarf die Protokollfilterung für spezifische benannte Clients.</span><span class="sxs-lookup"><span data-stu-id="99b9d-504">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="99b9d-505">Konfigurieren von HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="99b9d-505">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="99b9d-506">Es kann notwendig sein, die Konfiguration des inneren von einem Client verwendeten `HttpMessageHandler` zu steuern.</span><span class="sxs-lookup"><span data-stu-id="99b9d-506">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="99b9d-507">`IHttpClientBuilder` wird zurückgegeben, wenn benannte oder typisierte Clients hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-507">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="99b9d-508">Die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> kann zum Definieren eines Delegaten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-508">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="99b9d-509">Der Delegat wird verwendet, um den primären `HttpMessageHandler` zu erstellen und konfigurieren, der von dem Client verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="99b9d-509">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="99b9d-510">Verwenden von IHttpClientFactory in einer Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="99b9d-510">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="99b9d-511">Fügen Sie in einer Konsolen-App dem Projekt die folgenden Paketverweise hinzu:</span><span class="sxs-lookup"><span data-stu-id="99b9d-511">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="99b9d-512">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="99b9d-512">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="99b9d-513">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="99b9d-513">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="99b9d-514">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="99b9d-514">In the following example:</span></span>

* <span data-ttu-id="99b9d-515"><xref:System.Net.Http.IHttpClientFactory> ist im Dienstcontainer des [generischen Hosts](xref:fundamentals/host/generic-host) registriert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-515"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="99b9d-516">`MyService` erstellt eine Clientfactoryinstanz aus dem-Dienst, der zum Erstellen eines `HttpClient`verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-516">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="99b9d-517">`HttpClient` wird zum Abrufen einer Webseite verwendet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-517">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="99b9d-518">`Main` erstellt einen Bereich, um die `GetPage`-Methode des Diensts auszuführen und die ersten 500 Zeichen des Webseiteninhalts in die Konsole zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-518">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="additional-resources"></a><span data-ttu-id="99b9d-519">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="99b9d-519">Additional resources</span></span>

* [<span data-ttu-id="99b9d-520">Verwenden von HttpClientFactory zur Implementierung robuster HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="99b9d-520">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="99b9d-521">Implementieren von Wiederholungen von HTTP-Aufrufen mit exponentiellem Backoff mit HttpClientFactory und Polly-Richtlinien</span><span class="sxs-lookup"><span data-stu-id="99b9d-521">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="99b9d-522">Implementieren des Trennschaltermusters</span><span class="sxs-lookup"><span data-stu-id="99b9d-522">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="99b9d-523">Von [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), und [Steve Gordon](https://github.com/stevejgordon)</span><span class="sxs-lookup"><span data-stu-id="99b9d-523">By [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), and [Steve Gordon](https://github.com/stevejgordon)</span></span>

<span data-ttu-id="99b9d-524"><xref:System.Net.Http.IHttpClientFactory> kann registriert und zum Konfigurieren und Erstellen von <xref:System.Net.Http.HttpClient>-Instanzen in einer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-524">An <xref:System.Net.Http.IHttpClientFactory> can be registered and used to configure and create <xref:System.Net.Http.HttpClient> instances in an app.</span></span> <span data-ttu-id="99b9d-525">Dies hat folgende Vorteile:</span><span class="sxs-lookup"><span data-stu-id="99b9d-525">It offers the following benefits:</span></span>

* <span data-ttu-id="99b9d-526">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-526">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="99b9d-527">Ein *GitHub*-Client kann beispielsweise registriert werden und so konfiguriert werden, um auf [GitHub](https://github.com/) zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-527">For example, a *github* client can be registered and configured to access [GitHub](https://github.com/).</span></span> <span data-ttu-id="99b9d-528">Ein Standard-Client kann für andere Zwecke registriert werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-528">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="99b9d-529">Das Konzept der ausgehenden Middleware wird über delegierende Handler in `HttpClient` in Code umgesetzt. Außerdem werden Erweiterungen für auf Polly basierende Middleware bereitgestellt, die dies nutzen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-529">Codifies the concept of outgoing middleware via delegating handlers in `HttpClient` and provides extensions for Polly-based middleware to take advantage of that.</span></span>
* <span data-ttu-id="99b9d-530">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-530">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="99b9d-531">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-531">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="99b9d-532">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="99b9d-532">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="99b9d-533">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="99b9d-533">Prerequisites</span></span>

<span data-ttu-id="99b9d-534">Für Projekte mit der Zielplattform .NET Framework muss das NuGet-Paket [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) installiert werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-534">Projects targeting .NET Framework require installation of the [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) NuGet package.</span></span> <span data-ttu-id="99b9d-535">Projekte für .NET Core, die auf das [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) verweisen, enthalten bereits das `Microsoft.Extensions.Http`-Paket.</span><span class="sxs-lookup"><span data-stu-id="99b9d-535">Projects that target .NET Core and reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) already include the `Microsoft.Extensions.Http` package.</span></span>

## <a name="consumption-patterns"></a><span data-ttu-id="99b9d-536">Verbrauchsmuster</span><span class="sxs-lookup"><span data-stu-id="99b9d-536">Consumption patterns</span></span>

<span data-ttu-id="99b9d-537">Es gibt mehrere Möglichkeiten `IHttpClientFactory` in einer App zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-537">There are several ways `IHttpClientFactory` can be used in an app:</span></span>

* [<span data-ttu-id="99b9d-538">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="99b9d-538">Basic usage</span></span>](#basic-usage)
* [<span data-ttu-id="99b9d-539">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-539">Named clients</span></span>](#named-clients)
* [<span data-ttu-id="99b9d-540">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-540">Typed clients</span></span>](#typed-clients)
* [<span data-ttu-id="99b9d-541">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-541">Generated clients</span></span>](#generated-clients)

<span data-ttu-id="99b9d-542">Keine dieser Möglichkeiten ist einer anderen überlegen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-542">None of them are strictly superior to another.</span></span> <span data-ttu-id="99b9d-543">Der beste Ansatz richtet sich nach den Einschränkungen der App.</span><span class="sxs-lookup"><span data-stu-id="99b9d-543">The best approach depends upon the app's constraints.</span></span>

### <a name="basic-usage"></a><span data-ttu-id="99b9d-544">Grundlegende Verwendung</span><span class="sxs-lookup"><span data-stu-id="99b9d-544">Basic usage</span></span>

<span data-ttu-id="99b9d-545">`IHttpClientFactory` kann durch Aufrufen der Erweiterungsmethode `AddHttpClient` auf `IServiceCollection` in der Methode `Startup.ConfigureServices` registriert werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-545">The `IHttpClientFactory` can be registered by calling the `AddHttpClient` extension method on the `IServiceCollection`, inside the `Startup.ConfigureServices` method.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

<span data-ttu-id="99b9d-546">Nach der Registrierung kann Code `IHttpClientFactory` überall akzeptieren, wo Dienste mithilfe der [Dependency Injection](xref:fundamentals/dependency-injection) (DI) eingefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="99b9d-546">Once registered, code can accept an `IHttpClientFactory` anywhere services can be injected with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="99b9d-547">`IHttpClientFactory` kann zum Erstellen einer `HttpClient`-Instanz verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-547">The `IHttpClientFactory` can be used to create an `HttpClient` instance:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

<span data-ttu-id="99b9d-548">`IHttpClientFactory` auf diese Weise zu verwenden, ist eine gute Möglichkeit zum Umgestalten einer vorhandenen App.</span><span class="sxs-lookup"><span data-stu-id="99b9d-548">Using `IHttpClientFactory` in this fashion is a good way to refactor an existing app.</span></span> <span data-ttu-id="99b9d-549">Dies hat keine Auswirkung auf die Verwendung von `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="99b9d-549">It has no impact on the way `HttpClient` is used.</span></span> <span data-ttu-id="99b9d-550">An Stellen, in denen `HttpClient`-Instanzen derzeit erstellt werden, können Sie diese Ereignisse mit einem Aufruf von <xref:System.Net.Http.IHttpClientFactory.CreateClient*> ersetzen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-550">In places where `HttpClient` instances are currently created, replace those occurrences with a call to <xref:System.Net.Http.IHttpClientFactory.CreateClient*>.</span></span>

### <a name="named-clients"></a><span data-ttu-id="99b9d-551">Benannte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-551">Named clients</span></span>

<span data-ttu-id="99b9d-552">Wenn eine App mehrere verschiedene Verwendungen von `HttpClient` mit jeweils unterschiedlicher Konfiguration erfordert, ist die Verwendung von **benannten Clients** eine Option.</span><span class="sxs-lookup"><span data-stu-id="99b9d-552">If an app requires many distinct uses of `HttpClient`, each with a different configuration, an option is to use **named clients**.</span></span> <span data-ttu-id="99b9d-553">Die Konfiguration einer benannten `HttpClient` kann während der Registrierung in `Startup.ConfigureServices` angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-553">Configuration for a named `HttpClient` can be specified during registration in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

<span data-ttu-id="99b9d-554">Im oben stehenden Code wird `AddHttpClient` aufgerufen, und dabei wird der Name *github* angegeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-554">In the preceding code, `AddHttpClient` is called, providing the name *github*.</span></span> <span data-ttu-id="99b9d-555">Für diesen Client wird Standardkonfiguration angewendet, d.h. die Basisadresse und zwei Header, die erforderlich sind, um mit der GitHub-API zu funktionieren.</span><span class="sxs-lookup"><span data-stu-id="99b9d-555">This client has some default configuration applied&mdash;namely the base address and two headers required to work with the GitHub API.</span></span>

<span data-ttu-id="99b9d-556">Wenn `CreateClient` aufgerufen wird, wird jedes Mal eine neue Instanz von `HttpClient` erstellt und die Konfigurationsaktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-556">Each time `CreateClient` is called, a new instance of `HttpClient` is created and the configuration action is called.</span></span>

<span data-ttu-id="99b9d-557">Zum Verarbeiten eines benannten Clients kann ein Zeichenfolgenparameter an `CreateClient` übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-557">To consume a named client, a string parameter can be passed to `CreateClient`.</span></span> <span data-ttu-id="99b9d-558">Geben Sie den Namen des zu erstellenden Clients an:</span><span class="sxs-lookup"><span data-stu-id="99b9d-558">Specify the name of the client to be created:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

<span data-ttu-id="99b9d-559">Im vorangehenden Code muss die Anforderung keinen Hostnamen angeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-559">In the preceding code, the request doesn't need to specify a hostname.</span></span> <span data-ttu-id="99b9d-560">Sie muss nur den Pfad übergeben, da die für den Client konfigurierte Basisadresse verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-560">It can pass just the path, since the base address configured for the client is used.</span></span>

### <a name="typed-clients"></a><span data-ttu-id="99b9d-561">Typisierte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-561">Typed clients</span></span>

<span data-ttu-id="99b9d-562">Typisierte Clients:</span><span class="sxs-lookup"><span data-stu-id="99b9d-562">Typed clients:</span></span>

* <span data-ttu-id="99b9d-563">Stellen dieselben Funktionen wie benannte Clients bereit, ohne Zeichenfolgen als Schlüssel verwenden zu müssen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-563">Provide the same capabilities as named clients without the need to use strings as keys.</span></span>
* <span data-ttu-id="99b9d-564">Bieten Hilfe durch IntelliSense und Compiler beim Verarbeiten von Clients.</span><span class="sxs-lookup"><span data-stu-id="99b9d-564">Provides IntelliSense and compiler help when consuming clients.</span></span>
* <span data-ttu-id="99b9d-565">Stellen einen einzelnen Ort zum Konfigurieren und Interagieren mit einem bestimmten `HttpClient` bereit.</span><span class="sxs-lookup"><span data-stu-id="99b9d-565">Provide a single location to configure and interact with a particular `HttpClient`.</span></span> <span data-ttu-id="99b9d-566">Zum Beispiel kann ein einzelner typisierter Client für einen einzelnen Back-End-Endpunkt verwendet werden, der jegliche Logik kapselt, die mit diesem Endpunkt interagiert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-566">For example, a single typed client might be used for a single backend endpoint and encapsulate all logic dealing with that endpoint.</span></span>
* <span data-ttu-id="99b9d-567">Funktionieren mit DI und können überall in Ihrer App eingefügt werden, wo sie benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-567">Work with DI and can be injected where required in your app.</span></span>

<span data-ttu-id="99b9d-568">Ein typisierter Client akzeptiert einen `HttpClient`-Parameter in seinem Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="99b9d-568">A typed client accepts an `HttpClient` parameter in its constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

<span data-ttu-id="99b9d-569">Im vorangehenden Code wird die Konfiguration in den typisierten Client verschoben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-569">In the preceding code, the configuration is moved into the typed client.</span></span> <span data-ttu-id="99b9d-570">Das `HttpClient`-Objekt wird als öffentliche Eigenschaft zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-570">The `HttpClient` object is exposed as a public property.</span></span> <span data-ttu-id="99b9d-571">Es ist möglich, API-spezifische Methoden zu definieren, die die `HttpClient`-Funktionalität zur Verfügung stellen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-571">It's possible to define API-specific methods that expose `HttpClient` functionality.</span></span> <span data-ttu-id="99b9d-572">Die Methode `GetAspNetDocsIssues` kapselt den Code, der für die Abfrage und Analyse des neuesten offenen Problems aus dem GitHub-Repository erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="99b9d-572">The `GetAspNetDocsIssues` method encapsulates the code needed to query for and parse out the latest open issues from a GitHub repository.</span></span>

<span data-ttu-id="99b9d-573">Zum Registrieren eines typisierten Clients kann die generische Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> innerhalb von `Startup.ConfigureServices` verwendet werden, die die typisierte Klasse angeben:</span><span class="sxs-lookup"><span data-stu-id="99b9d-573">To register a typed client, the generic <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> extension method can be used within `Startup.ConfigureServices`, specifying the typed client class:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

<span data-ttu-id="99b9d-574">Der typisierte Client wird mit DI als „vorübergehend“ registriert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-574">The typed client is registered as transient with DI.</span></span> <span data-ttu-id="99b9d-575">Der typisierte Client kann direkt eingefügt und verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-575">The typed client can be injected and consumed directly:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

<span data-ttu-id="99b9d-576">Die Konfiguration kann für einen typisierten Client während der Registrierung in `Startup.ConfigureServices` angegeben werden, anstatt im Konstruktor des typisierten Clients, falls dies bevorzugt wird:</span><span class="sxs-lookup"><span data-stu-id="99b9d-576">If preferred, the configuration for a typed client can be specified during registration in `Startup.ConfigureServices`, rather than in the typed client's constructor:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

<span data-ttu-id="99b9d-577">Es ist möglich, `HttpClient` vollständig in einem typisierten Client zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="99b9d-577">It's possible to entirely encapsulate the `HttpClient` within a typed client.</span></span> <span data-ttu-id="99b9d-578">Anstatt ihn als eine Eigenschaft zur Verfügung zu stellen, können öffentliche Methoden bereitgestellt werden, die die `HttpClient`-Instanz intern aufrufen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-578">Rather than exposing it as a property, public methods can be provided which call the `HttpClient` instance internally.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

<span data-ttu-id="99b9d-579">Im vorangehenden Code wird `HttpClient` als ein privates Feld gespeichert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-579">In the preceding code, the `HttpClient` is stored as a private field.</span></span> <span data-ttu-id="99b9d-580">Alle Zugriffe, die externe Aufrufe durchführen, durchlaufen die Methode `GetRepos`.</span><span class="sxs-lookup"><span data-stu-id="99b9d-580">All access to make external calls goes through the `GetRepos` method.</span></span>

### <a name="generated-clients"></a><span data-ttu-id="99b9d-581">Generierte Clients</span><span class="sxs-lookup"><span data-stu-id="99b9d-581">Generated clients</span></span>

<span data-ttu-id="99b9d-582">`IHttpClientFactory` kann in Verbindung mit anderen Bibliotheken von Drittanbietern verwendet werden, z.B. [Refit](https://github.com/paulcbetts/refit).</span><span class="sxs-lookup"><span data-stu-id="99b9d-582">`IHttpClientFactory` can be used in combination with other third-party libraries such as [Refit](https://github.com/paulcbetts/refit).</span></span> <span data-ttu-id="99b9d-583">Refit ist eine REST-Bibliothek für .NET.</span><span class="sxs-lookup"><span data-stu-id="99b9d-583">Refit is a REST library for .NET.</span></span> <span data-ttu-id="99b9d-584">Sie konvertiert REST-APIs in Live-Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-584">It converts REST APIs into live interfaces.</span></span> <span data-ttu-id="99b9d-585">Eine Implementierung der Schnittstelle wird dynamisch von `RestService` generiert. `HttpClient` wird für die externen HTTP-Aufrufe verwendet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-585">An implementation of the interface is generated dynamically by the `RestService`, using `HttpClient` to make the external HTTP calls.</span></span>

<span data-ttu-id="99b9d-586">Eine Schnittstelle und eine Antwort werden definiert, um die externe API und die Antwort darzustellen:</span><span class="sxs-lookup"><span data-stu-id="99b9d-586">An interface and a reply are defined to represent the external API and its response:</span></span>

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

<span data-ttu-id="99b9d-587">Ein typisierter Client kann hinzugefügt werden. Refit wird zum Generieren der Implementierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="99b9d-587">A typed client can be added, using Refit to generate the implementation:</span></span>

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

<span data-ttu-id="99b9d-588">Die definierte Schnittstelle kann bei Bedarf mit der von DI und Refit bereitgestellten Implementierung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-588">The defined interface can be consumed where necessary, with the implementation provided by DI and Refit:</span></span>

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

## <a name="outgoing-request-middleware"></a><span data-ttu-id="99b9d-589">Middleware für ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="99b9d-589">Outgoing request middleware</span></span>

<span data-ttu-id="99b9d-590">`HttpClient` enthält bereits das Konzept, Handler zu delegieren, die für ausgehende HTTP-Anforderungen miteinander verknüpft werden können.</span><span class="sxs-lookup"><span data-stu-id="99b9d-590">`HttpClient` already has the concept of delegating handlers that can be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="99b9d-591">`IHttpClientFactory` erleichtert das Definieren der Handler, die für jeden benannten Client angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-591">The `IHttpClientFactory` makes it easy to define the handlers to apply for each named client.</span></span> <span data-ttu-id="99b9d-592">Die Registrierung und Verkettung von mehreren Handlern wird unterstützt, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-592">It supports registration and chaining of multiple handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="99b9d-593">Jeder dieser Handler kann vor und nach der ausgehenden Anforderung Aufgaben ausführen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-593">Each of these handlers is able to perform work before and after the outgoing request.</span></span> <span data-ttu-id="99b9d-594">Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="99b9d-594">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="99b9d-595">Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="99b9d-595">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>

<span data-ttu-id="99b9d-596">Definieren Sie zum Erstellen eines Handlers eine von <xref:System.Net.Http.DelegatingHandler> abgeleitete Klasse.</span><span class="sxs-lookup"><span data-stu-id="99b9d-596">To create a handler, define a class deriving from <xref:System.Net.Http.DelegatingHandler>.</span></span> <span data-ttu-id="99b9d-597">Überschreiben Sie die Methode `SendAsync`, um Code auszuführen, bevor die Anforderung an den nächsten Handler in der Pipeline übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="99b9d-597">Override the `SendAsync` method to execute code before passing the request to the next handler in the pipeline:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

<span data-ttu-id="99b9d-598">Der vorangehende Code definiert einen einfachen Handler.</span><span class="sxs-lookup"><span data-stu-id="99b9d-598">The preceding code defines a basic handler.</span></span> <span data-ttu-id="99b9d-599">Er überprüft, ob ein `X-API-KEY`-Header in die Anforderung eingefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="99b9d-599">It checks to see if an `X-API-KEY` header has been included on the request.</span></span> <span data-ttu-id="99b9d-600">Wenn der Header fehlt, kann er den HTTP-Aufruf vermeiden und eine geeignete Antwort zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-600">If the header is missing, it can avoid the HTTP call and return a suitable response.</span></span>

<span data-ttu-id="99b9d-601">Während der Registrierung kann mindestens ein Handler der Konfiguration eines `HttpClient` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-601">During registration, one or more handlers can be added to the configuration for an `HttpClient`.</span></span> <span data-ttu-id="99b9d-602">Dieser Vorgang wird über die Erweiterungsmethoden von <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="99b9d-602">This task is accomplished via extension methods on the <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder>.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

<span data-ttu-id="99b9d-603">Im vorangehenden Code ist `ValidateHeaderHandler` mit DI registriert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-603">In the preceding code, the `ValidateHeaderHandler` is registered with DI.</span></span> <span data-ttu-id="99b9d-604">Der Handler **muss** in DI als vorübergehender Dienst registriert sein, niemals bereichsbezogen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-604">The handler **must** be registered in DI as a transient service, never scoped.</span></span> <span data-ttu-id="99b9d-605">Wenn der Handler als bereichsbezogener Dienst registriert ist und alle Dienste, von denen der Handler abhängig ist, gelöscht werden können:</span><span class="sxs-lookup"><span data-stu-id="99b9d-605">If the handler is registered as a scoped service and any services that the handler depends upon are disposable:</span></span>

* <span data-ttu-id="99b9d-606">Die Dienste des Handlers können verworfen werden, bevor der Handler den Gültigkeitsbereich verlässt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-606">The handler's services could be disposed before the handler goes out of scope.</span></span>
* <span data-ttu-id="99b9d-607">Der verworfenen Handlerdienst bewirken, dass der Handler fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-607">The disposed handler services causes the handler to fail.</span></span>

<span data-ttu-id="99b9d-608">Nach der Registrierung kann <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> aufgerufen werden, wobei der Typ für den Handler übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-608">Once registered, <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> can be called, passing in the handler type.</span></span>

<span data-ttu-id="99b9d-609">Mehrere Handler können in der Reihenfolge registriert werden, in der sie ausgeführt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-609">Multiple handlers can be registered in the order that they should execute.</span></span> <span data-ttu-id="99b9d-610">Jeder Handler umschließt den nächsten Handler, bis der endgültige `HttpClientHandler` die Anforderung ausführt:</span><span class="sxs-lookup"><span data-stu-id="99b9d-610">Each handler wraps the next handler until the final `HttpClientHandler` executes the request:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

<span data-ttu-id="99b9d-611">Verwenden Sie einen der folgenden Ansätze, um den anforderungsspezifischen Zustand mit Meldungshandlern zu teilen:</span><span class="sxs-lookup"><span data-stu-id="99b9d-611">Use one of the following approaches to share per-request state with message handlers:</span></span>

* <span data-ttu-id="99b9d-612">Übergeben Sie Daten mit `HttpRequestMessage.Properties` an den Handler.</span><span class="sxs-lookup"><span data-stu-id="99b9d-612">Pass data into the handler using `HttpRequestMessage.Properties`.</span></span>
* <span data-ttu-id="99b9d-613">Greifen Sie mit `IHttpContextAccessor` auf die aktuelle Anforderung zu.</span><span class="sxs-lookup"><span data-stu-id="99b9d-613">Use `IHttpContextAccessor` to access the current request.</span></span>
* <span data-ttu-id="99b9d-614">Erstellen Sie ein benutzerdefiniertes `AsyncLocal`-Speicherobjekt, um die Daten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-614">Create a custom `AsyncLocal` storage object to pass the data.</span></span>

## <a name="use-polly-based-handlers"></a><span data-ttu-id="99b9d-615">Verwenden von Polly-basierten Handlern</span><span class="sxs-lookup"><span data-stu-id="99b9d-615">Use Polly-based handlers</span></span>

<span data-ttu-id="99b9d-616">`IHttpClientFactory` integriert mit der beliebten Drittanbieter-Bibliothek namens [Polly](https://github.com/App-vNext/Polly).</span><span class="sxs-lookup"><span data-stu-id="99b9d-616">`IHttpClientFactory` integrates with a popular third-party library called [Polly](https://github.com/App-vNext/Polly).</span></span> <span data-ttu-id="99b9d-617">Polly ist eine umfassende Bibliothek für die Behandlung von beständigen und vorübergehenden Fehlern für .NET.</span><span class="sxs-lookup"><span data-stu-id="99b9d-617">Polly is a comprehensive resilience and transient fault-handling library for .NET.</span></span> <span data-ttu-id="99b9d-618">Entwicklern wird damit ermöglicht, Richtlinien wie Wiederholungsrichtlinien, Trennschalterrichtlinien, Timeout-Richtlinien, Bulkhead Isolation-Richtlinien und Ausweichrichtlinien in einer flüssigen und threadsicheren Art und Weise auszudrücken.</span><span class="sxs-lookup"><span data-stu-id="99b9d-618">It allows developers to express policies such as Retry, Circuit Breaker, Timeout, Bulkhead Isolation, and Fallback in a fluent and thread-safe manner.</span></span>

<span data-ttu-id="99b9d-619">Erweiterungsmethoden werden bereitgestellt, um die Verwendung von Polly-Richtlinien mit konfigurierten `HttpClient`-Instanzen zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="99b9d-619">Extension methods are provided to enable the use of Polly policies with configured `HttpClient` instances.</span></span> <span data-ttu-id="99b9d-620">Die Polly-Erweiterungen:</span><span class="sxs-lookup"><span data-stu-id="99b9d-620">The Polly extensions:</span></span>

* <span data-ttu-id="99b9d-621">Unterstützen das Hinzufügen von Polly-basierten Handlern zu Clients.</span><span class="sxs-lookup"><span data-stu-id="99b9d-621">Support adding Polly-based handlers to clients.</span></span>
* <span data-ttu-id="99b9d-622">Können nach Installation des [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/)-NuGet-Pakets verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-622">Can be used after installing the [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/) NuGet package.</span></span> <span data-ttu-id="99b9d-623">Das Paket ist nicht im freigegebenen ASP.NET Core-Framework enthalten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-623">The package isn't included in the ASP.NET Core shared framework.</span></span>

### <a name="handle-transient-faults"></a><span data-ttu-id="99b9d-624">Behandeln von vorrübergehenden Fehlern</span><span class="sxs-lookup"><span data-stu-id="99b9d-624">Handle transient faults</span></span>

<span data-ttu-id="99b9d-625">Am häufigsten treten Fehler auf, wenn externe HTTP-Aufrufe vorübergehend sind.</span><span class="sxs-lookup"><span data-stu-id="99b9d-625">Most common faults occur when external HTTP calls are transient.</span></span> <span data-ttu-id="99b9d-626">Eine praktische Erweiterungsmethode namens `AddTransientHttpErrorPolicy` ist enthalten. Sie ermöglicht das Definieren von Richtlinien zum Behandeln vorübergehender Fehler.</span><span class="sxs-lookup"><span data-stu-id="99b9d-626">A convenient extension method called `AddTransientHttpErrorPolicy` is included which allows a policy to be defined to handle transient errors.</span></span> <span data-ttu-id="99b9d-627">Richtlinien, die mit dieser Erweiterungsmethode konfiguriert wurden, behandeln `HttpRequestException`, HTTP 5xx-Antworten und HTTP 408-Antworten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-627">Policies configured with this extension method handle `HttpRequestException`, HTTP 5xx responses, and HTTP 408 responses.</span></span>

<span data-ttu-id="99b9d-628">Die `AddTransientHttpErrorPolicy`-Erweiterung kann in `Startup.ConfigureServices` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-628">The `AddTransientHttpErrorPolicy` extension can be used within `Startup.ConfigureServices`.</span></span> <span data-ttu-id="99b9d-629">Die Erweiterung ermöglicht den Zugriff auf ein `PolicyBuilder`-Objekt, das für die Fehlerbehandlung konfiguriert wurde und mögliche vorübergehende Fehler darstellt:</span><span class="sxs-lookup"><span data-stu-id="99b9d-629">The extension provides access to a `PolicyBuilder` object configured to handle errors representing a possible transient fault:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

<span data-ttu-id="99b9d-630">Im vorangehenden Code wird eine `WaitAndRetryAsync`-Richtlinie definiert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-630">In the preceding code, a `WaitAndRetryAsync` policy is defined.</span></span> <span data-ttu-id="99b9d-631">Anforderungsfehler werden bis zu dreimal mit einer Verzögerung von 600 ms wiederholt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-631">Failed requests are retried up to three times with a delay of 600 ms between attempts.</span></span>

### <a name="dynamically-select-policies"></a><span data-ttu-id="99b9d-632">Dynamisches Auswählen von Richtlinien</span><span class="sxs-lookup"><span data-stu-id="99b9d-632">Dynamically select policies</span></span>

<span data-ttu-id="99b9d-633">Es gibt zusätzliche Erweiterungsmethoden, die zum Hinzufügen von Polly-basierten Handlern verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="99b9d-633">Additional extension methods exist which can be used to add Polly-based handlers.</span></span> <span data-ttu-id="99b9d-634">Eine dieser Erweiterungen ist `AddPolicyHandler`, die über mehrere Überladungen verfügt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-634">One such extension is `AddPolicyHandler`, which has multiple overloads.</span></span> <span data-ttu-id="99b9d-635">Eine Überladung ermöglicht, dass die Anforderung beim Definieren der zu verwendenden Richtlinie überprüft werden kann:</span><span class="sxs-lookup"><span data-stu-id="99b9d-635">One overload allows the request to be inspected when defining which policy to apply:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

<span data-ttu-id="99b9d-636">Im vorangehenden Code wird ein 10 Sekunden langer Timeout angewendet, wenn die ausgehende Anforderung eine HTTP GET-Anforderung ist.</span><span class="sxs-lookup"><span data-stu-id="99b9d-636">In the preceding code, if the outgoing request is an HTTP GET, a 10-second timeout is applied.</span></span> <span data-ttu-id="99b9d-637">Für alle anderen HTTP-Methoden wird ein 30 Sekunden langer Timeout verwendet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-637">For any other HTTP method, a 30-second timeout is used.</span></span>

### <a name="add-multiple-polly-handlers"></a><span data-ttu-id="99b9d-638">Hinzufügen mehrerer Polly-Handler</span><span class="sxs-lookup"><span data-stu-id="99b9d-638">Add multiple Polly handlers</span></span>

<span data-ttu-id="99b9d-639">Es ist üblich, Polly-Richtlinien zu schachteln, um erweiterte Funktionen bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="99b9d-639">It's common to nest Polly policies to provide enhanced functionality:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

<span data-ttu-id="99b9d-640">Im vorangehenden Beispiel werden zwei Handler hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-640">In the preceding example, two handlers are added.</span></span> <span data-ttu-id="99b9d-641">Der Erste verwendet die `AddTransientHttpErrorPolicy`-Erweiterung, um eine Wiederholungsrichtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-641">The first uses the `AddTransientHttpErrorPolicy` extension to add a retry policy.</span></span> <span data-ttu-id="99b9d-642">Fehlgeschlagene Anforderungen werden bis zu drei Mal wiederholt.</span><span class="sxs-lookup"><span data-stu-id="99b9d-642">Failed requests are retried up to three times.</span></span> <span data-ttu-id="99b9d-643">Der zweite Aufruf von `AddTransientHttpErrorPolicy` fügt eine Trennschalterrichtlinie hinzu.</span><span class="sxs-lookup"><span data-stu-id="99b9d-643">The second call to `AddTransientHttpErrorPolicy` adds a circuit breaker policy.</span></span> <span data-ttu-id="99b9d-644">Weitere externe Anforderungen werden 30 Sekunden lang blockiert, wenn fünf Fehlversuche hintereinander stattfinden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-644">Further external requests are blocked for 30 seconds if five failed attempts occur sequentially.</span></span> <span data-ttu-id="99b9d-645">Trennschalterrichtlinien sind zustandsbehaftet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-645">Circuit breaker policies are stateful.</span></span> <span data-ttu-id="99b9d-646">Alle Aufrufe über diesen Client teilen den gleichen Schalterzustand.</span><span class="sxs-lookup"><span data-stu-id="99b9d-646">All calls through this client share the same circuit state.</span></span>

### <a name="add-policies-from-the-polly-registry"></a><span data-ttu-id="99b9d-647">Hinzufügen von Richtlinien aus der Polly-Registrierung</span><span class="sxs-lookup"><span data-stu-id="99b9d-647">Add policies from the Polly registry</span></span>

<span data-ttu-id="99b9d-648">Eine Methode zum Verwalten regelmäßig genutzter Richtlinien ist, Sie einmal zu definieren und mit `PolicyRegistry` zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="99b9d-648">An approach to managing regularly used policies is to define them once and register them with a `PolicyRegistry`.</span></span> <span data-ttu-id="99b9d-649">Eine Erweiterungsmethode wird bereitgestellt, die einem Handler ermöglicht, mithilfe einer Richtlinie aus der Registrierung hinzugefügt zu werden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-649">An extension method is provided which allows a handler to be added using a policy from the registry:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

<span data-ttu-id="99b9d-650">Im oben stehenden Code werden zwei Richtlinien registriert, wenn `PolicyRegistry` zu `ServiceCollection` hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-650">In the preceding code, two policies are registered when the `PolicyRegistry` is added to the `ServiceCollection`.</span></span> <span data-ttu-id="99b9d-651">Damit eine Richtlinie aus der Registrierung verwendet werden kann, wird die Methode `AddPolicyHandlerFromRegistry` verwendet. Dabei wird der Name der anzuwendenden Richtlinie übergeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-651">To use a policy from the registry, the `AddPolicyHandlerFromRegistry` method is used, passing the name of the policy to apply.</span></span>

<span data-ttu-id="99b9d-652">Weitere Informationen zu `IHttpClientFactory` und Polly-Integrationen finden Sie im [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span><span class="sxs-lookup"><span data-stu-id="99b9d-652">Further information about `IHttpClientFactory` and Polly integrations can be found on the [Polly wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).</span></span>

## <a name="httpclient-and-lifetime-management"></a><span data-ttu-id="99b9d-653">HttpClient und die Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="99b9d-653">HttpClient and lifetime management</span></span>

<span data-ttu-id="99b9d-654">Bei jedem Aufruf von `CreateClient` in der `IHttpClientFactory` wird eine neue Instanz von `HttpClient` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-654">A new `HttpClient` instance is returned each time `CreateClient` is called on the `IHttpClientFactory`.</span></span> <span data-ttu-id="99b9d-655">Es gibt einen <xref:System.Net.Http.HttpMessageHandler> pro benanntem Client.</span><span class="sxs-lookup"><span data-stu-id="99b9d-655">There's an <xref:System.Net.Http.HttpMessageHandler> per named client.</span></span> <span data-ttu-id="99b9d-656">Die Factory verwaltet die Lebensdauer der `HttpMessageHandler`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-656">The factory manages the lifetimes of the `HttpMessageHandler` instances.</span></span>

<span data-ttu-id="99b9d-657">`IHttpClientFactory` legt die `HttpMessageHandler`-Instanzen zusammen, die von der Factory zum Reduzieren des Ressourcenverbrauchs erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-657">`IHttpClientFactory` pools the `HttpMessageHandler` instances created by the factory to reduce resource consumption.</span></span> <span data-ttu-id="99b9d-658">Eine `HttpMessageHandler`-Instanz kann aus dem Pool wiederverwendet werden, wenn eine neue `HttpClient`-Instanz erstellt wird und deren Lebensdauer noch nicht abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="99b9d-658">An `HttpMessageHandler` instance may be reused from the pool when creating a new `HttpClient` instance if its lifetime hasn't expired.</span></span>

<span data-ttu-id="99b9d-659">Das Zusammenlegen von Handlern ist ein wünschenswerter Vorgang, da jeder Handler in der Regel seine zugrunde liegenden HTTP-Verbindungen selbst verwaltet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-659">Pooling of handlers is desirable as each handler typically manages its own underlying HTTP connections.</span></span> <span data-ttu-id="99b9d-660">Wenn mehr Handler als nötig erstellt werden, können Verzögerungen bei Verbindungen entstehen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-660">Creating more handlers than necessary can result in connection delays.</span></span> <span data-ttu-id="99b9d-661">Einige Handler halten Verbindungen auch unbegrenzt offen, was verhindert, dass der Handler auf DNS-Änderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-661">Some handlers also keep connections open indefinitely, which can prevent the handler from reacting to DNS changes.</span></span>

<span data-ttu-id="99b9d-662">Die Standardlebensdauer von Handlern beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-662">The default handler lifetime is two minutes.</span></span> <span data-ttu-id="99b9d-663">Der Standardwert kann für jeden benannten Client überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-663">The default value can be overridden on a per named client basis.</span></span> <span data-ttu-id="99b9d-664">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> auf dem bei der Erstellung des Clients zurückgegebenen `IHttpClientBuilder` auf, um den Wert zu überschreiben:</span><span class="sxs-lookup"><span data-stu-id="99b9d-664">To override it, call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> on the `IHttpClientBuilder` that is returned when creating the client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

<span data-ttu-id="99b9d-665">Das Verwerfen des Client ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="99b9d-665">Disposal of the client isn't required.</span></span> <span data-ttu-id="99b9d-666">Beim Verwerfen werden ausgehende Anforderungen abgebrochen, und es wird sichergestellt, dass die angegebene `HttpClient`-Instanz nach dem Aufruf von <xref:System.IDisposable.Dispose*> nicht mehr verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="99b9d-666">Disposal cancels outgoing requests and guarantees the given `HttpClient` instance can't be used after calling <xref:System.IDisposable.Dispose*>.</span></span> <span data-ttu-id="99b9d-667">`IHttpClientFactory` verfolgt von `HttpClient`-Instanzen verwendete Ressourcen nach und verwirft sie.</span><span class="sxs-lookup"><span data-stu-id="99b9d-667">`IHttpClientFactory` tracks and disposes resources used by `HttpClient` instances.</span></span> <span data-ttu-id="99b9d-668">Die `HttpClient`-Instanzen können im Allgemeinen als .NET-Objekte behandelt werden, die nicht verworfen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-668">The `HttpClient` instances can generally be treated as .NET objects not requiring disposal.</span></span>

<span data-ttu-id="99b9d-669">Das Beibehalten einer einzelnen `HttpClient`-Instanz für einen langen Zeitraum ist ein allgemeines Muster, das vor der Einführung von `IHttpClientFactory` verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="99b9d-669">Keeping a single `HttpClient` instance alive for a long duration is a common pattern used before the inception of `IHttpClientFactory`.</span></span> <span data-ttu-id="99b9d-670">Dieses Muster wird nach der Migration zu `IHttpClientFactory` überflüssig.</span><span class="sxs-lookup"><span data-stu-id="99b9d-670">This pattern becomes unnecessary after migrating to `IHttpClientFactory`.</span></span>

### <a name="alternatives-to-ihttpclientfactory"></a><span data-ttu-id="99b9d-671">Alternativen zu IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="99b9d-671">Alternatives to IHttpClientFactory</span></span>

<span data-ttu-id="99b9d-672">Mit der Verwendung von `IHttpClientFactory` in einer DI-fähigen App wird Folgendes vermieden:</span><span class="sxs-lookup"><span data-stu-id="99b9d-672">Using `IHttpClientFactory` in a DI-enabled app avoids:</span></span>

* <span data-ttu-id="99b9d-673">Probleme mit der Ressourcenauslastung durch Zusammenlegen von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="99b9d-673">Resource exhaustion problems by pooling `HttpMessageHandler` instances.</span></span>
* <span data-ttu-id="99b9d-674">Probleme durch veraltetes DNS durch regelmäßigen Wechsel von `HttpMessageHandler`-Instanzen</span><span class="sxs-lookup"><span data-stu-id="99b9d-674">Stale DNS problems by cycling `HttpMessageHandler` instances at regular intervals.</span></span>

<span data-ttu-id="99b9d-675">Es gibt alternative Möglichkeiten zum Lösen des vorangehenden Problems mithilfe einer langlebigen <xref:System.Net.Http.SocketsHttpHandler>-Instanz:</span><span class="sxs-lookup"><span data-stu-id="99b9d-675">There are alternative ways to solve the preceding problems using a long-lived <xref:System.Net.Http.SocketsHttpHandler> instance.</span></span>

- <span data-ttu-id="99b9d-676">Erstellen Sie eine `SocketsHttpHandler`-Instanz, wenn die App gestartet wird, und verwenden Sie diese für die Lebensdauer der App.</span><span class="sxs-lookup"><span data-stu-id="99b9d-676">Create an instance of `SocketsHttpHandler` when the app starts and use it for the life of the app.</span></span>
- <span data-ttu-id="99b9d-677">Konfigurieren Sie einen geeigneten Wert für <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> basierend auf den DNS-Aktualisierungszeiten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-677">Configure <xref:System.Net.Http.SocketsHttpHandler.PooledConnectionLifetime> to an appropriate value based on DNS refresh times.</span></span>
- <span data-ttu-id="99b9d-678">Erstellen Sie bei Bedarf `HttpClient`-Instanzen mithilfe von `new HttpClient(handler, disposeHandler: false)`.</span><span class="sxs-lookup"><span data-stu-id="99b9d-678">Create `HttpClient` instances using `new HttpClient(handler, disposeHandler: false)` as needed.</span></span>

<span data-ttu-id="99b9d-679">Diese Ansätze lösen die Ressourcenverwaltungsprobleme, die `IHttpClientFactory` auf ähnliche Weise löst.</span><span class="sxs-lookup"><span data-stu-id="99b9d-679">The preceding approaches solve the resource management problems that `IHttpClientFactory` solves in a similar way.</span></span>

- <span data-ttu-id="99b9d-680">`SocketsHttpHandler` gibt Verbindungen für `HttpClient`-Instanzen frei.</span><span class="sxs-lookup"><span data-stu-id="99b9d-680">The `SocketsHttpHandler` shares connections across `HttpClient` instances.</span></span> <span data-ttu-id="99b9d-681">Durch diese Freigabe wird die Erschöpfung von Sockets vermieden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-681">This sharing prevents socket exhaustion.</span></span>
- <span data-ttu-id="99b9d-682">`SocketsHttpHandler` wechselt die Verbindungen anhand von `PooledConnectionLifetime`, um Probleme durch veraltetes DNS zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-682">The `SocketsHttpHandler` cycles connections according to `PooledConnectionLifetime` to avoid stale DNS problems.</span></span>

### <a name="cookies"></a><span data-ttu-id="99b9d-683">Cookies</span><span class="sxs-lookup"><span data-stu-id="99b9d-683">Cookies</span></span>

<span data-ttu-id="99b9d-684">Die zusammengelegten `HttpMessageHandler`-Instanzen resultieren in der Freigabe von `CookieContainer`-Objekten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-684">The pooled `HttpMessageHandler` instances results in `CookieContainer` objects being shared.</span></span> <span data-ttu-id="99b9d-685">Die unerwartete Freigabe von `CookieContainer`-Objekten führt oft zu fehlerhaftem Code.</span><span class="sxs-lookup"><span data-stu-id="99b9d-685">Unanticipated `CookieContainer` object sharing often results in incorrect code.</span></span> <span data-ttu-id="99b9d-686">Ziehen Sie für Apps, die Cookies erfordern, folgende Vorgehensweisen in Betracht:</span><span class="sxs-lookup"><span data-stu-id="99b9d-686">For apps that require cookies, consider either:</span></span>

 - <span data-ttu-id="99b9d-687">Deaktivieren der automatischen Cookieverarbeitung</span><span class="sxs-lookup"><span data-stu-id="99b9d-687">Disabling automatic cookie handling</span></span>
 - <span data-ttu-id="99b9d-688">Vermeiden der Verwendung von `IHttpClientFactory`</span><span class="sxs-lookup"><span data-stu-id="99b9d-688">Avoiding `IHttpClientFactory`</span></span>

<span data-ttu-id="99b9d-689">Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> auf, um die automatische Cookieverarbeitung zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="99b9d-689">Call <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> to disable automatic cookie handling:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet13)]

## <a name="logging"></a><span data-ttu-id="99b9d-690">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="99b9d-690">Logging</span></span>

<span data-ttu-id="99b9d-691">Über `IHttpClientFactory` erstellte Clients zeichnen Protokollmeldungen für alle Anforderungen auf.</span><span class="sxs-lookup"><span data-stu-id="99b9d-691">Clients created via `IHttpClientFactory` record log messages for all requests.</span></span> <span data-ttu-id="99b9d-692">Aktivieren Sie die entsprechende Informationsebene in Ihrer Protokollierungskonfiguration, um die Standardprotokollmeldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="99b9d-692">Enable the appropriate information level in your logging configuration to see the default log messages.</span></span> <span data-ttu-id="99b9d-693">Zusätzliche Protokollierung, z.B. das Protokollieren von Anforderungsheadern, wird nur auf der Ablaufverfolgungsebene enthalten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-693">Additional logging, such as the logging of request headers, is only included at trace level.</span></span>

<span data-ttu-id="99b9d-694">Die Protokollierungskategorie für jeden Client enthält den Namen des Clients.</span><span class="sxs-lookup"><span data-stu-id="99b9d-694">The log category used for each client includes the name of the client.</span></span> <span data-ttu-id="99b9d-695">Ein Client namens *MyNamedClient* protokolliert beispielsweise Meldungen mit der Kategorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span><span class="sxs-lookup"><span data-stu-id="99b9d-695">A client named *MyNamedClient*, for example, logs messages with a category of `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`.</span></span> <span data-ttu-id="99b9d-696">Meldungen mit dem Suffix *LogicalHandler* treten außerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="99b9d-696">Messages suffixed with *LogicalHandler* occur outside the request handler pipeline.</span></span> <span data-ttu-id="99b9d-697">In der Anforderung werden Meldungen protokolliert, bevor andere Handler in der Pipeline sie verarbeitet haben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-697">On the request, messages are logged before any other handlers in the pipeline have processed it.</span></span> <span data-ttu-id="99b9d-698">In der Antwort werden Meldungen protokolliert, nachdem andere Handler in der Pipeline die Antwort empfangen haben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-698">On the response, messages are logged after any other pipeline handlers have received the response.</span></span>

<span data-ttu-id="99b9d-699">Die Protokollierung tritt ebenfalls innerhalb der Anforderungshandlerpipeline auf.</span><span class="sxs-lookup"><span data-stu-id="99b9d-699">Logging also occurs inside the request handler pipeline.</span></span> <span data-ttu-id="99b9d-700">Im Beispiel *MyNamedClient* werden diese Meldungen für die Protokollkategorie `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` protokolliert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-700">In the *MyNamedClient* example, those messages are logged against the log category `System.Net.Http.HttpClient.MyNamedClient.ClientHandler`.</span></span> <span data-ttu-id="99b9d-701">Dies findet für die Anforderung statt, nachdem alle anderen Handler ausgeführt wurden und bevor die Anforderung an das Netzwerk gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-701">For the request, this occurs after all other handlers have run and immediately before the request is sent out on the network.</span></span> <span data-ttu-id="99b9d-702">Diese Protokollierung enthält den Zustand der Antwort in der Antwort, bevor sie an die Handlerpipeline zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-702">On the response, this logging includes the state of the response before it passes back through the handler pipeline.</span></span>

<span data-ttu-id="99b9d-703">Das Aktivieren der Protokollierung außerhalb und innerhalb der Pipeline ermöglicht die Überprüfung der Änderungen, die durch andere Handler in der Pipeline erfolgt sind.</span><span class="sxs-lookup"><span data-stu-id="99b9d-703">Enabling logging outside and inside the pipeline enables inspection of the changes made by the other pipeline handlers.</span></span> <span data-ttu-id="99b9d-704">Dies kann beispielsweise die Änderungen an Anforderungsheadern oder am Statuscode der Antwort enthalten.</span><span class="sxs-lookup"><span data-stu-id="99b9d-704">This may include changes to request headers, for example, or to the response status code.</span></span>

<span data-ttu-id="99b9d-705">Das Einschließen des Namens des Clients in der Protokollkategorie aktiviert bei Bedarf die Protokollfilterung für spezifische benannte Clients.</span><span class="sxs-lookup"><span data-stu-id="99b9d-705">Including the name of the client in the log category enables log filtering for specific named clients where necessary.</span></span>

## <a name="configure-the-httpmessagehandler"></a><span data-ttu-id="99b9d-706">Konfigurieren von HttpMessageHandler</span><span class="sxs-lookup"><span data-stu-id="99b9d-706">Configure the HttpMessageHandler</span></span>

<span data-ttu-id="99b9d-707">Es kann notwendig sein, die Konfiguration des inneren von einem Client verwendeten `HttpMessageHandler` zu steuern.</span><span class="sxs-lookup"><span data-stu-id="99b9d-707">It may be necessary to control the configuration of the inner `HttpMessageHandler` used by a client.</span></span>

<span data-ttu-id="99b9d-708">`IHttpClientBuilder` wird zurückgegeben, wenn benannte oder typisierte Clients hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-708">An `IHttpClientBuilder` is returned when adding named or typed clients.</span></span> <span data-ttu-id="99b9d-709">Die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> kann zum Definieren eines Delegaten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="99b9d-709">The <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> extension method can be used to define a delegate.</span></span> <span data-ttu-id="99b9d-710">Der Delegat wird verwendet, um den primären `HttpMessageHandler` zu erstellen und konfigurieren, der von dem Client verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="99b9d-710">The delegate is used to create and configure the primary `HttpMessageHandler` used by that client:</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a><span data-ttu-id="99b9d-711">Verwenden von IHttpClientFactory in einer Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="99b9d-711">Use IHttpClientFactory in a console app</span></span>

<span data-ttu-id="99b9d-712">Fügen Sie in einer Konsolen-App dem Projekt die folgenden Paketverweise hinzu:</span><span class="sxs-lookup"><span data-stu-id="99b9d-712">In a console app, add the following package references to the project:</span></span>

* [<span data-ttu-id="99b9d-713">Microsoft.Extensions.Hosting</span><span class="sxs-lookup"><span data-stu-id="99b9d-713">Microsoft.Extensions.Hosting</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [<span data-ttu-id="99b9d-714">Microsoft.Extensions.Http</span><span class="sxs-lookup"><span data-stu-id="99b9d-714">Microsoft.Extensions.Http</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Http)

<span data-ttu-id="99b9d-715">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="99b9d-715">In the following example:</span></span>

* <span data-ttu-id="99b9d-716"><xref:System.Net.Http.IHttpClientFactory> ist im Dienstcontainer des [generischen Hosts](xref:fundamentals/host/generic-host) registriert.</span><span class="sxs-lookup"><span data-stu-id="99b9d-716"><xref:System.Net.Http.IHttpClientFactory> is registered in the [Generic Host's](xref:fundamentals/host/generic-host) service container.</span></span>
* <span data-ttu-id="99b9d-717">`MyService` erstellt eine Clientfactoryinstanz aus dem-Dienst, der zum Erstellen eines `HttpClient`verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="99b9d-717">`MyService` creates a client factory instance from the service, which is used to create an `HttpClient`.</span></span> <span data-ttu-id="99b9d-718">`HttpClient` wird zum Abrufen einer Webseite verwendet.</span><span class="sxs-lookup"><span data-stu-id="99b9d-718">`HttpClient` is used to retrieve a webpage.</span></span>
* <span data-ttu-id="99b9d-719">`Main` erstellt einen Bereich, um die `GetPage`-Methode des Diensts auszuführen und die ersten 500 Zeichen des Webseiteninhalts in die Konsole zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-719">`Main` creates a scope to execute the service's `GetPage` method and write the first 500 characters of the webpage content to the console.</span></span>

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

## <a name="header-propagation-middleware"></a><span data-ttu-id="99b9d-720">Middleware für Headerweitergabe</span><span class="sxs-lookup"><span data-stu-id="99b9d-720">Header propagation middleware</span></span>

<span data-ttu-id="99b9d-721">Für die Headerweitergabe wird eine von der Community unterstützte Middleware verwendet, um HTTP-Header von eingehenden Anforderungen an ausgehende HTTP-Clientanforderungen weiterzugeben.</span><span class="sxs-lookup"><span data-stu-id="99b9d-721">Header propagation is a community supported middleware to propagate HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> <span data-ttu-id="99b9d-722">So verwenden Sie die Headerweitergabe:</span><span class="sxs-lookup"><span data-stu-id="99b9d-722">To use header propagation:</span></span>

* <span data-ttu-id="99b9d-723">Erstellen Sie einen Verweis auf das von der Community unterstützte [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation)-Paket.</span><span class="sxs-lookup"><span data-stu-id="99b9d-723">Reference the community supported port of the package [HeaderPropagation](https://www.nuget.org/packages/HeaderPropagation).</span></span> <span data-ttu-id="99b9d-724">ASp.NET Core 3.1 und höher unterstützen [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span><span class="sxs-lookup"><span data-stu-id="99b9d-724">ASP.NET Core 3.1 and later supports [Microsoft.AspNetCore.HeaderPropagation](https://www.nuget.org/packages/Microsoft.AspNetCore.HeaderPropagation).</span></span>

* <span data-ttu-id="99b9d-725">Konfigurieren Sie in `Startup` die Middleware und `HttpClient`:</span><span class="sxs-lookup"><span data-stu-id="99b9d-725">Configure the middleware and `HttpClient` in `Startup`:</span></span>

  [!code-csharp[](http-requests/samples/2.x/Startup21.cs?highlight=5-9,25&name=snippet)]

* <span data-ttu-id="99b9d-726">Der Client schließt die konfigurierten Header für ausgehende Anforderungen ein:</span><span class="sxs-lookup"><span data-stu-id="99b9d-726">The client includes the configured headers on outbound requests:</span></span>

  ```csharp
  var client = clientFactory.CreateClient("MyForwardingClient");
  var response = client.GetAsync(...);
  ```

## <a name="additional-resources"></a><span data-ttu-id="99b9d-727">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="99b9d-727">Additional resources</span></span>

* [<span data-ttu-id="99b9d-728">Verwenden von HttpClientFactory zur Implementierung robuster HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="99b9d-728">Use HttpClientFactory to implement resilient HTTP requests</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [<span data-ttu-id="99b9d-729">Implementieren von Wiederholungen von HTTP-Aufrufen mit exponentiellem Backoff mit HttpClientFactory und Polly-Richtlinien</span><span class="sxs-lookup"><span data-stu-id="99b9d-729">Implement HTTP call retries with exponential backoff with HttpClientFactory and Polly policies</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [<span data-ttu-id="99b9d-730">Implementieren des Trennschaltermusters</span><span class="sxs-lookup"><span data-stu-id="99b9d-730">Implement the Circuit Breaker pattern</span></span>](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)

::: moniker-end
