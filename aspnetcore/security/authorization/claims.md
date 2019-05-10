---
title: Anspruchsbasierte Autorisierung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Ansprüche von Überprüfungen für die Autorisierung in ASP.NET Core-Apps hinzufügen.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/claims
ms.openlocfilehash: 6b60ae5515819b017ab577f655ed91ee4d8ed0dd
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086156"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="408ee-103">Anspruchsbasierte Autorisierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="408ee-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="408ee-104">Wenn eine Identität erstellt, wird möglicherweise eine oder mehrere Ansprüche, die von einer vertrauenswürdigen Partei ausgestellt zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="408ee-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="408ee-105">Ein Anspruch ist, dass ein Name-Wert-Paar, das stellt, welche der Antragsteller dar ist, nicht was der Betreff möglich.</span><span class="sxs-lookup"><span data-stu-id="408ee-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="408ee-106">Sie möglicherweise z. B. ein Führerschein, von einem lokalen treibende Lizenz Zertifizierungsstelle ausgestellt.</span><span class="sxs-lookup"><span data-stu-id="408ee-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="408ee-107">Der Führerschein weist Ihr Geburtsdatum auf.</span><span class="sxs-lookup"><span data-stu-id="408ee-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="408ee-108">In diesem Fall wäre der Anspruchsname `DateOfBirth`, der Wert des Anspruchs wäre Ihr Geburtsdatum, z. B. `8th June 1970` und der Aussteller wäre die treibende Autorität für die Lizenz.</span><span class="sxs-lookup"><span data-stu-id="408ee-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="408ee-109">Im einfachsten Fall anspruchsbasierte Autorisierung überprüft den Wert des Anspruchs und ermöglicht den Zugriff auf eine Ressource auf Grundlage dieses Werts.</span><span class="sxs-lookup"><span data-stu-id="408ee-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="408ee-110">Für das Beispiel, wenn Sie möchten den Autorisierungsprozess Zugriff auf eine Club Nacht sein kann:</span><span class="sxs-lookup"><span data-stu-id="408ee-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="408ee-111">Die Tür Sicherheitsbeauftragten ergibt den Wert für Ihr Geburtsdatum-Anspruch und gibt an, ob sie den Aussteller (die treibende Lizenz Authority) vertrauen, bevor gewährt wird, dass Sie Zugriff auf.</span><span class="sxs-lookup"><span data-stu-id="408ee-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="408ee-112">Eine Identität kann mehrere Ansprüche mit mehreren Werten enthalten, und es kann mehrere Ansprüche vom selben Typ enthalten.</span><span class="sxs-lookup"><span data-stu-id="408ee-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="408ee-113">Hinzufügen von Anspruchsanbieter-Überprüfungen</span><span class="sxs-lookup"><span data-stu-id="408ee-113">Adding claims checks</span></span>

<span data-ttu-id="408ee-114">Anspruch basierend autorisierungsprüfungen sind deklarative – der Entwickler bettet diese in ihren Code für einen Controller oder einer Aktion innerhalb eines Controllers angeben von Ansprüchen, die der aktuelle Benutzer muss über verfügen und optional des Werts der Forderung muss für den Zugriff auf die angeforderte Ressource.</span><span class="sxs-lookup"><span data-stu-id="408ee-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="408ee-115">Ansprüche an, dass Anforderungen richtlinienbasierten sind, muss der Entwickler erstellen und registrieren eine Richtlinie, die die Ansprüche Anforderungen ausgedrückt.</span><span class="sxs-lookup"><span data-stu-id="408ee-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="408ee-116">Die einfachste Art des Anspruchs Richtlinie sieht auf das Vorhandensein eines Anspruchs und überprüfen Sie den Wert nicht.</span><span class="sxs-lookup"><span data-stu-id="408ee-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="408ee-117">Zunächst müssen Sie zum Erstellen und registrieren Sie die Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="408ee-117">First you need to build and register the policy.</span></span> <span data-ttu-id="408ee-118">Hierbei handelt es sich als Teil der Dienstkonfiguration Autorisierung, das normalerweise in wird `ConfigureServices()` in Ihre *"Startup.cs"* Datei.</span><span class="sxs-lookup"><span data-stu-id="408ee-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

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

<span data-ttu-id="408ee-119">In diesem Fall die `EmployeeOnly` Richtlinie überprüft das Vorhandensein einer `EmployeeNumber` Anspruch auf die aktuelle Identität.</span><span class="sxs-lookup"><span data-stu-id="408ee-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="408ee-120">Wenden Sie dann die Richtlinie mithilfe der `Policy` Eigenschaft für die `AuthorizeAttribute` Attribut, um die Namen der Richtlinie; anzugeben.</span><span class="sxs-lookup"><span data-stu-id="408ee-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="408ee-121">Die `AuthorizeAttribute` Attribut kann auf einen gesamten Controller, in dieser Instanz angewendet werden, nur die Abgleichsrichtlinie Identitäten Zugriff auf eine beliebige Aktion auf dem Controller möglich ist.</span><span class="sxs-lookup"><span data-stu-id="408ee-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="408ee-122">Wenn Sie einen Controller verfügen, die von geschützt ist die `AuthorizeAttribute` Attribut jedoch anonymen Zugriff auf bestimmte Aktionen zulassen Sie anwenden möchten, dass die `AllowAnonymousAttribute` Attribut.</span><span class="sxs-lookup"><span data-stu-id="408ee-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

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

<span data-ttu-id="408ee-123">Die meisten Erklärungen verfügen über einen Wert.</span><span class="sxs-lookup"><span data-stu-id="408ee-123">Most claims come with a value.</span></span> <span data-ttu-id="408ee-124">Sie können eine Liste der zulässigen Werte angeben, wenn Sie die Richtlinie zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="408ee-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="408ee-125">Im folgende Beispiel wird nur für Mitarbeiter erfolgreich durchgeführt, dessen Mitarbeiter 1, 2, 3, 4 oder 5 wurden.</span><span class="sxs-lookup"><span data-stu-id="408ee-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

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

### <a name="add-a-generic-claim-check"></a><span data-ttu-id="408ee-126">Hinzufügen einer generischen Anspruch-Prüfung</span><span class="sxs-lookup"><span data-stu-id="408ee-126">Add a generic claim check</span></span>

<span data-ttu-id="408ee-127">Wenn der Wert des Anspruchs keinen einzelnen Wert aus, oder eine Transformation erforderlich ist, verwenden Sie [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span><span class="sxs-lookup"><span data-stu-id="408ee-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="408ee-128">Weitere Informationen finden Sie unter [eine Funktion verwenden, um eine Richtlinie zu erfüllen](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span><span class="sxs-lookup"><span data-stu-id="408ee-128">For more information, see [Using a func to fulfill a policy](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="408ee-129">Mehrere Richtlinienauswertung</span><span class="sxs-lookup"><span data-stu-id="408ee-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="408ee-130">Wenn Sie mehrere Richtlinien auf einen Controller oder eine Aktion anwenden, müssen alle Richtlinien übergeben, bevor der Zugriff gewährt wird.</span><span class="sxs-lookup"><span data-stu-id="408ee-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="408ee-131">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="408ee-131">For example:</span></span>

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

<span data-ttu-id="408ee-132">Im obigen Beispiel eine Identität, die erfüllt die `EmployeeOnly` Richtlinie kann Zugriff auf die `Payslip` erreichen Sie, wenn diese Richtlinie wird erzwungen, auf dem Controller.</span><span class="sxs-lookup"><span data-stu-id="408ee-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="408ee-133">Jedoch zum Aufrufen der `UpdateSalary` Aktion, die die Identität muss erfüllen *sowohl* der `EmployeeOnly` Richtlinie und die `HumanResources` Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="408ee-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="408ee-134">Sie kompliziertere Richtlinien können z.B. das Erstellen von eines Datums Geburtsdatum Anspruchs, berechnen ein Alter aus, und klicken Sie dann überprüfen das Alter 21 oder ältere wird Sie schreiben müssen [benutzerdefinierte Richtlinie Handler](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="408ee-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
