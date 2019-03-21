---
title: Zweistufige Authentifizierung mit SMS in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie die zweistufige Authentifizierung (2FA) mit einer ASP.NET Core-app einrichten.
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: seodec18
uid: security/authentication/2fa
ms.openlocfilehash: 116249a7cd4faebd0c899e383d86f5c5c3c7146a
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265242"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="c081f-103">Zweistufige Authentifizierung mit SMS in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c081f-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="c081f-104">Durch [Rick Anderson](https://twitter.com/RickAndMSFT) und [Schweizer-Entwickler](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="c081f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="c081f-105">Zwei Faktor-Authentifizierung (2FA) Authentifikator-apps, verwenden eine zeitbasierte Einmalkennwort Kennwort Algorithmus (TOTP), sind der empfohlene Ansatz für 2FA Branche.</span><span class="sxs-lookup"><span data-stu-id="c081f-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="c081f-106">2FA TOTP mit SMS 2FA vorzuziehen ist.</span><span class="sxs-lookup"><span data-stu-id="c081f-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="c081f-107">Weitere Informationen finden Sie unter [QR-Code aktivieren-Generierung für Authentifikator-apps in ASP.NET Core TOTP](xref:security/authentication/identity-enable-qrcodes) für ASP.NET Core 2.0 und höher.</span><span class="sxs-lookup"><span data-stu-id="c081f-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="c081f-108">In diesem Tutorial veranschaulicht das Einrichten der zweistufigen Authentifizierung (2FA) mithilfe von SMS.</span><span class="sxs-lookup"><span data-stu-id="c081f-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="c081f-109">Anweisungen werden vorgestellt, für das [Twilio](https://www.twilio.com/) und [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), aber Sie können alle anderen SMS-Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="c081f-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="c081f-110">Es wird empfohlen [Kontobestätigung und Kennwortwiederherstellung](xref:security/authentication/accconfirm) vor Beginn dieses Tutorials.</span><span class="sxs-lookup"><span data-stu-id="c081f-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="c081f-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA)</span><span class="sxs-lookup"><span data-stu-id="c081f-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="c081f-112">[Zum Herunterladen](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="c081f-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="c081f-113">Erstellen eines neuen ASP.NET Core-Projekts</span><span class="sxs-lookup"><span data-stu-id="c081f-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="c081f-114">Erstellen Sie eine neue ASP.NET Core-Web-app mit dem Namen `Web2FA` mit individuellen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="c081f-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="c081f-115">Befolgen Sie die Anweisungen in <xref:security/enforcing-ssl> einrichten und die verbindliche Verwendung von HTTPS.</span><span class="sxs-lookup"><span data-stu-id="c081f-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="c081f-116">Erstellen Sie eine SMS-Dienstkonto</span><span class="sxs-lookup"><span data-stu-id="c081f-116">Create an SMS account</span></span>

<span data-ttu-id="c081f-117">Erstellen Sie eine SMS-Dienstkonto, z. B. aus [Twilio](https://www.twilio.com/) oder [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span><span class="sxs-lookup"><span data-stu-id="c081f-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="c081f-118">Notieren Sie die Anmeldeinformationen für die Authentifizierung (für Twilio: AccountSid "und" AuthToken, für die ASPSMS: UserKey und Kennwort).</span><span class="sxs-lookup"><span data-stu-id="c081f-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="c081f-119">Ermitteln Anmeldeinformationen für die SMS-Anbieter</span><span class="sxs-lookup"><span data-stu-id="c081f-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="c081f-120">**Twilio:**</span><span class="sxs-lookup"><span data-stu-id="c081f-120">**Twilio:**</span></span>

<span data-ttu-id="c081f-121">Kopieren Sie auf der Registerkarte Dashboard für Ihr Twilio-Konto die **Konto-SID** und **Authentifizierungstoken**.</span><span class="sxs-lookup"><span data-stu-id="c081f-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="c081f-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="c081f-122">**ASPSMS:**</span></span>

<span data-ttu-id="c081f-123">Navigieren Sie in Ihren kontoeinstellungen zu **Userkey** und kopieren Sie ihn zusammen mit Ihrer **Kennwort**.</span><span class="sxs-lookup"><span data-stu-id="c081f-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="c081f-124">Wir werden später speichern diese Werte sich mit dem Geheimnis-Manager-Tool in den Schlüsseln `SMSAccountIdentification` und `SMSAccountPassword`.</span><span class="sxs-lookup"><span data-stu-id="c081f-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="c081f-125">Angeben der Absender-ID / Ersteller</span><span class="sxs-lookup"><span data-stu-id="c081f-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="c081f-126">**Twilio:** Kopieren Sie Ihre Twilio auf der Registerkarte Zahlen **Telefonnummer**.</span><span class="sxs-lookup"><span data-stu-id="c081f-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="c081f-127">**ASPSMS:** Klicken Sie im Menü Urheber entsperren entsperren Sie eine oder mehrere Urheber, oder wählen Sie eine alphanumerische Absender (von allen Netzwerken nicht unterstützt).</span><span class="sxs-lookup"><span data-stu-id="c081f-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="c081f-128">Wir werden später speichern Sie diesen Wert mit dem Geheimnis-Manager-Tool im Schlüssel `SMSAccountFrom`.</span><span class="sxs-lookup"><span data-stu-id="c081f-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="c081f-129">Geben Sie Anmeldeinformationen für den SMS-Dienst</span><span class="sxs-lookup"><span data-stu-id="c081f-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="c081f-130">Wir verwenden die [optionsmuster](xref:fundamentals/configuration/options) auf die Benutzer-Konto und dieser Schlüssel Einstellungen zugreifen.</span><span class="sxs-lookup"><span data-stu-id="c081f-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="c081f-131">Erstellen Sie eine Klasse zum Abrufen der sicheren SMS-Clientschlüssel.</span><span class="sxs-lookup"><span data-stu-id="c081f-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="c081f-132">In diesem Beispiel die `SMSoptions` Klasse wird erstellt, der *Services/SMSoptions.cs* Datei.</span><span class="sxs-lookup"><span data-stu-id="c081f-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="c081f-133">Legen Sie die `SMSAccountIdentification`, `SMSAccountPassword` und `SMSAccountFrom` mit der [Secret-Manager-Tool](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="c081f-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="c081f-134">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="c081f-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="c081f-135">Fügen Sie das NuGet-Paket für den SMS-Anbieter hinzu.</span><span class="sxs-lookup"><span data-stu-id="c081f-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="c081f-136">Über die Paket-Manager-Konsole (PMC) ausführen:</span><span class="sxs-lookup"><span data-stu-id="c081f-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="c081f-137">**Twilio:**</span><span class="sxs-lookup"><span data-stu-id="c081f-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="c081f-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="c081f-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="c081f-139">Fügen Sie Code in die *Services/MessageServices.cs* Datei, die SMS zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="c081f-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="c081f-140">Verwenden Sie entweder die Twilio oder Abschnitt ASPSMS:</span><span class="sxs-lookup"><span data-stu-id="c081f-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="c081f-141">**Twilio:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]</span><span class="sxs-lookup"><span data-stu-id="c081f-141">**Twilio:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]</span></span>

<span data-ttu-id="c081f-142">**ASPSMS:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]</span><span class="sxs-lookup"><span data-stu-id="c081f-142">**ASPSMS:** [!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]</span></span>

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="c081f-143">Konfigurieren Sie beim Start verwenden `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="c081f-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="c081f-144">Hinzufügen `SMSoptions` zum Dienstcontainer in die `ConfigureServices` -Methode in der die *"Startup.cs"*:</span><span class="sxs-lookup"><span data-stu-id="c081f-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="c081f-145">Zwei-Faktor-Authentifizierung aktivieren</span><span class="sxs-lookup"><span data-stu-id="c081f-145">Enable two-factor authentication</span></span>

<span data-ttu-id="c081f-146">Öffnen der *Views/Manage/Index.cshtml* Razor-Ansichtsdatei und entfernen, die der Kommentar Zeichen (also kein Markup auskommentiert ist).</span><span class="sxs-lookup"><span data-stu-id="c081f-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="c081f-147">Melden Sie sich mit einer zweistufigen Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c081f-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="c081f-148">Die app ausführen und einen neuen Benutzer registrieren</span><span class="sxs-lookup"><span data-stu-id="c081f-148">Run the app and register a new user</span></span>

![Web Application-Register-Ansicht in Microsoft Edge öffnen](2fa/_static/login2fa1.png)

* <span data-ttu-id="c081f-150">Tippen Sie auf Ihren Benutzernamen, das aktiviert wird, die `Index` Aktionsmethode im Controller der verwalten.</span><span class="sxs-lookup"><span data-stu-id="c081f-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="c081f-151">Tippen Sie dann auf die Telefonnummer **hinzufügen** Link.</span><span class="sxs-lookup"><span data-stu-id="c081f-151">Then tap the phone number **Add** link.</span></span>

![Verwalten von Ansicht: Tippen Sie auf den Link "hinzufügen"](2fa/_static/login2fa2.png)

* <span data-ttu-id="c081f-153">Fügen Sie eine Telefonnummer, die den Überprüfungscode erhalten, und tippen Sie auf **Überprüfungscode senden**.</span><span class="sxs-lookup"><span data-stu-id="c081f-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![Telefonnummer an-Seite hinzufügen](2fa/_static/login2fa3.png)

* <span data-ttu-id="c081f-155">Sie erhalten eine Textnachricht mit dem Überprüfungscode.</span><span class="sxs-lookup"><span data-stu-id="c081f-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="c081f-156">Geben Sie ihn, und tippen Sie auf **senden**</span><span class="sxs-lookup"><span data-stu-id="c081f-156">Enter it and tap **Submit**</span></span>

![Überprüfen Sie die Seite "Telefonnummer"](2fa/_static/login2fa4.png)

<span data-ttu-id="c081f-158">Wenn Sie keine SMS erhalten, finden Sie unter protokollseite Twilio.</span><span class="sxs-lookup"><span data-stu-id="c081f-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="c081f-159">Die Ansicht "verwalten" zeigt, dass Ihre Telefonnummer wurde erfolgreich hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="c081f-159">The Manage view shows your phone number was added successfully.</span></span>

![Verwalten von Ansicht – Profiler - Telefonnummer wurde erfolgreich hinzugefügt.](2fa/_static/login2fa5.png)

* <span data-ttu-id="c081f-161">Tippen Sie auf **aktivieren** zweistufige Authentifizierung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="c081f-161">Tap **Enable** to enable two-factor authentication.</span></span>

![Verwalten von Ansicht: zwei-Faktor-Authentifizierung aktivieren](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="c081f-163">Die zweistufige Authentifizierung testen</span><span class="sxs-lookup"><span data-stu-id="c081f-163">Test two-factor authentication</span></span>

* <span data-ttu-id="c081f-164">Melden Sie sich ab.</span><span class="sxs-lookup"><span data-stu-id="c081f-164">Log off.</span></span>

* <span data-ttu-id="c081f-165">Anmelden.</span><span class="sxs-lookup"><span data-stu-id="c081f-165">Log in.</span></span>

* <span data-ttu-id="c081f-166">Das Benutzerkonto verfügt über zwei-Faktor-Authentifizierung aktiviert, müssen Sie die zweite Stufe der Authentifizierung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c081f-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="c081f-167">In diesem Tutorial haben Sie die Überprüfung per Telefon aktiviert.</span><span class="sxs-lookup"><span data-stu-id="c081f-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="c081f-168">Die integrierten Vorlagen ermöglichen Ihnen das Einrichten von e-Mail als zweiter authentifizierungsfaktor auch.</span><span class="sxs-lookup"><span data-stu-id="c081f-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="c081f-169">Sie können die zweite Faktoren für die Authentifizierung wie z. B. QR-Codes einrichten.</span><span class="sxs-lookup"><span data-stu-id="c081f-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="c081f-170">Tippen Sie auf **übermitteln**.</span><span class="sxs-lookup"><span data-stu-id="c081f-170">Tap **Submit**.</span></span>

![Überprüfungscode senden](2fa/_static/login2fa7.png)

* <span data-ttu-id="c081f-172">Geben Sie den Code, den Sie in der SMS-Nachricht erhalten.</span><span class="sxs-lookup"><span data-stu-id="c081f-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="c081f-173">Durch Klicken auf die **diesen Browser merken** das Kontrollkästchen schließen Sie 2FA verwenden, melden Sie sich, wenn Sie den gleichen Browser und Geräte verwenden müssen.</span><span class="sxs-lookup"><span data-stu-id="c081f-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="c081f-174">2FA aktiviert und auf **diesen Browser merken** erhalten Sie starke 2FA Schutz vor böswilligen Benutzern, die Ihr Konto zugreifen möchten, solange sie keinen Zugriff auf Ihr Gerät haben.</span><span class="sxs-lookup"><span data-stu-id="c081f-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="c081f-175">Sie können auf allen privaten Geräten dazu, die Sie regelmäßig verwenden.</span><span class="sxs-lookup"><span data-stu-id="c081f-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="c081f-176">Durch Festlegen von **diesen Browser merken**, Sie erhalten die Erhöhung der Sicherheit der 2FA von Geräten, die Sie nicht regelmäßig verwenden und Sie erhalten die Vorteile nicht auf Ihren eigenen Geräten 2FA durchlaufen haben.</span><span class="sxs-lookup"><span data-stu-id="c081f-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![Überprüfen Sie die Ansicht](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="c081f-178">Sperre zum Schutz vor brute-Force-Angriffen</span><span class="sxs-lookup"><span data-stu-id="c081f-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="c081f-179">Kontosperrung wird mit 2FA empfohlen.</span><span class="sxs-lookup"><span data-stu-id="c081f-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="c081f-180">Sobald ein Benutzer über ein lokales Konto oder ein Konto für soziales Netzwerk anmeldet, wird jeder fehlerhafte Versuch am 2FA gespeichert.</span><span class="sxs-lookup"><span data-stu-id="c081f-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="c081f-181">Wenn die maximale zugriffsversuchsfehlern erreicht ist, wird der Benutzer gesperrt ist (Standardwert: 5 Minuten Sperrung nach 5 Versuche fehlgeschlagen).</span><span class="sxs-lookup"><span data-stu-id="c081f-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="c081f-182">Eine erfolgreiche Authentifizierung setzt die Anzahl der fehlgeschlagenen Zugriffe Versuche und setzt die Uhr.</span><span class="sxs-lookup"><span data-stu-id="c081f-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="c081f-183">Die maximale Anzahl von fehlgeschlagenen Zugriffsversuchen und Dauer der Sperrung kann festgelegt werden, mit [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) und [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span><span class="sxs-lookup"><span data-stu-id="c081f-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="c081f-184">Der folgende Code konfiguriert kontosperrung nach 10 Minuten nach 10 Versuche nicht erfolgreichen:</span><span class="sxs-lookup"><span data-stu-id="c081f-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="c081f-185">Überprüfen Sie, ob [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) legt `lockoutOnFailure` zu `true`:</span><span class="sxs-lookup"><span data-stu-id="c081f-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
