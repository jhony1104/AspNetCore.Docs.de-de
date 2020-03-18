---
title: Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory B2C
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: b4d32e91b4013cbea37baecb972a535d2874d3d1
ms.sourcegitcommit: 5bdc54162d7dea8d9fa54ac3055678db23586af1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/17/2020
ms.locfileid: "79434459"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit Azure Active Directory B2C

Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

So erstellen Sie eine eigenständige Blazor Webassembly-APP, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet:

1. Befolgen Sie die Anweisungen in den folgenden Themen, um einen Mandanten zu erstellen und eine Web-App im Azure-Portal zu registrieren:

   * [Erstellen Sie einen Aad B2C](/azure/active-directory-b2c/tutorial-create-tenant) Mandanten &ndash; notieren Sie die folgenden Informationen:

     1 \. Aad B2C Instanz (z. b. `https://contoso.b2clogin.com/`, einschließlich des nachgestellten Schrägstrichs)<br>
     2 \. Aad B2C Mandanten Domäne (z. b. `contoso.onmicrosoft.com`)

   * [Registrieren Sie eine Webanwendung](/azure/active-directory-b2c/tutorial-register-applications) &ndash; nehmen Sie bei der APP-Registrierung folgende Auswahl vor:

     1 \. Legen Sie **Web-App/Web-API** auf **Ja**fest.<br>
     2 \. Legen Sie **implizites Flow zulassen** auf **Ja**fest.<br>
     3 \. Fügen Sie eine **Antwort-URL** `https://localhost:5001/authentication/login-callback`hinzu.

     Notieren Sie die Anwendungs-ID (Client-ID) (z. b. `11111111-1111-1111-1111-111111111111`).

   * [Erstellen Sie benutzerflows](/azure/active-directory-b2c/tutorial-create-user-flows) , &ndash; einen Registrierungs-und Anmelde benutzerflow erstellen.

     Wählen Sie mindestens das **Anwendungs Anspruchs** > Benutzer Attribut " **Anzeige Name** " aus, um die `context.User.Identity.Name` in der `LoginDisplay` Komponente (*Shared/logindisplay. Razor*) aufzufüllen.

     Zeichnen Sie den für die APP erstellten Registrierungs-und Anmeldedaten Flussnamen auf (z. b. `B2C_1_signupsignin`).

1. Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, fügen Sie die Output-Option im Befehl mit einem Pfad (z. b. `-o BlazorSample`) ein. Der Name des Ordners wird auch Teil des Projekt namens.

## <a name="authentication-package"></a>Authentifizierungs Paket

Wenn eine App erstellt wird, um ein einzelnes B2C-Konto (`IndividualB2C`) zu verwenden, erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.

Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Ersetzen Sie `{VERSION}` im vorangehenden Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.

Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.

## <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienst

Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der `AddMsalAuthentication`-Erweiterungsmethode registriert, die vom `Microsoft.Authentication.WebAssembly.Msal`-Paket bereitgestellt wird. Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

Die `AddMsalAuthentication`-Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind. Die für die Konfiguration der APP erforderlichen Werte können aus der Aad-Konfiguration des Azure-Portals abgerufen werden, wenn Sie die APP registrieren.

Die Vorlage Blazor Webassembly konfiguriert die APP nicht automatisch so, dass Sie ein Zugriffs Token für eine sichere API anfordert. Fügen Sie zum Bereitstellen eines Tokens als Teil des Anmelde Flusses den Gültigkeitsbereich zu den standardzugriffstokenbereichen der `MsalProviderOptions`hinzu:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{API ID URI}/{SCOPE}");
});
```

## <a name="index-page"></a>Indexseite

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a>App-Komponente

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>Redirecttologin-Komponente

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>Logindisplay-Komponente

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Authentifizierungs Komponente

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authentication/azure-ad-b2c>
* [Tutorial zum Erstellen eines Azure Active Directory B2C-Mandanten](/azure/active-directory-b2c/tutorial-create-tenant)
