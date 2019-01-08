---
title: Zweistufige Authentifizierung mit SMS in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie die zweistufige Authentifizierung (2FA) mit einer ASP.NET Core-app einrichten.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: seodec18
uid: security/authentication/2fa
ms.openlocfilehash: 48bfc50378fc0ec212f5b9d4e7ce05bb4fc97b9d
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098894"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>Zweistufige Authentifizierung mit SMS in ASP.NET Core

Durch [Rick Anderson](https://twitter.com/RickAndMSFT) und [Schweizer-Entwickler](https://github.com/Swiss-Devs)

>[!WARNING]
> Zwei Faktor-Authentifizierung (2FA) Authentifikator-apps, verwenden eine zeitbasierte Einmalkennwort Kennwort Algorithmus (TOTP), sind der empfohlene Ansatz für 2FA Branche. 2FA TOTP mit SMS 2FA vorzuziehen ist. Weitere Informationen finden Sie unter [QR-Code aktivieren-Generierung für Authentifikator-apps in ASP.NET Core TOTP](xref:security/authentication/identity-enable-qrcodes) für ASP.NET Core 2.0 und höher.

In diesem Tutorial veranschaulicht das Einrichten der zweistufigen Authentifizierung (2FA) mithilfe von SMS. Anweisungen werden vorgestellt, für das [Twilio](https://www.twilio.com/) und [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), aber Sie können alle anderen SMS-Anbieter verwenden. Es wird empfohlen [Kontobestätigung und Kennwortwiederherstellung](xref:security/authentication/accconfirm) vor Beginn dieses Tutorials.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA) [Zum Herunterladen](xref:index#how-to-download-a-sample).

## <a name="create-a-new-aspnet-core-project"></a>Erstellen eines neuen ASP.NET Core-Projekts

Erstellen Sie eine neue ASP.NET Core-Web-app mit dem Namen `Web2FA` mit individuellen Benutzerkonten. Befolgen Sie die Anweisungen in <xref:security/enforcing-ssl> einrichten und die verbindliche Verwendung von HTTPS.

### <a name="create-an-sms-account"></a>Erstellen Sie eine SMS-Dienstkonto

Erstellen Sie eine SMS-Dienstkonto, z. B. aus [Twilio](https://www.twilio.com/) oder [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/). Notieren Sie die Anmeldeinformationen für die Authentifizierung (für Twilio: AccountSid "und" AuthToken, für die ASPSMS: UserKey und Kennwort).

#### <a name="figuring-out-sms-provider-credentials"></a>Ermitteln Anmeldeinformationen für die SMS-Anbieter

**Twilio:** Kopieren Sie auf der Registerkarte Dashboard für Ihr Twilio-Konto die **Konto-SID** und **Authentifizierungstoken**.

**ASPSMS:** Navigieren Sie in Ihren kontoeinstellungen zu **Userkey** und kopieren Sie ihn zusammen mit Ihrer **Kennwort**.

Wir werden später speichern diese Werte sich mit dem Geheimnis-Manager-Tool in den Schlüsseln `SMSAccountIdentification` und `SMSAccountPassword`.

#### <a name="specifying-senderid--originator"></a>Angeben der Absender-ID / Ersteller

**Twilio:** Kopieren Sie Ihre Twilio auf der Registerkarte Zahlen **Telefonnummer**.

**ASPSMS:** Klicken Sie im Menü Urheber entsperren entsperren Sie eine oder mehrere Urheber, oder wählen Sie eine alphanumerische Absender (von allen Netzwerken nicht unterstützt).

Wir werden später speichern Sie diesen Wert mit dem Geheimnis-Manager-Tool im Schlüssel `SMSAccountFrom`.


### <a name="provide-credentials-for-the-sms-service"></a>Geben Sie Anmeldeinformationen für den SMS-Dienst

Wir verwenden die [optionsmuster](xref:fundamentals/configuration/options) auf die Benutzer-Konto und dieser Schlüssel Einstellungen zugreifen.

   * Erstellen Sie eine Klasse zum Abrufen der sicheren SMS-Clientschlüssel. In diesem Beispiel die `SMSoptions` Klasse wird erstellt, der *Services/SMSoptions.cs* Datei.

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

Legen Sie die `SMSAccountIdentification`, `SMSAccountPassword` und `SMSAccountFrom` mit der [Secret-Manager-Tool](xref:security/app-secrets). Zum Beispiel:

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```
* Fügen Sie das NuGet-Paket für den SMS-Anbieter hinzu. Über die Paket-Manager-Konsole (PMC) ausführen:

**Twilio:**
`Install-Package Twilio`

**ASPSMS:**
`Install-Package ASPSMS`


* Fügen Sie Code in die *Services/MessageServices.cs* Datei, die SMS zu aktivieren. Verwenden Sie entweder die Twilio oder Abschnitt ASPSMS:


**Twilio:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>Konfigurieren Sie beim Start verwenden `SMSoptions`

Hinzufügen `SMSoptions` zum Dienstcontainer in die `ConfigureServices` -Methode in der die *"Startup.cs"*:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>Zwei-Faktor-Authentifizierung aktivieren

Öffnen der *Views/Manage/Index.cshtml* Razor-Ansichtsdatei und entfernen, die der Kommentar Zeichen (also kein Markup auskommentiert ist).

## <a name="log-in-with-two-factor-authentication"></a>Melden Sie sich mit einer zweistufigen Authentifizierung

* Die app ausführen und einen neuen Benutzer registrieren

![Web Application-Register-Ansicht in Microsoft Edge öffnen](2fa/_static/login2fa1.png)

* Tippen Sie auf Ihren Benutzernamen, das aktiviert wird, die `Index` Aktionsmethode im Controller der verwalten. Tippen Sie dann auf die Telefonnummer **hinzufügen** Link.

![Verwalten von Ansicht: Tippen Sie auf den Link "hinzufügen"](2fa/_static/login2fa2.png)

* Fügen Sie eine Telefonnummer, die den Überprüfungscode erhalten, und tippen Sie auf **Überprüfungscode senden**.

![Telefonnummer an-Seite hinzufügen](2fa/_static/login2fa3.png)

* Sie erhalten eine Textnachricht mit dem Überprüfungscode. Geben Sie ihn, und tippen Sie auf **senden**

![Überprüfen Sie die Seite "Telefonnummer"](2fa/_static/login2fa4.png)

Wenn Sie keine SMS erhalten, finden Sie unter protokollseite Twilio.

* Die Ansicht "verwalten" zeigt, dass Ihre Telefonnummer wurde erfolgreich hinzugefügt wurde.

![Verwalten von Ansicht – Profiler - Telefonnummer wurde erfolgreich hinzugefügt.](2fa/_static/login2fa5.png)

* Tippen Sie auf **aktivieren** zweistufige Authentifizierung zu aktivieren.

![Verwalten von Ansicht: zwei-Faktor-Authentifizierung aktivieren](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>Die zweistufige Authentifizierung testen

* Melden Sie sich ab.

* Anmelden.

* Das Benutzerkonto verfügt über zwei-Faktor-Authentifizierung aktiviert, müssen Sie die zweite Stufe der Authentifizierung bereitzustellen. In diesem Tutorial haben Sie die Überprüfung per Telefon aktiviert. Die integrierten Vorlagen ermöglichen Ihnen das Einrichten von e-Mail als zweiter authentifizierungsfaktor auch. Sie können die zweite Faktoren für die Authentifizierung wie z. B. QR-Codes einrichten. Tippen Sie auf **übermitteln**.

![Überprüfungscode senden](2fa/_static/login2fa7.png)

* Geben Sie den Code, den Sie in der SMS-Nachricht erhalten.

* Durch Klicken auf die **diesen Browser merken** das Kontrollkästchen schließen Sie 2FA verwenden, melden Sie sich, wenn Sie den gleichen Browser und Geräte verwenden müssen. 2FA aktiviert und auf **diesen Browser merken** erhalten Sie starke 2FA Schutz vor böswilligen Benutzern, die Ihr Konto zugreifen möchten, solange sie keinen Zugriff auf Ihr Gerät haben. Sie können auf allen privaten Geräten dazu, die Sie regelmäßig verwenden. Durch Festlegen von **diesen Browser merken**, Sie erhalten die Erhöhung der Sicherheit der 2FA von Geräten, die Sie nicht regelmäßig verwenden und Sie erhalten die Vorteile nicht auf Ihren eigenen Geräten 2FA durchlaufen haben.

![Überprüfen Sie die Ansicht](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>Sperre zum Schutz vor brute-Force-Angriffen

Kontosperrung wird mit 2FA empfohlen. Sobald ein Benutzer über ein lokales Konto oder ein Konto für soziales Netzwerk anmeldet, wird jeder fehlerhafte Versuch am 2FA gespeichert. Wenn die maximale zugriffsversuchsfehlern erreicht ist, wird der Benutzer gesperrt ist (Standardwert: 5 Minuten Sperrung nach 5 Versuche fehlgeschlagen). Eine erfolgreiche Authentifizierung setzt die Anzahl der fehlgeschlagenen Zugriffe Versuche und setzt die Uhr. Die maximale Anzahl von fehlgeschlagenen Zugriffsversuchen und Dauer der Sperrung kann festgelegt werden, mit [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) und [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan). Der folgende Code konfiguriert kontosperrung nach 10 Minuten nach 10 Versuche nicht erfolgreichen:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

Überprüfen Sie, ob [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) legt `lockoutOnFailure` zu `true`:

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
