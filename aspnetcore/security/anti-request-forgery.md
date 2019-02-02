---
title: Zu verhindern, dass Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core
author: steve-smith
description: Erfahren Sie, wie ein, um Angriffe auf Web-apps zu verhindern, in dem eine schädliche Website die Interaktion zwischen einem Clientbrowser und die app auswirken kann.
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: security/anti-request-forgery
ms.openlocfilehash: 6e140717834b901e12ef7863fd07b983b0c55107
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667660"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a><span data-ttu-id="de4bd-103">Zu verhindern, dass Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="de4bd-103">Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core</span></span>

<span data-ttu-id="de4bd-104">Durch [Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="de4bd-104">By [Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="de4bd-105">Websiteübergreifende anforderungsfälschung (auch bekannt als XSRF oder CSRF-Angriffen ausgesprochen *finden Sie unter ausspricht*) ist ein Angriff gegen gehostete Web-apps, die eine böswilligen Web-app kann bei dem die Interaktion zwischen einem Clientbrowser und einer Web-app, die vertraut, die beeinflussen Browser.</span><span class="sxs-lookup"><span data-stu-id="de4bd-105">Cross-site request forgery (also known as XSRF or CSRF, pronounced *see-surf*) is an attack against web-hosted apps whereby a malicious web app can influence the interaction between a client browser and a web app that trusts that browser.</span></span> <span data-ttu-id="de4bd-106">Diese Angriffe sind möglich, da Webbrowser einige Arten von Authentifizierungstoken automatisch mit jeder Anforderung an eine Website gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="de4bd-106">These attacks are possible because web browsers send some types of authentication tokens automatically with every request to a website.</span></span> <span data-ttu-id="de4bd-107">Diese Form der Exploit ist auch bekannt als eine *One-Click-Angriffs* oder *Sitzung Vorderkante* , da der Angriff nutzt die der Benutzer zuvor Sitzung authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="de4bd-107">This form of exploit is also known as a *one-click attack* or *session riding* because the attack takes advantage of the user's previously authenticated session.</span></span>

<span data-ttu-id="de4bd-108">Ein Beispiel eines CSRF-Angriffs:</span><span class="sxs-lookup"><span data-stu-id="de4bd-108">An example of a CSRF attack:</span></span>

1. <span data-ttu-id="de4bd-109">Ein Benutzer meldet sich in `www.good-banking-site.com` mit Formularauthentifizierung.</span><span class="sxs-lookup"><span data-stu-id="de4bd-109">A user signs into `www.good-banking-site.com` using forms authentication.</span></span> <span data-ttu-id="de4bd-110">Der Server authentifiziert den Benutzer und gibt eine Antwort, die ein Authentifizierungscookie enthält.</span><span class="sxs-lookup"><span data-stu-id="de4bd-110">The server authenticates the user and issues a response that includes an authentication cookie.</span></span> <span data-ttu-id="de4bd-111">Der Standort ist anfällig für Angriffe, da es sich um alle Anforderungen vertraut, die sie mit der ein gültiges Authentifizierungscookie erhält.</span><span class="sxs-lookup"><span data-stu-id="de4bd-111">The site is vulnerable to attack because it trusts any request that it receives with a valid authentication cookie.</span></span>
1. <span data-ttu-id="de4bd-112">Der Benutzer besucht eine schädliche Website, `www.bad-crook-site.com`.</span><span class="sxs-lookup"><span data-stu-id="de4bd-112">The user visits a malicious site, `www.bad-crook-site.com`.</span></span>

   <span data-ttu-id="de4bd-113">Die bösartige Website, `www.bad-crook-site.com`, enthält ein HTML-Formular, das dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="de4bd-113">The malicious site, `www.bad-crook-site.com`, contains an HTML form similar to the following:</span></span>

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   <span data-ttu-id="de4bd-114">Beachten Sie, dass des Formulars `action` Beiträge an anfälligen Website und nicht an die bösartige Website.</span><span class="sxs-lookup"><span data-stu-id="de4bd-114">Notice that the form's `action` posts to the vulnerable site, not to the malicious site.</span></span> <span data-ttu-id="de4bd-115">Dies ist der Teil von "Cross-Site" für CSRF.</span><span class="sxs-lookup"><span data-stu-id="de4bd-115">This is the "cross-site" part of CSRF.</span></span>

1. <span data-ttu-id="de4bd-116">Der Benutzer wählt die Schaltfläche "Senden".</span><span class="sxs-lookup"><span data-stu-id="de4bd-116">The user selects the submit button.</span></span> <span data-ttu-id="de4bd-117">Der Browser sendet die Anforderung und schließt automatisch das Authentifizierungscookie für die angeforderte Domäne `www.good-banking-site.com`.</span><span class="sxs-lookup"><span data-stu-id="de4bd-117">The browser makes the request and automatically includes the authentication cookie for the requested domain, `www.good-banking-site.com`.</span></span>
1. <span data-ttu-id="de4bd-118">Die Anforderung ausgeführt wird, auf die `www.good-banking-site.com` Server mit dem Kontext des Benutzers Authentifizierung und können Aktionen, die ein authentifizierter Benutzer ausführen darf.</span><span class="sxs-lookup"><span data-stu-id="de4bd-118">The request runs on the `www.good-banking-site.com` server with the user's authentication context and can perform any action that an authenticated user is allowed to perform.</span></span>

<span data-ttu-id="de4bd-119">Zusätzlich zu dem Szenario, in dem sich der Benutzer auf die Schaltfläche zum Senden des Formulars auswählt, können die bösartige Website:</span><span class="sxs-lookup"><span data-stu-id="de4bd-119">In addition to the scenario where the user selects the button to submit the form, the malicious site could:</span></span>

* <span data-ttu-id="de4bd-120">Führen Sie ein Skript, das automatisch auf das Formular übermittelt.</span><span class="sxs-lookup"><span data-stu-id="de4bd-120">Run a script that automatically submits the form.</span></span>
* <span data-ttu-id="de4bd-121">Senden Sie die Übermittlung des Formulars wird als eine AJAX-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="de4bd-121">Send the form submission as an AJAX request.</span></span>
* <span data-ttu-id="de4bd-122">Blenden Sie das Formular mit CSS.</span><span class="sxs-lookup"><span data-stu-id="de4bd-122">Hide the form using CSS.</span></span>

<span data-ttu-id="de4bd-123">Diese alternativen Szenarien erforderlich keine Aktion oder die Eingabe des Benutzers als zunächst auf die bösartige Website.</span><span class="sxs-lookup"><span data-stu-id="de4bd-123">These alternative scenarios don't require any action or input from the user other than initially visiting the malicious site.</span></span>

<span data-ttu-id="de4bd-124">Mithilfe von HTTPS nicht verhindert, dass eine CSRF-Angriffe.</span><span class="sxs-lookup"><span data-stu-id="de4bd-124">Using HTTPS doesn't prevent a CSRF attack.</span></span> <span data-ttu-id="de4bd-125">Kann die bösartige Website senden ein `https://www.good-banking-site.com/` Anforderung so einfach wie eine unsichere Anforderung senden kann.</span><span class="sxs-lookup"><span data-stu-id="de4bd-125">The malicious site can send an `https://www.good-banking-site.com/` request just as easily as it can send an insecure request.</span></span>

<span data-ttu-id="de4bd-126">Einige Angriffe Zielen Endpunkte, die auf den GET-Anforderungen, Antworten in diesem Fall ein Bildtag dienen zum Ausführen der Aktion.</span><span class="sxs-lookup"><span data-stu-id="de4bd-126">Some attacks target endpoints that respond to GET requests, in which case an image tag can be used to perform the action.</span></span> <span data-ttu-id="de4bd-127">Diese Form des Angriffs ist üblich, auf der Forum-Websites, die Bilder zulässig, aber Hiermit blockieren Sie JavaScript.</span><span class="sxs-lookup"><span data-stu-id="de4bd-127">This form of attack is common on forum sites that permit images but block JavaScript.</span></span> <span data-ttu-id="de4bd-128">Apps, die statusänderung zu GET-Anforderungen, bei dem Variablen oder Ressourcen geändert werden, sind anfällig für Angriffe.</span><span class="sxs-lookup"><span data-stu-id="de4bd-128">Apps that change state on GET requests, where variables or resources are altered, are vulnerable to malicious attacks.</span></span> <span data-ttu-id="de4bd-129">**GET-Anforderungen, die Status ändern, sind unsicher. Eine bewährte Methode besteht darin Zustand für eine GET-Anforderung nie zu ändern.**</span><span class="sxs-lookup"><span data-stu-id="de4bd-129">**GET requests that change state are insecure. A best practice is to never change state on a GET request.**</span></span>

<span data-ttu-id="de4bd-130">CSRF-Angriffe sind für Web-apps, die Cookies für die Authentifizierung zu verwenden, da möglich:</span><span class="sxs-lookup"><span data-stu-id="de4bd-130">CSRF attacks are possible against web apps that use cookies for authentication because:</span></span>

* <span data-ttu-id="de4bd-131">Speichern Browser die Cookies, die von einer Web-app ausgestellt.</span><span class="sxs-lookup"><span data-stu-id="de4bd-131">Browsers store cookies issued by a web app.</span></span>
* <span data-ttu-id="de4bd-132">Gespeicherten Cookies enthalten die Sitzungscookies für authentifizierte Benutzer.</span><span class="sxs-lookup"><span data-stu-id="de4bd-132">Stored cookies include session cookies for authenticated users.</span></span>
* <span data-ttu-id="de4bd-133">Browser senden, dass alle Cookies mit einer Domäne zur Web-app verknüpft ist jede Anforderung unabhängig davon, wie die Anforderung an die app im Browser generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="de4bd-133">Browsers send all of the cookies associated with a domain to the web app every request regardless of how the request to app was generated within the browser.</span></span>

<span data-ttu-id="de4bd-134">Allerdings CSRF-Angriffe sind nicht beschränkt auf Cookies ausnutzen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-134">However, CSRF attacks aren't limited to exploiting cookies.</span></span> <span data-ttu-id="de4bd-135">Z. B. Basis-und Digestauthentifizierung sind auch anfällig.</span><span class="sxs-lookup"><span data-stu-id="de4bd-135">For example, Basic and Digest authentication are also vulnerable.</span></span> <span data-ttu-id="de4bd-136">Nachdem ein Benutzer mit der Standard oder Digest-Authentifizierung anmeldet, sendet der Browser die Anmeldeinformationen automatisch bis die Sitzung&dagger; endet.</span><span class="sxs-lookup"><span data-stu-id="de4bd-136">After a user signs in with Basic or Digest authentication, the browser automatically sends the credentials until the session&dagger; ends.</span></span>

<span data-ttu-id="de4bd-137">&dagger;In diesem Kontext *Sitzung* bezieht sich auf die clientseitige Sitzung während der der Benutzer wird authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="de4bd-137">&dagger;In this context, *session* refers to the client-side session during which the user is authenticated.</span></span> <span data-ttu-id="de4bd-138">Es ist, die nicht mit einem serverseitigen Sitzungen oder [ASP.NET Core-Sitzungsmiddleware](xref:fundamentals/app-state).</span><span class="sxs-lookup"><span data-stu-id="de4bd-138">It's unrelated to server-side sessions or [ASP.NET Core Session Middleware](xref:fundamentals/app-state).</span></span>

<span data-ttu-id="de4bd-139">Benutzer können sich gegen CSRF-Schwachstellen durch Vorsichtsmaßnahmen schützen:</span><span class="sxs-lookup"><span data-stu-id="de4bd-139">Users can guard against CSRF vulnerabilities by taking precautions:</span></span>

* <span data-ttu-id="de4bd-140">Melden Sie sich auf die Web-apps nach deren Verwendung.</span><span class="sxs-lookup"><span data-stu-id="de4bd-140">Sign off of web apps when finished using them.</span></span>
* <span data-ttu-id="de4bd-141">Deaktivieren der Browsercookies in regelmäßigen Abständen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-141">Clear browser cookies periodically.</span></span>

<span data-ttu-id="de4bd-142">CSRF-Sicherheitsrisiken sind jedoch im Grunde ein Problem mit der Web-app nicht der Endbenutzer.</span><span class="sxs-lookup"><span data-stu-id="de4bd-142">However, CSRF vulnerabilities are fundamentally a problem with the web app, not the end user.</span></span>

## <a name="authentication-fundamentals"></a><span data-ttu-id="de4bd-143">Grundlagen der Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="de4bd-143">Authentication fundamentals</span></span>

<span data-ttu-id="de4bd-144">Cookie-basierte Authentifizierung ist eine gängige Form der Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="de4bd-144">Cookie-based authentication is a popular form of authentication.</span></span> <span data-ttu-id="de4bd-145">Tokenbasierte Authentifizierungssysteme wachsen Beliebtheit, insbesondere für Single Page Applications (SPAs).</span><span class="sxs-lookup"><span data-stu-id="de4bd-145">Token-based authentication systems are growing in popularity, especially for Single Page Applications (SPAs).</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="de4bd-146">Cookie-basierte Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="de4bd-146">Cookie-based authentication</span></span>

<span data-ttu-id="de4bd-147">Wenn ein Benutzer authentifiziert, mit ihrem Benutzernamen und Kennwort, sind sie ausgestellt ein Token, mit der ein Authentifizierungsticket, die für die Authentifizierung und Autorisierung verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="de4bd-147">When a user authenticates using their username and password, they're issued a token, containing an authentication ticket that can be used for authentication and authorization.</span></span> <span data-ttu-id="de4bd-148">Das Token wird gespeichert, da ein Cookie an, die mit jeder Anforderung des Clients ist.</span><span class="sxs-lookup"><span data-stu-id="de4bd-148">The token is stored as a cookie that accompanies every request the client makes.</span></span> <span data-ttu-id="de4bd-149">Erstellen und überprüfen dieses Cookie wird durch die Cookieauthentifizierungs-Middleware ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="de4bd-149">Generating and validating this cookie is performed by the Cookie Authentication Middleware.</span></span> <span data-ttu-id="de4bd-150">Die [Middleware](xref:fundamentals/middleware/index) einen Benutzerprinzipal in einem verschlüsselten Cookie serialisiert.</span><span class="sxs-lookup"><span data-stu-id="de4bd-150">The [middleware](xref:fundamentals/middleware/index) serializes a user principal into an encrypted cookie.</span></span> <span data-ttu-id="de4bd-151">Bei nachfolgenden Anforderungen, die Middleware überprüft das Cookie, erstellt den Prinzipal aus, und weist den Prinzipal, der [Benutzer](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) Eigenschaft ["HttpContext"](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span><span class="sxs-lookup"><span data-stu-id="de4bd-151">On subsequent requests, the middleware validates the cookie, recreates the principal, and assigns the principal to the [User](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) property of [HttpContext](/dotnet/api/microsoft.aspnetcore.http.httpcontext).</span></span>

### <a name="token-based-authentication"></a><span data-ttu-id="de4bd-152">Tokenbasierte Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="de4bd-152">Token-based authentication</span></span>

<span data-ttu-id="de4bd-153">Wenn ein Benutzer authentifiziert ist, können sie ein Token (kein Antifälschungstoken) ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="de4bd-153">When a user is authenticated, they're issued a token (not an antiforgery token).</span></span> <span data-ttu-id="de4bd-154">Das Token enthält Benutzerinformationen in Form von [Ansprüche](/dotnet/framework/security/claims-based-identity-model) oder ein Verweistoken, die die app auf Benutzerstatus, die in der app verwaltet verweist.</span><span class="sxs-lookup"><span data-stu-id="de4bd-154">The token contains user information in the form of [claims](/dotnet/framework/security/claims-based-identity-model) or a reference token that points the app to user state maintained in the app.</span></span> <span data-ttu-id="de4bd-155">Wenn ein Benutzer versucht, die Zugriff auf eine Ressource, die eine Authentifizierung erforderlich, wird das Token an die app mit einer zusätzlichen Authorization-Header in Form von Trägertoken, das gesendet.</span><span class="sxs-lookup"><span data-stu-id="de4bd-155">When a user attempts to access a resource requiring authentication, the token is sent to the app with an additional authorization header in form of Bearer token.</span></span> <span data-ttu-id="de4bd-156">Dadurch wird die app zustandslos.</span><span class="sxs-lookup"><span data-stu-id="de4bd-156">This makes the app stateless.</span></span> <span data-ttu-id="de4bd-157">In jeder nachfolgenden Anforderung wird das Token für die serverseitige Validierung in der Anforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="de4bd-157">In each subsequent request, the token is passed in the request for server-side validation.</span></span> <span data-ttu-id="de4bd-158">Dieses Token ist nicht *verschlüsselte*; es *codiert*.</span><span class="sxs-lookup"><span data-stu-id="de4bd-158">This token isn't *encrypted*; it's *encoded*.</span></span> <span data-ttu-id="de4bd-159">Auf dem Server wird das Token decodiert, für den Zugriff auf ihre Informationen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-159">On the server, the token is decoded to access its information.</span></span> <span data-ttu-id="de4bd-160">Um das Token bei nachfolgenden Anforderungen zu senden, müssen Sie das Token im lokalen Speicher des Browsers gespeichert.</span><span class="sxs-lookup"><span data-stu-id="de4bd-160">To send the token on subsequent requests, store the token in the browser's local storage.</span></span> <span data-ttu-id="de4bd-161">Werden Sie keine Bedenken bezüglich eines CSRF-Sicherheitsrisiko, wenn das Token im lokalen Speicher des Browsers gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="de4bd-161">Don't be concerned about CSRF vulnerability if the token is stored in the browser's local storage.</span></span> <span data-ttu-id="de4bd-162">CSRF ist ein Problem auf, wenn das Token in einem Cookie gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="de4bd-162">CSRF is a concern when the token is stored in a cookie.</span></span>

### <a name="multiple-apps-hosted-at-one-domain"></a><span data-ttu-id="de4bd-163">Mehrere apps, die in einer Domäne gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="de4bd-163">Multiple apps hosted at one domain</span></span>

<span data-ttu-id="de4bd-164">Freigegebene Hostingumgebungen sind anfällig für Session Hijacking, CSRF-Anmeldung und andere Angriffe.</span><span class="sxs-lookup"><span data-stu-id="de4bd-164">Shared hosting environments are vulnerable to session hijacking, login CSRF, and other attacks.</span></span>

<span data-ttu-id="de4bd-165">Obwohl `example1.contoso.net` und `example2.contoso.net` sind verschiedene Hosts besteht ein implizites Vertrauensverhältnis zwischen Hosts in der `*.contoso.net` Domäne.</span><span class="sxs-lookup"><span data-stu-id="de4bd-165">Although `example1.contoso.net` and `example2.contoso.net` are different hosts, there's an implicit trust relationship between hosts under the `*.contoso.net` domain.</span></span> <span data-ttu-id="de4bd-166">Diese implizite Vertrauensstellung kann potenziell nicht vertrauenswürdige Hosts die Cookies des jeweils anderen auswirken (die gleichen Ursprungs-Richtlinien, die steuern, AJAX-Anforderungen unbedingt gelten nicht für HTTP-Cookies).</span><span class="sxs-lookup"><span data-stu-id="de4bd-166">This implicit trust relationship allows potentially untrusted hosts to affect each other's cookies (the same-origin policies that govern AJAX requests don't necessarily apply to HTTP cookies).</span></span>

<span data-ttu-id="de4bd-167">Angriffe, die vertrauenswürdige Cookies zwischen apps, die in der gleichen Domäne gehostet ausnutzen können verhindert werden, durch die Domänen nicht freigegeben wird.</span><span class="sxs-lookup"><span data-stu-id="de4bd-167">Attacks that exploit trusted cookies between apps hosted on the same domain can be prevented by not sharing domains.</span></span> <span data-ttu-id="de4bd-168">Wenn jede app in einer eigenen Domäne gehostet wird, besteht keine Vertrauensstellung für implizite Cookie ausnutzen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-168">When each app is hosted on its own domain, there is no implicit cookie trust relationship to exploit.</span></span>

## <a name="aspnet-core-antiforgery-configuration"></a><span data-ttu-id="de4bd-169">ASP.NET Core-antiforgery-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="de4bd-169">ASP.NET Core antiforgery configuration</span></span>

> [!WARNING]
> <span data-ttu-id="de4bd-170">ASP.NET Core implementiert antiforgery mit [ASP.NET Core-Datenschutz](xref:security/data-protection/introduction).</span><span class="sxs-lookup"><span data-stu-id="de4bd-170">ASP.NET Core implements antiforgery using [ASP.NET Core Data Protection](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="de4bd-171">Stapel für den Schutz von Daten muss konfiguriert werden, um in einer Serverfarm zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="de4bd-171">The data protection stack must be configured to work in a server farm.</span></span> <span data-ttu-id="de4bd-172">Finden Sie unter [konfigurieren Sie den Schutz von Daten von](xref:security/data-protection/configuration/overview) für Weitere Informationen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-172">See [Configuring data protection](xref:security/data-protection/configuration/overview) for more information.</span></span>

<span data-ttu-id="de4bd-173">In ASP.NET Core 2.0 oder höher der [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) fälschungssicherheitstoken in HTML-Formularelemente einfügt.</span><span class="sxs-lookup"><span data-stu-id="de4bd-173">In ASP.NET Core 2.0 or later, the [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injects antiforgery tokens into HTML form elements.</span></span> <span data-ttu-id="de4bd-174">Das folgende Markup in einer Razor-Datei wird automatisch fälschungssicherheitstoken generiert:</span><span class="sxs-lookup"><span data-stu-id="de4bd-174">The following markup in a Razor file automatically generates antiforgery tokens:</span></span>

```cshtml
<form method="post">
    ...
</form>
```

<span data-ttu-id="de4bd-175">Entsprechend, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) fälschungssicherheitstoken standardmäßig generiert, wenn die-Methode des Formulars GET nicht ist.</span><span class="sxs-lookup"><span data-stu-id="de4bd-175">Similarily, [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) generates antiforgery tokens by default if the form's method isn't GET.</span></span>

<span data-ttu-id="de4bd-176">Erfolgt die automatische Generierung von fälschungssicherheitstoken für HTML-Formularelemente bei der `<form>` -Tag enthält den `method="post"` -Attribut und eine der folgenden erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="de4bd-176">The automatic generation of antiforgery tokens for HTML form elements happens when the `<form>` tag contains the `method="post"` attribute and either of the following are true:</span></span>

  * <span data-ttu-id="de4bd-177">Das Action-Attribut ist leer (`action=""`).</span><span class="sxs-lookup"><span data-stu-id="de4bd-177">The action attribute is empty (`action=""`).</span></span>
  * <span data-ttu-id="de4bd-178">Das Action-Attribut ist nicht angegeben (`<form method="post">`).</span><span class="sxs-lookup"><span data-stu-id="de4bd-178">The action attribute isn't supplied (`<form method="post">`).</span></span>

<span data-ttu-id="de4bd-179">Automatische Generierung von fälschungssicherheitstoken für HTML-Formularelemente kann deaktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="de4bd-179">Automatic generation of antiforgery tokens for HTML form elements can be disabled:</span></span>

* <span data-ttu-id="de4bd-180">Deaktivieren Sie explizit die antiforgery Token mit den `asp-antiforgery` Attribut:</span><span class="sxs-lookup"><span data-stu-id="de4bd-180">Explicitly disable antiforgery tokens with the `asp-antiforgery` attribute:</span></span>

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* <span data-ttu-id="de4bd-181">Das Formularelement ist entschieden-Out-of Taghilfsprogramme mit dem Taghilfsprogramm [! melden Sie sich Symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span><span class="sxs-lookup"><span data-stu-id="de4bd-181">The form element is opted-out of Tag Helpers by using the Tag Helper [! opt-out symbol](xref:mvc/views/tag-helpers/intro#opt-out):</span></span>

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* <span data-ttu-id="de4bd-182">Entfernen Sie die `FormTagHelper` aus der Sicht.</span><span class="sxs-lookup"><span data-stu-id="de4bd-182">Remove the `FormTagHelper` from the view.</span></span> <span data-ttu-id="de4bd-183">Die `FormTagHelper` können aus einer Sicht entfernt werden, indem Sie die Razor-Ansicht die folgende Anweisung hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="de4bd-183">The `FormTagHelper` can be removed from a view by adding the following directive to the Razor view:</span></span>

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> <span data-ttu-id="de4bd-184">[Razor-Seiten](xref:razor-pages/index) vor XSRF/CSRF automatisch geschützt sind.</span><span class="sxs-lookup"><span data-stu-id="de4bd-184">[Razor Pages](xref:razor-pages/index) are automatically protected from XSRF/CSRF.</span></span> <span data-ttu-id="de4bd-185">Weitere Informationen finden Sie unter [XSRF/CSRF und Razor-Seiten](xref:razor-pages/index#xsrf).</span><span class="sxs-lookup"><span data-stu-id="de4bd-185">For more information, see [XSRF/CSRF and Razor Pages](xref:razor-pages/index#xsrf).</span></span>

<span data-ttu-id="de4bd-186">Die am häufigsten verwendete Ansatz für die Verteidigung gegen CSRF-Angriffe ist die Verwendung der *für die Domänensynchronisierung Tokenmuster* (STP).</span><span class="sxs-lookup"><span data-stu-id="de4bd-186">The most common approach to defending against CSRF attacks is to use the *Synchronizer Token Pattern* (STP).</span></span> <span data-ttu-id="de4bd-187">STP wird verwendet, wenn der Benutzer eine Seite mit den Formulardaten anfordert:</span><span class="sxs-lookup"><span data-stu-id="de4bd-187">STP is used when the user requests a page with form data:</span></span>

1. <span data-ttu-id="de4bd-188">Der Server sendet ein Token, die denen des aktuellen Benutzers Identität an den Client zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="de4bd-188">The server sends a token associated with the current user's identity to the client.</span></span>
1. <span data-ttu-id="de4bd-189">Der Client sendet das Token zurück an den Server für die Überprüfung.</span><span class="sxs-lookup"><span data-stu-id="de4bd-189">The client sends back the token to the server for verification.</span></span>
1. <span data-ttu-id="de4bd-190">Wenn der Server ein Token, die Identität des authentifizierten Benutzers entsprechen nicht erhält, wird die Anforderung zurückgewiesen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-190">If the server receives a token that doesn't match the authenticated user's identity, the request is rejected.</span></span>

<span data-ttu-id="de4bd-191">Das Token ist eindeutig und nicht vorhersagbar.</span><span class="sxs-lookup"><span data-stu-id="de4bd-191">The token is unique and unpredictable.</span></span> <span data-ttu-id="de4bd-192">Das Token kann auch verwendet werden, um sicherzustellen, dass ordnungsgemäße Sequenzierung einer Reihe von Anforderungen (z. B. Sicherstellen der anforderungssequenz des: Seite 1 &ndash; Seite 2 &ndash; Seite 3).</span><span class="sxs-lookup"><span data-stu-id="de4bd-192">The token can also be used to ensure proper sequencing of a series of requests (for example, ensuring the request sequence of: page 1 &ndash; page 2 &ndash; page 3).</span></span> <span data-ttu-id="de4bd-193">Alle Formulare in ASP.NET Core MVC und Razor Pages-Vorlagen generiert fälschungssicherheitstoken.</span><span class="sxs-lookup"><span data-stu-id="de4bd-193">All of the forms in ASP.NET Core MVC and Razor Pages templates generate antiforgery tokens.</span></span> <span data-ttu-id="de4bd-194">Die folgenden zwei Beispiele anzeigen generieren fälschungssicherheitstoken:</span><span class="sxs-lookup"><span data-stu-id="de4bd-194">The following pair of view examples generate antiforgery tokens:</span></span>

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

<span data-ttu-id="de4bd-195">Explizit hinzufügen ein Antifälschungstoken an einen `<form>` -Element ohne das HTML-Hilfsobjekt mit Taghilfsprogrammen [ @Html.AntiForgeryToken ](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span><span class="sxs-lookup"><span data-stu-id="de4bd-195">Explicitly add an antiforgery token to a `<form>` element without using Tag Helpers with the HTML helper [@Html.AntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):</span></span>

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

<span data-ttu-id="de4bd-196">In allen genannten Fällen fügt ASP.NET Core ein ausgeblendetes Formularfeld, die etwa wie folgt hinzu:</span><span class="sxs-lookup"><span data-stu-id="de4bd-196">In each of the preceding cases, ASP.NET Core adds a hidden form field similar to the following:</span></span>

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

<span data-ttu-id="de4bd-197">ASP.NET Core enthält drei [Filter](xref:mvc/controllers/filters) für die Verwendung von fälschungssicherheitstoken:</span><span class="sxs-lookup"><span data-stu-id="de4bd-197">ASP.NET Core includes three [filters](xref:mvc/controllers/filters) for working with antiforgery tokens:</span></span>

* [<span data-ttu-id="de4bd-198">ValidateAntiForgeryToken</span><span class="sxs-lookup"><span data-stu-id="de4bd-198">ValidateAntiForgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [<span data-ttu-id="de4bd-199">AutoValidateAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="de4bd-199">AutoValidateAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [<span data-ttu-id="de4bd-200">IgnoreAntiforgeryToken</span><span class="sxs-lookup"><span data-stu-id="de4bd-200">IgnoreAntiforgeryToken</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a><span data-ttu-id="de4bd-201">Antiforgery-Optionen</span><span class="sxs-lookup"><span data-stu-id="de4bd-201">Antiforgery options</span></span>

<span data-ttu-id="de4bd-202">Anpassen von [antiforgery Optionen](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="de4bd-202">Customize [antiforgery options](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="de4bd-203">&dagger;Legen Sie die antifälschungsunterstützung `Cookie` Eigenschaften, die mithilfe der Eigenschaften der [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) Klasse.</span><span class="sxs-lookup"><span data-stu-id="de4bd-203">&dagger;Set the antiforgery `Cookie` properties using the properties of the [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) class.</span></span>

| <span data-ttu-id="de4bd-204">Option</span><span class="sxs-lookup"><span data-stu-id="de4bd-204">Option</span></span> | <span data-ttu-id="de4bd-205">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="de4bd-205">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="de4bd-206">Cookie</span><span class="sxs-lookup"><span data-stu-id="de4bd-206">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="de4bd-207">Bestimmt die Einstellungen verwendet, um die antiforgery Cookies zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-207">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="de4bd-208">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="de4bd-208">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="de4bd-209">Der Name des ausgeblendeten Formularfelds mit denen vom System antiforgery fälschungssicherheitstoken in Ansichten gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="de4bd-209">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="de4bd-210">HeaderName</span><span class="sxs-lookup"><span data-stu-id="de4bd-210">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="de4bd-211">Der Name des Headers der antiforgery-System verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="de4bd-211">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="de4bd-212">Wenn `null`, das System berücksichtigt nur die Formulardaten.</span><span class="sxs-lookup"><span data-stu-id="de4bd-212">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="de4bd-213">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="de4bd-213">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="de4bd-214">Gibt an, ob die Erstellung von Unterdrücken der `X-Frame-Options` Header.</span><span class="sxs-lookup"><span data-stu-id="de4bd-214">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="de4bd-215">Standardmäßig wird der Header mit einem Wert von "SAMEORIGIN" generiert.</span><span class="sxs-lookup"><span data-stu-id="de4bd-215">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="de4bd-216">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="de4bd-216">Defaults to `false`.</span></span> |

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

| <span data-ttu-id="de4bd-217">Option</span><span class="sxs-lookup"><span data-stu-id="de4bd-217">Option</span></span> | <span data-ttu-id="de4bd-218">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="de4bd-218">Description</span></span> |
| ------ | ----------- |
| [<span data-ttu-id="de4bd-219">Cookie</span><span class="sxs-lookup"><span data-stu-id="de4bd-219">Cookie</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | <span data-ttu-id="de4bd-220">Bestimmt die Einstellungen verwendet, um die antiforgery Cookies zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-220">Determines the settings used to create the antiforgery cookies.</span></span> |
| [<span data-ttu-id="de4bd-221">CookieDomain</span><span class="sxs-lookup"><span data-stu-id="de4bd-221">CookieDomain</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | <span data-ttu-id="de4bd-222">Die Domäne des Cookies.</span><span class="sxs-lookup"><span data-stu-id="de4bd-222">The domain of the cookie.</span></span> <span data-ttu-id="de4bd-223">Wird standardmäßig auf `null` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="de4bd-223">Defaults to `null`.</span></span> <span data-ttu-id="de4bd-224">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="de4bd-224">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="de4bd-225">Die empfohlene Alternative ist die Cookie.Domain.</span><span class="sxs-lookup"><span data-stu-id="de4bd-225">The recommended alternative is Cookie.Domain.</span></span> |
| [<span data-ttu-id="de4bd-226">CookieName</span><span class="sxs-lookup"><span data-stu-id="de4bd-226">CookieName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | <span data-ttu-id="de4bd-227">Der Name des Cookies.</span><span class="sxs-lookup"><span data-stu-id="de4bd-227">The name of the cookie.</span></span> <span data-ttu-id="de4bd-228">Wenn nicht festgelegt ist, generiert das System einen eindeutigen Namen ab, mit der [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore.Antiforgery.").</span><span class="sxs-lookup"><span data-stu-id="de4bd-228">If not set, the system generates a unique name beginning with the [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (".AspNetCore.Antiforgery.").</span></span> <span data-ttu-id="de4bd-229">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="de4bd-229">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="de4bd-230">Die empfohlene Alternative ist die Cookie.Name.</span><span class="sxs-lookup"><span data-stu-id="de4bd-230">The recommended alternative is Cookie.Name.</span></span> |
| [<span data-ttu-id="de4bd-231">CookiePath</span><span class="sxs-lookup"><span data-stu-id="de4bd-231">CookiePath</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | <span data-ttu-id="de4bd-232">Der Pfad für das Cookie festgelegt.</span><span class="sxs-lookup"><span data-stu-id="de4bd-232">The path set on the cookie.</span></span> <span data-ttu-id="de4bd-233">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="de4bd-233">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="de4bd-234">Die empfohlene Alternative ist die Cookie.Path.</span><span class="sxs-lookup"><span data-stu-id="de4bd-234">The recommended alternative is Cookie.Path.</span></span> |
| [<span data-ttu-id="de4bd-235">FormFieldName</span><span class="sxs-lookup"><span data-stu-id="de4bd-235">FormFieldName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | <span data-ttu-id="de4bd-236">Der Name des ausgeblendeten Formularfelds mit denen vom System antiforgery fälschungssicherheitstoken in Ansichten gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="de4bd-236">The name of the hidden form field used by the antiforgery system to render antiforgery tokens in views.</span></span> |
| [<span data-ttu-id="de4bd-237">HeaderName</span><span class="sxs-lookup"><span data-stu-id="de4bd-237">HeaderName</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | <span data-ttu-id="de4bd-238">Der Name des Headers der antiforgery-System verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="de4bd-238">The name of the header used by the antiforgery system.</span></span> <span data-ttu-id="de4bd-239">Wenn `null`, das System berücksichtigt nur die Formulardaten.</span><span class="sxs-lookup"><span data-stu-id="de4bd-239">If `null`, the system considers only form data.</span></span> |
| [<span data-ttu-id="de4bd-240">RequireSsl</span><span class="sxs-lookup"><span data-stu-id="de4bd-240">RequireSsl</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | <span data-ttu-id="de4bd-241">Gibt an, ob das System antiforgery HTTPS erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="de4bd-241">Specifies whether HTTPS is required by the antiforgery system.</span></span> <span data-ttu-id="de4bd-242">Wenn `true`, nicht-HTTPS-Anforderungen schlagen fehl.</span><span class="sxs-lookup"><span data-stu-id="de4bd-242">If `true`, non-HTTPS requests fail.</span></span> <span data-ttu-id="de4bd-243">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="de4bd-243">Defaults to `false`.</span></span> <span data-ttu-id="de4bd-244">Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt.</span><span class="sxs-lookup"><span data-stu-id="de4bd-244">This property is obsolete and will be removed in a future version.</span></span> <span data-ttu-id="de4bd-245">Die empfohlene Alternative ist Cookie.SecurePolicy festlegen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-245">The recommended alternative is to set Cookie.SecurePolicy.</span></span> |
| [<span data-ttu-id="de4bd-246">SuppressXFrameOptionsHeader</span><span class="sxs-lookup"><span data-stu-id="de4bd-246">SuppressXFrameOptionsHeader</span></span>](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | <span data-ttu-id="de4bd-247">Gibt an, ob die Erstellung von Unterdrücken der `X-Frame-Options` Header.</span><span class="sxs-lookup"><span data-stu-id="de4bd-247">Specifies whether to suppress generation of the `X-Frame-Options` header.</span></span> <span data-ttu-id="de4bd-248">Standardmäßig wird der Header mit einem Wert von "SAMEORIGIN" generiert.</span><span class="sxs-lookup"><span data-stu-id="de4bd-248">By default, the header is generated with a value of "SAMEORIGIN".</span></span> <span data-ttu-id="de4bd-249">Wird standardmäßig auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="de4bd-249">Defaults to `false`.</span></span> |

::: moniker-end

<span data-ttu-id="de4bd-250">Weitere Informationen finden Sie unter [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span><span class="sxs-lookup"><span data-stu-id="de4bd-250">For more information, see [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).</span></span>

## <a name="configure-antiforgery-features-with-iantiforgery"></a><span data-ttu-id="de4bd-251">Konfigurieren antiforgery-Features mit IAntiforgery</span><span class="sxs-lookup"><span data-stu-id="de4bd-251">Configure antiforgery features with IAntiforgery</span></span>

<span data-ttu-id="de4bd-252">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) stellt die API zum Konfigurieren der antiforgery Features bereit.</span><span class="sxs-lookup"><span data-stu-id="de4bd-252">[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) provides the API to configure antiforgery features.</span></span> <span data-ttu-id="de4bd-253">`IAntiforgery` können angefordert werden, der `Configure` Methode der `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="de4bd-253">`IAntiforgery` can be requested in the `Configure` method of the `Startup` class.</span></span> <span data-ttu-id="de4bd-254">Im folgenden Beispiel wird die Middleware auf der Startseite der app eine antiforgery Token generieren und senden sie in der Antwort als Cookie (mithilfe von die standardmäßige Angular Benennungskonvention weiter unten in diesem Thema beschrieben):</span><span class="sxs-lookup"><span data-stu-id="de4bd-254">The following example uses middleware from the app's home page to generate an antiforgery token and send it in the response as a cookie (using the default Angular naming convention described later in this topic):</span></span>

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

### <a name="require-antiforgery-validation"></a><span data-ttu-id="de4bd-255">Antiforgery-Überprüfung</span><span class="sxs-lookup"><span data-stu-id="de4bd-255">Require antiforgery validation</span></span>

<span data-ttu-id="de4bd-256">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) ist ein Aktionsfilter, die mit einer einzelnen Aktion, einen Controller angewendet werden kann oder Global.</span><span class="sxs-lookup"><span data-stu-id="de4bd-256">[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) is an action filter that can be applied to an individual action, a controller, or globally.</span></span> <span data-ttu-id="de4bd-257">Anforderungen an Aktionen, die dieser Filter angewendet haben werden blockiert, es sei denn, der die Anforderung ein gültiges antiforgery Token enthält.</span><span class="sxs-lookup"><span data-stu-id="de4bd-257">Requests made to actions that have this filter applied are blocked unless the request includes a valid antiforgery token.</span></span>

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

<span data-ttu-id="de4bd-258">Die `ValidateAntiForgeryToken` Attribut erfordert ein Token für Anforderungen an die Aktionsmethoden, die es ausstattet, einschließlich HTTP GET-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-258">The `ValidateAntiForgeryToken` attribute requires a token for requests to the action methods it decorates, including HTTP GET requests.</span></span> <span data-ttu-id="de4bd-259">Wenn die `ValidateAntiForgeryToken` Attribut angewendet wird, auf der app Controller, sie kann überschrieben werden, mit der `IgnoreAntiforgeryToken` Attribut.</span><span class="sxs-lookup"><span data-stu-id="de4bd-259">If the `ValidateAntiForgeryToken` attribute is applied across the app's controllers, it can be overridden with the `IgnoreAntiforgeryToken` attribute.</span></span>

> [!NOTE]
> <span data-ttu-id="de4bd-260">ASP.NET Core unterstützt keine GET-Anforderungen automatisch antiforgery Token hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="de4bd-260">ASP.NET Core doesn't support adding antiforgery tokens to GET requests automatically.</span></span>

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a><span data-ttu-id="de4bd-261">Überprüfen Sie fälschungssicherheitstoken für unsichere HTTP-Methoden nur automatisch</span><span class="sxs-lookup"><span data-stu-id="de4bd-261">Automatically validate antiforgery tokens for unsafe HTTP methods only</span></span>

<span data-ttu-id="de4bd-262">ASP.NET Core-apps erstellen keine fälschungssicherheitstoken für sichere HTTP-Methoden (GET, HEAD, Optionen und ABLAUFVERFOLGUNG).</span><span class="sxs-lookup"><span data-stu-id="de4bd-262">ASP.NET Core apps don't generate antiforgery tokens for safe HTTP methods (GET, HEAD, OPTIONS, and TRACE).</span></span> <span data-ttu-id="de4bd-263">Anstelle von Allgemein Anwenden der `ValidateAntiForgeryToken` -Attribut, und überschreiben Sie dann mit `IgnoreAntiforgeryToken` Attribute, die [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) Attribut kann verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="de4bd-263">Instead of broadly applying the `ValidateAntiForgeryToken` attribute and then overriding it with `IgnoreAntiforgeryToken` attributes, the [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) attribute can be used.</span></span> <span data-ttu-id="de4bd-264">Dieses Attribut funktioniert genauso wie die `ValidateAntiForgeryToken` Attribut, außer dass es keine Token für Anforderungen mit den folgenden HTTP-Methoden erfordert:</span><span class="sxs-lookup"><span data-stu-id="de4bd-264">This attribute works identically to the `ValidateAntiForgeryToken` attribute, except that it doesn't require tokens for requests made using the following HTTP methods:</span></span>

* <span data-ttu-id="de4bd-265">GET</span><span class="sxs-lookup"><span data-stu-id="de4bd-265">GET</span></span>
* <span data-ttu-id="de4bd-266">HEAD</span><span class="sxs-lookup"><span data-stu-id="de4bd-266">HEAD</span></span>
* <span data-ttu-id="de4bd-267">OPTIONEN</span><span class="sxs-lookup"><span data-stu-id="de4bd-267">OPTIONS</span></span>
* <span data-ttu-id="de4bd-268">TRACE</span><span class="sxs-lookup"><span data-stu-id="de4bd-268">TRACE</span></span>

<span data-ttu-id="de4bd-269">Wir empfehlen die Verwendung von `AutoValidateAntiforgeryToken` Allgemein für nicht-API-Szenarien.</span><span class="sxs-lookup"><span data-stu-id="de4bd-269">We recommend use of `AutoValidateAntiforgeryToken` broadly for non-API scenarios.</span></span> <span data-ttu-id="de4bd-270">Dadurch wird sichergestellt, dass standardmäßig die POST-Aktionen geschützt sind.</span><span class="sxs-lookup"><span data-stu-id="de4bd-270">This ensures POST actions are protected by default.</span></span> <span data-ttu-id="de4bd-271">Die Alternative ist standardmäßig fälschungssicherheitstoken ignoriert werden sollen, es sei denn, `ValidateAntiForgeryToken` auf einzelne Aktionsmethoden angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="de4bd-271">The alternative is to ignore antiforgery tokens by default, unless `ValidateAntiForgeryToken` is applied to individual action methods.</span></span> <span data-ttu-id="de4bd-272">Verlassen die app anfällig für CSRF-Angriffe ungeschützten versehen, eher er in diesem Szenario für eine POST-Aktion-Methode, die verbleiben.</span><span class="sxs-lookup"><span data-stu-id="de4bd-272">It's more likely in this scenario for a POST action method to be left unprotected by mistake, leaving the app vulnerable to CSRF attacks.</span></span> <span data-ttu-id="de4bd-273">Alle Beiträge, sollte das Antifälschungstoken senden.</span><span class="sxs-lookup"><span data-stu-id="de4bd-273">All POSTs should send the antiforgery token.</span></span>

<span data-ttu-id="de4bd-274">APIs verfügen nicht über einen automatischen Mechanismus zum Senden des nicht-Cookie-Teils des Tokens.</span><span class="sxs-lookup"><span data-stu-id="de4bd-274">APIs don't have an automatic mechanism for sending the non-cookie part of the token.</span></span> <span data-ttu-id="de4bd-275">Die Implementierung hängt wahrscheinlich die Client-Code-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="de4bd-275">The implementation probably depends on the client code implementation.</span></span> <span data-ttu-id="de4bd-276">Nachfolgend einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="de4bd-276">Some examples are shown below:</span></span>

<span data-ttu-id="de4bd-277">Auf Klassenebene-Beispiel:</span><span class="sxs-lookup"><span data-stu-id="de4bd-277">Class-level example:</span></span>

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

<span data-ttu-id="de4bd-278">Globale Beispiel:</span><span class="sxs-lookup"><span data-stu-id="de4bd-278">Global example:</span></span>

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a><span data-ttu-id="de4bd-279">Globale außer Kraft setzen oder antiforgery Attributen für controller</span><span class="sxs-lookup"><span data-stu-id="de4bd-279">Override global or controller antiforgery attributes</span></span>

<span data-ttu-id="de4bd-280">Die [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) Filter wird verwendet, um das Antifälschungstoken für eine bestimmte Aktion (oder Controller) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="de4bd-280">The [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) filter is used to eliminate the need for an antiforgery token for a given action (or controller).</span></span> <span data-ttu-id="de4bd-281">Wenn angewendet wird, überschreibt dieser Filter `ValidateAntiForgeryToken` und `AutoValidateAntiforgeryToken` angegebenen Filtern auf einer höheren Ebene (Global oder auf einem Domänencontroller).</span><span class="sxs-lookup"><span data-stu-id="de4bd-281">When applied, this filter overrides `ValidateAntiForgeryToken` and `AutoValidateAntiforgeryToken` filters specified at a higher level (globally or on a controller).</span></span>

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

## <a name="refresh-tokens-after-authentication"></a><span data-ttu-id="de4bd-282">Aktualisierungstoken nach der Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="de4bd-282">Refresh tokens after authentication</span></span>

<span data-ttu-id="de4bd-283">Token sollte aktualisiert werden, nachdem der Benutzer authentifiziert ist, durch Weiterleiten des Benutzers auf eine Sicht oder Razor Pages-Seite.</span><span class="sxs-lookup"><span data-stu-id="de4bd-283">Tokens should be refreshed after the user is authenticated by redirecting the user to a view or Razor Pages page.</span></span>

## <a name="javascript-ajax-and-spas"></a><span data-ttu-id="de4bd-284">JavaScript, AJAX und SPAs</span><span class="sxs-lookup"><span data-stu-id="de4bd-284">JavaScript, AJAX, and SPAs</span></span>

<span data-ttu-id="de4bd-285">In herkömmlichen HTML-basierte apps werden fälschungssicherheitstoken mit ausgeblendeten Formularfeldern an den Server übergeben.</span><span class="sxs-lookup"><span data-stu-id="de4bd-285">In traditional HTML-based apps, antiforgery tokens are passed to the server using hidden form fields.</span></span> <span data-ttu-id="de4bd-286">In modernen apps mit JavaScript-basierten und SPAs werden viele Anforderungen programmgesteuert vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-286">In modern JavaScript-based apps and SPAs, many requests are made programmatically.</span></span> <span data-ttu-id="de4bd-287">Diese AJAX-Anforderungen möglicherweise andere Techniken (z.B. Anforderungsheader oder Cookies) verwenden, um das Token senden zu können.</span><span class="sxs-lookup"><span data-stu-id="de4bd-287">These AJAX requests may use other techniques (such as request headers or cookies) to send the token.</span></span>

<span data-ttu-id="de4bd-288">Wenn Cookies zum Speichern von Authentifizierungstoken und zum Authentifizieren der API-Anforderungen auf dem Server verwendet werden, ist CSRF ein potenzielles Problem.</span><span class="sxs-lookup"><span data-stu-id="de4bd-288">If cookies are used to store authentication tokens and to authenticate API requests on the server, CSRF is a potential problem.</span></span> <span data-ttu-id="de4bd-289">Wenn lokaler Speicher verwendet wird, um das Token zu speichern, kann CSRF-Sicherheitsrisiko verringert werden, da die Werte aus dem lokalen Speicher automatisch mit jeder Anforderung an den Server gesendet werden nicht.</span><span class="sxs-lookup"><span data-stu-id="de4bd-289">If local storage is used to store the token, CSRF vulnerability might be mitigated because values from local storage aren't sent automatically to the server with every request.</span></span> <span data-ttu-id="de4bd-290">Daher verwenden lokalen Speicher zum Speichern der Antifälschungstoken auf dem Client und Senden des Tokens, wie ein Anforderungsheader ein empfohlenes Verfahren ist.</span><span class="sxs-lookup"><span data-stu-id="de4bd-290">Thus, using local storage to store the antiforgery token on the client and sending the token as a request header is a recommended approach.</span></span>

### <a name="javascript"></a><span data-ttu-id="de4bd-291">JavaScript</span><span class="sxs-lookup"><span data-stu-id="de4bd-291">JavaScript</span></span>

<span data-ttu-id="de4bd-292">Verwenden von JavaScript mit Sichten, kann das Token erstellt werden mithilfe eines Diensts aus, in der Ansicht.</span><span class="sxs-lookup"><span data-stu-id="de4bd-292">Using JavaScript with views, the token can be created using a service from within the view.</span></span> <span data-ttu-id="de4bd-293">Einfügen der [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) Dienst in der Ansicht, und rufen [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span><span class="sxs-lookup"><span data-stu-id="de4bd-293">Inject the [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) service into the view and call [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):</span></span>

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

<span data-ttu-id="de4bd-294">Dadurch entfällt das arbeiten direkt mit Cookies vom Server festlegen, oder sie vom Client gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="de4bd-294">This approach eliminates the need to deal directly with setting cookies from the server or reading them from the client.</span></span>

<span data-ttu-id="de4bd-295">Im vorherige Beispiel verwendet JavaScript zum Wert des ausgeblendeten Felds für den AJAX-POST-Header zu lesen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-295">The preceding example uses JavaScript to read the hidden field value for the AJAX POST header.</span></span>

<span data-ttu-id="de4bd-296">JavaScript kann auch Zugriffstoken in Cookies und Inhalt für das Cookie Header mit dem Wert von des Tokens zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-296">JavaScript can also access tokens in cookies and use the cookie's contents to create a header with the token's value.</span></span>

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

<span data-ttu-id="de4bd-297">Vorausgesetzt, das Skript fordert zum Senden von Token in einer Kopfzeile namens `X-CSRF-TOKEN`, konfigurieren Sie den antiforgery-Dienst auf die Suche nach der `X-CSRF-TOKEN` Header:</span><span class="sxs-lookup"><span data-stu-id="de4bd-297">Assuming the script requests to send the token in a header called `X-CSRF-TOKEN`, configure the antiforgery service to look for the `X-CSRF-TOKEN` header:</span></span>

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

<span data-ttu-id="de4bd-298">Im folgenden Beispiel wird JavaScript, um eine AJAX-Anforderung mit den entsprechenden Header zu machen:</span><span class="sxs-lookup"><span data-stu-id="de4bd-298">The following example uses JavaScript to make an AJAX request with the appropriate header:</span></span>

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

### <a name="angularjs"></a><span data-ttu-id="de4bd-299">AngularJS</span><span class="sxs-lookup"><span data-stu-id="de4bd-299">AngularJS</span></span>

<span data-ttu-id="de4bd-300">AngularJS verwendet eine Konvention gilt CSRF-Adresse.</span><span class="sxs-lookup"><span data-stu-id="de4bd-300">AngularJS uses a convention to address CSRF.</span></span> <span data-ttu-id="de4bd-301">Wenn der Server ein Cookie mit dem Namen sendet `XSRF-TOKEN`, die AngularJS `$http` Service eines Headers den Cookiewert hinzufügt, wenn er eine Anforderung an den Server sendet.</span><span class="sxs-lookup"><span data-stu-id="de4bd-301">If the server sends a cookie with the name `XSRF-TOKEN`, the AngularJS `$http` service adds the cookie value to a header when it sends a request to the server.</span></span> <span data-ttu-id="de4bd-302">Dieser Prozess erfolgt automatisch.</span><span class="sxs-lookup"><span data-stu-id="de4bd-302">This process is automatic.</span></span> <span data-ttu-id="de4bd-303">Der Header muss nicht explizit im Client festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="de4bd-303">The header doesn't need to be set in the client explicitly.</span></span> <span data-ttu-id="de4bd-304">Der Headername ist `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="de4bd-304">The header name is `X-XSRF-TOKEN`.</span></span> <span data-ttu-id="de4bd-305">Der Server sollte dieser Header zu erkennen und seinen Inhalt zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-305">The server should detect this header and validate its contents.</span></span>

<span data-ttu-id="de4bd-306">Für ASP.NET Core-API diese Konvention in Ihrem Start der Anwendung funktioniert:</span><span class="sxs-lookup"><span data-stu-id="de4bd-306">For ASP.NET Core API to work with this convention in your application startup:</span></span>

* <span data-ttu-id="de4bd-307">Konfigurieren Ihrer app für ein Token in einem Cookie namens bereitstellen `XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="de4bd-307">Configure your app to provide a token in a cookie called `XSRF-TOKEN`.</span></span>
* <span data-ttu-id="de4bd-308">Konfigurieren den antiforgery-Dienst einen Header mit Namen gesucht `X-XSRF-TOKEN`.</span><span class="sxs-lookup"><span data-stu-id="de4bd-308">Configure the antiforgery service to look for a header named `X-XSRF-TOKEN`.</span></span>

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

<span data-ttu-id="de4bd-309">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="de4bd-309">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="extend-antiforgery"></a><span data-ttu-id="de4bd-310">Erweitern Sie antifälschungsunterstützung</span><span class="sxs-lookup"><span data-stu-id="de4bd-310">Extend antiforgery</span></span>

<span data-ttu-id="de4bd-311">Die [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) Typ ermöglicht Entwicklern, die das Verhalten des Anti-CSRF-Systems durch zusätzliche Round-Tripping-Daten in jedem Token zu erweitern.</span><span class="sxs-lookup"><span data-stu-id="de4bd-311">The [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) type allows developers to extend the behavior of the anti-CSRF system by round-tripping additional data in each token.</span></span> <span data-ttu-id="de4bd-312">Die [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) Methode jedes Mal aufgerufen, wird ein Token generiert und der zurückgegebene Wert in das generierte Token eingebettet ist.</span><span class="sxs-lookup"><span data-stu-id="de4bd-312">The [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) method is called each time a field token is generated, and the return value is embedded within the generated token.</span></span> <span data-ttu-id="de4bd-313">Eine Implementierung kann einen Zeitstempel, eine Nonce oder einen anderen Wert zurück, und rufen dann [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) auf diese Daten zu überprüfen, wenn das Token überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="de4bd-313">An implementer could return a timestamp, a nonce, or any other value and then call [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) to validate this data when the token is validated.</span></span> <span data-ttu-id="de4bd-314">Den Benutzernamen des Clients ist bereits in das generierte Token, eingebettet, daher keine Notwendigkeit besteht, diese Informationen umfassen.</span><span class="sxs-lookup"><span data-stu-id="de4bd-314">The client's username is already embedded in the generated tokens, so there's no need to include this information.</span></span> <span data-ttu-id="de4bd-315">Wenn ein Token, ergänzende Daten, aber keine enthält `IAntiForgeryAdditionalDataProvider` wird konfiguriert, die ergänzenden Daten wird nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="de4bd-315">If a token includes supplemental data but no `IAntiForgeryAdditionalDataProvider` is configured, the supplemental data isn't validated.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="de4bd-316">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="de4bd-316">Additional resources</span></span>

* <span data-ttu-id="de4bd-317">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) auf [Öffnen der Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span><span class="sxs-lookup"><span data-stu-id="de4bd-317">[CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) on [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).</span></span>
* <xref:host-and-deploy/web-farm>
