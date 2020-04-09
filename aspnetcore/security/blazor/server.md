---
title: Sichere ASP.NET Blazor Core Server-Apps
author: guardrex
description: Erfahren Sie, wie Blazor Sie Sicherheitsbedrohungen für Server-Apps verringern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626023"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a><span data-ttu-id="37580-103">Sichere ASP.NET Core Blazor Server-Apps</span><span class="sxs-lookup"><span data-stu-id="37580-103">Secure ASP.NET Core Blazor Server apps</span></span>

<span data-ttu-id="37580-104">Von [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="37580-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="37580-105">Blazor Server-Apps verwenden ein *zustandsbehaftetes* Datenverarbeitungsmodell, bei dem Server und Client eine langjährige Beziehung pflegen.</span><span class="sxs-lookup"><span data-stu-id="37580-105">Blazor Server apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="37580-106">Der persistente Zustand wird von einer [Schaltung](xref:blazor/state-management)verwaltet, die Verbindungen überspannen kann, die auch potenziell langlebig sind.</span><span class="sxs-lookup"><span data-stu-id="37580-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="37580-107">Wenn ein Benutzer eine Blazor Server-Site besucht, erstellt der Server eine Schaltung im Speicher des Servers.</span><span class="sxs-lookup"><span data-stu-id="37580-107">When a user visits a Blazor Server site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="37580-108">Die Schaltung zeigt dem Browser an, welche Inhalte gerendert werden sollen, und reagiert auf Ereignisse, z. B. wenn der Benutzer eine Schaltfläche in der Benutzeroberfläche auswählt.</span><span class="sxs-lookup"><span data-stu-id="37580-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="37580-109">Um diese Aktionen auszuführen, ruft eine Verbindung JavaScript-Funktionen im Browser des Benutzers und .NET-Methoden auf dem Server auf.</span><span class="sxs-lookup"><span data-stu-id="37580-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="37580-110">Diese zweiseitige JavaScript-basierte Interaktion wird als [JavaScript-Interop (JS-Interop)](xref:blazor/call-javascript-from-dotnet)bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="37580-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="37580-111">Da JS-Interop über das Internet auftritt und der Client einen Remotebrowser verwendet, teilen Blazor Server-Apps die meisten Sicherheitsbedenken für Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="37580-111">Because JS interop occurs over the Internet and the client uses a remote browser, Blazor Server apps share most web app security concerns.</span></span> <span data-ttu-id="37580-112">In diesem Thema werden häufige Bedrohungen für Blazor Server-Apps beschrieben und Anleitungen zur Bedrohungsminderung bereitgestellt, die sich auf Apps mit Internetanschluss konzentrieren.</span><span class="sxs-lookup"><span data-stu-id="37580-112">This topic describes common threats to Blazor Server apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="37580-113">In eingeschränkten Umgebungen, z. B. in Unternehmensnetzwerken oder Intranets, finden Sie einige der Minderungsrichtlinien:</span><span class="sxs-lookup"><span data-stu-id="37580-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="37580-114">Gilt nicht in der eingeschränkten Umgebung.</span><span class="sxs-lookup"><span data-stu-id="37580-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="37580-115">Die Implementierung lohnt sich nicht, da das Sicherheitsrisiko in einer eingeschränkten Umgebung gering ist.</span><span class="sxs-lookup"><span data-stu-id="37580-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="blazor-server-project-template"></a><span data-ttu-id="37580-116">Blazor Server-Projektvorlage</span><span class="sxs-lookup"><span data-stu-id="37580-116">Blazor Server project template</span></span>

<span data-ttu-id="37580-117">Die Blazor Server-Projektvorlage kann für die Authentifizierung konfiguriert werden, wenn das Projekt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="37580-117">The Blazor Server project template can be configured for authentication when the project is created.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="37580-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="37580-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="37580-119">Befolgen Sie die Visual Studio-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="37580-119">Follow the Visual Studio guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism.</span></span>

<span data-ttu-id="37580-120">Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor-Server-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern**.</span><span class="sxs-lookup"><span data-stu-id="37580-120">After choosing the **Blazor Server App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

<span data-ttu-id="37580-121">Ein Dialogfeld wird geöffnet, in dem dieselben Authentifizierungsmechanismen angeboten werden, die auch für andere ASP.NET-Core-Projekte verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="37580-121">A dialog opens to offer the same set of authentication mechanisms available for other ASP.NET Core projects:</span></span>

* <span data-ttu-id="37580-122">**Keine Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="37580-122">**No Authentication**</span></span>
* <span data-ttu-id="37580-123">**Einzelne Benutzerkonten** &ndash; Benutzerkonten können gespeichert werden:</span><span class="sxs-lookup"><span data-stu-id="37580-123">**Individual User Accounts** &ndash; User accounts can be stored:</span></span>
  * <span data-ttu-id="37580-124">Innerhalb der App anhand des Systems [Identität](xref:security/authentication/identity) von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="37580-124">Within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>
  * <span data-ttu-id="37580-125">Mit [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span><span class="sxs-lookup"><span data-stu-id="37580-125">With [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span>
* <span data-ttu-id="37580-126">**Arbeits- oder Schulkonten**</span><span class="sxs-lookup"><span data-stu-id="37580-126">**Work or School Accounts**</span></span>
* <span data-ttu-id="37580-127">**Windows-Authentifizierung**</span><span class="sxs-lookup"><span data-stu-id="37580-127">**Windows Authentication**</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="37580-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="37580-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="37580-129">Befolgen Sie die Visual Studio Code-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="37580-129">Follow the Visual Studio Code guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="37580-130">Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="37580-130">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="37580-131">Authentifizierungsmechanismus</span><span class="sxs-lookup"><span data-stu-id="37580-131">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="37580-132">Wert vom Typ `{AUTHENTICATION}`</span><span class="sxs-lookup"><span data-stu-id="37580-132">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="37580-133">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="37580-133">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="37580-134">Einzelkonto</span><span class="sxs-lookup"><span data-stu-id="37580-134">Individual</span></span><br><span data-ttu-id="37580-135">In der App mit ASP.NET Core-Identität gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="37580-135">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="37580-136">Einzelkonto</span><span class="sxs-lookup"><span data-stu-id="37580-136">Individual</span></span><br><span data-ttu-id="37580-137">In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="37580-137">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="37580-138">Geschäfts-, Schul- oder Unikonten</span><span class="sxs-lookup"><span data-stu-id="37580-138">Work or School Accounts</span></span><br><span data-ttu-id="37580-139">Organisationauthentifizierung für einzelne Mandanten</span><span class="sxs-lookup"><span data-stu-id="37580-139">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="37580-140">Geschäfts-, Schul- oder Unikonten</span><span class="sxs-lookup"><span data-stu-id="37580-140">Work or School Accounts</span></span><br><span data-ttu-id="37580-141">Organisationauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="37580-141">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="37580-142">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="37580-142">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="37580-143">Der Befehl erstellt einen Ordner mit dem für den Platzhalter `{APP NAME}` angegebenen Wert und verwendet den Ordnernamen als Namen der App.</span><span class="sxs-lookup"><span data-stu-id="37580-143">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="37580-144">Weitere Informationen finden Sie im Befehl [dotnet new](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.</span><span class="sxs-lookup"><span data-stu-id="37580-144">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="37580-145">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="37580-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="37580-146">Befolgen Sie die Anleitung <xref:blazor/get-started> von Visual Studio für Mac im Artikel.</span><span class="sxs-lookup"><span data-stu-id="37580-146">Follow the Visual Studio for Mac guidance in the <xref:blazor/get-started> article.</span></span>

1. <span data-ttu-id="37580-147">Wählen Sie im Schritt **Konfigurieren der neuen Blazor Server-App** im Dropdown-Dropdown **"Authentifizierung"** **(in-app) die Option Individuelle Authentifizierung (in-App)** aus.</span><span class="sxs-lookup"><span data-stu-id="37580-147">On the **Configure your new Blazor Server App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="37580-148">Die App wird für einzelne Benutzer erstellt, die in der App mit ASP.NET Core Identity gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="37580-148">The app is created for individual users stored in the app with ASP.NET Core Identity.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="37580-149">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="37580-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="37580-150">Befolgen Sie die Richtlinien <xref:blazor/get-started> von .NET Core CLI im Artikel, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="37580-150">Follow the .NET Core CLI guidance in the <xref:blazor/get-started> article to create a new Blazor Server project with an authentication mechanism:</span></span>

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

<span data-ttu-id="37580-151">Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="37580-151">Permissible authentication values (`{AUTHENTICATION}`) are shown in the following table.</span></span>

| <span data-ttu-id="37580-152">Authentifizierungsmechanismus</span><span class="sxs-lookup"><span data-stu-id="37580-152">Authentication mechanism</span></span>                                                                 | <span data-ttu-id="37580-153">Wert vom Typ `{AUTHENTICATION}`</span><span class="sxs-lookup"><span data-stu-id="37580-153">`{AUTHENTICATION}` value</span></span> |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| <span data-ttu-id="37580-154">Keine Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="37580-154">No Authentication</span></span>                                                                        | `None`                   |
| <span data-ttu-id="37580-155">Einzelkonto</span><span class="sxs-lookup"><span data-stu-id="37580-155">Individual</span></span><br><span data-ttu-id="37580-156">In der App mit ASP.NET Core-Identität gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="37580-156">Users stored in the app with ASP.NET Core Identity.</span></span>                        | `Individual`             |
| <span data-ttu-id="37580-157">Einzelkonto</span><span class="sxs-lookup"><span data-stu-id="37580-157">Individual</span></span><br><span data-ttu-id="37580-158">In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer</span><span class="sxs-lookup"><span data-stu-id="37580-158">Users stored in [Azure AD B2C](xref:security/authentication/azure-ad-b2c).</span></span> | `IndividualB2C`          |
| <span data-ttu-id="37580-159">Geschäfts-, Schul- oder Unikonten</span><span class="sxs-lookup"><span data-stu-id="37580-159">Work or School Accounts</span></span><br><span data-ttu-id="37580-160">Organisationauthentifizierung für einzelne Mandanten</span><span class="sxs-lookup"><span data-stu-id="37580-160">Organizational authentication for a single tenant.</span></span>            | `SingleOrg`              |
| <span data-ttu-id="37580-161">Geschäfts-, Schul- oder Unikonten</span><span class="sxs-lookup"><span data-stu-id="37580-161">Work or School Accounts</span></span><br><span data-ttu-id="37580-162">Organisationauthentifizierung für mehrere Mandanten</span><span class="sxs-lookup"><span data-stu-id="37580-162">Organizational authentication for multiple tenants.</span></span>           | `MultiOrg`               |
| <span data-ttu-id="37580-163">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="37580-163">Windows Authentication</span></span>                                                                   | `Windows`                |

<span data-ttu-id="37580-164">Der Befehl erstellt einen Ordner mit dem für den Platzhalter `{APP NAME}` angegebenen Wert und verwendet den Ordnernamen als Namen der App.</span><span class="sxs-lookup"><span data-stu-id="37580-164">The command creates a folder named with the value provided for the `{APP NAME}` placeholder and uses the folder name as the app's name.</span></span> <span data-ttu-id="37580-165">Weitere Informationen finden Sie im Befehl [dotnet new](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.</span><span class="sxs-lookup"><span data-stu-id="37580-165">For more information, see the [dotnet new](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

---

## <a name="pass-tokens-to-a-blazor-server-app"></a><span data-ttu-id="37580-166">Übergeben von Token an eine Blazor Server-App</span><span class="sxs-lookup"><span data-stu-id="37580-166">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="37580-167">Authentifizieren Sie die Blazor Server-App wie mit einer normalen Razor Pages- oder MVC-App.</span><span class="sxs-lookup"><span data-stu-id="37580-167">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="37580-168">Bereitstellen und Speichern der Token im Authentifizierungscookie.</span><span class="sxs-lookup"><span data-stu-id="37580-168">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="37580-169">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="37580-169">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

<span data-ttu-id="37580-170">Beispielcode, einschließlich eines `Startup.ConfigureServices` vollständigen Beispiels, finden Sie in den [Übergabetoken an eine serverseitige Blazor-Anwendung](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="37580-170">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="37580-171">Definieren Sie eine Klasse, die im ursprünglichen App-Status mit den Zugriffs- und Aktualisierungstoken übergeben werden soll:</span><span class="sxs-lookup"><span data-stu-id="37580-171">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="37580-172">Definieren **scoped** Sie einen Bereichstokenanbieterdienst, der in der Blazor-App verwendet werden kann, um die Token aus DI aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="37580-172">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from DI:</span></span>

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="37580-173">In `Startup.ConfigureServices`fügen Sie Dienste hinzu für:</span><span class="sxs-lookup"><span data-stu-id="37580-173">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="37580-174">Erstellen und instanziausen sie in `InitialApplicationState` der Datei *_Host.cshtml* erstellen und instanzionnen und übergeben Sie sie als Parameter an die App:</span><span class="sxs-lookup"><span data-stu-id="37580-174">In the *_Host.cshtml* file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="37580-175">Lösen `App` Sie in der Komponente (*App.razor*) den Dienst auf und initialisieren Sie ihn mit den Daten aus dem Parameter:</span><span class="sxs-lookup"><span data-stu-id="37580-175">In the `App` component (*App.razor*), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="37580-176">Fügen Sie in dem Dienst, der eine sichere API-Anforderung ausführt, den Tokenanbieter ein, und rufen Sie das Token ab, um die API aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="37580-176">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a><span data-ttu-id="37580-177">Ressourcenerschöpfung</span><span class="sxs-lookup"><span data-stu-id="37580-177">Resource exhaustion</span></span>

<span data-ttu-id="37580-178">Ressourcenerschöpfung kann auftreten, wenn ein Client mit dem Server interagiert und dazu führt, dass der Server übermäßige Ressourcen verbraucht.</span><span class="sxs-lookup"><span data-stu-id="37580-178">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="37580-179">Übermäßiger Ressourcenverbrauch wirkt sich in erster Linie auf:</span><span class="sxs-lookup"><span data-stu-id="37580-179">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="37580-180">CPU</span><span class="sxs-lookup"><span data-stu-id="37580-180">CPU</span></span>](#cpu)
* [<span data-ttu-id="37580-181">Memory</span><span class="sxs-lookup"><span data-stu-id="37580-181">Memory</span></span>](#memory)
* [<span data-ttu-id="37580-182">Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="37580-182">Client connections</span></span>](#client-connections)

<span data-ttu-id="37580-183">DoS-Angriffe (Denial of Service) zielen in der Regel darauf ab, die Ressourcen einer App oder eines Servers auszuschöpfen.</span><span class="sxs-lookup"><span data-stu-id="37580-183">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="37580-184">Die Ressourcenerschöpfung ist jedoch nicht unbedingt das Ergebnis eines Angriffs auf das System.</span><span class="sxs-lookup"><span data-stu-id="37580-184">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="37580-185">Beispielsweise können endliche Ressourcen aufgrund der hohen Benutzernachfrage erschöpft sein.</span><span class="sxs-lookup"><span data-stu-id="37580-185">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="37580-186">DoS wird im Abschnitt [DoS-Angriffe (Denial of Service)](#denial-of-service-dos-attacks) weiter behandelt.</span><span class="sxs-lookup"><span data-stu-id="37580-186">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="37580-187">Ressourcen außerhalb des Blazor-Frameworks, z. B. Datenbanken und Dateihandles (zum Lesen und Schreiben von Dateien), können ebenfalls ressourcenerabschöpfung erfahren.</span><span class="sxs-lookup"><span data-stu-id="37580-187">Resources external to the Blazor framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="37580-188">Weitere Informationen finden Sie unter <xref:performance/performance-best-practices>.</span><span class="sxs-lookup"><span data-stu-id="37580-188">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="37580-189">CPU</span><span class="sxs-lookup"><span data-stu-id="37580-189">CPU</span></span>

<span data-ttu-id="37580-190">CPU-Erschöpfung kann auftreten, wenn ein oder mehrere Clients den Server zwingen, intensive CPU-Arbeiten durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="37580-190">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="37580-191">Betrachten Sie beispielsweise eine Blazor Server-App, die eine *Fibonnacci-Nummer*berechnet.</span><span class="sxs-lookup"><span data-stu-id="37580-191">For example, consider a Blazor Server app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="37580-192">Eine Fibonnacci-Nummer wird aus einer Fibonnacci-Sequenz erzeugt, wobei jede Zahl in der Sequenz die Summe der beiden vorhergehenden Zahlen ist.</span><span class="sxs-lookup"><span data-stu-id="37580-192">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="37580-193">Der Arbeitsaufwand, der erforderlich ist, um die Antwort zu erreichen, hängt von der Länge der Sequenz und der Größe des Anfangswerts ab.</span><span class="sxs-lookup"><span data-stu-id="37580-193">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="37580-194">Wenn die App der Anforderung eines Clients keine Grenzen einhält, können die CPU-intensiven Berechnungen die Zeit der CPU dominieren und die Leistung anderer Aufgaben beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="37580-194">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="37580-195">Übermäßiger Ressourcenverbrauch ist ein Sicherheitsproblem, das sich auf die Verfügbarkeit auswirkt.</span><span class="sxs-lookup"><span data-stu-id="37580-195">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="37580-196">CPU-Erschöpfung ist ein Problem für alle öffentlich zugänglichen Apps.</span><span class="sxs-lookup"><span data-stu-id="37580-196">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="37580-197">In normalen Web-Apps, Anfragen und Verbindungen Zeitauszeit als Schutz, aber Blazor Server-Apps bieten nicht die gleichen Sicherheitsvorkehrungen.</span><span class="sxs-lookup"><span data-stu-id="37580-197">In regular web apps, requests and connections time out as a safeguard, but Blazor Server apps don't provide the same safeguards.</span></span> <span data-ttu-id="37580-198">Blazor Server-Apps müssen entsprechende Prüfungen und Grenzwerte enthalten, bevor potenziell CPU-intensive Arbeiten ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="37580-198">Blazor Server apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="37580-199">Arbeitsspeicher</span><span class="sxs-lookup"><span data-stu-id="37580-199">Memory</span></span>

<span data-ttu-id="37580-200">Speicherentwirrung kann auftreten, wenn ein oder mehrere Clients den Server zwingen, eine große Menge an Arbeitsspeicher zu verbrauchen.</span><span class="sxs-lookup"><span data-stu-id="37580-200">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="37580-201">Betrachten Sie beispielsweise eine Blazor-Server-Seite-App mit einer Komponente, die eine Liste von Elementen akzeptiert und anzeigt.</span><span class="sxs-lookup"><span data-stu-id="37580-201">For example, consider a Blazor-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="37580-202">Wenn die Blazor-App keine Beschränkungen für die Anzahl der zulässigen Elemente oder die Anzahl der Elemente setzt, die dem Client wiedergegeben werden, kann die speicherintensive Verarbeitung und das Rendering den Speicher des Servers so stark dominieren, dass die Leistung des Servers leidet.</span><span class="sxs-lookup"><span data-stu-id="37580-202">If the Blazor app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="37580-203">Der Server kann abstürzen oder so langsam sein, dass er abgestürzt zu sein scheint.</span><span class="sxs-lookup"><span data-stu-id="37580-203">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="37580-204">Betrachten Sie das folgende Szenario zum Verwalten und Anzeigen einer Liste von Elementen, die sich auf ein potenzielles Speichererschöpfungsszenario auf dem Server beziehen:</span><span class="sxs-lookup"><span data-stu-id="37580-204">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="37580-205">Die Elemente `List<MyItem>` in einer Eigenschaft oder einem Feld verwenden den Speicher des Servers.</span><span class="sxs-lookup"><span data-stu-id="37580-205">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="37580-206">Wenn die App zulässt, dass die Liste der Elemente unbegrenzt wächst, besteht die Gefahr, dass dem Server der Arbeitsspeicher ausgeht.</span><span class="sxs-lookup"><span data-stu-id="37580-206">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="37580-207">Wenn nicht mehr Arbeitsspeicher verfügbar ist, endet die aktuelle Sitzung (Absturz), und alle gleichzeitigen Sitzungen in dieser Serverinstanz erhalten eine Ausnahme ohne Arbeitsspeicher.</span><span class="sxs-lookup"><span data-stu-id="37580-207">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="37580-208">Um zu verhindern, dass dieses Szenario auftritt, muss die App eine Datenstruktur verwenden, die gleichzeitigen Benutzern ein Elementlimit auferlegt.</span><span class="sxs-lookup"><span data-stu-id="37580-208">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="37580-209">Wenn ein Pagingschema nicht zum Rendern verwendet wird, verwendet der Server zusätzlichen Speicher für Objekte, die in der Benutzeroberfläche nicht sichtbar sind.</span><span class="sxs-lookup"><span data-stu-id="37580-209">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="37580-210">Ohne eine Begrenzung der Anzahl der Elemente kann der Speicherbedarf den verfügbaren Serverspeicher ausschöpfen.</span><span class="sxs-lookup"><span data-stu-id="37580-210">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="37580-211">Um dieses Szenario zu verhindern, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="37580-211">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="37580-212">Verwenden Sie paginierte Listen beim Rendern.</span><span class="sxs-lookup"><span data-stu-id="37580-212">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="37580-213">Zeigen Sie nur die ersten 100 bis 1.000 Elemente an, und der Benutzer muss Suchkriterien eingeben, um Elemente jenseits der angezeigten Elemente zu finden.</span><span class="sxs-lookup"><span data-stu-id="37580-213">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="37580-214">Implementieren Sie für ein erweitertes Renderingszenario Listen oder Raster, die *Virtualisierung*unterstützen.</span><span class="sxs-lookup"><span data-stu-id="37580-214">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="37580-215">Mithilfe der Virtualisierung rendern Listen nur eine Teilmenge von Elementen, die derzeit für den Benutzer sichtbar sind.</span><span class="sxs-lookup"><span data-stu-id="37580-215">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="37580-216">Wenn der Benutzer mit der Bildlaufleiste in der Benutzeroberfläche interagiert, rendert die Komponente nur die Elemente, die für die Anzeige erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="37580-216">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="37580-217">Die Elemente, die derzeit nicht für die Anzeige benötigt werden, können im sekundären Speicher gespeichert werden, was der ideale Ansatz ist.</span><span class="sxs-lookup"><span data-stu-id="37580-217">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="37580-218">Nicht angezeigte Elemente können auch im Speicher gespeichert werden, was weniger ideal ist.</span><span class="sxs-lookup"><span data-stu-id="37580-218">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="37580-219">Blazor Server-Apps bieten ein ähnliches Programmiermodell wie andere UI-Frameworks für zustandsbehaftete Apps, z. B. WPF, Windows Forms oder Blazor WebAssembly.</span><span class="sxs-lookup"><span data-stu-id="37580-219">Blazor Server apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or Blazor WebAssembly.</span></span> <span data-ttu-id="37580-220">Der Hauptunterschied besteht darin, dass in mehreren UI-Frameworks der von der App verbrauchte Speicher dem Client gehört und sich nur auf diesen einzelnen Client auswirkt.</span><span class="sxs-lookup"><span data-stu-id="37580-220">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="37580-221">Beispielsweise wird eine Blazor WebAssembly-App vollständig auf dem Client ausgeführt und verwendet nur Clientspeicherressourcen.</span><span class="sxs-lookup"><span data-stu-id="37580-221">For example, a Blazor WebAssembly app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="37580-222">Im Blazor Server-Szenario gehört der von der App verbrauchte Speicher zum Server und wird von Clients auf der Serverinstanz gemeinsam genutzt.</span><span class="sxs-lookup"><span data-stu-id="37580-222">In the Blazor Server scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="37580-223">Serverseitige Speicheranforderungen sind eine Überlegung für alle Blazor Server-Apps.</span><span class="sxs-lookup"><span data-stu-id="37580-223">Server-side memory demands are a consideration for all Blazor Server apps.</span></span> <span data-ttu-id="37580-224">Die meisten Web-Apps sind jedoch zustandslos, und der Speicher, der während der Verarbeitung einer Anforderung verwendet wird, wird freigegeben, wenn die Antwort zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="37580-224">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="37580-225">Als allgemeine Empfehlung können Clients nicht zulassen, dass sie eine ungebundene Speichermenge zuweisen, wie in jeder anderen serverseitigen App, die Clientverbindungen beibehält.</span><span class="sxs-lookup"><span data-stu-id="37580-225">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="37580-226">Der von einer Blazor Server-App belegte Speicher bleibt länger als eine einzelne Anforderung erhalten.</span><span class="sxs-lookup"><span data-stu-id="37580-226">The memory consumed by a Blazor Server app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="37580-227">Während der Entwicklung kann ein Profiler verwendet oder eine Ablaufverfolgung erfasst werden, um speicheranforderungen von Clients zu bewerten.</span><span class="sxs-lookup"><span data-stu-id="37580-227">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="37580-228">Ein Profiler oder eine Ablaufverfolgung erfasst nicht den Speicher, der einem bestimmten Client zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="37580-228">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="37580-229">Um die Speichernutzung eines bestimmten Clients während der Entwicklung zu erfassen, erfassen Sie ein Dump, und untersuchen Sie den Speicherbedarf aller Objekte, die in der Schaltung eines Benutzers verwurzelt sind.</span><span class="sxs-lookup"><span data-stu-id="37580-229">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="37580-230">Clientverbindungen</span><span class="sxs-lookup"><span data-stu-id="37580-230">Client connections</span></span>

<span data-ttu-id="37580-231">Verbindungserlass kann auftreten, wenn ein oder mehrere Clients zu viele gleichzeitige Verbindungen zum Server öffnen, wodurch andere Clients daran gehindert werden, neue Verbindungen herzustellen.</span><span class="sxs-lookup"><span data-stu-id="37580-231">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="37580-232">Blazor-Clients stellen eine einzelne Verbindung pro Sitzung her und halten die Verbindung so lange geöffnet, wie das Browserfenster geöffnet ist.</span><span class="sxs-lookup"><span data-stu-id="37580-232">Blazor clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="37580-233">Die Anforderungen an den Server, alle Verbindungen zu verwalten, sind nicht spezifisch für Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="37580-233">The demands on the server of maintaining all of the connections isn't specific to Blazor apps.</span></span> <span data-ttu-id="37580-234">Angesichts der hartnäckigen Beständigkeit der Verbindungen und der zustandsbehafteten Natur von Blazor Server-Apps stellt die Erschöpfung der Verbindung ein größeres Risiko für die Verfügbarkeit der App dar.</span><span class="sxs-lookup"><span data-stu-id="37580-234">Given the persistent nature of the connections and the stateful nature of Blazor Server apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="37580-235">Standardmäßig gibt es keine Begrenzung für die Anzahl der Verbindungen pro Benutzer für eine Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="37580-235">By default, there's no limit on the number of connections per user for a Blazor Server app.</span></span> <span data-ttu-id="37580-236">Wenn die App ein Verbindungslimit erfordert, gehen Sie von einem oder mehreren der folgenden Ansätze aus:</span><span class="sxs-lookup"><span data-stu-id="37580-236">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="37580-237">Erfordern Sie eine Authentifizierung, was natürlich die Fähigkeit nicht autorisierter Benutzer einschränkt, eine Verbindung mit der App herzustellen.</span><span class="sxs-lookup"><span data-stu-id="37580-237">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="37580-238">Damit dieses Szenario wirksam ist, müssen Benutzer daran gehindert werden, neue Benutzer nach Belieben zur Bereitstellung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="37580-238">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="37580-239">Beschränken Sie die Anzahl der Verbindungen pro Benutzer.</span><span class="sxs-lookup"><span data-stu-id="37580-239">Limit the number of connections per user.</span></span> <span data-ttu-id="37580-240">Das Einschränken von Verbindungen kann über die folgenden Ansätze erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="37580-240">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="37580-241">Üben Sie Vorsicht, damit legitime Benutzer auf die App zugreifen können (z. B. wenn ein Verbindungslimit basierend auf der IP-Adresse des Clients festgelegt wird).</span><span class="sxs-lookup"><span data-stu-id="37580-241">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="37580-242">Auf App-Ebene:</span><span class="sxs-lookup"><span data-stu-id="37580-242">At the app level:</span></span>
    * <span data-ttu-id="37580-243">Erweiterbarkeit des Endpunktroutings.</span><span class="sxs-lookup"><span data-stu-id="37580-243">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="37580-244">Authentifizierung erforderlich, um eine Verbindung mit der App herzustellen und die aktiven Sitzungen pro Benutzer nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="37580-244">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="37580-245">Lehnen Sie neue Sitzungen ab, wenn Sie ein Limit erreichen.</span><span class="sxs-lookup"><span data-stu-id="37580-245">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="37580-246">Proxy WebSocket-Verbindungen mit einer App mithilfe eines Proxys, z. B. des [Azure SignalR-Dienstes,](/azure/azure-signalr/signalr-overview) der Verbindungen von Clients zu einer App beimultiplet.</span><span class="sxs-lookup"><span data-stu-id="37580-246">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="37580-247">Dadurch wird eine App mit einer größeren Verbindungskapazität bereitgestellt, als ein einzelner Client einrichten kann, wodurch verhindert wird, dass ein Client die Verbindungen zum Server erschöpft.</span><span class="sxs-lookup"><span data-stu-id="37580-247">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="37580-248">Auf Serverebene: Verwenden Sie einen Proxy/Gateway vor der App.</span><span class="sxs-lookup"><span data-stu-id="37580-248">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="37580-249">Azure [Front Door](/azure/frontdoor/front-door-overview) ermöglicht es Ihnen beispielsweise, das globale Routing von Webdatenverkehr zu einer App zu definieren, zu verwalten und zu überwachen.</span><span class="sxs-lookup"><span data-stu-id="37580-249">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="37580-250">DoS-Angriffe (Denial of Service)</span><span class="sxs-lookup"><span data-stu-id="37580-250">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="37580-251">DoS-Angriffe (Denial of Service) betreffen einen Client, der dazu führt, dass der Server eine oder mehrere seiner Ressourcen ausschöpft, sodass die App nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="37580-251">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="37580-252">Blazor Server-Apps enthalten einige Standardlimits und verlassen sich zum Schutz vor DoS-Angriffen auf andere ASP.NET Core- und SignalR-Grenzwerte:</span><span class="sxs-lookup"><span data-stu-id="37580-252">Blazor Server apps include some default limits and rely on other ASP.NET Core and SignalR limits to protect against DoS attacks:</span></span>

| <span data-ttu-id="37580-253">Blazor Server-App-Limit</span><span class="sxs-lookup"><span data-stu-id="37580-253">Blazor Server app limit</span></span>                            | <span data-ttu-id="37580-254">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="37580-254">Description</span></span> | <span data-ttu-id="37580-255">Standard</span><span class="sxs-lookup"><span data-stu-id="37580-255">Default</span></span> |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | <span data-ttu-id="37580-256">Maximale Anzahl getrennter Schaltungen, die ein bestimmter Server gleichzeitig im Arbeitsspeicher hält.</span><span class="sxs-lookup"><span data-stu-id="37580-256">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> | <span data-ttu-id="37580-257">100</span><span class="sxs-lookup"><span data-stu-id="37580-257">100</span></span> |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | <span data-ttu-id="37580-258">Maximale Zeitdauer, die eine getrennte Schaltung im Speicher hält, bevor sie abgerissen wird.</span><span class="sxs-lookup"><span data-stu-id="37580-258">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> | <span data-ttu-id="37580-259">3 Minuten</span><span class="sxs-lookup"><span data-stu-id="37580-259">3 minutes</span></span> |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | <span data-ttu-id="37580-260">Maximale Zeitdauer, die der Server wartet, bevor ein asynchroner JavaScript-Funktionsaufruf ausdembesteht.</span><span class="sxs-lookup"><span data-stu-id="37580-260">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> | <span data-ttu-id="37580-261">1 Minute</span><span class="sxs-lookup"><span data-stu-id="37580-261">1 minute</span></span> |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | <span data-ttu-id="37580-262">Maximale Anzahl nicht bestätigter Renderbatches, die der Server zu einem bestimmten Zeitpunkt im Arbeitsspeicher pro Schaltung aufbewahren, um eine robuste Wiederverbindung zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="37580-262">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="37580-263">Nach Erreichen des Limits stellt der Server die Produktion neuer Renderbatches ein, bis ein oder mehrere Batches vom Client bestätigt wurden.</span><span class="sxs-lookup"><span data-stu-id="37580-263">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> | <span data-ttu-id="37580-264">10</span><span class="sxs-lookup"><span data-stu-id="37580-264">10</span></span> |


| <span data-ttu-id="37580-265">SignalR und ASP.NET Core-Limit</span><span class="sxs-lookup"><span data-stu-id="37580-265">SignalR and ASP.NET Core limit</span></span>             | <span data-ttu-id="37580-266">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="37580-266">Description</span></span> | <span data-ttu-id="37580-267">Standard</span><span class="sxs-lookup"><span data-stu-id="37580-267">Default</span></span> |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | <span data-ttu-id="37580-268">Nachrichtengröße für eine einzelne Nachricht.</span><span class="sxs-lookup"><span data-stu-id="37580-268">Message size for an individual message.</span></span> | <span data-ttu-id="37580-269">32 KB</span><span class="sxs-lookup"><span data-stu-id="37580-269">32 KB</span></span> |

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="37580-270">Interaktionen mit dem Browser (Client)</span><span class="sxs-lookup"><span data-stu-id="37580-270">Interactions with the browser (client)</span></span>

<span data-ttu-id="37580-271">Ein Client interagiert mit dem Server über js interop-Ereignisdispatching und Renderabschluss.</span><span class="sxs-lookup"><span data-stu-id="37580-271">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="37580-272">Die JS-Interopkommunikation verläuft in beide Richtungen zwischen JavaScript und .NET:</span><span class="sxs-lookup"><span data-stu-id="37580-272">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="37580-273">Browserereignisse werden asynchron vom Client an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="37580-273">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="37580-274">Der Server reagiert bei Bedarf asynchron auf das erneute Rendern der Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="37580-274">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="37580-275">JavaScript-Funktionen, die von .NET aufgerufen werden</span><span class="sxs-lookup"><span data-stu-id="37580-275">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="37580-276">Für Aufrufe von .NET-Methoden an JavaScript:</span><span class="sxs-lookup"><span data-stu-id="37580-276">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="37580-277">Alle Aufrufe haben ein konfigurierbares Timeout, nach <xref:System.OperationCanceledException> dem sie fehlschlagen, und geben einen an den Aufrufer zurück.</span><span class="sxs-lookup"><span data-stu-id="37580-277">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="37580-278">Es gibt ein Standardtimeout für`CircuitOptions.JSInteropDefaultCallTimeout`die Anrufe ( ) von einer Minute.</span><span class="sxs-lookup"><span data-stu-id="37580-278">There's a default timeout for the calls (`CircuitOptions.JSInteropDefaultCallTimeout`) of one minute.</span></span> <span data-ttu-id="37580-279">Informationen zum Konfigurieren <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>dieses Limits finden Sie unter .</span><span class="sxs-lookup"><span data-stu-id="37580-279">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="37580-280">Ein Stornierungstoken kann bereitgestellt werden, um die Stornierung pro Anruf zu steuern.</span><span class="sxs-lookup"><span data-stu-id="37580-280">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="37580-281">Verlassen Sie sich auf das Standardaufruftimeout, wo möglich, und zeitgebundene Anrufe an den Client, wenn ein Abbruchtoken bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="37580-281">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="37580-282">Das Ergebnis eines JavaScript-Aufrufs kann nicht vertrauenswürdig sein.</span><span class="sxs-lookup"><span data-stu-id="37580-282">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="37580-283">Der Blazor im Browser ausgeführte App-Client sucht nach der javaScript-Funktion, die aufgerufen werden soll.</span><span class="sxs-lookup"><span data-stu-id="37580-283">The Blazor app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="37580-284">Die Funktion wird aufgerufen, und entweder wird das Ergebnis oder ein Fehler erzeugt.</span><span class="sxs-lookup"><span data-stu-id="37580-284">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="37580-285">Ein böswilliger Client kann versuchen:</span><span class="sxs-lookup"><span data-stu-id="37580-285">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="37580-286">Verursachen Sie ein Problem in der App, indem Sie einen Fehler von der JavaScript-Funktion zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="37580-286">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="37580-287">Induzieren Sie ein unbeabsichtigtes Verhalten auf dem Server, indem Sie ein unerwartetes Ergebnis von der JavaScript-Funktion zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="37580-287">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="37580-288">Ergreifen Sie die folgenden Vorsichtsmaßnahmen, um sich vor den vorhergehenden Szenarien zu schützen:</span><span class="sxs-lookup"><span data-stu-id="37580-288">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="37580-289">Umschließen sie JS-Interopaufrufe in [try-catch-Anweisungen,](/dotnet/csharp/language-reference/keywords/try-catch) um Fehler zu berücksichtigen, die während der Aufrufe auftreten können.</span><span class="sxs-lookup"><span data-stu-id="37580-289">Wrap JS interop calls within [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="37580-290">Weitere Informationen finden Sie unter <xref:blazor/handle-errors#javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="37580-290">For more information, see <xref:blazor/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="37580-291">Überprüfen Sie Daten, die von JS-Interop-Aufrufen zurückgegeben werden, einschließlich Fehlermeldungen, bevor Sie Maßnahmen ergreifen.</span><span class="sxs-lookup"><span data-stu-id="37580-291">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="37580-292">.NET-Methoden, die vom Browser aufgerufen werden</span><span class="sxs-lookup"><span data-stu-id="37580-292">.NET methods invoked from the browser</span></span>

<span data-ttu-id="37580-293">Vertrauen Sie Aufrufen von JavaScript-Methoden nicht auf .NET-Methoden.</span><span class="sxs-lookup"><span data-stu-id="37580-293">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="37580-294">Wenn eine .NET-Methode für JavaScript verfügbar gemacht wird, sollten Sie überlegen, wie die .NET-Methode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="37580-294">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="37580-295">Behandeln Sie jede .NET-Methode, die javaScript verfügbar gemacht wird, wie ein öffentlicher Endpunkt für die App.</span><span class="sxs-lookup"><span data-stu-id="37580-295">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="37580-296">Überprüfen Sie die Eingabe.</span><span class="sxs-lookup"><span data-stu-id="37580-296">Validate input.</span></span>
    * <span data-ttu-id="37580-297">Stellen Sie sicher, dass die Werte innerhalb der erwarteten Bereiche liegen.</span><span class="sxs-lookup"><span data-stu-id="37580-297">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="37580-298">Stellen Sie sicher, dass der Benutzer über die Berechtigung zum Ausführen der angeforderten Aktion verfügt.</span><span class="sxs-lookup"><span data-stu-id="37580-298">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="37580-299">Weisen Sie im Rahmen des .NET-Methodenaufrufs keine übermäßige Menge an Ressourcen zu.</span><span class="sxs-lookup"><span data-stu-id="37580-299">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="37580-300">Führen Sie z. B. Prüfungen durch und setzen Sie Grenzwerte für die CPU- und Speichernutzung.</span><span class="sxs-lookup"><span data-stu-id="37580-300">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="37580-301">Berücksichtigen Sie, dass statische methoden und Instanzmethoden für JavaScript-Clients verfügbar gemacht werden können.</span><span class="sxs-lookup"><span data-stu-id="37580-301">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="37580-302">Vermeiden Sie die gemeinsame Nutzung des Status über Sitzungen hinweg, es sei denn, der Entwurf fordert die freigabe des Status mit entsprechenden Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="37580-302">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="37580-303">Beispielsweise sollten Methoden, die durch `DotNetReference` Objekte verfügbar gemacht werden, die ursprünglich durch Abhängigkeitsinjektion (DI) erstellt wurden, als Bereichsobjekte registriert werden.</span><span class="sxs-lookup"><span data-stu-id="37580-303">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="37580-304">Dies gilt für alle Blazor DI-Dienste, die von der Server-App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="37580-304">This applies to any DI service that the Blazor Server app uses.</span></span>
    * <span data-ttu-id="37580-305">Vermeiden Sie bei statischen Methoden das Festlegen eines Status, der nicht auf den Client beschränkt werden kann, es sei denn, die App gibt den Status für alle Benutzer auf einer Serverinstanz explizit frei.</span><span class="sxs-lookup"><span data-stu-id="37580-305">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="37580-306">Vermeiden Sie die Weitergabe von vom Benutzer bereitgestellten Daten in Parametern an JavaScript-Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="37580-306">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="37580-307">Wenn die Weitergabe von Daten in Parameter unbedingt erforderlich ist, stellen Sie sicher, dass der JavaScript-Code die Weitergabe der Daten verarbeitet, ohne [XSS-Schwachstellen (Cross-Site Scripting)](#cross-site-scripting-xss) einzuführen.</span><span class="sxs-lookup"><span data-stu-id="37580-307">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="37580-308">Schreiben Sie z. B. keine vom Benutzer bereitgestellten Daten in `innerHTML` das Dokumentobjektmodell (DOM), indem Sie die Eigenschaft eines Elements festlegen.</span><span class="sxs-lookup"><span data-stu-id="37580-308">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="37580-309">Erwägen Sie die Verwendung von Content `eval` Security Policy [(CSP),](https://developer.mozilla.org/docs/Web/HTTP/CSP) um andere unsichere JavaScript-Primitive zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="37580-309">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="37580-310">Vermeiden Sie die Implementierung des benutzerdefinierten Dispatchings von .NET-Aufrufen zusätzlich zur Dispatching-Implementierung des Frameworks.</span><span class="sxs-lookup"><span data-stu-id="37580-310">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="37580-311">Das Verfügbarmachen von .NET-Methoden für den Browser Blazor ist ein erweitertes Szenario, das für die allgemeine Entwicklung nicht empfohlen wird.</span><span class="sxs-lookup"><span data-stu-id="37580-311">Exposing .NET methods to the browser is an advanced scenario, not recommended for general Blazor development.</span></span>

### <a name="events"></a><span data-ttu-id="37580-312">Ereignisse</span><span class="sxs-lookup"><span data-stu-id="37580-312">Events</span></span>

<span data-ttu-id="37580-313">Ereignisse stellen einen Einstiegspunkt für eine Blazor Server-App bereit.</span><span class="sxs-lookup"><span data-stu-id="37580-313">Events provide an entry point to a Blazor Server app.</span></span> <span data-ttu-id="37580-314">Die gleichen Regeln zum Schutz von Endpunkten in Blazor Web-Apps gelten für die Ereignisbehandlung in Server-Apps.</span><span class="sxs-lookup"><span data-stu-id="37580-314">The same rules for safeguarding endpoints in web apps apply to event handling in Blazor Server apps.</span></span> <span data-ttu-id="37580-315">Ein böswilliger Client kann alle Daten senden, die er als Nutzlast für ein Ereignis senden möchte.</span><span class="sxs-lookup"><span data-stu-id="37580-315">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="37580-316">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="37580-316">For example:</span></span>

* <span data-ttu-id="37580-317">Ein Änderungsereignis `<select>` für a kann einen Wert senden, der nicht innerhalb der Optionen liegt, die die App dem Client präsentiert hat.</span><span class="sxs-lookup"><span data-stu-id="37580-317">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="37580-318">An `<input>` kann beliebige Textdaten an den Server senden und dabei die clientseitige Validierung umgehen.</span><span class="sxs-lookup"><span data-stu-id="37580-318">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="37580-319">Die App muss die Daten für jedes Ereignis überprüfen, das die App verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="37580-319">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="37580-320">Die Blazor [Frameworkformskomponenten](xref:blazor/forms-validation) führen grundlegende Validierungen durch.</span><span class="sxs-lookup"><span data-stu-id="37580-320">The Blazor framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="37580-321">Wenn die App benutzerdefinierte Formularkomponenten verwendet, muss benutzerdefinierter Code geschrieben werden, um Ereignisdaten entsprechend zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="37580-321">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

Blazor<span data-ttu-id="37580-322">Serverereignisse sind asynchron, sodass mehrere Ereignisse an den Server gesendet werden können, bevor die App Zeit hat, durch die Erstellung eines neuen Renderings zu reagieren.</span><span class="sxs-lookup"><span data-stu-id="37580-322"> Server events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="37580-323">Dies hat einige Sicherheitsauswirkungen zu berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="37580-323">This has some security implications to consider.</span></span> <span data-ttu-id="37580-324">Das Einschränken von Clientaktionen in der App muss innerhalb von Ereignishandlern ausgeführt werden und hängt nicht vom aktuellen gerenderten Ansichtszustand ab.</span><span class="sxs-lookup"><span data-stu-id="37580-324">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="37580-325">Betrachten Sie eine Zählerkomponente, die es einem Benutzer ermöglichen soll, einen Leistungsindikator maximal dreimal zu erhöhen.</span><span class="sxs-lookup"><span data-stu-id="37580-325">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="37580-326">Die Schaltfläche zum Erhöhen des Zählers basiert `count`bedingt auf dem Wert von:</span><span class="sxs-lookup"><span data-stu-id="37580-326">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="37580-327">Ein Client kann ein oder mehrere Inkrementereignisse aussenden, bevor das Framework ein neues Rendering dieser Komponente erzeugt.</span><span class="sxs-lookup"><span data-stu-id="37580-327">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="37580-328">Das Ergebnis ist, dass die `count` vom Benutzer über das *Dreifache* erhöht werden kann, da die Schaltfläche von der Benutzeroberfläche nicht schnell genug entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="37580-328">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="37580-329">Der richtige Weg, um `count` die Grenze von drei Schritten zu erreichen, wird im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="37580-329">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="37580-330">Durch Hinzufügen `if (count < 3) { ... }` der Prüfung innerhalb des `count` Handlers basiert die Entscheidung zum Inkrementieren auf dem aktuellen App-Status.</span><span class="sxs-lookup"><span data-stu-id="37580-330">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="37580-331">Die Entscheidung basiert nicht wie im vorherigen Beispiel auf dem Status der Benutzeroberfläche, der vorübergehend veraltet sein könnte.</span><span class="sxs-lookup"><span data-stu-id="37580-331">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="37580-332">Schutz vor mehreren Dispatches</span><span class="sxs-lookup"><span data-stu-id="37580-332">Guard against multiple dispatches</span></span>

<span data-ttu-id="37580-333">Wenn ein Ereignisrückruf einen lang andauernden Vorgang asynchron aufruft, z. B. das Abrufen von Daten aus einem externen Dienst oder einer Datenbank, sollten Sie einen Guard verwenden.</span><span class="sxs-lookup"><span data-stu-id="37580-333">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="37580-334">Der Wächter kann verhindern, dass der Benutzer mehrere Vorgänge in die Warteschlange stellt, während der Vorgang mit visueller Rückmeldung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="37580-334">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="37580-335">Der folgende Komponentencode `true` `GetForecastAsync` wird festgelegt, `isLoading` während Daten vom Server abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="37580-335">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="37580-336">Während `isLoading` `true`dieSchaltfläche ist, ist sie in der Benutzeroberfläche deaktiviert:</span><span class="sxs-lookup"><span data-stu-id="37580-336">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="37580-337">Das im vorherigen Beispiel gezeigte Schutzmuster funktioniert, wenn der `async` - `await` Hintergrundvorgang asynchron mit dem Muster ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="37580-337">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="37580-338">Frühzeitig abbrechen und Verwendung nach Entsorgung vermeiden</span><span class="sxs-lookup"><span data-stu-id="37580-338">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="37580-339">Zusätzlich zur Verwendung eines Schutzes, wie im Abschnitt Schutz vor <xref:System.Threading.CancellationToken> mehreren [Dispatches](#guard-against-multiple-dispatches) beschrieben, sollten Sie eine verwenden, um lang andauernde Vorgänge abzubrechen, wenn die Komponente verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="37580-339">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="37580-340">Dieser Ansatz hat den zusätzlichen Vorteil, dass *die Verwendung nach der Entsorgung* in Komponenten vermieden wird:</span><span class="sxs-lookup"><span data-stu-id="37580-340">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="37580-341">Vermeiden Sie Ereignisse, die große Datenmengen erzeugen</span><span class="sxs-lookup"><span data-stu-id="37580-341">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="37580-342">Einige DOM-Ereignisse, `oninput` `onscroll`z. B. oder , können eine große Datenmenge erzeugen.</span><span class="sxs-lookup"><span data-stu-id="37580-342">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="37580-343">Vermeiden Sie die Blazor Verwendung dieser Ereignisse in Server-Apps.</span><span class="sxs-lookup"><span data-stu-id="37580-343">Avoid using these events in Blazor server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="37580-344">Zusätzliche Sicherheitshinweise</span><span class="sxs-lookup"><span data-stu-id="37580-344">Additional security guidance</span></span>

<span data-ttu-id="37580-345">Die Anleitung zum Sichern ASP.NET Blazor Core-Apps gelten für Server-Apps und werden in den folgenden Abschnitten behandelt:</span><span class="sxs-lookup"><span data-stu-id="37580-345">The guidance for securing ASP.NET Core apps apply to Blazor Server apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="37580-346">Protokollierung und sensible Daten</span><span class="sxs-lookup"><span data-stu-id="37580-346">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="37580-347">Schützen von Informationen während der Übertragung mit HTTPS</span><span class="sxs-lookup"><span data-stu-id="37580-347">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* <span data-ttu-id="37580-348">[Cross-Site Scripting (XSS)](#cross-site-scripting-xss))</span><span class="sxs-lookup"><span data-stu-id="37580-348">[Cross-site scripting (XSS)](#cross-site-scripting-xss))</span></span>
* [<span data-ttu-id="37580-349">Ursprungsübergreifender Schutz</span><span class="sxs-lookup"><span data-stu-id="37580-349">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="37580-350">Click-Jacking</span><span class="sxs-lookup"><span data-stu-id="37580-350">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="37580-351">Offene Umleitungen</span><span class="sxs-lookup"><span data-stu-id="37580-351">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="37580-352">Protokollierung und sensible Daten</span><span class="sxs-lookup"><span data-stu-id="37580-352">Logging and sensitive data</span></span>

<span data-ttu-id="37580-353">JS-Interop-Interaktionen zwischen Client und Server werden <xref:Microsoft.Extensions.Logging.ILogger> in den Protokollen des Servers mit Instanzen aufgezeichnet.</span><span class="sxs-lookup"><span data-stu-id="37580-353">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> Blazor<span data-ttu-id="37580-354">vermeidet die Protokollierung vertraulicher Informationen, z. B. tatsächliche Ereignisse oder JS-Interop-Ein- und -Ausgänge.</span><span class="sxs-lookup"><span data-stu-id="37580-354"> avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="37580-355">Wenn auf dem Server ein Fehler auftritt, benachrichtigt das Framework den Client und reißt die Sitzung ab.</span><span class="sxs-lookup"><span data-stu-id="37580-355">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="37580-356">Standardmäßig erhält der Client eine allgemeine Fehlermeldung, die in den Entwicklertools des Browsers angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="37580-356">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="37580-357">Der clientseitige Fehler enthält nicht die Aufrufliste und enthält keine Details zur Fehlerursache, aber Serverprotokolle enthalten solche Informationen.</span><span class="sxs-lookup"><span data-stu-id="37580-357">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="37580-358">Zu Entwicklungszwecken können dem Client vertrauliche Fehlerinformationen zur Verfügung gestellt werden, indem detaillierte Fehler aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="37580-358">For development purposes, sensitive error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="37580-359">Aktivieren Sie detaillierte Fehler mit:</span><span class="sxs-lookup"><span data-stu-id="37580-359">Enable detailed errors with:</span></span>

* <span data-ttu-id="37580-360">`CircuitOptions.DetailedErrors`.</span><span class="sxs-lookup"><span data-stu-id="37580-360">`CircuitOptions.DetailedErrors`.</span></span>
* <span data-ttu-id="37580-361">`DetailedErrors`Konfigurationsschlüssel.</span><span class="sxs-lookup"><span data-stu-id="37580-361">`DetailedErrors` configuration key.</span></span> <span data-ttu-id="37580-362">Legen Sie beispielsweise `ASPNETCORE_DETAILEDERRORS` die Umgebungsvariable `true`auf den Wert von fest.</span><span class="sxs-lookup"><span data-stu-id="37580-362">For example, set the `ASPNETCORE_DETAILEDERRORS` environment variable to a value of `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="37580-363">Das Aufstellen von Fehlerinformationen für Clients im Internet ist ein Sicherheitsrisiko, das immer vermieden werden sollte.</span><span class="sxs-lookup"><span data-stu-id="37580-363">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="37580-364">Schützen von Informationen während der Übertragung mit HTTPS</span><span class="sxs-lookup"><span data-stu-id="37580-364">Protect information in transit with HTTPS</span></span>

Blazor<span data-ttu-id="37580-365">Server SignalR verwendet für die Kommunikation zwischen dem Client und dem Server.</span><span class="sxs-lookup"><span data-stu-id="37580-365"> Server uses SignalR for communication between the client and the server.</span></span> Blazor<span data-ttu-id="37580-366">Server verwendet normalerweise SignalR den aushandelten Transport, der in der Regel WebSockets ist.</span><span class="sxs-lookup"><span data-stu-id="37580-366"> Server normally uses the transport that SignalR negotiates, which is typically WebSockets.</span></span>

Blazor<span data-ttu-id="37580-367">Server stellt die Integrität und Vertraulichkeit der zwischen dem Server und dem Client gesendeten Daten nicht sicher.</span><span class="sxs-lookup"><span data-stu-id="37580-367"> Server doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="37580-368">Verwenden Sie immer HTTPS.</span><span class="sxs-lookup"><span data-stu-id="37580-368">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="37580-369">Siteübergreifende Skripterstellung (XSS)</span><span class="sxs-lookup"><span data-stu-id="37580-369">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="37580-370">Cross-Site Scripting (XSS) ermöglicht es einer nicht autorisierten Partei, beliebige Logik im Kontext des Browsers auszuführen.</span><span class="sxs-lookup"><span data-stu-id="37580-370">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="37580-371">Eine kompromittierte App kann möglicherweise beliebigen Code auf dem Client ausführen.</span><span class="sxs-lookup"><span data-stu-id="37580-371">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="37580-372">Die Sicherheitsanfälligkeit kann verwendet werden, um möglicherweise eine Reihe bösartiger Aktionen gegen den Server auszuführen:</span><span class="sxs-lookup"><span data-stu-id="37580-372">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="37580-373">Senden Sie gefälschte/ungültige Ereignisse an den Server.</span><span class="sxs-lookup"><span data-stu-id="37580-373">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="37580-374">Dispatch fail/invalid render completions.</span><span class="sxs-lookup"><span data-stu-id="37580-374">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="37580-375">Vermeiden Sie das Dispatchen von Rendervervollständigungen.</span><span class="sxs-lookup"><span data-stu-id="37580-375">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="37580-376">Senden Von Interopaufrufen aus JavaScript an .NET.</span><span class="sxs-lookup"><span data-stu-id="37580-376">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="37580-377">Ändern Sie die Antwort von Interopaufrufen von .NET auf JavaScript.</span><span class="sxs-lookup"><span data-stu-id="37580-377">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="37580-378">Vermeiden Sie das Versenden von .NET an JS-Interop-Ergebnisse.</span><span class="sxs-lookup"><span data-stu-id="37580-378">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="37580-379">Das Blazor Serverframework ergreift Maßnahmen zum Schutz vor einigen der vorherigen Bedrohungen:</span><span class="sxs-lookup"><span data-stu-id="37580-379">The Blazor Server framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="37580-380">Beendet die Erstellung neuer UI-Aktualisierungen, wenn der Client keine Renderbatches bestätigt.</span><span class="sxs-lookup"><span data-stu-id="37580-380">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="37580-381">Konfiguriert mit `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.</span><span class="sxs-lookup"><span data-stu-id="37580-381">Configured with `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.</span></span>
* <span data-ttu-id="37580-382">Wenn Sie einen .NET-zu-JavaScript-Aufruf nach einer Minute durchführen, ohne eine Antwort vom Client zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="37580-382">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="37580-383">Konfiguriert mit `CircuitOptions.JSInteropDefaultCallTimeout`.</span><span class="sxs-lookup"><span data-stu-id="37580-383">Configured with `CircuitOptions.JSInteropDefaultCallTimeout`.</span></span>
* <span data-ttu-id="37580-384">Führt eine grundlegende Validierung aller Eingaben aus dem Browser während des JS-Interops durch:</span><span class="sxs-lookup"><span data-stu-id="37580-384">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="37580-385">.NET-Referenzen sind gültig und werden vom Typ erwartet, der von der .NET-Methode erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="37580-385">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="37580-386">Die Daten sind nicht falsch formatiert.</span><span class="sxs-lookup"><span data-stu-id="37580-386">The data isn't malformed.</span></span>
  * <span data-ttu-id="37580-387">Die richtige Anzahl von Argumenten für die Methode ist in der Nutzlast vorhanden.</span><span class="sxs-lookup"><span data-stu-id="37580-387">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="37580-388">Die Argumente oder das Ergebnis können korrekt deserialisiert werden, bevor die Methode aufruft.</span><span class="sxs-lookup"><span data-stu-id="37580-388">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="37580-389">Führt eine grundlegende Validierung aller Eingaben aus dem Browser aus ausgelösten Ereignissen durch:</span><span class="sxs-lookup"><span data-stu-id="37580-389">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="37580-390">Das Ereignis hat einen gültigen Typ.</span><span class="sxs-lookup"><span data-stu-id="37580-390">The event has a valid type.</span></span>
  * <span data-ttu-id="37580-391">Die Daten für das Ereignis können deserialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="37580-391">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="37580-392">Dem Ereignis ist ein Ereignishandler zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="37580-392">There's an event handler associated with the event.</span></span>

<span data-ttu-id="37580-393">Zusätzlich zu den Sicherheitsvorkehrungen, die das Framework implementiert, muss die App vom Entwickler codiert werden, um sich vor Bedrohungen zu schützen und geeignete Maßnahmen zu ergreifen:</span><span class="sxs-lookup"><span data-stu-id="37580-393">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="37580-394">Überprüfen Sie immer Daten, wenn Sie Ereignisse behandeln.</span><span class="sxs-lookup"><span data-stu-id="37580-394">Always validate data when handling events.</span></span>
* <span data-ttu-id="37580-395">Ergreifen Sie beim Empfang ungültiger Daten geeignete Maßnahmen:</span><span class="sxs-lookup"><span data-stu-id="37580-395">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="37580-396">Ignorieren Sie die Daten, und geben Sie zurück.</span><span class="sxs-lookup"><span data-stu-id="37580-396">Ignore the data and return.</span></span> <span data-ttu-id="37580-397">Dadurch kann die App die Verarbeitung von Anforderungen fortsetzen.</span><span class="sxs-lookup"><span data-stu-id="37580-397">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="37580-398">Wenn die App feststellt, dass die Eingabe illegitim ist und nicht vom legitimen Client erstellt werden kann, löst eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="37580-398">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="37580-399">Wenn Sie eine Ausnahme auslösen, wird die Schaltung heruntergeschraubt und die Sitzung beendet.</span><span class="sxs-lookup"><span data-stu-id="37580-399">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="37580-400">Vertrauen Sie der Fehlermeldung nicht, die von Renderbatchvervollständigungen bereitgestellt wird, die in den Protokollen enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="37580-400">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="37580-401">Der Fehler wird vom Client bereitgestellt und kann im Allgemeinen nicht als vertrauenswürdig eingestuft werden, da der Client möglicherweise kompromittiert ist.</span><span class="sxs-lookup"><span data-stu-id="37580-401">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="37580-402">Vertrauen Sie der Eingabe von JS-Interopaufrufen in beiden Richtungen zwischen JavaScript- und .NET-Methoden nicht.</span><span class="sxs-lookup"><span data-stu-id="37580-402">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="37580-403">Die App ist dafür verantwortlich, zu überprüfen, ob der Inhalt von Argumenten und Ergebnissen gültig ist, auch wenn die Argumente oder Ergebnisse korrekt deserialisiert sind.</span><span class="sxs-lookup"><span data-stu-id="37580-403">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="37580-404">Damit eine XSS-Schwachstelle vorhanden ist, muss die App Benutzereingaben in die gerenderte Seite integrieren.</span><span class="sxs-lookup"><span data-stu-id="37580-404">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> Blazor<span data-ttu-id="37580-405">Serverkomponenten führen einen Kompilierungsschritt aus, bei dem das Markup in einer *Razor-Datei* in eine prozedurale C-Logik umgewandelt wird.</span><span class="sxs-lookup"><span data-stu-id="37580-405"> Server components execute a compile-time step where the markup in a *.razor* file is transformed into procedural C# logic.</span></span> <span data-ttu-id="37580-406">Zur Laufzeit erstellt die C-Logik eine *Renderstruktur,* die die Elemente, den Text und die untergeordneten Komponenten beschreibt.</span><span class="sxs-lookup"><span data-stu-id="37580-406">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="37580-407">Dies wird über eine Folge von JavaScript-Anweisungen auf das DOM des Browsers angewendet (oder im Falle des Prerenderings in HTML serialisiert):</span><span class="sxs-lookup"><span data-stu-id="37580-407">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="37580-408">Benutzereingaben, die über die normale `@someStringValue`Razor-Syntax gerendert werden (z. B. ) machen keine XSS-Schwachstelle verfügbar, da die Razor-Syntax dem DOM über Befehle hinzugefügt wird, die nur Text schreiben können.</span><span class="sxs-lookup"><span data-stu-id="37580-408">User input rendered via normal Razor syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the Razor syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="37580-409">Auch wenn der Wert HTML-Markup enthält, wird der Wert als statischer Text angezeigt.</span><span class="sxs-lookup"><span data-stu-id="37580-409">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="37580-410">Beim Vorrendern ist die Ausgabe HTML-codiert, wodurch der Inhalt auch als statischer Text angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="37580-410">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="37580-411">Skript-Tags sind nicht zulässig und sollten nicht in der Komponenten-Renderstruktur der App enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="37580-411">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="37580-412">Wenn ein Skript-Tag im Markup einer Komponente enthalten ist, wird ein Kompilierungsfehler generiert.</span><span class="sxs-lookup"><span data-stu-id="37580-412">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="37580-413">Komponentenautoren können Komponenten in C-Code erstellen, ohne Razor zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="37580-413">Component authors can author components in C# without using Razor.</span></span> <span data-ttu-id="37580-414">Der Komponentenautor ist für die Verwendung der richtigen APIs beim Ausgeben der Ausgabe verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="37580-414">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="37580-415">Verwenden Sie `builder.AddContent(0, someUserSuppliedString)` z. B. und *nicht* `builder.AddMarkupContent(0, someUserSuppliedString)`, da letztere eine XSS-Schwachstelle verursachen könnte.</span><span class="sxs-lookup"><span data-stu-id="37580-415">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="37580-416">Als Teil des Schutzes vor XSS-Angriffen sollten Sie die Implementierung von XSS-Abschwächungen in Betracht ziehen, z. B. [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span><span class="sxs-lookup"><span data-stu-id="37580-416">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="37580-417">Weitere Informationen finden Sie unter <xref:security/cross-site-scripting>.</span><span class="sxs-lookup"><span data-stu-id="37580-417">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="37580-418">Ursprungsübergreifender Schutz</span><span class="sxs-lookup"><span data-stu-id="37580-418">Cross-origin protection</span></span>

<span data-ttu-id="37580-419">Ursprungsübergreifende Angriffe betreffen einen Client mit einem anderen Ursprung, der eine Aktion gegen den Server ausführt.</span><span class="sxs-lookup"><span data-stu-id="37580-419">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="37580-420">Die böswillige Aktion ist in der Regel eine GET-Anforderung oder ein Formular-POST (Cross-Site Request Forgery, CSRF), aber das Öffnen eines bösartigen WebSocket ist auch möglich.</span><span class="sxs-lookup"><span data-stu-id="37580-420">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> Blazor<span data-ttu-id="37580-421">Server-Apps bieten [die gleichen SignalR Garantien wie jede andere App, die das Hubprotokoll verwendet:](xref:signalr/security)</span><span class="sxs-lookup"><span data-stu-id="37580-421"> Server apps offer [the same guarantees that any other SignalR app using the hub protocol offer](xref:signalr/security):</span></span>

* Blazor<span data-ttu-id="37580-422">Auf Server-Apps kann ursprungsübergreifend zugegriffen werden, es sei denn, es werden zusätzliche Maßnahmen ergriffen, um dies zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="37580-422"> Server apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="37580-423">Um den Ursprungsübergreifenden Zugriff zu deaktivieren, deaktivieren Sie entweder CORS im Endpunkt, indem Sie die CORS-Middleware zur Pipeline hinzufügen und die `DisableCorsAttribute` zu den Blazor Endpunktmetadaten hinzufügen, oder den Satz zulässiger Ursprünge einschränken, indem Sie für die [ursprungsübergreifende Ressourcenfreigabe konfigurieren. SignalR ](xref:signalr/security#cross-origin-resource-sharing)</span><span class="sxs-lookup"><span data-stu-id="37580-423">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the `DisableCorsAttribute` to the Blazor endpoint metadata or limit the set of allowed origins by [configuring SignalR for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="37580-424">Wenn CORS aktiviert ist, sind möglicherweise zusätzliche Schritte erforderlich, um die App abhängig von der CORS-Konfiguration zu schützen.</span><span class="sxs-lookup"><span data-stu-id="37580-424">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="37580-425">Wenn CORS global aktiviert ist, kann CORS für Blazor den `DisableCorsAttribute` Serverhub deaktiviert werden, `hub.MapBlazorHub()`indem die Metadaten nach dem Aufruf zu den Endpunktmetadaten hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="37580-425">If CORS is globally enabled, CORS can be disabled for the Blazor Server hub by adding the `DisableCorsAttribute` metadata to the endpoint metadata after calling `hub.MapBlazorHub()`.</span></span>

<span data-ttu-id="37580-426">Weitere Informationen finden Sie unter <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="37580-426">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="37580-427">Click-Jacking</span><span class="sxs-lookup"><span data-stu-id="37580-427">Click-jacking</span></span>

<span data-ttu-id="37580-428">Click-Jacking beinhaltet das Rendern einer Website als `<iframe>` eine Website innerhalb einer Website von einem anderen Ursprung, um den Benutzer dazu zu bringen, Aktionen auf der angegriffenen Website auszuführen.</span><span class="sxs-lookup"><span data-stu-id="37580-428">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="37580-429">Um eine App vor dem `<iframe>`Rendern innerhalb einer zu schützen, verwenden Sie [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) und den `X-Frame-Options` Header.</span><span class="sxs-lookup"><span data-stu-id="37580-429">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="37580-430">Weitere Informationen finden Sie unter [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span><span class="sxs-lookup"><span data-stu-id="37580-430">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="37580-431">Offene Umleitungen</span><span class="sxs-lookup"><span data-stu-id="37580-431">Open redirects</span></span>

<span data-ttu-id="37580-432">Wenn Blazor eine Server-App-Sitzung gestartet wird, führt der Server eine grundlegende Überprüfung der URLs durch, die als Teil des Startens der Sitzung gesendet wurden.</span><span class="sxs-lookup"><span data-stu-id="37580-432">When a Blazor Server app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="37580-433">Das Framework überprüft, ob die Basis-URL ein übergeordnetes Element der aktuellen URL ist, bevor die Schaltung festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="37580-433">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="37580-434">Vom Framework werden keine zusätzlichen Prüfungen durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="37580-434">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="37580-435">Wenn ein Benutzer einen Link auf dem Client auswählt, wird die URL für den Link an den Server gesendet, der bestimmt, welche Aktion ausgeführt werden soll.</span><span class="sxs-lookup"><span data-stu-id="37580-435">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="37580-436">Die App kann z. B. eine clientseitige Navigation durchführen oder dem Browser angeben, zum neuen Speicherort zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="37580-436">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="37580-437">Komponenten können Navigationsanforderungen auch programmgesteuert über `NavigationManager`die Verwendung von auslösen.</span><span class="sxs-lookup"><span data-stu-id="37580-437">Components can also trigger navigation requests programatically through the use of `NavigationManager`.</span></span> <span data-ttu-id="37580-438">In solchen Szenarien führt die App möglicherweise eine clientseitige Navigation durch oder weist den Browser darauf hin, zum neuen Speicherort zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="37580-438">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="37580-439">Komponenten müssen:</span><span class="sxs-lookup"><span data-stu-id="37580-439">Components must:</span></span>

* <span data-ttu-id="37580-440">Vermeiden Sie die Verwendung von Benutzereingaben als Teil der Navigationsaufrufargumente.</span><span class="sxs-lookup"><span data-stu-id="37580-440">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="37580-441">Überprüfen Sie Argumente, um sicherzustellen, dass das Ziel von der App zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="37580-441">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="37580-442">Andernfalls kann ein böswilliger Benutzer den Browser zwingen, zu einer vom Angreifer kontrollierten Website zu wechseln.</span><span class="sxs-lookup"><span data-stu-id="37580-442">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="37580-443">In diesem Szenario versucht der Angreifer die App, einige Benutzereingaben `NavigationManager.Navigate` als Teil des Aufrufs der Methode zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="37580-443">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the `NavigationManager.Navigate` method.</span></span>

<span data-ttu-id="37580-444">Diese Empfehlung gilt auch beim Rendern von Links als Teil der App:</span><span class="sxs-lookup"><span data-stu-id="37580-444">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="37580-445">Verwenden Sie nach Möglichkeit relative Links.</span><span class="sxs-lookup"><span data-stu-id="37580-445">If possible, use relative links.</span></span>
* <span data-ttu-id="37580-446">Überprüfen Sie, ob absolute Verknüpfungsziele gültig sind, bevor Sie sie in eine Seite einfügen.</span><span class="sxs-lookup"><span data-stu-id="37580-446">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="37580-447">Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="37580-447">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="37580-448">Authentifizierung und Autorisierung</span><span class="sxs-lookup"><span data-stu-id="37580-448">Authentication and authorization</span></span>

<span data-ttu-id="37580-449">Hinweise zur Authentifizierung und <xref:security/blazor/index>Autorisierung finden Sie unter .</span><span class="sxs-lookup"><span data-stu-id="37580-449">For guidance on authentication and authorization, see <xref:security/blazor/index>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="37580-450">Checkliste für die Sicherheit</span><span class="sxs-lookup"><span data-stu-id="37580-450">Security checklist</span></span>

<span data-ttu-id="37580-451">Die folgende Liste von Sicherheitsüberlegungen ist nicht vollständig:</span><span class="sxs-lookup"><span data-stu-id="37580-451">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="37580-452">Überprüfen sie Argumente aus Ereignissen.</span><span class="sxs-lookup"><span data-stu-id="37580-452">Validate arguments from events.</span></span>
* <span data-ttu-id="37580-453">Überprüfen Sie Eingaben und Ergebnisse von JS-Interopaufrufen.</span><span class="sxs-lookup"><span data-stu-id="37580-453">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="37580-454">Vermeiden Sie die Verwendung (oder Validierung im Voraus) Benutzereingaben für .NET für JS-Interop-Aufrufe.</span><span class="sxs-lookup"><span data-stu-id="37580-454">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="37580-455">Verhindern Sie, dass der Client eine ungebundene Speichermenge zuweist.</span><span class="sxs-lookup"><span data-stu-id="37580-455">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="37580-456">Daten innerhalb der Komponente.</span><span class="sxs-lookup"><span data-stu-id="37580-456">Data within the component.</span></span>
  * <span data-ttu-id="37580-457">`DotNetObject`Verweise an den Client zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="37580-457">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="37580-458">Schutz vor mehreren Dispatches.</span><span class="sxs-lookup"><span data-stu-id="37580-458">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="37580-459">Abbrechen lang andauernder Vorgänge, wenn die Komponente freigegeben wird.</span><span class="sxs-lookup"><span data-stu-id="37580-459">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="37580-460">Vermeiden Sie Ereignisse, die große Datenmengen erzeugen.</span><span class="sxs-lookup"><span data-stu-id="37580-460">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="37580-461">Vermeiden Sie die Verwendung von `NavigationManager.Navigate` Benutzereingaben als Teil von Aufrufen an URLs und überprüfen Sie diese anhand einer Reihe zulässiger Ursprünge, wenn dies nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="37580-461">Avoid using user input as part of calls to `NavigationManager.Navigate` and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="37580-462">Treffen Sie Autorisierungsentscheidungen nicht basierend auf dem Status der Benutzeroberfläche, sondern nur aus dem Komponentenstatus.</span><span class="sxs-lookup"><span data-stu-id="37580-462">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="37580-463">Erwägen Sie die Verwendung von [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) zum Schutz vor XSS-Angriffen.</span><span class="sxs-lookup"><span data-stu-id="37580-463">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="37580-464">Erwägen Sie die Verwendung von CSP- und [X-Frame-Optionen](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) zum Schutz vor Klick-Jacking.</span><span class="sxs-lookup"><span data-stu-id="37580-464">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="37580-465">Stellen Sie sicher, dass die CORS-Einstellungen Blazor beim Aktivieren von CORS oder beim expliziten Deaktivieren von CORS für Apps geeignet sind.</span><span class="sxs-lookup"><span data-stu-id="37580-465">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for Blazor apps.</span></span>
* <span data-ttu-id="37580-466">Testen Sie, um sicherzustellen, dass Blazor die serverseitigen Grenzwerte für die App eine akzeptable Benutzererfahrung ohne inakzeptable Senkwerte bieten.</span><span class="sxs-lookup"><span data-stu-id="37580-466">Test to ensure that the server-side limits for the Blazor app provide an acceptable user experience without unacceptable levels of risk.</span></span>
