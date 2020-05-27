---
<span data-ttu-id="bd5df-101">Title: "Sichern einer Blazor eigenständigen App mit ASP.net Core Webassembly mit der Authentifizierungs Bibliothek ' Author: Description: monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:</span><span class="sxs-lookup"><span data-stu-id="bd5df-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bd5df-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bd5df-102">'Blazor'</span></span>
- <span data-ttu-id="bd5df-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bd5df-103">'Identity'</span></span>
- <span data-ttu-id="bd5df-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bd5df-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="bd5df-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bd5df-105">'Razor'</span></span>
- <span data-ttu-id="bd5df-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="bd5df-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="bd5df-107">Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit der Authentifizierungs Bibliothek</span><span class="sxs-lookup"><span data-stu-id="bd5df-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="bd5df-108">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bd5df-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bd5df-109">*Befolgen Sie die Anweisungen in diesem Thema für Azure Active Directory (AAD) und Azure Active Directory B2C (AAD B2C). Weitere Informationen finden Sie in den Themen zu Aad und Aad B2C in diesem Knoten Inhaltsverzeichnis.*</span><span class="sxs-lookup"><span data-stu-id="bd5df-109">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="bd5df-110">BlazorFühren Sie den folgenden Befehl in einer Befehlsshell aus, um eine eigenständige Webassembly-APP zu erstellen, die die [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) -Bibliothek verwendet:</span><span class="sxs-lookup"><span data-stu-id="bd5df-110">To create a Blazor WebAssembly standalone app that uses [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="bd5df-111">Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem Pfad (z `-o BlazorSample` . b.) ein.</span><span class="sxs-lookup"><span data-stu-id="bd5df-111">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="bd5df-112">Der Name des Ordners wird auch Teil des Projekt namens.</span><span class="sxs-lookup"><span data-stu-id="bd5df-112">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="bd5df-113">Erstellen Sie in Visual Studio [eine Blazor Webassembly-App](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="bd5df-113">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="bd5df-114">Legen Sie die **Authentifizierung** auf **einzelne Benutzerkonten** fest, indem Sie die Option **Benutzerkonten in-APP speichern** auswählen.</span><span class="sxs-lookup"><span data-stu-id="bd5df-114">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="bd5df-115">Authentifizierungs Paket</span><span class="sxs-lookup"><span data-stu-id="bd5df-115">Authentication package</span></span>

<span data-ttu-id="bd5df-116">Wenn eine APP für die Verwendung einzelner Benutzerkonten erstellt wird, erhält die APP automatisch einen Paket Verweis für das [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) -Paket in der Projektdatei der app.</span><span class="sxs-lookup"><span data-stu-id="bd5df-116">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="bd5df-117">Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="bd5df-117">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="bd5df-118">Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="bd5df-118">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="bd5df-119">Unterstützung für Authentifizierungsdienst</span><span class="sxs-lookup"><span data-stu-id="bd5df-119">Authentication service support</span></span>

<span data-ttu-id="bd5df-120">Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> vom [Microsoft. aspnetcore. Components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) -Paket bereitgestellten Erweiterungsmethode registriert.</span><span class="sxs-lookup"><span data-stu-id="bd5df-120">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="bd5df-121">Diese Methode richtet die Dienste ein, die für die Interaktion der APP mit dem Identity Anbieter (IP) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="bd5df-121">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="bd5df-122">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="bd5df-122">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="bd5df-123">Die Konfiguration wird von der Datei " *wwwroot/appSettings. JSON* " bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="bd5df-123">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="bd5df-124">Die Authentifizierungs Unterstützung für eigenständige apps wird mithilfe von Open ID Connect (oidc) angeboten.</span><span class="sxs-lookup"><span data-stu-id="bd5df-124">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="bd5df-125">Die- <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer App mithilfe von oidc erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="bd5df-125">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="bd5df-126">Die für die Konfiguration der APP erforderlichen Werte können von der oidc-kompatiblen IP-Adresse abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="bd5df-126">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="bd5df-127">Rufen Sie die Werte ab, wenn Sie die APP registrieren, die in der Regel in Ihrem Onlineportal erfolgt.</span><span class="sxs-lookup"><span data-stu-id="bd5df-127">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="bd5df-128">Zugriffs Token-Bereiche</span><span class="sxs-lookup"><span data-stu-id="bd5df-128">Access token scopes</span></span>

<span data-ttu-id="bd5df-129">Die Blazor webassemblyvorlage konfiguriert die APP nicht automatisch, um ein Zugriffs Token für eine sichere API anzufordern.</span><span class="sxs-lookup"><span data-stu-id="bd5df-129">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="bd5df-130">Fügen Sie zum Bereitstellen eines Zugriffs Tokens als Teil des Anmelde Flusses den Gültigkeitsbereich zu den standardtokenbereichen von hinzu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="bd5df-130">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="bd5df-131">Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels *weitere Szenarien* :</span><span class="sxs-lookup"><span data-stu-id="bd5df-131">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="bd5df-132">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="bd5df-132">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="bd5df-133">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="bd5df-133">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="bd5df-134">Import Datei</span><span class="sxs-lookup"><span data-stu-id="bd5df-134">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="bd5df-135">Indexseite</span><span class="sxs-lookup"><span data-stu-id="bd5df-135">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="bd5df-136">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="bd5df-136">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="bd5df-137">Redirecttologin-Komponente</span><span class="sxs-lookup"><span data-stu-id="bd5df-137">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="bd5df-138">Logindisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="bd5df-138">LoginDisplay component</span></span>

<span data-ttu-id="bd5df-139">Die `LoginDisplay` Komponente (*Shared/logindisplay. Razor*) wird in der `MainLayout` Komponente (*Shared/MainLayout. Razor*) gerendert und verwaltet die folgenden Verhaltensweisen:</span><span class="sxs-lookup"><span data-stu-id="bd5df-139">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="bd5df-140">Für authentifizierte Benutzer:</span><span class="sxs-lookup"><span data-stu-id="bd5df-140">For authenticated users:</span></span>
  * <span data-ttu-id="bd5df-141">Zeigt den aktuellen Benutzernamen an.</span><span class="sxs-lookup"><span data-stu-id="bd5df-141">Displays the current username.</span></span>
  * <span data-ttu-id="bd5df-142">Bietet eine Schaltfläche, um sich von der APP abzumelden.</span><span class="sxs-lookup"><span data-stu-id="bd5df-142">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="bd5df-143">Für anonyme Benutzer bietet die Option zum Anmelden.</span><span class="sxs-lookup"><span data-stu-id="bd5df-143">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="bd5df-144">Authentifizierungs Komponente</span><span class="sxs-lookup"><span data-stu-id="bd5df-144">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="bd5df-145">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bd5df-145">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="bd5df-146">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client</span><span class="sxs-lookup"><span data-stu-id="bd5df-146">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
