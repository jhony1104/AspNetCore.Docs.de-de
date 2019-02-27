---
title: Authentifizieren von Benutzern mit WS-Verbund in ASP.NET Core
author: chlowell
description: Dieses Tutorial veranschaulicht die Verwendung von WS-Verbund in einer ASP.NET Core-app.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: 7967410686da0e59742b721c0154e143bf19ba01
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833539"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="2c28c-103">Authentifizieren von Benutzern mit WS-Verbund in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2c28c-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="2c28c-104">Dieses Tutorial veranschaulicht, wie Benutzern die Anmeldung von eines WS-Verbund-Authentifizierungsanbieter wie Active Directory Federation Services (ADFS) oder [Azure Active Directory](/azure/active-directory/) (AAD).</span><span class="sxs-lookup"><span data-stu-id="2c28c-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="2c28c-105">Er verwendet die ASP.NET Core 2.0-beispielanwendung, die in beschriebenen [Facebook, Google, und die Authentifizierung des externen Anbieter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="2c28c-105">It uses the ASP.NET Core 2.0 sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="2c28c-106">Für apps mit ASP.NET Core 2.0, WS-Verbund-Unterstützung bereitgestellt [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span><span class="sxs-lookup"><span data-stu-id="2c28c-106">For ASP.NET Core 2.0 apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="2c28c-107">Diese Komponente wird von portiert [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) und nutzt viele Mechanismen für die Komponente.</span><span class="sxs-lookup"><span data-stu-id="2c28c-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="2c28c-108">Allerdings unterscheiden sich die Komponenten, in einigen wichtigen Aspekten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="2c28c-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="2c28c-109">Standardmäßig ist die neue Middleware:</span><span class="sxs-lookup"><span data-stu-id="2c28c-109">By default, the new middleware:</span></span>

* <span data-ttu-id="2c28c-110">Lässt keine unerwünschten Anmeldungen zu.</span><span class="sxs-lookup"><span data-stu-id="2c28c-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="2c28c-111">Dieses Feature von der WS-Verbund-Protokoll ist anfällig für XSRF-Angriffen.</span><span class="sxs-lookup"><span data-stu-id="2c28c-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="2c28c-112">Es kann jedoch aktiviert werden, mit der `AllowUnsolicitedLogins` Option.</span><span class="sxs-lookup"><span data-stu-id="2c28c-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="2c28c-113">Jedes Formular-Post-Anmeldung Nachrichten nicht überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="2c28c-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="2c28c-114">Nur Anforderungen an die `CallbackPath` SSO-Ins überprüft werden `CallbackPath` standardmäßig `/signin-wsfed` kann jedoch geändert werden über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [ WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) Klasse.</span><span class="sxs-lookup"><span data-stu-id="2c28c-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="2c28c-115">Dieser Pfad kann mit anderen Authentifizierungsanbieter erforderlich freigegeben werden, durch Aktivieren der [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) Option.</span><span class="sxs-lookup"><span data-stu-id="2c28c-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="2c28c-116">Registrieren der app mit Active Directory</span><span class="sxs-lookup"><span data-stu-id="2c28c-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="2c28c-117">Active Directory-Verbunddienste</span><span class="sxs-lookup"><span data-stu-id="2c28c-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="2c28c-118">Öffnen Sie des Serverzertifikat **Relying Party Trust Assistenten zum Hinzufügen von** über die AD FS-Verwaltungskonsole:</span><span class="sxs-lookup"><span data-stu-id="2c28c-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Relying Party Trust-Assistent zum Hinzufügen von: Willkommen](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="2c28c-120">Wählen Sie auf die Daten manuell eingeben:</span><span class="sxs-lookup"><span data-stu-id="2c28c-120">Choose to enter data manually:</span></span>

![Relying Party Trust-Assistent zum Hinzufügen von: Auswählen einer Datenquelle](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="2c28c-122">Geben Sie einen Anzeigenamen für die vertrauende Seite ein.</span><span class="sxs-lookup"><span data-stu-id="2c28c-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="2c28c-123">Der Name nicht wichtig, die ASP.NET Core-app.</span><span class="sxs-lookup"><span data-stu-id="2c28c-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="2c28c-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) fehlt die Unterstützung für die Verschlüsselung von token, damit Sie ein tokenverschlüsselungszertifikat nicht konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="2c28c-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Relying Party Trust-Assistent zum Hinzufügen von: Zertifikat konfigurieren](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="2c28c-126">Aktivieren der Unterstützung für WS-Verbund Passive Protocol, verwenden die URL der app.</span><span class="sxs-lookup"><span data-stu-id="2c28c-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="2c28c-127">Stellen Sie sicher, dass der Port für die app richtig ist:</span><span class="sxs-lookup"><span data-stu-id="2c28c-127">Verify the port is correct for the app:</span></span>

![Relying Party Trust-Assistent zum Hinzufügen von: URL konfigurieren](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="2c28c-129">Dies muss eine HTTPS-URL sein.</span><span class="sxs-lookup"><span data-stu-id="2c28c-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="2c28c-130">IIS Express bietet ein selbst signiertes Zertifikat, wenn die app während der Entwicklung zu hosten.</span><span class="sxs-lookup"><span data-stu-id="2c28c-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="2c28c-131">Kestrel ist die manuelle Zertifikatkonfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="2c28c-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="2c28c-132">Finden Sie unter den [Kestrel Dokumentation](xref:fundamentals/servers/kestrel) Weitere Details.</span><span class="sxs-lookup"><span data-stu-id="2c28c-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="2c28c-133">Klicken Sie auf **Weiter** über den Assistenten und **schließen** am Ende.</span><span class="sxs-lookup"><span data-stu-id="2c28c-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="2c28c-134">ASP.NET Core Identity verlangt, dass eine **namens-ID** Anspruch.</span><span class="sxs-lookup"><span data-stu-id="2c28c-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="2c28c-135">Fügen Sie eine aus der **Edit Claim Rules** Dialogfeld:</span><span class="sxs-lookup"><span data-stu-id="2c28c-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Anspruchsregeln bearbeiten](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="2c28c-137">In der **transformieren Anspruch Assistenten zum Hinzufügen von**, übernehmen Sie den Standardwert **LDAP-Attributen als Ansprüche senden** Vorlage ausgewählt, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="2c28c-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="2c28c-138">Fügen Sie eine Zuordnung für die Regel der **SAM-Kontonamen** LDAP-Attribut, um die **namens-ID** ausgehenden Anspruch:</span><span class="sxs-lookup"><span data-stu-id="2c28c-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Fügen Sie die Assistenten zum Transformieren hinzu: Anspruchsregel konfigurieren](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="2c28c-140">Klicken Sie auf **Fertig stellen** > **OK** in die **Edit Claim Rules** Fenster.</span><span class="sxs-lookup"><span data-stu-id="2c28c-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="2c28c-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="2c28c-141">Azure Active Directory</span></span>

* <span data-ttu-id="2c28c-142">Navigieren Sie zum Blatt "des AAD-Mandanten-app-Registrierungen".</span><span class="sxs-lookup"><span data-stu-id="2c28c-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="2c28c-143">Klicken Sie auf **Registrierung einer neuen Anwendung**:</span><span class="sxs-lookup"><span data-stu-id="2c28c-143">Click **New application registration**:</span></span>

![Azure Active Directory: App-Registrierungen](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="2c28c-145">Geben Sie einen Namen für die app-Registrierung.</span><span class="sxs-lookup"><span data-stu-id="2c28c-145">Enter a name for the app registration.</span></span> <span data-ttu-id="2c28c-146">Dies ist wichtig, die ASP.NET Core-app nicht.</span><span class="sxs-lookup"><span data-stu-id="2c28c-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="2c28c-147">Geben Sie die URL, die die app, wie lauscht die **Anmelde-URL**:</span><span class="sxs-lookup"><span data-stu-id="2c28c-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: Erstellen von app-Registrierung](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="2c28c-149">Klicken Sie auf **Endpunkte** und notieren Sie sich die **Verbundmetadaten-Dokument** URL.</span><span class="sxs-lookup"><span data-stu-id="2c28c-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="2c28c-150">Dies ist der WS-Federation Middleware `MetadataAddress`:</span><span class="sxs-lookup"><span data-stu-id="2c28c-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: Endpunkte](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="2c28c-152">Navigieren Sie zu der neuen app-Registrierung.</span><span class="sxs-lookup"><span data-stu-id="2c28c-152">Navigate to the new app registration.</span></span> <span data-ttu-id="2c28c-153">Klicken Sie auf **Einstellungen** > **Eigenschaften** und notieren Sie sich die **App ID-URI**.</span><span class="sxs-lookup"><span data-stu-id="2c28c-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="2c28c-154">Dies ist der WS-Federation Middleware `Wtrealm`:</span><span class="sxs-lookup"><span data-stu-id="2c28c-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: Eigenschaften der App-Registrierung](ws-federation/_static/AadAppIdUri.png)

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="2c28c-156">Hinzufügen von WS-Verbund als einem externen Anmeldeanbieter für ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="2c28c-156">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="2c28c-157">Fügen Sie eine Abhängigkeit auf [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) zum Projekt.</span><span class="sxs-lookup"><span data-stu-id="2c28c-157">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="2c28c-158">Hinzufügen von WS-Verbund, `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2c28c-158">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

    ```csharp
    services.AddIdentity<IdentityUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddAuthentication()
        .AddWsFederation(options =>
        {
            // MetadataAddress represents the Active Directory instance used to authenticate users.
            options.MetadataAddress = "https://<ADFS FQDN or AAD tenant>/FederationMetadata/2007-06/FederationMetadata.xml";

            // Wtrealm is the app's identifier in the Active Directory instance.
            // For ADFS, use the relying party's identifier, its WS-Federation Passive protocol URL:
            options.Wtrealm = "https://localhost:44307/";

            // For AAD, use the App ID URI from the app registration's Properties blade:
            options.Wtrealm = "https://wsfedsample.onmicrosoft.com/bf0e7e6d-056e-4e37-b9a6-2c36797b9f01";
        });

    services.AddMvc()
     // ...
    ```

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="2c28c-159">Melden Sie sich mit WS-Verbund</span><span class="sxs-lookup"><span data-stu-id="2c28c-159">Log in with WS-Federation</span></span>

<span data-ttu-id="2c28c-160">Navigieren Sie zur app, und klicken Sie auf die **melden Sie sich bei** -Link in der Nav-Header.</span><span class="sxs-lookup"><span data-stu-id="2c28c-160">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="2c28c-161">Es ist eine Option aus, um die Anmeldung mit WsFederation: ![Anmeldeseite](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="2c28c-161">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="2c28c-162">Mit ADFS als Anbieter leitet die Schaltfläche mit der an einen AD FS-Anmeldeseite ein: ![AD FS-Anmeldeseite](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="2c28c-162">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="2c28c-163">Mit Azure Active Directory als Anbieter leitet die Schaltfläche mit der an eine AAD-Anmeldeseite ein: ![AAD-Anmeldeseite](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="2c28c-163">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="2c28c-164">Eine erfolgreiche Anmeldung für einen neuen Benutzer leitet an der app Benutzer-Registrierungsseite: ![Seite "registrieren"](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="2c28c-164">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="2c28c-165">Verwenden von WS-Verbund, ohne ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="2c28c-165">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="2c28c-166">Die WS-Verbund-Middleware kann ohne Identity verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2c28c-166">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="2c28c-167">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2c28c-167">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(sharedOptions =>
    {
        sharedOptions.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultSignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
        sharedOptions.DefaultChallengeScheme = WsFederationDefaults.AuthenticationScheme;
    })
    .AddWsFederation(options =>
    {
        options.Wtrealm = Configuration["wsfed:realm"];
        options.MetadataAddress = Configuration["wsfed:metadata"];
    })
    .AddCookie();
}

public void Configure(IApplicationBuilder app)
{
    app.UseAuthentication();
        // …
}
```
