---
title: Rollenbasierte Autorisierung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core-Controller und Aktion Zugriff einschränken, indem Sie die Rollen an das Authorize-Attribut übergeben.
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/roles
ms.openlocfilehash: 0e01e1976e2721ca64720a67c6341661f646395c
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209095"
---
# <a name="role-based-authorization-in-aspnet-core"></a>Rollenbasierte Autorisierung in ASP.NET Core

<a name="security-authorization-role-based"></a>

Wenn eine Identität erstellt wird möglicherweise eine oder mehrere Rollen angehören. Tracy kann z. B. den Administrator- und Rollen gehören, obwohl Scott nur für die Benutzerrolle angehören kann. Wie diese Rollen erstellt und verwaltet werden, hängt von der Sicherungsspeicher der Autorisierung ab. Rollen werden verfügbar gemacht, für den Entwickler über die ["IsInRole"](/dotnet/api/system.security.principal.genericprincipal.isinrole) Methode für die ["ClaimsPrincipal"](/dotnet/api/system.security.claims.claimsprincipal) Klasse.

## <a name="adding-role-checks"></a>Hinzufügen von rollenüberprüfungen

Rollenbasierte autorisierungsprüfungen sind deklarativ&mdash;Entwickler bettet diese in ihren Code für einen Controller oder einer Aktion innerhalb eines Controllers angeben von Rollen, die der aktuelle Benutzer ein Mitglied sein muss, um die angeforderte Ressource zuzugreifen.

Z. B. der folgende Code, schränkt den Zugriff auf alle Aktionen auf der `AdministrationController` für Benutzer sind, die ein Mitglied der `Administrator` Rolle:

```csharp
[Authorize(Roles = "Administrator")]
public class AdministrationController : Controller
{
}
```

Sie können mehrere Rollen als durch Trennzeichen getrennte Liste angeben:

```csharp
[Authorize(Roles = "HRManager,Finance")]
public class SalaryController : Controller
{
}
```

Dieser Controller nur zugegriffen werden von Benutzern, die Mitglieder sind von der `HRManager` Rolle oder die `Finance` Rolle.

Wenn Sie mehrere Attribute anwenden, und klicken Sie dann ein Zugriff auf Benutzer ein Mitglied aller angegebenen Rollen sein muss; Das folgende Beispiel erfordert, dass ein Benutzer muss ein Mitglied sowohl der `PowerUser` und `ControlPanelUser` Rolle.

```csharp
[Authorize(Roles = "PowerUser")]
[Authorize(Roles = "ControlPanelUser")]
public class ControlPanelController : Controller
{
}
```

Sie können den Zugriff weiter einzuschränken, durch zusätzliche Rolle Authorization-Attribute auf der Aktionsebene anwenden:

```csharp
[Authorize(Roles = "Administrator, PowerUser")]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [Authorize(Roles = "Administrator")]
    public ActionResult ShutDown()
    {
    }
}
```

In den vorherigen Code Snippet Membern der der `Administrator` Rolle oder die `PowerUser` Rolle kann auf den Controller zugreifen und die `SetTime` Aktion, aber nur Mitglieder der der `Administrator` Rolle zugreifen kann die `ShutDown` Aktion.

Sie können auch einen Controller Sperren jedoch anonyme, nicht authentifizierte Zugriff auf einzelne Aktionen zulassen.

```csharp
[Authorize]
public class ControlPanelController : Controller
{
    public ActionResult SetTime()
    {
    }

    [AllowAnonymous]
    public ActionResult Login()
    {
    }
}
```

::: moniker range=">= aspnetcore-2.0"

Razor-Seiten die `AuthorizeAttribute` angewendet werden können, indem entweder:

* Mit einem [Konvention](xref:razor-pages/razor-pages-conventions#page-model-action-conventions), oder
* Anwenden der `AuthorizeAttribute` auf die `PageModel` Instanz:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public class UpdateModel : PageModel
{
    public ActionResult OnPost()
    {
    }
}
```

> [!IMPORTANT]
> Filtern Sie die Attribute, einschließlich `AuthorizeAttribute`, kann nur auf "pagemodel" angewendet werden und nicht auf bestimmte Seite Handlermethoden angewendet werden.
::: moniker-end

<a name="security-authorization-role-policy"></a>

## <a name="policy-based-role-checks"></a>Überprüfung der richtlinienbasierten-Funktion

Anforderungen an die Rolle können auch mithilfe der neuen Richtlinie-Syntax wird registriert, in denen ein Entwickler eine Richtlinie beim Start als Teil der Dienstkonfiguration für die Autorisierung ausgedrückt werden. Dieser Fehler tritt normalerweise in `ConfigureServices()` in Ihre *"Startup.cs"* Datei.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireAdministratorRole",
             policy => policy.RequireRole("Administrator"));
    });
}
```

Richtlinien werden angewendet, mit der `Policy` Eigenschaft für die `AuthorizeAttribute` Attribut:

```csharp
[Authorize(Policy = "RequireAdministratorRole")]
public IActionResult Shutdown()
{
    return View();
}
```

Wenn Sie eine Anforderung mehrere zulässige Rollen angeben möchten, und klicken Sie dann als Parameter zum Angeben der `RequireRole` Methode:

```csharp
options.AddPolicy("ElevatedRights", policy =>
                  policy.RequireRole("Administrator", "PowerUser", "BackupAdministrator"));
```

In diesem Beispiel autorisiert Benutzer Zugehörigkeit zu der `Administrator`, `PowerUser` oder `BackupAdministrator` Rollen.

### <a name="add-role-services-to-identity"></a>Fügen Sie Rollendienste hinzu, die Identität

Fügen Sie [Rollen](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) Rollendienste hinzufügen:

[!code-csharp[](roles/samples/Startup.cs?name=snippet&highlight=7)]
