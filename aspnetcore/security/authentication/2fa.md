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
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="551aa-103">Zweistufige Authentifizierung mit SMS in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="551aa-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="551aa-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Schweizer-Entwickler](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="551aa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="551aa-105">Zwei Faktor-Authentifizierung (2FA) Authentifikator-apps, verwenden eine zeitbasierte Einmalkennwort Kennwort Algorithmus (TOTP), sind der empfohlene Ansatz für 2FA Branche.</span><span class="sxs-lookup"><span data-stu-id="551aa-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="551aa-106">2FA TOTP mit SMS 2FA vorzuziehen ist.</span><span class="sxs-lookup"><span data-stu-id="551aa-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="551aa-107">Weitere Informationen finden Sie unter [Aktivieren der QR-Code Generierung für TOTP Authenticator-apps in ASP.net Core](xref:security/authentication/identity-enable-qrcodes) für ASP.net Core 2,0 und höher.</span><span class="sxs-lookup"><span data-stu-id="551aa-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="551aa-108">In diesem Tutorial veranschaulicht das Einrichten der zweistufigen Authentifizierung (2FA) mithilfe von SMS.</span><span class="sxs-lookup"><span data-stu-id="551aa-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="551aa-109">Für [twilio](https://www.twilio.com/) und [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)werden Anweisungen erteilt, aber Sie können auch einen beliebigen anderen SMS-Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="551aa-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="551aa-110">Bevor Sie mit diesem Tutorial beginnen, wird empfohlen, die [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm) abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="551aa-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="551aa-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA)</span><span class="sxs-lookup"><span data-stu-id="551aa-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="551aa-112">[Herunterladen von](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="551aa-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="551aa-113">Erstellen eines neuen ASP.NET Core-Projekts</span><span class="sxs-lookup"><span data-stu-id="551aa-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="551aa-114">Erstellen Sie eine neue ASP.net Core Web-App mit dem Namen `Web2FA` mit einzelnen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="551aa-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="551aa-115">Befolgen Sie die Anweisungen unter <xref:security/enforcing-ssl>, um HTTPS einzurichten und anzufordern.</span><span class="sxs-lookup"><span data-stu-id="551aa-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="551aa-116">Erstellen Sie eine SMS-Dienstkonto</span><span class="sxs-lookup"><span data-stu-id="551aa-116">Create an SMS account</span></span>

<span data-ttu-id="551aa-117">Erstellen Sie ein SMS-Konto, z. b. aus [twilio](https://www.twilio.com/) oder [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span><span class="sxs-lookup"><span data-stu-id="551aa-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="551aa-118">Notieren Sie die Anmeldeinformationen für die Authentifizierung (für Twilio: AccountSid "und" AuthToken, für die ASPSMS: Userkey und ein Kennwort).</span><span class="sxs-lookup"><span data-stu-id="551aa-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="551aa-119">Ermitteln Anmeldeinformationen für die SMS-Anbieter</span><span class="sxs-lookup"><span data-stu-id="551aa-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="551aa-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="551aa-120">**Twilio:**</span></span>

<span data-ttu-id="551aa-121">Kopieren Sie die **Konto-SID** und das Authentifizierungs **Token**auf der Registerkarte Dashboard Ihres twilio-Kontos.</span><span class="sxs-lookup"><span data-stu-id="551aa-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="551aa-122">**Aspsms:**</span><span class="sxs-lookup"><span data-stu-id="551aa-122">**ASPSMS:**</span></span>

<span data-ttu-id="551aa-123">Navigieren Sie in Ihren Kontoeinstellungen zu **UserKey** , und kopieren Sie es mit Ihrem **Kennwort**.</span><span class="sxs-lookup"><span data-stu-id="551aa-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="551aa-124">Diese Werte werden später in mit dem Secret-Manager-Tool in den Schlüsseln `SMSAccountIdentification` und `SMSAccountPassword`gespeichert.</span><span class="sxs-lookup"><span data-stu-id="551aa-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="551aa-125">Angeben der Absender-ID / Ersteller</span><span class="sxs-lookup"><span data-stu-id="551aa-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="551aa-126">**Twilio:** Kopieren Sie Ihre twilio- **Telefonnummer**auf der Registerkarte Zahlen.</span><span class="sxs-lookup"><span data-stu-id="551aa-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="551aa-127">**Aspsms:** Entsperren Sie einen oder mehrere Originatoren im Menü Unlock-Absender, oder wählen Sie einen alphanumerischen Absender aus (wird nicht von allen Netzwerken unterstützt).</span><span class="sxs-lookup"><span data-stu-id="551aa-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="551aa-128">Dieser Wert wird später mit dem Secret-Manager-Tool im Schlüssel `SMSAccountFrom`gespeichert.</span><span class="sxs-lookup"><span data-stu-id="551aa-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="551aa-129">Geben Sie Anmeldeinformationen für den SMS-Dienst</span><span class="sxs-lookup"><span data-stu-id="551aa-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="551aa-130">Wir verwenden das [options Muster](xref:fundamentals/configuration/options) für den Zugriff auf das Benutzerkonto und die Schlüssel Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="551aa-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="551aa-131">Erstellen Sie eine Klasse zum Abrufen der sicheren SMS-Clientschlüssel.</span><span class="sxs-lookup"><span data-stu-id="551aa-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="551aa-132">In diesem Beispiel wird die `SMSoptions`-Klasse in der Datei *Services/smsoptions. cs* erstellt.</span><span class="sxs-lookup"><span data-stu-id="551aa-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="551aa-133">Legen Sie die `SMSAccountIdentification`, `SMSAccountPassword` und `SMSAccountFrom` mit dem [Geheimnis-Manager-Tool](xref:security/app-secrets)fest.</span><span class="sxs-lookup"><span data-stu-id="551aa-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="551aa-134">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="551aa-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="551aa-135">Fügen Sie das NuGet-Paket für den SMS-Anbieter hinzu.</span><span class="sxs-lookup"><span data-stu-id="551aa-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="551aa-136">Über die Paket-Manager-Konsole (PMC) ausführen:</span><span class="sxs-lookup"><span data-stu-id="551aa-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="551aa-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="551aa-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="551aa-138">**Aspsms:**</span><span class="sxs-lookup"><span data-stu-id="551aa-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="551aa-139">Fügen Sie Code in der Datei " *Services/messageservices. cs* " hinzu, um SMS zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="551aa-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="551aa-140">Verwenden Sie entweder die Twilio oder Abschnitt ASPSMS:</span><span class="sxs-lookup"><span data-stu-id="551aa-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="551aa-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="551aa-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="551aa-142">**Aspsms:**</span><span class="sxs-lookup"><span data-stu-id="551aa-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="551aa-143">Konfigurieren des Starts für die Verwendung `SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="551aa-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="551aa-144">Fügen Sie dem Dienst Container in der `ConfigureServices`-Methode in der *Startup.cs*-Methode `SMSoptions` hinzu:</span><span class="sxs-lookup"><span data-stu-id="551aa-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="551aa-145">Zwei-Faktor-Authentifizierung aktivieren</span><span class="sxs-lookup"><span data-stu-id="551aa-145">Enable two-factor authentication</span></span>

<span data-ttu-id="551aa-146">Öffnen Sie die Razor-Ansichts Datei *views/Manage/Index. cshtml* , und entfernen Sie die Kommentarzeichen (sodass kein Markup auskommentiert ist).</span><span class="sxs-lookup"><span data-stu-id="551aa-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="551aa-147">Melden Sie sich mit einer zweistufigen Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="551aa-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="551aa-148">Die app ausführen und einen neuen Benutzer registrieren</span><span class="sxs-lookup"><span data-stu-id="551aa-148">Run the app and register a new user</span></span>

![Web Application-Register-Ansicht in Microsoft Edge öffnen](2fa/_static/login2fa1.png)

* <span data-ttu-id="551aa-150">Tippen Sie auf Ihren Benutzernamen, der die `Index` Aktionsmethode in Manage Controller aktiviert.</span><span class="sxs-lookup"><span data-stu-id="551aa-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="551aa-151">Tippen Sie dann auf den Link Telefonnummer **Hinzufügen** .</span><span class="sxs-lookup"><span data-stu-id="551aa-151">Then tap the phone number **Add** link.</span></span>

![Verwalten von Ansicht: Tippen Sie auf den Link "hinzufügen"](2fa/_static/login2fa2.png)

* <span data-ttu-id="551aa-153">Fügen Sie eine Telefonnummer hinzu, die den Überprüfungs Code empfängt, und tippen **Sie auf Überprüfungs Code senden**.</span><span class="sxs-lookup"><span data-stu-id="551aa-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![Telefonnummer an-Seite hinzufügen](2fa/_static/login2fa3.png)

* <span data-ttu-id="551aa-155">Sie erhalten eine Textnachricht mit dem Überprüfungscode.</span><span class="sxs-lookup"><span data-stu-id="551aa-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="551aa-156">EINGABETASTE</span><span class="sxs-lookup"><span data-stu-id="551aa-156">Enter it and tap **Submit**</span></span>

![Überprüfen Sie die Seite "Telefonnummer"](2fa/_static/login2fa4.png)

<span data-ttu-id="551aa-158">Wenn Sie keine SMS erhalten, finden Sie unter protokollseite Twilio.</span><span class="sxs-lookup"><span data-stu-id="551aa-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="551aa-159">Die Ansicht "verwalten" zeigt, dass Ihre Telefonnummer wurde erfolgreich hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="551aa-159">The Manage view shows your phone number was added successfully.</span></span>

![Verwalten von Ansicht – Profiler - Telefonnummer wurde erfolgreich hinzugefügt.](2fa/_static/login2fa5.png)

* <span data-ttu-id="551aa-161">Tippen Sie auf **aktivieren** , um die zweistufige Authentifizierung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="551aa-161">Tap **Enable** to enable two-factor authentication.</span></span>

![Verwalten von Ansicht: zwei-Faktor-Authentifizierung aktivieren](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="551aa-163">Die zweistufige Authentifizierung testen</span><span class="sxs-lookup"><span data-stu-id="551aa-163">Test two-factor authentication</span></span>

* <span data-ttu-id="551aa-164">Melden Sie sich ab.</span><span class="sxs-lookup"><span data-stu-id="551aa-164">Log off.</span></span>

* <span data-ttu-id="551aa-165">Melden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="551aa-165">Log in.</span></span>

* <span data-ttu-id="551aa-166">Das Benutzerkonto verfügt über zwei-Faktor-Authentifizierung aktiviert, müssen Sie die zweite Stufe der Authentifizierung bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="551aa-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="551aa-167">In diesem Tutorial haben Sie die Überprüfung per Telefon aktiviert.</span><span class="sxs-lookup"><span data-stu-id="551aa-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="551aa-168">Die integrierten Vorlagen ermöglichen Ihnen das Einrichten von e-Mail als zweiter authentifizierungsfaktor auch.</span><span class="sxs-lookup"><span data-stu-id="551aa-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="551aa-169">Sie können die zweite Faktoren für die Authentifizierung wie z. B. QR-Codes einrichten.</span><span class="sxs-lookup"><span data-stu-id="551aa-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="551aa-170">Tippen Sie auf **senden**.</span><span class="sxs-lookup"><span data-stu-id="551aa-170">Tap **Submit**.</span></span>

![Überprüfungscode senden](2fa/_static/login2fa7.png)

* <span data-ttu-id="551aa-172">Geben Sie den Code, den Sie in der SMS-Nachricht erhalten.</span><span class="sxs-lookup"><span data-stu-id="551aa-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="551aa-173">Wenn Sie auf das Kontrollkästchen **diesen Browser speichern** klicken, werden Sie von der Verwendung von 2FA zum Anmelden aufgefordert, wenn Sie das gleiche Gerät und den gleichen Browser verwenden.</span><span class="sxs-lookup"><span data-stu-id="551aa-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="551aa-174">Durch das Aktivieren von 2FA und das Klicken auf **Speichern dieses Browsers** erhalten Sie einen starken 2FA-Schutz vor böswilligen Benutzern, die versuchen, auf Ihr Konto zuzugreifen, solange Sie keinen Zugriff auf Ihr Gerät haben.</span><span class="sxs-lookup"><span data-stu-id="551aa-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="551aa-175">Sie können auf allen privaten Geräten dazu, die Sie regelmäßig verwenden.</span><span class="sxs-lookup"><span data-stu-id="551aa-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="551aa-176">Wenn Sie **diesen Browser merken**, erhalten Sie die zusätzliche Sicherheit von 2FA von Geräten, die Sie nicht regelmäßig verwenden, und Sie erhalten die Möglichkeit, die 2FA nicht auf Ihren eigenen Geräten zu durchlaufen.</span><span class="sxs-lookup"><span data-stu-id="551aa-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![Überprüfen Sie die Ansicht](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="551aa-178">Sperre zum Schutz vor brute-Force-Angriffen</span><span class="sxs-lookup"><span data-stu-id="551aa-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="551aa-179">Kontosperrung wird mit 2FA empfohlen.</span><span class="sxs-lookup"><span data-stu-id="551aa-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="551aa-180">Sobald ein Benutzer über ein lokales Konto oder ein Konto für soziales Netzwerk anmeldet, wird jeder fehlerhafte Versuch am 2FA gespeichert.</span><span class="sxs-lookup"><span data-stu-id="551aa-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="551aa-181">Wenn die maximale zugriffsversuchsfehlern erreicht ist, wird der Benutzer gesperrt ist (Standardwert: 5-minütige Sperre nach 5 Versuche fehlgeschlagen).</span><span class="sxs-lookup"><span data-stu-id="551aa-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="551aa-182">Eine erfolgreiche Authentifizierung setzt die Anzahl der fehlgeschlagenen Zugriffe Versuche und setzt die Uhr.</span><span class="sxs-lookup"><span data-stu-id="551aa-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="551aa-183">Die maximalen fehlgeschlagenen Zugriffsversuche und Sperr Zeit können mit " [maxfailedaccessversuchs](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) " und " [defaultlockouttimespan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)" festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="551aa-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="551aa-184">Der folgende Code konfiguriert kontosperrung nach 10 Minuten nach 10 Versuche nicht erfolgreichen:</span><span class="sxs-lookup"><span data-stu-id="551aa-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="551aa-185">Vergewissern Sie sich, dass [passwordsigninasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) `lockoutOnFailure` auf `true`festlegt:</span><span class="sxs-lookup"><span data-stu-id="551aa-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
