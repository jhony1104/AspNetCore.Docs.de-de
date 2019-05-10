---
title: Verhindern von Angriffen durch offene umleitungen in ASP.NET Core
author: ardalis
description: Zeigt, wie zum Verhindern von Angriffen durch offene umleitungen für eine ASP.NET Core-app
ms.author: riande
ms.date: 07/07/2017
uid: security/preventing-open-redirects
ms.openlocfilehash: 0896189d2caaccb19647eb7c6d57f29dfc0290dd
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891617"
---
# <a name="prevent-open-redirect-attacks-in-aspnet-core"></a>Verhindern von Angriffen durch offene umleitungen in ASP.NET Core

Eine Web-app, die an eine URL umleitet, die über die Anforderung wie z. B. die Abfragezeichenfolge oder Form-Daten angegeben wird kann möglicherweise manipuliert werden Benutzer auf eine externe, böswillige URL umgeleitet. Diese Manipulation wird einen offene umleitungen-Angriff bezeichnet.

Wenn Ihre Anwendungslogik an eine angegebene URL umgeleitet wird, müssen Sie sicherstellen, dass die umleitungs-URL nicht manipuliert wurde. ASP.NET Core verfügt über integrierte Funktionen, die apps vor Angriffen durch offene umleitungen (auch bekannt als offene umleitungen) zu schützen.

## <a name="what-is-an-open-redirect-attack"></a>Was ist ein Angriff offene umleitungen?

Web-Anwendungen umleiten häufig Benutzer zu einer Anmeldeseite beim Zugriff auf Ressourcen, die eine Authentifizierung erfordern. Die Umleitung in der Regel enthält eine `returnUrl` Querystring-Parameter, damit der Benutzer zur ursprünglich angeforderten URL zurückgegeben werden kann, nachdem sie sich erfolgreich angemeldet haben. Nachdem der Benutzer authentifiziert hat, sind sie an die URL umgeleitet, die er ursprünglich angefordert hatte.

Da die Ziel-URL in der Abfragezeichenfolge der Anforderung angegeben ist, könnte ein böswilliger Benutzer mit der Abfragezeichenfolge manipulieren. Eine manipulierte Abfragezeichenfolge kann die Website Umleiten des Benutzers an eine externe, bösartige Website ermöglichen. Dieses Verfahren ist einen open Redirect (oder die Umleitung)-Angriff bezeichnet.

### <a name="an-example-attack"></a>Ein Beispiel-Angriff

Ein böswilliger Benutzer kann einen Angriff, bestimmt der böswillige Benutzer Zugriff auf Anmeldeinformationen oder vertrauliche Informationen eines Benutzers entwickeln. Um den Angriff zu starten, überredet der böswillige Benutzer den Benutzer auf einen Link zu Ihrer Website-Anmeldeseite mit einem `returnUrl` Querystring-Wert, der URL hinzugefügt. Betrachten Sie beispielsweise eine app im `contoso.com` , umfasst eine Anmeldeseite auf `http://contoso.com/Account/LogOn?returnUrl=/Home/About`. Der Angriff: Diese Schritte aus

1. Der Benutzer klickt auf einen bösartigen Link zu `http://contoso.com/Account/LogOn?returnUrl=http://contoso1.com/Account/LogOn` (die zweite URL dient "Contoso**1**.com", nicht "contoso.com").
2. Der Benutzer anmeldet erfolgreich.
3. Der Benutzer wird (von der Website) umgeleitet, um `http://contoso1.com/Account/LogOn` (eine schädliche Website, die genau wie die wirkliche Website aussieht).
4. Der Benutzer wieder anmeldet (sodass böswillige Standort ihrer Anmeldeinformationen) und wieder an die wirkliche Website umgeleitet wird.

Der Benutzer wahrscheinlich ist der Auffassung, dass ihre erste Anmeldung ist fehlgeschlagen und der zweite Versuch erfolgreich ist. Der Benutzer bleibt in den meisten Fällen merkt nicht, dass ihre Anmeldeinformationen kompromittiert werden.

![Offene Umleitungen Angriff Prozess](preventing-open-redirects/_static/open-redirection-attack-process.png)

Geben Sie zusätzlich zu Anmeldeseiten einige Websites umleitungs-Seiten oder Endpunkte. Angenommen, Ihre app verfügt über eine Seite mit einer open-Umleitung `/Home/Redirect`. Ein Angreifer könnte erstellen, z. B. einen Link per e-Mail, der zum führt `[yoursite]/Home/Redirect?url=http://phishingsite.com/Home/Login`. Ein typischer Benutzer wird sehen Sie sich die URL, und sehen, dass sie durch den Standortnamen Ihres beginnt. Gewähren von vertrauen, die, werden sie auf den Link klicken. Klicken Sie dann würde der offene Umleitung den Benutzer senden, auf die Phishing-Website, die mit Ihnen identisch ist, und der Benutzer wahrscheinlich melden Sie sich, was ihrer Meinung nach wird Ihre Website.

## <a name="protecting-against-open-redirect-attacks"></a>Schutz vor Angriffen durch offene umleitungen

Bei der Entwicklung von Webanwendungen, behandeln Sie alle Benutzer bereitgestellten Daten als nicht vertrauenswürdig. Wenn Ihre Anwendung Funktionen, der den Benutzer basierend auf den Inhalt der URL umleitet verfügt, stellen Sie sicher, dass solche leitet nur lokal ausgeführt werden, in Ihrer app (oder an eine bekannte URL, die nicht zu einer URL, die in der Abfragezeichenfolge angegeben werden kann).

### <a name="localredirect"></a>LocalRedirect

Verwenden der `LocalRedirect` Hilfsmethode, die von der Basisklasse `Controller` Klasse:

```csharp
public IActionResult SomeAction(string redirectUrl)
{
    return LocalRedirect(redirectUrl);
}
```

`LocalRedirect` Wenn eine nicht lokale URL angegeben ist, wird eine Ausnahme ausgelöst. Anderenfalls verhält sich genau wie die `Redirect` Methode.

### <a name="islocalurl"></a>IsLocalUrl

Verwenden der [IsLocalUrl](/dotnet/api/Microsoft.AspNetCore.Mvc.IUrlHelper?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_IUrlHelper_IsLocalUrl_System_String_) Methode, um vor dem Umleiten von URLs zu testen:

Das folgende Beispiel zeigt, wie Sie überprüfen, ob eine URL, die vor der Umleitung lokal ist.

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

Die `IsLocalUrl` Methode verhindert, dass Benutzer versehentlich an eine schädliche Website umgeleitet wird. Sie können die Details der URL protokollieren, die bereitgestellt wurde, wenn eine nicht lokale URL in einer Situation angegeben wird, wenn Sie eine lokale URL erwartet. Protokollierung Redirect können URLs bei der Diagnose von Umleitung-Angriffe.
