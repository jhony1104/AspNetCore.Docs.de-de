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
# <a name="claims-based-authorization-in-aspnet-core"></a>Anspruchs basierte Autorisierung in ASP.net Core

<a name="security-authorization-claims-based"></a>

Wenn eine Identität erstellt wird, kann Ihr eine oder mehrere Ansprüche zugewiesen werden, die von einer vertrauenswürdigen Partei ausgegeben werden. Ein Anspruch ist ein Name-Wert-Paar, das den Betreff darstellt, nicht das, was der Betreff tun kann. Beispielsweise können Sie über die Lizenz eines Treibers verfügen, die von einer lokalen Führerschein Zertifizierungsstelle ausgestellt wurde. Die Lizenz Ihres Treibers hat das Geburtsdatum. In diesem Fall wäre der Anspruchs Name `DateOfBirth`. der Anspruchs Wert wäre z. b. das Geburtsdatum `8th June 1970`, und der Aussteller wäre die treibende Lizenz. Die Anspruchs basierte Autorisierung überprüft am einfachsten den Wert eines Anspruchs und ermöglicht den Zugriff auf eine Ressource basierend auf diesem Wert. Wenn Sie z. b. auf einen Nacht Club zugreifen möchten, könnte der Autorisierungs Prozess wie folgt lauten:

Der Door Security Officer wertet den Wert Ihres Geburts Geburts Anspruchs aus und gibt an, ob er dem Aussteller (der führerscheinbehörde) vertraut, bevor Sie Zugriff gewähren.

Eine Identität kann mehrere Ansprüche mit mehreren Werten enthalten und mehrere Ansprüche desselben Typs enthalten.

## <a name="adding-claims-checks"></a>Hinzufügen von Anspruchs Prüfungen

Anspruchs basierte Autorisierungs Überprüfungen sind deklarativ. der Entwickler bettet diese innerhalb Ihres Codes, für einen Controller oder eine Aktion innerhalb eines Controllers ein, gibt Ansprüche an, die der aktuelle Benutzer besitzen muss, und optional den Wert, den der Anspruch enthalten muss, um auf das angeforderte Ressource. Anspruchs Anforderungen sind Richtlinien basiert. der Entwickler muss eine Richtlinie erstellen und registrieren, die die Anspruchs Anforderungen ausdrückt.

Der einfachste Typ der Anspruchs Richtlinie sucht nach dem vorhanden sein eines Anspruchs und überprüft den Wert nicht.

Zuerst müssen Sie die Richtlinie erstellen und registrieren. Dies erfolgt im Rahmen der Autorisierungs Dienst Konfiguration, die normalerweise an `ConfigureServices()` in Ihrer *Startup.cs* -Datei beteiligt ist.

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

In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.

You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.

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

Most claims come with a value. You can specify a list of allowed values when creating the policy. The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.

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
### <a name="add-a-generic-claim-check"></a>Add a generic claim check

If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion). For more information, see [Using a func to fulfill a policy](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).

## <a name="multiple-policy-evaluation"></a>Multiple Policy Evaluation

If you apply multiple policies to a controller or action, then all policies must pass before access is granted. Beispiel:

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

In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller. However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.

If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).
