---
title: Einfache Autorisierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie das Attribut "autorisieren" verwenden, um den Zugriff auf ASP.net Core Controller und Aktionen einzuschränken.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/simple
ms.openlocfilehash: b5f97038145ed479c315af50a35d6c64d85425a7
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652948"
---
# <a name="simple-authorization-in-aspnet-core"></a>Einfache Autorisierung in ASP.net Core

<a name="security-authorization-simple"></a>

Die Autorisierung in ASP.net Core wird mit <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> und den verschiedenen Parametern gesteuert. In seiner einfachsten Form schränkt das Anwenden des- `[Authorize]` Attributs auf einen Controller, eine Aktion oder eine Razor Seite den Zugriff auf diese Komponente auf einen beliebigen authentifizierten Benutzer ein.

Der folgende Code schränkt z. b. den Zugriff auf `AccountController` alle authentifizierten Benutzer ein.

```csharp
[Authorize]
public class AccountController : Controller
{
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Wenn Sie die Autorisierung auf eine Aktion anstelle des Controllers anwenden möchten, wenden Sie das- `AuthorizeAttribute` Attribut auf die eigentliche Aktion an:

```csharp
public class AccountController : Controller
{
   public ActionResult Login()
   {
   }

   [Authorize]
   public ActionResult Logout()
   {
   }
}
```

Jetzt können nur authentifizierte Benutzer auf die `Logout` Funktion zugreifen.

Sie können auch das- `AllowAnonymous` Attribut verwenden, um den Zugriff durch nicht authentifizierte Benutzer auf einzelne Aktionen zuzulassen. Beispiel:

```csharp
[Authorize]
public class AccountController : Controller
{
    [AllowAnonymous]
    public ActionResult Login()
    {
    }

    public ActionResult Logout()
    {
    }
}
```

Dies erlaubt nur authentifizierte Benutzer, `AccountController` außer bei der `Login` Aktion, auf die jeder zugreifen kann, unabhängig vom authentifizierten oder nicht authentifizierten/anonymen Status.

> [!WARNING]
> `[AllowAnonymous]`umgeht alle Autorisierungs Anweisungen. Wenn Sie `[AllowAnonymous]` und ein beliebiges Attribut kombinieren `[Authorize]` , `[Authorize]` werden die Attribute ignoriert. Wenn Sie z. b `[AllowAnonymous]` . auf Controller Ebene anwenden, werden alle `[Authorize]` Attribute auf dem gleichen Controller (oder in einer beliebigen Aktion) ignoriert.

<a name="aarp"></a>

## <a name="authorize-attribute-and-razor-pages"></a>Autorisieren von Attributen und Razor Seiten

<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>Kann ***nicht*** auf Razor Seiten Handler angewendet werden. `[Authorize]`Kann z. b. nicht auf `OnGet` , `OnPost` oder einen anderen Seiten Handler angewendet werden.

Die folgenden zwei Ansätze können verwendet werden, um die Autorisierung auf Razor seitenhandlermethoden anzuwenden:

* Verwenden Sie separate Seiten für Seiten Handler, die eine unterschiedliche Autorisierung erfordern. Freigegebenen Inhalt in eine oder mehrere [Teilansichten](xref:mvc/views/partial)verschoben. Wenn möglich, ist dies die empfohlene Vorgehensweise.
* Für Inhalte, die eine gemeinsame Seite freigeben müssen, schreiben Sie einen Filter, der eine Autorisierung als Teil von [iasyncpagefilter. onpagehandlerselectionasync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A)ausführt. Das [pagehandlerauth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) -GitHub-Projekt veranschaulicht diesen Ansatz:
  * Der Autorisierungs Filter wird von " [autorisierungshandlerfilter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizePageHandlerFilter.cs) " implementiert:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]

  * Das Attribut [[Autorität Page Handler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) wird auf den `OnGet` Seiten Handler angewendet:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]

> [!WARNING]
> Der [pagehandlerauth](https://github.com/pranavkm/PageHandlerAuth) -Beispiel Ansatz ist ***nicht***:
> * Verfassen Sie mit Autorisierungs Attributen, die auf die Seite, das Seiten Modell oder Global angewendet werden. Das Verfassen von Autorisierungs Attributen führt dazu, dass die Authentifizierung und Autorisierung mehrmals durchgeführt `AuthorizeAttribute` wird, wenn eine oder mehrere `AuthorizeFilter` Instanzen auch auf die Seite angewendet werden.
> * Arbeiten Sie in Verbindung mit dem Rest ASP.net Core Authentifizierungs-und Autorisierungssystem. Sie müssen überprüfen, ob dieser Ansatz für Ihre Anwendung ordnungsgemäß funktioniert.

Es ist nicht geplant, den `AuthorizeAttribute` auf Razor Seiten Handlern zu unterstützen. 
