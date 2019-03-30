---
title: Setup von Microsoft Account externen Anmeldung mit ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht die Integration von Microsoft-Konto-Benutzerauthentifizierung in eine vorhandene ASP.NET Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 1733d049d6752c24d7749b5b5ae2a4b866492358
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750998"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>Setup von Microsoft Account externen Anmeldung mit ASP.NET Core

Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Tutorial erfahren Sie, wie Sie Benutzern die Anmeldung mit ihrem Microsoft-Kontos mit einem ASP.NET Core 2.0-Beispielprojekt auf erstellt ermöglichen, sich die [Vorgängerseite](xref:security/authentication/social/index).

## <a name="create-the-app-in-microsoft-developer-portal"></a>Erstellen einer app in Microsoft-Entwicklerportal

* Navigieren Sie zu [ https://apps.dev.microsoft.com ](https://apps.dev.microsoft.com) erstellen, und melden Sie sich bei einem Microsoft-Konto:

![Anmeldedialogfeld](index/_static/MicrosoftDevLogin.png)

Wenn Sie bereits über ein Microsoft-Konto haben, tippen Sie auf  **[erstellen!](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)** Nach der Anmeldung werden Sie zur weitergeleitet **meiner Anwendungen** Seite:

![Microsoft Developer-Portal in Microsoft Edge öffnen](index/_static/MicrosoftDev.png)

* Tippen Sie auf **fügen Sie eine app** in der oberen rechten Ecke, und geben Sie Ihre **Anwendungsname** und **Contact Email**:

![Dialogfeld "neue Anwendungsregistrierung"](index/_static/MicrosoftDevAppCreate.png)

* Deaktivieren Sie für die Zwecke dieses Tutorials die **geführtes Setup** Kontrollkästchen.

* Tippen Sie auf **erstellen** , weiterhin die **Registrierung** Seite. Geben Sie eine **Name** und notieren Sie den Wert der **Anwendungs-Id**, die Sie als verwenden `ClientId` später im Tutorial:

![Registrierungsseite](index/_static/MicrosoftDevAppReg.png)

* Tippen Sie auf **Plattform hinzufügen** in die **Plattformen** aus, und wählen Sie die **Web** Plattform:

![Plattform-Dialogfeld "hinzufügen"](index/_static/MicrosoftDevAppPlatform.png)

* In der neuen **Web** Plattform Geben Sie Ihre Entwicklung-URL mit `/signin-microsoft` angefügt, die in der **Umleitungs-URLs** Feld (z. B.: `https://localhost:44320/signin-microsoft`). Das Microsoft-Authentifizierungsschema, das später in diesem Tutorial konfiguriert automatisch behandelt Anforderungen an `/signin-microsoft` Route zum Implementieren des OAuth-Flows:

![Web-Plattform-Abschnitt](index/_static/MicrosoftRedirectUri.png)

> [!NOTE]
> Der URI-Segment `/signin-microsoft` ist als der standardrückruf des Authentifizierungsanbieters Microsoft festgelegt. Sie können den standardrückruf-URI ändern, während der Konfiguration der Microsoft-Authentifizierung-Middleware über die geerbte [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) Eigenschaft der [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) Klasse.

* Tippen Sie auf **-URL hinzufügen** um sicherzustellen, dass die URL wurde hinzugefügt.

* Füllen Sie ggf. Weitere Anwendungseinstellungen, bei Bedarf, und tippen Sie auf **speichern** am unteren Rand der Seite, app-Konfiguration zu speichern.

* Bei der Bereitstellung der Website müssen Sie noch einmal die **Registrierung** Seite, und legen Sie eine neue Öffentliche URL.

## <a name="store-microsoft-application-id-and-password"></a>Store Microsoft Anwendungs-Id und Kennwort

* Beachten Sie die `Application Id` angezeigt, auf die **Registrierung** Seite.

* Tippen Sie auf **neues Kennwort generieren** in die **Anwendungsgeheimnisse** Abschnitt. Dieser Befehl zeigt ein Feld, in dem Sie das anwendungskennwort kopieren können:

![Dialogfeld "Neues Kennwort wurde generiert"](index/_static/MicrosoftDevPassword.png)

Verknüpfen Sie sensible Einstellungen wie Microsoft `Application ID` und `Password` zu Ihrer Anwendungskonfigurationsdatei mithilfe der [Secret Manager](xref:security/app-secrets). Benennen Sie die Token für die Zwecke dieses Lernprogramms, `Authentication:Microsoft:ApplicationId` und `Authentication:Microsoft:Password`.

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Konfigurieren von Microsoft-Kontoauthentifizierung

Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount) Paket ist bereits installiert.

* Um dieses Paket mit Visual Studio 2017 zu installieren, mit der Maustaste, auf das Projekt, und wählen **NuGet-Pakete verwalten**.
* Führen Sie Folgendes in Ihrem Projektverzeichnis, um mit .NET Core-CLI zu installieren:

   `dotnet add package Microsoft.AspNetCore.Authentication.MicrosoftAccount`

::: moniker range=">= aspnetcore-2.0"

Fügen Sie den Microsoft-Account-Dienst in der `ConfigureServices` -Methode in der *"Startup.cs"* Datei:

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddMicrosoftAccount(microsoftOptions =>
{
    microsoftOptions.ClientId = Configuration["Authentication:Microsoft:ApplicationId"];
    microsoftOptions.ClientSecret = Configuration["Authentication:Microsoft:Password"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Fügen Sie die Microsoft-Account-Middleware in der `Configure` -Methode in der *"Startup.cs"* Datei:

```csharp
app.UseMicrosoftAccountAuthentication(new MicrosoftAccountOptions()
{
    ClientId = Configuration["Authentication:Microsoft:ApplicationId"],
    ClientSecret = Configuration["Authentication:Microsoft:Password"]
});
```

::: moniker-end

Obwohl die Terminologie, die auf Microsoft-Entwicklerportal diese Token Namen `ApplicationId` und `Password`, sie als verfügbar gemacht werden `ClientId` und `ClientSecret` zum Konfigurations-API.

Finden Sie unter den [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API-Referenz für Weitere Informationen zu den Konfigurationsoptionen, die von der Microsoft-Account-Authentifizierung unterstützt. Dies kann verwendet werden, um verschiedene Informationen über den Benutzer anzufordern.

## <a name="sign-in-with-microsoft-account"></a>Melden Sie sich mit Microsoft-Konto

Führen Sie die Anwendung, und klicken Sie auf **melden Sie sich bei**. Eine Option aus, um sich mit Microsoft anmelden wird angezeigt:

![Webanwendung Log auf: Benutzer nicht authentifiziert.](index/_static/DoneMicrosoft.png)

Wenn Sie auf Microsoft klicken, werden Sie für die Authentifizierung an Microsoft weitergeleitet. Nachdem Sie sich mit Ihrem Microsoft Account (sofern nicht bereits angemeldet) werden Sie aufgefordert, zu der app den Zugriff auf Ihre Infos ermöglichen:

![Dialogfeld "Microsoft-Authentifizierung"](index/_static/MicrosoftLogin.png)

Tippen Sie auf **Ja** und Sie gelangen zurück auf der Website, in dem Sie Ihre e-Mail-Adresse festlegen können.

Sie sind jetzt angemeldet, sich mit Ihren Microsoft-Anmeldeinformationen:

![-Webanwendung: Benutzerauthentifizierung](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Problembehandlung

* Wenn der Microsoft-Account-Anbieter auf eine Fehler-Anmeldeseite umleitet, beachten Sie die Fehler Titel und Beschreibung Abfragezeichenfolgen-Parameter direkt nach der `#` (Hashtag) im Uri.

  Obwohl die Fehlermeldung ein Problem mit der Microsoft-Authentifizierung angeben anscheinend, ist die häufigste Ursache Ihrer Anwendungs-Uri, die nicht mit einer der der **Umleitungs-URIs** für die **Web** Plattform .
* **ASP.NET Core 2.x nur:** Wenn Identität nicht, durch den Aufruf konfiguriert ist `services.AddIdentity` in `ConfigureServices`, Authentifizierungsversuch führt zu *ArgumentException: Die Option 'SignInScheme' muss angegeben werden*. Die Projektvorlage, die in diesem Tutorial verwendete wird sichergestellt, dass dies geschehen ist.
* Wenn die Standortdatenbank nicht erstellt wurde, indem die ursprüngliche Migration anwenden, erhalten Sie *Fehler bei ein Datenbankvorgang beim Verarbeiten der Anforderung* Fehler. Tippen Sie auf **Migrationen anwenden** der Datenbank zu erstellen und aktualisieren, um den Fehler hinaus fortgesetzt.

## <a name="next-steps"></a>Nächste Schritte

* In diesem Artikel wurde gezeigt, wie Sie mit Microsoft authentifizieren können. Führen Sie einen ähnlichen Ansatz für die Authentifizierung mit anderen Anbietern aufgeführt, auf die [Vorgängerseite](xref:security/authentication/social/index).

* Nachdem Sie Ihre Website in Azure-Web-app veröffentlichen, erstellen Sie eine neue `Password` im Microsoft Developer Portal.

* Legen Sie die `Authentication:Microsoft:ApplicationId` und `Authentication:Microsoft:Password` Anwendungseinstellungen im Azure-Portal. Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.
