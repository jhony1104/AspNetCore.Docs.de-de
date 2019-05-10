---
title: Einfache Autorisierung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie das Authorize-Attribut zu verwenden, um den Zugriff auf ASP.NET Core-Controllern und Aktionen beschränken.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/simple
ms.openlocfilehash: 6409def0508b855d3d2a4a1f4d3a3d15bfe5dd32
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897677"
---
# <a name="simple-authorization-in-aspnet-core"></a>Einfache Autorisierung in ASP.NET Core

<a name="security-authorization-simple"></a>

Autorisierung in MVC wird gesteuert, durch die `AuthorizeAttribute` -Attribut und seine verschiedenen Parameter. Im einfachsten Fall Anwenden der `AuthorizeAttribute` Attribut einen Controller oder eine Aktion schränkt den Zugriff auf den Controller oder eine Aktion für alle authentifizierten Benutzer.

Der folgende Code beschränkt beispielsweise den Zugriff auf die `AccountController` für alle authentifizierten Benutzer.

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

Wenn die Autorisierung auf eine Aktion, anstatt den Controller angewendet werden sollen, wenden Sie die `AuthorizeAttribute` -Attribut auf die Aktion selbst:

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

Jetzt nur authentifizierte Benutzer zugreifen, können die `Logout` Funktion.

Sie können auch die `AllowAnonymous` Attribut, um den Zugriff durch nicht authentifizierte Benutzer auf individuelle Aktionen zu ermöglichen. Zum Beispiel:

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

Dies würde nur authentifizierte Benutzer zu ermöglichen, die `AccountController`, mit Ausnahme der `Login` Aktion, die von jedermann, unabhängig von ihren Status authentifiziert oder nicht authentifizierte / anonym zugegriffen werden kann.

> [!WARNING]
> `[AllowAnonymous]` umgeht alle Anweisungen der Autorisierung. Wenn Sie kombinieren `[AllowAnonymous]` sowie `[Authorize]` -Attribut, die `[Authorize]` Attribute werden ignoriert. Angenommen, Sie gelten `[AllowAnonymous]` auf Controllerebene, alle `[Authorize]` Attribute, die auf denselben Controller (oder auf eine beliebige Aktion darin) wird ignoriert.
