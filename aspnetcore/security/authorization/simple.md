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
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="96580-103">Einfache Autorisierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="96580-103">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="96580-104">Die Autorisierung in ASP.net Core wird mit <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> und den verschiedenen Parametern gesteuert.</span><span class="sxs-lookup"><span data-stu-id="96580-104">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="96580-105">In seiner einfachsten Form schränkt das Anwenden des- `[Authorize]` Attributs auf einen Controller, eine Aktion oder eine Razor Seite den Zugriff auf diese Komponente auf einen beliebigen authentifizierten Benutzer ein.</span><span class="sxs-lookup"><span data-stu-id="96580-105">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="96580-106">Der folgende Code schränkt z. b. den Zugriff auf `AccountController` alle authentifizierten Benutzer ein.</span><span class="sxs-lookup"><span data-stu-id="96580-106">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="96580-107">Wenn Sie die Autorisierung auf eine Aktion anstelle des Controllers anwenden möchten, wenden Sie das- `AuthorizeAttribute` Attribut auf die eigentliche Aktion an:</span><span class="sxs-lookup"><span data-stu-id="96580-107">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="96580-108">Jetzt können nur authentifizierte Benutzer auf die `Logout` Funktion zugreifen.</span><span class="sxs-lookup"><span data-stu-id="96580-108">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="96580-109">Sie können auch das- `AllowAnonymous` Attribut verwenden, um den Zugriff durch nicht authentifizierte Benutzer auf einzelne Aktionen zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="96580-109">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="96580-110">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="96580-110">For example:</span></span>

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

<span data-ttu-id="96580-111">Dies erlaubt nur authentifizierte Benutzer, `AccountController` außer bei der `Login` Aktion, auf die jeder zugreifen kann, unabhängig vom authentifizierten oder nicht authentifizierten/anonymen Status.</span><span class="sxs-lookup"><span data-stu-id="96580-111">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="96580-112">`[AllowAnonymous]`umgeht alle Autorisierungs Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="96580-112">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="96580-113">Wenn Sie `[AllowAnonymous]` und ein beliebiges Attribut kombinieren `[Authorize]` , `[Authorize]` werden die Attribute ignoriert.</span><span class="sxs-lookup"><span data-stu-id="96580-113">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="96580-114">Wenn Sie z. b `[AllowAnonymous]` . auf Controller Ebene anwenden, werden alle `[Authorize]` Attribute auf dem gleichen Controller (oder in einer beliebigen Aktion) ignoriert.</span><span class="sxs-lookup"><span data-stu-id="96580-114">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>

<a name="aarp"></a>

## <a name="authorize-attribute-and-razor-pages"></a><span data-ttu-id="96580-115">Autorisieren von Attributen und Razor Seiten</span><span class="sxs-lookup"><span data-stu-id="96580-115">Authorize attribute and Razor Pages</span></span>

<span data-ttu-id="96580-116"><xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>Kann ***nicht*** auf Razor Seiten Handler angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="96580-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> can ***not*** be applied to Razor Page handlers.</span></span> <span data-ttu-id="96580-117">`[Authorize]`Kann z. b. nicht auf `OnGet` , `OnPost` oder einen anderen Seiten Handler angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="96580-117">For example, `[Authorize]` can't be applied to `OnGet`, `OnPost`, or any other page handler.</span></span>

<span data-ttu-id="96580-118">Die folgenden zwei Ansätze können verwendet werden, um die Autorisierung auf Razor seitenhandlermethoden anzuwenden:</span><span class="sxs-lookup"><span data-stu-id="96580-118">The following two approaches can be used to apply authorization to Razor Page handler methods:</span></span>

* <span data-ttu-id="96580-119">Verwenden Sie separate Seiten für Seiten Handler, die eine unterschiedliche Autorisierung erfordern.</span><span class="sxs-lookup"><span data-stu-id="96580-119">Use separate pages for page handlers requiring different authorization.</span></span> <span data-ttu-id="96580-120">Freigegebenen Inhalt in eine oder mehrere [Teilansichten](xref:mvc/views/partial)verschoben.</span><span class="sxs-lookup"><span data-stu-id="96580-120">Moved shared content into one or more [partial views](xref:mvc/views/partial).</span></span> <span data-ttu-id="96580-121">Wenn möglich, ist dies die empfohlene Vorgehensweise.</span><span class="sxs-lookup"><span data-stu-id="96580-121">When possible, this is the recommended approach.</span></span>
* <span data-ttu-id="96580-122">Für Inhalte, die eine gemeinsame Seite freigeben müssen, schreiben Sie einen Filter, der eine Autorisierung als Teil von [iasyncpagefilter. onpagehandlerselectionasync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A)ausführt.</span><span class="sxs-lookup"><span data-stu-id="96580-122">For content that must share a common page, write a filter that performs authorization as part of [IAsyncPageFilter.OnPageHandlerSelectionAsync](xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter.OnPageHandlerSelectionAsync%2A).</span></span> <span data-ttu-id="96580-123">Das [pagehandlerauth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) -GitHub-Projekt veranschaulicht diesen Ansatz:</span><span class="sxs-lookup"><span data-stu-id="96580-123">The [PageHandlerAuth](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth) GitHub project demonstrates this approach:</span></span>
  * <span data-ttu-id="96580-124">Der Autorisierungs Filter wird von " [autorisierungshandlerfilter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizePageHandlerFilter.cs) " implementiert:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="96580-124">The [AuthorizePageHandlerFilter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizePageHandlerFilter.cs) implements the authorization filter: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs?name=snippet)]</span></span>

  * <span data-ttu-id="96580-125">Das Attribut [[Autorität Page Handler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) wird auf den `OnGet` Seiten Handler angewendet:[!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="96580-125">The [[AuthorizePageHandler]](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/simple/samples/3.1/PageHandlerAuth/Pages/Index.cshtml.cs#L16) attribute is applied to the `OnGet` page handler: [!code-csharp[](~/security/authorization/simple/samples/3.1/PageHandlerAuth/AuthorizeIndexPageHandlerFilter.cs?name=snippet)]</span></span>

> [!WARNING]
> <span data-ttu-id="96580-126">Der [pagehandlerauth](https://github.com/pranavkm/PageHandlerAuth) -Beispiel Ansatz ist ***nicht***:</span><span class="sxs-lookup"><span data-stu-id="96580-126">The [PageHandlerAuth](https://github.com/pranavkm/PageHandlerAuth) sample approach does ***not***:</span></span>
> * <span data-ttu-id="96580-127">Verfassen Sie mit Autorisierungs Attributen, die auf die Seite, das Seiten Modell oder Global angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="96580-127">Compose with authorization attributes applied to the page, page model, or globally.</span></span> <span data-ttu-id="96580-128">Das Verfassen von Autorisierungs Attributen führt dazu, dass die Authentifizierung und Autorisierung mehrmals durchgeführt `AuthorizeAttribute` wird, wenn eine oder mehrere `AuthorizeFilter` Instanzen auch auf die Seite angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="96580-128">Composing authorization attributes results in authentication and authorization executing multiple times when you have one more `AuthorizeAttribute` or `AuthorizeFilter` instances also applied to the page.</span></span>
> * <span data-ttu-id="96580-129">Arbeiten Sie in Verbindung mit dem Rest ASP.net Core Authentifizierungs-und Autorisierungssystem.</span><span class="sxs-lookup"><span data-stu-id="96580-129">Work in conjunction with the rest of ASP.NET Core authentication and authorization system.</span></span> <span data-ttu-id="96580-130">Sie müssen überprüfen, ob dieser Ansatz für Ihre Anwendung ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="96580-130">You must verify using this approach works correctly for your application.</span></span>

<span data-ttu-id="96580-131">Es ist nicht geplant, den `AuthorizeAttribute` auf Razor Seiten Handlern zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="96580-131">There are no plans to support the `AuthorizeAttribute` on Razor Page handlers.</span></span> 
