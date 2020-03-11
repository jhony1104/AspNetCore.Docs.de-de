---
title: Einfache Autorisierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie das Attribut "autorisieren" verwenden, um den Zugriff auf ASP.net Core Controller und Aktionen einzuschränken.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/simple
ms.openlocfilehash: 6409def0508b855d3d2a4a1f4d3a3d15bfe5dd32
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653557"
---
# <a name="simple-authorization-in-aspnet-core"></a>Einfache Autorisierung in ASP.net Core

<a name="security-authorization-simple"></a>

Die Autorisierung in MVC wird durch das `AuthorizeAttribute`-Attribut und die verschiedenen Parameter gesteuert. Wenn das `AuthorizeAttribute`-Attribut auf einen Controller oder eine Aktion angewendet wird, wird der Zugriff auf den Controller oder die Aktion auf einen beliebigen authentifizierten Benutzer beschränkt.

Der folgende Code schränkt z. b. den Zugriff auf den `AccountController` auf einen beliebigen authentifizierten Benutzer ein.

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

Wenn Sie die Autorisierung auf eine Aktion anstelle des Controllers anwenden möchten, wenden Sie das `AuthorizeAttribute`-Attribut auf die eigentliche Aktion an:

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

Sie können auch das `AllowAnonymous`-Attribut verwenden, um den Zugriff durch nicht authentifizierte Benutzer auf einzelne Aktionen zuzulassen. Beispiel:

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

Dies ermöglicht nur authentifizierten Benutzern das `AccountController`, mit Ausnahme der `Login` Aktion, auf die jeder zugreifen kann, unabhängig vom authentifizierten oder nicht authentifizierten/anonymen Status.

> [!WARNING]
> `[AllowAnonymous]` umgeht alle Autorisierungs Anweisungen. Wenn Sie `[AllowAnonymous]` und ein beliebiges `[Authorize]` Attribut kombinieren, werden die `[Authorize]` Attribute ignoriert. Wenn Sie z. b. `[AllowAnonymous]` auf Controller Ebene anwenden, werden alle `[Authorize]` Attribute auf dem gleichen Controller (oder in einer beliebigen Aktion) ignoriert.
