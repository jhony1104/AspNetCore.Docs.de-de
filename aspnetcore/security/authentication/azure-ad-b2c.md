---
title: Cloudauthentifizierung mit Azure Active Directory B2C in ASP.NET Core
author: camsoper
description: Erfahren Sie, wie Azure Active Directory B2C-Authentifizierung mit ASP.NET Core einrichten.
ms.author: casoper
ms.custom: mvc
ms.date: 02/27/2019
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: 54117bf0dd45305d060eef5fecfb98ed45f8ecdb
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815302"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a>Cloudauthentifizierung mit Azure Active Directory B2C in ASP.NET Core

Von [Cam Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) ist eine Cloudlösung für die Verwaltung von Identität für Web- und mobilen apps. Der Dienst ermöglicht die Authentifizierung für apps, die in der Cloud und lokal gehostet werden. Authentifizierungstypen sind einzelne Konten, Konten sozialer Netzwerke, und Verbundbenutzer Unternehmenskonten zu authentifizieren. Darüber hinaus bieten die Azure AD B2C Multi-Factor Authentication mit Minimalkonfiguration.

> [!TIP]
> Azure Active Directory (Azure AD) und Azure AD B2C sind separate Produktangebote. Azure AD-Mandant repräsentiert eine Organisation, auf, während ein Azure AD B2C-Mandanten ist, eine Sammlung von Identitäten, die mit Anwendungen der vertrauenden Seite verwendet werden. Weitere Informationen finden Sie unter [Azure AD B2C: Häufig gestellte Fragen (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).

In diesem Tutorial erfahren Sie, wie Sie:

> [!div class="checklist"]
> * Erstellen eines Azure Active Directory B2C-Mandanten
> * Registrieren einer app in Azure AD B2C
> * Verwenden Sie Visual Studio zum Erstellen einer ASP.NET Core-Web-Apps so konfiguriert, dass die Azure AD B2C-Mandanten für die Authentifizierung verwenden
> * Konfigurieren von Richtlinien, die Steuerung des Verhaltens des Azure AD B2C-Mandanten

## <a name="prerequisites"></a>Erforderliche Komponenten

Die folgenden Voraussetzungen gelten für diese exemplarische Vorgehensweise:

* [Microsoft Azure-Abonnement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Erstellen der Azure Active Directory B2C-Mandanten

Erstellen eines Azure Active Directory B2C-Mandanten [wie in der Dokumentation beschrieben](/azure/active-directory-b2c/active-directory-b2c-get-started). Wenn Sie aufgefordert werden, ist ein Azure-Abonnement des Mandanten zuordnen optional für dieses Tutorial.

## <a name="register-the-app-in-azure-ad-b2c"></a>Registrieren der app in Azure AD B2C

In den neu erstellten Azure AD B2C-Mandanten Registrieren Ihrer app über [die Schritte in der Dokumentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) unter der **Registrieren einer WebApp** Abschnitt. Beenden der **erstellen Sie ein Web-app-clientgeheimnis** Abschnitt. Ein clientgeheimnis ist für dieses Tutorial nicht erforderlich. 

Verwenden Sie die folgenden Werte ein:

| Einstellung                       | Wert                     | Hinweise                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Name**                      | *&lt;App-name&gt;*        | Geben Sie einen **Namen** für die app, die Ihre app für Kunden beschreibt.                                                                                                                                 |
| **Einschließen von Web-app / web-API** | Ja                       |                                                                                                                                                                                                    |
| **Impliziten Fluss zulassen**       | Ja                       |                                                                                                                                                                                                    |
| **Antwort-URL**                 | `https://localhost:44300/signin-oidc` | Antwort-URLs sind Endpunkte, an denen Azure AD B2C, die von Ihrer app angeforderte Token zurückgibt. Visual Studio bietet die Antwort-URL verwenden. Geben Sie für den Moment `https://localhost:44300/signin-oidc` um das Formular auszufüllen. |
| **App-ID-URI**                | Leer lassen               | Für dieses Tutorial erforderlich nicht.                                                                                                                                                                    |
| **Nativen Client einschließen**     | Nein                        |                                                                                                                                                                                                    |

> [!WARNING]
> Wenn das Einrichten einer nicht-Localhost-Antwort-URL, berücksichtigen die [Einschränkungen auf, was in der Antwort-URL-Liste zulässig ist](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application). 

Nachdem die app registriert wurde, wird die Liste der apps im Mandanten angezeigt. Wählen Sie die app, die gerade registriert wurde. Wählen Sie die **Kopie** Symbol rechts neben der **Anwendungs-ID** Feld, um ihn in die Zwischenablage zu kopieren.

Nichts mehr zu diesem Zeitpunkt im Azure AD B2C-Mandanten konfiguriert werden können, aber lassen das Browserfenster geöffnet. Es gibt weitere Konfigurationsschritte nach der Erstellung der ASP.NET Core-app.

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Erstellen einer ASP.NET Core-Apps in Visual Studio

Die Visual Studio Web Application-Vorlage kann für die verwenden Sie des Azure AD B2C-Mandantenverwaltungs für die Authentifizierung konfiguriert werden.

In Visual Studio:

1. Erstellen Sie eine neue ASP.NET Core-Webanwendung. 
2. Wählen Sie **Webanwendung** aus der Liste der Vorlagen.
3. Wählen Sie die **Authentifizierung ändern** Schaltfläche.
    
    ![Schaltfläche "Authentifizierung ändern"](./azure-ad-b2c/_static/changeauth.png)

4. In der **Authentifizierung ändern** wählen Sie im Dialogfeld **einzelne Benutzerkonten**, und wählen Sie dann **Herstellen einer Verbindung mit einem vorhandenen Benutzerspeicher in der Cloud** in der Dropdownliste aus. 
    
    ![Authentifizierung-Dialogfeld "ändern"](./azure-ad-b2c/_static/changeauthdialog.png)

5. Füllen Sie das Formular mit den folgenden Werten:
    
    | Einstellung                       | Wert                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Domänenname**               | *&lt;der Domänenname Ihres B2C-Mandanten&gt;*          |
    | **Anwendungs-ID**            | *&lt;Fügen Sie die Anwendungs-ID aus der Zwischenablage&gt;* |
    | **Rückrufpfad**             | *&lt;Verwenden Sie den Standardwert&gt;*                       |
    | **Richtlinie für Registrierung oder Anmeldung** | `B2C_1_SiUpIn`                                        |
    | **Richtlinie für die kennwortzurücksetzung**     | `B2C_1_SSPR`                                          |
    | **Richtlinie bearbeiten**       | *&lt;Leer lassen&gt;*                                 |
    
    Wählen Sie die **Kopie** link **Antwort-URI** der Antwort-URI in die Zwischenablage zu kopieren. Wählen Sie **OK** schließen die **Authentifizierung ändern** Dialogfeld. Wählen Sie **OK** zum Erstellen der Web-app.

## <a name="finish-the-b2c-app-registration"></a>Beenden Sie die B2C-app-Registrierung

Zurück zum Browserfenster mit den B2C-app-Eigenschaften noch geöffnet. Ändern Sie den temporären **Antwort-URL** früher auf den Wert kopiert aus Visual Studio angegeben. Wählen Sie **speichern** am oberen Rand des Fensters.

> [!TIP]
> Wenn Sie nicht die Antwort-URL kopieren, verwenden Sie die HTTPS-Adresse aus der Registerkarte "Debuggen" in den Web-Projekteigenschaften, und fügen die **CallbackPath** Wert *"appSettings.JSON"* .

## <a name="configure-policies"></a>Konfigurieren von Richtlinien

Verwenden Sie die Schritte in der Azure AD B2C-Dokumentation, [erstellen Sie eine Richtlinie für Registrierung oder Anmeldung](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), und klicken Sie dann [erstellen Sie eine Richtlinie zur kennwortzurücksetzung](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions). Verwenden Sie die Beispielwerte finden Sie in der Dokumentation für **Identitätsanbieter**, **Registrierungsattribute**, und **anwendungsansprüche**. Mithilfe der **jetzt ausführen** , um die Richtlinien zu testen, wie in der Dokumentation beschrieben ist optional.

> [!WARNING]
> Stellen Sie sicher, Richtliniennamen werden genau wie in der Dokumentation beschrieben, wie diese Richtlinien, in verwendet wurden der **Authentifizierung ändern** Dialogfeld in Visual Studio. Namen der Richtlinien überprüft werden können, im *"appSettings.JSON"* .

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a>Konfigurieren Sie die zugrunde liegenden OpenIdConnectOptions/JwtBearer/Cookie-Optionen

Um die zugrunde liegenden Optionen direkt konfigurieren zu können, verwenden Sie die entsprechenden Schema-Konstante in `Startup.ConfigureServices`:

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

Drücken Sie in Visual Studio **F5** erstellen und Ausführen der app. Nachdem die Web-app gestartet wird, wählen Sie **Accept** akzeptieren die Verwendung von Cookies (ggf.), und wählen Sie dann **Anmeldung**.

![Melden Sie sich bei der app](./azure-ad-b2c/_static/signin.png)

Der Browser leitet an die Azure AD B2C-Mandanten. Melden Sie sich mit einem vorhandenen Konto, (wenn es erstellt wurde, testen die Richtlinien), oder wählen Sie **registrieren Sie sich jetzt** um ein neues Konto zu erstellen. Die **haben Ihr Kennwort vergessen?** Link wird verwendet, um ein vergessenes Kennwort zurückzusetzen.

![Azure AD B2C-Anmeldung](./azure-ad-b2c/_static/b2csts.png)

Nach erfolgreicher Anmeldung, leitet den Browser zur Web-app.

![Erfolgreich](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen eines Azure Active Directory B2C-Mandanten
> * Registrieren einer app in Azure AD B2C
> * Verwenden Sie Visual Studio zum Erstellen einer ASP.NET Core-Webanwendung so konfiguriert, dass die Azure AD B2C-Mandanten für die Authentifizierung verwenden
> * Konfigurieren von Richtlinien, die Steuerung des Verhaltens des Azure AD B2C-Mandanten

Die ASP.NET Core-app ist jetzt konfiguriert, um die Azure AD B2C für die Authentifizierung verwenden die [Authorize-Attribut](xref:security/authorization/simple) kann zum Schutz Ihrer app verwendet werden. Fahren Sie fort, entwickeln Ihre app, indem Sie auf:

* [Anpassen der Benutzeroberfläche von Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Konfigurieren Sie die Anforderungen an die Kennwortkomplexität](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Aktivieren von Multi-Factor Authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Konfigurieren zusätzlicher Identitätsanbieter, z. B. [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), und andere.
* [Verwenden Sie die Azure AD Graph-API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) um zusätzliche Benutzerinformationen zu erhalten, wie z. B. die Gruppenmitgliedschaft, aus dem Azure AD B2C-Mandanten abzurufen.
* [Sichern einer ASP.NET Core-Web-API mit Azure AD B2C](xref:security/authentication/azure-ad-b2c-webapi).
* [Rufen Sie eine .NET Web-API aus einer .NET Web-app mithilfe von Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).
