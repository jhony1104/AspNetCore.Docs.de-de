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
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="647cd-103">Einfache Autorisierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="647cd-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="647cd-104">Autorisierung in MVC wird gesteuert, durch die `AuthorizeAttribute` -Attribut und seine verschiedenen Parameter.</span><span class="sxs-lookup"><span data-stu-id="647cd-104">Authorization in MVC is controlled through the `AuthorizeAttribute` attribute and its various parameters.</span></span> <span data-ttu-id="647cd-105">Im einfachsten Fall Anwenden der `AuthorizeAttribute` Attribut einen Controller oder eine Aktion schränkt den Zugriff auf den Controller oder eine Aktion für alle authentifizierten Benutzer.</span><span class="sxs-lookup"><span data-stu-id="647cd-105">At its simplest, applying the `AuthorizeAttribute` attribute to a controller or action limits access to the controller or action to any authenticated user.</span></span>

<span data-ttu-id="647cd-106">Der folgende Code beschränkt beispielsweise den Zugriff auf die `AccountController` für alle authentifizierten Benutzer.</span><span class="sxs-lookup"><span data-stu-id="647cd-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="647cd-107">Wenn die Autorisierung auf eine Aktion, anstatt den Controller angewendet werden sollen, wenden Sie die `AuthorizeAttribute` -Attribut auf die Aktion selbst:</span><span class="sxs-lookup"><span data-stu-id="647cd-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="647cd-108">Jetzt nur authentifizierte Benutzer zugreifen, können die `Logout` Funktion.</span><span class="sxs-lookup"><span data-stu-id="647cd-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="647cd-109">Sie können auch die `AllowAnonymous` Attribut, um den Zugriff durch nicht authentifizierte Benutzer auf individuelle Aktionen zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="647cd-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="647cd-110">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="647cd-110">For example:</span></span>

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

<span data-ttu-id="647cd-111">Dies würde nur authentifizierte Benutzer zu ermöglichen, die `AccountController`, mit Ausnahme der `Login` Aktion, die von jedermann, unabhängig von ihren Status authentifiziert oder nicht authentifizierte / anonym zugegriffen werden kann.</span><span class="sxs-lookup"><span data-stu-id="647cd-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="647cd-112">`[AllowAnonymous]` umgeht alle Anweisungen der Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="647cd-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="647cd-113">Wenn Sie kombinieren `[AllowAnonymous]` sowie `[Authorize]` -Attribut, die `[Authorize]` Attribute werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="647cd-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="647cd-114">Angenommen, Sie gelten `[AllowAnonymous]` auf Controllerebene, alle `[Authorize]` Attribute, die auf denselben Controller (oder auf eine beliebige Aktion darin) wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="647cd-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
