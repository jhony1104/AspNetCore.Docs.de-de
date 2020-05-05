---
title: Verhindern offener Weiterleitungs Angriffe in ASP.net Core
author: ardalis
description: Zeigt, wie Sie Open Redirect-Angriffe gegen eine ASP.net Core-App verhindern können.
ms.author: riande
ms.date: 07/07/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/preventing-open-redirects
ms.openlocfilehash: ad4c9806146567b6ef1f5e78eaeca96cb649c1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774391"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>Verhindern offener Weiterleitungs Angriffe in ASP.net Core

Eine Web-App, die an eine URL umgeleitet wird, die über die Anforderung angegeben wird, z. b. die QueryString-oder Formulardaten, kann möglicherweise manipuliert werden, um Benutzer an eine externe, böswillige URL umzuleiten. Diese Manipulation wird als offener Umleitungs Angriff bezeichnet.

Wenn Ihre Anwendungslogik eine Umleitung an eine angegebene URL durchführt, müssen Sie überprüfen, ob die Umleitungs-URL nicht manipuliert wurde. ASP.net Core verfügt über integrierte Funktionen zum Schutz von apps vor offenen Umleitungs Angriffen (auch als offene Umleitung bezeichnet).

## <a name="what-is-an-open-redirect-attack"></a>Was ist ein offener Umleitungs Angriff?

Webanwendungen leiten Benutzer häufig an eine Anmeldeseite weiter, wenn Sie auf Ressourcen zugreifen, die eine Authentifizierung erfordern. Die Umleitung umfasst in der `returnUrl` Regel einen QueryString-Parameter, sodass der Benutzer an die ursprünglich angeforderte URL zurückgegeben werden kann, nachdem Sie sich erfolgreich angemeldet haben. Nachdem sich der Benutzer authentifiziert hat, werden Sie an die URL umgeleitet, die Sie ursprünglich angefordert hatten.

Da die Ziel-URL in der Abfrage Zeichenfolge der Anforderung angegeben ist, kann ein böswilliger Benutzer die QueryString manipulieren. Eine Manipulation QueryString könnte es der Website ermöglichen, den Benutzer an eine externe, böswillige Website umzuleiten. Diese Technik wird als offener Umleitungs-oder Umleitungs Angriff bezeichnet.

### <a name="an-example-attack"></a>Beispiel Angriff

Ein böswilliger Benutzer kann einen Angriff entwickeln, der dem böswilligen Benutzer den Zugriff auf die Anmelde Informationen eines Benutzers oder vertrauliche Informationen ermöglicht. Um mit dem Angriff zu beginnen, überredet der böswillige Benutzer den Benutzer, auf einen Link zur Anmeldeseite Ihrer Website `returnUrl` mit einem QueryString-Wert zu klicken, der der URL hinzugefügt wurde. Betrachten Sie beispielsweise eine APP, `contoso.com` die eine Anmeldeseite unter `http://contoso.com/Account/LogOn?returnUrl=/Home/About`enthält. Der Angriff führt folgende Schritte aus:

1. Der Benutzer klickt auf einen bösartigen Link `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` zu (die zweite URL lautet "%%**1**amp; quot;", nicht "contoso.com").
2. Der Benutzer meldet sich erfolgreich an.
3. Der Benutzer wird (von der Site) an `http://contoso1.com/Account/LogOn` (ein böswilliger Standort, der genau wie die tatsächliche Website aussieht) umgeleitet.
4. Der Benutzer meldet sich erneut an (gibt die Anmelde Informationen für böswillige Websites an) und wird zurück an die tatsächliche Website weitergeleitet.

Der Benutzer ist wahrscheinlich der Meinung, dass der erste Anmeldeversuch fehlgeschlagen ist und dass der zweite Versuch erfolgreich war. Der Benutzer ist wahrscheinlich nicht sicher, dass Ihre Anmelde Informationen kompromittiert sind.

![Umleitungs Angriffs Prozess öffnen](preventing-open-redirects/_static/open-redirection-attack-process.png)

Zusätzlich zu den Anmelde Seiten stellen einige Websites Umleitungs Seiten oder Endpunkte bereit. Stellen Sie sich vor, Ihre APP verfügt über eine Seite `/Home/Redirect`mit einer geöffneten Umleitung,. Ein Angreifer könnte z. b. einen Link in einer e-Mail erstellen, die zu `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`gehört. Ein typischer Benutzer sieht sich die URL an und zeigt, dass er mit Ihrem Website Namen beginnt. Wenn Sie darauf vertrauen, klicken Sie auf den Link. Die geöffnete Umleitung würde den Benutzer dann an die Phishingsite senden, die mit Ihrem identisch ist, und der Benutzer würde sich wahrscheinlich bei der Website anmelden, die Sie glauben.

## <a name="protecting-against-open-redirect-attacks"></a>Schutz gegen Open Redirect-Angriffe

Wenn Sie Webanwendungen entwickeln, behandeln Sie alle vom Benutzer bereitgestellten Daten als nicht vertrauenswürdig. Wenn Ihre Anwendung über Funktionen verfügt, die den Benutzer basierend auf dem Inhalt der URL umleitet, stellen Sie sicher, dass solche Umleitungen nur lokal innerhalb Ihrer APP erfolgen (oder an eine bekannte URL, nicht an eine URL, die in der Abfrage Zeichenfolge angegeben werden kann).

### <a name="localredirect"></a>Localredirect

Verwenden Sie `LocalRedirect` die-Hilfsmethode aus `Controller` der-Basisklasse:

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect`löst eine Ausnahme aus, wenn eine nicht lokale URL angegeben wird. Andernfalls verhält sie sich wie die `Redirect` -Methode.

### <a name="islocalurl"></a>Islocalurl

Verwenden Sie die [islocalurl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper.islocalurl#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) -Methode, um URLs vor der Umleitung zu testen:

Im folgenden Beispiel wird gezeigt, wie vor der Umleitung überprüft wird, ob eine URL lokal ist.

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

Mit `IsLocalUrl` der-Methode wird verhindert, dass Benutzer versehentlich an eine böswillige Site umgeleitet werden. Sie können die Details der URL protokollieren, die bereitgestellt wurde, wenn eine nicht lokale URL in einer Situation angegeben wird, in der Sie eine lokale URL erwartet haben. Das Protokollieren von Umleitungs-URLs kann bei der Diagnose von Umleitungen helfen.
