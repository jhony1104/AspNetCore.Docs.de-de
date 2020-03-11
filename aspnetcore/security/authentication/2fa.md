---
title: Zweistufige Authentifizierung mit SMS in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie die zweistufige Authentifizierung (2FA) mit einer ASP.NET Core-app einrichten.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
uid: security/authentication/2fa
ms.openlocfilehash: 424d21e446de02b39daa7685205a00931361b326
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652723"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>Zweistufige Authentifizierung mit SMS in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Schweizer-Entwickler](https://github.com/Swiss-Devs)

>[!WARNING]
> Zwei Faktor-Authentifizierung (2FA) Authentifikator-apps, verwenden eine zeitbasierte Einmalkennwort Kennwort Algorithmus (TOTP), sind der empfohlene Ansatz für 2FA Branche. 2FA TOTP mit SMS 2FA vorzuziehen ist. Weitere Informationen finden Sie unter [Aktivieren der QR-Code Generierung für TOTP Authenticator-apps in ASP.net Core](xref:security/authentication/identity-enable-qrcodes) für ASP.net Core 2,0 und höher.

In diesem Tutorial veranschaulicht das Einrichten der zweistufigen Authentifizierung (2FA) mithilfe von SMS. Für [twilio](https://www.twilio.com/) und [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)werden Anweisungen erteilt, aber Sie können auch einen beliebigen anderen SMS-Anbieter verwenden. Bevor Sie mit diesem Tutorial beginnen, wird empfohlen, die [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm) abzuschließen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA) [Herunterladen von](xref:index#how-to-download-a-sample)

## <a name="create-a-new-aspnet-core-project"></a>Erstellen eines neuen ASP.NET Core-Projekts

Erstellen Sie eine neue ASP.net Core Web-App mit dem Namen `Web2FA` mit einzelnen Benutzerkonten. Befolgen Sie die Anweisungen unter <xref:security/enforcing-ssl>, um HTTPS einzurichten und anzufordern.

### <a name="create-an-sms-account"></a>Erstellen Sie eine SMS-Dienstkonto

Erstellen Sie ein SMS-Konto, z. b. aus [twilio](https://www.twilio.com/) oder [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/). Notieren Sie die Anmeldeinformationen für die Authentifizierung (für Twilio: AccountSid "und" AuthToken, für die ASPSMS: Userkey und ein Kennwort).

#### <a name="figuring-out-sms-provider-credentials"></a>Ermitteln Anmeldeinformationen für die SMS-Anbieter

**Twilio**

Kopieren Sie die **Konto-SID** und das Authentifizierungs **Token**auf der Registerkarte Dashboard Ihres twilio-Kontos.

**Aspsms:**

Navigieren Sie in Ihren Kontoeinstellungen zu **UserKey** , und kopieren Sie es mit Ihrem **Kennwort**.

Diese Werte werden später in mit dem Secret-Manager-Tool in den Schlüsseln `SMSAccountIdentification` und `SMSAccountPassword`gespeichert.

#### <a name="specifying-senderid--originator"></a>Angeben der Absender-ID / Ersteller

**Twilio:** Kopieren Sie Ihre twilio- **Telefonnummer**auf der Registerkarte Zahlen.

**Aspsms:** Entsperren Sie einen oder mehrere Originatoren im Menü Unlock-Absender, oder wählen Sie einen alphanumerischen Absender aus (wird nicht von allen Netzwerken unterstützt).

Dieser Wert wird später mit dem Secret-Manager-Tool im Schlüssel `SMSAccountFrom`gespeichert.

### <a name="provide-credentials-for-the-sms-service"></a>Geben Sie Anmeldeinformationen für den SMS-Dienst

Wir verwenden das [options Muster](xref:fundamentals/configuration/options) für den Zugriff auf das Benutzerkonto und die Schlüssel Einstellungen.

* Erstellen Sie eine Klasse zum Abrufen der sicheren SMS-Clientschlüssel. In diesem Beispiel wird die `SMSoptions`-Klasse in der Datei *Services/smsoptions. cs* erstellt.

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

Legen Sie die `SMSAccountIdentification`, `SMSAccountPassword` und `SMSAccountFrom` mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets)fest. Beispiel:

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* Fügen Sie das NuGet-Paket für den SMS-Anbieter hinzu. Über die Paket-Manager-Konsole (PMC) ausführen:

**Twilio**

`Install-Package Twilio`

**Aspsms:**

`Install-Package ASPSMS`

* Fügen Sie Code in der Datei " *Services/messageservices. cs* " hinzu, um SMS zu aktivieren. Verwenden Sie entweder die Twilio oder Abschnitt ASPSMS:

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**Aspsms:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>Konfigurieren des Starts für die Verwendung `SMSoptions`

Fügen Sie dem Dienst Container in der `ConfigureServices`-Methode in der *Startup.cs*-Methode `SMSoptions` hinzu:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>Zwei-Faktor-Authentifizierung aktivieren

Öffnen Sie die Razor-Ansichts Datei *views/Manage/Index. cshtml* , und entfernen Sie die Kommentarzeichen (sodass kein Markup auskommentiert ist).

## <a name="log-in-with-two-factor-authentication"></a>Melden Sie sich mit einer zweistufigen Authentifizierung

* Die app ausführen und einen neuen Benutzer registrieren

![Web Application-Register-Ansicht in Microsoft Edge öffnen](2fa/_static/login2fa1.png)

* Tippen Sie auf Ihren Benutzernamen, der die `Index` Aktionsmethode in Manage Controller aktiviert. Tippen Sie dann auf den Link Telefonnummer **Hinzufügen** .

![Verwalten von Ansicht: Tippen Sie auf den Link "hinzufügen"](2fa/_static/login2fa2.png)

* Fügen Sie eine Telefonnummer hinzu, die den Überprüfungs Code empfängt, und tippen **Sie auf Überprüfungs Code senden**.

![Telefonnummer an-Seite hinzufügen](2fa/_static/login2fa3.png)

* Sie erhalten eine Textnachricht mit dem Überprüfungscode. EINGABETASTE

![Überprüfen Sie die Seite "Telefonnummer"](2fa/_static/login2fa4.png)

Wenn Sie keine SMS erhalten, finden Sie unter protokollseite Twilio.

* Die Ansicht "verwalten" zeigt, dass Ihre Telefonnummer wurde erfolgreich hinzugefügt wurde.

![Verwalten von Ansicht – Profiler - Telefonnummer wurde erfolgreich hinzugefügt.](2fa/_static/login2fa5.png)

* Tippen Sie auf **aktivieren** , um die zweistufige Authentifizierung zu aktivieren.

![Verwalten von Ansicht: zwei-Faktor-Authentifizierung aktivieren](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>Die zweistufige Authentifizierung testen

* Melden Sie sich ab.

* Melden Sie sich an.

* Das Benutzerkonto verfügt über zwei-Faktor-Authentifizierung aktiviert, müssen Sie die zweite Stufe der Authentifizierung bereitzustellen. In diesem Tutorial haben Sie die Überprüfung per Telefon aktiviert. Die integrierten Vorlagen ermöglichen Ihnen das Einrichten von e-Mail als zweiter authentifizierungsfaktor auch. Sie können die zweite Faktoren für die Authentifizierung wie z. B. QR-Codes einrichten. Tippen Sie auf **senden**.

![Überprüfungscode senden](2fa/_static/login2fa7.png)

* Geben Sie den Code, den Sie in der SMS-Nachricht erhalten.

* Wenn Sie auf das Kontrollkästchen **diesen Browser speichern** klicken, werden Sie von der Verwendung von 2FA zum Anmelden aufgefordert, wenn Sie das gleiche Gerät und den gleichen Browser verwenden. Durch das Aktivieren von 2FA und das Klicken auf **Speichern dieses Browsers** erhalten Sie einen starken 2FA-Schutz vor böswilligen Benutzern, die versuchen, auf Ihr Konto zuzugreifen, solange Sie keinen Zugriff auf Ihr Gerät haben. Sie können auf allen privaten Geräten dazu, die Sie regelmäßig verwenden. Wenn Sie **diesen Browser merken**, erhalten Sie die zusätzliche Sicherheit von 2FA von Geräten, die Sie nicht regelmäßig verwenden, und Sie erhalten die Möglichkeit, die 2FA nicht auf Ihren eigenen Geräten zu durchlaufen.

![Überprüfen Sie die Ansicht](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>Sperre zum Schutz vor brute-Force-Angriffen

Kontosperrung wird mit 2FA empfohlen. Sobald ein Benutzer über ein lokales Konto oder ein Konto für soziales Netzwerk anmeldet, wird jeder fehlerhafte Versuch am 2FA gespeichert. Wenn die maximale zugriffsversuchsfehlern erreicht ist, wird der Benutzer gesperrt ist (Standardwert: 5-minütige Sperre nach 5 Versuche fehlgeschlagen). Eine erfolgreiche Authentifizierung setzt die Anzahl der fehlgeschlagenen Zugriffe Versuche und setzt die Uhr. Die maximalen fehlgeschlagenen Zugriffsversuche und Sperr Zeit können mit " [maxfailedaccessversuchs](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) " und " [defaultlockouttimespan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)" festgelegt werden. Der folgende Code konfiguriert kontosperrung nach 10 Minuten nach 10 Versuche nicht erfolgreichen:

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

Vergewissern Sie sich, dass [passwordsigninasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) `lockoutOnFailure` auf `true`festlegt:

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
