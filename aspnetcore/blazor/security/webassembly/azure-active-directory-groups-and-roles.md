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
ms.openlocfilehash: ed49ba13842f2b5805250d8c12535397c542cfd4
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242874"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="d68c1-103">Gruppen, Administratorrollen und benutzerdefinierte Rollen aus Azure AD</span><span class="sxs-lookup"><span data-stu-id="d68c1-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="d68c1-104">Von [Luke Latham](https://github.com/guardrex) und [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="d68c1-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="d68c1-105">Azure Active Directory (AAD) bietet mehrere Autorisierungsansätze, die mit ASP.NET Core Identity kombiniert werden können:</span><span class="sxs-lookup"><span data-stu-id="d68c1-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="d68c1-106">Benutzerdefinierte Gruppen</span><span class="sxs-lookup"><span data-stu-id="d68c1-106">User-defined groups</span></span>
  * <span data-ttu-id="d68c1-107">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="d68c1-107">Security</span></span>
  * <span data-ttu-id="d68c1-108">O365</span><span class="sxs-lookup"><span data-stu-id="d68c1-108">O365</span></span>
  * <span data-ttu-id="d68c1-109">Verteilung</span><span class="sxs-lookup"><span data-stu-id="d68c1-109">Distribution</span></span>
* <span data-ttu-id="d68c1-110">Rollen</span><span class="sxs-lookup"><span data-stu-id="d68c1-110">Roles</span></span>
  * <span data-ttu-id="d68c1-111">Integrierte Administratorrollen</span><span class="sxs-lookup"><span data-stu-id="d68c1-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="d68c1-112">Benutzerdefinierte Rollen</span><span class="sxs-lookup"><span data-stu-id="d68c1-112">User-defined roles</span></span>

<span data-ttu-id="d68c1-113">Die Informationen in diesem Artikel beziehen sich auf die in den folgenden Artikeln beschriebenen Bereitstellungsszenarios für Blazor WebAssembly und AAD:</span><span class="sxs-lookup"><span data-stu-id="d68c1-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="d68c1-114">Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Microsoft-Konten</span><span class="sxs-lookup"><span data-stu-id="d68c1-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="d68c1-115">Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="d68c1-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="d68c1-116">Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="d68c1-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="d68c1-117">Benutzerdefinierte Gruppen und integrierte Administratorrollen</span><span class="sxs-lookup"><span data-stu-id="d68c1-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="d68c1-118">Informationen zur Konfiguration der App im Azure-Portal für die Bereitstellung eines `groups`-Mitgliedschaftsanspruchs finden Sie in den folgenden Abschnitten in Azure-Artikeln.</span><span class="sxs-lookup"><span data-stu-id="d68c1-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="d68c1-119">Weisen Sie Benutzer benutzerdefinierten AAD-Gruppen und integrierten AAD-Administratorrollen zu.</span><span class="sxs-lookup"><span data-stu-id="d68c1-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="d68c1-120">Rollen auf Grundlage von Azure AD-Sicherheitsgruppen</span><span class="sxs-lookup"><span data-stu-id="d68c1-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="d68c1-121">`groupMembershipClaims`-Attribut</span><span class="sxs-lookup"><span data-stu-id="d68c1-121">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="d68c1-122">In den folgenden Beispielen wird davon ausgegangen, dass ein Benutzer der in AAD integrierten Rolle *Abrechnungsadministrator* zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="d68c1-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="d68c1-123">Der von AAD gesendete einzelne `groups`-Anspruch zeigt die Gruppen und Rollen des Benutzers als Objekt-IDs (GUIDs) in einem JSON-Array an.</span><span class="sxs-lookup"><span data-stu-id="d68c1-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="d68c1-124">Die App muss das JSON-Array mit Gruppen und Rollen in einzelne `group`-Ansprüche konvertieren, für die sie [Richtlinien](xref:security/authorization/policies) erstellen kann.</span><span class="sxs-lookup"><span data-stu-id="d68c1-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="d68c1-125">Erweitern Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> um Arrayeigenschaften für Gruppen und Rollen.</span><span class="sxs-lookup"><span data-stu-id="d68c1-125">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span>

<span data-ttu-id="d68c1-126">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="d68c1-126">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="d68c1-127">Erstellen Sie in der eigenständigen App oder der Client-App einer gehosteten Lösung eine benutzerdefinierte Benutzerfactory.</span><span class="sxs-lookup"><span data-stu-id="d68c1-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="d68c1-128">Die folgende Factory ist auch für die Verarbeitung von `roles`-Anspruchsarrays konfiguriert. Diese werden im Abschnitt [Benutzerdefinierte Rollen](#user-defined-roles) behandelt.</span><span class="sxs-lookup"><span data-stu-id="d68c1-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

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

<span data-ttu-id="d68c1-129">Für das Entfernen des ursprünglichen `groups`-Anspruchs muss kein Code bereitgestellt werden, da das Framework diesen automatisch entfernt.</span><span class="sxs-lookup"><span data-stu-id="d68c1-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="d68c1-130">Registrieren Sie die Factory in `Program.Main` (`Program.cs`) der eigenständigen App oder der Client-App einer gehosteten Lösung:</span><span class="sxs-lookup"><span data-stu-id="d68c1-130">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or Client app of a Hosted solution:</span></span>

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

<span data-ttu-id="d68c1-131">Erstellen Sie für jede Gruppe oder Rolle eine [Richtlinie](xref:security/authorization/policies) in `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="d68c1-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="d68c1-132">Im folgenden Beispiel wird eine Richtlinie für die in AAD integrierte Rolle *Abrechnungsadministrator* erstellt:</span><span class="sxs-lookup"><span data-stu-id="d68c1-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="d68c1-133">Eine vollständige Liste der Objekt-IDs für die einzelnen AAD-Rollen finden Sie im Abschnitt [Gruppen-IDs für AAD-Administratorrollen](#aad-adminstrative-role-group-ids).</span><span class="sxs-lookup"><span data-stu-id="d68c1-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="d68c1-134">In den folgenden Beispielen verwendet die App die obige Richtlinie für die Autorisierung des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="d68c1-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="d68c1-135">Die [`AuthorizeView`-Komponente](xref:blazor/security/index#authorizeview-component) verwendet die Richtlinie:</span><span class="sxs-lookup"><span data-stu-id="d68c1-135">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="d68c1-136">Durch Verwendung der [`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) kann mithilfe der Richtlinie der Zugriff auf eine gesamte Komponente geregelt werden:</span><span class="sxs-lookup"><span data-stu-id="d68c1-136">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="d68c1-137">Wenn der Benutzer nicht angemeldet ist, wird er auf die AAD-Anmeldeseite umgeleitet und nach der Anmeldung zurück zur entsprechenden Komponente.</span><span class="sxs-lookup"><span data-stu-id="d68c1-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="d68c1-138">Eine Richtlinienüberprüfung kann auch [in Code mit prozeduraler Logik durchgeführt werden](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="d68c1-138">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

### <a name="user-defined-roles"></a><span data-ttu-id="d68c1-139">Benutzerdefinierte Rollen</span><span class="sxs-lookup"><span data-stu-id="d68c1-139">User-defined roles</span></span>

<span data-ttu-id="d68c1-140">In AAD registrierte Apps können auch für die Verwendung von benutzerdefinierten Rollen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="d68c1-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="d68c1-141">Informationen zur Konfiguration der App im Azure-Portal für die Bereitstellung eines `roles`-Mitgliedschaftsanspruchs finden Sie in der Azure-Dokumentation unter [ Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).</span><span class="sxs-lookup"><span data-stu-id="d68c1-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="d68c1-142">Im folgenden Beispiel wird davon ausgegangen, dass eine App mit zwei Rollen konfiguriert wurde:</span><span class="sxs-lookup"><span data-stu-id="d68c1-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="d68c1-143">Zwar können Sie Rollen nur mit einem Azure AD Premium-Konto Sicherheitsgruppen zuweisen, allerdings können Sie mit einem Azure-Standardkonto Rollen Benutzer zuweisen und einen `roles`-Anspruch für Benutzer erhalten.</span><span class="sxs-lookup"><span data-stu-id="d68c1-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="d68c1-144">Für die Schritte in diesem Abschnitt ist kein Azure AD Premium-Konto erforderlich.</span><span class="sxs-lookup"><span data-stu-id="d68c1-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="d68c1-145">Mehrere Rollen werden im Azure-Portal durch **_separates Hinzufügen eines Benutzers_** für jede weitere Rollenzuweisung zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="d68c1-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="d68c1-146">Der von AAD gesendete einzelne `roles`-Anspruch zeigt die benutzerdefinierten Rollen jeweils als `value` für `appRoles` in einem JSON-Array an.</span><span class="sxs-lookup"><span data-stu-id="d68c1-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="d68c1-147">Die App muss das JSON-Array mit Rollen in einzelne `role`-Ansprüche konvertieren.</span><span class="sxs-lookup"><span data-stu-id="d68c1-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="d68c1-148">Die im Abschnitt [Benutzerdefinierte Gruppen und integrierte Administratorrollen](#user-defined-groups-and-built-in-administrative-roles) gezeigte `CustomUserFactory` ist so eingerichtet, dass sie auf einen `roles`-Anspruch mit einem JSON-Arraywert reagiert.</span><span class="sxs-lookup"><span data-stu-id="d68c1-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="d68c1-149">Fügen Sie die `CustomUserFactory` wie im Abschnitt [Benutzerdefinierte Gruppen und integrierte Administratorrollen](#user-defined-groups-and-built-in-administrative-roles) beschrieben in der eigenständigen App oder der Client-App einer gehosteten Lösung hinzu, und registrieren Sie sie dort.</span><span class="sxs-lookup"><span data-stu-id="d68c1-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="d68c1-150">Für das Entfernen des ursprünglichen `roles`-Anspruchs muss kein Code bereitgestellt werden, da das Framework diesen automatisch entfernt.</span><span class="sxs-lookup"><span data-stu-id="d68c1-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="d68c1-151">Geben Sie in der eigenständigen App oder der Client-App einer gehosteten Lösung in `Program.Main` den Anspruch mit dem Namen „`role`“ als Rollenanspruch an:</span><span class="sxs-lookup"><span data-stu-id="d68c1-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="d68c1-152">Die Komponentenautorisierungsansätze sind Stand jetzt funktional.</span><span class="sxs-lookup"><span data-stu-id="d68c1-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="d68c1-153">Jeder der Autorisierungsmechanismen bei den Komponenten kann die Rolle `admin` verwenden, um den Benutzer zu autorisieren:</span><span class="sxs-lookup"><span data-stu-id="d68c1-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="d68c1-154">[`AuthorizeView`-Komponente](xref:blazor/security/index#authorizeview-component) (Beispiel: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="d68c1-154">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="d68c1-155">[`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Beispiel: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="d68c1-155">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="d68c1-156">[Prozedurale Logik](xref:blazor/security/index#procedural-logic) (Beispiel: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="d68c1-156">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="d68c1-157">Mehrere Rollentests werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="d68c1-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="d68c1-158">Gruppen-IDs für AAD-Administratorrollen</span><span class="sxs-lookup"><span data-stu-id="d68c1-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="d68c1-159">Die in der folgenden Tabelle aufgeführten Objekt-IDs werden zum Erstellen von [Richtlinien](xref:security/authorization/policies) für `group`-Ansprüche verwendet.</span><span class="sxs-lookup"><span data-stu-id="d68c1-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="d68c1-160">Richtlinien ermöglichen es einer App, Benutzer für verschiedene Aktivitäten in einer App zu autorisieren.</span><span class="sxs-lookup"><span data-stu-id="d68c1-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="d68c1-161">Weitere Informationen finden Sie im Abschnitt [Benutzerdefinierte Gruppen und integrierte Administratorrollen](#user-defined-groups-and-built-in-administrative-roles).</span><span class="sxs-lookup"><span data-stu-id="d68c1-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="d68c1-162">AAD-Administratorrolle</span><span class="sxs-lookup"><span data-stu-id="d68c1-162">AAD Administrative Role</span></span> | <span data-ttu-id="d68c1-163">ObjectID</span><span class="sxs-lookup"><span data-stu-id="d68c1-163">Object ID</span></span>
--- | ---
<span data-ttu-id="d68c1-164">Anwendungsadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-164">Application administrator</span></span> | <span data-ttu-id="d68c1-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="d68c1-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="d68c1-166">Anwendungsentwickler</span><span class="sxs-lookup"><span data-stu-id="d68c1-166">Application developer</span></span> | <span data-ttu-id="d68c1-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="d68c1-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="d68c1-168">Authentifizierungsadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-168">Authentication administrator</span></span> | <span data-ttu-id="d68c1-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="d68c1-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="d68c1-170">Azure DevOps-Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-170">Azure DevOps administrator</span></span> | <span data-ttu-id="d68c1-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="d68c1-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="d68c1-172">Azure Information Protection-Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="d68c1-173">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="d68c1-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="d68c1-174">B2C-IEF-Schlüsselsatzadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="d68c1-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="d68c1-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="d68c1-176">B2C-IEF-Richtlinienadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="d68c1-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="d68c1-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="d68c1-178">B2C-Benutzerflowadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-178">B2C user flow administrator</span></span> | <span data-ttu-id="d68c1-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="d68c1-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="d68c1-180">B2C-Administrator für Benutzerflowattribute</span><span class="sxs-lookup"><span data-stu-id="d68c1-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="d68c1-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="d68c1-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="d68c1-182">Rechnungsadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-182">Billing administrator</span></span> | <span data-ttu-id="d68c1-183">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="d68c1-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="d68c1-184">Cloudanwendungsadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-184">Cloud application administrator</span></span> | <span data-ttu-id="d68c1-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="d68c1-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="d68c1-186">Cloudgeräteadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-186">Cloud device administrator</span></span> | <span data-ttu-id="d68c1-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="d68c1-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="d68c1-188">Complianceadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-188">Compliance administrator</span></span> | <span data-ttu-id="d68c1-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="d68c1-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="d68c1-190">Compliancedatenadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-190">Compliance data administrator</span></span> | <span data-ttu-id="d68c1-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="d68c1-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="d68c1-192">Administrator für den bedingten Zugriff</span><span class="sxs-lookup"><span data-stu-id="d68c1-192">Conditional Access administrator</span></span> | <span data-ttu-id="d68c1-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="d68c1-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="d68c1-194">Genehmigende Person für den LockBox-Kundenzugriff</span><span class="sxs-lookup"><span data-stu-id="d68c1-194">Customer LockBox access approver</span></span> | <span data-ttu-id="d68c1-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="d68c1-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="d68c1-196">Desktop Analytics-Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="d68c1-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="d68c1-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="d68c1-198">Rolle „Verzeichnis lesen“</span><span class="sxs-lookup"><span data-stu-id="d68c1-198">Directory readers</span></span> | <span data-ttu-id="d68c1-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="d68c1-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="d68c1-200">Dynamics 365-Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="d68c1-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="d68c1-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="d68c1-202">Exchange-Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-202">Exchange administrator</span></span> | <span data-ttu-id="d68c1-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="d68c1-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="d68c1-204">Externer Identitätsanbieteradministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-204">External Identity Provider administrator</span></span> | <span data-ttu-id="d68c1-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="d68c1-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="d68c1-206">Globaler Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-206">Global administrator</span></span> | <span data-ttu-id="d68c1-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="d68c1-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="d68c1-208">Globaler Leser</span><span class="sxs-lookup"><span data-stu-id="d68c1-208">Global reader</span></span> | <span data-ttu-id="d68c1-209">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="d68c1-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="d68c1-210">Gruppenadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-210">Groups administrator</span></span> | <span data-ttu-id="d68c1-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="d68c1-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="d68c1-212">Gasteinladender</span><span class="sxs-lookup"><span data-stu-id="d68c1-212">Guest inviter</span></span> | <span data-ttu-id="d68c1-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="d68c1-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="d68c1-214">Helpdesk-Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-214">Helpdesk administrator</span></span> | <span data-ttu-id="d68c1-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="d68c1-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="d68c1-216">Intune-Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-216">Intune administrator</span></span> | <span data-ttu-id="d68c1-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="d68c1-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="d68c1-218">Kaizala-Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-218">Kaizala administrator</span></span> | <span data-ttu-id="d68c1-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="d68c1-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="d68c1-220">Lizenzadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-220">License administrator</span></span> | <span data-ttu-id="d68c1-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="d68c1-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="d68c1-222">Nachrichtencenter-Datenschutzleseberechtigter</span><span class="sxs-lookup"><span data-stu-id="d68c1-222">Message center privacy reader</span></span> | <span data-ttu-id="d68c1-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="d68c1-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="d68c1-224">Nachrichtencenter-Leser</span><span class="sxs-lookup"><span data-stu-id="d68c1-224">Message center reader</span></span> | <span data-ttu-id="d68c1-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="d68c1-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="d68c1-226">Office-Apps-Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-226">Office apps administrator</span></span> | <span data-ttu-id="d68c1-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="d68c1-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="d68c1-228">Kennwortadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-228">Password administrator</span></span> | <span data-ttu-id="d68c1-229">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="d68c1-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="d68c1-230">Power BI-Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-230">Power BI administrator</span></span> | <span data-ttu-id="d68c1-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="d68c1-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="d68c1-232">Power Platform-Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-232">Power platform administrator</span></span> | <span data-ttu-id="d68c1-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="d68c1-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="d68c1-234">Privilegierter Authentifizierungsadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-234">Privileged authentication administrator</span></span> | <span data-ttu-id="d68c1-235">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="d68c1-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="d68c1-236">Administrator für privilegierte Rollen</span><span class="sxs-lookup"><span data-stu-id="d68c1-236">Privileged role administrator</span></span> | <span data-ttu-id="d68c1-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="d68c1-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="d68c1-238">Berichtsleser</span><span class="sxs-lookup"><span data-stu-id="d68c1-238">Reports reader</span></span> | <span data-ttu-id="d68c1-239">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="d68c1-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="d68c1-240">Suchadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-240">Search administrator</span></span> | <span data-ttu-id="d68c1-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="d68c1-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="d68c1-242">Such-Editor</span><span class="sxs-lookup"><span data-stu-id="d68c1-242">Search editor</span></span> | <span data-ttu-id="d68c1-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="d68c1-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="d68c1-244">Sicherheitsadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-244">Security administrator</span></span> | <span data-ttu-id="d68c1-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="d68c1-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="d68c1-246">Sicherheitsoperator</span><span class="sxs-lookup"><span data-stu-id="d68c1-246">Security operator</span></span> | <span data-ttu-id="d68c1-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="d68c1-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="d68c1-248">Sicherheitsleseberechtigter</span><span class="sxs-lookup"><span data-stu-id="d68c1-248">Security reader</span></span> | <span data-ttu-id="d68c1-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="d68c1-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="d68c1-250">Dienstunterstützungsadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-250">Service support administrator</span></span> | <span data-ttu-id="d68c1-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="d68c1-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="d68c1-252">SharePoint-Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-252">SharePoint administrator</span></span> | <span data-ttu-id="d68c1-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="d68c1-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="d68c1-254">Skype for Business-Administrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-254">Skype for Business administrator</span></span> | <span data-ttu-id="d68c1-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="d68c1-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="d68c1-256">Teams-Kommunikationsadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-256">Teams Communications Administrator</span></span> | <span data-ttu-id="d68c1-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="d68c1-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="d68c1-258">Teams-Kommunikationssupporttechniker</span><span class="sxs-lookup"><span data-stu-id="d68c1-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="d68c1-259">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="d68c1-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="d68c1-260">Supportfachmann für die Teams-Kommunikation</span><span class="sxs-lookup"><span data-stu-id="d68c1-260">Teams Communications Specialist</span></span> | <span data-ttu-id="d68c1-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="d68c1-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="d68c1-262">Teams-Dienstadministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-262">Teams Service Administrator</span></span> | <span data-ttu-id="d68c1-263">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="d68c1-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="d68c1-264">Benutzeradministrator</span><span class="sxs-lookup"><span data-stu-id="d68c1-264">User administrator</span></span> | <span data-ttu-id="d68c1-265">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="d68c1-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d68c1-266">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d68c1-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
