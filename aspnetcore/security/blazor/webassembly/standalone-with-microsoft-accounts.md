---
title: Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit Microsoft-Konten
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 6883af3486256e7c6905626d8da09e8ae0c4a896
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083650"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Sichern eines ASP.net Core Blazor eigenständigen Webassembly-App mit Microsoft-Konten

Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

So erstellen Sie eine eigenständige Blazor Webassembly-APP, die [Microsoft-Konten mit Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) für die Authentifizierung verwendet:

1. [Erstellen eines Aad-Mandanten und einer Webanwendung](/azure/active-directory/develop/v2-overview)

   Registrieren Sie eine Aad-App im Bereich **Azure Active Directory** > **App-Registrierungen** des Azure-Portal:

   1 \. Geben Sie einen **Namen** für die APP an (z. b. **Blazor Aad-Client**).<br>
   2 \. Wählen Sie **unter Unterstützte Konto Typen**die Option **Konten in einem beliebigen Organisations Verzeichnis**aus.<br>
   3 \. Lassen Sie die Dropdown-Einstellung für **Umleitungs-URI** auf **Web**und einen Umleitungs-URI `https://localhost:5001/authentication/login-callback`.<br>
   4 \. Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **erteilen Sie Administrator Berechtigungen für OpenID und offline_access Berechtigungen** .<br>
   5 \. Wählen Sie **Registrieren**.

   In der **Authentifizierung** > **Platt Form Konfigurationen** > **Web**:

   1 \. Vergewissern Sie sich, dass der **Umleitungs-URI** `https://localhost:5001/authentication/login-callback` vorhanden ist.<br>
   2 \. Aktivieren Sie für **implizite Gewährung**die Kontrollkästchen für **Zugriffs Token** und **ID-Token**.<br>
   3 \. Die verbleibenden Standardwerte für die APP sind für diese Art von akzeptabel.<br>
   4 \. Wählen Sie die Schaltfläche **Speichern** aus.

   Notieren Sie die Anwendungs-ID (Client-ID) (z. b. `11111111-1111-1111-1111-111111111111`).

1. Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor aufgezeichneten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Um den Ausgabe Speicherort anzugeben, der einen Projektordner erstellt, wenn er nicht vorhanden ist, fügen Sie die Output-Option im Befehl mit einem Pfad (z. b. `-o BlazorSample`) ein. Der Name des Ordners wird auch Teil des Projekt namens.

Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen ausführen können:

* Melden Sie sich mit einem Microsoft-Konto bei der APP an.
* Anfordern von Zugriffs Token für Microsoft-APIs mit dem gleichen Ansatz wie für eigenständige Blazor-apps, vorausgesetzt, dass Sie die APP ordnungsgemäß konfiguriert haben. Weitere Informationen finden Sie unter [Schnellstart: Konfigurieren einer Anwendung für die](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)Bereitstellung von Web-APIs.

## <a name="authentication-package"></a>Authentifizierungs Paket

Wenn eine APP für die Verwendung von Geschäfts-, Schul-oder unikonten (`SingleOrg`) erstellt wird, erhält die APP automatisch einen Paket Verweis für die [Microsoft-Authentifizierungs Bibliothek](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`). Das Paket enthält eine Reihe von primitiven, die der APP helfen, Benutzer zu authentifizieren und Token abzurufen, um geschützte APIs aufzurufen.

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
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

Die `AddMsalAuthentication`-Methode akzeptiert einen Rückruf, um die Parameter zu konfigurieren, die zum Authentifizieren einer APP erforderlich sind. Die für die Konfiguration der APP erforderlichen Werte können bei der Registrierung der APP aus der Microsoft-Konto Konfiguration abgerufen werden.

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

* [Schnellstart: Registrieren einer Anwendung bei der Microsoft Identity-Plattform](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Schnellstart: Konfigurieren einer Anwendung für die Bereitstellung von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
