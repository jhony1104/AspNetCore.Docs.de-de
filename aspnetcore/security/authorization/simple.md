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
ms.openlocfilehash: f273c3e9db74fa63de85c65d94223d0ef7326036
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775634"
---
# <a name="simple-authorization-in-aspnet-core"></a>Einfache Autorisierung in ASP.net Core

<a name="security-authorization-simple"></a>

Die Autorisierung in MVC wird durch `AuthorizeAttribute` das-Attribut und die verschiedenen Parameter gesteuert. Am einfachsten schränkt das Anwenden des `AuthorizeAttribute` -Attributs auf einen Controller oder eine Aktion den Zugriff auf den Controller oder die Aktion auf einen beliebigen authentifizierten Benutzer ein.

Der folgende Code schränkt z. b. den Zugriff `AccountController` auf alle authentifizierten Benutzer ein.

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

Wenn Sie die Autorisierung auf eine Aktion anstelle des Controllers anwenden möchten, wenden Sie `AuthorizeAttribute` das-Attribut auf die eigentliche Aktion an:

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

Dies erlaubt nur authentifizierte Benutzer, außer bei `AccountController`der `Login` Aktion, auf die jeder zugreifen kann, unabhängig vom authentifizierten oder nicht authentifizierten/anonymen Status.

> [!WARNING]
> `[AllowAnonymous]`umgeht alle Autorisierungs Anweisungen. Wenn Sie und `[AllowAnonymous]` ein beliebiges `[Authorize]` Attribut Kombi `[Authorize]` Nieren, werden die Attribute ignoriert. Wenn Sie z. b `[AllowAnonymous]` . auf Controller Ebene anwenden, `[Authorize]` werden alle Attribute auf dem gleichen Controller (oder in einer beliebigen Aktion) ignoriert.
