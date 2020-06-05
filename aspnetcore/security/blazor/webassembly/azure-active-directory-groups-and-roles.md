---
title: ASP.net Core Blazor Webassembly mit Azure Active Directory Gruppen und Rollen
author: guardrex
description: Erfahren Sie, wie Sie Blazor Webassembly für die Verwendung von Azure Active Directory Gruppen und Rollen konfigurieren.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/aad-groups-roles
ms.openlocfilehash: 3ed06cca7e20da381b870e642a6c616b2578cd0a
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451874"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Azure Ad Gruppen, administrative Rollen und benutzerdefinierte Rollen

Von [Luke Latham](https://github.com/guardrex) und [Javier calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) bietet mehrere Autorisierungs Ansätze, die mit ASP.net Core kombiniert werden können Identity :

* Benutzerdefinierte Gruppen
  * Sicherheit
  * O365
  * Distribution
* Rollen
  * Integrierte Verwaltungs Rollen
  * Benutzerdefinierte Rollen

Die Anleitung in diesem Artikel gilt für die Blazor Aad-Bereitstellungs Szenarios der Webassembly, die in den folgenden Themen beschrieben werden:

* [Eigenständig mit Microsoft-Konten](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [Eigenständig mit AAD](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [Gehostet mit AAD](xref:security/blazor/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a>Benutzerdefinierte Gruppen und integrierte Verwaltungs Rollen

Informationen zum Konfigurieren der APP im Azure-Portal für die Bereitstellung eines `groups` Mitgliedschafts Anspruchs finden Sie in den folgenden Azure-Artikeln. Zuweisen von Benutzern zu benutzerdefinierten Aad-Gruppen und integrierten Administrator Rollen

* [Rollen auf Grundlage von Azure AD-Sicherheitsgruppen](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [groupMembershipClaims-Attribut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

In den folgenden Beispielen wird davon ausgegangen, dass ein Benutzer der integrierten Aad- *Abrechnungs Administrator* Rolle zugewiesen ist.

Der `groups` von Aad gesendete einzelne Anspruch zeigt die Gruppen und Rollen des Benutzers als Objekt-IDs (GUIDs) in einem JSON-Array an. Die APP muss das JSON-Array von Gruppen und Rollen in einzelne `group` Ansprüche konvertieren, für die die APP [Richtlinien](xref:security/authorization/policies) erstellen kann.

Erweitern <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> Sie diese Eigenschaft, um Array Eigenschaften für Gruppen und Rollen einzubeziehen.

*CustomUserAccount.cs*:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; }

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; }
}
```

Erstellen Sie eine benutzerdefinierte benutzerfactory in der eigenständigen APP oder Client-App einer gehosteten Lösung. Die folgende Factory ist auch für die Behandlung von `roles` Anspruchs Arrays konfiguriert, die im Abschnitt " [benutzerdefinierte Rollen](#user-defined-roles) " behandelt werden:

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomUserFactory(NavigationManager navigationManager,
        IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("role", role));
            }

            foreach (var group in account.Groups)
            {
                userIdentity.AddClaim(new Claim("group", group));
            }
        }

        return initialUser;
    }
}
```

Es muss kein Code bereitgestellt werden, um den ursprünglichen Anspruch zu entfernen, `groups` da er automatisch vom Framework entfernt wird.

Registrieren Sie die Factory in `Program.Main` (*Program.cs*) der eigenständigen APP-oder Client-App einer gehosteten Lösung:

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");
    
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

Erstellen Sie eine [Richtlinie](xref:security/authorization/policies) für jede Gruppe oder Rolle in `Program.Main` . Im folgenden Beispiel wird eine Richtlinie für die integrierte Aad- *Abrechnungs Administrator* Rolle erstellt:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Eine umfassende Liste der Aad-Rollen Objekt-IDs finden Sie im Abschnitt [Aad Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) .

In den folgenden Beispielen verwendet die APP die vorangehende Richtlinie, um den Benutzer zu autorisieren.

Die [Autorität für die autorizeview](xref:security/blazor/index#authorizeview-component) funktioniert mit der Richtlinie:

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

Der Zugriff auf eine gesamte Komponente kann auf der Richtlinie mithilfe der [ `[Authorize]` ]-Attribut Direktive] (Xref: Security/blazor/Index # Autorisierungs Attribut) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) basieren:

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Wenn der Benutzer nicht angemeldet ist, wird er auf die Aad-Anmeldeseite umgeleitet und dann zurück zur Komponente, nachdem Sie sich angemeldet haben.

Eine Richtlinien Überprüfung kann auch [in Code mit prozeduraler Logik ausgeführt](xref:security/blazor/index#procedural-logic)werden:

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

### <a name="user-defined-roles"></a>Benutzerdefinierte Rollen

Eine Aad-registrierte App kann auch so konfiguriert werden, dass benutzerdefinierte Rollen verwendet werden.

Informationen zum Konfigurieren der APP im Azure-Portal für die Bereitstellung eines `roles` Mitgliedschafts Anspruchs finden Sie unter Gewusst [wie: Hinzufügen von App-Rollen in Ihrer Anwendung und empfangen der APP im Token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in der Azure-Dokumentation.

Im folgenden Beispiel wird davon ausgegangen, dass eine APP mit zwei Rollen konfiguriert ist:

* `admin`
* `developer`

> [!NOTE]
> Obwohl Sie Sicherheitsgruppen ohne ein Azure AD Premium Konto keine Rollen zuweisen können, können Sie Benutzern Rollen zuweisen und einen `roles` Anspruch für Benutzer mit einem standardmäßigen Azure-Konto erhalten. Die Anleitung in diesem Abschnitt erfordert kein Azure AD Premium Konto.
>
> Mehrere Rollen werden in der Azure-Portal zugewiesen, indem ein Benutzer für jede weitere Rollenzuweisung **_neu hinzugefügt_** wird.

Der `roles` von Aad gesendete einzelne Anspruch zeigt die benutzerdefinierten Rollen als `appRoles` `value` s in einem JSON-Array an. Die APP muss das JSON-Array von Rollen in einzelne `role` Ansprüche konvertieren.

Der `CustomUserFactory` im Abschnitt [benutzerdefinierte Gruppen und integrierte Aad-Administrator Rollen](#user-defined-groups-and-built-in-administrative-roles) angezeigte Vorgang ist so eingerichtet, dass er für einen `roles` Anspruch mit einem JSON-Array Wert ausgeführt wird. Fügen Sie die `CustomUserFactory` in der eigenständigen APP oder Client-App einer gehosteten Lösung hinzu, und registrieren Sie Sie, wie im Abschnitt [benutzerdefinierte Gruppen und integrierte Aad-Verwaltungs Rollen](#user-defined-groups-and-built-in-administrative-roles) beschrieben. Es muss kein Code bereitgestellt werden, um den ursprünglichen Anspruch zu entfernen, `roles` da er automatisch vom Framework entfernt wird.

Geben Sie in der `Program.Main` eigenständigen APP oder Client-App einer gehosteten Lösung den Anspruch mit dem Namen " `role` " als Rollen Anspruch an:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

An dieser Stelle sind die Komponenten Autorisierungs Ansätze funktionsfähig. Alle Autorisierungs Mechanismen in-Komponenten können die- `admin` Rolle verwenden, um den Benutzer zu autorisieren:

* [Autorität View-Komponente](xref:security/blazor/index#authorizeview-component) (Beispiel: `<AuthorizeView Roles="admin">` )
* [ `[Authorize]` ]-Attribut Direktive] (Xref: Security/blazor/Index # autorisieren-Attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (Beispiel: `@attribute [Authorize(Roles = "admin")]` )
* [Prozedurale Logik](xref:security/blazor/index#procedural-logic) (Beispiel: `if (user.IsInRole("admin")) { ... }` )

  Mehrere Rollen Tests werden unterstützt:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>Rollen Gruppen-IDs für Aad-Administratoren

Die Objekt-IDs, die in der folgenden Tabelle dargestellt werden, dienen zum Erstellen von [Richtlinien](xref:security/authorization/policies) für `group` Ansprüche. Mit Richtlinien ist es einer APP gestattet, Benutzer für verschiedene Aktivitäten in einer APP zu autorisieren. Weitere Informationen finden Sie im Abschnitt [benutzerdefinierte Gruppen und integrierte Aad-Administrator Rollen](#user-defined-groups-and-built-in-administrative-roles) .

Aad-Administrator Rolle | ObjectID
--- | ---
Anwendungsadministrator | fa11557b-4f15-4ddd-85d5-313c7cd74047
Anwendungsentwickler | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Authentifizierungsadministrator | 02d110a1-96b1-419e-af87-746461b60ed7
Azure DevOps-Administrator | a5311ace-ca41-44cd-b833-8d22caa0b34f
Azure Information Protection-Administrator | 18632dce-sp9b5-4f 01-ABB5-37051f 06860e
B2C-IEF-keysetadministrator | 0c2e87e5-94b9-4adb-AE8C-bcafe11bd368
B2C-IEF-Richtlinien Administrator | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
B2C-Benutzer Fluss Administrator | baa531b7-8cf0-44ad-8f98-eded88dae827
B2C-Benutzer Fluss Attribut-Administrator | dd0baca0-a535-48c1-b871-8431abe16452
Rechnungsadministrator | 69ff516a-b57d-4697-A429-9DE 4af7b5609
Cloudanwendungsadministrator | 250b5fe3-B553-458D-9a53-b782c13c34bf
Cloudgeräteadministrator | 26cd4b44-2636-4ddb-BDFA-27feae66f 86D
Complianceadministrator | 9d6e1dd0-c9b8-45b8-b558-b134f 700116c
Compliancedatenadministrator | 4c0ca3a2-231E-416c-9411-4abe57d5cb9d
Administrator für den bedingten Zugriff | 8f 71a611-137d-49af-87ad-e97f 1F-5da76
Genehmigende Person für den LockBox-Kundenzugriff | c18d54a8-b13e-4954-a1a4-7deaf2e4f 184
Desktop Analytics-Administrator | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Rolle „Verzeichnis lesen“ | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Dynamics 365-Administrator | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Exchange-Administrator | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
Externer Identity Anbieter Administrator | febfaeb4-e478-407a-b4b3-f4d9716618a2
Globaler Administrator | a45ba61b-44db-462c-924b-3b2719152588
Globaler Leser | f6903b21-6aba-4124-b44c-76671796b9d5
Gruppenadministrator | 158 b3e5a-d89d-460b-92b5-3b34985-l0197
Gasteinladender | 4c730a1d-cc22-44af-8-Datei-eec635c7502
Helpdesk-Administrator | 108678c8-6628-44e1-8d01-caf598a6a5f 5
Intune-Administrator | 79950741-23fa-4189-b2cb-46640601c497
Kaizala-Administrator | d6322af2-48e7-42e0-8c68-0bbe31af3412
Lizenzadministrator | 3355458a-E423-44bf -8b98-4ac5e572cea5
Nachrichtencenter-Datenschutzleseberechtigter | 6395db95-9lb8-42b9-b1ed-30a2405eee6f
Nachrichtencenter-Leser | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Office-Apps-Administrator | 5F 3870cd-B042-4t93-86d7-c9d77c664dc7
Kennwortadministrator | 466e48b7-5d66-4ae5-8911-1a118debug
Power BI-Administrator | 984e83b8-8337-4255-91a1-acb663175ab4
Power Platform-Administrator | 76d6b95e-9A15-4d7d-8D21-00be00f FD
Privilegierter Authentifizierungsadministrator | 0829-b46d-419f-9742-aeb122367d11
Administrator für privilegierte Rollen | f20a725a-d1c8-4107-83ea-1171c97d00c7
Berichtsleser | 54635450-e8ed-4b2d-9632-07db2517b4de
Suchadministrator | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Such-Editor | 6a6858c6-5-Datei (c)
Sicherheitsadministrator | 20fa50e3-6531-44d8-bd39-b251420568ad
Sicherheitsoperator | 43aae017-8e51-4188-91ab-e6debd572800
Sicherheitsleseberechtigter | 45035cd3-sd97-4250-8197-3a53d3562d9b
Dienstunterstützungsadministrator | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
SharePoint-Administrator | e1c32229-875e-461d-ae24-3cb99116e86c
Skype for Business-Administrator | 0a8cee12-e21d-43ef-abd9-f 1 ea85710e30
Teams-Kommunikationsadministrator | 2393e455-6e13-4743-9b52-63F b6a9c
Teams-Kommunikationssupporttechniker | 802dd94e-d717-46f6-af98-b9167071e9fc
Teams Communications Specialist | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Teams-Dienstadministrator | 8846a0be-197b-443a-b13c-11192691fa24
Benutzeradministrator | 1F 6eed58-7dd3-460b-a298-666f 975427a1

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/claims>
* <xref:security/blazor/index>
