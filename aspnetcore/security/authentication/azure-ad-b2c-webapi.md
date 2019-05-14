---
title: Authentifizierung in Web-APIs mit Azure Active Directory B2C in ASP.NET Core
author: camsoper
description: Erfahren Sie, wie Azure Active Directory B2C-Authentifizierung mit ASP.NET Core-Web-API einrichten. Testen Sie die authentifizierte Web-API mit Postman an.
ms.author: casoper
ms.date: 09/21/2018
ms.custom: mvc, seodec18
uid: security/authentication/azure-ad-b2c-webapi
ms.openlocfilehash: 0eb8b533f44a1f72cfc3c4ec5ec060adb37eed6c
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610366"
---
# <a name="authentication-in-web-apis-with-azure-active-directory-b2c-in-aspnet-core"></a>Authentifizierung in Web-APIs mit Azure Active Directory B2C in ASP.NET Core

Von [Cam Soper](https://twitter.com/camsoper)

[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) ist eine Cloudlösung für die Verwaltung von Identität für Web- und mobilen apps. Der Dienst ermöglicht die Authentifizierung für apps, die in der Cloud und lokal gehostet werden. Authentifizierungstypen sind einzelne Konten, Konten sozialer Netzwerke, und Verbundbenutzer Unternehmenskonten zu authentifizieren. Azure AD B2C bietet auch Multi-Factor Authentication mit Minimalkonfiguration.

Azure Active Directory (Azure AD) und Azure AD B2C sind separate Produktangebote. Azure AD-Mandant repräsentiert eine Organisation, auf, während ein Azure AD B2C-Mandanten ist, eine Sammlung von Identitäten, die mit Anwendungen der vertrauenden Seite verwendet werden. Weitere Informationen finden Sie unter [Azure AD B2C: Häufig gestellte Fragen (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).

Da Web-APIs enthalten keine Benutzeroberfläche, können sie nicht den Benutzer an einem sicheren Tokendienst wie Azure AD B2C umleiten. Stattdessen wird die API ein trägertoken, das von der aufrufenden app übergeben bereits den Benutzer mit Azure AD B2C authentifiziert hat. Die API überprüft das Token ohne direkten Benutzereingriff.

In diesem Tutorial erfahren Sie, wie Sie:

> [!div class="checklist"]
> * Erstellen eines Azure Active Directory B2C-Mandanten.
> * Registrieren einer Webs-API in Azure AD B2C.
> * Verwenden Sie Visual Studio zum Erstellen einer Web-API für die Verwendung des Azure AD B2C-Mandanten für die Authentifizierung konfiguriert.
> * Konfigurieren von Richtlinien, die das Verhalten des Azure AD B2C-Mandanten steuern.
> * Verwenden von Postman, eine Web-app zu simulieren, die ein Dialogfeld zur Anmeldung, stellt ein Token abgerufen, und wird verwendet, um eine Anforderung an die Web-API richten.

## <a name="prerequisites"></a>Vorraussetzungen

Die folgenden Voraussetzungen gelten für diese exemplarische Vorgehensweise:

* [Microsoft Azure-Abonnement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)
* [Postman](https://www.getpostman.com/postman)

## <a name="create-the-azure-active-directory-b2c-tenant"></a>Erstellen der Azure Active Directory B2C-Mandanten

Erstellen eines Azure AD B2C-Mandanten [wie in der Dokumentation beschrieben](/azure/active-directory-b2c/active-directory-b2c-get-started). Wenn Sie aufgefordert werden, ist ein Azure-Abonnement des Mandanten zuordnen optional für dieses Tutorial.

## <a name="configure-a-sign-up-or-sign-in-policy"></a>Konfigurieren Sie eine Richtlinie für Registrierung oder Anmeldung

Verwenden Sie die Schritte in der Azure AD B2C-Dokumentation, [erstellen Sie eine Richtlinie für Registrierung oder Anmeldung](/azure/active-directory-b2c/active-directory-b2c-reference-policies#create-a-sign-up-or-sign-in-policy). Benennen Sie die Richtlinie **SiUpIn**.  Verwenden Sie die Beispielwerte finden Sie in der Dokumentation für **Identitätsanbieter**, **Registrierungsattribute**, und **anwendungsansprüche**. Mithilfe der **jetzt ausführen** Schaltfläche, um die Richtlinie zu testen, wie in der Dokumentation beschrieben ist optional.

## <a name="register-the-api-in-azure-ad-b2c"></a>Registrieren der API in Azure AD B2C

In den neu erstellten Azure AD B2C-Mandanten, registrieren Sie Ihre API mit [die Schritte in der Dokumentation](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-api) unter der **Registrieren einer Web-API-** Abschnitt.

Verwenden Sie die folgenden Werte ein:

| Einstellung                       | Wert               | Hinweise                                                                                  |
|-------------------------------|---------------------|----------------------------------------------------------------------------------------|
| **Name**                      | *{API-Name}*        | Geben Sie einen **Namen** für die app, die Ihre app für Kunden beschreibt.                     |
| **Einschließen von Web-app / web-API** | Ja                 |                                                                                        |
| **Impliziten Fluss zulassen**       | Ja                 |                                                                                        |
| **Antwort-URL**                 | `https://localhost` | Antwort-URLs sind Endpunkte, an denen Azure AD B2C, die von Ihrer app angeforderte Token zurückgibt. |
| **App-ID-URI**                | *api*               | Der URI muss nicht in eine physische Adresse aufgelöst werden. Es muss nur eindeutig sein.     |
| **Nativen Client einschließen**     | Nein                  |                                                                                        |

Nachdem die API registriert wurde, wird die Liste der apps und APIs im Mandanten angezeigt. Wählen Sie die API, die bereits registriert wurde. Wählen Sie die **Kopie** Symbol rechts neben der **Anwendungs-ID** Feld, um ihn in die Zwischenablage zu kopieren. Wählen Sie **veröffentlichte Bereiche** und überprüfen Sie den standardmäßigen *"user_impersonation"* Bereich vorhanden ist.

## <a name="create-an-aspnet-core-app-in-visual-studio"></a>Erstellen einer ASP.NET Core-Apps in Visual Studio

Die Visual Studio Web Application-Vorlage kann für die verwenden Sie des Azure AD B2C-Mandantenverwaltungs für die Authentifizierung konfiguriert werden.

In Visual Studio:

1. Erstellen Sie eine neue ASP.NET Core-Webanwendung. 
2. Wählen Sie **Web-API-** aus der Liste der Vorlagen.
3. Wählen Sie die **Authentifizierung ändern** Schaltfläche.

    ![Schaltfläche "Authentifizierung ändern"](./azure-ad-b2c-webapi/change-auth-button.png)

4. In der **Authentifizierung ändern** wählen Sie im Dialogfeld **einzelne Benutzerkonten** > **Herstellen einer Verbindung mit einem vorhandenen Benutzerspeicher in der Cloud**.

    ![Authentifizierung-Dialogfeld "ändern"](./azure-ad-b2c-webapi/change-auth-dialog.png)

5. Füllen Sie das Formular mit den folgenden Werten:

    | Einstellung                       | Wert                                                 |
    |-------------------------------|-------------------------------------------------------|
    | **Domänenname**               | *{der Domänenname Ihres B2C-Mandanten}*                |
    | **Anwendungs-ID**            | *{die Anwendungs-ID aus der Zwischenablage einfügen}*       |
    | **Richtlinie für Registrierung oder Anmeldung** | B2C_1_SiUpIn                                          |

    Wählen Sie **OK** schließen die **Authentifizierung ändern** Dialogfeld. Wählen Sie **OK** zum Erstellen der Web-app.

Visual Studio erstellt die Web-API mit einem Controller mit dem Namen *ValuesController.cs* , hartcodierte Werte für GET-Anforderungen zurückgibt. Die Klasse wird ergänzt, mit der [Authorize-Attribut](xref:security/authorization/simple), sodass die Authentifizierung für alle Anforderungen erforderlich ist.

## <a name="run-the-web-api"></a>Führen Sie die Web-API

Führen Sie die API in Visual Studio. Visual Studio startet einen Browser auf die API Stamm-URL verwiesen wird. Beachten Sie die URL in die Adressleiste ein, und lassen Sie die API im Hintergrund ausgeführt wird.

> [!NOTE]
> Da kein Controller für die Stamm-URL definiert ist, kann Fehler 404 (Seite nicht gefunden) im Browser angezeigt werden. Dabei handelt es sich um ein erwartetes Verhalten.

## <a name="use-postman-to-get-a-token-and-test-the-api"></a>Verwenden von Postman zum Abrufen eines Tokens, und Testen der API

[Postman](https://getpostman.com/postman) ist ein Tool zum Testen von web-APIs. In diesem Tutorial wird simuliert, Postman eine Web-app, die die Web-API im Auftrag des Benutzers zugreift.

### <a name="register-postman-as-a-web-app"></a>Registrieren Sie Postman wird als eine Web-app.

Da Sie Postman über eine Web-app simuliert, die Token aus dem Azure AD B2C-Mandanten erhält, muss es als Web-app im Mandanten registriert werden. Registrieren Sie mithilfe von Postman [die Schritte in der Dokumentation](/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-app) unter der **Registrieren einer WebApp** Abschnitt. Beenden der **erstellen Sie ein Web-app-clientgeheimnis** Abschnitt. Ein clientgeheimnis ist für dieses Tutorial nicht erforderlich. 

Verwenden Sie die folgenden Werte ein:

| Einstellung                       | Wert                            | Hinweise                           |
|-------------------------------|----------------------------------|---------------------------------|
| **Name**                      | Postman                          |                                 |
| **Einschließen von Web-app / web-API** | Ja                              |                                 |
| **Impliziten Fluss zulassen**       | Ja                              |                                 |
| **Antwort-URL**                 | `https://getpostman.com/postman` |                                 |
| **App-ID-URI**                | *{leerlassen des}*                  | Für dieses Tutorial erforderlich nicht. |
| **Nativen Client einschließen**     | Nein                               |                                 |

Die neu registrierte Web-app benötigt die Berechtigung, auf die Web-API im Auftrag des Benutzers zugreifen.  

1. Wählen Sie **Postman** in der Liste der apps, und wählen Sie dann **API-Zugriff** im Menü auf der linken Seite.
1. Wählen Sie **+ hinzufügen**.
1. In der **API auswählen** Dropdownliste, wählen Sie den Namen der Web-API.
1. In der **Bereiche auswählen** Dropdownliste, stellen Sie sicher, alle Bereiche ausgewählt sind.
1. Wählen Sie **Ok**.

Beachten Sie der Postman-app-Anwendungs-ID, da dies erforderlich ist, um ein bearertoken zu erhalten.

### <a name="create-a-postman-request"></a>Erstellen einer Postman-Anforderung

Starten Sie Postman. Standardmäßig Postman zeigt die **neu erstellen** Dialogfeld beim Starten. Wenn das Dialogfeld nicht angezeigt wird, wählen Sie die **+ neu** -Schaltfläche in der oberen linken Ecke.

Von der **neu erstellen** Dialogfeld:

1. Wählen Sie **anfordern**.

    ![Schaltfläche "anfordern"](./azure-ad-b2c-webapi/postman-create-new.png)

2. Geben Sie *Werte abrufen* in die **Anforderungsnamen** Feld.
3. Wählen Sie **+ Create Collection** zum Erstellen einer neuen Auflistung zum Speichern von der Anforderung. Namen für die Sammlung *ASP.NET Core-Tutorials* und wählen Sie dann auf das Häkchen.

    ![Eine neue Sammlung erstellen](./azure-ad-b2c-webapi/postman-create-collection.png)

4. Wählen Sie die **in ASP.NET Core-Tutorials speichern** Schaltfläche.

### <a name="test-the-web-api-without-authentication"></a>Die Web-API ohne Authentifizierung testen

Um sicherzustellen, dass die Web-API eine Authentifizierung erforderlich ist, stellen Sie zunächst eine Anforderung ohne Authentifizierung.

1. In der **Anforderungs-URL eingeben** Geben Sie die URL für `ValuesController`. Die URL entspricht dem im Browser mit angezeigten **-api/Values** angefügt. Beispielsweise `https://localhost:44375/api/values`.
2. Wählen Sie die **senden** Schaltfläche.
3. Beachten Sie, dass der Status der Antwort *401 nicht autorisiert*.

    ![die Antwort 401 nicht autorisiert](./azure-ad-b2c-webapi/postman-401-status.png)

> [!IMPORTANT]
> Wenn Sie die Fehlermeldung "Die Antwort konnte nicht abgerufen werden" erhalten, müssen Sie möglicherweise so deaktivieren Sie die Überprüfung von SSL-Zertifikat in der [Postman Einstellungen](https://learning.getpostman.com/docs/postman/launching_postman/settings).

### <a name="obtain-a-bearer-token"></a>Abrufen eines bearertokens

Um eine authentifizierte Anforderung an die Web-API vornehmen zu können, ist ein trägertoken, das erforderlich. Postman erleichtert es, melden Sie sich an den Azure AD B2C-Mandanten und ein Token abrufen.

1. Auf der **Autorisierung** Registerkarte die **Typ** Dropdownliste wählen **OAuth 2.0**. In der **Autorisierungsdaten hinzufügen** Dropdownliste wählen **Anforderungsheader**. Wählen Sie **Abrufen eines neuen Zugriffstokens**.

    ![Registerkarte "Autorisierung" mit Einstellungen](./azure-ad-b2c-webapi/postman-auth-tab.png)

2. Abschließen der **Abrufen neuer ZUGRIFFSTOKEN** Dialogfeld wie folgt:

   |                Einstellung                 |                                             Wert                                             |                                                                                                                                    Hinweise                                                                                                                                     |
   |----------------------------------------|-----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   |      **Tokenname**       |                                          *{Tokenname}*                                       |                                                                                                                   Geben Sie einen beschreibenden Namen für das Token aus.                                                                                                                    |
   |      **Gewährungstyp**       |                                           Implizit                                            |                                                                                                                                                                                                                                                                              |
   |     **Rückruf-URL**      |                                 `https://getpostman.com/postman`                              |                                                                                                                                                                                                                                                                              |
   |       **Authentifizierungs-URL**        | `https://login.microsoftonline.com/{tenant domain name}/oauth2/v2.0/authorize?p=B2C_1_SiUpIn` |  Ersetzen Sie dies *{Domänenname des Mandanten}* mit Domänennamen des Mandanten. **WICHTIGE**: Diese URL müssen den gleichen Domänennamen an, als was in befindet `AzureAdB2C.Instance` in der Web-API *"appSettings.JSON"* Datei. Siehe Hinweis&dagger;.                                                  |
   |       **Client-ID**       |                *{Geben Sie der Postman-app **Anwendungs-ID**}*                              |                                                                                                                                                                                                                                                                              |
   |         **Bereich**         |         `https://{tenant domain name}/{api}/user_impersonation openid offline_access`       | Ersetzen Sie dies *{Domänenname des Mandanten}* mit Domänennamen des Mandanten. Ersetzen Sie dies *{api}* mit der App-ID-URI haben Sie die Web-API bei der ersten Registrierung (in diesem Fall `api`). Das Muster für die URL lautet: `https://{tenant}.onmicrosoft.com/{api-id-uri}/{scope name}`.         |
   |         **Zustand**         |                                      *{leerlassen des}*                                          |                                                                                                                                                                                                                                                                              |
   | **Die Clientauthentifizierung** |                                Senden von Clientanmeldeinformationen in Text                                |                                                                                                                                                                                                                                                                              |

    > [!NOTE]
    > &dagger; Das Dialogfeld für die Richtlinie Einstellungen im Azure Active Directory B2C-Portal zeigt zwei mögliche URLs: Eine im Format `https://login.microsoftonline.com/`{Mandanten-Domain-Name} / {zusätzliche Pfadinformationen}, und die andere im Format `https://{tenant name}.b2clogin.com/`{Mandanten-Domain-Name} / {zusätzliche Pfadinformationen}. Sie verfügt über **kritische** , die die Domäne finden Sie in in `AzureAdB2C.Instance` in der Web-API *"appSettings.JSON"* Datei entspricht, die in der Web-app verwendet *"appSettings.JSON"* Datei. Dies ist der gleichen Domäne, die für die Authentifizierungs-URL-Feld in Postman verwendet. Beachten Sie, dass Visual Studio verwendet ein etwas anderes URL-Format als was im Portal angezeigt wird. Solange die angegebenen Domänen entsprechen, funktioniert die URL.

3. Wählen Sie die **Token anfordern** Schaltfläche.

4. Postman wird ein neues Fenster mit den Azure AD B2C-Mandanten-Dialogfeld geöffnet. Melden Sie sich mit einem vorhandenen Konto, (wenn es erstellt wurde, testen die Richtlinien), oder wählen Sie **registrieren Sie sich jetzt** um ein neues Konto zu erstellen. Die **haben Ihr Kennwort vergessen?** Link wird verwendet, um ein vergessenes Kennwort zurückzusetzen.

5. Nach erfolgreicher Anmeldung, das Fenster geschlossen wird und die **ZUGRIFFSTOKEN verwalten** Dialogfeld wird angezeigt. Scrollen Sie zu den nach unten, und wählen die **verwenden Token** Schaltfläche.

    ![Wo die Schaltfläche "Token verwenden" zu finden.](./azure-ad-b2c-webapi/postman-access-token.png)

### <a name="test-the-web-api-with-authentication"></a>Testen Sie die Web-API mit Authentifizierung

Wählen Sie die **senden** Schaltfläche, um die Anforderung erneut zu senden. In diesem Fall der Antwortstatus ist *200 OK* und die JSON-Nutzlast wird angezeigt, in der Antwort **Text** Registerkarte.

![Nutzlast und den Erfolg](./azure-ad-b2c-webapi/postman-success.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen eines Azure Active Directory B2C-Mandanten.
> * Registrieren einer Webs-API in Azure AD B2C.
> * Verwenden Sie Visual Studio zum Erstellen einer Web-API für die Verwendung des Azure AD B2C-Mandanten für die Authentifizierung konfiguriert.
> * Konfigurieren von Richtlinien, die das Verhalten des Azure AD B2C-Mandanten steuern.
> * Verwenden von Postman, eine Web-app zu simulieren, die ein Dialogfeld zur Anmeldung, stellt ein Token abgerufen, und wird verwendet, um eine Anforderung an die Web-API richten.

Fahren Sie fort, entwickeln Ihre API, indem Sie auf:

* [Sichern einer ASP.NET Core-Web-app mit Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* [Rufen Sie eine .NET Web-API aus einer .NET Web-app mithilfe von Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-web-api-dotnet).
* [Anpassen der Benutzeroberfläche von Azure AD B2C](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).
* [Konfigurieren Sie die Anforderungen an die Kennwortkomplexität](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).
* [Aktivieren von Multi-Factor Authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).
* Konfigurieren zusätzlicher Identitätsanbieter, z. B. [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter ](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), und andere.
* [Verwenden Sie die Azure AD Graph-API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) um zusätzliche Benutzerinformationen zu erhalten, wie z. B. die Gruppenmitgliedschaft, aus dem Azure AD B2C-Mandanten abzurufen.
