---
title: Sichern einer Blazor eigenständigen Core WebAssembly-App mit der Authentifizierungsbibliothek ASP.NET Core WebAssembly
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977040"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>Sichern einer Blazor eigenständigen Core WebAssembly-App mit der Authentifizierungsbibliothek ASP.NET Core WebAssembly

Von [Javier Calvarro Nelson](https://github.com/javiercn) und Luke [Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*Befolgen Sie für Azure Active Directory (AAD) und Azure Active Directory B2C (AAD B2C) nicht die Anweisungen in diesem Thema. Siehe die Themen AAD und AAD B2C in diesem Inhaltsverzeichnisknoten.*

Um eine Blazor eigenständige WebAssembly-App `Microsoft.AspNetCore.Components.WebAssembly.Authentication` zu erstellen, die Bibliothek verwendet, führen Sie den folgenden Befehl in einer Befehlsshell aus:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Um den Ausgabespeicherort anzugeben, der einen Projektordner erstellt, falls dieser nicht vorhanden ist, fügen `-o BlazorSample`Sie die Ausgabeoption in den Befehl mit einem Pfad ein (z. B. ). Der Ordnername wird auch Teil des Projektnamens.

Erstellen Sie in Visual Studio [eine Blazor WebAssembly-App](xref:blazor/get-started). Legen Sie **die Authentifizierung** auf **einzelne Benutzerkonten** mit der **Option Benutzerkonten speichern in der App** fest.

## <a name="authentication-package"></a>Authentifizierungspaket

Wenn eine App für die Verwendung einzelner Benutzerkonten erstellt wird, erhält die App automatisch eine Paketreferenz für das `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Paket in der Projektdatei der App. Das Paket stellt eine Reihe von Primitiven bereit, die der App helfen, Benutzer zu authentifizieren und Token zum Aufrufen geschützter APIs zu erhalten.

Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell zur Projektdatei der App hinzu:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Ersetzen `{VERSION}` Sie im vorherigen Paketverweis `Microsoft.AspNetCore.Blazor.Templates` durch die <xref:blazor/get-started> im Artikel gezeigte Version des Pakets.

## <a name="authentication-service-support"></a>Unterstützung des Authentifizierungsdienstes

Unterstützung für die Authentifizierung von Benutzern wird `AddOidcAuthentication` im Dienstcontainer `Microsoft.AspNetCore.Components.WebAssembly.Authentication` mit der vom Paket bereitgestellten Erweiterungsmethode registriert. Diese Methode richtet alle Dienste ein, die für die Interaktion der App mit dem Identitätsanbieter (IDENTITY Provider, IP) erforderlich sind.

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

Die Authentifizierungsunterstützung für eigenständige Apps wird mit Open ID Connect (OIDC) angeboten. Die `AddOidcAuthentication` Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die erforderlich sind, um eine App mit OIDC zu authentifizieren. Die für die Konfiguration der App erforderlichen Werte können über die OIDC-kompatible IP abgerufen werden. Rufen Sie die Werte ab, wenn Sie die App registrieren, was in der Regel in ihrem Onlineportal auftritt.

## <a name="access-token-scopes"></a>Zugriffstokenbereiche

Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch, um ein Zugriffstoken für eine sichere API anzufordern. Um ein Token als Teil des Anmeldeflusses zu bereitstellen, fügen Sie `OidcProviderOptions`den Bereich den Standardtokenbereichen der folgenden Werte hinzu:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Wenn das Azure-Portal einen Bereichs-URI bereitstellt und **die App eine nicht behandelte Ausnahme auslöst,** wenn sie eine *401 nicht autorisierte* Antwort von der API empfängt, versuchen Sie, einen Bereichs-URI zu verwenden, der das Schema und den Host nicht enthält. Das Azure-Portal kann z. B. eines der folgenden Bereichs-URI-Formate bereitstellen:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Geben Sie den Bereichs-URI ohne Schema und Host:
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Weitere Informationen finden Sie unter <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>Imports-Datei

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Indexseite

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>App-Komponente

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin-Komponente

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay-Komponente

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Authentifizierungskomponente

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Zusätzliche Zugriffstoken anfordern](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
