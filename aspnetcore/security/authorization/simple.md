---
<span data-ttu-id="ee5b7-101">Title: einfache Autorisierung in ASP.net Core Author: Rick-Anderson Description: erfahren Sie, wie Sie das Attribut "autorisieren" verwenden, um den Zugriff auf ASP.net Core Controller und Aktionen einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="ee5b7-101">title: Simple authorization in ASP.NET Core author: rick-anderson description: Learn how to use the Authorize attribute to restrict access to ASP.NET Core controllers and actions.</span></span>
<span data-ttu-id="ee5b7-102">ms. Author: Riande ms. Date: 10/14/2016 NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="ee5b7-102">ms.author: riande ms.date: 10/14/2016 no-loc:</span></span>
- <span data-ttu-id="ee5b7-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ee5b7-103">'Blazor'</span></span>
- <span data-ttu-id="ee5b7-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ee5b7-104">'Identity'</span></span>
- <span data-ttu-id="ee5b7-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ee5b7-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="ee5b7-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ee5b7-106">'Razor'</span></span>
- <span data-ttu-id="ee5b7-107">' SignalR ' UID: Security/Authorization/Simple</span><span class="sxs-lookup"><span data-stu-id="ee5b7-107">'SignalR' uid: security/authorization/simple</span></span>

---
# <a name="simple-authorization-in-aspnet-core"></a><span data-ttu-id="ee5b7-108">Einfache Autorisierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ee5b7-108">Simple authorization in ASP.NET Core</span></span>

<a name="security-authorization-simple"></a>

<span data-ttu-id="ee5b7-109">Die Autorisierung in ASP.net Core wird mit <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> und den verschiedenen Parametern gesteuert.</span><span class="sxs-lookup"><span data-stu-id="ee5b7-109">Authorization in ASP.NET Core is controlled with <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> and its various parameters.</span></span> <span data-ttu-id="ee5b7-110">In seiner einfachsten Form schränkt das Anwenden des- `[Authorize]` Attributs auf einen Controller, eine Aktion oder eine Razor Seite den Zugriff auf diese Komponente auf einen beliebigen authentifizierten Benutzer ein.</span><span class="sxs-lookup"><span data-stu-id="ee5b7-110">In its simplest form, applying the `[Authorize]` attribute to a controller, action, or Razor Page, limits access to that component to any authenticated user.</span></span>

<span data-ttu-id="ee5b7-111">Der folgende Code schränkt z. b. den Zugriff auf `AccountController` alle authentifizierten Benutzer ein.</span><span class="sxs-lookup"><span data-stu-id="ee5b7-111">For example, the following code limits access to the `AccountController` to any authenticated user.</span></span>

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

<span data-ttu-id="ee5b7-112">Wenn Sie die Autorisierung auf eine Aktion anstelle des Controllers anwenden möchten, wenden Sie das- `AuthorizeAttribute` Attribut auf die eigentliche Aktion an:</span><span class="sxs-lookup"><span data-stu-id="ee5b7-112">If you want to apply authorization to an action rather than the controller, apply the `AuthorizeAttribute` attribute to the action itself:</span></span>

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

<span data-ttu-id="ee5b7-113">Jetzt können nur authentifizierte Benutzer auf die `Logout` Funktion zugreifen.</span><span class="sxs-lookup"><span data-stu-id="ee5b7-113">Now only authenticated users can access the `Logout` function.</span></span>

<span data-ttu-id="ee5b7-114">Sie können auch das- `AllowAnonymous` Attribut verwenden, um den Zugriff durch nicht authentifizierte Benutzer auf einzelne Aktionen zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="ee5b7-114">You can also use the `AllowAnonymous` attribute to allow access by non-authenticated users to individual actions.</span></span> <span data-ttu-id="ee5b7-115">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ee5b7-115">For example:</span></span>

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

<span data-ttu-id="ee5b7-116">Dies erlaubt nur authentifizierte Benutzer, `AccountController` außer bei der `Login` Aktion, auf die jeder zugreifen kann, unabhängig vom authentifizierten oder nicht authentifizierten/anonymen Status.</span><span class="sxs-lookup"><span data-stu-id="ee5b7-116">This would allow only authenticated users to the `AccountController`, except for the `Login` action, which is accessible by everyone, regardless of their authenticated or unauthenticated / anonymous status.</span></span>

> [!WARNING]
> <span data-ttu-id="ee5b7-117">`[AllowAnonymous]`umgeht alle Autorisierungs Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="ee5b7-117">`[AllowAnonymous]` bypasses all authorization statements.</span></span> <span data-ttu-id="ee5b7-118">Wenn Sie `[AllowAnonymous]` und ein beliebiges Attribut kombinieren `[Authorize]` , `[Authorize]` werden die Attribute ignoriert.</span><span class="sxs-lookup"><span data-stu-id="ee5b7-118">If you combine `[AllowAnonymous]` and any `[Authorize]` attribute, the `[Authorize]` attributes are ignored.</span></span> <span data-ttu-id="ee5b7-119">Wenn Sie z. b `[AllowAnonymous]` . auf Controller Ebene anwenden, werden alle `[Authorize]` Attribute auf dem gleichen Controller (oder in einer beliebigen Aktion) ignoriert.</span><span class="sxs-lookup"><span data-stu-id="ee5b7-119">For example if you apply `[AllowAnonymous]` at the controller level, any `[Authorize]` attributes on the same controller (or on any action within it) is ignored.</span></span>
