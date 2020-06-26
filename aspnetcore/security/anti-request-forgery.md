---
title: Verhindern von Angriffen für Website übergreifende Anforderungs Fälschung (XSRF/CSRF) in ASP.net Core
author: steve-smith
description: Erfahren Sie, wie Sie Angriffe auf Web-Apps verhindern, bei denen eine böswillige Website die Interaktion zwischen einem Client Browser und der APP beeinflussen kann.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/anti-request-forgery
ms.openlocfilehash: 0ec9aa5f2e1c9d439bbea2ac15ffb50b3e42daca
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399334"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="ca7dd-103">Verhindern von Angriffen für Website übergreifende Anforderungs Fälschung (XSRF/CSRF) in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ca7dd-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="ca7dd-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), von " [fyaz Hasan](https://twitter.com/FiyazBinHasan)" und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ca7dd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ca7dd-105">Website übergreifende Anforderungs Fälschung (auch als XSRF oder CSRF bezeichnet) ist ein Angriff auf im Web gehostete Apps, bei dem eine böswillige Web-App die Interaktion zwischen einem Client Browser und einer Web-App beeinflussen kann, die diesem Browser vertraut.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-105">Cross-site request forgery (also known as XSRF or CSRF) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="ca7dd-106">Diese Angriffe sind möglich, da Webbrowser einige Arten von Authentifizierungs Token automatisch mit jeder Anforderung an eine Website senden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="ca7dd-107">Diese Art von Exploit wird auch als *One-Click-Angriff* oder *Sitzungsart* bezeichnet, da der Angriff die zuvor authentifizierte Sitzung des Benutzers nutzt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="ca7dd-108">Ein Beispiel für einen CSRF-Angriff:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="ca7dd-109">Ein Benutzer meldet sich `www.good-banking-site.com` mit der Formular Authentifizierung an.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="ca7dd-110">Der Server authentifiziert den Benutzer und gibt eine Antwort aus, die ein Authentifizierungs Cookie enthält.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="ca7dd-111">Die Site ist anfällig für Angriffe, da Sie jede empfangene Anforderung mit einem gültigen Authentifizierungs Cookie vertraut.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="ca7dd-112">Der Benutzer besucht eine böswillige Website, `www.bad-crook-site.com` .</span><span class="sxs-lookup"><span data-stu-id="ca7dd-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="ca7dd-113">Die böswillige Site `www.bad-crook-site.com` enthält ein HTML-Formular, das dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="ca7dd-114">Beachten Sie, dass das Formular die `action` Beiträge an die anfällige Site und nicht an die böswillige Site sendet.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="ca7dd-115">Dies ist der "Cross-Site"-Teil von CSRF.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="ca7dd-116">Der Benutzer wählt die Schaltfläche Senden aus.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-116">The user selects the submit button.</span></span> <span data-ttu-id="ca7dd-117">Der Browser stellt die Anforderung her und schließt automatisch das Authentifizierungs Cookie für die angeforderte Domäne ein `www.good-banking-site.com` .</span><span class="sxs-lookup"><span data-stu-id="ca7dd-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="ca7dd-118">Die Anforderung wird auf dem `www.good-banking-site.com` Server mit dem Authentifizierungs Kontext des Benutzers ausgeführt und kann alle Aktionen ausführen, die ein authentifizierter Benutzer ausführen darf.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="ca7dd-119">Zusätzlich zu dem Szenario, in dem der Benutzer die Schaltfläche zum Senden des Formulars auswählt, könnte die böswillige Site folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="ca7dd-120">Führen Sie ein Skript aus, das das Formular automatisch sendet.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="ca7dd-121">Senden Sie die Formular Übermittlung als AJAX-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="ca7dd-122">Blenden Sie das Formular mit CSS aus.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-122">Hide the form using CSS.</span></span>

<span data-ttu-id="ca7dd-123">Für diese alternativen Szenarien sind keine Aktionen oder Eingaben vom Benutzer erforderlich, außer wenn Sie zunächst die böswillige Website besuchen.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="ca7dd-124">Durch die Verwendung von HTTPS wird kein CSRF-Angriff verhindert.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="ca7dd-125">Die böswillige Site kann eine `https://www.good-banking-site.com/` Anforderung genauso einfach senden, wie Sie eine unsichere Anforderung senden kann.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="ca7dd-126">Einige Angriffe zielen auf Endpunkte ab, die auf Get-Anforderungen reagieren. in diesem Fall kann ein imagetag verwendet werden, um die Aktion auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="ca7dd-127">Diese Art von Angriff erfolgt häufig auf Forums Websites, die Bilder zulassen, aber JavaScript blockieren.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="ca7dd-128">Apps, die den Status von Get-Anforderungen ändern, bei denen Variablen oder Ressourcen geändert werden, sind anfällig für böswillige Angriffe.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="ca7dd-129">**Get-Anforderungen, die den Zustand ändern, sind unsicher. Eine bewährte Vorgehensweise besteht darin, den Status einer GET-Anforderung nie zu ändern.**</span><span class="sxs-lookup"><span data-stu-id="ca7dd-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="ca7dd-130">Csrf-Angriffe sind für Web-Apps möglich, die Cookies für die Authentifizierung verwenden:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="ca7dd-131">Browser speichern Cookies, die von einer Web-App ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="ca7dd-132">Gespeicherte Cookies enthalten Sitzungs Cookies für authentifizierte Benutzer.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="ca7dd-133">Browser senden alle Cookies, die einer Domäne zugeordnet sind, an die Web-App jede Anforderung, unabhängig davon, wie die App-Anforderung im Browser generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="ca7dd-134">Allerdings sind CSRF-Angriffe nicht auf das Ausnutzen von Cookies beschränkt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="ca7dd-135">Beispielsweise sind die Standard-und Digestauthentifizierung auch anfällig.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="ca7dd-136">Nachdem sich ein Benutzer mit der Standard-oder Digestauthentifizierung angemeldet hat, sendet der Browser die Anmelde Informationen automatisch, bis die Sitzung &dagger; beendet wird.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="ca7dd-137">&dagger;In diesem Kontext bezieht sich *Sitzung* auf die Client seitige Sitzung, in der der Benutzer authentifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="ca7dd-138">Sie steht nicht im Zusammenhang mit serverseitigen Sitzungen oder [ASP.net Core Sitzungs Middleware](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="ca7dd-139">Benutzer können Schutz vor CSRF-Sicherheitsrisiken durch Vorkehrungen treffen:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="ca7dd-140">Melden Sie sich von Web-Apps ab, wenn Sie diese verwenden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="ca7dd-141">Löschen Sie Browser Cookies regelmäßig.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="ca7dd-142">Csrf-Sicherheitsrisiken sind jedoch im Grunde ein Problem mit der Web-App, nicht mit dem Endbenutzer.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="ca7dd-143">Grundlagen der Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="ca7dd-143">Authentication fundamentals</span></span>

<span data-ttu-id="ca7dd-144">Die cookiebasierte Authentifizierung ist eine beliebte Form der Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="ca7dd-145">Tokenbasierte Authentifizierungssysteme werden immer beliebter, insbesondere für Single-Page-Anwendungen (Spas).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="ca7dd-146">Cookiebasierte Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="ca7dd-146">Cookie-based authentication</span></span>

<span data-ttu-id="ca7dd-147">Wenn ein Benutzer sich mit seinem Benutzernamen und Kennwort authentifiziert, wird ein Token ausgestellt, das ein Authentifizierungs Ticket enthält, das für die Authentifizierung und Autorisierung verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="ca7dd-148">Das Token wird als Cookie gespeichert, das jede vom Client vornimmt Anforderung begleitet.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="ca7dd-149">Das Erstellen und Validieren dieses Cookies erfolgt durch die cookumsauthentifizierungs-Middleware.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="ca7dd-150">Die [Middleware](xref:fundamentals/middleware/index) serialisiert einen Benutzer Prinzipal in ein verschlüsseltes Cookie.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="ca7dd-151">Bei nachfolgenden Anforderungen überprüft die Middleware das Cookie, erstellt den Prinzipal neu und weist den Prinzipal der [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) -Eigenschaft von [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext)zu.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="ca7dd-152">Tokenbasierte Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="ca7dd-152">Token-based authentication</span></span>

<span data-ttu-id="ca7dd-153">Wenn ein Benutzer authentifiziert wird, wird ein Token ausgestellt (kein antifälschungstoken).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="ca7dd-154">Das Token enthält Benutzerinformationen in Form von [Ansprüchen](/dotnet/framework/security/claims-based-identity-model) oder ein Verweis Token, das die APP auf den Benutzer Zustand verweist, der in der APP verwaltet wird.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="ca7dd-155">Wenn ein Benutzer versucht, auf eine Ressource zuzugreifen, die eine Authentifizierung erfordert, wird das Token mit einem zusätzlichen Autorisierungs Header als bearertoken an die APP gesendet.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="ca7dd-156">Dadurch wird die APP zustandslos.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-156">This makes the app stateless.</span></span> <span data-ttu-id="ca7dd-157">In jeder nachfolgenden Anforderung wird das Token in der Anforderung für die serverseitige Validierung übermittelt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="ca7dd-158">Dieses Token ist nicht *verschlüsselt*. Sie ist *codiert*.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="ca7dd-159">Auf dem Server wird das Token decodiert, um auf seine Informationen zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="ca7dd-160">Um das Token für nachfolgende Anforderungen zu senden, speichern Sie das Token im lokalen Speicher des Browsers.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="ca7dd-161">Machen Sie sich keine Gedanken über das Sicherheitsrisiko von CSRF, wenn das Token im lokalen Speicher des Browsers gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="ca7dd-162">Csrf ist ein Problem, wenn das Token in einem Cookie gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-162">CSRF is a concern when the token is stored in a cookie.</span></span> <span data-ttu-id="ca7dd-163">Weitere Informationen finden Sie im GitHub Issue [Spa-Codebeispiel zum Hinzufügen von zwei Cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-163">For more information, see the GitHub issue [SPA code sample adds two cookies](https://github.com/dotnet/AspNetCore.Docs/issues/13369).</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="ca7dd-164">Mehrere apps, die in einer Domäne gehostet werden</span><span class="sxs-lookup"><span data-stu-id="ca7dd-164">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="ca7dd-165">Freigegebene Hostingumgebungen sind anfällig für Session Hijacking, Login CSRF und andere Angriffe.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-165">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="ca7dd-166">Obwohl `example1.contoso.net` `example2.contoso.net` es sich bei und um unterschiedliche Hosts handelt, gibt es eine implizite Vertrauensstellung zwischen den Hosts in der `*.contoso.net` Domäne.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-166">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="ca7dd-167">Mit dieser impliziten Vertrauensstellung können sich potenziell nicht vertrauenswürdige Hosts auf die Cookies der anderen Anwendungen auswirken (die gleichen Ursprungs Richtlinien, die AJAX-Anforderungen Regeln, werden nicht unbedingt auf http-Cookies angewendet).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-167">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="ca7dd-168">Angriffe, die vertrauenswürdige Cookies zwischen apps ausnutzen, die in derselben Domäne gehostet werden, können durch das Freigeben von Domänen verhindert werden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-168">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="ca7dd-169">Wenn jede app in ihrer eigenen Domäne gehostet wird, ist keine implizite Cookie-Vertrauensstellung zum ausnutzen vorhanden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-169">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="ca7dd-170">Konfiguration der ASP.net Core-Antifälschung</span><span class="sxs-lookup"><span data-stu-id="ca7dd-170">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="ca7dd-171">ASP.net Core implementiert Antifälschung mithilfe [ASP.net Core Datenschutzes](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-171">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="ca7dd-172">Der Stapel für den Datenschutz muss so konfiguriert sein, dass er in einer Serverfarm funktioniert.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-172">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="ca7dd-173">Weitere Informationen finden Sie unter [Konfigurieren des Schutzes von Daten](xref:security/data-protection/configuration/overview) .</span><span class="sxs-lookup"><span data-stu-id="ca7dd-173">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ca7dd-174">Die Middleware für die Antifälschung wird dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) hinzugefügt, wenn eine der folgenden APIs in aufgerufen wird `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ca7dd-174">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when one of the following APIs is called in `Startup.ConfigureServices`:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>
* <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*>
* <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>
* <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub*>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca7dd-175">Antifälschungsmiddleware wird dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> in aufgerufen wird.`Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="ca7dd-175">Antiforgery middleware is added to the [Dependency injection](xref:fundamentals/dependency-injection) container when <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> is called in `Startup.ConfigureServices`</span></span>

::: moniker-end

<span data-ttu-id="ca7dd-176">In ASP.net Core 2,0 oder höher fügt [formtaghelper](xref:mvc/views/working-with-forms#the-form-tag-helper) antifälschungstoken in HTML-Formularelemente ein.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-176">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="ca7dd-177">Das folgende Markup in einer Razor Datei generiert automatisch antifälschungstokentoken:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-177">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="ca7dd-178">Ebenso generiert [ihtmlhelper. BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) standardmäßig antifälschungstokentoken, wenn die-Methode des Formulars nicht erhalten wird.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-178">Similarly, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="ca7dd-179">Die automatische Generierung von antifälschungstoken für HTML-Formularelemente tritt auf, wenn das `<form>` -Tag das `method="post"` -Attribut enthält und eine der folgenden Optionen zutrifft:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-179">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

* <span data-ttu-id="ca7dd-180">Das Aktions Attribut ist leer ( `action=""` ).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-180">The action attribute is empty (`action=""`).</span></span>
* <span data-ttu-id="ca7dd-181">Das action-Attribut wird nicht bereitgestellt ( `<form method="post">` ).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-181">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="ca7dd-182">Die automatische Generierung von antifälschungstoken für HTML-Formularelemente kann deaktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-182">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="ca7dd-183">Deaktivieren Sie antifälschungstoken explizit mit dem- `asp-antiforgery` Attribut:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-183">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="ca7dd-184">Das Formular Element wird von taghilfsprogrammen mithilfe des taghilfsprogramms [! Opt-out-Symbols](xref:mvc/views/tag-helpers/intro#opt-out)deaktiviert:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-184">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="ca7dd-185">Entfernen Sie `FormTagHelper` aus der Ansicht.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-185">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="ca7dd-186">Der `FormTagHelper` kann aus einer Ansicht entfernt werden, indem der Ansicht die folgende-Direktive hinzugefügt wird Razor :</span><span class="sxs-lookup"><span data-stu-id="ca7dd-186">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="ca7dd-187">[ Razor Seiten](xref:razor-pages/index) werden automatisch vor XSRF/CSRF geschützt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-187">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="ca7dd-188">Weitere Informationen finden Sie unter [XSRF/CSRF und Razor Seiten](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-188">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="ca7dd-189">Der häufigste Ansatz für die Verteidigung gegen CSRF-Angriffe ist die Verwendung des *Synchronisierungs Token-Musters* (STP).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-189">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="ca7dd-190">STP wird verwendet, wenn der Benutzer eine Seite mit Formulardaten anfordert:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-190">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="ca7dd-191">Der Server sendet ein Token, das der Identität des aktuellen Benutzers zugeordnet ist, an den Client.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-191">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="ca7dd-192">Der Client sendet das Token zur Überprüfung an den Server zurück.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-192">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="ca7dd-193">Wenn der Server ein Token empfängt, das nicht mit der Identität des authentifizierten Benutzers identisch ist, wird die Anforderung abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-193">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="ca7dd-194">Das Token ist eindeutig und unvorhersehbar.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-194">The token is unique and unpredictable.</span></span> <span data-ttu-id="ca7dd-195">Das Token kann auch verwendet werden, um eine ordnungsgemäße Sequenzierung einer Reihe von Anforderungen sicherzustellen (z. b. zur Sicherstellung der Anforderungs Sequenz von: Seite 1 > Seite 2 > Seite 3).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-195">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 > page 2 > page 3).</span></span> <span data-ttu-id="ca7dd-196">Alle Formulare in ASP.net Core MVC-und Razor Seitenvorlagen generieren antifälschungstokentoken.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-196">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="ca7dd-197">Das folgende Paar von Sicht Beispielen generiert antifälschungstokentoken:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-197">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="ca7dd-198">Fügen Sie einem-Element explizit ein antifälschungstoken hinzu, `<form>` ohne taghilfsprogramme mit der HTML-Hilfe zu verwenden [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken) :</span><span class="sxs-lookup"><span data-stu-id="ca7dd-198">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [`@Html.AntiForgeryToken`](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="ca7dd-199">In jedem der vorangegangenen Fälle fügt ASP.net Core ein ausgeblendetes Formularfeld ähnlich dem folgenden hinzu:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-199">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="ca7dd-200">ASP.net Core enthält drei [Filter](xref:mvc/controllers/filters) zum Arbeiten mit antifälschungstoken:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-200">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="ca7dd-201">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="ca7dd-201">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="ca7dd-202">Autovalidateantiforgerytoken</span><span class="sxs-lookup"><span data-stu-id="ca7dd-202">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="ca7dd-203">Ignoreantiforgerytoken</span><span class="sxs-lookup"><span data-stu-id="ca7dd-203">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="ca7dd-204">Antifälschungsoptionen</span><span class="sxs-lookup"><span data-stu-id="ca7dd-204">Antiforgery options</span></span>

<span data-ttu-id="ca7dd-205">Optionen für die [Antifälschung](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) anpassen in `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ca7dd-205">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="ca7dd-206">&dagger;Legen Sie die Eigenschaften für die Antifälschung `Cookie` mithilfe der Eigenschaften der [cookiebuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) -Klasse fest.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-206">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="ca7dd-207">Option</span><span class="sxs-lookup"><span data-stu-id="ca7dd-207">Option</span></span> | <span data-ttu-id="ca7dd-208">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="ca7dd-208">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="ca7dd-209">Cookie</span><span class="sxs-lookup"><span data-stu-id="ca7dd-209">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="ca7dd-210">Legt die Einstellungen fest, die zum Erstellen der antifälschungscookies verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-210">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="ca7dd-211">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="ca7dd-211">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="ca7dd-212">Der Name des ausgeblendeten Formular Felds, das vom antifälschungs System zum Rendering von antifälschungstoken in Sichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-212">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="ca7dd-213">Header Name</span><span class="sxs-lookup"><span data-stu-id="ca7dd-213">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="ca7dd-214">Der Name des Headers, der vom antifälschungs System verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-214">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="ca7dd-215">Gibt an `null` , dass das System nur Formulardaten berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-215">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="ca7dd-216">Suppressxframeoptionsheader</span><span class="sxs-lookup"><span data-stu-id="ca7dd-216">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="ca7dd-217">Gibt an, ob die Generierung des Headers unterdrückt werden soll `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="ca7dd-217">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="ca7dd-218">Standardmäßig wird der-Header mit dem Wert "sameorigin" generiert.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-218">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="ca7dd-219">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-219">Defaults to `false`.</span></span> |

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

| <span data-ttu-id="ca7dd-220">Option</span><span class="sxs-lookup"><span data-stu-id="ca7dd-220">Option</span></span> | <span data-ttu-id="ca7dd-221">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="ca7dd-221">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="ca7dd-222">Cookie</span><span class="sxs-lookup"><span data-stu-id="ca7dd-222">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="ca7dd-223">Legt die Einstellungen fest, die zum Erstellen der antifälschungscookies verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-223">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="ca7dd-224">Cookiedomäne</span><span class="sxs-lookup"><span data-stu-id="ca7dd-224">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | <span data-ttu-id="ca7dd-225">Die Domäne des Cookies.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-225">The domain of the cookie.</span></span> <span data-ttu-id="ca7dd-226">Wird standardmäßig auf `null` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-226">Defaults to `null`.</span></span> <span data-ttu-id="ca7dd-227">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-227">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="ca7dd-228">Die empfohlene Alternative ist "Cookie. Domain".</span><span class="sxs-lookup"><span data-stu-id="ca7dd-228">The recommended alternative is Cookie.Domain.</span></span> |
| [<span data-ttu-id="ca7dd-229">CookieName</span><span class="sxs-lookup"><span data-stu-id="ca7dd-229">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | <span data-ttu-id="ca7dd-230">Der Name des Cookies.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-230">The name of the cookie.</span></span> <span data-ttu-id="ca7dd-231">Wenn nicht festgelegt, generiert das System einen eindeutigen Namen, beginnend mit dem [defaultcookieprefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". Aspnetcore. Antifälschung. ").</span><span class="sxs-lookup"><span data-stu-id="ca7dd-231">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="ca7dd-232">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-232">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="ca7dd-233">Die empfohlene Alternative ist Cookie.Name.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-233">The recommended alternative is Cookie.Name.</span></span> |
| [<span data-ttu-id="ca7dd-234">CookiePath</span><span class="sxs-lookup"><span data-stu-id="ca7dd-234">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | <span data-ttu-id="ca7dd-235">Der Pfad, der für das Cookie festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-235">The path set on the cookie.</span></span> <span data-ttu-id="ca7dd-236">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-236">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="ca7dd-237">Die empfohlene Alternative ist Cookie. Path.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-237">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="ca7dd-238">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="ca7dd-238">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="ca7dd-239">Der Name des ausgeblendeten Formular Felds, das vom antifälschungs System zum Rendering von antifälschungstoken in Sichten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-239">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="ca7dd-240">Header Name</span><span class="sxs-lookup"><span data-stu-id="ca7dd-240">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="ca7dd-241">Der Name des Headers, der vom antifälschungs System verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-241">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="ca7dd-242">Gibt an `null` , dass das System nur Formulardaten berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-242">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="ca7dd-243">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="ca7dd-243">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="ca7dd-244">Gibt an, ob HTTPS für das antifälschungs System erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-244">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="ca7dd-245">Gibt an `true` , dass nicht-HTTPS-Anforderungen fehlschlagen.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-245">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="ca7dd-246">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-246">Defaults to `false`.</span></span> <span data-ttu-id="ca7dd-247">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-247">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="ca7dd-248">Die empfohlene Alternative ist die Festlegung von Cookie. securepolicy.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-248">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="ca7dd-249">Suppressxframeoptionsheader</span><span class="sxs-lookup"><span data-stu-id="ca7dd-249">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="ca7dd-250">Gibt an, ob die Generierung des Headers unterdrückt werden soll `X-Frame-Options` .</span><span class="sxs-lookup"><span data-stu-id="ca7dd-250">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="ca7dd-251">Standardmäßig wird der-Header mit dem Wert "sameorigin" generiert.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-251">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="ca7dd-252">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-252">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="ca7dd-253">Weitere Informationen finden Sie unter [cookieauthenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-253">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="ca7dd-254">Konfigurieren von antifälschungsfeatures mit iantifälschung</span><span class="sxs-lookup"><span data-stu-id="ca7dd-254">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="ca7dd-255">[Iantifälschung](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) bietet die API zum Konfigurieren von antifälschungs Features.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-255">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="ca7dd-256">`IAntiforgery`kann in der- `Configure` Methode der-Klasse angefordert werden `Startup` .</span><span class="sxs-lookup"><span data-stu-id="ca7dd-256">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="ca7dd-257">Im folgenden Beispiel wird Middleware von der Startseite der APP verwendet, um ein antifälschungstoken zu generieren und in der Antwort als Cookie zu senden (unter Verwendung der standardmäßigen Angular-Benennungs Konvention, die weiter unten in diesem Thema beschrieben wird):</span><span class="sxs-lookup"><span data-stu-id="ca7dd-257">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

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

### <a name="require-antiforgery-validation"></a><span data-ttu-id="ca7dd-258">Antifälschungs Überprüfung erforderlich</span><span class="sxs-lookup"><span data-stu-id="ca7dd-258">Require antiforgery validation</span></span>

<span data-ttu-id="ca7dd-259">[Validateantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) ist ein Aktionsfilter, der auf eine einzelne Aktion, einen Controller oder Global angewendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-259">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="ca7dd-260">Anforderungen an Aktionen, die diesen Filter angewendet haben, werden blockiert, es sei denn, die Anforderung enthält ein gültiges antifälschungstoken.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-260">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

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

<span data-ttu-id="ca7dd-261">Das- `ValidateAntiForgeryToken` Attribut erfordert ein Token für Anforderungen an die Aktionsmethoden, die es markiert, einschließlich HTTP GET-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-261">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it marks, including HTTP GET requests.</span></span> <span data-ttu-id="ca7dd-262">Wenn das `ValidateAntiForgeryToken` -Attribut auf die Controller der APP angewendet wird, kann es mit dem-Attribut überschrieben werden `IgnoreAntiforgeryToken` .</span><span class="sxs-lookup"><span data-stu-id="ca7dd-262">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="ca7dd-263">ASP.net Core unterstützt das Hinzufügen von antifälschungstoken, um Anforderungen automatisch zu erhalten</span><span class="sxs-lookup"><span data-stu-id="ca7dd-263">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="ca7dd-264">Antifälschungstokentoken nur für unsichere HTTP-Methoden automatisch überprüfen</span><span class="sxs-lookup"><span data-stu-id="ca7dd-264">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="ca7dd-265">ASP.net Core apps generieren keine antifälschungstokentoken für sichere HTTP-Methoden (Get, Head, Options und Trace).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-265">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="ca7dd-266">Anstatt das Attribut weitgehend anzuwenden `ValidateAntiForgeryToken` und dann mit Attributen zu überschreiben `IgnoreAntiforgeryToken` , kann das [autovalidateantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) -Attribut verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-266">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="ca7dd-267">Dieses Attribut funktioniert identisch mit dem- `ValidateAntiForgeryToken` Attribut, mit dem Unterschied, dass es keine Token für Anforderungen erfordert, die mit den folgenden HTTP-Methoden ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-267">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="ca7dd-268">GET</span><span class="sxs-lookup"><span data-stu-id="ca7dd-268">GET</span></span>
* <span data-ttu-id="ca7dd-269">HEAD</span><span class="sxs-lookup"><span data-stu-id="ca7dd-269">HEAD</span></span>
* <span data-ttu-id="ca7dd-270">OPTIONS</span><span class="sxs-lookup"><span data-stu-id="ca7dd-270">OPTIONS</span></span>
* <span data-ttu-id="ca7dd-271">TRACE</span><span class="sxs-lookup"><span data-stu-id="ca7dd-271">TRACE</span></span>

<span data-ttu-id="ca7dd-272">Wir empfehlen die Verwendung von `AutoValidateAntiforgeryToken` für nicht-API-Szenarien.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-272">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="ca7dd-273">Dadurch wird sichergestellt, dass Post-Aktionen standardmäßig geschützt werden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-273">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="ca7dd-274">Die Alternative besteht darin, antifälschungstokentoken standardmäßig zu ignorieren, es sei denn `ValidateAntiForgeryToken` , wird auf einzelne Aktionsmethoden angewendet.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-274">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="ca7dd-275">In diesem Szenario ist es wahrscheinlicher, dass eine Post-Aktionsmethode versehentlich ungeschützt bleibt, und die APP ist anfällig für CSRF-Angriffe.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-275">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="ca7dd-276">Alle Beiträge sollten das antifälschungstoken senden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-276">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="ca7dd-277">APIs verfügen nicht über einen automatischen Mechanismus zum Senden des nicht-Cookie-Teils des Tokens.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-277">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="ca7dd-278">Die Implementierung hängt wahrscheinlich von der Implementierung des Client Codes ab.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-278">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="ca7dd-279">Einige Beispiele sind unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-279">Some examples are shown below:</span></span>

<span data-ttu-id="ca7dd-280">Beispiel auf Klassenebene:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-280">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="ca7dd-281">Globales Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-281">Global example:</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ca7dd-282">Betreuung. Addmvc (Optionen => Optionen. Filters. Add (neues autovalidateantiforgerytokenattribute ()));</span><span class="sxs-lookup"><span data-stu-id="ca7dd-282">services.AddMvc(options =>     options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

```csharp
services.AddControllersWithViews(options =>
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

::: moniker-end

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="ca7dd-283">Globale oder Controller-antifälschungsattribute überschreiben</span><span class="sxs-lookup"><span data-stu-id="ca7dd-283">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="ca7dd-284">Der [ignoreantiforgerytoken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) -Filter wird verwendet, um den Bedarf an einem antifälschungstoken für eine bestimmte Aktion (oder einen Controller) auszuschließen.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-284">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="ca7dd-285">Wenn dieser Filter angewendet wird, überschreibt dieser Filter die `ValidateAntiForgeryToken` Filter und Filter, die `AutoValidateAntiforgeryToken` auf einer höheren Ebene (Global oder auf einem Controller) angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-285">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

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

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="ca7dd-286">Token nach der Authentifizierung aktualisieren</span><span class="sxs-lookup"><span data-stu-id="ca7dd-286">Refresh tokens after authentication</span></span>

<span data-ttu-id="ca7dd-287">Token sollten aktualisiert werden, nachdem der Benutzer authentifiziert wurde, indem er den Benutzer auf eine Seite oder eine Razor Seiten Seite umleitet.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-287">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="ca7dd-288">JavaScript, AJAX und Spas</span><span class="sxs-lookup"><span data-stu-id="ca7dd-288">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="ca7dd-289">In herkömmlichen HTML-basierten apps werden antifälschungstokentoken mithilfe von ausgeblendeten Formularfeldern an den Server übermittelt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-289">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="ca7dd-290">In modernen JavaScript-basierten apps und Spas werden viele Anforderungen Programm gesteuert durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-290">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="ca7dd-291">Diese AJAX-Anforderungen verwenden möglicherweise andere Techniken (z. b. Anforderungs Header oder Cookies), um das Token zu senden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-291">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="ca7dd-292">Wenn Cookies zum Speichern von Authentifizierungs Token und zum Authentifizieren von API-Anforderungen auf dem Server verwendet werden, ist CSRF ein potenzielles Problem.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-292">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="ca7dd-293">Wenn lokaler Speicher zum Speichern des Tokens verwendet wird, wird die CSRF-Sicherheits Anfälligkeit möglicherweise verringert, da Werte aus lokalem Speicher nicht automatisch mit jeder Anforderung an den Server gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-293">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="ca7dd-294">Daher ist die Verwendung von lokalem Speicher, um das antifälschungstoken auf dem Client zu speichern und das Token als Anforderungs Header zu senden, eine empfohlene Vorgehensweise.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-294">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="ca7dd-295">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ca7dd-295">JavaScript</span></span>

<span data-ttu-id="ca7dd-296">Mithilfe von JavaScript mit Sichten kann das Token mithilfe eines Diensts innerhalb der Sicht erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-296">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="ca7dd-297">Fügen Sie den Dienst " [Microsoft. aspnetcore. Antifälschung. iantifälschung](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) " in die Ansicht ein, und nennen Sie " [getandstoretokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens)":</span><span class="sxs-lookup"><span data-stu-id="ca7dd-297">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="ca7dd-298">Bei dieser Vorgehensweise entfällt die Notwendigkeit, direkt mit dem Festlegen von Cookies vom Server oder dem Lesen aus dem Client umzugehen.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-298">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="ca7dd-299">Im vorangehenden Beispiel wird JavaScript verwendet, um den Wert des ausgeblendeten Felds für den AJAX-Post-Header</span><span class="sxs-lookup"><span data-stu-id="ca7dd-299">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="ca7dd-300">JavaScript kann auch auf Token in Cookies zugreifen und den Inhalt des Cookies verwenden, um einen Header mit dem Wert des Tokens zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-300">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="ca7dd-301">Wenn das Skript anfordert, das Token in einem Header mit dem Namen zu senden `X-CSRF-TOKEN` , konfigurieren Sie den antifälschungs Dienst so, dass er nach dem `X-CSRF-TOKEN` Header sucht:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-301">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="ca7dd-302">Im folgenden Beispiel wird JavaScript verwendet, um eine AJAX-Anforderung mit dem entsprechenden Header zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-302">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

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

### <a name="angularjs"></a><span data-ttu-id="ca7dd-303">AngularJS</span><span class="sxs-lookup"><span data-stu-id="ca7dd-303">AngularJS</span></span>

<span data-ttu-id="ca7dd-304">Angularjs verwendet eine Konvention, um CSRF zu adressieren.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-304">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="ca7dd-305">Wenn der Server ein Cookie mit dem Namen sendet `XSRF-TOKEN` , fügt der angularjs- `$http` Dienst den cookiehwert einem Header hinzu, wenn er eine Anforderung an den Server sendet.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-305">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="ca7dd-306">Dieser Prozess erfolgt automatisch.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-306">This process is automatic.</span></span> <span data-ttu-id="ca7dd-307">Der-Header muss nicht explizit im Client festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-307">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="ca7dd-308">Der Header Name ist `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="ca7dd-308">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="ca7dd-309">Der Server sollte diesen Header erkennen und seinen Inhalt überprüfen.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-309">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="ca7dd-310">Damit ASP.net Core-API mit dieser Konvention beim Starten der Anwendung funktioniert:</span><span class="sxs-lookup"><span data-stu-id="ca7dd-310">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="ca7dd-311">Konfigurieren Sie Ihre APP so, dass Sie ein Token in einem Cookie namens bereitstellt `XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="ca7dd-311">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="ca7dd-312">Konfigurieren Sie den antifälschungs Dienst für die Suche nach einem Header mit dem Namen `X-XSRF-TOKEN` .</span><span class="sxs-lookup"><span data-stu-id="ca7dd-312">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

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

<span data-ttu-id="ca7dd-313">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca7dd-313">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="ca7dd-314">Antifälschung erweitern</span><span class="sxs-lookup"><span data-stu-id="ca7dd-314">Extend antiforgery</span></span>

<span data-ttu-id="ca7dd-315">Der [iantiforgeryadditionaldataprovider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) -Typ ermöglicht es Entwicklern, das Verhalten des Anti-CSRF-Systems zu erweitern, indem zusätzliche Daten in jedem Token abgerundet werden.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-315">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="ca7dd-316">Die [getadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) -Methode wird jedes Mal aufgerufen, wenn ein Feld Token generiert wird, und der Rückgabewert wird in das generierte Token eingebettet.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-316">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="ca7dd-317">Ein Implementierer könnte einen Zeitstempel, eine Nonce oder einen anderen Wert zurückgeben und dann [validateadditionaldata](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) aufzurufen, um diese Daten zu validieren, wenn das Token überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-317">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="ca7dd-318">Der Benutzername des Clients ist bereits in die generierten Token eingebettet, sodass es nicht erforderlich ist, diese Informationen einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-318">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="ca7dd-319">Wenn ein Token zusätzliche Daten enthält, aber keine `IAntiForgeryAdditionalDataProvider` konfiguriert ist, werden die ergänzenden Daten nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="ca7dd-319">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ca7dd-320">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ca7dd-320">Additional resources</span></span>

* <span data-ttu-id="ca7dd-321">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) für das [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="ca7dd-321">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
