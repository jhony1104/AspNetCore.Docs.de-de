---
title: Authentifizieren von Benutzern mit dem WS-Verbund in ASP.net Core
author: chlowell
description: In diesem Tutorial wird veranschaulicht, wie Sie WS-Federation in einer ASP.net Core-App verwenden.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: d82421a14ede6cb6b01ef59f233bb2eba6b56aec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651331"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a><span data-ttu-id="660be-103">Authentifizieren von Benutzern mit dem WS-Verbund in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="660be-103">Authenticate users with WS-Federation in ASP.NET Core</span></span>

<span data-ttu-id="660be-104">In diesem Tutorial wird veranschaulicht, wie Sie es Benutzern ermöglichen, sich mit einem WS-Verbund-Authentifizierungs Anbieter wie Active Directory-Verbunddienste (AD FS) (ADFS) oder [Azure Active Directory](/azure/active-directory/) (AAD) anzumelden.</span><span class="sxs-lookup"><span data-stu-id="660be-104">This tutorial demonstrates how to enable users to sign in with a WS-Federation authentication provider like Active Directory Federation Services (ADFS) or [Azure Active Directory](/azure/active-directory/) (AAD).</span></span> <span data-ttu-id="660be-105">Dabei wird die in [Facebook, Google und der Authentifizierung externer Anbieter](xref:security/authentication/social/index)beschriebene ASP.net Core 2,0-Beispiel-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="660be-105">It uses the ASP.NET Core 2.0 sample app described in [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="660be-106">Bei ASP.net Core 2,0-apps wird die WS-Verbund Unterstützung von [Microsoft. aspnetcore. Authentication. wsfederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="660be-106">For ASP.NET Core 2.0 apps, WS-Federation support is provided by [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation).</span></span> <span data-ttu-id="660be-107">Diese Komponente wird von [Microsoft. owin. Security. wsfederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) portiert und nutzt viele der Mechanismen dieser Komponente.</span><span class="sxs-lookup"><span data-stu-id="660be-107">This component is ported from [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) and shares many of that component's mechanics.</span></span> <span data-ttu-id="660be-108">Allerdings unterscheiden sich die Komponenten auf einige wichtige Weise.</span><span class="sxs-lookup"><span data-stu-id="660be-108">However, the components differ in a couple of important ways.</span></span>

<span data-ttu-id="660be-109">Standardmäßig wird die neue Middleware:</span><span class="sxs-lookup"><span data-stu-id="660be-109">By default, the new middleware:</span></span>

* <span data-ttu-id="660be-110">Lässt keine nicht angeforderten Anmeldungen zu.</span><span class="sxs-lookup"><span data-stu-id="660be-110">Doesn't allow unsolicited logins.</span></span> <span data-ttu-id="660be-111">Diese Funktion des WS-Verbund Protokolls ist anfällig für XSRF-Angriffe.</span><span class="sxs-lookup"><span data-stu-id="660be-111">This feature of the WS-Federation protocol is vulnerable to XSRF attacks.</span></span> <span data-ttu-id="660be-112">Sie kann jedoch mit der Option `AllowUnsolicitedLogins` aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="660be-112">However, it can be enabled with the `AllowUnsolicitedLogins` option.</span></span>
* <span data-ttu-id="660be-113">Prüft nicht alle Formular Beiträge auf Anmelde Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="660be-113">Doesn't check every form post for sign-in messages.</span></span> <span data-ttu-id="660be-114">Nur Anforderungen an die `CallbackPath` werden auf Anmeldungen geprüft. `CallbackPath` ist standardmäßig `/signin-wsfed`, kann aber über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [wsfederationoptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) -Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="660be-114">Only requests to the `CallbackPath` are checked for sign-ins. `CallbackPath` defaults to `/signin-wsfed` but can be changed via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) class.</span></span> <span data-ttu-id="660be-115">Dieser Pfad kann für andere Authentifizierungs Anbieter freigegeben werden, indem die [skipunerkenzedrequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) -Option aktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="660be-115">This path can be shared with other authentication providers by enabling the [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) option.</span></span>

## <a name="register-the-app-with-active-directory"></a><span data-ttu-id="660be-116">Registrieren der App bei Active Directory</span><span class="sxs-lookup"><span data-stu-id="660be-116">Register the app with Active Directory</span></span>

### <a name="active-directory-federation-services"></a><span data-ttu-id="660be-117">Active Directory-Verbunddienste (AD FS)</span><span class="sxs-lookup"><span data-stu-id="660be-117">Active Directory Federation Services</span></span>

* <span data-ttu-id="660be-118">Öffnen **Sie den Assistenten zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite** in der AD FS-Verwaltungskonsole:</span><span class="sxs-lookup"><span data-stu-id="660be-118">Open the server's **Add Relying Party Trust Wizard** from the ADFS Management console:</span></span>

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite](ws-federation/_static/AdfsAddTrust.png)

* <span data-ttu-id="660be-120">Daten manuell eingeben:</span><span class="sxs-lookup"><span data-stu-id="660be-120">Choose to enter data manually:</span></span>

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite: Datenquelle](ws-federation/_static/AdfsSelectDataSource.png)

* <span data-ttu-id="660be-122">Geben Sie einen anzeigen Amen für die vertrauende Seite ein.</span><span class="sxs-lookup"><span data-stu-id="660be-122">Enter a display name for the relying party.</span></span> <span data-ttu-id="660be-123">Der Name ist für die ASP.net Core-APP nicht wichtig.</span><span class="sxs-lookup"><span data-stu-id="660be-123">The name isn't important to the ASP.NET Core app.</span></span>

* <span data-ttu-id="660be-124">[Microsoft. aspnetcore. Authentication. wsfederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) hat keine Unterstützung für die tokenverschlüsselung, konfigurieren Sie also kein tokenverschlüsselungszertifikat:</span><span class="sxs-lookup"><span data-stu-id="660be-124">[Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) lacks support for token encryption, so don't configure a token encryption certificate:</span></span>

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite](ws-federation/_static/AdfsConfigureCert.png)

* <span data-ttu-id="660be-126">Aktivieren Sie die Unterstützung für das passive WS-Verbund Protokoll mithilfe der App-URL.</span><span class="sxs-lookup"><span data-stu-id="660be-126">Enable support for WS-Federation Passive protocol, using the app's URL.</span></span> <span data-ttu-id="660be-127">Vergewissern Sie sich, dass der Port für die APP richtig ist:</span><span class="sxs-lookup"><span data-stu-id="660be-127">Verify the port is correct for the app:</span></span>

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> <span data-ttu-id="660be-129">Dabei muss es sich um eine HTTPS-URL handeln.</span><span class="sxs-lookup"><span data-stu-id="660be-129">This must be an HTTPS URL.</span></span> <span data-ttu-id="660be-130">IIS Express können ein selbst signiertes Zertifikat bereitstellen, wenn die APP während der Entwicklung gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="660be-130">IIS Express can provide a self-signed certificate when hosting the app during development.</span></span> <span data-ttu-id="660be-131">Kestrel erfordert eine manuelle Zertifikat Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="660be-131">Kestrel requires manual certificate configuration.</span></span> <span data-ttu-id="660be-132">Weitere Informationen finden Sie in der [Kestrel-Dokumentation](xref:fundamentals/servers/kestrel) .</span><span class="sxs-lookup"><span data-stu-id="660be-132">See the [Kestrel documentation](xref:fundamentals/servers/kestrel) for more details.</span></span>

* <span data-ttu-id="660be-133">Klicken Sie im restlichen Assistenten auf **weiter** , und **Schließen** Sie am Ende.</span><span class="sxs-lookup"><span data-stu-id="660be-133">Click **Next** through the rest of the wizard and **Close** at the end.</span></span>

* <span data-ttu-id="660be-134">Für ASP.net Core Identität ist ein **Name-ID** -Anspruch erforderlich.</span><span class="sxs-lookup"><span data-stu-id="660be-134">ASP.NET Core Identity requires a **Name ID** claim.</span></span> <span data-ttu-id="660be-135">Fügen Sie eine aus dem Dialogfeld " **Anspruchs Regeln bearbeiten** " hinzu:</span><span class="sxs-lookup"><span data-stu-id="660be-135">Add one from the **Edit Claim Rules** dialog:</span></span>

![Bearbeiten von Anspruchsregeln](ws-federation/_static/EditClaimRules.png)

* <span data-ttu-id="660be-137">Belassen Sie im **Assistenten zum Hinzufügen von Transformations Anspruchs Regeln**die Vorlage Standardwert **für LDAP-Attribute als Ansprüche senden** , und klicken Sie auf **weiter**.</span><span class="sxs-lookup"><span data-stu-id="660be-137">In the **Add Transform Claim Rule Wizard**, leave the default **Send LDAP Attributes as Claims** template selected, and click **Next**.</span></span> <span data-ttu-id="660be-138">Fügen Sie eine Regel hinzu, die das LDAP **-Attribut Sam-Account-Name** dem ausgehenden Anspruch der **namens-ID** zuordnet:</span><span class="sxs-lookup"><span data-stu-id="660be-138">Add a rule mapping the **SAM-Account-Name** LDAP attribute to the **Name ID** outgoing claim:</span></span>

![Assistent zum Hinzufügen von Transformations Anspruchs Regeln: Konfigurieren von Anspruchs Regeln](ws-federation/_static/AddTransformClaimRule.png)

* <span data-ttu-id="660be-140">Klicken Sie im Fenster **Anspruchs Regeln bearbeiten** auf **Fertig** stellen > **OK** .</span><span class="sxs-lookup"><span data-stu-id="660be-140">Click **Finish** > **OK** in the **Edit Claim Rules** window.</span></span>

### <a name="azure-active-directory"></a><span data-ttu-id="660be-141">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="660be-141">Azure Active Directory</span></span>

* <span data-ttu-id="660be-142">Navigieren Sie zum Blatt App-Registrierungen des Aad-Mandanten.</span><span class="sxs-lookup"><span data-stu-id="660be-142">Navigate to the AAD tenant's app registrations blade.</span></span> <span data-ttu-id="660be-143">Klicken Sie auf **neue Anwendungs Registrierung**:</span><span class="sxs-lookup"><span data-stu-id="660be-143">Click **New application registration**:</span></span>

![Azure Active Directory: App-Registrierungen](ws-federation/_static/AadNewAppRegistration.png)

* <span data-ttu-id="660be-145">Geben Sie einen Namen für die APP-Registrierung ein.</span><span class="sxs-lookup"><span data-stu-id="660be-145">Enter a name for the app registration.</span></span> <span data-ttu-id="660be-146">Dies ist für die ASP.net Core-APP nicht von Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="660be-146">This isn't important to the ASP.NET Core app.</span></span>
* <span data-ttu-id="660be-147">Geben Sie die URL ein, die die APP als **Anmelde-URL**überwacht:</span><span class="sxs-lookup"><span data-stu-id="660be-147">Enter the URL the app listens on as the **Sign-on URL**:</span></span>

![Azure Active Directory: App-Registrierung erstellen](ws-federation/_static/AadCreateAppRegistration.png)

* <span data-ttu-id="660be-149">Klicken Sie auf **Endpunkte** , und notieren Sie die Verbund **Metadaten-Dokument** -URL</span><span class="sxs-lookup"><span data-stu-id="660be-149">Click **Endpoints** and note the **Federation Metadata Document** URL.</span></span> <span data-ttu-id="660be-150">Dies sind die `MetadataAddress`der WS-Verbund Middleware:</span><span class="sxs-lookup"><span data-stu-id="660be-150">This is the WS-Federation middleware's `MetadataAddress`:</span></span>

![Azure Active Directory: Endpunkte](ws-federation/_static/AadFederationMetadataDocument.png)

* <span data-ttu-id="660be-152">Navigieren Sie zur neuen App-Registrierung.</span><span class="sxs-lookup"><span data-stu-id="660be-152">Navigate to the new app registration.</span></span> <span data-ttu-id="660be-153">Klicken Sie auf **Einstellungen** > **Eigenschaften** , und notieren Sie sich den **App-ID-URI**.</span><span class="sxs-lookup"><span data-stu-id="660be-153">Click **Settings** > **Properties** and make note of the **App ID URI**.</span></span> <span data-ttu-id="660be-154">Dies sind die `Wtrealm`der WS-Verbund Middleware:</span><span class="sxs-lookup"><span data-stu-id="660be-154">This is the WS-Federation middleware's `Wtrealm`:</span></span>

![Azure Active Directory: Eigenschaften der APP-Registrierung](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a><span data-ttu-id="660be-156">Verwenden von WS-Federation ohne ASP.net Core Identität</span><span class="sxs-lookup"><span data-stu-id="660be-156">Use WS-Federation without ASP.NET Core Identity</span></span>

<span data-ttu-id="660be-157">Die WS-Verbund-Middleware kann ohne Identität verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="660be-157">The WS-Federation middleware can be used without Identity.</span></span> <span data-ttu-id="660be-158">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="660be-158">For example:</span></span>
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a><span data-ttu-id="660be-159">WS-Verbund als externen Anmelde Anbieter für ASP.net Core Identität hinzufügen</span><span class="sxs-lookup"><span data-stu-id="660be-159">Add WS-Federation as an external login provider for ASP.NET Core Identity</span></span>

* <span data-ttu-id="660be-160">Fügen Sie eine Abhängigkeit von [Microsoft. aspnetcore. Authentication. wsfederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) zum Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="660be-160">Add a dependency on [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) to the project.</span></span>
* <span data-ttu-id="660be-161">Fügen Sie `Startup.ConfigureServices`WS-Verbund hinzu:</span><span class="sxs-lookup"><span data-stu-id="660be-161">Add WS-Federation to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a><span data-ttu-id="660be-162">Anmelden mit dem WS-Verbund</span><span class="sxs-lookup"><span data-stu-id="660be-162">Log in with WS-Federation</span></span>

<span data-ttu-id="660be-163">Navigieren Sie zur APP, und klicken Sie auf den Link **Anmelden** im Navigations Header.</span><span class="sxs-lookup"><span data-stu-id="660be-163">Browse to the app and click the **Log in** link in the nav header.</span></span> <span data-ttu-id="660be-164">Es gibt eine Option zum Anmelden mit wsfederation: ![Anmeldeseite](ws-federation/_static/WsFederationButton.png)</span><span class="sxs-lookup"><span data-stu-id="660be-164">There's an option to log in with WsFederation: ![Log in page](ws-federation/_static/WsFederationButton.png)</span></span>

<span data-ttu-id="660be-165">Bei ADFS als Anbieter wird die Schaltfläche zu einer AD FS-Anmeldeseite umgeleitet: ![ADFS-Anmeldeseite](ws-federation/_static/AdfsLoginPage.png)</span><span class="sxs-lookup"><span data-stu-id="660be-165">With ADFS as the provider, the button redirects to an ADFS sign-in page: ![ADFS sign-in page](ws-federation/_static/AdfsLoginPage.png)</span></span>

<span data-ttu-id="660be-166">Wenn Azure Active Directory als Anbieter fungiert, wird die Schaltfläche an eine Aad-Anmeldeseite umgeleitet: ![Aad-Anmeldeseite](ws-federation/_static/AadSignIn.png)</span><span class="sxs-lookup"><span data-stu-id="660be-166">With Azure Active Directory as the provider, the button redirects to an AAD sign-in page: ![AAD sign-in page](ws-federation/_static/AadSignIn.png)</span></span>

<span data-ttu-id="660be-167">Eine erfolgreiche Anmeldung für einen neuen Benutzer leitet zur Benutzer Registrierungsseite der App um: ![Register Seite](ws-federation/_static/Register.png)</span><span class="sxs-lookup"><span data-stu-id="660be-167">A successful sign-in for a new user redirects to the app's user registration page: ![Register page](ws-federation/_static/Register.png)</span></span>