---
title: ASP.NET Core Blazor WebAssembly mit Azure Active Directory-Gruppen und -Rollen
author: guardrex
description: Erfahren Sie, wie Sie Blazor WebAssembly für die Verwendung von Azure Active Directory-Gruppen und -Rollen konfigurieren.
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 99ebe43da191153aa98cce6bae8fe98035bc7d6f
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103288"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Gruppen, Administratorrollen und benutzerdefinierte Rollen aus Azure AD

Von [Luke Latham](https://github.com/guardrex) und [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) bietet mehrere Autorisierungsansätze, die mit ASP.NET Core Identity kombiniert werden können:

* Benutzerdefinierte Gruppen
  * Sicherheit
  * O365
  * Verteilung
* Rollen
  * Integrierte Administratorrollen
  * Benutzerdefinierte Rollen

Die Informationen in diesem Artikel beziehen sich auf die in den folgenden Artikeln beschriebenen Bereitstellungsszenarios für Blazor WebAssembly und AAD:

* [Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Microsoft-Konten](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a>Benutzerdefinierte Gruppen und integrierte Administratorrollen

Informationen zur Konfiguration der App im Azure-Portal für die Bereitstellung eines `groups`-Mitgliedschaftsanspruchs finden Sie in den folgenden Abschnitten in Azure-Artikeln. Weisen Sie Benutzer benutzerdefinierten AAD-Gruppen und integrierten AAD-Administratorrollen zu.

* [Rollen auf Grundlage von Azure AD-Sicherheitsgruppen](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [groupMembershipClaims-Attribut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

In den folgenden Beispielen wird davon ausgegangen, dass ein Benutzer der in AAD integrierten Rolle *Abrechnungsadministrator* zugewiesen ist.

Der von AAD gesendete einzelne `groups`-Anspruch zeigt die Gruppen und Rollen des Benutzers als Objekt-IDs (GUIDs) in einem JSON-Array an. Die App muss das JSON-Array mit Gruppen und Rollen in einzelne `group`-Ansprüche konvertieren, für die sie [Richtlinien](xref:security/authorization/policies) erstellen kann.

Erweitern Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> um Arrayeigenschaften für Gruppen und Rollen.

*CustomUserAccount.cs:*

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

Erstellen Sie in der eigenständigen App oder der Client-App einer gehosteten Lösung eine benutzerdefinierte Benutzerfactory. Die folgende Factory ist auch für die Verarbeitung von `roles`-Anspruchsarrays konfiguriert. Diese werden im Abschnitt [Benutzerdefinierte Rollen](#user-defined-roles) behandelt.

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

Für das Entfernen des ursprünglichen `groups`-Anspruchs muss kein Code bereitgestellt werden, da das Framework diesen automatisch entfernt.

Registrieren Sie die Factory in der eigenständigen App oder der Client-App einer gehosteten Lösung in `Program.Main` (*Program.cs*):

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

Erstellen Sie für jede Gruppe oder Rolle eine [Richtlinie](xref:security/authorization/policies) in `Program.Main`. Im folgenden Beispiel wird eine Richtlinie für die in AAD integrierte Rolle *Abrechnungsadministrator* erstellt:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Eine vollständige Liste der Objekt-IDs für die einzelnen AAD-Rollen finden Sie im Abschnitt [Gruppen-IDs für AAD-Administratorrollen](#aad-adminstrative-role-group-ids).

In den folgenden Beispielen verwendet die App die obige Richtlinie für die Autorisierung des Benutzers.

Die [AuthorizeView-Komponente](xref:blazor/security/index#authorizeview-component) verwendet die Richtlinie:

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

Durch Verwendung der [`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) kann mithilfe der Richtlinie der Zugriff auf eine gesamte Komponente geregelt werden:

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Wenn der Benutzer nicht angemeldet ist, wird er auf die AAD-Anmeldeseite umgeleitet und nach der Anmeldung zurück zur entsprechenden Komponente.

Eine Richtlinienüberprüfung kann auch [in Code mit prozeduraler Logik durchgeführt werden](xref:blazor/security/index#procedural-logic):

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

In AAD registrierte Apps können auch für die Verwendung von benutzerdefinierten Rollen konfiguriert werden.

Informationen zur Konfiguration der App im Azure-Portal für die Bereitstellung eines `roles`-Mitgliedschaftsanspruchs finden Sie in der Azure-Dokumentation unter [ Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

Im folgenden Beispiel wird davon ausgegangen, dass eine App mit zwei Rollen konfiguriert wurde:

* `admin`
* `developer`

> [!NOTE]
> Zwar können Sie Rollen nur mit einem Azure AD Premium-Konto Sicherheitsgruppen zuweisen, allerdings können Sie mit einem Azure-Standardkonto Rollen Benutzer zuweisen und einen `roles`-Anspruch für Benutzer erhalten. Für die Schritte in diesem Abschnitt ist kein Azure AD Premium-Konto erforderlich.
>
> Mehrere Rollen werden im Azure-Portal durch **_separates Hinzufügen eines Benutzers_** für jede weitere Rollenzuweisung zugewiesen.

Der von AAD gesendete einzelne `roles`-Anspruch zeigt die benutzerdefinierten Rollen jeweils als `value` für `appRoles` in einem JSON-Array an. Die App muss das JSON-Array mit Rollen in einzelne `role`-Ansprüche konvertieren.

Die im Abschnitt [Benutzerdefinierte Gruppen und integrierte Administratorrollen](#user-defined-groups-and-built-in-administrative-roles) gezeigte `CustomUserFactory` ist so eingerichtet, dass sie auf einen `roles`-Anspruch mit einem JSON-Arraywert reagiert. Fügen Sie die `CustomUserFactory` wie im Abschnitt [Benutzerdefinierte Gruppen und integrierte Administratorrollen](#user-defined-groups-and-built-in-administrative-roles) beschrieben in der eigenständigen App oder der Client-App einer gehosteten Lösung hinzu, und registrieren Sie sie dort. Für das Entfernen des ursprünglichen `roles`-Anspruchs muss kein Code bereitgestellt werden, da das Framework diesen automatisch entfernt.

Geben Sie in der eigenständigen App oder der Client-App einer gehosteten Lösung in `Program.Main` den Anspruch mit dem Namen „`role`“ als Rollenanspruch an:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

Die Komponentenautorisierungsansätze sind Stand jetzt funktional. Jeder der Autorisierungsmechanismen bei den Komponenten kann die Rolle `admin` verwenden, um den Benutzer zu autorisieren:

* [AuthorizeView-Komponente](xref:blazor/security/index#authorizeview-component) (Beispiel: `<AuthorizeView Roles="admin">`)
* [`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Beispiel: `@attribute [Authorize(Roles = "admin")]`)
* [Prozedurale Logik](xref:blazor/security/index#procedural-logic) (Beispiel: `if (user.IsInRole("admin")) { ... }`)

  Mehrere Rollentests werden unterstützt:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a>Gruppen-IDs für AAD-Administratorrollen

Die in der folgenden Tabelle aufgeführten Objekt-IDs werden zum Erstellen von [Richtlinien](xref:security/authorization/policies) für `group`-Ansprüche verwendet. Richtlinien ermöglichen es einer App, Benutzer für verschiedene Aktivitäten in einer App zu autorisieren. Weitere Informationen finden Sie im Abschnitt [Benutzerdefinierte Gruppen und integrierte Administratorrollen](#user-defined-groups-and-built-in-administrative-roles).

AAD-Administratorrolle | ObjectID
--- | ---
Anwendungsadministrator | fa11557b-4f15-4ddd-85d5-313c7cd74047
Anwendungsentwickler | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Authentifizierungsadministrator | 02d110a1-96b1-419e-af87-746461b60ed7
Azure DevOps-Administrator | a5311ace-ca41-44cd-b833-8d22caa0b34f
Azure Information Protection-Administrator | 18632dce-f9b5-4f01-abb5-37051f06860e
B2C-IEF-Schlüsselsatzadministrator | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
B2C-IEF-Richtlinienadministrator | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
B2C-Benutzerflowadministrator | baa531b7-8cf0-44ad-8f98-eded88dae827
B2C-Administrator für Benutzerflowattribute | dd0baca0-a535-48c1-b871-8431abe16452
Rechnungsadministrator | 69ff516a-b57d-4697-a429-9de4af7b5609
Cloudanwendungsadministrator | 250b5fe3-b553-458d-9a53-b782c13c34bf
Cloudgeräteadministrator | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
Complianceadministrator | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Compliancedatenadministrator | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Administrator für den bedingten Zugriff | 8f71a611-137d-49af-87ad-e97f1fd5da76
Genehmigende Person für den LockBox-Kundenzugriff | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
Desktop Analytics-Administrator | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Rolle „Verzeichnis lesen“ | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Dynamics 365-Administrator | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Exchange-Administrator | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
Externer Identitätsanbieteradministrator | febfaeb4-e478-407a-b4b3-f4d9716618a2
Globaler Administrator | a45ba61b-44db-462c-924b-3b2719152588
Globaler Leser | f6903b21-6aba-4124-b44c-76671796b9d5
Gruppenadministrator | 158b3e5a-d89d-460b-92b5-3b34985f0197
Gasteinladender | 4c730a1d-cc22-44af-8f9f-4eec635c7502
Helpdesk-Administrator | 108678c8-6628-44e1-8d01-caf598a6a5f5
Intune-Administrator | 79950741-23fa-4189-b2cb-46640601c497
Kaizala-Administrator | d6322af2-48e7-42e0-8c68-0bbe31af3412
Lizenzadministrator | 3355458a-e423-44bf-8b98-4ac5e572cea5
Nachrichtencenter-Datenschutzleseberechtigter | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Nachrichtencenter-Leser | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Office-Apps-Administrator | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Kennwortadministrator | 466e48b7-5d66-4ae5-8911-1a118de74941
Power BI-Administrator | 984e83b8-8337-4255-91a1-acb663175ab4
Power Platform-Administrator | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Privilegierter Authentifizierungsadministrator | 0829f731-b46d-419f-9742-aeb122367d11
Administrator für privilegierte Rollen | f20a725a-d1c8-4107-83ea-1171c97d00c7
Berichtsleser | 54635450-e8ed-4f2d-9632-07db2517b4de
Suchadministrator | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Such-Editor | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
Sicherheitsadministrator | 20fa50e3-6531-44d8-bd39-b251420568ad
Sicherheitsoperator | 43aae017-8e51-4188-91ab-e6debd572800
Sicherheitsleseberechtigter | 45035cd3-fd97-4250-8197-3a53d3562d9b
Dienstunterstützungsadministrator | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
SharePoint-Administrator | e1c32229-875e-461d-ae24-3cb99116e86c
Skype for Business-Administrator | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Teams-Kommunikationsadministrator | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Teams-Kommunikationssupporttechniker | 802dd94e-d717-46f6-af98-b9167071e9fc
Supportfachmann für die Teams-Kommunikation | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Teams-Dienstadministrator | 8846a0be-197b-443a-b13c-11192691fa24
Benutzeradministrator | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
