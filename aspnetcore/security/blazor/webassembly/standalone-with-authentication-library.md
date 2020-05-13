---
title: Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit der Authentifizierungs Bibliothek
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 219364ef2e699ff1029536effd106a80ec02825c
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153413"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>Sichern einer ASP.net Core Blazor eigenständigen Webassembly-App mit der Authentifizierungs Bibliothek

Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*Befolgen Sie die Anweisungen in diesem Thema für Azure Active Directory (AAD) und Azure Active Directory B2C (AAD B2C). Weitere Informationen finden Sie in den Themen zu Aad und Aad B2C in diesem Knoten Inhaltsverzeichnis.*

Blazor `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine eigenständige Webassembly-APP zu erstellen, die die Bibliothek verwendet:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, schließen Sie die Output-Option in den Befehl mit einem Pfad (z `-o BlazorSample` . b.) ein. Der Name des Ordners wird auch Teil des Projekt namens.

Erstellen Sie in Visual Studio [eine Blazor Webassembly-App](xref:blazor/get-started). Legen Sie die **Authentifizierung** auf **einzelne Benutzerkonten** fest, indem Sie die Option **Benutzerkonten in-APP speichern** auswählen.

## <a name="authentication-package"></a>Authentifizierungs Paket

Wenn eine APP für die Verwendung einzelner Benutzerkonten erstellt wird, erhält die APP automatisch einen Paket Verweis für das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket in der Projektdatei der app. Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.

Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Ersetzen Sie `{VERSION}` im vorherigen Paket Verweis durch die Version des Pakets, das `Microsoft.AspNetCore.Blazor.Templates` im Artikel angezeigt wird <xref:blazor/get-started> .

## <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienst

Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der `AddOidcAuthentication` vom Paket bereitgestellten Erweiterungsmethode registriert `Microsoft.AspNetCore.Components.WebAssembly.Authentication` . Diese Methode richtet alle Dienste ein, die für die Interaktion der APP mit dem Identity Anbieter (IP) erforderlich sind.

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

Die Konfiguration wird von der Datei " *wwwroot/appSettings. JSON* " bereitgestellt:

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

Die Authentifizierungs Unterstützung für eigenständige apps wird mithilfe von Open ID Connect (oidc) angeboten. Die- `AddOidcAuthentication` Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer App mithilfe von oidc erforderlich sind. Die für die Konfiguration der APP erforderlichen Werte können von der oidc-kompatiblen IP-Adresse abgerufen werden. Rufen Sie die Werte ab, wenn Sie die APP registrieren, die in der Regel in Ihrem Onlineportal erfolgt.

## <a name="access-token-scopes"></a>Zugriffs Token-Bereiche

Die Blazor webassemblyvorlage konfiguriert die APP nicht automatisch, um ein Zugriffs Token für eine sichere API anzufordern. Fügen Sie zum Bereitstellen eines Zugriffs Tokens als Teil des Anmelde Flusses den Gültigkeitsbereich zu den standardtokenbereichen von hinzu `OidcProviderOptions` :

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
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
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels *weitere Szenarien* :

* [Anfordern zusätzlicher Zugriffstoken](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Anfügen von Token an ausgehende Anforderungen](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Import Datei

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Indexseite

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

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
* [Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer APP mit einem sicheren Standard Client](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
