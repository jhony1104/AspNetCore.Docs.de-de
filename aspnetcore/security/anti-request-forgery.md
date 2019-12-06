---
title: Verhindern von Angriffen für Website übergreifende Anforderungs Fälschung (XSRF/CSRF) in ASP.net Core
author: steve-smith
description: Erfahren Sie, wie Sie Angriffe auf Web-Apps verhindern, bei denen eine böswillige Website die Interaktion zwischen einem Client Browser und der APP beeinflussen kann.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: security/anti-request-forgery
ms.openlocfilehash: 54e153af55f28d9a89bbf16bce1c17f876567b59
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880808"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="4973d-103">Verhindern von Angriffen für Website übergreifende Anforderungs Fälschung (XSRF/CSRF) in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="4973d-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="4973d-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), von " [fyaz Hasan](https://twitter.com/FiyazBinHasan)" und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4973d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4973d-105">Website übergreifende Anforderungs Fälschung (auch als XSRF oder CSRF bezeichnet) ist ein Angriff auf im Web gehostete Apps, bei dem eine böswillige Web-App die Interaktion zwischen einem Client Browser und einer Web-App beeinflussen kann, die diesem Browser vertraut.</span><span class="sxs-lookup"><span data-stu-id="4973d-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="4973d-106">Diese Angriffe sind möglich, da Webbrowser einige Arten von Authentifizierungs Token automatisch mit jeder Anforderung an eine Website senden.</span><span class="sxs-lookup"><span data-stu-id="4973d-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="4973d-107">Diese Art von Exploit wird auch als *One-Click-Angriff* oder *Sitzungsart* bezeichnet, da der Angriff die zuvor authentifizierte Sitzung des Benutzers nutzt.</span><span class="sxs-lookup"><span data-stu-id="4973d-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="4973d-108">Ein Beispiel für einen CSRF-Angriff:</span><span class="sxs-lookup"><span data-stu-id="4973d-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="4973d-109">Ein Benutzer meldet sich mit der Formular Authentifizierung bei `www.good-banking-site.com` an.</span><span class="sxs-lookup"><span data-stu-id="4973d-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="4973d-110">Der Server authentifiziert den Benutzer und gibt eine Antwort aus, die ein Authentifizierungs Cookie enthält.</span><span class="sxs-lookup"><span data-stu-id="4973d-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="4973d-111">Die Site ist anfällig für Angriffe, da Sie jede empfangene Anforderung mit einem gültigen Authentifizierungs Cookie vertraut.</span><span class="sxs-lookup"><span data-stu-id="4973d-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="4973d-112">Der Benutzer besucht eine böswillige Website, `www.bad-crook-site.com`.</span><span class="sxs-lookup"><span data-stu-id="4973d-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="4973d-113">Die schädliche Website, `www.bad-crook-site.com`, enthält ein HTML-Formular, das dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="4973d-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="4973d-114">Beachten Sie, dass das Formular `action` an die anfällige Site und nicht an die böswillige Site sendet.</span><span class="sxs-lookup"><span data-stu-id="4973d-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="4973d-115">Dies ist der "Cross-Site"-Teil von CSRF.</span><span class="sxs-lookup"><span data-stu-id="4973d-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="4973d-116">Der Benutzer wählt die Schaltfläche Senden aus.</span><span class="sxs-lookup"><span data-stu-id="4973d-116">The user selects the submit button.</span></span> <span data-ttu-id="4973d-117">Der Browser stellt die Anforderung her und schließt automatisch das Authentifizierungs Cookie für die angeforderte Domäne ein, `www.good-banking-site.com`.</span><span class="sxs-lookup"><span data-stu-id="4973d-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="4973d-118">Die Anforderung wird auf dem `www.good-banking-site.com` Server mit dem Authentifizierungs Kontext des Benutzers ausgeführt und kann alle Aktionen ausführen, die ein authentifizierter Benutzer ausführen darf.</span><span class="sxs-lookup"><span data-stu-id="4973d-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="4973d-119">Zusätzlich zu dem Szenario, in dem der Benutzer die Schaltfläche zum Senden des Formulars auswählt, könnte die böswillige Site folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="4973d-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="4973d-120">Führen Sie ein Skript aus, das das Formular automatisch sendet.</span><span class="sxs-lookup"><span data-stu-id="4973d-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="4973d-121">Senden Sie die Formular Übermittlung als AJAX-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4973d-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="4973d-122">Blenden Sie das Formular mit CSS aus.</span><span class="sxs-lookup"><span data-stu-id="4973d-122">Hide the form using CSS.</span></span>

<span data-ttu-id="4973d-123">Für diese alternativen Szenarien sind keine Aktionen oder Eingaben vom Benutzer erforderlich, außer wenn Sie zunächst die böswillige Website besuchen.</span><span class="sxs-lookup"><span data-stu-id="4973d-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="4973d-124">Durch die Verwendung von HTTPS wird kein CSRF-Angriff verhindert.</span><span class="sxs-lookup"><span data-stu-id="4973d-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="4973d-125">Die böswillige Site kann eine `https://www.good-banking-site.com/` Anforderung genauso einfach senden, wie Sie eine unsichere Anforderung senden kann.</span><span class="sxs-lookup"><span data-stu-id="4973d-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="4973d-126">Einige Angriffe zielen auf Endpunkte ab, die auf Get-Anforderungen reagieren. in diesem Fall kann ein imagetag verwendet werden, um die Aktion auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4973d-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="4973d-127">Diese Art von Angriff erfolgt häufig auf Forums Websites, die Bilder zulassen, aber JavaScript blockieren.</span><span class="sxs-lookup"><span data-stu-id="4973d-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="4973d-128">Apps, die den Status von Get-Anforderungen ändern, bei denen Variablen oder Ressourcen geändert werden, sind anfällig für böswillige Angriffe.</span><span class="sxs-lookup"><span data-stu-id="4973d-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="4973d-129">**Get-Anforderungen, die den Zustand ändern, sind unsicher. Eine bewährte Vorgehensweise besteht darin, den Status einer GET-Anforderung nie zu ändern.**</span><span class="sxs-lookup"><span data-stu-id="4973d-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="4973d-130">Csrf-Angriffe sind für Web-Apps möglich, die Cookies für die Authentifizierung verwenden:</span><span class="sxs-lookup"><span data-stu-id="4973d-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="4973d-131">Browser speichern Cookies, die von einer Web-App ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="4973d-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="4973d-132">Gespeicherte Cookies enthalten Sitzungs Cookies für authentifizierte Benutzer.</span><span class="sxs-lookup"><span data-stu-id="4973d-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="4973d-133">Browser senden alle Cookies, die einer Domäne zugeordnet sind, an die Web-App jede Anforderung, unabhängig davon, wie die App-Anforderung im Browser generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="4973d-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="4973d-134">Allerdings sind CSRF-Angriffe nicht auf das Ausnutzen von Cookies beschränkt.</span><span class="sxs-lookup"><span data-stu-id="4973d-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="4973d-135">Beispielsweise sind die Standard-und Digestauthentifizierung auch anfällig.</span><span class="sxs-lookup"><span data-stu-id="4973d-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="4973d-136">Nachdem sich ein Benutzer mit der Standard-oder Digestauthentifizierung angemeldet hat, sendet der Browser die Anmelde Informationen automatisch, bis die Sitzung beendet&dagger;.</span><span class="sxs-lookup"><span data-stu-id="4973d-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="4973d-137">&dagger;in diesem Kontext bezieht sich *Sitzung* auf die Client seitige Sitzung, in der der Benutzer authentifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="4973d-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="4973d-138">Sie steht nicht im Zusammenhang mit serverseitigen Sitzungen oder [ASP.net Core Sitzungs Middleware](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="4973d-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="4973d-139">Benutzer können Schutz vor CSRF-Sicherheitsrisiken durch Vorkehrungen treffen:</span><span class="sxs-lookup"><span data-stu-id="4973d-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="4973d-140">Melden Sie sich von Web-Apps ab, wenn Sie diese verwenden.</span><span class="sxs-lookup"><span data-stu-id="4973d-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="4973d-141">Löschen Sie Browser Cookies regelmäßig.</span><span class="sxs-lookup"><span data-stu-id="4973d-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="4973d-142">Csrf-Sicherheitsrisiken sind jedoch im Grunde ein Problem mit der Web-App, nicht mit dem Endbenutzer.</span><span class="sxs-lookup"><span data-stu-id="4973d-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="4973d-143">Grundlagen der Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="4973d-143">Authentication fundamentals</span></span>

<span data-ttu-id="4973d-144">Die cookiebasierte Authentifizierung ist eine beliebte Form der Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="4973d-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="4973d-145">Tokenbasierte Authentifizierungssysteme werden immer beliebter, insbesondere für Single-Page-Anwendungen (Spas).</span><span class="sxs-lookup"><span data-stu-id="4973d-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="4973d-146">Cookiebasierte Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="4973d-146">Cookie-based authentication</span></span>

<span data-ttu-id="4973d-147">Wenn ein Benutzer sich mit seinem Benutzernamen und Kennwort authentifiziert, wird ein Token ausgestellt, das ein Authentifizierungs Ticket enthält, das für die Authentifizierung und Autorisierung verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="4973d-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="4973d-148">Das Token wird als Cookie gespeichert, das jede vom Client vornimmt Anforderung begleitet.</span><span class="sxs-lookup"><span data-stu-id="4973d-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="4973d-149">Das Erstellen und Validieren dieses Cookies erfolgt durch die cookumsauthentifizierungs-Middleware.</span><span class="sxs-lookup"><span data-stu-id="4973d-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="4973d-150">Die [Middleware](xref:fundamentals/middleware/index) serialisiert einen Benutzer Prinzipal in ein verschlüsseltes Cookie.</span><span class="sxs-lookup"><span data-stu-id="4973d-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="4973d-151">Bei nachfolgenden Anforderungen überprüft die Middleware das Cookie, erstellt den Prinzipal neu und weist den Prinzipal der [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) -Eigenschaft von [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)zu.</span><span class="sxs-lookup"><span data-stu-id="4973d-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="4973d-152">Tokenbasierte Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="4973d-152">Token-based authentication</span></span>

<span data-ttu-id="4973d-153">Wenn ein Benutzer authentifiziert wird, wird ein Token ausgestellt (kein antifälschungstoken).</span><span class="sxs-lookup"><span data-stu-id="4973d-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="4973d-154">Das Token enthält Benutzerinformationen in Form von [Ansprüchen](/dotnet/framework/security/claims-based-identity-model) oder ein Verweis Token, das die APP auf den Benutzer Zustand verweist, der in der APP verwaltet wird.</span><span class="sxs-lookup"><span data-stu-id="4973d-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="4973d-155">Wenn ein Benutzer versucht, auf eine Ressource zuzugreifen, die eine Authentifizierung erfordert, wird das Token mit einem zusätzlichen Autorisierungs Header als bearertoken an die APP gesendet.</span><span class="sxs-lookup"><span data-stu-id="4973d-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="4973d-156">Dadurch wird die APP zustandslos.</span><span class="sxs-lookup"><span data-stu-id="4973d-156">This makes the app stateless.</span></span> <span data-ttu-id="4973d-157">In jeder nachfolgenden Anforderung wird das Token in der Anforderung für die serverseitige Validierung übermittelt.</span><span class="sxs-lookup"><span data-stu-id="4973d-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="4973d-158">Dieses Token ist nicht *verschlüsselt*. Sie ist *codiert*.</span><span class="sxs-lookup"><span data-stu-id="4973d-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="4973d-159">Auf dem Server wird das Token decodiert, um auf seine Informationen zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="4973d-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="4973d-160">Um das Token für nachfolgende Anforderungen zu senden, speichern Sie das Token im lokalen Speicher des Browsers.</span><span class="sxs-lookup"><span data-stu-id="4973d-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="4973d-161">Machen Sie sich keine Gedanken über das Sicherheitsrisiko von CSRF, wenn das Token im lokalen Speicher des Browsers gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="4973d-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="4973d-162">Csrf ist ein Problem, wenn das Token in einem Cookie gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="4973d-162">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="4973d-163">Weitere Informationen finden Sie im GitHub Issue [Spa-Codebeispiel zum Hinzufügen von zwei Cookies](https://github.com/aspnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="4973d-163">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/aspnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="4973d-164">Mehrere apps, die in einer Domäne gehostet werden</span><span class="sxs-lookup"><span data-stu-id="4973d-164">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="4973d-165">Freigegebene Hostingumgebungen sind anfällig für Session Hijacking, Login CSRF und andere Angriffe.</span><span class="sxs-lookup"><span data-stu-id="4973d-165">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="4973d-166">Obwohl es sich bei `example1.contoso.net` und `example2.contoso.net` um unterschiedliche Hosts handelt, gibt es eine implizite Vertrauensstellung zwischen den Hosts in der `*.contoso.net` Domäne.</span><span class="sxs-lookup"><span data-stu-id="4973d-166">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="4973d-167">Mit dieser impliziten Vertrauensstellung können sich potenziell nicht vertrauenswürdige Hosts auf die Cookies der anderen Anwendungen auswirken (die gleichen Ursprungs Richtlinien, die AJAX-Anforderungen Regeln, werden nicht unbedingt auf http-Cookies angewendet).</span><span class="sxs-lookup"><span data-stu-id="4973d-167">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="4973d-168">Angriffe, die vertrauenswürdige Cookies zwischen apps ausnutzen, die in derselben Domäne gehostet werden, können durch das Freigeben von Domänen verhindert werden.</span><span class="sxs-lookup"><span data-stu-id="4973d-168">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="4973d-169">Wenn jede app in ihrer eigenen Domäne gehostet wird, ist keine implizite Cookie-Vertrauensstellung zum ausnutzen vorhanden.</span><span class="sxs-lookup"><span data-stu-id="4973d-169">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="4973d-170">Konfiguration der ASP.net Core-Antifälschung</span><span class="sxs-lookup"><span data-stu-id="4973d-170">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="4973d-171">ASP.net Core implementiert Antifälschung mithilfe [ASP.net Core Datenschutzes](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="4973d-171">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="4973d-172">Der Stapel für den Datenschutz muss so konfiguriert sein, dass er in einer Serverfarm funktioniert.</span><span class="sxs-lookup"><span data-stu-id="4973d-172">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="4973d-173">Weitere Informationen finden Sie unter [Konfigurieren des Schutzes von Daten](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="4973d-173">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4973d-174">Die Middleware für die Antifälschung wird dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) hinzugefügt, wenn eine der folgenden APIs in `Startup.ConfigureServices`aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="4973d-174">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4973d-175">Die antifälschungsmiddleware wird dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> in aufgerufen wird `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="4973d-175">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="4973d-176">In ASP.net Core 2,0 oder höher fügt [formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper) antifälschungstoken in HTML-Formularelemente ein.</span><span class="sxs-lookup"><span data-stu-id="4973d-176">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="4973d-177">Das folgende Markup in einer Razor-Datei generiert automatisch antifälschungstokentoken:</span><span class="sxs-lookup"><span data-stu-id="4973d-177">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="4973d-178">Ebenso generiert [ihtmlhelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) standardmäßig antifälschungstokentoken, wenn die-Methode des Formulars nicht erhalten wird.</span><span class="sxs-lookup"><span data-stu-id="4973d-178">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="4973d-179">Die automatische Generierung von antifälschungstoken für HTML-Formularelemente erfolgt, wenn das `<form>`-Tag das `method="post"`-Attribut enthält und eine der folgenden Optionen zutrifft:</span><span class="sxs-lookup"><span data-stu-id="4973d-179">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="4973d-180">Das Aktions Attribut ist leer (`action=""`).</span><span class="sxs-lookup"><span data-stu-id="4973d-180">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="4973d-181">Das action-Attribut wird nicht bereitgestellt (`<form method="post">`).</span><span class="sxs-lookup"><span data-stu-id="4973d-181">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="4973d-182">Die automatische Generierung von antifälschungstoken für HTML-Formularelemente kann deaktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="4973d-182">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="4973d-183">Deaktivieren Sie antifälschungstoken explizit mit dem `asp-antiforgery`-Attribut:</span><span class="sxs-lookup"><span data-stu-id="4973d-183">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="4973d-184">Das Formular Element wird von taghilfsprogrammen mithilfe des taghilfsprogramms [! Opt-out-Symbols](xref:mvc/views/tag-helpers/intro#opt-out)deaktiviert:</span><span class="sxs-lookup"><span data-stu-id="4973d-184">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="4973d-185">Entfernen Sie die `FormTagHelper` aus der Ansicht.</span><span class="sxs-lookup"><span data-stu-id="4973d-185">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="4973d-186">Die `FormTagHelper` kann aus einer Ansicht entfernt werden, indem der Razor-Ansicht die folgende-Direktive hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="4973d-186">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="4973d-187">[Razor Pages](xref:razor-pages/index) werden automatisch vor XSRF/CSRF geschützt.</span><span class="sxs-lookup"><span data-stu-id="4973d-187">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="4973d-188">Weitere Informationen finden Sie unter [XSRF/CSRF und Razor Pages](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="4973d-188">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="4973d-189">Der häufigste Ansatz für die Verteidigung gegen CSRF-Angriffe ist die Verwendung des *Synchronisierungs Token-Musters* (STP).</span><span class="sxs-lookup"><span data-stu-id="4973d-189">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="4973d-190">STP wird verwendet, wenn der Benutzer eine Seite mit Formulardaten anfordert:</span><span class="sxs-lookup"><span data-stu-id="4973d-190">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="4973d-191">Der Server sendet ein Token, das der Identität des aktuellen Benutzers zugeordnet ist, an den Client.</span><span class="sxs-lookup"><span data-stu-id="4973d-191">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="4973d-192">Der Client sendet das Token zur Überprüfung an den Server zurück.</span><span class="sxs-lookup"><span data-stu-id="4973d-192">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="4973d-193">Wenn der Server ein Token empfängt, das nicht mit der Identität des authentifizierten Benutzers identisch ist, wird die Anforderung abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="4973d-193">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="4973d-194">Das Token ist eindeutig und unvorhersehbar.</span><span class="sxs-lookup"><span data-stu-id="4973d-194">The token is unique and unpredictable.</span></span> <span data-ttu-id="4973d-195">Das Token kann auch verwendet werden, um eine ordnungsgemäße Sequenzierung einer Reihe von Anforderungen sicherzustellen (z. b. zur Sicherstellung der Anforderungs Sequenz von: Seite 1 &ndash; Seite 2 &ndash; Seite 3).</span><span class="sxs-lookup"><span data-stu-id="4973d-195">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 &ndash; page 2 &ndash; page 3).</span></span> <span data-ttu-id="4973d-196">Alle Formulare in ASP.net Core MVC-und Razor Pages Vorlagen generieren antifälschungstokentoken.</span><span class="sxs-lookup"><span data-stu-id="4973d-196">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="4973d-197">Das folgende Paar von Sicht Beispielen generiert antifälschungstokentoken:</span><span class="sxs-lookup"><span data-stu-id="4973d-197">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="4973d-198">Fügen Sie einem `<form>`-Element explizit ein antifälschungstoken hinzu, ohne taghilfsprogramme mit dem HTML-Hilfsobjekt [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span><span class="sxs-lookup"><span data-stu-id="4973d-198">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="4973d-199">In jedem der vorangegangenen Fälle fügt ASP.net Core ein ausgeblendetes Formularfeld ähnlich dem folgenden hinzu:</span><span class="sxs-lookup"><span data-stu-id="4973d-199">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="4973d-200">ASP.net Core enthält drei [Filter](xref:mvc/controllers/filters) zum Arbeiten mit antifälschungstoken:</span><span class="sxs-lookup"><span data-stu-id="4973d-200">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="4973d-201">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="4973d-201">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="4973d-202">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="4973d-202">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="4973d-203">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="4973d-203">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="4973d-204">Antifälschungsoptionen</span><span class="sxs-lookup"><span data-stu-id="4973d-204">Antiforgery options</span></span>

<span data-ttu-id="4973d-205">Optionen für die [Antifälschung](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`anpassen:</span><span class="sxs-lookup"><span data-stu-id="4973d-205">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

<span data-ttu-id="4973d-206">&dagger;die `Cookie` Eigenschaften der Antifälschung mithilfe der Eigenschaften der [cookiebuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) -Klasse festzulegen.</span><span class="sxs-lookup"><span data-stu-id="4973d-206">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="4973d-207">-Option</span><span class="sxs-lookup"><span data-stu-id="4973d-207">Option</span></span> | <span data-ttu-id="4973d-208">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="4973d-208">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="4973d-209">Cookie</span><span class="sxs-lookup"><span data-stu-id="4973d-209">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="4973d-210">Legt die Einstellungen fest, die zum Erstellen der antifälschungscookies verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4973d-210">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="4973d-211">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="4973d-211">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="4973d-212">Der Name des ausgeblendeten Formular Felds, das vom antifälschungs System zum Rendering von antifälschungstoken in Sichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4973d-212">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="4973d-213">HeaderName</span><span class="sxs-lookup"><span data-stu-id="4973d-213">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="4973d-214">Der Name des Headers, der vom antifälschungs System verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4973d-214">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="4973d-215">Wenn `null`, berücksichtigt das System nur Formulardaten.</span><span class="sxs-lookup"><span data-stu-id="4973d-215">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="4973d-216">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="4973d-216">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="4973d-217">Gibt an, ob die Generierung des `X-Frame-Options`-Headers unterdrückt werden soll.</span><span class="sxs-lookup"><span data-stu-id="4973d-217">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="4973d-218">Standardmäßig wird der-Header mit dem Wert "sameorigin" generiert.</span><span class="sxs-lookup"><span data-stu-id="4973d-218">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="4973d-219">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4973d-219">Defaults to `false`.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| <span data-ttu-id="4973d-220">-Option</span><span class="sxs-lookup"><span data-stu-id="4973d-220">Option</span></span> | <span data-ttu-id="4973d-221">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="4973d-221">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="4973d-222">Cookie</span><span class="sxs-lookup"><span data-stu-id="4973d-222">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="4973d-223">Legt die Einstellungen fest, die zum Erstellen der antifälschungscookies verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4973d-223">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="4973d-224">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="4973d-224">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | <span data-ttu-id="4973d-225">Die Domäne des Cookies.</span><span class="sxs-lookup"><span data-stu-id="4973d-225">The domain of the cookie.</span></span> <span data-ttu-id="4973d-226">Wird standardmäßig auf `null` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4973d-226">Defaults to `null`.</span></span> <span data-ttu-id="4973d-227">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="4973d-227">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="4973d-228">Die empfohlene Alternative ist "Cookie. Domain".</span><span class="sxs-lookup"><span data-stu-id="4973d-228">The recommended alternative is Cookie.Domain.</span></span> |
| [<span data-ttu-id="4973d-229">CookieName</span><span class="sxs-lookup"><span data-stu-id="4973d-229">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | <span data-ttu-id="4973d-230">Der Name des Cookies.</span><span class="sxs-lookup"><span data-stu-id="4973d-230">The name of the cookie.</span></span> <span data-ttu-id="4973d-231">Wenn nicht festgelegt, generiert das System einen eindeutigen Namen, beginnend mit dem [defaultcookieprefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". Aspnetcore. Antifälschung. ").</span><span class="sxs-lookup"><span data-stu-id="4973d-231">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="4973d-232">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="4973d-232">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="4973d-233">Die empfohlene Alternative ist Cookie.Name.</span><span class="sxs-lookup"><span data-stu-id="4973d-233">The recommended alternative is Cookie.Name.</span></span> |
| [<span data-ttu-id="4973d-234">CookiePath</span><span class="sxs-lookup"><span data-stu-id="4973d-234">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | <span data-ttu-id="4973d-235">Der Pfad, der für das Cookie festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="4973d-235">The path set on the cookie.</span></span> <span data-ttu-id="4973d-236">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="4973d-236">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="4973d-237">Die empfohlene Alternative ist Cookie. Path.</span><span class="sxs-lookup"><span data-stu-id="4973d-237">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="4973d-238">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="4973d-238">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="4973d-239">Der Name des ausgeblendeten Formular Felds, das vom antifälschungs System zum Rendering von antifälschungstoken in Sichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4973d-239">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="4973d-240">HeaderName</span><span class="sxs-lookup"><span data-stu-id="4973d-240">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="4973d-241">Der Name des Headers, der vom antifälschungs System verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="4973d-241">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="4973d-242">Wenn `null`, berücksichtigt das System nur Formulardaten.</span><span class="sxs-lookup"><span data-stu-id="4973d-242">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="4973d-243">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="4973d-243">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="4973d-244">Gibt an, ob HTTPS für das antifälschungs System erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="4973d-244">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="4973d-245">Wenn `true`, schlagen nicht-HTTPS-Anforderungen fehl.</span><span class="sxs-lookup"><span data-stu-id="4973d-245">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="4973d-246">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4973d-246">Defaults to `false`.</span></span> <span data-ttu-id="4973d-247">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="4973d-247">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="4973d-248">Die empfohlene Alternative ist die Festlegung von Cookie. securepolicy.</span><span class="sxs-lookup"><span data-stu-id="4973d-248">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="4973d-249">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="4973d-249">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="4973d-250">Gibt an, ob die Generierung des `X-Frame-Options`-Headers unterdrückt werden soll.</span><span class="sxs-lookup"><span data-stu-id="4973d-250">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="4973d-251">Standardmäßig wird der-Header mit dem Wert "sameorigin" generiert.</span><span class="sxs-lookup"><span data-stu-id="4973d-251">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="4973d-252">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="4973d-252">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="4973d-253">Weitere Informationen finden Sie unter [cookieauthenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="4973d-253">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="4973d-254">Konfigurieren von antifälschungsfeatures mit iantifälschung</span><span class="sxs-lookup"><span data-stu-id="4973d-254">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="4973d-255">[Iantifälschung](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) bietet die API zum Konfigurieren von antifälschungs Features.</span><span class="sxs-lookup"><span data-stu-id="4973d-255">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="4973d-256">`IAntiforgery` können in der `Configure`-Methode der `Startup`-Klasse angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="4973d-256">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="4973d-257">Im folgenden Beispiel wird Middleware von der Startseite der APP verwendet, um ein antifälschungstoken zu generieren und in der Antwort als Cookie zu senden (unter Verwendung der standardmäßigen Angular-Benennungs Konvention, die weiter unten in diesem Thema beschrieben wird):</span><span class="sxs-lookup"><span data-stu-id="4973d-257">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a><span data-ttu-id="4973d-258">Antifälschungs Überprüfung erforderlich</span><span class="sxs-lookup"><span data-stu-id="4973d-258">Require antiforgery validation</span></span>

<span data-ttu-id="4973d-259">[Validateantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) ist ein Aktionsfilter, der auf eine einzelne Aktion, einen Controller oder Global angewendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="4973d-259">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="4973d-260">Anforderungen an Aktionen, die diesen Filter angewendet haben, werden blockiert, es sei denn, die Anforderung enthält ein gültiges antifälschungstoken.</span><span class="sxs-lookup"><span data-stu-id="4973d-260">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

<span data-ttu-id="4973d-261">Das `ValidateAntiForgeryToken`-Attribut erfordert ein Token für Anforderungen an die Aktionsmethoden, die es markiert, einschließlich HTTP GET-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="4973d-261">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="4973d-262">Wenn das `ValidateAntiForgeryToken`-Attribut auf die Controller der APP angewendet wird, kann es mit dem `IgnoreAntiforgeryToken`-Attribut überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="4973d-262">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="4973d-263">ASP.net Core unterstützt das Hinzufügen von antifälschungstoken, um Anforderungen automatisch zu erhalten</span><span class="sxs-lookup"><span data-stu-id="4973d-263">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="4973d-264">Antifälschungstokentoken nur für unsichere HTTP-Methoden automatisch überprüfen</span><span class="sxs-lookup"><span data-stu-id="4973d-264">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="4973d-265">ASP.net Core apps generieren keine antifälschungstokentoken für sichere HTTP-Methoden (Get, Head, Options und Trace).</span><span class="sxs-lookup"><span data-stu-id="4973d-265">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="4973d-266">Anstatt das `ValidateAntiForgeryToken` Attribut weitgehend anzuwenden und es dann mit `IgnoreAntiforgeryToken` Attributen zu überschreiben, kann das [autovalidateantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) -Attribut verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4973d-266">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="4973d-267">Dieses Attribut funktioniert identisch mit dem `ValidateAntiForgeryToken`-Attribut, mit dem Unterschied, dass es keine Token für Anforderungen erfordert, die mit den folgenden HTTP-Methoden ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="4973d-267">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="4973d-268">GET</span><span class="sxs-lookup"><span data-stu-id="4973d-268">GET</span></span>
* <span data-ttu-id="4973d-269">HEAD-,</span><span class="sxs-lookup"><span data-stu-id="4973d-269">HEAD</span></span>
* <span data-ttu-id="4973d-270">OPTIONEN</span><span class="sxs-lookup"><span data-stu-id="4973d-270">OPTIONS</span></span>
* <span data-ttu-id="4973d-271">ABLAUFVERFOLGUNG</span><span class="sxs-lookup"><span data-stu-id="4973d-271">TRACE</span></span>

<span data-ttu-id="4973d-272">Wir empfehlen die Verwendung von `AutoValidateAntiforgeryToken` für Szenarien ohne API.</span><span class="sxs-lookup"><span data-stu-id="4973d-272">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="4973d-273">Dadurch wird sichergestellt, dass Post-Aktionen standardmäßig geschützt werden.</span><span class="sxs-lookup"><span data-stu-id="4973d-273">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="4973d-274">Die Alternative besteht darin, antifälschungstokentoken standardmäßig zu ignorieren, es sei denn, `ValidateAntiForgeryToken` auf einzelne Aktionsmethoden angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="4973d-274">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="4973d-275">In diesem Szenario ist es wahrscheinlicher, dass eine Post-Aktionsmethode versehentlich ungeschützt bleibt, und die APP ist anfällig für CSRF-Angriffe.</span><span class="sxs-lookup"><span data-stu-id="4973d-275">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="4973d-276">Alle Beiträge sollten das antifälschungstoken senden.</span><span class="sxs-lookup"><span data-stu-id="4973d-276">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="4973d-277">APIs verfügen nicht über einen automatischen Mechanismus zum Senden des nicht-Cookie-Teils des Tokens.</span><span class="sxs-lookup"><span data-stu-id="4973d-277">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="4973d-278">Die Implementierung hängt wahrscheinlich von der Implementierung des Client Codes ab.</span><span class="sxs-lookup"><span data-stu-id="4973d-278">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="4973d-279">Einige Beispiele sind unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="4973d-279">Some examples are shown below:</span></span>

<span data-ttu-id="4973d-280">Beispiel auf Klassenebene:</span><span class="sxs-lookup"><span data-stu-id="4973d-280">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="4973d-281">Globales Beispiel:</span><span class="sxs-lookup"><span data-stu-id="4973d-281">Global example:</span></span>

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="4973d-282">Globale oder Controller-antifälschungsattribute überschreiben</span><span class="sxs-lookup"><span data-stu-id="4973d-282">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="4973d-283">Der [ignoreantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) -Filter wird verwendet, um den Bedarf an einem antifälschungstoken für eine bestimmte Aktion (oder einen Controller) auszuschließen.</span><span class="sxs-lookup"><span data-stu-id="4973d-283">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="4973d-284">Wenn dieser Filter angewendet wird, überschreibt er `ValidateAntiForgeryToken`-und `AutoValidateAntiforgeryToken` Filter, die auf einer höheren Ebene (Global oder auf einem Controller) angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="4973d-284">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="4973d-285">Token nach der Authentifizierung aktualisieren</span><span class="sxs-lookup"><span data-stu-id="4973d-285">Refresh tokens after authentication</span></span>

<span data-ttu-id="4973d-286">Token sollten aktualisiert werden, nachdem der Benutzer authentifiziert wurde, indem er den Benutzer an eine Ansicht oder eine Razor Pages Seite umgeleitet hat.</span><span class="sxs-lookup"><span data-stu-id="4973d-286">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="4973d-287">JavaScript, AJAX und Spas</span><span class="sxs-lookup"><span data-stu-id="4973d-287">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="4973d-288">In herkömmlichen HTML-basierten apps werden antifälschungstokentoken mithilfe von ausgeblendeten Formularfeldern an den Server übermittelt.</span><span class="sxs-lookup"><span data-stu-id="4973d-288">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="4973d-289">In modernen JavaScript-basierten apps und Spas werden viele Anforderungen Programm gesteuert durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="4973d-289">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="4973d-290">Diese AJAX-Anforderungen verwenden möglicherweise andere Techniken (z. b. Anforderungs Header oder Cookies), um das Token zu senden.</span><span class="sxs-lookup"><span data-stu-id="4973d-290">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="4973d-291">Wenn Cookies zum Speichern von Authentifizierungs Token und zum Authentifizieren von API-Anforderungen auf dem Server verwendet werden, ist CSRF ein potenzielles Problem.</span><span class="sxs-lookup"><span data-stu-id="4973d-291">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="4973d-292">Wenn lokaler Speicher zum Speichern des Tokens verwendet wird, wird die CSRF-Sicherheits Anfälligkeit möglicherweise verringert, da Werte aus lokalem Speicher nicht automatisch mit jeder Anforderung an den Server gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="4973d-292">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="4973d-293">Daher ist die Verwendung von lokalem Speicher, um das antifälschungstoken auf dem Client zu speichern und das Token als Anforderungs Header zu senden, eine empfohlene Vorgehensweise.</span><span class="sxs-lookup"><span data-stu-id="4973d-293">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="4973d-294">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4973d-294">JavaScript</span></span>

<span data-ttu-id="4973d-295">Mithilfe von JavaScript mit Sichten kann das Token mithilfe eines Diensts innerhalb der Sicht erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="4973d-295">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="4973d-296">Fügen Sie den Dienst " [Microsoft. aspnetcore. Antifälschung. iantifälschung](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) " in die Ansicht ein, und nennen Sie " [getandstoretokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)":</span><span class="sxs-lookup"><span data-stu-id="4973d-296">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="4973d-297">Bei dieser Vorgehensweise entfällt die Notwendigkeit, direkt mit dem Festlegen von Cookies vom Server oder dem Lesen aus dem Client umzugehen.</span><span class="sxs-lookup"><span data-stu-id="4973d-297">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="4973d-298">Im vorangehenden Beispiel wird JavaScript verwendet, um den Wert des ausgeblendeten Felds für den AJAX-Post-Header</span><span class="sxs-lookup"><span data-stu-id="4973d-298">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="4973d-299">JavaScript kann auch auf Token in Cookies zugreifen und den Inhalt des Cookies verwenden, um einen Header mit dem Wert des Tokens zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4973d-299">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="4973d-300">Wenn das Skript anfordert, das Token in einem Header namens `X-CSRF-TOKEN`zu senden, konfigurieren Sie den antifälschungs Dienst so, dass er nach dem `X-CSRF-TOKEN`-Header sucht:</span><span class="sxs-lookup"><span data-stu-id="4973d-300">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="4973d-301">Im folgenden Beispiel wird JavaScript verwendet, um eine AJAX-Anforderung mit dem entsprechenden Header zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="4973d-301">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a><span data-ttu-id="4973d-302">AngularJS</span><span class="sxs-lookup"><span data-stu-id="4973d-302">AngularJS</span></span>

<span data-ttu-id="4973d-303">Angularjs verwendet eine Konvention, um CSRF zu adressieren.</span><span class="sxs-lookup"><span data-stu-id="4973d-303">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="4973d-304">Wenn der Server ein Cookie mit dem Namen `XSRF-TOKEN`sendet, fügt der angularjs-`$http` Dienst den cookiehwert einem Header hinzu, wenn er eine Anforderung an den Server sendet.</span><span class="sxs-lookup"><span data-stu-id="4973d-304">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="4973d-305">Dieser Prozess erfolgt automatisch.</span><span class="sxs-lookup"><span data-stu-id="4973d-305">This process is automatic.</span></span> <span data-ttu-id="4973d-306">Der-Header muss nicht explizit im Client festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="4973d-306">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="4973d-307">Der Header Name ist `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="4973d-307">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="4973d-308">Der Server sollte diesen Header erkennen und seinen Inhalt überprüfen.</span><span class="sxs-lookup"><span data-stu-id="4973d-308">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="4973d-309">Damit ASP.net Core-API mit dieser Konvention beim Starten der Anwendung funktioniert:</span><span class="sxs-lookup"><span data-stu-id="4973d-309">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="4973d-310">Konfigurieren Sie Ihre APP, um ein Token in einem Cookie namens `XSRF-TOKEN`bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="4973d-310">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="4973d-311">Konfigurieren Sie den antifälschungs Dienst für die Suche nach einem Header mit dem Namen `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="4973d-311">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

<span data-ttu-id="4973d-312">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4973d-312">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="4973d-313">Antifälschung erweitern</span><span class="sxs-lookup"><span data-stu-id="4973d-313">Extend antiforgery</span></span>

<span data-ttu-id="4973d-314">Der [iantiforgeryadditionaldataprovider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) -Typ ermöglicht es Entwicklern, das Verhalten des Anti-CSRF-Systems zu erweitern, indem zusätzliche Daten in jedem Token abgerundet werden.</span><span class="sxs-lookup"><span data-stu-id="4973d-314">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="4973d-315">Die [getadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) -Methode wird jedes Mal aufgerufen, wenn ein Feld Token generiert wird, und der Rückgabewert wird in das generierte Token eingebettet.</span><span class="sxs-lookup"><span data-stu-id="4973d-315">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="4973d-316">Ein Implementierer könnte einen Zeitstempel, eine Nonce oder einen anderen Wert zurückgeben und dann [validateadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) aufzurufen, um diese Daten zu validieren, wenn das Token überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="4973d-316">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="4973d-317">Der Benutzername des Clients ist bereits in die generierten Token eingebettet, sodass es nicht erforderlich ist, diese Informationen einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="4973d-317">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="4973d-318">Wenn ein Token zusätzliche Daten enthält, aber keine `IAntiForgeryAdditionalDataProvider` konfiguriert ist, werden die ergänzenden Daten nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="4973d-318">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4973d-319">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4973d-319">Additional resources</span></span>

* <span data-ttu-id="4973d-320">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) für das [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="4973d-320">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
