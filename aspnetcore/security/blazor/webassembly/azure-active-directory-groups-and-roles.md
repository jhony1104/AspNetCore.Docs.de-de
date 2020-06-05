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
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="f7c12-103">Azure Ad Gruppen, administrative Rollen und benutzerdefinierte Rollen</span><span class="sxs-lookup"><span data-stu-id="f7c12-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="f7c12-104">Von [Luke Latham](https://github.com/guardrex) und [Javier calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="f7c12-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="f7c12-105">Azure Active Directory (AAD) bietet mehrere Autorisierungs Ansätze, die mit ASP.net Core kombiniert werden können Identity :</span><span class="sxs-lookup"><span data-stu-id="f7c12-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="f7c12-106">Benutzerdefinierte Gruppen</span><span class="sxs-lookup"><span data-stu-id="f7c12-106">User-defined groups</span></span>
  * <span data-ttu-id="f7c12-107">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="f7c12-107">Security</span></span>
  * <span data-ttu-id="f7c12-108">O365</span><span class="sxs-lookup"><span data-stu-id="f7c12-108">O365</span></span>
  * <span data-ttu-id="f7c12-109">Distribution</span><span class="sxs-lookup"><span data-stu-id="f7c12-109">Distribution</span></span>
* <span data-ttu-id="f7c12-110">Rollen</span><span class="sxs-lookup"><span data-stu-id="f7c12-110">Roles</span></span>
  * <span data-ttu-id="f7c12-111">Integrierte Verwaltungs Rollen</span><span class="sxs-lookup"><span data-stu-id="f7c12-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="f7c12-112">Benutzerdefinierte Rollen</span><span class="sxs-lookup"><span data-stu-id="f7c12-112">User-defined roles</span></span>

<span data-ttu-id="f7c12-113">Die Anleitung in diesem Artikel gilt für die Blazor Aad-Bereitstellungs Szenarios der Webassembly, die in den folgenden Themen beschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="f7c12-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="f7c12-114">Eigenständig mit Microsoft-Konten</span><span class="sxs-lookup"><span data-stu-id="f7c12-114">Standalone with Microsoft Accounts</span></span>](xref:security/blazor/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="f7c12-115">Eigenständig mit AAD</span><span class="sxs-lookup"><span data-stu-id="f7c12-115">Standalone with AAD</span></span>](xref:security/blazor/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="f7c12-116">Gehostet mit AAD</span><span class="sxs-lookup"><span data-stu-id="f7c12-116">Hosted with AAD</span></span>](xref:security/blazor/webassembly/hosted-with-azure-active-directory)

### <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="f7c12-117">Benutzerdefinierte Gruppen und integrierte Verwaltungs Rollen</span><span class="sxs-lookup"><span data-stu-id="f7c12-117">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="f7c12-118">Informationen zum Konfigurieren der APP im Azure-Portal für die Bereitstellung eines `groups` Mitgliedschafts Anspruchs finden Sie in den folgenden Azure-Artikeln.</span><span class="sxs-lookup"><span data-stu-id="f7c12-118">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="f7c12-119">Zuweisen von Benutzern zu benutzerdefinierten Aad-Gruppen und integrierten Administrator Rollen</span><span class="sxs-lookup"><span data-stu-id="f7c12-119">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="f7c12-120">Rollen auf Grundlage von Azure AD-Sicherheitsgruppen</span><span class="sxs-lookup"><span data-stu-id="f7c12-120">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="f7c12-121">groupMembershipClaims-Attribut</span><span class="sxs-lookup"><span data-stu-id="f7c12-121">groupMembershipClaims attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="f7c12-122">In den folgenden Beispielen wird davon ausgegangen, dass ein Benutzer der integrierten Aad- *Abrechnungs Administrator* Rolle zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="f7c12-122">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="f7c12-123">Der `groups` von Aad gesendete einzelne Anspruch zeigt die Gruppen und Rollen des Benutzers als Objekt-IDs (GUIDs) in einem JSON-Array an.</span><span class="sxs-lookup"><span data-stu-id="f7c12-123">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="f7c12-124">Die APP muss das JSON-Array von Gruppen und Rollen in einzelne `group` Ansprüche konvertieren, für die die APP [Richtlinien](xref:security/authorization/policies) erstellen kann.</span><span class="sxs-lookup"><span data-stu-id="f7c12-124">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="f7c12-125">Erweitern <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> Sie diese Eigenschaft, um Array Eigenschaften für Gruppen und Rollen einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="f7c12-125">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span>

<span data-ttu-id="f7c12-126">*CustomUserAccount.cs*:</span><span class="sxs-lookup"><span data-stu-id="f7c12-126">*CustomUserAccount.cs*:</span></span>

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

<span data-ttu-id="f7c12-127">Erstellen Sie eine benutzerdefinierte benutzerfactory in der eigenständigen APP oder Client-App einer gehosteten Lösung.</span><span class="sxs-lookup"><span data-stu-id="f7c12-127">Create a custom user factory in the standalone app or Client app of a Hosted solution.</span></span> <span data-ttu-id="f7c12-128">Die folgende Factory ist auch für die Behandlung von `roles` Anspruchs Arrays konfiguriert, die im Abschnitt " [benutzerdefinierte Rollen](#user-defined-roles) " behandelt werden:</span><span class="sxs-lookup"><span data-stu-id="f7c12-128">The following factory is also configured to handle `roles` claim arrays, which are covered in the [User-defined roles](#user-defined-roles) section:</span></span>

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

<span data-ttu-id="f7c12-129">Es muss kein Code bereitgestellt werden, um den ursprünglichen Anspruch zu entfernen, `groups` da er automatisch vom Framework entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="f7c12-129">There's no need to provide code to remove the original `groups` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="f7c12-130">Registrieren Sie die Factory in `Program.Main` (*Program.cs*) der eigenständigen APP-oder Client-App einer gehosteten Lösung:</span><span class="sxs-lookup"><span data-stu-id="f7c12-130">Register the factory in `Program.Main` (*Program.cs*) of the standalone app or Client app of a Hosted solution:</span></span>

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

<span data-ttu-id="f7c12-131">Erstellen Sie eine [Richtlinie](xref:security/authorization/policies) für jede Gruppe oder Rolle in `Program.Main` .</span><span class="sxs-lookup"><span data-stu-id="f7c12-131">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="f7c12-132">Im folgenden Beispiel wird eine Richtlinie für die integrierte Aad- *Abrechnungs Administrator* Rolle erstellt:</span><span class="sxs-lookup"><span data-stu-id="f7c12-132">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="f7c12-133">Eine umfassende Liste der Aad-Rollen Objekt-IDs finden Sie im Abschnitt [Aad Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) .</span><span class="sxs-lookup"><span data-stu-id="f7c12-133">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="f7c12-134">In den folgenden Beispielen verwendet die APP die vorangehende Richtlinie, um den Benutzer zu autorisieren.</span><span class="sxs-lookup"><span data-stu-id="f7c12-134">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="f7c12-135">Die [Autorität für die autorizeview](xref:security/blazor/index#authorizeview-component) funktioniert mit der Richtlinie:</span><span class="sxs-lookup"><span data-stu-id="f7c12-135">The [AuthorizeView component](xref:security/blazor/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="f7c12-136">Der Zugriff auf eine gesamte Komponente kann auf der Richtlinie mithilfe der [ `[Authorize]` ]-Attribut Direktive] (Xref: Security/blazor/Index # Autorisierungs Attribut) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) basieren:</span><span class="sxs-lookup"><span data-stu-id="f7c12-136">Access to an entire component can be based on the policy using [`[Authorize]`] attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="f7c12-137">Wenn der Benutzer nicht angemeldet ist, wird er auf die Aad-Anmeldeseite umgeleitet und dann zurück zur Komponente, nachdem Sie sich angemeldet haben.</span><span class="sxs-lookup"><span data-stu-id="f7c12-137">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="f7c12-138">Eine Richtlinien Überprüfung kann auch [in Code mit prozeduraler Logik ausgeführt](xref:security/blazor/index#procedural-logic)werden:</span><span class="sxs-lookup"><span data-stu-id="f7c12-138">A policy check can also be [performed in code with procedural logic](xref:security/blazor/index#procedural-logic):</span></span>

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

### <a name="user-defined-roles"></a><span data-ttu-id="f7c12-139">Benutzerdefinierte Rollen</span><span class="sxs-lookup"><span data-stu-id="f7c12-139">User-defined roles</span></span>

<span data-ttu-id="f7c12-140">Eine Aad-registrierte App kann auch so konfiguriert werden, dass benutzerdefinierte Rollen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f7c12-140">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="f7c12-141">Informationen zum Konfigurieren der APP im Azure-Portal für die Bereitstellung eines `roles` Mitgliedschafts Anspruchs finden Sie unter Gewusst [wie: Hinzufügen von App-Rollen in Ihrer Anwendung und empfangen der APP im Token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in der Azure-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="f7c12-141">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="f7c12-142">Im folgenden Beispiel wird davon ausgegangen, dass eine APP mit zwei Rollen konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="f7c12-142">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="f7c12-143">Obwohl Sie Sicherheitsgruppen ohne ein Azure AD Premium Konto keine Rollen zuweisen können, können Sie Benutzern Rollen zuweisen und einen `roles` Anspruch für Benutzer mit einem standardmäßigen Azure-Konto erhalten.</span><span class="sxs-lookup"><span data-stu-id="f7c12-143">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="f7c12-144">Die Anleitung in diesem Abschnitt erfordert kein Azure AD Premium Konto.</span><span class="sxs-lookup"><span data-stu-id="f7c12-144">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="f7c12-145">Mehrere Rollen werden in der Azure-Portal zugewiesen, indem ein Benutzer für jede weitere Rollenzuweisung **_neu hinzugefügt_** wird.</span><span class="sxs-lookup"><span data-stu-id="f7c12-145">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="f7c12-146">Der `roles` von Aad gesendete einzelne Anspruch zeigt die benutzerdefinierten Rollen als `appRoles` `value` s in einem JSON-Array an.</span><span class="sxs-lookup"><span data-stu-id="f7c12-146">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="f7c12-147">Die APP muss das JSON-Array von Rollen in einzelne `role` Ansprüche konvertieren.</span><span class="sxs-lookup"><span data-stu-id="f7c12-147">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="f7c12-148">Der `CustomUserFactory` im Abschnitt [benutzerdefinierte Gruppen und integrierte Aad-Administrator Rollen](#user-defined-groups-and-built-in-administrative-roles) angezeigte Vorgang ist so eingerichtet, dass er für einen `roles` Anspruch mit einem JSON-Array Wert ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="f7c12-148">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="f7c12-149">Fügen Sie die `CustomUserFactory` in der eigenständigen APP oder Client-App einer gehosteten Lösung hinzu, und registrieren Sie Sie, wie im Abschnitt [benutzerdefinierte Gruppen und integrierte Aad-Verwaltungs Rollen](#user-defined-groups-and-built-in-administrative-roles) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="f7c12-149">Add and register the `CustomUserFactory` in the standalone app or Client app of a Hosted solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="f7c12-150">Es muss kein Code bereitgestellt werden, um den ursprünglichen Anspruch zu entfernen, `roles` da er automatisch vom Framework entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="f7c12-150">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="f7c12-151">Geben Sie in der `Program.Main` eigenständigen APP oder Client-App einer gehosteten Lösung den Anspruch mit dem Namen " `role` " als Rollen Anspruch an:</span><span class="sxs-lookup"><span data-stu-id="f7c12-151">In `Program.Main` of the standalone app or Client app of a Hosted solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="f7c12-152">An dieser Stelle sind die Komponenten Autorisierungs Ansätze funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="f7c12-152">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="f7c12-153">Alle Autorisierungs Mechanismen in-Komponenten können die- `admin` Rolle verwenden, um den Benutzer zu autorisieren:</span><span class="sxs-lookup"><span data-stu-id="f7c12-153">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="f7c12-154">[Autorität View-Komponente](xref:security/blazor/index#authorizeview-component) (Beispiel: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="f7c12-154">[AuthorizeView component](xref:security/blazor/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="f7c12-155">[ `[Authorize]` ]-Attribut Direktive] (Xref: Security/blazor/Index # autorisieren-Attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (Beispiel: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="f7c12-155">[`[Authorize]`] attribute directive](xref:security/blazor/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="f7c12-156">[Prozedurale Logik](xref:security/blazor/index#procedural-logic) (Beispiel: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="f7c12-156">[Procedural logic](xref:security/blazor/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="f7c12-157">Mehrere Rollen Tests werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="f7c12-157">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="f7c12-158">Rollen Gruppen-IDs für Aad-Administratoren</span><span class="sxs-lookup"><span data-stu-id="f7c12-158">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="f7c12-159">Die Objekt-IDs, die in der folgenden Tabelle dargestellt werden, dienen zum Erstellen von [Richtlinien](xref:security/authorization/policies) für `group` Ansprüche.</span><span class="sxs-lookup"><span data-stu-id="f7c12-159">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="f7c12-160">Mit Richtlinien ist es einer APP gestattet, Benutzer für verschiedene Aktivitäten in einer APP zu autorisieren.</span><span class="sxs-lookup"><span data-stu-id="f7c12-160">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="f7c12-161">Weitere Informationen finden Sie im Abschnitt [benutzerdefinierte Gruppen und integrierte Aad-Administrator Rollen](#user-defined-groups-and-built-in-administrative-roles) .</span><span class="sxs-lookup"><span data-stu-id="f7c12-161">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="f7c12-162">Aad-Administrator Rolle</span><span class="sxs-lookup"><span data-stu-id="f7c12-162">AAD Administrative Role</span></span> | <span data-ttu-id="f7c12-163">ObjectID</span><span class="sxs-lookup"><span data-stu-id="f7c12-163">Object ID</span></span>
--- | ---
<span data-ttu-id="f7c12-164">Anwendungsadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-164">Application administrator</span></span> | <span data-ttu-id="f7c12-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="f7c12-165">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="f7c12-166">Anwendungsentwickler</span><span class="sxs-lookup"><span data-stu-id="f7c12-166">Application developer</span></span> | <span data-ttu-id="f7c12-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="f7c12-167">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="f7c12-168">Authentifizierungsadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-168">Authentication administrator</span></span> | <span data-ttu-id="f7c12-169">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="f7c12-169">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="f7c12-170">Azure DevOps-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-170">Azure DevOps administrator</span></span> | <span data-ttu-id="f7c12-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="f7c12-171">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="f7c12-172">Azure Information Protection-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-172">Azure Information Protection administrator</span></span> | <span data-ttu-id="f7c12-173">18632dce-sp9b5-4f 01-ABB5-37051f 06860e</span><span class="sxs-lookup"><span data-stu-id="f7c12-173">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="f7c12-174">B2C-IEF-keysetadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-174">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="f7c12-175">0c2e87e5-94b9-4adb-AE8C-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="f7c12-175">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="f7c12-176">B2C-IEF-Richtlinien Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-176">B2C IEF Policy administrator</span></span> | <span data-ttu-id="f7c12-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="f7c12-177">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="f7c12-178">B2C-Benutzer Fluss Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-178">B2C user flow administrator</span></span> | <span data-ttu-id="f7c12-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="f7c12-179">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="f7c12-180">B2C-Benutzer Fluss Attribut-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-180">B2C user flow attribute administrator</span></span> | <span data-ttu-id="f7c12-181">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="f7c12-181">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="f7c12-182">Rechnungsadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-182">Billing administrator</span></span> | <span data-ttu-id="f7c12-183">69ff516a-b57d-4697-A429-9DE 4af7b5609</span><span class="sxs-lookup"><span data-stu-id="f7c12-183">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="f7c12-184">Cloudanwendungsadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-184">Cloud application administrator</span></span> | <span data-ttu-id="f7c12-185">250b5fe3-B553-458D-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="f7c12-185">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="f7c12-186">Cloudgeräteadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-186">Cloud device administrator</span></span> | <span data-ttu-id="f7c12-187">26cd4b44-2636-4ddb-BDFA-27feae66f 86D</span><span class="sxs-lookup"><span data-stu-id="f7c12-187">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="f7c12-188">Complianceadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-188">Compliance administrator</span></span> | <span data-ttu-id="f7c12-189">9d6e1dd0-c9b8-45b8-b558-b134f 700116c</span><span class="sxs-lookup"><span data-stu-id="f7c12-189">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="f7c12-190">Compliancedatenadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-190">Compliance data administrator</span></span> | <span data-ttu-id="f7c12-191">4c0ca3a2-231E-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="f7c12-191">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="f7c12-192">Administrator für den bedingten Zugriff</span><span class="sxs-lookup"><span data-stu-id="f7c12-192">Conditional Access administrator</span></span> | <span data-ttu-id="f7c12-193">8f 71a611-137d-49af-87ad-e97f 1F-5da76</span><span class="sxs-lookup"><span data-stu-id="f7c12-193">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="f7c12-194">Genehmigende Person für den LockBox-Kundenzugriff</span><span class="sxs-lookup"><span data-stu-id="f7c12-194">Customer LockBox access approver</span></span> | <span data-ttu-id="f7c12-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f 184</span><span class="sxs-lookup"><span data-stu-id="f7c12-195">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="f7c12-196">Desktop Analytics-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-196">Desktop Analytics administrator</span></span> | <span data-ttu-id="f7c12-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="f7c12-197">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="f7c12-198">Rolle „Verzeichnis lesen“</span><span class="sxs-lookup"><span data-stu-id="f7c12-198">Directory readers</span></span> | <span data-ttu-id="f7c12-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="f7c12-199">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="f7c12-200">Dynamics 365-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-200">Dynamics 365 administrator</span></span> | <span data-ttu-id="f7c12-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="f7c12-201">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="f7c12-202">Exchange-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-202">Exchange administrator</span></span> | <span data-ttu-id="f7c12-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="f7c12-203">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="f7c12-204">Externer Identity Anbieter Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-204">External Identity Provider administrator</span></span> | <span data-ttu-id="f7c12-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="f7c12-205">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="f7c12-206">Globaler Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-206">Global administrator</span></span> | <span data-ttu-id="f7c12-207">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="f7c12-207">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="f7c12-208">Globaler Leser</span><span class="sxs-lookup"><span data-stu-id="f7c12-208">Global reader</span></span> | <span data-ttu-id="f7c12-209">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="f7c12-209">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="f7c12-210">Gruppenadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-210">Groups administrator</span></span> | <span data-ttu-id="f7c12-211">158 b3e5a-d89d-460b-92b5-3b34985-l0197</span><span class="sxs-lookup"><span data-stu-id="f7c12-211">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="f7c12-212">Gasteinladender</span><span class="sxs-lookup"><span data-stu-id="f7c12-212">Guest inviter</span></span> | <span data-ttu-id="f7c12-213">4c730a1d-cc22-44af-8-Datei-eec635c7502</span><span class="sxs-lookup"><span data-stu-id="f7c12-213">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="f7c12-214">Helpdesk-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-214">Helpdesk administrator</span></span> | <span data-ttu-id="f7c12-215">108678c8-6628-44e1-8d01-caf598a6a5f 5</span><span class="sxs-lookup"><span data-stu-id="f7c12-215">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="f7c12-216">Intune-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-216">Intune administrator</span></span> | <span data-ttu-id="f7c12-217">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="f7c12-217">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="f7c12-218">Kaizala-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-218">Kaizala administrator</span></span> | <span data-ttu-id="f7c12-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="f7c12-219">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="f7c12-220">Lizenzadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-220">License administrator</span></span> | <span data-ttu-id="f7c12-221">3355458a-E423-44bf -8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="f7c12-221">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="f7c12-222">Nachrichtencenter-Datenschutzleseberechtigter</span><span class="sxs-lookup"><span data-stu-id="f7c12-222">Message center privacy reader</span></span> | <span data-ttu-id="f7c12-223">6395db95-9lb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="f7c12-223">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="f7c12-224">Nachrichtencenter-Leser</span><span class="sxs-lookup"><span data-stu-id="f7c12-224">Message center reader</span></span> | <span data-ttu-id="f7c12-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="f7c12-225">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="f7c12-226">Office-Apps-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-226">Office apps administrator</span></span> | <span data-ttu-id="f7c12-227">5F 3870cd-B042-4t93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="f7c12-227">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="f7c12-228">Kennwortadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-228">Password administrator</span></span> | <span data-ttu-id="f7c12-229">466e48b7-5d66-4ae5-8911-1a118debug</span><span class="sxs-lookup"><span data-stu-id="f7c12-229">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="f7c12-230">Power BI-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-230">Power BI administrator</span></span> | <span data-ttu-id="f7c12-231">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="f7c12-231">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="f7c12-232">Power Platform-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-232">Power platform administrator</span></span> | <span data-ttu-id="f7c12-233">76d6b95e-9A15-4d7d-8D21-00be00f FD</span><span class="sxs-lookup"><span data-stu-id="f7c12-233">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="f7c12-234">Privilegierter Authentifizierungsadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-234">Privileged authentication administrator</span></span> | <span data-ttu-id="f7c12-235">0829-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="f7c12-235">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="f7c12-236">Administrator für privilegierte Rollen</span><span class="sxs-lookup"><span data-stu-id="f7c12-236">Privileged role administrator</span></span> | <span data-ttu-id="f7c12-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="f7c12-237">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="f7c12-238">Berichtsleser</span><span class="sxs-lookup"><span data-stu-id="f7c12-238">Reports reader</span></span> | <span data-ttu-id="f7c12-239">54635450-e8ed-4b2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="f7c12-239">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="f7c12-240">Suchadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-240">Search administrator</span></span> | <span data-ttu-id="f7c12-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="f7c12-241">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="f7c12-242">Such-Editor</span><span class="sxs-lookup"><span data-stu-id="f7c12-242">Search editor</span></span> | <span data-ttu-id="f7c12-243">6a6858c6-5-Datei (c)</span><span class="sxs-lookup"><span data-stu-id="f7c12-243">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="f7c12-244">Sicherheitsadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-244">Security administrator</span></span> | <span data-ttu-id="f7c12-245">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="f7c12-245">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="f7c12-246">Sicherheitsoperator</span><span class="sxs-lookup"><span data-stu-id="f7c12-246">Security operator</span></span> | <span data-ttu-id="f7c12-247">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="f7c12-247">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="f7c12-248">Sicherheitsleseberechtigter</span><span class="sxs-lookup"><span data-stu-id="f7c12-248">Security reader</span></span> | <span data-ttu-id="f7c12-249">45035cd3-sd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="f7c12-249">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="f7c12-250">Dienstunterstützungsadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-250">Service support administrator</span></span> | <span data-ttu-id="f7c12-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="f7c12-251">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="f7c12-252">SharePoint-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-252">SharePoint administrator</span></span> | <span data-ttu-id="f7c12-253">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="f7c12-253">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="f7c12-254">Skype for Business-Administrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-254">Skype for Business administrator</span></span> | <span data-ttu-id="f7c12-255">0a8cee12-e21d-43ef-abd9-f 1 ea85710e30</span><span class="sxs-lookup"><span data-stu-id="f7c12-255">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="f7c12-256">Teams-Kommunikationsadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-256">Teams Communications Administrator</span></span> | <span data-ttu-id="f7c12-257">2393e455-6e13-4743-9b52-63F b6a9c</span><span class="sxs-lookup"><span data-stu-id="f7c12-257">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="f7c12-258">Teams-Kommunikationssupporttechniker</span><span class="sxs-lookup"><span data-stu-id="f7c12-258">Teams Communications Support Engineer</span></span> | <span data-ttu-id="f7c12-259">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="f7c12-259">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="f7c12-260">Teams Communications Specialist</span><span class="sxs-lookup"><span data-stu-id="f7c12-260">Teams Communications Specialist</span></span> | <span data-ttu-id="f7c12-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="f7c12-261">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="f7c12-262">Teams-Dienstadministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-262">Teams Service Administrator</span></span> | <span data-ttu-id="f7c12-263">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="f7c12-263">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="f7c12-264">Benutzeradministrator</span><span class="sxs-lookup"><span data-stu-id="f7c12-264">User administrator</span></span> | <span data-ttu-id="f7c12-265">1F 6eed58-7dd3-460b-a298-666f 975427a1</span><span class="sxs-lookup"><span data-stu-id="f7c12-265">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f7c12-266">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f7c12-266">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:security/blazor/index>
