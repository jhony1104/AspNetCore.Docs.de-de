---
title: Anforderungsfeatures in ASP.NET Core
author: ardalis
description: Erfahren Sie mehr zur Webserverimplementierung, die mit HTTP-Anforderungen und -antworten in Verbindung stehen, die in Schnittstellen definiert werden.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651991"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="1da3b-103">Anforderungsfeatures in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1da3b-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="1da3b-104">Von [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="1da3b-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1da3b-105">Ausführliche Informationen zur Webserverimplementierung, die mit HTTP-Anforderungen und -antworten in Verbindung stehen, werden in Schnittstellen definiert.</span><span class="sxs-lookup"><span data-stu-id="1da3b-105">Web server implementation details related to HTTP requests and responses are defined in interfaces.</span></span> <span data-ttu-id="1da3b-106">Diese Schnittstellen werden von Serverimplementierungen und Middleware verwendet, um die Hostingpipeline der App zu erstellen und anzupassen.</span><span class="sxs-lookup"><span data-stu-id="1da3b-106">These interfaces are used by server implementations and middleware to create and modify the application's hosting pipeline.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="1da3b-107">Featureschnittstellen</span><span class="sxs-lookup"><span data-stu-id="1da3b-107">Feature interfaces</span></span>

<span data-ttu-id="1da3b-108">ASP.NET Core definiert einige HTTP-Featureschnittstellen in `Microsoft.AspNetCore.Http.Features`, die von Servern verwendet werden, um unterstützte Features zu erkennen.</span><span class="sxs-lookup"><span data-stu-id="1da3b-108">ASP.NET Core defines a number of HTTP feature interfaces in `Microsoft.AspNetCore.Http.Features` which are used by servers to identify the features they support.</span></span> <span data-ttu-id="1da3b-109">Die folgenden Featureschnittstellen verarbeiten Anforderungen und geben Antworten zurück:</span><span class="sxs-lookup"><span data-stu-id="1da3b-109">The following feature interfaces handle requests and return responses:</span></span>

<span data-ttu-id="1da3b-110">`IHttpRequestFeature` definiert die Struktur einer HTTP-Anforderung, einschließlich Protokoll, Pfad, Abfragezeichenfolge, Header und Text.</span><span class="sxs-lookup"><span data-stu-id="1da3b-110">`IHttpRequestFeature` Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span>

<span data-ttu-id="1da3b-111">`IHttpResponseFeature` definiert die Struktur einer HTTP-Antwort, einschließlich Statuscode, Header und Antworttext.</span><span class="sxs-lookup"><span data-stu-id="1da3b-111">`IHttpResponseFeature` Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span>

<span data-ttu-id="1da3b-112">`IHttpAuthenticationFeature` definiert die Unterstützung für das Erkennen von Benutzern auf Grundlage eines `ClaimsPrincipal` und für das Festlegen eines Authentifizierungshandlers.</span><span class="sxs-lookup"><span data-stu-id="1da3b-112">`IHttpAuthenticationFeature` Defines support for identifying users based on a `ClaimsPrincipal` and specifying an authentication handler.</span></span>

<span data-ttu-id="1da3b-113">`IHttpUpgradeFeature` definiert Unterstützung für [HTTP-Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), die es dem Client ermöglichen, festzulegen, welche zusätzlichen Protokolle er verwenden möchte, wenn der Server Protokolle wechseln möchte.</span><span class="sxs-lookup"><span data-stu-id="1da3b-113">`IHttpUpgradeFeature` Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="1da3b-114">`IHttpBufferingFeature` definiert Methoden zum Deaktivieren des Pufferns von Anforderungen und/oder Antworten.</span><span class="sxs-lookup"><span data-stu-id="1da3b-114">`IHttpBufferingFeature` Defines methods for disabling buffering of requests and/or responses.</span></span>

<span data-ttu-id="1da3b-115">`IHttpConnectionFeature` definiert Eigenschaften für lokale und Remote-Adressen und Ports.</span><span class="sxs-lookup"><span data-stu-id="1da3b-115">`IHttpConnectionFeature` Defines properties for local and remote addresses and ports.</span></span>

<span data-ttu-id="1da3b-116">`IHttpRequestLifetimeFeature` definiert Unterstützung für das Abbrechen von Verbindungen oder das Erkennen einer verfrühten Beendung einer Anforderung, z.B. durch eine abgebrochene Verbindung zum Client.</span><span class="sxs-lookup"><span data-stu-id="1da3b-116">`IHttpRequestLifetimeFeature` Defines support for aborting connections, or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

<span data-ttu-id="1da3b-117">`IHttpSendFileFeature` definiert eine Methode zum asynchronen Senden von Dateien.</span><span class="sxs-lookup"><span data-stu-id="1da3b-117">`IHttpSendFileFeature` Defines a method for sending files asynchronously.</span></span>

<span data-ttu-id="1da3b-118">`IHttpWebSocketFeature` definiert eine API zur Unterstützung von Websockets.</span><span class="sxs-lookup"><span data-stu-id="1da3b-118">`IHttpWebSocketFeature` Defines an API for supporting web sockets.</span></span>

<span data-ttu-id="1da3b-119">`IHttpRequestIdentifierFeature` fügt eine Eigenschaft hinzu, die implementiert werden kann, um eine Anforderung eindeutig identifizieren zu können.</span><span class="sxs-lookup"><span data-stu-id="1da3b-119">`IHttpRequestIdentifierFeature` Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="1da3b-120">`ISessionFeature` definiert die Abstraktionen `ISessionFactory` und `ISession` zur Unterstützung von Benutzersitzungen.</span><span class="sxs-lookup"><span data-stu-id="1da3b-120">`ISessionFeature` Defines `ISessionFactory` and `ISession` abstractions for supporting user sessions.</span></span>

<span data-ttu-id="1da3b-121">`ITlsConnectionFeature` definiert eine API zum Abrufen von Clientzertifikaten.</span><span class="sxs-lookup"><span data-stu-id="1da3b-121">`ITlsConnectionFeature` Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="1da3b-122">`ITlsTokenBindingFeature` definiert Methoden zum Arbeiten mit TLS-Tokenbindungsparametern.</span><span class="sxs-lookup"><span data-stu-id="1da3b-122">`ITlsTokenBindingFeature` Defines methods for working with TLS token binding parameters.</span></span>

> [!NOTE]
> <span data-ttu-id="1da3b-123">`ISessionFeature` ist kein Serverfeature, wird aber von der `SessionMiddleware` implementiert. Weitere Informationen finden Sie im Artikel zur [Verwaltung des Anwendungszustandes](app-state.md).</span><span class="sxs-lookup"><span data-stu-id="1da3b-123">`ISessionFeature` isn't a server feature, but is implemented by the `SessionMiddleware` (see [Managing Application State](app-state.md)).</span></span>

## <a name="feature-collections"></a><span data-ttu-id="1da3b-124">Featuresammlung</span><span class="sxs-lookup"><span data-stu-id="1da3b-124">Feature collections</span></span>

<span data-ttu-id="1da3b-125">Die `Features`-Eigenschaft von `HttpContext` bietet eine Schnittstelle zum Abrufen und Festlegen der verfügbaren HTTP-Features für die aktuelle Anforderung.</span><span class="sxs-lookup"><span data-stu-id="1da3b-125">The `Features` property of `HttpContext` provides an interface for getting and setting the available HTTP features for the current request.</span></span> <span data-ttu-id="1da3b-126">Da die Featuresammlung auch im Kontext einer Anforderung änderbar ist, kann Middleware verwendet werden, um die Sammlung zu modifizieren und Unterstützung für zusätzliche Features hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="1da3b-126">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span>

## <a name="middleware-and-request-features"></a><span data-ttu-id="1da3b-127">Middleware- und Anforderungsfeatures</span><span class="sxs-lookup"><span data-stu-id="1da3b-127">Middleware and request features</span></span>

<span data-ttu-id="1da3b-128">Obwohl Server für das Erstellen von Featuresammlungen zuständig sind, kann Middleware Features zu dieser hinzufügen und darin befindliche Features nutzen.</span><span class="sxs-lookup"><span data-stu-id="1da3b-128">While servers are responsible for creating the feature collection, middleware can both add to this collection and consume features from the collection.</span></span> <span data-ttu-id="1da3b-129">Die `StaticFileMiddleware` greift z.B. auf das `IHttpSendFileFeature`-Feature zu.</span><span class="sxs-lookup"><span data-stu-id="1da3b-129">For example, the `StaticFileMiddleware` accesses the `IHttpSendFileFeature` feature.</span></span> <span data-ttu-id="1da3b-130">Wenn das Feature vorhanden ist, wird es verwendet, um die angeforderte statische Datei von deren physischem Pfad aus zu senden.</span><span class="sxs-lookup"><span data-stu-id="1da3b-130">If the feature exists, it's used to send the requested static file from its physical path.</span></span> <span data-ttu-id="1da3b-131">Andernfalls wird eine alternative, langsamere Methode zum Senden der Datei verwendet.</span><span class="sxs-lookup"><span data-stu-id="1da3b-131">Otherwise, a slower alternative method is used to send the file.</span></span> <span data-ttu-id="1da3b-132">Wenn dies verfügbar ist, ermöglicht das `IHttpSendFileFeature` dem Betriebssystem das Öffnen der Datei und das Erstellen einer direkten Kopie im Kernelmodus auf der Netzwerkkarte.</span><span class="sxs-lookup"><span data-stu-id="1da3b-132">When available, the `IHttpSendFileFeature` allows the operating system to open the file and perform a direct kernel mode copy to the network card.</span></span>

<span data-ttu-id="1da3b-133">Zusätzlich kann Middleware Features zur Featuresammlung des Servers hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="1da3b-133">Additionally, middleware can add to the feature collection established by the server.</span></span> <span data-ttu-id="1da3b-134">Vorhandene Features können von Middleware ersetzt werden, sodass die Middleware die Funktionalität des Servers beeinflussen kann.</span><span class="sxs-lookup"><span data-stu-id="1da3b-134">Existing features can even be replaced by middleware, allowing the middleware to augment the functionality of the server.</span></span> <span data-ttu-id="1da3b-135">Features, die der Auflistung hinzugefügt wurden, stehen umgehend auch anderer Middleware oder der zugrunde liegenden Anwendung selbst zu einem späteren Zeitpunkt in der Anforderungspipeline zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="1da3b-135">Features added to the collection are available immediately to other middleware or the underlying application itself later in the request pipeline.</span></span>

<span data-ttu-id="1da3b-136">Anhand der Kombination benutzerdefinierter Serverimplementierungen und spezifischer Middlewareverbesserungen kann der genaue Satz von Features, den eine Anwendung erfordert, erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="1da3b-136">By combining custom server implementations and specific middleware enhancements, the precise set of features an application requires can be constructed.</span></span> <span data-ttu-id="1da3b-137">So können fehlende Features hinzugefügt werden, ohne dass der Server verändert werden muss. Zudem wird sichergestellt, dass nur so viele Features wie unbedingt nötig verfügbar gemacht werden, sodass die Angriffsfläche auf einem Minimum gehalten und die Leistung verbessert wird.</span><span class="sxs-lookup"><span data-stu-id="1da3b-137">This allows missing features to be added without requiring a change in server, and ensures only the minimal amount of features are exposed, thus limiting attack surface area and improving performance.</span></span>

## <a name="summary"></a><span data-ttu-id="1da3b-138">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="1da3b-138">Summary</span></span>

<span data-ttu-id="1da3b-139">Featureschnittstellen definieren spezifische HTTP-Features, die von einer angegebenen Anforderung möglicherweise unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="1da3b-139">Feature interfaces define specific HTTP features that a given request may support.</span></span> <span data-ttu-id="1da3b-140">Server definieren Featuresammlungen und den ersten Satz von Features, die von diesem Server unterstützt werden. Middleware kann jedoch verwendet werden, um diese Features zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="1da3b-140">Servers define collections of features, and the initial set of features supported by that server, but middleware can be used to enhance these features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1da3b-141">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1da3b-141">Additional resources</span></span>

* [<span data-ttu-id="1da3b-142">Server</span><span class="sxs-lookup"><span data-stu-id="1da3b-142">Servers</span></span>](xref:fundamentals/servers/index)
* [<span data-ttu-id="1da3b-143">Middleware</span><span class="sxs-lookup"><span data-stu-id="1da3b-143">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="1da3b-144">Open Web Interface for .NET (OWIN)</span><span class="sxs-lookup"><span data-stu-id="1da3b-144">Open Web Interface for .NET (OWIN)</span></span>](xref:fundamentals/owin)
