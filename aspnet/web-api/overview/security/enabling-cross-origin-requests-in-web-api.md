---
uid: web-api/overview/security/enabling-cross-origin-requests-in-web-api
title: Aktivieren von ursprungsübergreifenden Anforderungen in ASP.NET-Web-API 2 | Microsoft-Dokumentation
author: MikeWasson
description: Zeigt, wie zur Unterstützung von Cross-Origin Resource Sharing (CORS) in ASP.NET Web-API.
ms.author: riande
ms.date: 01/29/2019
ms.assetid: 9b265a5a-6a70-4a82-adce-2d7c56ae8bdd
msc.legacyurl: /web-api/overview/security/enabling-cross-origin-requests-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 97a0027194b019b09e220493dcb593e682027fe3
ms.sourcegitcommit: d22b3c23c45a076c4f394a70b1c8df2fbcdf656d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55428446"
---
<a name="enable-cross-origin-requests-in-aspnet-web-api-2"></a><span data-ttu-id="c490c-103">Aktivieren Sie ursprungsübergreifender Anforderungen in ASP.NET Web API 2</span><span class="sxs-lookup"><span data-stu-id="c490c-103">Enable cross-origin requests in ASP.NET Web API 2</span></span>
====================
<span data-ttu-id="c490c-104">durch [Mike Wasson](https://github.com/MikeWasson)</span><span class="sxs-lookup"><span data-stu-id="c490c-104">by [Mike Wasson](https://github.com/MikeWasson)</span></span>

> <span data-ttu-id="c490c-105">Browsersicherheit verhindert, dass eine Webseite AJAX-Anforderungen in eine andere Domäne.</span><span class="sxs-lookup"><span data-stu-id="c490c-105">Browser security prevents a web page from making AJAX requests to another domain.</span></span> <span data-ttu-id="c490c-106">Diese Einschränkung wird aufgerufen, die *Richtlinie desselben Ursprungs*, und verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest.</span><span class="sxs-lookup"><span data-stu-id="c490c-106">This restriction is called the *same-origin policy*, and prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="c490c-107">Jedoch sollten Sie manchmal auf andere Standorte Ihrer Web-API aufrufen können.</span><span class="sxs-lookup"><span data-stu-id="c490c-107">However, sometimes you might want to let other sites call your web API.</span></span>
>
> <span data-ttu-id="c490c-108">[Cross-Origin Resource Sharing](http://www.w3.org/TR/cors/) (CORS) ist ein W3C-Standard, der einem Server zu lockern die Richtlinie des gleichen Ursprungs ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="c490c-108">[Cross Origin Resource Sharing](http://www.w3.org/TR/cors/) (CORS) is a W3C standard that allows a server to relax the same-origin policy.</span></span> <span data-ttu-id="c490c-109">Mit CORS kann ein Server explizit einige ursprungsübergreifende Anforderungen zulassen und andere ablehnen.</span><span class="sxs-lookup"><span data-stu-id="c490c-109">Using CORS, a server can explicitly allow some cross-origin requests while rejecting others.</span></span> <span data-ttu-id="c490c-110">CORS ist sicherer und flexibler als frühere Techniken wie z. B. [JSONP](http://en.wikipedia.org/wiki/JSONP).</span><span class="sxs-lookup"><span data-stu-id="c490c-110">CORS is safer and more flexible than earlier techniques such as [JSONP](http://en.wikipedia.org/wiki/JSONP).</span></span> <span data-ttu-id="c490c-111">In diesem Tutorial veranschaulicht das Aktivieren von CORS in Ihrer Web-API-Anwendung.</span><span class="sxs-lookup"><span data-stu-id="c490c-111">This tutorial shows how to enable CORS in your Web API application.</span></span>
>
> ## <a name="software-used-in-the-tutorial"></a><span data-ttu-id="c490c-112">In diesem Tutorial verwendete Software</span><span class="sxs-lookup"><span data-stu-id="c490c-112">Software used in the tutorial</span></span>
>
> - [<span data-ttu-id="c490c-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c490c-113">Visual Studio</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)
> - <span data-ttu-id="c490c-114">Web-API 2.2</span><span class="sxs-lookup"><span data-stu-id="c490c-114">Web API 2.2</span></span>

## <a name="introduction"></a><span data-ttu-id="c490c-115">Einführung</span><span class="sxs-lookup"><span data-stu-id="c490c-115">Introduction</span></span>

<span data-ttu-id="c490c-116">In diesem Tutorial wird veranschaulicht, dass CORS-Unterstützung in ASP.NET Web-API.</span><span class="sxs-lookup"><span data-stu-id="c490c-116">This tutorial demonstrates CORS support in ASP.NET Web API.</span></span> <span data-ttu-id="c490c-117">Wir beginnen, erstellen Sie zwei ASP.NET-Projekte – einen namens "WebService", hostet einen Web-API-Controller, und die anderen namens "" Webclient "" der Webdienst aufruft.</span><span class="sxs-lookup"><span data-stu-id="c490c-117">We'll start by creating two ASP.NET projects – one called "WebService", which hosts a Web API controller, and the other called "WebClient", which calls WebService.</span></span> <span data-ttu-id="c490c-118">Da die beiden Anwendungen auf verschiedenen Domänen gehostet werden, ist eine AJAX-Anforderung von "Webclient" auf den Webdienst eine cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c490c-118">Because the two applications are hosted at different domains, an AJAX request from WebClient to WebService is a cross-origin request.</span></span>

![](enabling-cross-origin-requests-in-web-api/_static/image1.png)

### <a name="what-is-same-origin"></a><span data-ttu-id="c490c-119">Was ist "denselben Ursprung"?</span><span class="sxs-lookup"><span data-stu-id="c490c-119">What is "same origin"?</span></span>

<span data-ttu-id="c490c-120">Zwei URLs haben denselben Ursprung ggf. identische Schemas, Hosts und -Ports.</span><span class="sxs-lookup"><span data-stu-id="c490c-120">Two URLs have the same origin if they have identical schemes, hosts, and ports.</span></span> <span data-ttu-id="c490c-121">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span><span class="sxs-lookup"><span data-stu-id="c490c-121">([RFC 6454](http://tools.ietf.org/html/rfc6454))</span></span>

<span data-ttu-id="c490c-122">Diese zwei URLs haben denselben Ursprung an:</span><span class="sxs-lookup"><span data-stu-id="c490c-122">These two URLs have the same origin:</span></span>

- `http://example.com/foo.html`
- `http://example.com/bar.html`

<span data-ttu-id="c490c-123">Diese URLs haben verschiedene Ursprünge als die vorherige zwei:</span><span class="sxs-lookup"><span data-stu-id="c490c-123">These URLs have different origins than the previous two:</span></span>

- <span data-ttu-id="c490c-124">`http://example.net` -Andere Domäne</span><span class="sxs-lookup"><span data-stu-id="c490c-124">`http://example.net` - Different domain</span></span>
- <span data-ttu-id="c490c-125">`http://example.com:9000/foo.html` -Portnummer</span><span class="sxs-lookup"><span data-stu-id="c490c-125">`http://example.com:9000/foo.html` - Different port</span></span>
- <span data-ttu-id="c490c-126">`https://example.com/foo.html` -Andere Partitionsschema</span><span class="sxs-lookup"><span data-stu-id="c490c-126">`https://example.com/foo.html` - Different scheme</span></span>
- <span data-ttu-id="c490c-127">`http://www.example.com/foo.html` -Andere Unterdomäne</span><span class="sxs-lookup"><span data-stu-id="c490c-127">`http://www.example.com/foo.html` - Different subdomain</span></span>

> [!NOTE]
> <span data-ttu-id="c490c-128">Internet Explorer wird den Port nicht berücksichtigt, für den Vergleich Ursprünge.</span><span class="sxs-lookup"><span data-stu-id="c490c-128">Internet Explorer does not consider the port when comparing origins.</span></span>

## <a name="create-the-webservice-project"></a><span data-ttu-id="c490c-129">Erstellen Sie das WebService-Projekt</span><span class="sxs-lookup"><span data-stu-id="c490c-129">Create the WebService project</span></span>

> [!NOTE]
> <span data-ttu-id="c490c-130">In diesem Abschnitt wird davon ausgegangen, dass Sie bereits wissen, wie Web-API-Projekte erstellen.</span><span class="sxs-lookup"><span data-stu-id="c490c-130">This section assumes you already know how to create Web API projects.</span></span> <span data-ttu-id="c490c-131">Falls nicht, siehe [erste Schritte mit ASP.NET Web-API](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).</span><span class="sxs-lookup"><span data-stu-id="c490c-131">If not, see [Getting Started with ASP.NET Web API](../getting-started-with-aspnet-web-api/tutorial-your-first-web-api.md).</span></span>

1. <span data-ttu-id="c490c-132">Starten Sie Visual Studio, und erstellen Sie ein neues **ASP.NET-Webanwendung ((.NET Framework)** Projekt.</span><span class="sxs-lookup"><span data-stu-id="c490c-132">Start Visual Studio and create a new **ASP.NET Web Application (.NET Framework)** project.</span></span>
2. <span data-ttu-id="c490c-133">In der **neue ASP.NET-Webanwendung** wählen Sie im Dialogfeld die **leere** Projektvorlage.</span><span class="sxs-lookup"><span data-stu-id="c490c-133">In the **New ASP.NET Web Application** dialog box, select the **Empty** project template.</span></span> <span data-ttu-id="c490c-134">Klicken Sie unter **fügen Sie Ordner und kernreferenzen für**, wählen die **Web-API-** Kontrollkästchen.</span><span class="sxs-lookup"><span data-stu-id="c490c-134">Under **Add folders and core references for**, select the **Web API** checkbox.</span></span>

   ![Dialogfeld "neue ASP.NET Projekt" in Visual Studio](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog.png)

3. <span data-ttu-id="c490c-136">Fügen Sie einen Web-API-Controller mit dem Namen `TestController` durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c490c-136">Add a Web API controller named `TestController` with the following code:</span></span>

   [!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample1.cs)]

4. <span data-ttu-id="c490c-137">Sie können die Anwendung lokal ausführen oder in Azure bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="c490c-137">You can run the application locally or deploy to Azure.</span></span> <span data-ttu-id="c490c-138">(Die Screenshots in diesem Tutorial wird die app in Azure App Service Web Apps bereitgestellt.) Navigieren Sie zu, um sicherzustellen, dass die Web-API arbeitet, `http://hostname/api/test/`, wobei *Hostname* ist die Domäne, in dem Sie die Anwendung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c490c-138">(For the screenshots in this tutorial, the app deploys to Azure App Service Web Apps.) To verify that the web API is working, navigate to `http://hostname/api/test/`, where *hostname* is the domain where you deployed the application.</span></span> <span data-ttu-id="c490c-139">Daraufhin sollte der Antworttext, &quot;abrufen: Testnachricht&quot;.</span><span class="sxs-lookup"><span data-stu-id="c490c-139">You should see the response text, &quot;GET: Test Message&quot;.</span></span>

   ![Internet-Browser mit Test-Nachricht](enabling-cross-origin-requests-in-web-api/_static/image4.png)

## <a name="create-the-webclient-project"></a><span data-ttu-id="c490c-141">Erstellen Sie das Projekt "Webclient"</span><span class="sxs-lookup"><span data-stu-id="c490c-141">Create the WebClient project</span></span>

1. <span data-ttu-id="c490c-142">Erstellen Sie eine weitere **ASP.NET-Webanwendung ((.NET Framework)** Projekt, und wählen die **MVC** Projektvorlage.</span><span class="sxs-lookup"><span data-stu-id="c490c-142">Create another **ASP.NET Web Application (.NET Framework)** project and select the **MVC** project template.</span></span> <span data-ttu-id="c490c-143">Wählen Sie optional **Authentifizierung ändern** > **keine Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="c490c-143">Optionally, select **Change Authentication** > **No Authentication**.</span></span> <span data-ttu-id="c490c-144">Für dieses Tutorial benötigen Sie keine Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="c490c-144">You don't need authentication for this tutorial.</span></span>

   ![MVC-Vorlage im Dialogfeld für neues ASP.NET-Projekt in Visual Studio](enabling-cross-origin-requests-in-web-api/_static/new-web-app-dialog-mvc.png)

2. <span data-ttu-id="c490c-146">In **Projektmappen-Explorer**, öffnen Sie die Datei *Views/Home/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="c490c-146">In **Solution Explorer**, open the file *Views/Home/Index.cshtml*.</span></span> <span data-ttu-id="c490c-147">Ersetzen Sie den Code in dieser Datei durch Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="c490c-147">Replace the code in this file with the following:</span></span>

   [!code-cshtml[Main](enabling-cross-origin-requests-in-web-api/samples/sample2.cshtml?highlight=13)]

   <span data-ttu-id="c490c-148">Für die *ServiceUrl* Variable, verwenden Sie den URI der WebService-app.</span><span class="sxs-lookup"><span data-stu-id="c490c-148">For the *serviceUrl* variable, use the URI of the WebService app.</span></span>

3. <span data-ttu-id="c490c-149">Führen Sie den WebClient-app lokal oder in einer anderen Website veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="c490c-149">Run the WebClient app locally or publish it to another website.</span></span>

<span data-ttu-id="c490c-150">Wenn Sie die Schaltfläche "Ausprobieren" klicken, wird eine AJAX-Anforderung übermittelt, für die WebService-app, die mit der HTTP-Methode aufgeführt, die der Dropdownliste (GET, POST oder PUT).</span><span class="sxs-lookup"><span data-stu-id="c490c-150">When you click the "Try It" button, an AJAX request is submitted to the WebService app using the HTTP method listed in the dropdown box (GET, POST, or PUT).</span></span> <span data-ttu-id="c490c-151">Dadurch können Sie die verschiedene Cross-Origin-Anforderungen zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="c490c-151">This lets you examine different cross-origin requests.</span></span> <span data-ttu-id="c490c-152">Die WebService-app unterstützt derzeit keine CORS daher, wenn Sie auf die Schaltfläche klicken müssen Sie eine Fehlermeldung erhalten werden.</span><span class="sxs-lookup"><span data-stu-id="c490c-152">Currently, the WebService app does not support CORS, so if you click the button you'll get an error.</span></span>

![Fehler "Ausprobieren", im browser](enabling-cross-origin-requests-in-web-api/_static/image7.png)

> [!NOTE]
> <span data-ttu-id="c490c-154">Wenn Sie sehen Sie sich den HTTP-Datenverkehr in einem Tool wie [Fiddler](https://www.telerik.com/fiddler), sehen Sie, dass der Browser die GET-Anforderung sendet, und die Anforderung erfolgreich ist, aber der AJAX-Aufruf gibt einen Fehler zurück.</span><span class="sxs-lookup"><span data-stu-id="c490c-154">If you watch the HTTP traffic in a tool like [Fiddler](https://www.telerik.com/fiddler), you'll see that the browser does send the GET request, and the request succeeds, but the AJAX call returns an error.</span></span> <span data-ttu-id="c490c-155">Es ist wichtig zu verstehen, dass die Richtlinie desselben Ursprungs nicht durch den Browser verhindert *senden* der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c490c-155">It's important to understand that same-origin policy does not prevent the browser from *sending* the request.</span></span> <span data-ttu-id="c490c-156">Stattdessen es verhindert, dass die Anwendung sehen die *Antwort*.</span><span class="sxs-lookup"><span data-stu-id="c490c-156">Instead, it prevents the application from seeing the *response*.</span></span>

![Fiddler-webdebugger, die webanforderungen anzeigen](enabling-cross-origin-requests-in-web-api/_static/image8.png)

## <a name="enable-cors"></a><span data-ttu-id="c490c-158">Aktivieren von CORS</span><span class="sxs-lookup"><span data-stu-id="c490c-158">Enable CORS</span></span>

<span data-ttu-id="c490c-159">Jetzt aktivieren wir CORS in der WebService-app.</span><span class="sxs-lookup"><span data-stu-id="c490c-159">Now let's enable CORS in the WebService app.</span></span> <span data-ttu-id="c490c-160">Fügen Sie zunächst das CORS-NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="c490c-160">First, add the CORS NuGet package.</span></span> <span data-ttu-id="c490c-161">In Visual Studio aus der **Tools** die Option **NuGet Paket-Manager**, wählen Sie **Paket-Manager Konsole**.</span><span class="sxs-lookup"><span data-stu-id="c490c-161">In Visual Studio, from the **Tools** menu, select **NuGet Package Manager**, then select **Package Manager Console**.</span></span> <span data-ttu-id="c490c-162">Geben Sie im Fenster Paket-Manager-Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="c490c-162">In the Package Manager Console window, type the following command:</span></span>

[!code-powershell[Main](enabling-cross-origin-requests-in-web-api/samples/sample3.ps1)]

<span data-ttu-id="c490c-163">Dieser Befehl installiert das neueste Paket und alle Abhängigkeiten, einschließlich der Core-Web-API-Bibliotheken aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="c490c-163">This command installs the latest package and updates all dependencies, including the core Web API libraries.</span></span> <span data-ttu-id="c490c-164">Verwenden der `-Version` Flag, um eine bestimmte Version abzielen.</span><span class="sxs-lookup"><span data-stu-id="c490c-164">Use the `-Version` flag to target a specific version.</span></span> <span data-ttu-id="c490c-165">Das CORS-Paket ist die Web-API 2.0 oder höher erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c490c-165">The CORS package requires Web API 2.0 or later.</span></span>

<span data-ttu-id="c490c-166">Öffnen Sie die Datei *App\_Start/WebApiConfig.cs*.</span><span class="sxs-lookup"><span data-stu-id="c490c-166">Open the file *App\_Start/WebApiConfig.cs*.</span></span> <span data-ttu-id="c490c-167">Fügen Sie den folgenden Code der **WebApiConfig.Register** Methode:</span><span class="sxs-lookup"><span data-stu-id="c490c-167">Add the following code to the **WebApiConfig.Register** method:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample4.cs?highlight=9)]

<span data-ttu-id="c490c-168">Fügen Sie als Nächstes die **[EnableCors]** -Attribut auf die `TestController` Klasse:</span><span class="sxs-lookup"><span data-stu-id="c490c-168">Next, add the **[EnableCors]** attribute to the `TestController` class:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample5.cs?highlight=3,7)]

<span data-ttu-id="c490c-169">Für die *Ursprünge* -Parameter verwenden Sie den URI, wo Sie die Anwendung "Webclient" bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c490c-169">For the *origins* parameter, use the URI where you deployed the WebClient application.</span></span> <span data-ttu-id="c490c-170">Dies erlaubt Cross-Origin-Anfragen von "Webclient" untersagen weiterhin auf alle anderen domänenübergreifende Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="c490c-170">This allows cross-origin requests from WebClient, while still disallowing all other cross-domain requests.</span></span> <span data-ttu-id="c490c-171">Ich beschreibe später die Parameter für **[EnableCors]** im Detail.</span><span class="sxs-lookup"><span data-stu-id="c490c-171">Later, I'll describe the parameters for **[EnableCors]** in more detail.</span></span>

<span data-ttu-id="c490c-172">Schließen Sie keinen Schrägstrich am Ende der *Ursprünge* URL.</span><span class="sxs-lookup"><span data-stu-id="c490c-172">Do not include a forward slash at the end of the *origins* URL.</span></span>

<span data-ttu-id="c490c-173">Bereitstellen Sie die aktualisierte WebService-Anwendung erneut.</span><span class="sxs-lookup"><span data-stu-id="c490c-173">Redeploy the updated WebService application.</span></span> <span data-ttu-id="c490c-174">Sie müssen nicht "Webclient" zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="c490c-174">You don't need to update WebClient.</span></span> <span data-ttu-id="c490c-175">Die AJAX-Anforderung von "Webclient" sollte jetzt erfolgreich sein.</span><span class="sxs-lookup"><span data-stu-id="c490c-175">Now the AJAX request from WebClient should succeed.</span></span> <span data-ttu-id="c490c-176">Die GET, PUT und POST-Methoden sind alle zulässig.</span><span class="sxs-lookup"><span data-stu-id="c490c-176">The GET, PUT, and POST methods are all allowed.</span></span>

![Browser mit erfolgreichen Test Webnachricht](enabling-cross-origin-requests-in-web-api/_static/image9.png)

## <a name="how-cors-works"></a><span data-ttu-id="c490c-178">Funktionsweise von CORS</span><span class="sxs-lookup"><span data-stu-id="c490c-178">How CORS Works</span></span>

<span data-ttu-id="c490c-179">In diesem Abschnitt wird beschrieben, was geschieht, in einer CORS-Anforderung auf der Ebene der HTTP-Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="c490c-179">This section describes what happens in a CORS request, at the level of the HTTP messages.</span></span> <span data-ttu-id="c490c-180">Es ist wichtig zum Verständnis der Funktionsweise von CORS, sodass Sie konfigurieren können die **[EnableCors]** ordnungsgemäß Attribut, und beheben, wenn etwas nicht wie erwartet funktioniert.</span><span class="sxs-lookup"><span data-stu-id="c490c-180">It's important to understand how CORS works, so that you can configure the **[EnableCors]** attribute correctly and troubleshoot if things don't work as you expect.</span></span>

<span data-ttu-id="c490c-181">CORS-Spezifikation führt mehrere neue HTTP-Header, die Cross-Origin-Anforderungen zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="c490c-181">The CORS specification introduces several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="c490c-182">Wenn ein Browser CORS unterstützt, wird diese Header automatisch für Cross-Origin-Anforderungen; Sie müssen gar nichts Besonderes im JavaScript-Code.</span><span class="sxs-lookup"><span data-stu-id="c490c-182">If a browser supports CORS, it sets these headers automatically for cross-origin requests; you don't need to do anything special in your JavaScript code.</span></span>

<span data-ttu-id="c490c-183">Hier ist ein Beispiel für eine cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c490c-183">Here is an example of a cross-origin request.</span></span> <span data-ttu-id="c490c-184">Der "Origin"-Header gibt die Domäne der Website, die die Anforderung stammt.</span><span class="sxs-lookup"><span data-stu-id="c490c-184">The "Origin" header gives the domain of the site that is making the request.</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample6.cmd?highlight=5)]

<span data-ttu-id="c490c-185">Wenn der Server die Anforderung zulässt, wird der Access-Control-Allow-Origin-Header.</span><span class="sxs-lookup"><span data-stu-id="c490c-185">If the server allows the request, it sets the Access-Control-Allow-Origin header.</span></span> <span data-ttu-id="c490c-186">Der Wert dieses Headers entspricht den Origin-Header oder ist Sie den Platzhalterwert "\*", Bedeutung, die einen beliebigen Ursprung zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="c490c-186">The value of this header either matches the Origin header, or is the wildcard value "\*", meaning that any origin is allowed.</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample7.cmd?highlight=5)]

<span data-ttu-id="c490c-187">Wenn es sich bei den Access-Control-Allow-Origin-Header in die Antwort nicht enthalten ist, schlägt die AJAX-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c490c-187">If the response does not include the Access-Control-Allow-Origin header, the AJAX request fails.</span></span> <span data-ttu-id="c490c-188">Der Browser lässt, die die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c490c-188">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="c490c-189">Selbst wenn der Server eine erfolgreiche Antwort zurückgibt, ist der Browser nicht die Antwort an die Clientanwendung zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="c490c-189">Even if the server returns a successful response, the browser does not make the response available to the client application.</span></span>

<span data-ttu-id="c490c-190">**Preflight-Anforderungen**</span><span class="sxs-lookup"><span data-stu-id="c490c-190">**Preflight Requests**</span></span>

<span data-ttu-id="c490c-191">Für einige CORS-Anforderungen sendet der Browser eine zusätzliche Anforderung, die eine "preflight-Anforderung", aufgerufen, bevor die tatsächliche Anforderung für die Ressource gesendet.</span><span class="sxs-lookup"><span data-stu-id="c490c-191">For some CORS requests, the browser sends an additional request, called a "preflight request", before it sends the actual request for the resource.</span></span>

<span data-ttu-id="c490c-192">Der Browser kann die preflight-Anforderung überspringen, wenn die folgenden Bedingungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="c490c-192">The browser can skip the preflight request if the following conditions are true:</span></span>

- <span data-ttu-id="c490c-193">Die Anforderungsmethode ist GET, HEAD oder POST, *und*</span><span class="sxs-lookup"><span data-stu-id="c490c-193">The request method is GET, HEAD, or POST, *and*</span></span>
- <span data-ttu-id="c490c-194">Die Anwendung ist nicht festgelegt Anforderungsheader als Accept, Accept-Language, Inhaltssprache, Content-Type oder letzten-Ereignis-ID, *und*</span><span class="sxs-lookup"><span data-stu-id="c490c-194">The application does not set any request headers other than Accept, Accept-Language, Content-Language, Content-Type, or Last-Event-ID, *and*</span></span>
- <span data-ttu-id="c490c-195">Der Content-Type-Header (falls festgelegt) ist eine der folgenden:</span><span class="sxs-lookup"><span data-stu-id="c490c-195">The Content-Type header (if set) is one of the following:</span></span>

    - <span data-ttu-id="c490c-196">application/x-www-form-urlencoded</span><span class="sxs-lookup"><span data-stu-id="c490c-196">application/x-www-form-urlencoded</span></span>
    - <span data-ttu-id="c490c-197">Multipart/Form-data</span><span class="sxs-lookup"><span data-stu-id="c490c-197">multipart/form-data</span></span>
    - <span data-ttu-id="c490c-198">Text/plain</span><span class="sxs-lookup"><span data-stu-id="c490c-198">text/plain</span></span>

<span data-ttu-id="c490c-199">Die Regel zu Anforderungsheader gilt für Header, die die Anwendung durch Aufrufen von festlegt **SetRequestHeader** auf die **XMLHttpRequest** Objekt.</span><span class="sxs-lookup"><span data-stu-id="c490c-199">The rule about request headers applies to headers that the application sets by calling **setRequestHeader** on the **XMLHttpRequest** object.</span></span> <span data-ttu-id="c490c-200">(Die CORS-Spezifikation als diese "Author-Anforderungsheader" bezeichnet). Die Regel gilt nicht für Header der *Browser* festlegen können, z. B. Benutzer-Agent, Host und Content-Length.</span><span class="sxs-lookup"><span data-stu-id="c490c-200">(The CORS specification calls these "author request headers".) The rule does not apply to headers the *browser* can set, such as User-Agent, Host, or Content-Length.</span></span>

<span data-ttu-id="c490c-201">Hier ist ein Beispiel für eine preflight-Anforderung:</span><span class="sxs-lookup"><span data-stu-id="c490c-201">Here is an example of a preflight request:</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample8.cmd?highlight=4-5)]

<span data-ttu-id="c490c-202">Die Pre-Flight-Anforderung verwendet die HTTP OPTIONS-Methode.</span><span class="sxs-lookup"><span data-stu-id="c490c-202">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="c490c-203">Es enthält zwei spezielle Header:</span><span class="sxs-lookup"><span data-stu-id="c490c-203">It includes two special headers:</span></span>

- <span data-ttu-id="c490c-204">Access-Control-Request-Method: Die HTTP-Methode, die für die tatsächliche Anforderung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c490c-204">Access-Control-Request-Method: The HTTP method that will be used for the actual request.</span></span>
- <span data-ttu-id="c490c-205">Access-Control-Request-Headers: Eine Liste der Anforderungsheader, die die *Anwendung* legen Sie für die tatsächliche Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c490c-205">Access-Control-Request-Headers: A list of request headers that the *application* set on the actual request.</span></span> <span data-ttu-id="c490c-206">(In diesem Fall ist dies nicht-Header, die der Browser legt diese fest enthalten.)</span><span class="sxs-lookup"><span data-stu-id="c490c-206">(Again, this does not include headers that the browser sets.)</span></span>

<span data-ttu-id="c490c-207">Hier ist eine Beispielantwort, vorausgesetzt, dass der Server die Anforderung zulässt:</span><span class="sxs-lookup"><span data-stu-id="c490c-207">Here is an example response, assuming that the server allows the request:</span></span>

[!code-console[Main](enabling-cross-origin-requests-in-web-api/samples/sample9.cmd?highlight=6-7)]

<span data-ttu-id="c490c-208">Die Antwort enthält eine Access-Control-Allow-Methods-Header, der die zulässigen Methoden aufgeführt, und optional eine Access-Control-Allow-Headers-Header, der die erlaubten Header aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="c490c-208">The response includes an Access-Control-Allow-Methods header that lists the allowed methods, and optionally an Access-Control-Allow-Headers header, which lists the allowed headers.</span></span> <span data-ttu-id="c490c-209">Wenn die preflight-Anforderung erfolgreich ist, sendet der Browser die tatsächliche Anforderung an, wie oben beschrieben.</span><span class="sxs-lookup"><span data-stu-id="c490c-209">If the preflight request succeeds, the browser sends the actual request, as described earlier.</span></span>

<span data-ttu-id="c490c-210">Tools, die häufig verwendet, um die Endpunkte mit der Preflight-OPTIONS-Anforderungen zu testen (z. B. [Fiddler](https://www.telerik.com/fiddler) und [Postman](https://www.getpostman.com/)) nicht die erforderlichen Optionen Header standardmäßig gesendet.</span><span class="sxs-lookup"><span data-stu-id="c490c-210">Tools commonly used to test endpoints with preflight OPTIONS requests (for example, [Fiddler](https://www.telerik.com/fiddler) and [Postman](https://www.getpostman.com/)) don't send the required OPTIONS headers by default.</span></span> <span data-ttu-id="c490c-211">Überprüfen Sie, ob die `Access-Control-Request-Method` und `Access-Control-Request-Headers` Header mit der Anforderung gesendet werden, und, die Optionen Header erreichen, die app mithilfe von IIS.</span><span class="sxs-lookup"><span data-stu-id="c490c-211">Confirm that the `Access-Control-Request-Method` and `Access-Control-Request-Headers` headers are sent with the request and that OPTIONS headers reach the app through IIS.</span></span>

<span data-ttu-id="c490c-212">Zum Konfigurieren von IIS für die Verwendung eine ASP.NET-App zu empfangen und Verarbeiten von OPTION-Anforderungen ermöglichen, fügen Sie die folgende Konfiguration auf die *"Web.config"* Datei die `<system.webServer><handlers>` Abschnitt:</span><span class="sxs-lookup"><span data-stu-id="c490c-212">To configure IIS to allow an ASP.NET app to receive and handle OPTION requests, add the following configuration to the app's *web.config* file in the `<system.webServer><handlers>` section:</span></span>

```xml
<system.webServer>
  <handlers>
    <remove name="ExtensionlessUrlHandler-Integrated-4.0" />
    <remove name="OPTIONSVerbHandler" />
    <add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="*" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
  </handlers>
</system.webServer>
```

<span data-ttu-id="c490c-213">Das Entfernen der `OPTIONSVerbHandler` verhindert, dass IIS die OPTIONS-Anforderungen verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="c490c-213">The removal of `OPTIONSVerbHandler` prevents IIS from handling OPTIONS requests.</span></span> <span data-ttu-id="c490c-214">Die Ersetzung des `ExtensionlessUrlHandler-Integrated-4.0` ermöglicht die OPTIONS-Anforderungen an die app zu erreichen, da die Registrierung der Standard-Modul nur GET, HEAD, POST und DEBUG-Anforderungen mit URLs ohne Erweiterung lässt.</span><span class="sxs-lookup"><span data-stu-id="c490c-214">The replacement of `ExtensionlessUrlHandler-Integrated-4.0` allows OPTIONS requests to reach the app because the default module registration only allows GET, HEAD, POST, and DEBUG requests with extensionless URLs.</span></span>

## <a name="scope-rules-for-enablecors"></a><span data-ttu-id="c490c-215">Bereichsregeln für [EnableCors]</span><span class="sxs-lookup"><span data-stu-id="c490c-215">Scope Rules for [EnableCors]</span></span>

<span data-ttu-id="c490c-216">Sie können CORS pro Aktion, pro Controller oder global für alle Web-API-Controller in Ihrer Anwendung ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="c490c-216">You can enable CORS per action, per controller, or globally for all Web API controllers in your application.</span></span>

<span data-ttu-id="c490c-217">**Pro Aktion**</span><span class="sxs-lookup"><span data-stu-id="c490c-217">**Per Action**</span></span>

<span data-ttu-id="c490c-218">Legen Sie zum Aktivieren von CORS für eine einzelne Aktion die **[EnableCors]** Attribut für die Aktionsmethode.</span><span class="sxs-lookup"><span data-stu-id="c490c-218">To enable CORS for a single action, set the **[EnableCors]** attribute on the action method.</span></span> <span data-ttu-id="c490c-219">Im folgenden Beispiel wird CORS für den `GetItem` nur Methode.</span><span class="sxs-lookup"><span data-stu-id="c490c-219">The following example enables CORS for the `GetItem` method only.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample10.cs)]

<span data-ttu-id="c490c-220">**Pro Controller**</span><span class="sxs-lookup"><span data-stu-id="c490c-220">**Per Controller**</span></span>

<span data-ttu-id="c490c-221">Setzen Sie **[EnableCors]** auf die Controllerklasse, gilt für alle Aktionen auf dem Controller.</span><span class="sxs-lookup"><span data-stu-id="c490c-221">If you set **[EnableCors]** on the controller class, it applies to all the actions on the controller.</span></span> <span data-ttu-id="c490c-222">Wenn Sie CORS für eine Aktion deaktivieren möchten, fügen die **[DisableCors]** -Attribut auf die Aktion.</span><span class="sxs-lookup"><span data-stu-id="c490c-222">To disable CORS for an action, add the **[DisableCors]** attribute to the action.</span></span> <span data-ttu-id="c490c-223">Im folgenden Beispiel wird CORS für jede Methode, mit Ausnahme von `PutItem`.</span><span class="sxs-lookup"><span data-stu-id="c490c-223">The following example enables CORS for every method except `PutItem`.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample11.cs)]

<span data-ttu-id="c490c-224">**Globally**</span><span class="sxs-lookup"><span data-stu-id="c490c-224">**Globally**</span></span>

<span data-ttu-id="c490c-225">Übergeben Sie zum Aktivieren von CORS für alle Web-API-Controller in Ihrer Anwendung eine **EnableCorsAttribute** -Instanz, auf die **EnableCors** Methode:</span><span class="sxs-lookup"><span data-stu-id="c490c-225">To enable CORS for all Web API controllers in your application, pass an **EnableCorsAttribute** instance to the **EnableCors** method:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample12.cs)]

<span data-ttu-id="c490c-226">Wenn Sie das Attribut auf mehr als einen Bereich festlegen, ist die Reihenfolge auf:</span><span class="sxs-lookup"><span data-stu-id="c490c-226">If you set the attribute at more than one scope, the order of precedence is:</span></span>

1. <span data-ttu-id="c490c-227">Aktion</span><span class="sxs-lookup"><span data-stu-id="c490c-227">Action</span></span>
2. <span data-ttu-id="c490c-228">Controller</span><span class="sxs-lookup"><span data-stu-id="c490c-228">Controller</span></span>
3. <span data-ttu-id="c490c-229">Global</span><span class="sxs-lookup"><span data-stu-id="c490c-229">Global</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="c490c-230">Legen Sie die zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="c490c-230">Set the allowed origins</span></span>

<span data-ttu-id="c490c-231">Die *Ursprünge* Parameter, der die **[EnableCors]** Attribut gibt an, welche Ursprünge zulässig sind, auf die Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="c490c-231">The *origins* parameter of the **[EnableCors]** attribute specifies which origins are allowed to access the resource.</span></span> <span data-ttu-id="c490c-232">Der Wert ist eine durch Trennzeichen getrennte Liste der zulässigen Ursprünge.</span><span class="sxs-lookup"><span data-stu-id="c490c-232">The value is a comma-separated list of the allowed origins.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample13.cs)]

<span data-ttu-id="c490c-233">Sie können auch den Platzhalterwert "\*" um Anforderungen von der alle Ursprünge zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="c490c-233">You can also use the wildcard value "\*" to allow requests from any origins.</span></span>

<span data-ttu-id="c490c-234">Wägen Sie sorgfältig, bevor Sie die Anforderungen von einem beliebigen Ursprung zulassen.</span><span class="sxs-lookup"><span data-stu-id="c490c-234">Consider carefully before allowing requests from any origin.</span></span> <span data-ttu-id="c490c-235">Es bedeutet, dass praktisch jeder Website AJAX-Aufrufe Ihrer Web-API durchführen kann.</span><span class="sxs-lookup"><span data-stu-id="c490c-235">It means that literally any website can make AJAX calls to your web API.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample14.cs)]

## <a name="set-the-allowed-http-methods"></a><span data-ttu-id="c490c-236">Legen Sie die zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="c490c-236">Set the allowed HTTP methods</span></span>

<span data-ttu-id="c490c-237">Die *Methoden* Parameter, der die **[EnableCors]** Attribut gibt an, welche HTTP-Methoden zulässig sind, auf die Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="c490c-237">The *methods* parameter of the **[EnableCors]** attribute specifies which HTTP methods are allowed to access the resource.</span></span> <span data-ttu-id="c490c-238">Um alle Methoden zu ermöglichen, verwenden Sie den Platzhalterwert "\*".</span><span class="sxs-lookup"><span data-stu-id="c490c-238">To allow all methods, use the wildcard value "\*".</span></span> <span data-ttu-id="c490c-239">Im folgende Beispiel kann nur Get- und POST-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="c490c-239">The following example allows only GET and POST requests.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample15.cs)]

## <a name="set-the-allowed-request-headers"></a><span data-ttu-id="c490c-240">Legt die zulässigen Anforderungsheader fest.</span><span class="sxs-lookup"><span data-stu-id="c490c-240">Set the allowed request headers</span></span>

<span data-ttu-id="c490c-241">Dieser Artikel beschreibt, wie zuvor eine preflight-Anforderung auf einen Access-Control-Request-Headers-Header, die HTTP-Header, die von der Anwendung festgelegte auflisten umfassen kann (die so genannte "author-Anforderungsheader").</span><span class="sxs-lookup"><span data-stu-id="c490c-241">This article described earlier how a preflight request might include an Access-Control-Request-Headers header, listing the HTTP headers set by the application (the so-called "author request headers").</span></span> <span data-ttu-id="c490c-242">Die *Header* Parameter, der die **[EnableCors]** Attribut gibt an, welche Autor Anforderungsheader zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="c490c-242">The *headers* parameter of the **[EnableCors]** attribute specifies which author request headers are allowed.</span></span> <span data-ttu-id="c490c-243">Um alle Header zuzulassen, legen *Header* auf "\*".</span><span class="sxs-lookup"><span data-stu-id="c490c-243">To allow any headers, set *headers* to "\*".</span></span> <span data-ttu-id="c490c-244">Legen Sie auf eine Whitelist bestimmte Header, *Header* auf eine durch Trennzeichen getrennte Liste der zulässigen Header:</span><span class="sxs-lookup"><span data-stu-id="c490c-244">To whitelist specific headers, set *headers* to a comma-separated list of the allowed headers:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample16.cs)]

<span data-ttu-id="c490c-245">Browser sind jedoch nicht vollständig konsistent wie sie Access-Control-Request-Headers festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c490c-245">However, browsers are not entirely consistent in how they set Access-Control-Request-Headers.</span></span> <span data-ttu-id="c490c-246">So enthält beispielsweise Chrome derzeit "Origin".</span><span class="sxs-lookup"><span data-stu-id="c490c-246">For example, Chrome currently includes "origin".</span></span> <span data-ttu-id="c490c-247">FireFox enthält keine Standardheader wie z. B. "Accept", auch wenn die Anwendung im Skript festlegt.</span><span class="sxs-lookup"><span data-stu-id="c490c-247">FireFox does not include standard headers such as "Accept", even when the application sets them in script.</span></span>

<span data-ttu-id="c490c-248">Setzen Sie *Header* auf irgendetwas außer "\*", aufzunehmen mindestens "accept", "Content-Type" und "Origin" sowie alle benutzerdefinierten Header, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="c490c-248">If you set *headers* to anything other than "\*", you should include at least "accept", "content-type", and "origin", plus any custom headers that you want to support.</span></span>

## <a name="set-the-allowed-response-headers"></a><span data-ttu-id="c490c-249">Legen Sie die zulässigen Antwortheader</span><span class="sxs-lookup"><span data-stu-id="c490c-249">Set the allowed response headers</span></span>

<span data-ttu-id="c490c-250">Standardmäßig ist der Browser nicht alle Antwortheader für die Anwendung verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="c490c-250">By default, the browser does not expose all of the response headers to the application.</span></span> <span data-ttu-id="c490c-251">Die Antwortheader, die standardmäßig verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="c490c-251">The response headers that are available by default are:</span></span>

- <span data-ttu-id="c490c-252">Cache-Control</span><span class="sxs-lookup"><span data-stu-id="c490c-252">Cache-Control</span></span>
- <span data-ttu-id="c490c-253">Content-Language</span><span class="sxs-lookup"><span data-stu-id="c490c-253">Content-Language</span></span>
- <span data-ttu-id="c490c-254">Content-Type</span><span class="sxs-lookup"><span data-stu-id="c490c-254">Content-Type</span></span>
- <span data-ttu-id="c490c-255">Läuft ab</span><span class="sxs-lookup"><span data-stu-id="c490c-255">Expires</span></span>
- <span data-ttu-id="c490c-256">Zuletzt geändert</span><span class="sxs-lookup"><span data-stu-id="c490c-256">Last-Modified</span></span>
- <span data-ttu-id="c490c-257">Pragma</span><span class="sxs-lookup"><span data-stu-id="c490c-257">Pragma</span></span>

<span data-ttu-id="c490c-258">Die CORS-Spezifikation ruft diese [Header für die einfache Antwort](https://dvcs.w3.org/hg/cors/raw-file/tip/Overview.html#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="c490c-258">The CORS spec calls these [simple response headers](https://dvcs.w3.org/hg/cors/raw-file/tip/Overview.html#simple-response-header).</span></span> <span data-ttu-id="c490c-259">Um weitere Header für die Anwendung verfügbar machen, legen die *ExposedHeaders* Parameter **[EnableCors]**.</span><span class="sxs-lookup"><span data-stu-id="c490c-259">To make other headers available to the application, set the *exposedHeaders* parameter of **[EnableCors]**.</span></span>

<span data-ttu-id="c490c-260">Im folgenden Beispiel ist der Controller die `Get` Methode legt einen benutzerdefinierten Header mit dem Namen "X-Custom-Header" fest.</span><span class="sxs-lookup"><span data-stu-id="c490c-260">In the following example, the controller's `Get` method sets a custom header named ‘X-Custom-Header'.</span></span> <span data-ttu-id="c490c-261">Standardmäßig wird der Browser diesen Header in einer cors-Anforderung nicht verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="c490c-261">By default, the browser will not expose this header in a cross-origin request.</span></span> <span data-ttu-id="c490c-262">Um den Header verfügbar zu machen, schließen Sie "X-Custom-Header" in *ExposedHeaders*.</span><span class="sxs-lookup"><span data-stu-id="c490c-262">To make the header available, include ‘X-Custom-Header' in *exposedHeaders*.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample17.cs)]

## <a name="pass-credentials-in-cross-origin-requests"></a><span data-ttu-id="c490c-263">Übergeben von Anmeldeinformationen in Cross-Origin-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="c490c-263">Pass credentials in cross-origin requests</span></span>

<span data-ttu-id="c490c-264">Anmeldeinformationen erfordern besondere Behandlung in eine CORS-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c490c-264">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="c490c-265">Standardmäßig sendet der Browser keine Anmeldeinformationen mit einer cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c490c-265">By default, the browser does not send any credentials with a cross-origin request.</span></span> <span data-ttu-id="c490c-266">Anmeldeinformationen enthalten, Cookies sowie HTTP-Authentifizierungsschemas.</span><span class="sxs-lookup"><span data-stu-id="c490c-266">Credentials include cookies as well as HTTP authentication schemes.</span></span> <span data-ttu-id="c490c-267">Um die Anmeldeinformationen mit einer cors-Anforderung zu senden, muss der Client festgelegt **XMLHttpRequest.withCredentials** auf "true".</span><span class="sxs-lookup"><span data-stu-id="c490c-267">To send credentials with a cross-origin request, the client must set **XMLHttpRequest.withCredentials** to true.</span></span>

<span data-ttu-id="c490c-268">Mithilfe von **XMLHttpRequest** direkt:</span><span class="sxs-lookup"><span data-stu-id="c490c-268">Using **XMLHttpRequest** directly:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample18.cs)]

<span data-ttu-id="c490c-269">In jQuery:</span><span class="sxs-lookup"><span data-stu-id="c490c-269">In jQuery:</span></span>

[!code-javascript[Main](enabling-cross-origin-requests-in-web-api/samples/sample19.js)]

<span data-ttu-id="c490c-270">Darüber hinaus muss der Server die Anmeldeinformationen zulassen.</span><span class="sxs-lookup"><span data-stu-id="c490c-270">In addition, the server must allow the credentials.</span></span> <span data-ttu-id="c490c-271">Um ursprungsübergreifende Anmeldeinformationen in Web-API zu ermöglichen, legen die **"supportscredentials"** Eigenschaft auf "true", auf die **[EnableCors]** Attribut:</span><span class="sxs-lookup"><span data-stu-id="c490c-271">To allow cross-origin credentials in Web API, set the **SupportsCredentials** property to true on the **[EnableCors]** attribute:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample20.cs)]

<span data-ttu-id="c490c-272">Wenn diese Eigenschaft auf "true" festgelegt ist, wird die HTTP-Antwort einen Access-Control-Allow-Credentials-Header einfügen.</span><span class="sxs-lookup"><span data-stu-id="c490c-272">If this property is true, the HTTP response will include an Access-Control-Allow-Credentials header.</span></span> <span data-ttu-id="c490c-273">Dieser Header informiert den Browser, dass der Server die Anmeldeinformationen für eine Anforderung zwischen verschiedenen Ursprüngen zulässt.</span><span class="sxs-lookup"><span data-stu-id="c490c-273">This header tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="c490c-274">Wenn der Browser sendet die Anmeldeinformationen, aber die Antwort enthält keines gültigen Access-Control-Allow-Credentials-Headers, der Browser wird die Antwort an die Anwendung nicht verfügbar, und die AJAX-Anforderung ein Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="c490c-274">If the browser sends credentials, but the response does not include a valid Access-Control-Allow-Credentials header, the browser will not expose the response to the application, and the AJAX request fails.</span></span>

<span data-ttu-id="c490c-275">Achten Sie darauf, dass Sie zur Einstellung **"supportscredentials"** auf "true", da es bedeutet, dass eine Website in einer anderen Domäne kann Ihrer Web-API im Auftrag des Benutzers, eines angemeldeten Benutzers Anmeldeinformationen senden, ohne dass der Benutzer an, dass Sie sich,.</span><span class="sxs-lookup"><span data-stu-id="c490c-275">Be careful about setting **SupportsCredentials** to true, because it means a website at another domain can send a logged-in user's credentials to your Web API on the user's behalf, without the user being aware.</span></span> <span data-ttu-id="c490c-276">Die CORS-Spezifikation gibt auch an dieser Einstellung *Ursprünge* zu &quot; \* &quot; ist ungültig Wenn **"supportscredentials"** ist "true".</span><span class="sxs-lookup"><span data-stu-id="c490c-276">The CORS spec also states that setting *origins* to &quot;\*&quot; is invalid if **SupportsCredentials** is true.</span></span>

## <a name="custom-cors-policy-providers"></a><span data-ttu-id="c490c-277">Benutzerdefinierte Anbieter von CORS-Richtlinie</span><span class="sxs-lookup"><span data-stu-id="c490c-277">Custom CORS policy providers</span></span>

<span data-ttu-id="c490c-278">Die **[EnableCors]** Attribut implementiert die **ICorsPolicyProvider** Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="c490c-278">The **[EnableCors]** attribute implements the **ICorsPolicyProvider** interface.</span></span> <span data-ttu-id="c490c-279">Sie können Ihre eigene Implementierung bereitstellen, indem Sie eine abgeleitete Klasse erstellen **Attribut** und implementiert **ICorsProlicyProvider**.</span><span class="sxs-lookup"><span data-stu-id="c490c-279">You can provide your own implementation by creating a class that derives from **Attribute** and implements **ICorsProlicyProvider**.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample21.cs)]

<span data-ttu-id="c490c-280">Nachdem Sie das Attribut anwenden können, beliebiger Stelle, platzieren Sie würde **[EnableCors]**.</span><span class="sxs-lookup"><span data-stu-id="c490c-280">Now you can apply the attribute any place that you would put **[EnableCors]**.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample22.cs)]

<span data-ttu-id="c490c-281">Beispielsweise kann ein benutzerdefiniertes CORS-Richtlinienanbieter die Einstellungen aus einer Konfigurationsdatei lesen.</span><span class="sxs-lookup"><span data-stu-id="c490c-281">For example, a custom CORS policy provider could read the settings from a configuration file.</span></span>

<span data-ttu-id="c490c-282">Als Alternative zur Verwendung von Attributen, können Sie registrieren einen **ICorsPolicyProviderFactory** Objekt, das erstellt **ICorsPolicyProvider** Objekte.</span><span class="sxs-lookup"><span data-stu-id="c490c-282">As an alternative to using attributes, you can register an **ICorsPolicyProviderFactory** object that creates **ICorsPolicyProvider** objects.</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample23.cs)]

<span data-ttu-id="c490c-283">Festlegen der **ICorsPolicyProviderFactory**, rufen Sie die **SetCorsPolicyProviderFactory** Erweiterungsmethode beim Start wie folgt:</span><span class="sxs-lookup"><span data-stu-id="c490c-283">To set the **ICorsPolicyProviderFactory**, call the **SetCorsPolicyProviderFactory** extension method at startup, as follows:</span></span>

[!code-csharp[Main](enabling-cross-origin-requests-in-web-api/samples/sample24.cs)]

## <a name="browser-support"></a><span data-ttu-id="c490c-284">Browserunterstützung</span><span class="sxs-lookup"><span data-stu-id="c490c-284">Browser support</span></span>

<span data-ttu-id="c490c-285">Die Web-API-CORS-Paket ist eine serverseitige Technologie.</span><span class="sxs-lookup"><span data-stu-id="c490c-285">The Web API CORS package is a server-side technology.</span></span> <span data-ttu-id="c490c-286">Außerdem muss der Browser des Benutzers CORS-Unterstützung.</span><span class="sxs-lookup"><span data-stu-id="c490c-286">The user's browser also needs to support CORS.</span></span> <span data-ttu-id="c490c-287">Die aktuellen Versionen von allen wichtigen Browsern zum Glück enthalten [Unterstützung für CORS](http://caniuse.com/cors).</span><span class="sxs-lookup"><span data-stu-id="c490c-287">Fortunately, the current versions of all major browsers include [support for CORS](http://caniuse.com/cors).</span></span>
