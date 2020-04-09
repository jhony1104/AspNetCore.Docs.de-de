---
title: Authentifizierung und Autorisierung in ASP.NET Core Blazor
author: guardrex
description: Lernen Sie die Szenarien für die Authentifizierung und Autorisierung in Blazor kennen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 04bbf20d1d848edfa98e719f316b790c812bfd95
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501316"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a><span data-ttu-id="129e9-103">Authentifizierung und Autorisierung in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="129e9-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="129e9-104">Von [Steve Sanderson](https://github.com/SteveSandersonMS) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="129e9-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> <span data-ttu-id="129e9-105">Für alle Schritte in diesem Artikel, die für Blazor WebAssembly gelten, ist Blazor WebAssembly von ASP.NET Core in der Vorlagenversion 3.2 oder höher erforderlich.</span><span class="sxs-lookup"><span data-stu-id="129e9-105">For the guidance in this article that applies to Blazor WebAssembly, the ASP.NET Core Blazor WebAssembly template version 3.2 or later is required.</span></span> <span data-ttu-id="129e9-106">Wenn Sie nicht Visual Studio, Version 16.6 Preview 2 oder höher verwenden, müssen Sie die neueste Blazor WebAssembly-Vorlage entsprechend der Anleitung unter <xref:blazor/get-started> abrufen.</span><span class="sxs-lookup"><span data-stu-id="129e9-106">If you aren't using Visual Studio version 16.6 Preview 2 or later, obtain the latest Blazor WebAssembly template by following the guidance in <xref:blazor/get-started>.</span></span>

<span data-ttu-id="129e9-107">ASP.NET Core unterstützt die Konfiguration und Verwaltung der Sicherheit in Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="129e9-107">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="129e9-108">Es gibt jeweils unterschiedliche Sicherheitsszenarien für Blazor Server- und Blazor WebAssembly-Apps.</span><span class="sxs-lookup"><span data-stu-id="129e9-108">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="129e9-109">Da Blazor Server-Apps auf dem Server ausgeführt werden, kann durch Autorisierungsprüfungen Folgendes bestimmt werden:</span><span class="sxs-lookup"><span data-stu-id="129e9-109">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="129e9-110">Die UI-Optionen, die einem Benutzer angezeigt werden (z.B., welche Menüeinträge für einen Benutzer verfügbar sind).</span><span class="sxs-lookup"><span data-stu-id="129e9-110">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="129e9-111">Zugriffsregeln für Bereiche und Komponenten der App.</span><span class="sxs-lookup"><span data-stu-id="129e9-111">Access rules for areas of the app and components.</span></span>

Blazor<span data-ttu-id="129e9-112"> WebAssembly-Apps werden auf dem Client ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="129e9-112"> WebAssembly apps run on the client.</span></span> <span data-ttu-id="129e9-113">Die Autorisierung wird *nur* verwendet, um zu bestimmen, welche UI-Optionen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="129e9-113">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="129e9-114">Da clientseitige Prüfungen von einem Benutzer geändert oder umgangen werden können, kann eine Blazor WebAssembly-App keine Autorisierungszugriffsregeln durchsetzen.</span><span class="sxs-lookup"><span data-stu-id="129e9-114">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="129e9-115">[Autorisierungskonventionen von Razor Pages](xref:security/authorization/razor-pages-authorization) gelten nicht für routingfähige Razor-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="129e9-115">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="129e9-116">Wenn eine nicht routingfähige Razor-Komponente [auf einer Seite eingebettet](xref:blazor/integrate-components#render-components-from-a-page-or-view) wird, wirken sich die Autorisierungskonventionen der Seite indirekt auf die Razor-Komponenten sowie auf den Rest des Seiteninhalts aus.</span><span class="sxs-lookup"><span data-stu-id="129e9-116">If a non-routable Razor component is [embedded in a page](xref:blazor/integrate-components#render-components-from-a-page-or-view), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="129e9-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> und <xref:Microsoft.AspNetCore.Identity.UserManager%601> werden in Razor-Komponenten nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="129e9-117"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="129e9-118">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="129e9-118">Authentication</span></span>

Blazor<span data-ttu-id="129e9-119"> verwendet die vorhandenen ASP.NET Core-Authentifizierungsmechanismen, um die Identität des Benutzers festzustellen.</span><span class="sxs-lookup"><span data-stu-id="129e9-119"> uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="129e9-120">Der genaue Mechanismus hängt davon ab, wie die Blazor-App gehostet wird: über Blazor WebAssembly oder Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="129e9-120">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="opno-locblazor-webassembly-authentication"></a>Blazor<span data-ttu-id="129e9-121"> WebAssembly-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="129e9-121"> WebAssembly authentication</span></span>

<span data-ttu-id="129e9-122">In den Blazor WebAssembly-Apps können Authentifizierungsprüfungen umgangen werden, da der gesamte clientseitige Code von Benutzern geändert werden kann.</span><span class="sxs-lookup"><span data-stu-id="129e9-122">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="129e9-123">Dasselbe gilt für alle clientseitigen App-Technologien, einschließlich JavaScript SPA-Frameworks oder native Apps für jedes Betriebssystem.</span><span class="sxs-lookup"><span data-stu-id="129e9-123">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="129e9-124">Fügen Sie Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="129e9-124">Add the following:</span></span>

* <span data-ttu-id="129e9-125">Einen Paketverweis auf [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) in der Projektdatei der App</span><span class="sxs-lookup"><span data-stu-id="129e9-125">A package reference for [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) to the app's project file.</span></span>
* <span data-ttu-id="129e9-126">Den Namespace `Microsoft.AspNetCore.Components.Authorization` für die Datei *_Imports.razor* der App</span><span class="sxs-lookup"><span data-stu-id="129e9-126">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's *_Imports.razor* file.</span></span>

<span data-ttu-id="129e9-127">Für die Verarbeitung der Authentifizierung wird die Implementierung eines integrierten oder benutzerdefinierten Diensts `AuthenticationStateProvider` in den folgenden Abschnitten behandelt.</span><span class="sxs-lookup"><span data-stu-id="129e9-127">To handle authentication, implementation of a built-in or custom `AuthenticationStateProvider` service is covered in the following sections.</span></span>

<span data-ttu-id="129e9-128">Weitere Informationen zur Erstellung von Apps und Konfigurationen finden Sie unter <xref:security/blazor/webassembly/index>.</span><span class="sxs-lookup"><span data-stu-id="129e9-128">For more information on creating apps and configuration, see <xref:security/blazor/webassembly/index>.</span></span>

### <a name="opno-locblazor-server-authentication"></a>Blazor<span data-ttu-id="129e9-129">-Serverauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="129e9-129"> Server authentication</span></span>

Blazor<span data-ttu-id="129e9-130"> Server-Apps funktionieren über eine Echtzeitverbindung, die mit SignalR erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="129e9-130"> Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="129e9-131">Die [Authentifizierung in SignalR-basierten Apps wird verarbeitet](xref:signalr/authn-and-authz), wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="129e9-131">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="129e9-132">Die Authentifizierung kann auf einem Cookie oder einem anderen Bearertoken basieren.</span><span class="sxs-lookup"><span data-stu-id="129e9-132">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="129e9-133">Weitere Informationen zur Erstellung von Apps und Konfigurationen finden Sie unter <xref:security/blazor/server>.</span><span class="sxs-lookup"><span data-stu-id="129e9-133">For more information on creating apps and configuration, see <xref:security/blazor/server>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="129e9-134">AuthenticationStateProvider-Dienst</span><span class="sxs-lookup"><span data-stu-id="129e9-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="129e9-135">Der integrierte Dienst `AuthenticationStateProvider` ruft die Daten zum Authentifizierungsstatus von `HttpContext.User` von ASP. NET Core ab.</span><span class="sxs-lookup"><span data-stu-id="129e9-135">The built-in `AuthenticationStateProvider` service obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="129e9-136">Auf diese Weise lässt sich der Authentifizierungsstatus in bestehende Authentifizierungsmechanismen von ASP.NET Core integrieren.</span><span class="sxs-lookup"><span data-stu-id="129e9-136">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="129e9-137">`AuthenticationStateProvider` ist der zugrunde liegende Dienst, der von der `AuthorizeView`- und der `CascadingAuthenticationState`-Komponente verwendet wird, um den Authentifizierungsstatus abzurufen.</span><span class="sxs-lookup"><span data-stu-id="129e9-137">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="129e9-138">`AuthenticationStateProvider` wird in der Regel nicht direkt verwendet.</span><span class="sxs-lookup"><span data-stu-id="129e9-138">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="129e9-139">Verwenden Sie einen der später in diesem Artikel beschriebenen Ansätze ([AuthorizeView-Komponente](#authorizeview-component) oder [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter)).</span><span class="sxs-lookup"><span data-stu-id="129e9-139">Use the [AuthorizeView component](#authorizeview-component) or [Task\<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="129e9-140">Der Hauptnachteil bei der direkten Verwendung von `AuthenticationStateProvider` ist, dass die Komponente nicht automatisch benachrichtigt wird, wenn sich die zugrunde liegenden Daten des Authentifizierungsstatus ändern.</span><span class="sxs-lookup"><span data-stu-id="129e9-140">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="129e9-141">Der Dienst `AuthenticationStateProvider` kann die <xref:System.Security.Claims.ClaimsPrincipal>-Daten des aktuellen Benutzers bereitstellen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="129e9-141">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type &ndash; @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="129e9-142">Wenn `user.Identity.IsAuthenticated` den Wert `true` hat und da der Benutzer ein <xref:System.Security.Claims.ClaimsPrincipal> ist, können Ansprüche aufgezählt und die Mitgliedschaft in Rollen ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="129e9-142">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="129e9-143">Weitere Informationen zur Dependency Injection (DI) und den Diensten finden Sie unter <xref:blazor/dependency-injection> und <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="129e9-143">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="129e9-144">Implementieren eines benutzerdefinierten AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="129e9-144">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="129e9-145">Wenn die App einen benutzerdefinierten Anbieter erfordert, implementieren Sie `AuthenticationStateProvider`, und überschreiben Sie `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="129e9-145">If the app requires a custom provider, implement `AuthenticationStateProvider` and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="129e9-146">In einer Blazor-WebAssembly-App wird der `CustomAuthStateProvider`-Dienst in `Main` von *Program.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="129e9-146">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="129e9-147">In einer Blazor Server-App ist der Dienst `CustomAuthStateProvider` in `Startup.ConfigureServices` registriert:</span><span class="sxs-lookup"><span data-stu-id="129e9-147">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="129e9-148">Bei der Verwendung von `CustomAuthStateProvider` im vorherigen Beispiel werden alle Benutzer mit dem Benutzernamen `mrfibuli` authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="129e9-148">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="129e9-149">Verfügbar machen des Authentifizierungsstatus als kaskadierender Parameter</span><span class="sxs-lookup"><span data-stu-id="129e9-149">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="129e9-150">Wenn für die prozedurale Logik Authentifizierungsstatusdaten erforderlich sind, z. B. bei der Ausführung einer vom Benutzer ausgelösten Aktion, können Sie die Authentifizierungsstatusdaten abrufen, indem Sie einen kaskadierenden Parameter vom Typ `Task<AuthenticationState>` definieren:</span><span class="sxs-lookup"><span data-stu-id="129e9-150">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="129e9-151">Wenn `user.Identity.IsAuthenticated` den Wert `true` hat, können Ansprüche aufgezählt und die Mitgliedschaft in Rollen ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="129e9-151">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="129e9-152">Richten Sie den kaskadierenden Parameter `Task<AuthenticationState>` mit den Komponenten `AuthorizeRouteView` und `CascadingAuthenticationState` in der Komponente `App`(*App.razor*) ein:</span><span class="sxs-lookup"><span data-stu-id="129e9-152">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components in the `App` component (*App.razor*):</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="129e9-153">Fügen Sie `Program.Main` in einer Blazor WebAssembly-App Dienste für Optionen und die Autorisierung hinzu:</span><span class="sxs-lookup"><span data-stu-id="129e9-153">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="129e9-154">In einer Blazor Server-App sind Dienste für Optionen und die Autorisierung bereits vorhanden. Es ist also keine weitere Aktion erforderlich.</span><span class="sxs-lookup"><span data-stu-id="129e9-154">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="129e9-155">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="129e9-155">Authorization</span></span>

<span data-ttu-id="129e9-156">Nachdem ein Benutzer authentifiziert wurde, werden *Autorisierungsregeln* angewendet, um zu steuern, welche Funktionen der Benutzer ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="129e9-156">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="129e9-157">In der Regel wird der Zugriff in Abhängigkeit von folgenden Punkten gewährt oder verweigert:</span><span class="sxs-lookup"><span data-stu-id="129e9-157">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="129e9-158">Ein Benutzer ist authentifiziert (angemeldet).</span><span class="sxs-lookup"><span data-stu-id="129e9-158">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="129e9-159">Ein Benutzer hat eine *Rolle*.</span><span class="sxs-lookup"><span data-stu-id="129e9-159">A user is in a *role*.</span></span>
* <span data-ttu-id="129e9-160">Ein Benutzer hat einen *Anspruch*.</span><span class="sxs-lookup"><span data-stu-id="129e9-160">A user has a *claim*.</span></span>
* <span data-ttu-id="129e9-161">Ein *Richtlinie* wird erfüllt.</span><span class="sxs-lookup"><span data-stu-id="129e9-161">A *policy* is satisfied.</span></span>

<span data-ttu-id="129e9-162">Jedes dieser Konzepte entspricht dem einer ASP.NET Core MVC- oder Razor Pages-App.</span><span class="sxs-lookup"><span data-stu-id="129e9-162">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="129e9-163">Weitere Informationen zu ASP.NET Core-Sicherheit finden Sie im Artikel unter [ASP.NET Core-Sicherheit und -Identität](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="129e9-163">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="129e9-164">AuthorizeView-Komponente</span><span class="sxs-lookup"><span data-stu-id="129e9-164">AuthorizeView component</span></span>

<span data-ttu-id="129e9-165">Die `AuthorizeView`-Komponente zeigt selektiv die Benutzeroberfläche an, abhängig davon, ob der Benutzer berechtigt ist, diese zu sehen.</span><span class="sxs-lookup"><span data-stu-id="129e9-165">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="129e9-166">Dieser Ansatz ist nützlich, wenn Sie nur Daten für den Benutzer *anzeigen* müssen und nicht die Identität des Benutzers in der prozeduralen Logik verwenden müssen.</span><span class="sxs-lookup"><span data-stu-id="129e9-166">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="129e9-167">Die Komponente macht eine Variable `context` vom Typ `AuthenticationState` verfügbar, mit der Sie auf Informationen über den angemeldeten Benutzer zugreifen können:</span><span class="sxs-lookup"><span data-stu-id="129e9-167">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="129e9-168">Sie können auch andere Inhalte zur Anzeige bereitstellen, wenn der Benutzer nicht authentifiziert ist:</span><span class="sxs-lookup"><span data-stu-id="129e9-168">You can also supply different content for display if the user isn't authenticated:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="129e9-169">Die Komponente `AuthorizeView` kann in der Komponente `NavMenu` (*Shared/NavMenu.razor*) verwendet werden, um ein Listenelement (`<li>...</li>`) für `NavLink` anzuzeigen. Beachten Sie jedoch, dass bei diesem Ansatz nur das Listenelement aus der gerenderten Ausgabe entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="129e9-169">The `AuthorizeView` component can be used in the `NavMenu` component (*Shared/NavMenu.razor*) to display a list item (`<li>...</li>`) for a `NavLink`, but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="129e9-170">Benutzer werden nicht daran gehindert, zur Komponente zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="129e9-170">It doesn't prevent the user from navigating to the component.</span></span>

<span data-ttu-id="129e9-171">Der Inhalt der Tags `<Authorized>` und `<NotAuthorized>` kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten.</span><span class="sxs-lookup"><span data-stu-id="129e9-171">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="129e9-172">Autorisierungsbedingungen, wie Rollen oder Richtlinien, die Benutzeroberflächenoptionen oder den Zugriff steuern, werden im Abschnitt [Autorisierung](#authorization) behandelt.</span><span class="sxs-lookup"><span data-stu-id="129e9-172">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="129e9-173">Wenn keine Autorisierungsbedingungen angegeben sind, verwendet `AuthorizeView` eine Standardrichtlinie und behandelt:</span><span class="sxs-lookup"><span data-stu-id="129e9-173">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="129e9-174">Authentifizierte (angemeldete) Benutzer als autorisiert.</span><span class="sxs-lookup"><span data-stu-id="129e9-174">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="129e9-175">Nicht authentifizierte (abgemeldete) Benutzer als nicht autorisiert.</span><span class="sxs-lookup"><span data-stu-id="129e9-175">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="129e9-176">Rollenbasierte und richtlinienbasierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="129e9-176">Role-based and policy-based authorization</span></span>

<span data-ttu-id="129e9-177">Die `AuthorizeView`-Komponente unterstützt die *rollenbasierte* oder die *richtlinienbasierte* Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="129e9-177">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="129e9-178">Verwenden Sie für die rollenbasierte Autorisierung den `Roles`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="129e9-178">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="129e9-179">Weitere Informationen finden Sie unter <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="129e9-179">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="129e9-180">Verwenden Sie für die richtlinienbasierte Autorisierung den `Policy`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="129e9-180">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="129e9-181">Die anspruchsbasierte Autorisierung ist ein Sonderfall der richtlinienbasierten Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="129e9-181">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="129e9-182">Beispielsweise können Sie eine Richtlinie definieren, die vom Benutzer einen bestimmten Anspruch erfordert.</span><span class="sxs-lookup"><span data-stu-id="129e9-182">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="129e9-183">Weitere Informationen finden Sie unter <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="129e9-183">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="129e9-184">Diese APIs können entweder in Blazor Server- oder in Blazor WebAssembly-Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="129e9-184">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="129e9-185">Wenn weder `Roles` noch `Policy` angegeben wird, verwendet `AuthorizeView` die Standardrichtlinie.</span><span class="sxs-lookup"><span data-stu-id="129e9-185">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="129e9-186">Während der asynchronen Authentifizierung angezeigter Inhalt</span><span class="sxs-lookup"><span data-stu-id="129e9-186">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="129e9-187">Bei Blazor kann der Authentifizierungsstatus *asynchron* bestimmt werden.</span><span class="sxs-lookup"><span data-stu-id="129e9-187">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="129e9-188">Das primäre Szenario für diesen Ansatz sind Blazor WebAssembly-Apps, die eine Anforderung zur Authentifizierung an einen externen Endpunkt stellen.</span><span class="sxs-lookup"><span data-stu-id="129e9-188">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="129e9-189">Während der Authentifizierung zeigt `AuthorizeView` standardmäßig keine Inhalte an.</span><span class="sxs-lookup"><span data-stu-id="129e9-189">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="129e9-190">Um während der Authentifizierung Inhalte anzuzeigen, wenden Sie das `<Authorizing>`-Element:</span><span class="sxs-lookup"><span data-stu-id="129e9-190">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="129e9-191">Dieser Ansatz gilt in der Regel nicht für Blazor Server-Apps.</span><span class="sxs-lookup"><span data-stu-id="129e9-191">This approach isn't normally applicable to Blazor Server apps.</span></span> Blazor<span data-ttu-id="129e9-192"> Server-Apps kennen den Authentifizierungsstatus, sobald der Status hergestellt ist.</span><span class="sxs-lookup"><span data-stu-id="129e9-192"> Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="129e9-193">`Authorizing`-Inhalte können zwar in der `AuthorizeView`-Komponente einer Blazor Server-App bereitgestellt werden, aber der Inhalt wird nie angezeigt.</span><span class="sxs-lookup"><span data-stu-id="129e9-193">`Authorizing` content can be provided in a Blazor Server app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="129e9-194">[Authorize]-Attribut</span><span class="sxs-lookup"><span data-stu-id="129e9-194">[Authorize] attribute</span></span>

<span data-ttu-id="129e9-195">Das Attribut `[Authorize]` kann in Razor-Komponenten verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="129e9-195">The `[Authorize]` attribute can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="129e9-196">Verwenden Sie nur `[Authorize]` für `@page`-Komponenten, die über den Blazor-Router erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="129e9-196">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="129e9-197">Die Autorisierung wird nur als Aspekt des Routings und *nicht* für untergeordnete Komponenten durchgeführt, die innerhalb einer Seite gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="129e9-197">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="129e9-198">Um die Anzeige von bestimmten Teilen innerhalb einer Seite zu autorisieren, verwenden Sie stattdessen `AuthorizeView`.</span><span class="sxs-lookup"><span data-stu-id="129e9-198">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="129e9-199">Das `[Authorize]`-Attribut unterstützt auch die rollenbasierte oder die richtlinienbasierte Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="129e9-199">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="129e9-200">Verwenden Sie für die rollenbasierte Autorisierung den `Roles`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="129e9-200">For role-based authorization, use the `Roles` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="129e9-201">Verwenden Sie für die richtlinienbasierte Autorisierung den `Policy`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="129e9-201">For policy-based authorization, use the `Policy` parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="129e9-202">Wenn weder `Roles` noch `Policy` angegeben wird, verwendet `[Authorize]` die Standardrichtlinie und behandelt:</span><span class="sxs-lookup"><span data-stu-id="129e9-202">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="129e9-203">Authentifizierte (angemeldete) Benutzer als autorisiert.</span><span class="sxs-lookup"><span data-stu-id="129e9-203">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="129e9-204">Nicht authentifizierte (abgemeldete) Benutzer als nicht autorisiert.</span><span class="sxs-lookup"><span data-stu-id="129e9-204">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="129e9-205">Anpassen von unautorisierten Inhalten mit der Router-Komponente</span><span class="sxs-lookup"><span data-stu-id="129e9-205">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="129e9-206">Gemeinsam mit der `AuthorizeRouteView`-Komponente ermöglicht die `Router`-Komponente der App, benutzerdefinierten Inhalt anzugeben, wenn:</span><span class="sxs-lookup"><span data-stu-id="129e9-206">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="129e9-207">Inhalt nicht gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="129e9-207">Content isn't found.</span></span>
* <span data-ttu-id="129e9-208">Der Benutzer eine `[Authorize]`-Bedingung nicht erfüllt, die für die Komponente angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="129e9-208">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="129e9-209">Das `[Authorize]`-Attribut wird im Abschnitt [`[Authorize]`-Attribut](#authorize-attribute) behandelt.</span><span class="sxs-lookup"><span data-stu-id="129e9-209">The `[Authorize]` attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="129e9-210">Die Asynchrone Authentifizierung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="129e9-210">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="129e9-211">In der Standardprojektvorlage für Blazor Server zeigt die Komponente `App` (*App.razor*), wie benutzerdefinierte Inhalte festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="129e9-211">In the default Blazor Server project template, the `App` component (*App.razor*) demonstrates how to set custom content:</span></span>

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
            <NotAuthorized>
                <h1>Sorry</h1>
                <p>You're not authorized to reach this page.</p>
                <p>You may need to log in as a different user.</p>
            </NotAuthorized>
            <Authorizing>
                <h1>Authentication in progress</h1>
                <p>Only visible while authentication is in progress.</p>
            </Authorizing>
        </AuthorizeRouteView>
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

<span data-ttu-id="129e9-212">Der Inhalt der Tags `<NotFound>`, `<NotAuthorized>` und `<Authorizing>` kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten.</span><span class="sxs-lookup"><span data-stu-id="129e9-212">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="129e9-213">Wenn das Element `<NotAuthorized>` nicht angegeben ist, verwendet `AuthorizeRouteView` die folgende alternative Meldung:</span><span class="sxs-lookup"><span data-stu-id="129e9-213">If the `<NotAuthorized>` element isn't specified, the `AuthorizeRouteView` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="129e9-214">Benachrichtigung über Änderungen des Authentifizierungsstatus</span><span class="sxs-lookup"><span data-stu-id="129e9-214">Notification about authentication state changes</span></span>

<span data-ttu-id="129e9-215">Wenn die App feststellt, dass sich die zugrunde liegenden Daten des Authentifizierungsstatus geändert haben (z. B. weil sich der Benutzer abgemeldet hat oder ein anderer Benutzer seine Rollen geändert hat), kann ein [benutzerdefinierter AuthenticationStateProvider-Dienst](#implement-a-custom-authenticationstateprovider) optional die Methode `NotifyAuthenticationStateChanged` in der Basisklasse `AuthenticationStateProvider` aufrufen.</span><span class="sxs-lookup"><span data-stu-id="129e9-215">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="129e9-216">Dadurch werden die Verbraucher der Authentifizierungsstatusdaten (z. B. `AuthorizeView`) informiert, dass der Rendervorgang mit den neuen Daten erneut durchgeführt werden muss.</span><span class="sxs-lookup"><span data-stu-id="129e9-216">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="129e9-217">Prozedurale Logik</span><span class="sxs-lookup"><span data-stu-id="129e9-217">Procedural logic</span></span>

<span data-ttu-id="129e9-218">Wenn die App zur Überprüfung von Autorisierungsregeln im Rahmen der prozeduralen Logik benötigt wird, verwenden Sie einen kaskadierten Parameter vom Typ `Task<AuthenticationState>`, um das <xref:System.Security.Claims.ClaimsPrincipal> des Benutzers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="129e9-218">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="129e9-219">`Task<AuthenticationState>` kann zum Auswerten von Richtlinien mit anderen Diensten kombiniert werden, wie z.B. `IAuthorizationService`.</span><span class="sxs-lookup"><span data-stu-id="129e9-219">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="129e9-220">Fügen Sie in einer Komponente einer `Microsoft.AspNetCore.Components.Authorization` WebAssembly-App die Namespaces Blazor und `Microsoft.AspNetCore.Authorization` hinzu:</span><span class="sxs-lookup"><span data-stu-id="129e9-220">In a Blazor WebAssembly app component, add the `Microsoft.AspNetCore.Authorization` and `Microsoft.AspNetCore.Components.Authorization` namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="129e9-221">Diese Namespaces können global bereitgestellt werden, indem Sie sie der Datei *_Imports.razor* der App hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="129e9-221">These namespaces can be provided globally by adding them to the app's *_Imports.razor* file.</span></span>

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a><span data-ttu-id="129e9-222">Autorisierung in Blazor WebAssembly-Apps</span><span class="sxs-lookup"><span data-stu-id="129e9-222">Authorization in Blazor WebAssembly apps</span></span>

<span data-ttu-id="129e9-223">In Blazor WebAssembly-Apps können Autorisierungsprüfungen umgangen werden, da der gesamte clientseitige Code von Benutzern geändert werden kann.</span><span class="sxs-lookup"><span data-stu-id="129e9-223">In Blazor WebAssembly apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="129e9-224">Dasselbe gilt für alle clientseitigen App-Technologien, einschließlich JavaScript SPA-Frameworks oder native Apps für jedes Betriebssystem.</span><span class="sxs-lookup"><span data-stu-id="129e9-224">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="129e9-225">**Führen Sie Autorisierungsprüfungen auf dem Server immer innerhalb aller API-Endpunkte durch, auf die Ihre clientseitige App zugreift.**</span><span class="sxs-lookup"><span data-stu-id="129e9-225">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

<span data-ttu-id="129e9-226">Weitere Informationen finden Sie in den Artikeln unter <xref:security/blazor/webassembly/index>.</span><span class="sxs-lookup"><span data-stu-id="129e9-226">For more information, see the articles under <xref:security/blazor/webassembly/index>.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="129e9-227">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="129e9-227">Troubleshoot errors</span></span>

<span data-ttu-id="129e9-228">Häufige Fehler:</span><span class="sxs-lookup"><span data-stu-id="129e9-228">Common errors:</span></span>

* <span data-ttu-id="129e9-229">**Die Autorisierung erfordert einen kaskadierenden Parameter vom Typ „Task\<AuthenticationState >“. Verwenden Sie für die Bereitstellung „CascadingAuthenticationState“.**</span><span class="sxs-lookup"><span data-stu-id="129e9-229">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="129e9-230">**Für `authenticationStateTask` wird ein `null`** -Wert empfangen.</span><span class="sxs-lookup"><span data-stu-id="129e9-230">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="129e9-231">Wahrscheinlich wurde das Projekt nicht mit einer Blazor Server-Vorlage mit aktivierter Authentifizierung erstellt.</span><span class="sxs-lookup"><span data-stu-id="129e9-231">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="129e9-232">Umschließen Sie einen Teil der Benutzeroberflächenstruktur wie folgt mit `<CascadingAuthenticationState>`, z. B. in der Komponente `App` (*App.razor*):</span><span class="sxs-lookup"><span data-stu-id="129e9-232">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (*App.razor*) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="129e9-233">`CascadingAuthenticationState` stellt den kaskadierenden Parameter `Task<AuthenticationState>` zur Verfügung, den er wiederum vom zugrunde liegenden DI-Dienst `AuthenticationStateProvider` empfängt.</span><span class="sxs-lookup"><span data-stu-id="129e9-233">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="129e9-234">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="129e9-234">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* <span data-ttu-id="129e9-235">[Nützliche Ressourcen für Blazor: Authentifizierung](https://github.com/AdrienTorris/awesome-blazor#authentication) – Beispiellinks für die Community</span><span class="sxs-lookup"><span data-stu-id="129e9-235">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
