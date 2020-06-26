---
title: Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.net Core-App mit e-Mail-Bestätigung und Kenn Wort Zurücksetzung
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: bf599487fdc3e574f72f1a3d35278cc9c2ce7513
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404651"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="82a69-103">Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="82a69-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="82a69-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="82a69-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="82a69-105">Dieses Tutorial zeigt, wie Sie eine ASP.net Core-App mit e-Mail-Bestätigung und Kenn Wort Zurücksetzung erstellen.</span><span class="sxs-lookup"><span data-stu-id="82a69-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="82a69-106">Dieses Tutorial ist **kein** Thema.</span><span class="sxs-lookup"><span data-stu-id="82a69-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="82a69-107">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="82a69-107">You should be familiar with:</span></span>

* [<span data-ttu-id="82a69-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="82a69-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="82a69-109">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="82a69-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="82a69-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="82a69-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="82a69-111">Informationen zum ASP.net Core 1,1-Version finden Sie in [dieser PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) .</span><span class="sxs-lookup"><span data-stu-id="82a69-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="82a69-112">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="82a69-112">Prerequisites</span></span>

[<span data-ttu-id="82a69-113">.NET Core 3.0 SDK oder höher</span><span class="sxs-lookup"><span data-stu-id="82a69-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="82a69-114">Erstellen und Testen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="82a69-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="82a69-115">Führen Sie die folgenden Befehle aus, um eine Web-App mit Authentifizierung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="82a69-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="82a69-116">Führen Sie die APP aus, wählen Sie den Link **registrieren** aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="82a69-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="82a69-117">Nach der Registrierung werden Sie zur Seite an weitergeleitet, `/Identity/Account/RegisterConfirmation` die einen Link zum Simulieren einer e-Mail-Bestätigung enthält:</span><span class="sxs-lookup"><span data-stu-id="82a69-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="82a69-118">Wählen Sie den `Click here to confirm your account` Link aus.</span><span class="sxs-lookup"><span data-stu-id="82a69-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="82a69-119">Wählen Sie den **Anmelde** Link aus, und melden Sie sich mit denselben Anmelde Informationen an.</span><span class="sxs-lookup"><span data-stu-id="82a69-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="82a69-120">Wählen Sie den `Hello YourEmail@provider.com!` Link aus, der Sie zur Seite umleitet `/Identity/Account/Manage/PersonalData` .</span><span class="sxs-lookup"><span data-stu-id="82a69-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="82a69-121">Wählen Sie auf der linken Seite die Registerkarte **persönliche Daten** aus, und klicken Sie dann auf **Löschen**.</span><span class="sxs-lookup"><span data-stu-id="82a69-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="82a69-122">Konfigurieren eines e-Mail-Anbieters</span><span class="sxs-lookup"><span data-stu-id="82a69-122">Configure an email provider</span></span>

<span data-ttu-id="82a69-123">In diesem Tutorial wird [sendgrid](https://sendgrid.com) zum Senden von e-Mails verwendet.</span><span class="sxs-lookup"><span data-stu-id="82a69-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="82a69-124">Sie benötigen ein sendgrid-Konto und einen Schlüssel zum Senden von e-Mails.</span><span class="sxs-lookup"><span data-stu-id="82a69-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="82a69-125">Sie können andere e-Mail-Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="82a69-125">You can use other email providers.</span></span> <span data-ttu-id="82a69-126">Es wird empfohlen, sendgrid oder einen anderen e-Mail-Dienst zum Senden von e-Mails zu verwenden</span><span class="sxs-lookup"><span data-stu-id="82a69-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="82a69-127">SMTP ist schwierig zu sichern und ordnungsgemäß eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="82a69-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="82a69-128">Das sendgrid-Konto erfordert möglicherweise das [Hinzufügen eines Absenders](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="82a69-128">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="82a69-129">Erstellen Sie eine Klasse zum Abrufen des Schlüssels für sichere e-Mails.</span><span class="sxs-lookup"><span data-stu-id="82a69-129">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="82a69-130">Erstellen Sie für dieses Beispiel *Dienste/authmessagesenderoptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="82a69-130">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="82a69-131">Konfigurieren von sendgrid-Benutzer Geheimnissen</span><span class="sxs-lookup"><span data-stu-id="82a69-131">Configure SendGrid user secrets</span></span>

<span data-ttu-id="82a69-132">Legen Sie `SendGridUser` und `SendGridKey` mit dem [Secret-Manager-Tool](xref:security/app-secrets)fest.</span><span class="sxs-lookup"><span data-stu-id="82a69-132">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="82a69-133">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82a69-133">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="82a69-134">Unter Windows speichert Secret Manager Schlüssel-Wert-Paare in einer *secrets.js* in der Datei im `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="82a69-134">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="82a69-135">Der Inhalt des *secrets.jsin* der Datei ist nicht verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="82a69-135">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="82a69-136">Das folgende Markup zeigt die *secrets.jsfür* die Datei.</span><span class="sxs-lookup"><span data-stu-id="82a69-136">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="82a69-137">Der `SendGridKey` Wert wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="82a69-137">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="82a69-138">Weitere Informationen finden Sie im [options Muster](xref:fundamentals/configuration/options) und in der [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="82a69-138">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="82a69-139">Installieren von sendgrid</span><span class="sxs-lookup"><span data-stu-id="82a69-139">Install SendGrid</span></span>

<span data-ttu-id="82a69-140">In diesem Tutorial wird gezeigt, wie Sie e-Mail-Benachrichtigungen über [sendgrid](https://sendgrid.com/)hinzufügen, aber Sie können e-Mails mithilfe von SMTP und anderen Mechanismen senden.</span><span class="sxs-lookup"><span data-stu-id="82a69-140">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="82a69-141">Installieren Sie das `SendGrid` nuget-Paket:</span><span class="sxs-lookup"><span data-stu-id="82a69-141">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="82a69-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="82a69-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="82a69-143">Geben Sie in der Paket-Manager-Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="82a69-143">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="82a69-144">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="82a69-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="82a69-145">Geben Sie in der Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="82a69-145">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="82a69-146">Weitere Informationen zur Registrierung für ein kostenloses sendgrid-Konto finden [Sie unter Get Started with sendgrid](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="82a69-146">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="82a69-147">Implementieren von iemailsender</span><span class="sxs-lookup"><span data-stu-id="82a69-147">Implement IEmailSender</span></span>

<span data-ttu-id="82a69-148">Erstellen Sie zum Implementieren von `IEmailSender` *Services/emailsender. cs* mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="82a69-148">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="82a69-149">Konfigurieren des Starts zur Unterstützung von e-Mails</span><span class="sxs-lookup"><span data-stu-id="82a69-149">Configure startup to support email</span></span>

<span data-ttu-id="82a69-150">Fügen Sie der- `ConfigureServices` Methode in der Datei *Startup.cs* den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="82a69-150">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="82a69-151">Fügen Sie `EmailSender` als vorübergehenden Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="82a69-151">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="82a69-152">Registrieren Sie die `AuthMessageSenderOptions` Konfigurations Instanz.</span><span class="sxs-lookup"><span data-stu-id="82a69-152">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="82a69-153">Registrieren, bestätigen der e-Mail und Zurücksetzen des Kennworts</span><span class="sxs-lookup"><span data-stu-id="82a69-153">Register, confirm email, and reset password</span></span>

<span data-ttu-id="82a69-154">Führen Sie die Web-App aus, und testen Sie den Ablauf der Konto Bestätigung und Kenn Wort Wiederherstellung.</span><span class="sxs-lookup"><span data-stu-id="82a69-154">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="82a69-155">Ausführen der APP und Registrieren eines neuen Benutzers</span><span class="sxs-lookup"><span data-stu-id="82a69-155">Run the app and register a new user</span></span>
* <span data-ttu-id="82a69-156">Überprüfen Sie Ihre e-Mail auf den Link zur Konto Bestätigung.</span><span class="sxs-lookup"><span data-stu-id="82a69-156">Check your email for the account confirmation link.</span></span> <span data-ttu-id="82a69-157">Siehe [Debuggen von e-Mails](#debug) , wenn Sie die e-Mail nicht erhalten.</span><span class="sxs-lookup"><span data-stu-id="82a69-157">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="82a69-158">Klicken Sie auf den Link, um Ihre e-Mail zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="82a69-158">Click the link to confirm your email.</span></span>
* <span data-ttu-id="82a69-159">Melden Sie sich mit Ihrer e-Mail und dem Kennwort an.</span><span class="sxs-lookup"><span data-stu-id="82a69-159">Sign in with your email and password.</span></span>
* <span data-ttu-id="82a69-160">Melden Sie sich ab.</span><span class="sxs-lookup"><span data-stu-id="82a69-160">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="82a69-161">Kenn Wort Zurücksetzung testen</span><span class="sxs-lookup"><span data-stu-id="82a69-161">Test password reset</span></span>

* <span data-ttu-id="82a69-162">Wenn Sie angemeldet sind, wählen Sie **Abmelden aus**.</span><span class="sxs-lookup"><span data-stu-id="82a69-162">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="82a69-163">Wählen Sie den Link **Anmelden aus** , und wählen Sie den Link **Kennwort vergessen?** aus.</span><span class="sxs-lookup"><span data-stu-id="82a69-163">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="82a69-164">Geben Sie die e-Mail ein, die Sie zum Registrieren des Kontos verwendet haben.</span><span class="sxs-lookup"><span data-stu-id="82a69-164">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="82a69-165">Eine e-Mail mit einem Link zum Zurücksetzen Ihres Kennworts wird gesendet.</span><span class="sxs-lookup"><span data-stu-id="82a69-165">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="82a69-166">Überprüfen Sie Ihre e-Mail, und klicken Sie auf den Link zum Zurücksetzen Ihres Kennworts</span><span class="sxs-lookup"><span data-stu-id="82a69-166">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="82a69-167">Nachdem das Kennwort erfolgreich zurückgesetzt wurde, können Sie sich mit Ihrer e-Mail-Adresse und dem neuen Kennwort anmelden.</span><span class="sxs-lookup"><span data-stu-id="82a69-167">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="82a69-168">E-Mail-und Aktivitäts Timeout ändern</span><span class="sxs-lookup"><span data-stu-id="82a69-168">Change email and activity timeout</span></span>

<span data-ttu-id="82a69-169">Das standardmäßige Inaktivitäts Timeout beträgt 14 Tage.</span><span class="sxs-lookup"><span data-stu-id="82a69-169">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="82a69-170">Mit dem folgenden Code wird das Inaktivitäts Timeout auf 5 Tage festgelegt:</span><span class="sxs-lookup"><span data-stu-id="82a69-170">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="82a69-171">Alle Datenschutz Token-Lebensdauer ändern</span><span class="sxs-lookup"><span data-stu-id="82a69-171">Change all data protection token lifespans</span></span>

<span data-ttu-id="82a69-172">Der folgende Code ändert den Timeout Zeitraum für alle Token für die Datenschutz Token in 3 Stunden:</span><span class="sxs-lookup"><span data-stu-id="82a69-172">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="82a69-173">Die integrierten Identity Benutzer Token (siehe [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) haben ein [Timeout von einem Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="82a69-173">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="82a69-174">Lebensdauer von e-Mail-Token</span><span class="sxs-lookup"><span data-stu-id="82a69-174">Change the email token lifespan</span></span>

<span data-ttu-id="82a69-175">Die standardmäßige tokengültigkeits Dauer der [ Identity Benutzer Token](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ist [ein Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="82a69-175">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="82a69-176">In diesem Abschnitt wird gezeigt, wie Sie die Lebensdauer von e-Mail-</span><span class="sxs-lookup"><span data-stu-id="82a69-176">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="82a69-177">Fügen Sie einen benutzerdefinierten [dataprotectortokenprovider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) und Folgendes hinzu <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="82a69-177">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="82a69-178">Fügen Sie dem Dienst Container den benutzerdefinierten Anbieter hinzu:</span><span class="sxs-lookup"><span data-stu-id="82a69-178">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="82a69-179">E-Mail-Bestätigung erneut senden</span><span class="sxs-lookup"><span data-stu-id="82a69-179">Resend email confirmation</span></span>

<span data-ttu-id="82a69-180">Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="82a69-180">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="82a69-181">E-Mail Debuggen</span><span class="sxs-lookup"><span data-stu-id="82a69-181">Debug email</span></span>

<span data-ttu-id="82a69-182">Wenn Sie keine e-Mail mehr erhalten können:</span><span class="sxs-lookup"><span data-stu-id="82a69-182">If you can't get email working:</span></span>

* <span data-ttu-id="82a69-183">Legen Sie einen Haltepunkt fest `EmailSender.Execute` , um zu überprüfen, ob `SendGridClient.SendEmailAsync` aufgerufen wird</span><span class="sxs-lookup"><span data-stu-id="82a69-183">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="82a69-184">Erstellen [Sie eine Konsolen-App zum Senden von e-Mails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mithilfe eines ähnlichen Codes an `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="82a69-184">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="82a69-185">Lesen Sie die Seite [e-Mail-Aktivität](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="82a69-185">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="82a69-186">Überprüfen Sie Ihren Spam-Ordner.</span><span class="sxs-lookup"><span data-stu-id="82a69-186">Check your spam folder.</span></span>
* <span data-ttu-id="82a69-187">Probieren Sie einen anderen e-Mail-Alias für einen anderen e-Mail-Anbieter aus (Microsoft, Yahoo, Gmail usw.)</span><span class="sxs-lookup"><span data-stu-id="82a69-187">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="82a69-188">Senden Sie an verschiedene e-Mail-Konten.</span><span class="sxs-lookup"><span data-stu-id="82a69-188">Try sending to different email accounts.</span></span>

<span data-ttu-id="82a69-189">**Eine bewährte Sicherheits** Maßnahme besteht darin, **keine** Produktionsgeheimnisse in Test-und Entwicklungsumgebungen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="82a69-189">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="82a69-190">Wenn Sie die app in Azure veröffentlichen, legen Sie die sendgrid-Geheimnisse als Anwendungseinstellungen im Azure-Web-App-Portal fest.</span><span class="sxs-lookup"><span data-stu-id="82a69-190">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="82a69-191">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="82a69-191">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="82a69-192">Kombinieren von sozialen und lokalen Anmeldekonten</span><span class="sxs-lookup"><span data-stu-id="82a69-192">Combine social and local login accounts</span></span>

<span data-ttu-id="82a69-193">Zum Durchführen dieses Abschnitts müssen Sie zuerst einen externen Authentifizierungs Anbieter aktivieren.</span><span class="sxs-lookup"><span data-stu-id="82a69-193">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="82a69-194">Weitere Informationen finden Sie unter [Authentifizierung für Facebook, Google und externe Anbieter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="82a69-194">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="82a69-195">Sie können lokale und soziale Konten kombinieren, indem Sie auf Ihren e-Mail-Link klicken.</span><span class="sxs-lookup"><span data-stu-id="82a69-195">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="82a69-196">In der folgenden Sequenz wird " RickAndMSFT@gmail.com " zuerst als lokale Anmeldung erstellt. Sie können das Konto jedoch zuerst als Social Login (Social Login) erstellen und dann einen lokalen Anmelde Namen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="82a69-196">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webanwendung: RickAndMSFT@gmail.com Benutzer authentifiziert](accconfirm/_static/rick.png)

<span data-ttu-id="82a69-198">Klicken Sie auf den Link **Verwalten** .</span><span class="sxs-lookup"><span data-stu-id="82a69-198">Click on the **Manage** link.</span></span> <span data-ttu-id="82a69-199">Beachten Sie die 0 (null) externen (Anmeldungen), die diesem Konto zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="82a69-199">Note the 0 external (social logins) associated with this account.</span></span>

![Ansicht verwalten](accconfirm/_static/manage.png)

<span data-ttu-id="82a69-201">Klicken Sie auf den Link zu einem anderen Anmeldedienst, und akzeptieren Sie die APP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="82a69-201">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="82a69-202">In der folgenden Abbildung ist Facebook der externe Authentifizierungs Anbieter:</span><span class="sxs-lookup"><span data-stu-id="82a69-202">In the following image, Facebook is the external authentication provider:</span></span>

![Verwalten der Ansicht externer Anmeldungen mit Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="82a69-204">Die beiden Konten wurden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="82a69-204">The two accounts have been combined.</span></span> <span data-ttu-id="82a69-205">Sie können sich mit einem der beiden Konten anmelden.</span><span class="sxs-lookup"><span data-stu-id="82a69-205">You are able to sign in with either account.</span></span> <span data-ttu-id="82a69-206">Möglicherweise möchten Sie, dass Ihre Benutzer lokale Konten hinzufügen, falls der Authentifizierungsdienst für soziale Anmelde Informationen ausgefallen ist oder Sie den Zugriff auf Ihr Social Media-Konto verlieren.</span><span class="sxs-lookup"><span data-stu-id="82a69-206">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="82a69-207">Aktivieren der Konto Bestätigung nach einem Standort Benutzer</span><span class="sxs-lookup"><span data-stu-id="82a69-207">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="82a69-208">Das Aktivieren der Konto Bestätigung auf einem Standort mit Benutzern sperrt alle vorhandenen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="82a69-208">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="82a69-209">Vorhandene Benutzer werden gesperrt, da ihre Konten nicht bestätigt werden.</span><span class="sxs-lookup"><span data-stu-id="82a69-209">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="82a69-210">Verwenden Sie einen der folgenden Ansätze, um die vorhandene Benutzer Sperre zu umgehen:</span><span class="sxs-lookup"><span data-stu-id="82a69-210">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="82a69-211">Aktualisieren Sie die Datenbank, um alle vorhandenen Benutzer als bestätigt zu markieren.</span><span class="sxs-lookup"><span data-stu-id="82a69-211">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="82a69-212">Vorhandene Benutzer bestätigen.</span><span class="sxs-lookup"><span data-stu-id="82a69-212">Confirm existing users.</span></span> <span data-ttu-id="82a69-213">Beispiel: Senden von e-Mails mit Bestätigungs Links per Batch.</span><span class="sxs-lookup"><span data-stu-id="82a69-213">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="82a69-214">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="82a69-214">Prerequisites</span></span>

[<span data-ttu-id="82a69-215">.Net Core 2,2 SDK oder höher</span><span class="sxs-lookup"><span data-stu-id="82a69-215">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="82a69-216">Erstellen einer Web-App und eines GerüstsIdentity</span><span class="sxs-lookup"><span data-stu-id="82a69-216">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="82a69-217">Führen Sie die folgenden Befehle aus, um eine Web-App mit Authentifizierung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="82a69-217">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="82a69-218">Neue Benutzerregistrierung testen</span><span class="sxs-lookup"><span data-stu-id="82a69-218">Test new user registration</span></span>

<span data-ttu-id="82a69-219">Führen Sie die APP aus, wählen Sie den Link **registrieren** aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="82a69-219">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="82a69-220">An diesem Punkt wird nur die e-Mail-Überprüfung mit dem- [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) Attribut durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="82a69-220">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="82a69-221">Nachdem Sie die Registrierung übermittelt haben, werden Sie bei der App angemeldet.</span><span class="sxs-lookup"><span data-stu-id="82a69-221">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="82a69-222">Später in diesem Tutorial wird der Code aktualisiert, sodass neue Benutzer sich erst anmelden können, wenn Ihre e-Mail-Nachricht überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="82a69-222">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="82a69-223">Beachten Sie, dass das Feld der Tabelle `EmailConfirmed` ist `False` .</span><span class="sxs-lookup"><span data-stu-id="82a69-223">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="82a69-224">Sie sollten diese e-Mail im nächsten Schritt erneut verwenden, wenn die APP eine Bestätigungs-e-Mail sendet.</span><span class="sxs-lookup"><span data-stu-id="82a69-224">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="82a69-225">Klicken Sie mit der rechten Maustaste auf die Zeile, und wählen Sie **Löschen**.</span><span class="sxs-lookup"><span data-stu-id="82a69-225">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="82a69-226">Wenn Sie den e-Mail-Alias löschen, wird dies in den folgenden Schritten vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="82a69-226">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="82a69-227">Bestätigung per e-Mail erforderlich</span><span class="sxs-lookup"><span data-stu-id="82a69-227">Require email confirmation</span></span>

<span data-ttu-id="82a69-228">Es wird empfohlen, die e-Mail-Adresse einer neuen Benutzerregistrierung zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="82a69-228">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="82a69-229">Mithilfe der e-Mail-Bestätigung können Sie sicherstellen, dass Sie keine andere Identität annehmen (d. h., Sie haben sich nicht bei einer anderen e-Mail registriert).</span><span class="sxs-lookup"><span data-stu-id="82a69-229">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="82a69-230">Angenommen, Sie hatten ein Diskussionsforum, und Sie wollten verhindern, dass sich " yli@example.com " als "" registriert nolivetto@contoso.com .</span><span class="sxs-lookup"><span data-stu-id="82a69-230">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="82a69-231">Ohne e-Mail-Bestätigung kann " nolivetto@contoso.com " unerwünschte e-Mails von Ihrer APP erhalten.</span><span class="sxs-lookup"><span data-stu-id="82a69-231">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="82a69-232">Angenommen, der Benutzer hat sich versehentlich als " ylo@example.com " registriert und die falsche Schreibweise von "Yli" nicht bemerkt.</span><span class="sxs-lookup"><span data-stu-id="82a69-232">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="82a69-233">Sie sind nicht in der Lage, die Kenn Wort Wiederherstellung zu verwenden, da die APP nicht über die richtige e-Mail verfügt</span><span class="sxs-lookup"><span data-stu-id="82a69-233">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="82a69-234">E-Mail-Bestätigung bietet eingeschränkten Schutz vor Bots.</span><span class="sxs-lookup"><span data-stu-id="82a69-234">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="82a69-235">E-Mail-Bestätigung bietet keinen Schutz vor böswilligen Benutzern mit vielen e-Mail-Konten</span><span class="sxs-lookup"><span data-stu-id="82a69-235">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="82a69-236">In der Regel möchten Sie, dass neue Benutzerdaten an Ihrer Website veröffentlichen, bevor Sie eine bestätigte e-Mail-Adresse haben.</span><span class="sxs-lookup"><span data-stu-id="82a69-236">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="82a69-237">Aktualisieren `Startup.ConfigureServices` Sie, um eine bestätigte e-Mail anzufordern:</span><span class="sxs-lookup"><span data-stu-id="82a69-237">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="82a69-238">`config.SignIn.RequireConfirmedEmail = true;`verhindert, dass registrierte Benutzer sich anmelden, bis Ihre e-Mail-Nachricht bestätigt wird.</span><span class="sxs-lookup"><span data-stu-id="82a69-238">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="82a69-239">E-Mail-Anbieter</span><span class="sxs-lookup"><span data-stu-id="82a69-239">Configure email provider</span></span>

<span data-ttu-id="82a69-240">In diesem Tutorial wird [sendgrid](https://sendgrid.com) zum Senden von e-Mails verwendet.</span><span class="sxs-lookup"><span data-stu-id="82a69-240">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="82a69-241">Sie benötigen ein sendgrid-Konto und einen Schlüssel zum Senden von e-Mails.</span><span class="sxs-lookup"><span data-stu-id="82a69-241">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="82a69-242">Sie können andere e-Mail-Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="82a69-242">You can use other email providers.</span></span> <span data-ttu-id="82a69-243">ASP.net Core 2. x umfasst `System.Net.Mail` , das Ihnen ermöglicht, e-Mails von Ihrer APP zu senden.</span><span class="sxs-lookup"><span data-stu-id="82a69-243">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="82a69-244">Es wird empfohlen, sendgrid oder einen anderen e-Mail-Dienst zum Senden von e-Mails zu verwenden</span><span class="sxs-lookup"><span data-stu-id="82a69-244">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="82a69-245">SMTP ist schwierig zu sichern und ordnungsgemäß eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="82a69-245">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="82a69-246">Erstellen Sie eine Klasse zum Abrufen des Schlüssels für sichere e-Mails.</span><span class="sxs-lookup"><span data-stu-id="82a69-246">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="82a69-247">Erstellen Sie für dieses Beispiel *Dienste/authmessagesenderoptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="82a69-247">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="82a69-248">Konfigurieren von sendgrid-Benutzer Geheimnissen</span><span class="sxs-lookup"><span data-stu-id="82a69-248">Configure SendGrid user secrets</span></span>

<span data-ttu-id="82a69-249">Legen Sie `SendGridUser` und `SendGridKey` mit dem [Secret-Manager-Tool](xref:security/app-secrets)fest.</span><span class="sxs-lookup"><span data-stu-id="82a69-249">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="82a69-250">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="82a69-250">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="82a69-251">Unter Windows speichert Secret Manager Schlüssel-Wert-Paare in einer *secrets.js* in der Datei im `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="82a69-251">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="82a69-252">Der Inhalt des *secrets.jsin* der Datei ist nicht verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="82a69-252">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="82a69-253">Das folgende Markup zeigt die *secrets.jsfür* die Datei.</span><span class="sxs-lookup"><span data-stu-id="82a69-253">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="82a69-254">Der `SendGridKey` Wert wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="82a69-254">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="82a69-255">Weitere Informationen finden Sie im [options Muster](xref:fundamentals/configuration/options) und in der [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="82a69-255">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="82a69-256">Installieren von sendgrid</span><span class="sxs-lookup"><span data-stu-id="82a69-256">Install SendGrid</span></span>

<span data-ttu-id="82a69-257">In diesem Tutorial wird gezeigt, wie Sie e-Mail-Benachrichtigungen über [sendgrid](https://sendgrid.com/)hinzufügen, aber Sie können e-Mails mithilfe von SMTP und anderen Mechanismen senden.</span><span class="sxs-lookup"><span data-stu-id="82a69-257">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="82a69-258">Installieren Sie das `SendGrid` nuget-Paket:</span><span class="sxs-lookup"><span data-stu-id="82a69-258">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="82a69-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="82a69-259">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="82a69-260">Geben Sie in der Paket-Manager-Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="82a69-260">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="82a69-261">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="82a69-261">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="82a69-262">Geben Sie in der Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="82a69-262">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="82a69-263">Weitere Informationen zur Registrierung für ein kostenloses sendgrid-Konto finden [Sie unter Get Started with sendgrid](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="82a69-263">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="82a69-264">Implementieren von iemailsender</span><span class="sxs-lookup"><span data-stu-id="82a69-264">Implement IEmailSender</span></span>

<span data-ttu-id="82a69-265">Erstellen Sie zum Implementieren von `IEmailSender` *Services/emailsender. cs* mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="82a69-265">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="82a69-266">Konfigurieren des Starts zur Unterstützung von e-Mails</span><span class="sxs-lookup"><span data-stu-id="82a69-266">Configure startup to support email</span></span>

<span data-ttu-id="82a69-267">Fügen Sie der- `ConfigureServices` Methode in der Datei *Startup.cs* den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="82a69-267">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="82a69-268">Fügen Sie `EmailSender` als vorübergehenden Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="82a69-268">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="82a69-269">Registrieren Sie die `AuthMessageSenderOptions` Konfigurations Instanz.</span><span class="sxs-lookup"><span data-stu-id="82a69-269">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="82a69-270">Aktivieren der Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="82a69-270">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="82a69-271">Die Vorlage enthält den Code für die Konto Bestätigung und Kenn Wort Wiederherstellung.</span><span class="sxs-lookup"><span data-stu-id="82a69-271">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="82a69-272">Suchen Sie die `OnPostAsync` -Methode in " *Areas/ Identity /pages/Account/Register.cshtml.cs*".</span><span class="sxs-lookup"><span data-stu-id="82a69-272">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="82a69-273">Verhindern Sie, dass neu registrierte Benutzer automatisch angemeldet werden, indem Sie die folgende Zeile auskommentieren:</span><span class="sxs-lookup"><span data-stu-id="82a69-273">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="82a69-274">Die gesamte-Methode wird angezeigt, wenn die geänderte Zeile hervorgehoben ist:</span><span class="sxs-lookup"><span data-stu-id="82a69-274">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="82a69-275">Registrieren, bestätigen der e-Mail und Zurücksetzen des Kennworts</span><span class="sxs-lookup"><span data-stu-id="82a69-275">Register, confirm email, and reset password</span></span>

<span data-ttu-id="82a69-276">Führen Sie die Web-App aus, und testen Sie den Ablauf der Konto Bestätigung und Kenn Wort Wiederherstellung.</span><span class="sxs-lookup"><span data-stu-id="82a69-276">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="82a69-277">Ausführen der APP und Registrieren eines neuen Benutzers</span><span class="sxs-lookup"><span data-stu-id="82a69-277">Run the app and register a new user</span></span>
* <span data-ttu-id="82a69-278">Überprüfen Sie Ihre e-Mail auf den Link zur Konto Bestätigung.</span><span class="sxs-lookup"><span data-stu-id="82a69-278">Check your email for the account confirmation link.</span></span> <span data-ttu-id="82a69-279">Siehe [Debuggen von e-Mails](#debug) , wenn Sie die e-Mail nicht erhalten.</span><span class="sxs-lookup"><span data-stu-id="82a69-279">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="82a69-280">Klicken Sie auf den Link, um Ihre e-Mail zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="82a69-280">Click the link to confirm your email.</span></span>
* <span data-ttu-id="82a69-281">Melden Sie sich mit Ihrer e-Mail und dem Kennwort an.</span><span class="sxs-lookup"><span data-stu-id="82a69-281">Sign in with your email and password.</span></span>
* <span data-ttu-id="82a69-282">Melden Sie sich ab.</span><span class="sxs-lookup"><span data-stu-id="82a69-282">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="82a69-283">Anzeigen der Seite "verwalten"</span><span class="sxs-lookup"><span data-stu-id="82a69-283">View the manage page</span></span>

<span data-ttu-id="82a69-284">Wählen Sie Ihren Benutzernamen im Browser aus: ![ Browserfenster mit Benutzername](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="82a69-284">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="82a69-285">Die Seite verwalten wird angezeigt, und die Registerkarte **Profil** ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="82a69-285">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="82a69-286">Die **e-Mail** zeigt ein Kontrollkästchen an, das anzeigt, dass die e-Mail bestätigt wurde.</span><span class="sxs-lookup"><span data-stu-id="82a69-286">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="82a69-287">Kenn Wort Zurücksetzung testen</span><span class="sxs-lookup"><span data-stu-id="82a69-287">Test password reset</span></span>

* <span data-ttu-id="82a69-288">Wenn Sie angemeldet sind, wählen Sie **Abmelden aus**.</span><span class="sxs-lookup"><span data-stu-id="82a69-288">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="82a69-289">Wählen Sie den Link **Anmelden aus** , und wählen Sie den Link **Kennwort vergessen?** aus.</span><span class="sxs-lookup"><span data-stu-id="82a69-289">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="82a69-290">Geben Sie die e-Mail ein, die Sie zum Registrieren des Kontos verwendet haben.</span><span class="sxs-lookup"><span data-stu-id="82a69-290">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="82a69-291">Eine e-Mail mit einem Link zum Zurücksetzen Ihres Kennworts wird gesendet.</span><span class="sxs-lookup"><span data-stu-id="82a69-291">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="82a69-292">Überprüfen Sie Ihre e-Mail, und klicken Sie auf den Link zum Zurücksetzen Ihres Kennworts</span><span class="sxs-lookup"><span data-stu-id="82a69-292">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="82a69-293">Nachdem das Kennwort erfolgreich zurückgesetzt wurde, können Sie sich mit Ihrer e-Mail-Adresse und dem neuen Kennwort anmelden.</span><span class="sxs-lookup"><span data-stu-id="82a69-293">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="82a69-294">E-Mail-und Aktivitäts Timeout ändern</span><span class="sxs-lookup"><span data-stu-id="82a69-294">Change email and activity timeout</span></span>

<span data-ttu-id="82a69-295">Das standardmäßige Inaktivitäts Timeout beträgt 14 Tage.</span><span class="sxs-lookup"><span data-stu-id="82a69-295">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="82a69-296">Mit dem folgenden Code wird das Inaktivitäts Timeout auf 5 Tage festgelegt:</span><span class="sxs-lookup"><span data-stu-id="82a69-296">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="82a69-297">Alle Datenschutz Token-Lebensdauer ändern</span><span class="sxs-lookup"><span data-stu-id="82a69-297">Change all data protection token lifespans</span></span>

<span data-ttu-id="82a69-298">Der folgende Code ändert den Timeout Zeitraum für alle Token für die Datenschutz Token in 3 Stunden:</span><span class="sxs-lookup"><span data-stu-id="82a69-298">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="82a69-299">Die integrierten Identity Benutzer Token (siehe [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) haben ein [Timeout von einem Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="82a69-299">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="82a69-300">Lebensdauer von e-Mail-Token</span><span class="sxs-lookup"><span data-stu-id="82a69-300">Change the email token lifespan</span></span>

<span data-ttu-id="82a69-301">Die standardmäßige tokengültigkeits Dauer der [ Identity Benutzer Token](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ist [ein Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="82a69-301">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="82a69-302">In diesem Abschnitt wird gezeigt, wie Sie die Lebensdauer von e-Mail-</span><span class="sxs-lookup"><span data-stu-id="82a69-302">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="82a69-303">Fügen Sie einen benutzerdefinierten [dataprotectortokenprovider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) und Folgendes hinzu <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="82a69-303">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="82a69-304">Fügen Sie dem Dienst Container den benutzerdefinierten Anbieter hinzu:</span><span class="sxs-lookup"><span data-stu-id="82a69-304">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="82a69-305">E-Mail-Bestätigung erneut senden</span><span class="sxs-lookup"><span data-stu-id="82a69-305">Resend email confirmation</span></span>

<span data-ttu-id="82a69-306">Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="82a69-306">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="82a69-307">E-Mail Debuggen</span><span class="sxs-lookup"><span data-stu-id="82a69-307">Debug email</span></span>

<span data-ttu-id="82a69-308">Wenn Sie keine e-Mail mehr erhalten können:</span><span class="sxs-lookup"><span data-stu-id="82a69-308">If you can't get email working:</span></span>

* <span data-ttu-id="82a69-309">Legen Sie einen Haltepunkt fest `EmailSender.Execute` , um zu überprüfen, ob `SendGridClient.SendEmailAsync` aufgerufen wird</span><span class="sxs-lookup"><span data-stu-id="82a69-309">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="82a69-310">Erstellen [Sie eine Konsolen-App zum Senden von e-Mails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mithilfe eines ähnlichen Codes an `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="82a69-310">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="82a69-311">Lesen Sie die Seite [e-Mail-Aktivität](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="82a69-311">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="82a69-312">Überprüfen Sie Ihren Spam-Ordner.</span><span class="sxs-lookup"><span data-stu-id="82a69-312">Check your spam folder.</span></span>
* <span data-ttu-id="82a69-313">Probieren Sie einen anderen e-Mail-Alias für einen anderen e-Mail-Anbieter aus (Microsoft, Yahoo, Gmail usw.)</span><span class="sxs-lookup"><span data-stu-id="82a69-313">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="82a69-314">Senden Sie an verschiedene e-Mail-Konten.</span><span class="sxs-lookup"><span data-stu-id="82a69-314">Try sending to different email accounts.</span></span>

<span data-ttu-id="82a69-315">**Eine bewährte Sicherheits** Maßnahme besteht darin, **keine** Produktionsgeheimnisse in Test-und Entwicklungsumgebungen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="82a69-315">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="82a69-316">Wenn Sie die app in Azure veröffentlichen, können Sie die sendgrid-Geheimnisse als Anwendungseinstellungen im Azure-Web-App-Portal festlegen.</span><span class="sxs-lookup"><span data-stu-id="82a69-316">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="82a69-317">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="82a69-317">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="82a69-318">Kombinieren von sozialen und lokalen Anmeldekonten</span><span class="sxs-lookup"><span data-stu-id="82a69-318">Combine social and local login accounts</span></span>

<span data-ttu-id="82a69-319">Zum Durchführen dieses Abschnitts müssen Sie zuerst einen externen Authentifizierungs Anbieter aktivieren.</span><span class="sxs-lookup"><span data-stu-id="82a69-319">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="82a69-320">Weitere Informationen finden Sie unter [Authentifizierung für Facebook, Google und externe Anbieter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="82a69-320">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="82a69-321">Sie können lokale und soziale Konten kombinieren, indem Sie auf Ihren e-Mail-Link klicken.</span><span class="sxs-lookup"><span data-stu-id="82a69-321">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="82a69-322">In der folgenden Sequenz wird " RickAndMSFT@gmail.com " zuerst als lokale Anmeldung erstellt. Sie können das Konto jedoch zuerst als Social Login (Social Login) erstellen und dann einen lokalen Anmelde Namen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="82a69-322">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webanwendung: RickAndMSFT@gmail.com Benutzer authentifiziert](accconfirm/_static/rick.png)

<span data-ttu-id="82a69-324">Klicken Sie auf den Link **Verwalten** .</span><span class="sxs-lookup"><span data-stu-id="82a69-324">Click on the **Manage** link.</span></span> <span data-ttu-id="82a69-325">Beachten Sie die 0 (null) externen (Anmeldungen), die diesem Konto zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="82a69-325">Note the 0 external (social logins) associated with this account.</span></span>

![Ansicht verwalten](accconfirm/_static/manage.png)

<span data-ttu-id="82a69-327">Klicken Sie auf den Link zu einem anderen Anmeldedienst, und akzeptieren Sie die APP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="82a69-327">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="82a69-328">In der folgenden Abbildung ist Facebook der externe Authentifizierungs Anbieter:</span><span class="sxs-lookup"><span data-stu-id="82a69-328">In the following image, Facebook is the external authentication provider:</span></span>

![Verwalten der Ansicht externer Anmeldungen mit Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="82a69-330">Die beiden Konten wurden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="82a69-330">The two accounts have been combined.</span></span> <span data-ttu-id="82a69-331">Sie können sich mit einem der beiden Konten anmelden.</span><span class="sxs-lookup"><span data-stu-id="82a69-331">You are able to sign in with either account.</span></span> <span data-ttu-id="82a69-332">Möglicherweise möchten Sie, dass Ihre Benutzer lokale Konten hinzufügen, falls der Authentifizierungsdienst für soziale Anmelde Informationen ausgefallen ist oder Sie den Zugriff auf Ihr Social Media-Konto verlieren.</span><span class="sxs-lookup"><span data-stu-id="82a69-332">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="82a69-333">Aktivieren der Konto Bestätigung nach einem Standort Benutzer</span><span class="sxs-lookup"><span data-stu-id="82a69-333">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="82a69-334">Das Aktivieren der Konto Bestätigung auf einem Standort mit Benutzern sperrt alle vorhandenen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="82a69-334">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="82a69-335">Vorhandene Benutzer werden gesperrt, da ihre Konten nicht bestätigt werden.</span><span class="sxs-lookup"><span data-stu-id="82a69-335">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="82a69-336">Verwenden Sie einen der folgenden Ansätze, um die vorhandene Benutzer Sperre zu umgehen:</span><span class="sxs-lookup"><span data-stu-id="82a69-336">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="82a69-337">Aktualisieren Sie die Datenbank, um alle vorhandenen Benutzer als bestätigt zu markieren.</span><span class="sxs-lookup"><span data-stu-id="82a69-337">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="82a69-338">Vorhandene Benutzer bestätigen.</span><span class="sxs-lookup"><span data-stu-id="82a69-338">Confirm existing users.</span></span> <span data-ttu-id="82a69-339">Beispiel: Senden von e-Mails mit Bestätigungs Links per Batch.</span><span class="sxs-lookup"><span data-stu-id="82a69-339">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
