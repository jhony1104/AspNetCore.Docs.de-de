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
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>Authentifizieren von Benutzern mit dem WS-Verbund in ASP.net Core

In diesem Tutorial wird veranschaulicht, wie Sie es Benutzern ermöglichen, sich mit einem WS-Verbund-Authentifizierungs Anbieter wie Active Directory-Verbunddienste (AD FS) (ADFS) oder [Azure Active Directory](/azure/active-directory/) (AAD) anzumelden. Dabei wird die in [Facebook, Google und der Authentifizierung externer Anbieter](xref:security/authentication/social/index)beschriebene ASP.net Core 2,0-Beispiel-App verwendet.

Bei ASP.net Core 2,0-apps wird die WS-Verbund Unterstützung von [Microsoft. aspnetcore. Authentication. wsfederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)bereitgestellt. Diese Komponente wird von [Microsoft. owin. Security. wsfederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) portiert und nutzt viele der Mechanismen dieser Komponente. Allerdings unterscheiden sich die Komponenten auf einige wichtige Weise.

Standardmäßig wird die neue Middleware:

* Lässt keine nicht angeforderten Anmeldungen zu. Diese Funktion des WS-Verbund Protokolls ist anfällig für XSRF-Angriffe. Sie kann jedoch mit der Option `AllowUnsolicitedLogins` aktiviert werden.
* Prüft nicht alle Formular Beiträge auf Anmelde Nachrichten. Nur Anforderungen an die `CallbackPath` werden auf Anmeldungen geprüft. `CallbackPath` ist standardmäßig `/signin-wsfed`, kann aber über die geerbte [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) -Eigenschaft der [wsfederationoptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) -Klasse geändert werden. Dieser Pfad kann für andere Authentifizierungs Anbieter freigegeben werden, indem die [skipunerkenzedrequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) -Option aktiviert wird.

## <a name="register-the-app-with-active-directory"></a>Registrieren der App bei Active Directory

### <a name="active-directory-federation-services"></a>Active Directory-Verbunddienste (AD FS)

* Öffnen **Sie den Assistenten zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite** in der AD FS-Verwaltungskonsole:

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite](ws-federation/_static/AdfsAddTrust.png)

* Daten manuell eingeben:

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite: Datenquelle](ws-federation/_static/AdfsSelectDataSource.png)

* Geben Sie einen anzeigen Amen für die vertrauende Seite ein. Der Name ist für die ASP.net Core-APP nicht wichtig.

* [Microsoft. aspnetcore. Authentication. wsfederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) hat keine Unterstützung für die tokenverschlüsselung, konfigurieren Sie also kein tokenverschlüsselungszertifikat:

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite](ws-federation/_static/AdfsConfigureCert.png)

* Aktivieren Sie die Unterstützung für das passive WS-Verbund Protokoll mithilfe der App-URL. Vergewissern Sie sich, dass der Port für die APP richtig ist:

![Assistent zum Hinzufügen von Vertrauens Stellungen der vertrauenden Seite](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Dabei muss es sich um eine HTTPS-URL handeln. IIS Express können ein selbst signiertes Zertifikat bereitstellen, wenn die APP während der Entwicklung gehostet wird. Kestrel erfordert eine manuelle Zertifikat Konfiguration. Weitere Informationen finden Sie in der [Kestrel-Dokumentation](xref:fundamentals/servers/kestrel) .

* Klicken Sie im restlichen Assistenten auf **weiter** , und **Schließen** Sie am Ende.

* Für ASP.net Core Identität ist ein **Name-ID** -Anspruch erforderlich. Fügen Sie eine aus dem Dialogfeld " **Anspruchs Regeln bearbeiten** " hinzu:

![Bearbeiten von Anspruchsregeln](ws-federation/_static/EditClaimRules.png)

* Belassen Sie im **Assistenten zum Hinzufügen von Transformations Anspruchs Regeln**die Vorlage Standardwert **für LDAP-Attribute als Ansprüche senden** , und klicken Sie auf **weiter**. Fügen Sie eine Regel hinzu, die das LDAP **-Attribut Sam-Account-Name** dem ausgehenden Anspruch der **namens-ID** zuordnet:

![Assistent zum Hinzufügen von Transformations Anspruchs Regeln: Konfigurieren von Anspruchs Regeln](ws-federation/_static/AddTransformClaimRule.png)

* Klicken Sie im Fenster **Anspruchs Regeln bearbeiten** auf **Fertig** stellen > **OK** .

### <a name="azure-active-directory"></a>Azure Active Directory

* Navigieren Sie zum Blatt App-Registrierungen des Aad-Mandanten. Klicken Sie auf **neue Anwendungs Registrierung**:

![Azure Active Directory: App-Registrierungen](ws-federation/_static/AadNewAppRegistration.png)

* Geben Sie einen Namen für die APP-Registrierung ein. Dies ist für die ASP.net Core-APP nicht von Bedeutung.
* Geben Sie die URL ein, die die APP als **Anmelde-URL**überwacht:

![Azure Active Directory: App-Registrierung erstellen](ws-federation/_static/AadCreateAppRegistration.png)

* Klicken Sie auf **Endpunkte** , und notieren Sie die Verbund **Metadaten-Dokument** -URL Dies sind die `MetadataAddress`der WS-Verbund Middleware:

![Azure Active Directory: Endpunkte](ws-federation/_static/AadFederationMetadataDocument.png)

* Navigieren Sie zur neuen App-Registrierung. Klicken Sie auf **Einstellungen** > **Eigenschaften** , und notieren Sie sich den **App-ID-URI**. Dies sind die `Wtrealm`der WS-Verbund Middleware:

![Azure Active Directory: Eigenschaften der APP-Registrierung](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a>Verwenden von WS-Federation ohne ASP.net Core Identität

Die WS-Verbund-Middleware kann ohne Identität verwendet werden. Beispiel:
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a>WS-Verbund als externen Anmelde Anbieter für ASP.net Core Identität hinzufügen

* Fügen Sie eine Abhängigkeit von [Microsoft. aspnetcore. Authentication. wsfederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) zum Projekt hinzu.
* Fügen Sie `Startup.ConfigureServices`WS-Verbund hinzu:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>Anmelden mit dem WS-Verbund

Navigieren Sie zur APP, und klicken Sie auf den Link **Anmelden** im Navigations Header. Es gibt eine Option zum Anmelden mit wsfederation: ![Anmeldeseite](ws-federation/_static/WsFederationButton.png)

Bei ADFS als Anbieter wird die Schaltfläche zu einer AD FS-Anmeldeseite umgeleitet: ![ADFS-Anmeldeseite](ws-federation/_static/AdfsLoginPage.png)

Wenn Azure Active Directory als Anbieter fungiert, wird die Schaltfläche an eine Aad-Anmeldeseite umgeleitet: ![Aad-Anmeldeseite](ws-federation/_static/AadSignIn.png)

Eine erfolgreiche Anmeldung für einen neuen Benutzer leitet zur Benutzer Registrierungsseite der App um: ![Register Seite](ws-federation/_static/Register.png)