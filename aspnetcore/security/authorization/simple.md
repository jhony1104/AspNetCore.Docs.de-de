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
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="1ae8c-103">Einfache Autorisierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="1ae8c-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="1ae8c-104">Die Autorisierung in MVC wird durch `AuthorizeAttribute` das-Attribut und die verschiedenen Parameter gesteuert.</span><span class="sxs-lookup"><span data-stu-id="1ae8c-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="1ae8c-105">Am einfachsten schränkt das Anwenden des `AuthorizeAttribute` -Attributs auf einen Controller oder eine Aktion den Zugriff auf den Controller oder die Aktion auf einen beliebigen authentifizierten Benutzer ein.</span><span class="sxs-lookup"><span data-stu-id="1ae8c-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="1ae8c-106">Der folgende Code schränkt z. b. den Zugriff `AccountController` auf alle authentifizierten Benutzer ein.</span><span class="sxs-lookup"><span data-stu-id="1ae8c-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="1ae8c-107">Wenn Sie die Autorisierung auf eine Aktion anstelle des Controllers anwenden möchten, wenden Sie `AuthorizeAttribute` das-Attribut auf die eigentliche Aktion an:</span><span class="sxs-lookup"><span data-stu-id="1ae8c-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="1ae8c-108">Jetzt können nur authentifizierte Benutzer auf die `Logout` Funktion zugreifen.</span><span class="sxs-lookup"><span data-stu-id="1ae8c-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="1ae8c-109">Sie können auch das- `AllowAnonymous` Attribut verwenden, um den Zugriff durch nicht authentifizierte Benutzer auf einzelne Aktionen zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="1ae8c-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="1ae8c-110">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1ae8c-110">For example:</span></span>

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

<span data-ttu-id="1ae8c-111">Dies erlaubt nur authentifizierte Benutzer, außer bei `AccountController`der `Login` Aktion, auf die jeder zugreifen kann, unabhängig vom authentifizierten oder nicht authentifizierten/anonymen Status.</span><span class="sxs-lookup"><span data-stu-id="1ae8c-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="1ae8c-112">`[AllowAnonymous]`umgeht alle Autorisierungs Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="1ae8c-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="1ae8c-113">Wenn Sie und `[AllowAnonymous]` ein beliebiges `[Authorize]` Attribut Kombi `[Authorize]` Nieren, werden die Attribute ignoriert.</span><span class="sxs-lookup"><span data-stu-id="1ae8c-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="1ae8c-114">Wenn Sie z. b `[AllowAnonymous]` . auf Controller Ebene anwenden, `[Authorize]` werden alle Attribute auf dem gleichen Controller (oder in einer beliebigen Aktion) ignoriert.</span><span class="sxs-lookup"><span data-stu-id="1ae8c-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
