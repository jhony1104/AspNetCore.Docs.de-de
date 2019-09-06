---
title: Authentifizierung und Autorisierung in ASP.NET Core Blazor
author: guardrex
description: Lernen Sie die Szenarien für die Authentifizierung und Autorisierung in Blazor kennen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/29/2019
uid: security/blazor/index
ms.openlocfilehash: 8714acbeb6e8a00992a601030811b24f53426b82
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310529"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="411fa-103">Authentifizierung und Autorisierung in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="411fa-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="411fa-104">Von [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="411fa-104">By [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="411fa-105">ASP.NET Core unterstützt die Konfiguration und Verwaltung der Sicherheit in Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="411fa-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="411fa-106">Es gibt jeweils unterschiedliche Sicherheitsszenarien für serverseitige und clientseitige Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="411fa-106">Security scenarios differ between Blazor server-side and client-side apps.</span></span> <span data-ttu-id="411fa-107">Da serverseitige Blazor-Apps auf dem Server ausgeführt werden, können Autorisierungsprüfungen Folgendes bestimmen:</span><span class="sxs-lookup"><span data-stu-id="411fa-107">Because Blazor server-side apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="411fa-108">Die UI-Optionen, die einem Benutzer angezeigt werden (z.B., welche Menüeinträge für einen Benutzer verfügbar sind).</span><span class="sxs-lookup"><span data-stu-id="411fa-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="411fa-109">Zugriffsregeln für Bereiche und Komponenten der App.</span><span class="sxs-lookup"><span data-stu-id="411fa-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="411fa-110">Clientseitige Blazor-Apps werden auf dem Client ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="411fa-110">Blazor client-side apps run on the client.</span></span> <span data-ttu-id="411fa-111">Die Autorisierung wird *nur* verwendet, um zu bestimmen, welche UI-Optionen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="411fa-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="411fa-112">Da clientseitige Prüfungen von einem Benutzer geändert oder umgangen werden können, kann eine clientseitige Blazor-App keine Autorisierungszugriffsregeln durchsetzen.</span><span class="sxs-lookup"><span data-stu-id="411fa-112">Since client-side checks can be modified or bypassed by a user, a Blazor client-side app can't enforce authorization access rules.</span></span>

## <a name="authentication"></a><span data-ttu-id="411fa-113">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="411fa-113">Authentication</span></span>

<span data-ttu-id="411fa-114">Blazor verwendet die vorhandenen ASP.NET Core-Authentifizierungsmechanismen, um die Identität des Benutzers festzustellen.</span><span class="sxs-lookup"><span data-stu-id="411fa-114">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="411fa-115">Der genaue Mechanismus hängt davon ab, wie die Blazor-App gehostet wird – serverseitig oder clientseitig.</span><span class="sxs-lookup"><span data-stu-id="411fa-115">The exact mechanism depends on how the Blazor app is hosted, server-side or client-side.</span></span>

### <a name="blazor-server-side-authentication"></a><span data-ttu-id="411fa-116">Serverseitige Blazor-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="411fa-116">Blazor server-side authentication</span></span>

<span data-ttu-id="411fa-117">Serverseitige Blazor-Apps funktionieren über eine Echtzeitverbindung, die mit SignalR erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="411fa-117">Blazor server-side apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="411fa-118">Die [Authentifizierung in SignalR-basierten Apps](xref:signalr/authn-and-authz) wird verarbeitet, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="411fa-118">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="411fa-119">Die Authentifizierung kann auf einem Cookie oder einem anderen Bearertoken basieren.</span><span class="sxs-lookup"><span data-stu-id="411fa-119">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="411fa-120">Die serverseitige Blazor-Projektvorlage kann die Authentifizierung für Sie einrichten, wenn das Projekt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="411fa-120">The Blazor server-side project template can set up authentication for you when the project is created.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="411fa-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="411fa-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="411fa-122">Befolgen Sie die Visual Studio-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues serverseitiges Blazor-Projekt mit einem Authentifizierungsmechanismus zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="411fa-122">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor server-side project with an authentication mechanism.</span></span>

<span data-ttu-id="411fa-123">Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor-Server-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern**.</span><span class="sxs-lookup"><span data-stu-id="411fa-123">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="411fa-124">Ein Dialogfeld wird geöffnet, in dem dieselben Authentifizierungsmechanismen angeboten werden, die auch für andere ASP.NET-Core-Projekte verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="411fa-124">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="411fa-125">**Keine Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="411fa-125">**No Authentication**</span></span>
* <span data-ttu-id="411fa-126">**Einzelne Benutzerkonten** &ndash; Benutzerkonten können gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="411fa-126">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="411fa-127">Innerhalb der App anhand des Systems [Identität](xref:security/authentication/identity) von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="411fa-127">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="411fa-128">Mit [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="411fa-128">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="411fa-129">**Geschäfts-, Schul- oder Unikonten**</span><span class="sxs-lookup"><span data-stu-id="411fa-129">**Work or School Accounts**</span></span>
* <span data-ttu-id="411fa-130">**Windows-Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="411fa-130">**Windows Authentication**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="411fa-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="411fa-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="411fa-132">Befolgen Sie die Visual Studio Code-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues serverseitiges Blazor-Projekt mit einem Authentifizierungsmechanismus zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="411fa-132">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor server-side project with an authentication mechanism:</span></span>

```console
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="411fa-133">Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="411fa-133">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="411fa-134">Authentifizierungsmechanismus</span><span class="sxs-lookup"><span data-stu-id="411fa-134">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="411fa-135">`{AUTHENTICATION}`-Wert</span><span class="sxs-lookup"><span data-stu-id="411fa-135">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="411fa-136">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="411fa-136">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="411fa-137">Einzelkonto</span><span class="sxs-lookup"><span data-stu-id="411fa-137">Individual</span></span><br><span data-ttu-id="411fa-138">In der App mit ASP.NET Core-Identität gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="411fa-138">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="411fa-139">Einzelkonto</span><span class="sxs-lookup"><span data-stu-id="411fa-139">Individual</span></span><br><span data-ttu-id="411fa-140">In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="411fa-140">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="411fa-141">Geschäfts-, Schul- oder Unikonten</span><span class="sxs-lookup"><span data-stu-id="411fa-141">Work or School Accounts</span></span><br><span data-ttu-id="411fa-142">Organisationauthentifizierung für einzelne Mandanten</span><span class="sxs-lookup"><span data-stu-id="411fa-142">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="411fa-143">Geschäfts-, Schul- oder Unikonten</span><span class="sxs-lookup"><span data-stu-id="411fa-143">Work or School Accounts</span></span><br><span data-ttu-id="411fa-144">Organisationauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="411fa-144">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="411fa-145">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="411fa-145">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="411fa-146">Der Befehl erstellt einen Ordner mit dem für den Platzhalter `{APP NAME}` angegebenen Wert und verwendet den Ordnernamen als Namen der App.</span><span class="sxs-lookup"><span data-stu-id="411fa-146">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="411fa-147">Weitere Informationen finden Sie im Befehl [dotnet new](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.</span><span class="sxs-lookup"><span data-stu-id="411fa-147">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

<!--

# [Visual Studio for Mac](#tab/visual-studio-mac)

1. Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.

1.

1.

-->

<!--
# [.NET Core CLI](#tab/netcore-cli/)

Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor server-side project with an authentication mechanism:

```console
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.

| Authentication mechanism                                                                 | `{AUTHENTICATION}` value |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| No Authentication                                                                        | `None`                   |
| Individual<br>Users stored in the app with ASP.NET Core Identity.                        | `Individual`             |
| Individual<br>Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c). | `IndividualB2C`          |
| Work or School Accounts<br>Organizational authentication for a single tenant.            | `SingleOrg`              |
| Work or School Accounts<br>Organizational authentication for multiple tenants.           | `MultiOrg`               |
| Windows Authentication                                                                   | `Windows`                |

The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name. For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.

-->

---

### <a name="blazor-client-side-authentication"></a><span data-ttu-id="411fa-148">Clientseitige Blazor-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="411fa-148">Blazor client-side authentication</span></span>

<span data-ttu-id="411fa-149">In den clientseitigen Blazor-Apps können Authentifizierungsprüfungen umgangen werden, da der gesamte clientseitige Code von Benutzern geändert werden kann.</span><span class="sxs-lookup"><span data-stu-id="411fa-149">In Blazor client-side apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="411fa-150">Dasselbe gilt für alle clientseitigen App-Technologien, einschließlich JavaScript SPA-Frameworks oder native Apps für jedes Betriebssystem.</span><span class="sxs-lookup"><span data-stu-id="411fa-150">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="411fa-151">Die Implementierung eines benutzerdefinierten `AuthenticationStateProvider`-Diensts für clientseitigen Blazor-Apps wird in den folgenden Abschnitten behandelt.</span><span class="sxs-lookup"><span data-stu-id="411fa-151">Implementation of a custom `AuthenticationStateProvider` service for Blazor client-side apps is covered in the following sections.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="411fa-152">AuthenticationStateProvider-Dienst</span><span class="sxs-lookup"><span data-stu-id="411fa-152">AuthenticationStateProvider service</span></span>

<span data-ttu-id="411fa-153">Die serverseitigen Blazor-Apps beinhalten einen integrierten `AuthenticationStateProvider`-Dienst, der die Authentifizierungsstatusdaten von `HttpContext.User` von ASP. NET Core abruft.</span><span class="sxs-lookup"><span data-stu-id="411fa-153">Blazor server-side apps include a built-in `AuthenticationStateProvider` service that obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="411fa-154">Auf diese Weise lässt sich der Authentifizierungsstatus in bestehende serverseitige Authentifizierungsmechanismen von ASP.NET Core integrieren.</span><span class="sxs-lookup"><span data-stu-id="411fa-154">This is how authentication state integrates with existing ASP.NET Core server-side authentication mechanisms.</span></span>

<span data-ttu-id="411fa-155">`AuthenticationStateProvider` ist der zugrunde liegende Dienst, der von der `AuthorizeView`- und der `CascadingAuthenticationState`-Komponente verwendet wird, um den Authentifizierungsstatus abzurufen.</span><span class="sxs-lookup"><span data-stu-id="411fa-155">`AuthenticationStateProvider` is the underlying service used by the `AuthorizeView` component and `CascadingAuthenticationState` component to get the authentication state.</span></span>

<span data-ttu-id="411fa-156">`AuthenticationStateProvider` wird in der Regel nicht direkt verwendet.</span><span class="sxs-lookup"><span data-stu-id="411fa-156">You don't typically use `AuthenticationStateProvider` directly.</span></span> <span data-ttu-id="411fa-157">Verwenden Sie die später in diesem Artikel beschriebenen Ansätze [AuthorizeView-Komponente](#authorizeview-component) oder [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter).</span><span class="sxs-lookup"><span data-stu-id="411fa-157">Use the [AuthorizeView component](#authorizeview-component) or [Task<AuthenticationState>](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="411fa-158">Der Hauptnachteil bei der direkten Verwendung von `AuthenticationStateProvider` ist, dass die Komponente nicht automatisch benachrichtigt wird, wenn sich die zugrunde liegenden Daten des Authentifizierungsstatus ändern.</span><span class="sxs-lookup"><span data-stu-id="411fa-158">The main drawback to using `AuthenticationStateProvider` directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="411fa-159">Der Dienst `AuthenticationStateProvider` kann die <xref:System.Security.Claims.ClaimsPrincipal>-Daten des aktuellen Benutzers bereitstellen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="411fa-159">The `AuthenticationStateProvider` service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```cshtml
@page "/"
@inject AuthenticationStateProvider AuthenticationStateProvider

<button @onclick="@LogUsername">Write user info to console</button>

@code {
    private async Task LogUsername()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

<span data-ttu-id="411fa-160">Wenn `user.Identity.IsAuthenticated` den Wert `true` hat und da der Benutzer ein <xref:System.Security.Claims.ClaimsPrincipal> ist, können Ansprüche aufgezählt und die Mitgliedschaft in Rollen ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="411fa-160">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="411fa-161">Weitere Informationen zur Dependency Injection (DI) und den Diensten finden Sie unter <xref:blazor/dependency-injection> und <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="411fa-161">For more information on dependency injection (DI) and services, see <xref:blazor/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="411fa-162">Implementieren eines benutzerdefinierten AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="411fa-162">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="411fa-163">Wenn Sie eine clientseitige Blazor-App erstellen, oder wenn die Spezifikation Ihrer App unbedingt einen benutzerdefinierten Anbieter erfordert, implementieren Sie einen Anbieter und überschreiben Sie `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="411fa-163">If you're building a Blazor client-side app or if your app's specification absolutely requires a custom provider, implement a provider and override `GetAuthenticationStateAsync`:</span></span>

```csharp
class CustomAuthStateProvider : AuthenticationStateProvider
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

<span data-ttu-id="411fa-164">Der `CustomAuthStateProvider`-Dienst ist in `Startup.ConfigureServices` registriert:</span><span class="sxs-lookup"><span data-stu-id="411fa-164">The `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
}
```

<span data-ttu-id="411fa-165">Bei der Verwendung von `CustomAuthStateProvider` werden alle Benutzer mit dem Benutzernamen `mrfibuli` authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="411fa-165">Using the `CustomAuthStateProvider`, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="411fa-166">Verfügbar machen des Authentifizierungsstatus als kaskadierender Parameter</span><span class="sxs-lookup"><span data-stu-id="411fa-166">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="411fa-167">Wenn für die prozedurale Logik Authentifizierungsstatusdaten erforderlich sind, z. B. bei der Ausführung einer vom Benutzer ausgelösten Aktion, können Sie die Authentifizierungsstatusdaten abrufen, indem Sie einen kaskadierenden Parameter vom Typ `Task<AuthenticationState>` definieren:</span><span class="sxs-lookup"><span data-stu-id="411fa-167">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<AuthenticationState>`:</span></span>

```cshtml
@page "/"

<button @onclick="@LogUsername">Log username</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            Console.WriteLine($"{user.Identity.Name} is authenticated.");
        }
        else
        {
            Console.WriteLine("The user is NOT authenticated.");
        }
    }
}
```

<span data-ttu-id="411fa-168">Wenn `user.Identity.IsAuthenticated` den Wert `true` hat, können Ansprüche aufgezählt und die Mitgliedschaft in Rollen ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="411fa-168">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="411fa-169">Richten Sie den kaskadierenden Parameter `Task<AuthenticationState>` mit den Komponenten `AuthorizeRouteView` und `CascadingAuthenticationState` ein:</span><span class="sxs-lookup"><span data-stu-id="411fa-169">Set up the `Task<AuthenticationState>` cascading parameter using the `AuthorizeRouteView` and `CascadingAuthenticationState` components:</span></span>

```cshtml
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

## <a name="authorization"></a><span data-ttu-id="411fa-170">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="411fa-170">Authorization</span></span>

<span data-ttu-id="411fa-171">Nachdem ein Benutzer authentifiziert wurde, werden *Autorisierungsregeln* angewendet, um zu steuern, welche Funktionen der Benutzer ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="411fa-171">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="411fa-172">In der Regel wird der Zugriff in Abhängigkeit von folgenden Punkten gewährt oder verweigert:</span><span class="sxs-lookup"><span data-stu-id="411fa-172">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="411fa-173">Ein Benutzer ist authentifiziert (angemeldet).</span><span class="sxs-lookup"><span data-stu-id="411fa-173">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="411fa-174">Ein Benutzer hat eine *Rolle*.</span><span class="sxs-lookup"><span data-stu-id="411fa-174">A user is in a *role*.</span></span>
* <span data-ttu-id="411fa-175">Ein Benutzer hat einen *Anspruch*.</span><span class="sxs-lookup"><span data-stu-id="411fa-175">A user has a *claim*.</span></span>
* <span data-ttu-id="411fa-176">Ein *Richtlinie* wird erfüllt.</span><span class="sxs-lookup"><span data-stu-id="411fa-176">A *policy* is satisfied.</span></span>

<span data-ttu-id="411fa-177">Jedes dieser Konzepte entspricht dem einer ASP.NET Core MVC- oder Razor Pages-App.</span><span class="sxs-lookup"><span data-stu-id="411fa-177">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="411fa-178">Weitere Informationen zu ASP.NET Core-Sicherheit finden Sie im Artikel unter [ASP.NET Core-Sicherheit und -Identität](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="411fa-178">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="411fa-179">AuthorizeView-Komponente</span><span class="sxs-lookup"><span data-stu-id="411fa-179">AuthorizeView component</span></span>

<span data-ttu-id="411fa-180">Die `AuthorizeView`-Komponente zeigt selektiv die Benutzeroberfläche an, abhängig davon, ob der Benutzer berechtigt ist, diese zu sehen.</span><span class="sxs-lookup"><span data-stu-id="411fa-180">The `AuthorizeView` component selectively displays UI depending on whether the user is authorized to see it.</span></span> <span data-ttu-id="411fa-181">Dieser Ansatz ist nützlich, wenn Sie nur Daten für den Benutzer *anzeigen* müssen und nicht die Identität des Benutzers in der prozeduralen Logik verwenden müssen.</span><span class="sxs-lookup"><span data-stu-id="411fa-181">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="411fa-182">Die Komponente macht eine Variable `context` vom Typ `AuthenticationState` verfügbar, mit der Sie auf Informationen über den angemeldeten Benutzer zugreifen können:</span><span class="sxs-lookup"><span data-stu-id="411fa-182">The component exposes a `context` variable of type `AuthenticationState`, which you can use to access information about the signed-in user:</span></span>

```cshtml
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="411fa-183">Sie können auch andere Inhalte zur Anzeige bereitstellen, wenn der Benutzer nicht authentifiziert ist:</span><span class="sxs-lookup"><span data-stu-id="411fa-183">You can also supply different content for display if the user isn't authenticated:</span></span>

```cshtml
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

<span data-ttu-id="411fa-184">Der Inhalt von `<Authorized>` und `<NotAuthorized>` kann beliebige Elemente beinhalten, wie beispielsweise andere interaktive Komponenten.</span><span class="sxs-lookup"><span data-stu-id="411fa-184">The content of `<Authorized>` and `<NotAuthorized>` can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="411fa-185">Autorisierungsbedingungen, wie Rollen oder Richtlinien, die Benutzeroberflächenoptionen oder den Zugriff steuern, werden im Abschnitt [Autorisierung](#authorization) behandelt.</span><span class="sxs-lookup"><span data-stu-id="411fa-185">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="411fa-186">Wenn keine Autorisierungsbedingungen angegeben sind, verwendet `AuthorizeView` eine Standardrichtlinie und behandelt:</span><span class="sxs-lookup"><span data-stu-id="411fa-186">If authorization conditions aren't specified, `AuthorizeView` uses a default policy and treats:</span></span>

* <span data-ttu-id="411fa-187">Authentifizierte (angemeldete) Benutzer als autorisiert.</span><span class="sxs-lookup"><span data-stu-id="411fa-187">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="411fa-188">Nicht authentifizierte (abgemeldete) Benutzer als nicht autorisiert.</span><span class="sxs-lookup"><span data-stu-id="411fa-188">Unauthenticated (signed-out) users as unauthorized.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="411fa-189">Rollenbasierte und richtlinienbasierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="411fa-189">Role-based and policy-based authorization</span></span>

<span data-ttu-id="411fa-190">Die `AuthorizeView`-Komponente unterstützt die *rollenbasierte* oder die *richtlinienbasierte* Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="411fa-190">The `AuthorizeView` component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="411fa-191">Verwenden Sie für die rollenbasierte Autorisierung den `Roles`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="411fa-191">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="411fa-192">Weitere Informationen finden Sie unter <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="411fa-192">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="411fa-193">Verwenden Sie für die richtlinienbasierte Autorisierung den `Policy`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="411fa-193">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="411fa-194">Die anspruchsbasierte Autorisierung ist ein Sonderfall der richtlinienbasierten Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="411fa-194">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="411fa-195">Beispielsweise können Sie eine Richtlinie definieren, die vom Benutzer einen bestimmten Anspruch erfordert.</span><span class="sxs-lookup"><span data-stu-id="411fa-195">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="411fa-196">Weitere Informationen finden Sie unter <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="411fa-196">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="411fa-197">Diese APIs können entweder in serverseitigen oder clientseitigen Blazor-Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="411fa-197">These APIs can be used in either Blazor server-side or Blazor client-side apps.</span></span>

<span data-ttu-id="411fa-198">Wenn weder `Roles` noch `Policy` angegeben wird, verwendet `AuthorizeView` die Standardrichtlinie.</span><span class="sxs-lookup"><span data-stu-id="411fa-198">If neither `Roles` nor `Policy` is specified, `AuthorizeView` uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="411fa-199">Während der asynchronen Authentifizierung angezeigter Inhalt</span><span class="sxs-lookup"><span data-stu-id="411fa-199">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="411fa-200">Bei Blazor kann der Authentifizierungsstatus *asynchron* bestimmt werden.</span><span class="sxs-lookup"><span data-stu-id="411fa-200">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="411fa-201">Das primäre Szenario für diesen Ansatz sind clientseitige Blazor-Apps, die eine Anforderung zur Authentifizierung an einen externen Endpunkt stellen.</span><span class="sxs-lookup"><span data-stu-id="411fa-201">The primary scenario for this approach is in Blazor client-side apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="411fa-202">Während der Authentifizierung zeigt `AuthorizeView` standardmäßig keine Inhalte an.</span><span class="sxs-lookup"><span data-stu-id="411fa-202">While authentication is in progress, `AuthorizeView` displays no content by default.</span></span> <span data-ttu-id="411fa-203">Um während der Authentifizierung Inhalte anzuzeigen, wenden Sie das `<Authorizing>`-Element:</span><span class="sxs-lookup"><span data-stu-id="411fa-203">To display content while authentication occurs, use the `<Authorizing>` element:</span></span>

```cshtml
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

<span data-ttu-id="411fa-204">Dieser Ansatz gilt in der Regel nicht für serverseitige Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="411fa-204">This approach isn't normally applicable to Blazor server-side apps.</span></span> <span data-ttu-id="411fa-205">Serverseitige Blazor-Apps kennen den Authentifizierungsstatus, sobald der Status hergestellt ist.</span><span class="sxs-lookup"><span data-stu-id="411fa-205">Blazor server-side apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="411fa-206">`Authorizing`-Inhalte können zwar in der `AuthorizeView`-Komponente einer serverseitigen Blazor-App bereitgestellt werden, aber der Inhalt wird nie angezeigt.</span><span class="sxs-lookup"><span data-stu-id="411fa-206">`Authorizing` content can be provided in a Blazor server-side app's `AuthorizeView` component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="411fa-207">[Authorize]-Attribut</span><span class="sxs-lookup"><span data-stu-id="411fa-207">[Authorize] attribute</span></span>

<span data-ttu-id="411fa-208">Genauso wie eine App `[Authorize]` mit einem MVC-Controller oder einer Razor Page verwenden kann, kann `[Authorize]` auch mit Razor-Komponenten verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="411fa-208">Just like an app can use `[Authorize]` with an MVC controller or Razor page, `[Authorize]` can also be used with Razor Components:</span></span>

```cshtml
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="411fa-209">Verwenden Sie nur `[Authorize]` auf `@page`-Komponenten, die über den Blazor-Router erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="411fa-209">Only use `[Authorize]` on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="411fa-210">Die Autorisierung wird nur als Aspekt des Routings und *nicht* für untergeordnete Komponenten durchgeführt, die innerhalb einer Seite gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="411fa-210">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="411fa-211">Um die Anzeige von bestimmten Teilen innerhalb einer Seite zu autorisieren, verwenden Sie stattdessen `AuthorizeView`.</span><span class="sxs-lookup"><span data-stu-id="411fa-211">To authorize the display of specific parts within a page, use `AuthorizeView` instead.</span></span>

<span data-ttu-id="411fa-212">Möglicherweise müssen Sie `@using Microsoft.AspNetCore.Authorization` entweder zur Komponente oder zur Datei *_Imports.razor* hinzufügen, damit die Komponente kompiliert werden kann.</span><span class="sxs-lookup"><span data-stu-id="411fa-212">You may need to add `@using Microsoft.AspNetCore.Authorization` either to the component or to the *_Imports.razor* file in order for the component to compile.</span></span>

<span data-ttu-id="411fa-213">Das `[Authorize]`-Attribut unterstützt auch die rollenbasierte oder die richtlinienbasierte Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="411fa-213">The `[Authorize]` attribute also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="411fa-214">Verwenden Sie für die rollenbasierte Autorisierung den `Roles`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="411fa-214">For role-based authorization, use the `Roles` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="411fa-215">Verwenden Sie für die richtlinienbasierte Autorisierung den `Policy`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="411fa-215">For policy-based authorization, use the `Policy` parameter:</span></span>

```cshtml
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="411fa-216">Wenn weder `Roles` noch `Policy` angegeben wird, verwendet `[Authorize]` die Standardrichtlinie und behandelt:</span><span class="sxs-lookup"><span data-stu-id="411fa-216">If neither `Roles` nor `Policy` is specified, `[Authorize]` uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="411fa-217">Authentifizierte (angemeldete) Benutzer als autorisiert.</span><span class="sxs-lookup"><span data-stu-id="411fa-217">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="411fa-218">Nicht authentifizierte (abgemeldete) Benutzer als nicht autorisiert.</span><span class="sxs-lookup"><span data-stu-id="411fa-218">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="411fa-219">Anpassen von unautorisierten Inhalten mit der Router-Komponente</span><span class="sxs-lookup"><span data-stu-id="411fa-219">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="411fa-220">Gemeinsam mit der `AuthorizeRouteView`-Komponente ermöglicht die `Router`-Komponente der App, benutzerdefinierten Inhalt anzugeben, wenn:</span><span class="sxs-lookup"><span data-stu-id="411fa-220">The `Router` component, in conjunction with the `AuthorizeRouteView` component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="411fa-221">Inhalt nicht gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="411fa-221">Content isn't found.</span></span>
* <span data-ttu-id="411fa-222">Der Benutzer eine `[Authorize]`-Bedingung nicht erfüllt, die für die Komponente angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="411fa-222">The user fails an `[Authorize]` condition applied to the component.</span></span> <span data-ttu-id="411fa-223">Das `[Authorize]`-Attribut wird im Abschnitt [[Authorize]-Attribut](#authorize-attribute) behandelt.</span><span class="sxs-lookup"><span data-stu-id="411fa-223">The `[Authorize]` attribute is covered in the [[Authorize] attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="411fa-224">Die Asynchrone Authentifizierung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="411fa-224">Asynchronous authentication is in progress.</span></span>

<span data-ttu-id="411fa-225">In der standardmäßigen serverseitigen Blazor-Projektvorlage zeigt die Datei *App.razor*, wie benutzerdefinierte Inhalte eingestellt werden können:</span><span class="sxs-lookup"><span data-stu-id="411fa-225">In the default Blazor server-side project template, the *App.razor* file demonstrates how to set custom content:</span></span>

```cshtml
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

<span data-ttu-id="411fa-226">Der Inhalt von `<NotFound>`, `<NotAuthorized>` und `<Authorizing>` kann beliebige Elemente beinhalten, wie beispielsweise andere interaktive Komponenten.</span><span class="sxs-lookup"><span data-stu-id="411fa-226">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="411fa-227">Wenn `<NotAuthorized>` nicht angegeben ist, verwendet `<AuthorizeRouteView>` die folgende alternative Meldung:</span><span class="sxs-lookup"><span data-stu-id="411fa-227">If `<NotAuthorized>` isn't specified, the `<AuthorizeRouteView>` uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="411fa-228">Benachrichtigung über Änderungen des Authentifizierungsstatus</span><span class="sxs-lookup"><span data-stu-id="411fa-228">Notification about authentication state changes</span></span>

<span data-ttu-id="411fa-229">Wenn die App feststellt, dass sich die zugrunde liegenden Daten des Authentifizierungsstatus geändert haben (z.B. weil sich der Benutzer abgemeldet hat oder ein anderer Benutzer seine Rollen geändert hat), kann ein benutzerdefinierter `AuthenticationStateProvider` optional die Methode `NotifyAuthenticationStateChanged` in der Basisklasse `AuthenticationStateProvider` aufrufen.</span><span class="sxs-lookup"><span data-stu-id="411fa-229">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a custom `AuthenticationStateProvider` can optionally invoke the method `NotifyAuthenticationStateChanged` on the `AuthenticationStateProvider` base class.</span></span> <span data-ttu-id="411fa-230">Dadurch werden die Verbraucher der Authentifizierungsstatusdaten (z. B. `AuthorizeView`) informiert, dass der Rendervorgang mit den neuen Daten erneut durchgeführt werden muss.</span><span class="sxs-lookup"><span data-stu-id="411fa-230">This notifies consumers of the authentication state data (for example, `AuthorizeView`) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="411fa-231">Prozedurale Logik</span><span class="sxs-lookup"><span data-stu-id="411fa-231">Procedural logic</span></span>

<span data-ttu-id="411fa-232">Wenn die App zur Überprüfung von Autorisierungsregeln im Rahmen der prozeduralen Logik benötigt wird, verwenden Sie einen kaskadierten Parameter vom Typ `Task<AuthenticationState>`, um das <xref:System.Security.Claims.ClaimsPrincipal> des Benutzers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="411fa-232">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<AuthenticationState>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="411fa-233">`Task<AuthenticationState>` kann zum Auswerten von Richtlinien mit anderen Diensten kombiniert werden, wie z.B. `IAuthorizationService`.</span><span class="sxs-lookup"><span data-stu-id="411fa-233">`Task<AuthenticationState>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```cshtml
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

## <a name="authorization-in-blazor-client-side-apps"></a><span data-ttu-id="411fa-234">Autorisierung in clientseitigen Blazor-Apps</span><span class="sxs-lookup"><span data-stu-id="411fa-234">Authorization in Blazor client-side apps</span></span>

<span data-ttu-id="411fa-235">In den clientseitigen Blazor-Apps können Autorisierungsprüfungen umgangen werden, da der gesamte clientseitige Code von Benutzern geändert werden kann.</span><span class="sxs-lookup"><span data-stu-id="411fa-235">In Blazor client-side apps, authorization checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="411fa-236">Dasselbe gilt für alle clientseitigen App-Technologien, einschließlich JavaScript SPA-Frameworks oder native Apps für jedes Betriebssystem.</span><span class="sxs-lookup"><span data-stu-id="411fa-236">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="411fa-237">**Führen Sie Autorisierungsprüfungen auf dem Server immer innerhalb aller API-Endpunkte durch, auf die Ihre clientseitige App zugreift.**</span><span class="sxs-lookup"><span data-stu-id="411fa-237">**Always perform authorization checks on the server within any API endpoints accessed by your client-side app.**</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="411fa-238">Beheben von Fehlern</span><span class="sxs-lookup"><span data-stu-id="411fa-238">Troubleshoot errors</span></span>

<span data-ttu-id="411fa-239">Häufige Fehler:</span><span class="sxs-lookup"><span data-stu-id="411fa-239">Common errors:</span></span>

* <span data-ttu-id="411fa-240">**Die Autorisierung erfordert einen kaskadierenden Parameter vom Typ „Task\<AuthenticationState >“. Verwenden Sie für die Bereitstellung „CascadingAuthenticationState“.**</span><span class="sxs-lookup"><span data-stu-id="411fa-240">**Authorization requires a cascading parameter of type Task\<AuthenticationState>. Consider using CascadingAuthenticationState to supply this.**</span></span>

* <span data-ttu-id="411fa-241">**Für `authenticationStateTask` wird ein `null`** -Wert empfangen.</span><span class="sxs-lookup"><span data-stu-id="411fa-241">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="411fa-242">Wahrscheinlich wurde das Projekt nicht mit einer serverseitigen Blazor-Vorlage mit aktivierter Authentifizierung erstellt.</span><span class="sxs-lookup"><span data-stu-id="411fa-242">It's likely that the project wasn't created using a Blazor server-side template with authentication enabled.</span></span> <span data-ttu-id="411fa-243">Umschließen Sie einen Teil der Benutzeroberflächenstruktur wie folgt mit `<CascadingAuthenticationState>`, z.B. in *App.razor*:</span><span class="sxs-lookup"><span data-stu-id="411fa-243">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in *App.razor* as follows:</span></span>

```cshtml
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

<span data-ttu-id="411fa-244">`CascadingAuthenticationState` stellt den kaskadierenden Parameter `Task<AuthenticationState>` zur Verfügung, den er wiederum vom zugrunde liegenden DI-Dienst `AuthenticationStateProvider` empfängt.</span><span class="sxs-lookup"><span data-stu-id="411fa-244">The `CascadingAuthenticationState` supplies the `Task<AuthenticationState>` cascading parameter, which in turn it receives from the underlying `AuthenticationStateProvider` DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="411fa-245">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="411fa-245">Additional resources</span></span>

* <xref:security/index>
* <xref:security/blazor/server-side>
* <xref:security/authentication/windowsauth>
