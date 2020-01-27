---
title: Cloudauthentifizierung mit Azure Active Directory B2C in ASP.net Core
author: camsoper
description: Erfahren Sie, wie Sie Azure Active Directory B2C Authentifizierung mit ASP.net Core einrichten.
ms.author: casoper
ms.custom: mvc
ms.date: 01/21/2019
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 136fa47788456492a9a7fe6d9d9e5996c13e8c20
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727278"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a>Cloudauthentifizierung mit Azure Active Directory B2C in ASP.net Core

Von [Cam Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) ist eine Cloudlösung für die Verwaltung von Identität für Web- und mobilen apps. Der Dienst ermöglicht die Authentifizierung für apps, die in der Cloud und lokal gehostet werden. Authentifizierungstypen sind einzelne Konten, Konten sozialer Netzwerke, und Verbundbenutzer Unternehmenskonten zu authentifizieren. Darüber hinaus können Azure AD B2C Multi-Factor Authentication mit minimaler Konfiguration bereitstellen.

> [!TIP]
> Azure Active Directory (Azure AD) und Azure AD B2C sind separate Produktangebote. Azure AD-Mandant repräsentiert eine Organisation, auf, während ein Azure AD B2C-Mandanten ist, eine Sammlung von Identitäten, die mit Anwendungen der vertrauenden Seite verwendet werden. Weitere Informationen finden Sie unter [Azure AD B2C: häufig gestellte Fragen (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).

In diesem Tutorial erfahren Sie, wie Sie:

> [!div class="checklist"]
> * Erstellen eines Azure Active Directory B2C Mandanten
> * Registrieren einer APP in Azure AD B2C
> * Erstellen Sie mit Visual Studio eine ASP.net Core Web-App, die für die Verwendung des Azure AD B2C-Mandanten für die Authentifizierung konfiguriert ist
> * Konfigurieren von Richtlinien, die das Verhalten des Azure AD B2C Mandanten Steuern

## <a name="prerequisites"></a>Erforderliche Komponenten

Die folgenden Voraussetzungen gelten für diese exemplarische Vorgehensweise:

* [Microsoft Azure-Abonnement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Erstellen der Azure Active Directory B2C-Mandanten

Erstellen Sie einen Azure Active Directory B2C Mandanten, [wie in der Dokumentation beschrieben](/azure/active-directory-b2c/active-directory-b2c-get-started). Wenn Sie aufgefordert werden, ist ein Azure-Abonnement des Mandanten zuordnen optional für dieses Tutorial.

## <a name="register-the-app-in-azure-ad-b2c"></a>Registrieren der app in Azure AD B2C

Registrieren Sie Ihre APP im neu erstellten Azure AD B2C Mandanten mithilfe [der Schritte in der Dokumentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) im Abschnitt **Registrieren einer Web-App** . Beenden der **erstellen Sie ein Web-app-clientgeheimnis** Abschnitt. Ein clientgeheimnis ist für dieses Tutorial nicht erforderlich. 

Verwenden Sie die folgenden Werte:

| -Einstellung                       | {2&gt;Wert&lt;2}                     | Hinweise                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**                      | *&lt;App-Name&gt;*        | Geben Sie einen **Namen** für die app, die Ihre app für Kunden beschreibt.                                                                                                                                 |
| **Einschließen von Web-app / web-API** | Ja                       |                                                                                                                                                                                                    |
| **Impliziten Fluss zulassen**       | Ja                       |                                                                                                                                                                                                    |
| **Antwort-URL**                 | `https://localhost:44300/signin-oidc` | Antwort-URLs sind Endpunkte, an denen Azure AD B2C, die von Ihrer app angeforderte Token zurückgibt. Visual Studio stellt die zu verwendende Antwort-URL bereit. Geben Sie für den Moment `https://localhost:44300/signin-oidc` ein, um das Formular abzuschließen. |
| **App-ID-URI**                | Leer lassen               | Für dieses Tutorial erforderlich nicht.                                                                                                                                                                    |
| **Nativen Client einschließen**     | Nein                        |                                                                                                                                                                                                    |

> [!WARNING]
> Wenn Sie eine nicht-localhost-Antwort-URL einrichten, beachten Sie die Einschränkungen, die [in der Antwort-URL-Liste zulässig sind](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application). 

Nachdem die APP registriert wurde, wird die Liste der apps im Mandanten angezeigt. Wählen Sie die soeben registrierte App aus. Wählen Sie die **Kopie** Symbol rechts neben der **Anwendungs-ID** Feld, um ihn in die Zwischenablage zu kopieren.

Im Azure AD B2C Mandanten kann zurzeit nichts mehr konfiguriert werden, aber lassen Sie das Browserfenster geöffnet. Nachdem die ASP.net Core-App erstellt wurde, ist eine weitere Konfiguration vorhanden.

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Erstellen einer ASP.net Core-app in Visual Studio

Die Visual Studio Web Application-Vorlage kann für die verwenden Sie des Azure AD B2C-Mandantenverwaltungs für die Authentifizierung konfiguriert werden.

In Visual Studio:

1. Erstellen Sie eine neue ASP.NET Core-Webanwendung. 
2. Wählen Sie in der Liste der Vorlagen die Option **Webanwendung** aus.
3. Wählen Sie die **Authentifizierung ändern** Schaltfläche.
    
    ![Schaltfläche "Authentifizierung ändern"](./azure-ad-b2c/_static/changeauth.png)

4. Wählen Sie im Dialogfeld **Authentifizierung ändern** die Option **einzelne Benutzerkonten**aus, und wählen Sie dann in der Dropdown Liste die **Option mit einem vorhandenen Benutzerspeicher in der Cloud verbinden** aus. 
    
    ![Authentifizierung-Dialogfeld "ändern"](./azure-ad-b2c/_static/changeauthdialog.png)

5. Füllen Sie das Formular mit den folgenden Werten:
    
    | -Einstellung                       | {2&gt;Wert&lt;2}                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Domänenname**               | *&lt;den Domänen Namen Ihres B2C-Mandanten&gt;*          |
    | **Anwendungs-ID**            | *Fügen Sie &lt;die Anwendungs-ID aus der Zwischenablage ein&gt;* |
    | **Rückruf Pfad**             | *&lt;den Standardwert verwenden&gt;*                       |
    | **Richtlinie für Registrierung oder Anmeldung** | `B2C_1_SiUpIn`                                        |
    | **Kenn Wort Richtlinie zurücksetzen**     | `B2C_1_SSPR`                                          |
    | **Profil Richtlinie bearbeiten**       | *&lt;leer lassen&gt;*                                 |
    
    Wählen Sie den Link **Kopieren** neben dem **Antwort-URI** aus, um den Antwort-URI in die Zwischenablage zu kopieren. Wählen Sie **OK** schließen die **Authentifizierung ändern** Dialogfeld. Wählen Sie **OK** zum Erstellen der Web-app.

## <a name="finish-the-b2c-app-registration"></a>Abschließen der Registrierung der B2C-App

Kehren Sie zum Browserfenster zurück, in dem die B2C-App-Eigenschaften noch geöffnet sind. Ändern Sie die zuvor angegebene temporäre **Antwort-URL** in den aus Visual Studio kopierten Wert. Wählen Sie am oberen Rand des Fensters die Option **Speichern** aus.

> [!TIP]
> Wenn Sie die Antwort-URL nicht kopiert haben, verwenden Sie die HTTPS-Adresse auf der Registerkarte Debuggen in den Eigenschaften des Webprojekts, und fügen Sie den **callbackpath** -Wert aus *appSettings. JSON*an.

## <a name="configure-policies"></a>Konfigurieren von Richtlinien

Verwenden Sie die Schritte in der Azure AD B2C-Dokumentation, um [eine Registrierungs-oder Anmelde Richtlinie zu erstellen](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), und [Erstellen Sie dann eine Richtlinie zum Zurücksetzen des Kennworts](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions). Verwenden Sie die Beispielwerte finden Sie in der Dokumentation für **Identitätsanbieter**, **Registrierungsattribute**, und **anwendungsansprüche**. Die Verwendung der Schaltfläche **jetzt ausführen** zum Testen der Richtlinien, wie in der Dokumentation beschrieben, ist optional.

> [!WARNING]
> Stellen Sie sicher, dass die Richtlinien Namen genau wie in der Dokumentation beschrieben sind, da diese Richtlinien im Dialogfeld " **Authentifizierung ändern** " in Visual Studio verwendet wurden. Die Richtlinien Namen können in der *Datei "appSettings. JSON*" überprüft werden.

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a>Konfigurieren Sie die zugrunde liegenden openidconnectoptions/jwtbearertions/Cookie-Optionen.

Um die zugrunde liegenden Optionen direkt zu konfigurieren, verwenden Sie die entsprechende Schema Konstante in `Startup.ConfigureServices`:

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a>Ausführen der App

Drücken Sie in Visual Studio **F5** , um die APP zu erstellen und auszuführen. Wählen Sie nach dem Start der Web-App **akzeptieren** aus, um die Verwendung von Cookies zu akzeptieren (wenn Sie dazu aufgefordert werden), und wählen Sie dann **Anmelden**aus.

![Anmelden bei der APP](./azure-ad-b2c/_static/signin.png)

Der Browser leitet an den Azure AD B2C-Mandanten weiter. Melden Sie sich mit einem vorhandenen Konto, (wenn es erstellt wurde, testen die Richtlinien), oder wählen Sie **registrieren Sie sich jetzt** um ein neues Konto zu erstellen. Die **haben Ihr Kennwort vergessen?** Link wird verwendet, um ein vergessenes Kennwort zurückzusetzen.

![Azure AD B2C Anmeldung](./azure-ad-b2c/_static/b2csts.png)

Nach erfolgreicher Anmeldung wird der Browser an die Web-App umgeleitet.

![Success](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen eines Azure Active Directory B2C Mandanten
> * Registrieren einer APP in Azure AD B2C
> * Verwenden von Visual Studio zum Erstellen einer ASP.net Core-Webanwendung, die für die Verwendung des Azure AD B2C-Mandanten zur Authentifizierung konfiguriert
> * Konfigurieren von Richtlinien, die das Verhalten des Azure AD B2C Mandanten Steuern

Nun, da die ASP.net Core-App für die Verwendung von Azure AD B2C für die Authentifizierung konfiguriert ist, kann das [Autorisierungs Attribut](xref:security/authorization/simple) verwendet werden, um Ihre APP zu sichern. Entwickeln Sie Ihre APP weiter, indem Sie Folgendes lernen:

* [Anpassen der Benutzeroberfläche von Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Konfigurieren Sie die Anforderungen an die Kennwortkomplexität](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Aktivieren von Multi-Factor Authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Konfigurieren zusätzlicher Identitätsanbieter, z. B. [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), und andere.
* [Verwenden Sie die Azure AD Graph-API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) um zusätzliche Benutzerinformationen zu erhalten, wie z. B. die Gruppenmitgliedschaft, aus dem Azure AD B2C-Mandanten abzurufen.
* [Sichern einer ASP.net Core-Web-API mithilfe von Azure AD B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).
* [Rufen Sie eine .NET Web-API aus einer .NET Web-app mithilfe von Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).