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
# <a name="claims-based-authorization-in-aspnet-core"></a>Anspruchsbasierte Autorisierung in ASP.NET Core

<a name="security-authorization-claims-based"></a>

Wenn eine Identität erstellt, wird möglicherweise eine oder mehrere Ansprüche, die von einer vertrauenswürdigen Partei ausgestellt zugewiesen werden. Ein Anspruch ist, dass ein Name-Wert-Paar, das stellt, welche der Antragsteller dar ist, nicht was der Betreff möglich. Sie möglicherweise z. B. ein Führerschein, von einem lokalen treibende Lizenz Zertifizierungsstelle ausgestellt. Der Führerschein weist Ihr Geburtsdatum auf. In diesem Fall wäre der Anspruchsname `DateOfBirth`, der Wert des Anspruchs wäre Ihr Geburtsdatum, z. B. `8th June 1970` und der Aussteller wäre die treibende Autorität für die Lizenz. Im einfachsten Fall anspruchsbasierte Autorisierung überprüft den Wert des Anspruchs und ermöglicht den Zugriff auf eine Ressource auf Grundlage dieses Werts. Für das Beispiel, wenn Sie möchten den Autorisierungsprozess Zugriff auf eine Club Nacht sein kann:

Die Tür Sicherheitsbeauftragten ergibt den Wert für Ihr Geburtsdatum-Anspruch und gibt an, ob sie den Aussteller (die treibende Lizenz Authority) vertrauen, bevor gewährt wird, dass Sie Zugriff auf.

Eine Identität kann mehrere Ansprüche mit mehreren Werten enthalten, und es kann mehrere Ansprüche vom selben Typ enthalten.

## <a name="adding-claims-checks"></a>Hinzufügen von Anspruchsanbieter-Überprüfungen

Anspruch basierend autorisierungsprüfungen sind deklarative – der Entwickler bettet diese in ihren Code für einen Controller oder einer Aktion innerhalb eines Controllers angeben von Ansprüchen, die der aktuelle Benutzer muss über verfügen und optional des Werts der Forderung muss für den Zugriff auf die angeforderte Ressource. Ansprüche an, dass Anforderungen richtlinienbasierten sind, muss der Entwickler erstellen und registrieren eine Richtlinie, die die Ansprüche Anforderungen ausgedrückt.

Die einfachste Art des Anspruchs Richtlinie sieht auf das Vorhandensein eines Anspruchs und überprüfen Sie den Wert nicht.

Zunächst müssen Sie zum Erstellen und registrieren Sie die Richtlinie. Hierbei handelt es sich als Teil der Dienstkonfiguration Autorisierung, das normalerweise in wird `ConfigureServices()` in Ihre *"Startup.cs"* Datei.

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

In diesem Fall die `EmployeeOnly` Richtlinie überprüft das Vorhandensein einer `EmployeeNumber` Anspruch auf die aktuelle Identität.

Wenden Sie dann die Richtlinie mithilfe der `Policy` Eigenschaft für die `AuthorizeAttribute` Attribut, um die Namen der Richtlinie; anzugeben.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

Die `AuthorizeAttribute` Attribut kann auf einen gesamten Controller, in dieser Instanz angewendet werden, nur die Abgleichsrichtlinie Identitäten Zugriff auf eine beliebige Aktion auf dem Controller möglich ist.

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

Wenn Sie einen Controller verfügen, die von geschützt ist die `AuthorizeAttribute` Attribut jedoch anonymen Zugriff auf bestimmte Aktionen zulassen Sie anwenden möchten, dass die `AllowAnonymousAttribute` Attribut.

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

Die meisten Erklärungen verfügen über einen Wert. Sie können eine Liste der zulässigen Werte angeben, wenn Sie die Richtlinie zu erstellen. Im folgende Beispiel wird nur für Mitarbeiter erfolgreich durchgeführt, dessen Mitarbeiter 1, 2, 3, 4 oder 5 wurden.

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

### <a name="add-a-generic-claim-check"></a>Hinzufügen einer generischen Anspruch-Prüfung

Wenn der Wert des Anspruchs keinen einzelnen Wert aus, oder eine Transformation erforderlich ist, verwenden Sie [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion). Weitere Informationen finden Sie unter [eine Funktion verwenden, um eine Richtlinie zu erfüllen](xref:security/authorization/policies#using-a-func-to-fulfill-a-policy).

## <a name="multiple-policy-evaluation"></a>Mehrere Richtlinienauswertung

Wenn Sie mehrere Richtlinien auf einen Controller oder eine Aktion anwenden, müssen alle Richtlinien übergeben, bevor der Zugriff gewährt wird. Zum Beispiel:

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

Im obigen Beispiel eine Identität, die erfüllt die `EmployeeOnly` Richtlinie kann Zugriff auf die `Payslip` erreichen Sie, wenn diese Richtlinie wird erzwungen, auf dem Controller. Jedoch zum Aufrufen der `UpdateSalary` Aktion, die die Identität muss erfüllen *sowohl* der `EmployeeOnly` Richtlinie und die `HumanResources` Richtlinie.

Sie kompliziertere Richtlinien können z.B. das Erstellen von eines Datums Geburtsdatum Anspruchs, berechnen ein Alter aus, und klicken Sie dann überprüfen das Alter 21 oder ältere wird Sie schreiben müssen [benutzerdefinierte Richtlinie Handler](xref:security/authorization/policies).
