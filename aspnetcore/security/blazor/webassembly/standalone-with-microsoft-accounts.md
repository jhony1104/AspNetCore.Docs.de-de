---
title: Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit Microsoft-Konten
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 6f884cbfc9ac50f38c415af522d3d09a3ef38712
ms.sourcegitcommit: 363e3a2a035f4082cb92e7b75ed150ba304258b3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82976843"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a>Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit Microsoft-Konten

Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

So erstellen Sie Blazor eine eigenständige Webassembly-APP, die [Microsoft-Konten mit Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) für die Authentifizierung verwendet:

1. [Erstellen eines Aad-Mandanten und einer Webanwendung](/azure/active-directory/develop/v2-overview)

   Registrieren Sie eine Aad-App im **Azure Active Directory** > **App-Registrierungen** Bereich der Azure-Portal:

   1\. Geben Sie einen **Namen** für die APP an (z. b. ** Blazor Client Aad**).<br>
   2\. Wählen Sie **unter Unterstützte Konto Typen**die Option **Konten in einem beliebigen Organisations Verzeichnis**aus.<br>
   3 \. Belassen Sie den **Umleitungs-URI** -Dropdown auf **Web**, und geben Sie `https://localhost:5001/authentication/login-callback`einen Umleitungs-URI von an.<br>
   4 \. Deaktivieren Sie **das** > Kontrollkästchen**Administrator Berechtigungen für OpenID und offline_access Berechtigungen gewähren** .<br>
   5 \. Wählen Sie **Registrieren**.

   Im**Web**der **Authentifizierungs** > **Platt Form Konfigurationen** > :

   1\. Bestätigen Sie, dass der `https://localhost:5001/authentication/login-callback` **Umleitungs-URI** von vorhanden ist<br>
   2\. Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.<br>
   3 \. Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.<br>
   4 \. Wählen Sie die Schaltfläche **Speichern** aus.

   Notieren Sie sich `11111111-1111-1111-1111-111111111111`die Anwendungs-ID (Client-ID) (z. b.).

1. Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem `-o BlazorSample`Pfad (z. b.) ein. Der Name des Ordners wird auch Teil des Projekt namens.

Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen ausführen können:

* Melden Sie sich mit einem Microsoft-Konto bei der APP an.
* Anfordern von Zugriffs Token für Microsoft-APIs mit dem gleichen Ansatz wie Blazor für eigenständige apps, vorausgesetzt, dass Sie die APP ordnungsgemäß konfiguriert haben. Weitere Informationen finden Sie unter [Schnellstart: Konfigurieren einer Anwendung für die](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)Bereitstellung von Web-APIs.

## <a name="authentication-package"></a>Authentifizierungs Paket

Wenn eine APP für die Verwendung von Geschäfts-, Schul-`SingleOrg`oder unikonten () erstellt wird, erhält die APP automatisch einen Paket Verweis`Microsoft.Authentication.WebAssembly.Msal`für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) (). Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.

Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Ersetzen `{VERSION}` Sie im vorherigen Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.

Das `Microsoft.Authentication.WebAssembly.Msal` Paket fügt das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket transitiv der APP hinzu.

## <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienst

Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container `AddMsalAuthentication` mit der vom `Microsoft.Authentication.WebAssembly.Msal` Paket bereitgestellten Erweiterungsmethode registriert. Diese Methode richtet alle Dienste ein, die für die Interaktion der APP mit dem Identity Anbieter (IP) erforderlich sind.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

Die `AddMsalAuthentication` -Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind. Die für die Konfiguration der APP erforderlichen Werte können bei der Registrierung der APP aus der Microsoft-Konto Konfiguration abgerufen werden.

Die Konfiguration wird von der Datei " *wwwroot/appSettings. JSON* " bereitgestellt:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}"
  }
}
```

Beispiel:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a>Zugriffs Token-Bereiche

Die Blazor webassemblyvorlage konfiguriert die APP nicht automatisch, um ein Zugriffs Token für eine sichere API anzufordern. Fügen Sie zum Bereitstellen eines Zugriffs Tokens als Teil des Anmelde Flusses den Gültigkeitsbereich zu den standardzugriffstokenbereichen von hinzu `MsalProviderOptions`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Wenn die Azure-Portal einen Bereichs-URI bereitstellt und **die APP eine nicht behandelte Ausnahme** auslöst, wenn Sie eine 401-nicht *autorisierte* Antwort von der API empfängt, verwenden Sie einen Bereichs-URI, der das Schema und den Host nicht enthält. Der Azure-Portal kann z. b. eines der folgenden Bereich-URI-Formate bereitstellen:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Geben Sie den Bereichs-URI ohne das Schema und den Host an:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels *weitere Szenarien* :

* [Anfordern zusätzlicher Zugriffstoken](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Anfügen von Token an ausgehende Anforderungen](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Import Datei

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Indexseite

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

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

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/blazor/webassembly/aad-groups-roles>
* [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Schnellstart: Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
