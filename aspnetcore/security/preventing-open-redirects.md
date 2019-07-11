---
title: Verhindern von Angriffen durch offene umleitungen in ASP.NET Core
author: ardalis
description: Zeigt, wie zum Verhindern von Angriffen durch offene umleitungen für eine ASP.NET Core-app
ms.author: riande
ms.date: 07/07/2017
uid: security/preventing-open-redirects
ms.openlocfilehash: 9d8cac8708fe9aeadba5af1287362a20df7f6bfe
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815488"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a><span data-ttu-id="d7144-103">Verhindern von Angriffen durch offene umleitungen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7144-103">Prevent open redirect attacks in ASP.NET Core</span></span>

<span data-ttu-id="d7144-104">Eine Web-app, die an eine URL umleitet, die über die Anforderung wie z. B. die Abfragezeichenfolge oder Form-Daten angegeben wird kann möglicherweise manipuliert werden Benutzer auf eine externe, böswillige URL umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="d7144-104">A web app that redirects to a URL that's specified via the request such as the querystring or form data can potentially be tampered with to redirect users to an external, malicious URL.</span></span> <span data-ttu-id="d7144-105">Diese Manipulation wird einen offene umleitungen-Angriff bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="d7144-105">This tampering is called an open redirection attack.</span></span>

<span data-ttu-id="d7144-106">Wenn Ihre Anwendungslogik an eine angegebene URL umgeleitet wird, müssen Sie sicherstellen, dass die umleitungs-URL nicht manipuliert wurde.</span><span class="sxs-lookup"><span data-stu-id="d7144-106">Whenever your application logic redirects to a specified URL, you must verify that the redirection URL hasn't been tampered with.</span></span> <span data-ttu-id="d7144-107">ASP.NET Core verfügt über integrierte Funktionen, die apps vor Angriffen durch offene umleitungen (auch bekannt als offene umleitungen) zu schützen.</span><span class="sxs-lookup"><span data-stu-id="d7144-107">ASP.NET Core has built-in functionality to help protect apps from open redirect (also known as open redirection) attacks.</span></span>

## <a name="what-is-an-open-redirect-attack"></a><span data-ttu-id="d7144-108">Was ist ein Angriff offene umleitungen?</span><span class="sxs-lookup"><span data-stu-id="d7144-108">What is an open redirect attack?</span></span>

<span data-ttu-id="d7144-109">Web-Anwendungen umleiten häufig Benutzer zu einer Anmeldeseite beim Zugriff auf Ressourcen, die eine Authentifizierung erfordern.</span><span class="sxs-lookup"><span data-stu-id="d7144-109">Web applications frequently redirect users to a login page when they access resources that require authentication.</span></span> <span data-ttu-id="d7144-110">Die Umleitung in der Regel enthält eine `returnUrl` Querystring-Parameter, damit der Benutzer zur ursprünglich angeforderten URL zurückgegeben werden kann, nachdem sie sich erfolgreich angemeldet haben.</span><span class="sxs-lookup"><span data-stu-id="d7144-110">The redirection typically includes a `returnUrl` querystring parameter so that the user can be returned to the originally requested URL after they have successfully logged in.</span></span> <span data-ttu-id="d7144-111">Nachdem der Benutzer authentifiziert hat, sind sie an die URL umgeleitet, die er ursprünglich angefordert hatte.</span><span class="sxs-lookup"><span data-stu-id="d7144-111">After the user authenticates, they're redirected to the URL they had originally requested.</span></span>

<span data-ttu-id="d7144-112">Da die Ziel-URL in der Abfragezeichenfolge der Anforderung angegeben ist, könnte ein böswilliger Benutzer mit der Abfragezeichenfolge manipulieren.</span><span class="sxs-lookup"><span data-stu-id="d7144-112">Because the destination URL is specified in the querystring of the request, a malicious user could tamper with the querystring.</span></span> <span data-ttu-id="d7144-113">Eine manipulierte Abfragezeichenfolge kann die Website Umleiten des Benutzers an eine externe, bösartige Website ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="d7144-113">A tampered querystring could allow the site to redirect the user to an external, malicious site.</span></span> <span data-ttu-id="d7144-114">Dieses Verfahren ist einen open Redirect (oder die Umleitung)-Angriff bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="d7144-114">This technique is called an open redirect (or redirection) attack.</span></span>

### <a name="an-example-attack"></a><span data-ttu-id="d7144-115">Ein Beispiel-Angriff</span><span class="sxs-lookup"><span data-stu-id="d7144-115">An example attack</span></span>

<span data-ttu-id="d7144-116">Ein böswilliger Benutzer kann einen Angriff, bestimmt der böswillige Benutzer Zugriff auf Anmeldeinformationen oder vertrauliche Informationen eines Benutzers entwickeln.</span><span class="sxs-lookup"><span data-stu-id="d7144-116">A malicious user can develop an attack intended to allow the malicious user access to a user's credentials or sensitive information.</span></span> <span data-ttu-id="d7144-117">Um den Angriff zu starten, überredet der böswillige Benutzer den Benutzer auf einen Link zu Ihrer Website-Anmeldeseite mit einem `returnUrl` Querystring-Wert, der URL hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d7144-117">To begin the attack, the malicious user convinces the user to click a link to your site's login page with a `returnUrl` querystring value added to the URL.</span></span> <span data-ttu-id="d7144-118">Betrachten Sie beispielsweise eine app im `contoso.com` , umfasst eine Anmeldeseite auf `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="d7144-118">For example, consider an app at `contoso.com` that includes a login page at `http://contoso.com/Account/LogOn?returnUrl=/Home/About`.</span></span> <span data-ttu-id="d7144-119">Der Angriff: Diese Schritte aus</span><span class="sxs-lookup"><span data-stu-id="d7144-119">The attack follows these steps:</span></span>

1. <span data-ttu-id="d7144-120">Der Benutzer klickt auf einen bösartigen Link zu `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (die zweite URL dient "Contoso**1**.com", nicht "contoso.com").</span><span class="sxs-lookup"><span data-stu-id="d7144-120">The user clicks a malicious link to `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (the second URL is "contoso**1**.com", not "contoso.com").</span></span>
2. <span data-ttu-id="d7144-121">Der Benutzer anmeldet erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="d7144-121">The user logs in successfully.</span></span>
3. <span data-ttu-id="d7144-122">Der Benutzer wird (von der Website) umgeleitet, um `http://contoso1.com/Account/LogOn` (eine schädliche Website, die genau wie die wirkliche Website aussieht).</span><span class="sxs-lookup"><span data-stu-id="d7144-122">The user is redirected (by the site) to `http://contoso1.com/Account/LogOn` (a malicious site that looks exactly like real site).</span></span>
4. <span data-ttu-id="d7144-123">Der Benutzer wieder anmeldet (sodass böswillige Standort ihrer Anmeldeinformationen) und wieder an die wirkliche Website umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="d7144-123">The user logs in again (giving malicious site their credentials) and is redirected back to the real site.</span></span>

<span data-ttu-id="d7144-124">Der Benutzer wahrscheinlich ist der Auffassung, dass ihre erste Anmeldung ist fehlgeschlagen und der zweite Versuch erfolgreich ist.</span><span class="sxs-lookup"><span data-stu-id="d7144-124">The user likely believes that their first attempt to log in failed and that their second attempt is successful.</span></span> <span data-ttu-id="d7144-125">Der Benutzer bleibt in den meisten Fällen merkt nicht, dass ihre Anmeldeinformationen kompromittiert werden.</span><span class="sxs-lookup"><span data-stu-id="d7144-125">The user most likely remains unaware that their credentials are compromised.</span></span>

![Offene Umleitungen Angriff Prozess](preventing-open-redirects/_static/open-redirection-attack-process.png)

<span data-ttu-id="d7144-127">Geben Sie zusätzlich zu Anmeldeseiten einige Websites umleitungs-Seiten oder Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="d7144-127">In addition to login pages, some sites provide redirect pages or endpoints.</span></span> <span data-ttu-id="d7144-128">Angenommen, Ihre app verfügt über eine Seite mit einer open-Umleitung `/Home/Redirect`.</span><span class="sxs-lookup"><span data-stu-id="d7144-128">Imagine your app has a page with an open redirect, `/Home/Redirect`.</span></span> <span data-ttu-id="d7144-129">Ein Angreifer könnte erstellen, z. B. einen Link per e-Mail, der zum führt `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span><span class="sxs-lookup"><span data-stu-id="d7144-129">An attacker could create, for example, a link in an email that goes to `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`.</span></span> <span data-ttu-id="d7144-130">Ein typischer Benutzer wird sehen Sie sich die URL, und sehen, dass sie durch den Standortnamen Ihres beginnt.</span><span class="sxs-lookup"><span data-stu-id="d7144-130">A typical user will look at the URL and see it begins with your site name.</span></span> <span data-ttu-id="d7144-131">Gewähren von vertrauen, die, werden sie auf den Link klicken.</span><span class="sxs-lookup"><span data-stu-id="d7144-131">Trusting that, they will click the link.</span></span> <span data-ttu-id="d7144-132">Klicken Sie dann würde der offene Umleitung den Benutzer senden, auf die Phishing-Website, die mit Ihnen identisch ist, und der Benutzer wahrscheinlich melden Sie sich, was ihrer Meinung nach wird Ihre Website.</span><span class="sxs-lookup"><span data-stu-id="d7144-132">The open redirect would then send the user to the phishing site, which looks identical to yours, and the user would likely login to what they believe is your site.</span></span>

## <a name="protecting-against-open-redirect-attacks"></a><span data-ttu-id="d7144-133">Schutz vor Angriffen durch offene umleitungen</span><span class="sxs-lookup"><span data-stu-id="d7144-133">Protecting against open redirect attacks</span></span>

<span data-ttu-id="d7144-134">Bei der Entwicklung von Webanwendungen, behandeln Sie alle Benutzer bereitgestellten Daten als nicht vertrauenswürdig.</span><span class="sxs-lookup"><span data-stu-id="d7144-134">When developing web applications, treat all user-provided data as untrustworthy.</span></span> <span data-ttu-id="d7144-135">Wenn Ihre Anwendung Funktionen, der den Benutzer basierend auf den Inhalt der URL umleitet verfügt, stellen Sie sicher, dass solche leitet nur lokal ausgeführt werden, in Ihrer app (oder an eine bekannte URL, die nicht zu einer URL, die in der Abfragezeichenfolge angegeben werden kann).</span><span class="sxs-lookup"><span data-stu-id="d7144-135">If your application has functionality that redirects the user based on the contents of the URL,  ensure that such redirects are only done locally within your app (or to a known URL, not any URL that may be supplied in the querystring).</span></span>

### <a name="localredirect"></a><span data-ttu-id="d7144-136">LocalRedirect</span><span class="sxs-lookup"><span data-stu-id="d7144-136">LocalRedirect</span></span>

<span data-ttu-id="d7144-137">Verwenden der `LocalRedirect` Hilfsmethode, die von der Basisklasse `Controller` Klasse:</span><span class="sxs-lookup"><span data-stu-id="d7144-137">Use the `LocalRedirect` helper method from the base `Controller` class:</span></span>

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

<span data-ttu-id="d7144-138">`LocalRedirect` Wenn eine nicht lokale URL angegeben ist, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="d7144-138">`LocalRedirect` will throw an exception if a non-local URL is specified.</span></span> <span data-ttu-id="d7144-139">Anderenfalls verhält sich genau wie die `Redirect` Methode.</span><span class="sxs-lookup"><span data-stu-id="d7144-139">Otherwise, it behaves just like the `Redirect` method.</span></span>

### <a name="islocalurl"></a><span data-ttu-id="d7144-140">IsLocalUrl</span><span class="sxs-lookup"><span data-stu-id="d7144-140">IsLocalUrl</span></span>

<span data-ttu-id="d7144-141">Verwenden der [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) Methode, um vor dem Umleiten von URLs zu testen:</span><span class="sxs-lookup"><span data-stu-id="d7144-141">Use the [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) method to test URLs before redirecting:</span></span>

<span data-ttu-id="d7144-142">Das folgende Beispiel zeigt, wie Sie überprüfen, ob eine URL, die vor der Umleitung lokal ist.</span><span class="sxs-lookup"><span data-stu-id="d7144-142">The following example shows how to check whether a URL is local before redirecting.</span></span>

```csharp
private IActionResult RedirectToLocal(string returnUrl)
{
    if (Url.IsLocalUrl(returnUrl))
    {
        return Redirect(returnUrl);
    }
    else
    {
        return RedirectToAction(nameof(HomeController.Index), "Home");
    }
}
```

<span data-ttu-id="d7144-143">Die `IsLocalUrl` Methode verhindert, dass Benutzer versehentlich an eine schädliche Website umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="d7144-143">The `IsLocalUrl` method protects users from being inadvertently redirected to a malicious site.</span></span> <span data-ttu-id="d7144-144">Sie können die Details der URL protokollieren, die bereitgestellt wurde, wenn eine nicht lokale URL in einer Situation angegeben wird, wenn Sie eine lokale URL erwartet.</span><span class="sxs-lookup"><span data-stu-id="d7144-144">You can log the details of the URL that was provided when a non-local URL is supplied in a situation where you expected a local URL.</span></span> <span data-ttu-id="d7144-145">Protokollierung Redirect können URLs bei der Diagnose von Umleitung-Angriffe.</span><span class="sxs-lookup"><span data-stu-id="d7144-145">Logging redirect URLs may help in diagnosing redirection attacks.</span></span>
