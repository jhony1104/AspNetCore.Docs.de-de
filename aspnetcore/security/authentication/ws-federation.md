---
title: Authentifizieren von Benutzern mit WS-Verbund in ASP.NET Core
author: chlowell
description: Dieses Tutorial veranschaulicht die Verwendung von WS-Verbund in einer ASP.NET Core-app.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
uid: security/authentication/ws-federation
ms.openlocfilehash: 7967410686da0e59742b721c0154e143bf19ba01
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897567"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>Authentifizieren von Benutzern mit WS-Verbund in ASP.NET Core

Dieses Tutorial veranschaulicht, wie Benutzern die Anmeldung von eines WS-Verbund-Authentifizierungsanbieter wie Active Directory Federation Services (ADFS) oder [Azure Active Directory](/azure/active-directory/) (AAD). Er verwendet die ASP.NET Core 2.0-beispielanwendung, die in beschriebenen [Facebook, Google, und die Authentifizierung des externen Anbieter](xref:security/authentication/social/index).

Für apps mit ASP.NET Core 2.0, WS-Verbund-Unterstützung bereitgestellt [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation). Diese Komponente wird von portiert [Microsoft.Owin.Security.WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) und nutzt viele Mechanismen für die Komponente. Allerdings unterscheiden sich die Komponenten, in einigen wichtigen Aspekten zu erhalten.

Standardmäßig ist die neue Middleware:

* Lässt keine unerwünschten Anmeldungen zu. Dieses Feature von der WS-Verbund-Protokoll ist anfällig für XSRF-Angriffen. Es kann jedoch aktiviert werden, mit der `AllowUnsolicitedLogins` Option.
* Jedes Formular-Post-Anmeldung Nachrichten nicht überprüft werden. Nur Anforderungen an die `CallbackPath` SSO-Ins überprüft werden `CallbackPath` standardmäßig `/signin-wsfed` kann jedoch geändert werden über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [ WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) Klasse. Dieser Pfad kann mit anderen Authentifizierungsanbieter erforderlich freigegeben werden, durch Aktivieren der [SkipUnrecognizedRequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) Option.

## <a name="register-the-app-with-active-directory"></a>Registrieren der app mit Active Directory

### <a name="active-directory-federation-services"></a>Active Directory-Verbunddienste

* Öffnen Sie des Serverzertifikat **Relying Party Trust Assistenten zum Hinzufügen von** über die AD FS-Verwaltungskonsole:

![Relying Party Trust-Assistent zum Hinzufügen von: Willkommen](ws-federation/_static/AdfsAddTrust.png)

* Wählen Sie auf die Daten manuell eingeben:

![Relying Party Trust-Assistent zum Hinzufügen von: Auswählen einer Datenquelle](ws-federation/_static/AdfsSelectDataSource.png)

* Geben Sie einen Anzeigenamen für die vertrauende Seite ein. Der Name nicht wichtig, die ASP.NET Core-app.

* [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) fehlt die Unterstützung für die Verschlüsselung von token, damit Sie ein tokenverschlüsselungszertifikat nicht konfigurieren:

![Relying Party Trust-Assistent zum Hinzufügen von: Zertifikat konfigurieren](ws-federation/_static/AdfsConfigureCert.png)

* Aktivieren der Unterstützung für WS-Verbund Passive Protocol, verwenden die URL der app. Stellen Sie sicher, dass der Port für die app richtig ist:

![Relying Party Trust-Assistent zum Hinzufügen von: URL konfigurieren](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Dies muss eine HTTPS-URL sein. IIS Express bietet ein selbst signiertes Zertifikat, wenn die app während der Entwicklung zu hosten. Kestrel ist die manuelle Zertifikatkonfiguration erforderlich. Finden Sie unter den [Kestrel Dokumentation](xref:fundamentals/servers/kestrel) Weitere Details.

* Klicken Sie auf **Weiter** über den Assistenten und **schließen** am Ende.

* ASP.NET Core Identity verlangt, dass eine **namens-ID** Anspruch. Fügen Sie eine aus der **Edit Claim Rules** Dialogfeld:

![Anspruchsregeln bearbeiten](ws-federation/_static/EditClaimRules.png)

* In der **transformieren Anspruch Assistenten zum Hinzufügen von**, übernehmen Sie den Standardwert **LDAP-Attributen als Ansprüche senden** Vorlage ausgewählt, und klicken Sie auf **Weiter**. Fügen Sie eine Zuordnung für die Regel der **SAM-Kontonamen** LDAP-Attribut, um die **namens-ID** ausgehenden Anspruch:

![Fügen Sie die Assistenten zum Transformieren hinzu: Anspruchsregel konfigurieren](ws-federation/_static/AddTransformClaimRule.png)

* Klicken Sie auf **Fertig stellen** > **OK** in die **Edit Claim Rules** Fenster.

### <a name="azure-active-directory"></a>Azure Active Directory

* Navigieren Sie zum Blatt "des AAD-Mandanten-app-Registrierungen". Klicken Sie auf **Registrierung einer neuen Anwendung**:

![Azure Active Directory: App-Registrierungen](ws-federation/_static/AadNewAppRegistration.png)

* Geben Sie einen Namen für die app-Registrierung. Dies ist wichtig, die ASP.NET Core-app nicht.
* Geben Sie die URL, die die app, wie lauscht die **Anmelde-URL**:

![Azure Active Directory: Erstellen von app-Registrierung](ws-federation/_static/AadCreateAppRegistration.png)

* Klicken Sie auf **Endpunkte** und notieren Sie sich die **Verbundmetadaten-Dokument** URL. Dies ist der WS-Federation Middleware `MetadataAddress`:

![Azure Active Directory: Endpunkte](ws-federation/_static/AadFederationMetadataDocument.png)

* Navigieren Sie zu der neuen app-Registrierung. Klicken Sie auf **Einstellungen** > **Eigenschaften** und notieren Sie sich die **App ID-URI**. Dies ist der WS-Federation Middleware `Wtrealm`:

![Azure Active Directory: Eigenschaften der App-Registrierung](ws-federation/_static/AadAppIdUri.png)

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a>Hinzufügen von WS-Verbund als einem externen Anmeldeanbieter für ASP.NET Core Identity

* Fügen Sie eine Abhängigkeit auf [Microsoft.AspNetCore.Authentication.WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) zum Projekt.
* Hinzufügen von WS-Verbund, `Startup.ConfigureServices`:

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

### <a name="log-in-with-ws-federation"></a>Melden Sie sich mit WS-Verbund

Navigieren Sie zur app, und klicken Sie auf die **melden Sie sich bei** -Link in der Nav-Header. Es ist eine Option aus, um die Anmeldung mit WsFederation: ![Anmeldeseite](ws-federation/_static/WsFederationButton.png)

Mit ADFS als Anbieter leitet die Schaltfläche mit der an einen AD FS-Anmeldeseite ein: ![AD FS-Anmeldeseite](ws-federation/_static/AdfsLoginPage.png)

Mit Azure Active Directory als Anbieter leitet die Schaltfläche mit der an eine AAD-Anmeldeseite ein: ![AAD-Anmeldeseite](ws-federation/_static/AadSignIn.png)

Eine erfolgreiche Anmeldung für einen neuen Benutzer leitet an der app Benutzer-Registrierungsseite: ![Seite "registrieren"](ws-federation/_static/Register.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a>Verwenden von WS-Verbund, ohne ASP.NET Core Identity

Die WS-Verbund-Middleware kann ohne Identity verwendet werden. Zum Beispiel:

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
