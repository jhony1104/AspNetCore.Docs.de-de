---
title: Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit der Authentifizierungs Bibliothek
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: f9cc2884dcd94c729c45a056ae4327a2c75d34be
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083590"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit der Authentifizierungs Bibliothek

Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine eigenständige Blazor Webassembly-APP zu erstellen, die `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Bibliothek verwendet:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, fügen Sie die Output-Option im Befehl mit einem Pfad (z. b. `-o BlazorSample`) ein. Der Name des Ordners wird auch Teil des Projekt namens.

Erstellen Sie in Visual Studio [eine Blazor Webassembly-App](xref:blazor/get-started). Legen Sie die **Authentifizierung** auf **einzelne Benutzerkonten** fest, indem Sie die Option **Benutzerkonten in-APP speichern** auswählen.

## <a name="authentication-package"></a>Authentifizierungs Paket

Wenn eine APP für die Verwendung einzelner Benutzerkonten erstellt wird, erhält die APP automatisch einen Paket Verweis für das `Microsoft.AspNetCore.Components.WebAssembly.Authentication`-Paket in der Projektdatei der app. Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.

Wenn Sie eine Authentifizierung zu einer APP hinzufügen, fügen Sie das Paket manuell zur Projektdatei der APP hinzu:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Ersetzen Sie `{VERSION}` im vorangehenden Paket Verweis durch die Version des `Microsoft.AspNetCore.Blazor.Templates` Pakets, das im <xref:blazor/get-started> Artikel angezeigt wird.

## <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienst

Die Unterstützung für das Authentifizieren von Benutzern wird im Dienst Container mit der `AddOidcAuthentication`-Erweiterungsmethode registriert, die vom `Microsoft.AspNetCore.Components.WebAssembly.Authentication`-Paket bereitgestellt wird. Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die APP mit dem Identitäts Anbieter (IP) interagieren muss.

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

Die Authentifizierungs Unterstützung für eigenständige apps wird mithilfe von Open ID Connect (oidc) angeboten. Die `AddOidcAuthentication`-Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer App mithilfe von oidc erforderlich sind. Die für die Konfiguration der APP erforderlichen Werte können von der IP-Adresse abgerufen werden, z. b. Google, Microsoft oder einem anderen oidc-kompatiblen Anbieter. Rufen Sie die Werte ab, wenn Sie die APP registrieren, die in der Regel in Ihrem Onlineportal erfolgt.

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
