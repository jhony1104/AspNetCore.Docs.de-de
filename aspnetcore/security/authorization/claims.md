---
title: Anspruchs basierte Autorisierung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Anspruchs Überprüfungen zur Autorisierung in einer ASP.net Core-app hinzufügen.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/claims
ms.openlocfilehash: e289851aafcbc7e3b3f60ab9fbe4b182a78bdf8a
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143428"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="ca66e-103">Anspruchs basierte Autorisierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ca66e-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="ca66e-104">Wenn eine Identität erstellt wird, kann Ihr eine oder mehrere Ansprüche zugewiesen werden, die von einer vertrauenswürdigen Partei ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="ca66e-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="ca66e-105">Ein Anspruch ist ein Name-Wert-Paar, das den Betreff darstellt, nicht das, was der Betreff tun kann.</span><span class="sxs-lookup"><span data-stu-id="ca66e-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="ca66e-106">Beispielsweise können Sie über die Lizenz eines Treibers verfügen, die von einer lokalen Führerschein Zertifizierungsstelle ausgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="ca66e-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="ca66e-107">Die Lizenz Ihres Treibers hat das Geburtsdatum.</span><span class="sxs-lookup"><span data-stu-id="ca66e-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="ca66e-108">In diesem Fall wäre der Anspruchs Name `DateOfBirth`. der Anspruchs Wert wäre z. b. das Geburtsdatum `8th June 1970`, und der Aussteller wäre die treibende Lizenz.</span><span class="sxs-lookup"><span data-stu-id="ca66e-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="ca66e-109">Die Anspruchs basierte Autorisierung überprüft am einfachsten den Wert eines Anspruchs und ermöglicht den Zugriff auf eine Ressource basierend auf diesem Wert.</span><span class="sxs-lookup"><span data-stu-id="ca66e-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="ca66e-110">Wenn Sie z. b. auf einen Nacht Club zugreifen möchten, könnte der Autorisierungs Prozess wie folgt lauten:</span><span class="sxs-lookup"><span data-stu-id="ca66e-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="ca66e-111">Der Door Security Officer wertet den Wert Ihres Geburts Geburts Anspruchs aus und gibt an, ob er dem Aussteller (der führerscheinbehörde) vertraut, bevor Sie Zugriff gewähren.</span><span class="sxs-lookup"><span data-stu-id="ca66e-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="ca66e-112">Eine Identität kann mehrere Ansprüche mit mehreren Werten enthalten und mehrere Ansprüche desselben Typs enthalten.</span><span class="sxs-lookup"><span data-stu-id="ca66e-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="ca66e-113">Hinzufügen von Anspruchs Prüfungen</span><span class="sxs-lookup"><span data-stu-id="ca66e-113">Adding claims checks</span></span>

<span data-ttu-id="ca66e-114">Anspruchs basierte Autorisierungs Überprüfungen sind deklarativ. der Entwickler bettet diese innerhalb Ihres Codes, für einen Controller oder eine Aktion innerhalb eines Controllers ein, gibt Ansprüche an, die der aktuelle Benutzer besitzen muss, und optional den Wert, den der Anspruch enthalten muss, um auf das angeforderte Ressource.</span><span class="sxs-lookup"><span data-stu-id="ca66e-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="ca66e-115">Anspruchs Anforderungen sind Richtlinien basiert. der Entwickler muss eine Richtlinie erstellen und registrieren, die die Anspruchs Anforderungen ausdrückt.</span><span class="sxs-lookup"><span data-stu-id="ca66e-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="ca66e-116">Der einfachste Typ der Anspruchs Richtlinie sucht nach dem vorhanden sein eines Anspruchs und überprüft den Wert nicht.</span><span class="sxs-lookup"><span data-stu-id="ca66e-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="ca66e-117">Zuerst müssen Sie die Richtlinie erstellen und registrieren.</span><span class="sxs-lookup"><span data-stu-id="ca66e-117">First you need to build and register the policy.</span></span> <span data-ttu-id="ca66e-118">Dies erfolgt im Rahmen der Autorisierungs Dienst Konfiguration, die normalerweise an `ConfigureServices()` in Ihrer *Startup.cs* -Datei beteiligt ist.</span><span class="sxs-lookup"><span data-stu-id="ca66e-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

<span data-ttu-id="ca66e-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span><span class="sxs-lookup"><span data-stu-id="ca66e-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="ca66e-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span><span class="sxs-lookup"><span data-stu-id="ca66e-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="ca66e-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span><span class="sxs-lookup"><span data-stu-id="ca66e-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="ca66e-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span><span class="sxs-lookup"><span data-stu-id="ca66e-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

<span data-ttu-id="ca66e-123">Most claims come with a value.</span><span class="sxs-lookup"><span data-stu-id="ca66e-123">Most claims come with a value.</span></span> <span data-ttu-id="ca66e-124">You can specify a list of allowed values when creating the policy.</span><span class="sxs-lookup"><span data-stu-id="ca66e-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="ca66e-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span><span class="sxs-lookup"><span data-stu-id="ca66e-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a><span data-ttu-id="ca66e-126">Add a generic claim check</span><span class="sxs-lookup"><span data-stu-id="ca66e-126">Add a generic claim check</span></span>

<span data-ttu-id="ca66e-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span><span class="sxs-lookup"><span data-stu-id="ca66e-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="ca66e-128">For more information, see [Using a func to fulfill a policy](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span><span class="sxs-lookup"><span data-stu-id="ca66e-128">For more information, see [Using a func to fulfill a policy](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="ca66e-129">Multiple Policy Evaluation</span><span class="sxs-lookup"><span data-stu-id="ca66e-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="ca66e-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span><span class="sxs-lookup"><span data-stu-id="ca66e-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="ca66e-131">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ca66e-131">For example:</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

<span data-ttu-id="ca66e-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span><span class="sxs-lookup"><span data-stu-id="ca66e-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="ca66e-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span><span class="sxs-lookup"><span data-stu-id="ca66e-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="ca66e-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="ca66e-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
