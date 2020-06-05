---
title: Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.net Core-App mit e-Mail-Bestätigung und Kenn Wort Zurücksetzung
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: d5e0e3865702fe4e5cbe49e7f452f367a8a53de9
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84451744"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="58061-103">Account confirmation and password recovery in ASP.NET Core (Kontobestätigung und Kennwortwiederherstellung in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="58061-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="58061-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="58061-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="58061-105">Dieses Tutorial zeigt, wie Sie eine ASP.net Core-App mit e-Mail-Bestätigung und Kenn Wort Zurücksetzung erstellen.</span><span class="sxs-lookup"><span data-stu-id="58061-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="58061-106">Dieses Tutorial ist **kein** Thema.</span><span class="sxs-lookup"><span data-stu-id="58061-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="58061-107">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="58061-107">You should be familiar with:</span></span>

* [<span data-ttu-id="58061-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="58061-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="58061-109">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="58061-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="58061-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="58061-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="58061-111">Informationen zum ASP.net Core 1,1-Version finden Sie in [dieser PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) .</span><span class="sxs-lookup"><span data-stu-id="58061-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="58061-112">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="58061-112">Prerequisites</span></span>

[<span data-ttu-id="58061-113">.NET Core 3.0 SDK oder höher</span><span class="sxs-lookup"><span data-stu-id="58061-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="58061-114">Erstellen und Testen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="58061-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="58061-115">Führen Sie die folgenden Befehle aus, um eine Web-App mit Authentifizierung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="58061-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="58061-116">Führen Sie die APP aus, wählen Sie den Link **registrieren** aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="58061-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="58061-117">Nach der Registrierung werden Sie zur Seite an weitergeleitet, `/Identity/Account/RegisterConfirmation` die einen Link zum Simulieren einer e-Mail-Bestätigung enthält:</span><span class="sxs-lookup"><span data-stu-id="58061-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="58061-118">Wählen Sie den `Click here to confirm your account` Link aus.</span><span class="sxs-lookup"><span data-stu-id="58061-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="58061-119">Wählen Sie den **Anmelde** Link aus, und melden Sie sich mit denselben Anmelde Informationen an.</span><span class="sxs-lookup"><span data-stu-id="58061-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="58061-120">Wählen Sie den `Hello YourEmail@provider.com!` Link aus, der Sie zur Seite umleitet `/Identity/Account/Manage/PersonalData` .</span><span class="sxs-lookup"><span data-stu-id="58061-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="58061-121">Wählen Sie auf der linken Seite die Registerkarte **persönliche Daten** aus, und klicken Sie dann auf **Löschen**.</span><span class="sxs-lookup"><span data-stu-id="58061-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="58061-122">Konfigurieren eines e-Mail-Anbieters</span><span class="sxs-lookup"><span data-stu-id="58061-122">Configure an email provider</span></span>

<span data-ttu-id="58061-123">In diesem Tutorial wird [sendgrid](https://sendgrid.com) zum Senden von e-Mails verwendet.</span><span class="sxs-lookup"><span data-stu-id="58061-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="58061-124">Sie benötigen ein sendgrid-Konto und einen Schlüssel zum Senden von e-Mails.</span><span class="sxs-lookup"><span data-stu-id="58061-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="58061-125">Sie können andere e-Mail-Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="58061-125">You can use other email providers.</span></span> <span data-ttu-id="58061-126">Es wird empfohlen, sendgrid oder einen anderen e-Mail-Dienst zum Senden von e-Mails zu verwenden</span><span class="sxs-lookup"><span data-stu-id="58061-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="58061-127">SMTP ist schwierig zu sichern und ordnungsgemäß eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="58061-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="58061-128">Das sendgrid-Konto Ich benötige das [Hinzufügen eines Absenders](https://sendgrid.com/docs/ui/sending-email/senders/).</span><span class="sxs-lookup"><span data-stu-id="58061-128">The SendGrid account my require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="58061-129">Erstellen Sie eine Klasse zum Abrufen des Schlüssels für sichere e-Mails.</span><span class="sxs-lookup"><span data-stu-id="58061-129">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="58061-130">Erstellen Sie für dieses Beispiel *Dienste/authmessagesenderoptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="58061-130">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="58061-131">Konfigurieren von sendgrid-Benutzer Geheimnissen</span><span class="sxs-lookup"><span data-stu-id="58061-131">Configure SendGrid user secrets</span></span>

<span data-ttu-id="58061-132">Legen Sie `SendGridUser` und `SendGridKey` mit dem [Secret-Manager-Tool](xref:security/app-secrets)fest.</span><span class="sxs-lookup"><span data-stu-id="58061-132">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="58061-133">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="58061-133">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="58061-134">Unter Windows speichert der Secret Manager Schlüssel/Wert-Paare in der Datei " *Secrets. JSON* " im `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="58061-134">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="58061-135">Der Inhalt der Datei " *Secrets. JSON* " wird nicht verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="58061-135">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="58061-136">Das folgende Markup zeigt die Datei " *Secrets. JSON* ".</span><span class="sxs-lookup"><span data-stu-id="58061-136">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="58061-137">Der `SendGridKey` Wert wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="58061-137">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="58061-138">Weitere Informationen finden Sie im [options Muster](xref:fundamentals/configuration/options) und in der [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="58061-138">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="58061-139">Installieren von sendgrid</span><span class="sxs-lookup"><span data-stu-id="58061-139">Install SendGrid</span></span>

<span data-ttu-id="58061-140">In diesem Tutorial wird gezeigt, wie Sie e-Mail-Benachrichtigungen über [sendgrid](https://sendgrid.com/)hinzufügen, aber Sie können e-Mails mithilfe von SMTP und anderen Mechanismen senden.</span><span class="sxs-lookup"><span data-stu-id="58061-140">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="58061-141">Installieren Sie das `SendGrid` nuget-Paket:</span><span class="sxs-lookup"><span data-stu-id="58061-141">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58061-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58061-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58061-143">Geben Sie in der Paket-Manager-Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="58061-143">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="58061-144">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="58061-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="58061-145">Geben Sie in der Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="58061-145">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="58061-146">Weitere Informationen zur Registrierung für ein kostenloses sendgrid-Konto finden [Sie unter Get Started with sendgrid](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="58061-146">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="58061-147">Implementieren von iemailsender</span><span class="sxs-lookup"><span data-stu-id="58061-147">Implement IEmailSender</span></span>

<span data-ttu-id="58061-148">Erstellen Sie zum Implementieren von `IEmailSender` *Services/emailsender. cs* mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="58061-148">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="58061-149">Konfigurieren des Starts zur Unterstützung von e-Mails</span><span class="sxs-lookup"><span data-stu-id="58061-149">Configure startup to support email</span></span>

<span data-ttu-id="58061-150">Fügen Sie der- `ConfigureServices` Methode in der Datei *Startup.cs* den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="58061-150">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="58061-151">Fügen Sie `EmailSender` als vorübergehenden Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="58061-151">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="58061-152">Registrieren Sie die `AuthMessageSenderOptions` Konfigurations Instanz.</span><span class="sxs-lookup"><span data-stu-id="58061-152">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="58061-153">Registrieren, bestätigen der e-Mail und Zurücksetzen des Kennworts</span><span class="sxs-lookup"><span data-stu-id="58061-153">Register, confirm email, and reset password</span></span>

<span data-ttu-id="58061-154">Führen Sie die Web-App aus, und testen Sie den Ablauf der Konto Bestätigung und Kenn Wort Wiederherstellung.</span><span class="sxs-lookup"><span data-stu-id="58061-154">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="58061-155">Ausführen der APP und Registrieren eines neuen Benutzers</span><span class="sxs-lookup"><span data-stu-id="58061-155">Run the app and register a new user</span></span>
* <span data-ttu-id="58061-156">Überprüfen Sie Ihre e-Mail auf den Link zur Konto Bestätigung.</span><span class="sxs-lookup"><span data-stu-id="58061-156">Check your email for the account confirmation link.</span></span> <span data-ttu-id="58061-157">Siehe [Debuggen von e-Mails](#debug) , wenn Sie die e-Mail nicht erhalten.</span><span class="sxs-lookup"><span data-stu-id="58061-157">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="58061-158">Klicken Sie auf den Link, um Ihre e-Mail zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="58061-158">Click the link to confirm your email.</span></span>
* <span data-ttu-id="58061-159">Melden Sie sich mit Ihrer e-Mail und dem Kennwort an.</span><span class="sxs-lookup"><span data-stu-id="58061-159">Sign in with your email and password.</span></span>
* <span data-ttu-id="58061-160">Melden Sie sich ab.</span><span class="sxs-lookup"><span data-stu-id="58061-160">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="58061-161">Kenn Wort Zurücksetzung testen</span><span class="sxs-lookup"><span data-stu-id="58061-161">Test password reset</span></span>

* <span data-ttu-id="58061-162">Wenn Sie angemeldet sind, wählen Sie **Abmelden aus**.</span><span class="sxs-lookup"><span data-stu-id="58061-162">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="58061-163">Wählen Sie den Link **Anmelden aus** , und wählen Sie den Link **Kennwort vergessen?** aus.</span><span class="sxs-lookup"><span data-stu-id="58061-163">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="58061-164">Geben Sie die e-Mail ein, die Sie zum Registrieren des Kontos verwendet haben.</span><span class="sxs-lookup"><span data-stu-id="58061-164">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="58061-165">Eine e-Mail mit einem Link zum Zurücksetzen Ihres Kennworts wird gesendet.</span><span class="sxs-lookup"><span data-stu-id="58061-165">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="58061-166">Überprüfen Sie Ihre e-Mail, und klicken Sie auf den Link zum Zurücksetzen Ihres Kennworts</span><span class="sxs-lookup"><span data-stu-id="58061-166">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="58061-167">Nachdem das Kennwort erfolgreich zurückgesetzt wurde, können Sie sich mit Ihrer e-Mail-Adresse und dem neuen Kennwort anmelden.</span><span class="sxs-lookup"><span data-stu-id="58061-167">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="58061-168">E-Mail-und Aktivitäts Timeout ändern</span><span class="sxs-lookup"><span data-stu-id="58061-168">Change email and activity timeout</span></span>

<span data-ttu-id="58061-169">Das standardmäßige Inaktivitäts Timeout beträgt 14 Tage.</span><span class="sxs-lookup"><span data-stu-id="58061-169">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="58061-170">Mit dem folgenden Code wird das Inaktivitäts Timeout auf 5 Tage festgelegt:</span><span class="sxs-lookup"><span data-stu-id="58061-170">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="58061-171">Alle Datenschutz Token-Lebensdauer ändern</span><span class="sxs-lookup"><span data-stu-id="58061-171">Change all data protection token lifespans</span></span>

<span data-ttu-id="58061-172">Der folgende Code ändert den Timeout Zeitraum für alle Token für die Datenschutz Token in 3 Stunden:</span><span class="sxs-lookup"><span data-stu-id="58061-172">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="58061-173">Die integrierten Identity Benutzer Token (siehe [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) haben ein [Timeout von einem Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="58061-173">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="58061-174">Lebensdauer von e-Mail-Token</span><span class="sxs-lookup"><span data-stu-id="58061-174">Change the email token lifespan</span></span>

<span data-ttu-id="58061-175">Die standardmäßige tokengültigkeits Dauer der [ Identity Benutzer Token](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ist [ein Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="58061-175">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="58061-176">In diesem Abschnitt wird gezeigt, wie Sie die Lebensdauer von e-Mail-</span><span class="sxs-lookup"><span data-stu-id="58061-176">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="58061-177">Fügen Sie einen benutzerdefinierten [dataprotectortokenprovider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) und Folgendes hinzu <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="58061-177">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="58061-178">Fügen Sie dem Dienst Container den benutzerdefinierten Anbieter hinzu:</span><span class="sxs-lookup"><span data-stu-id="58061-178">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="58061-179">E-Mail-Bestätigung erneut senden</span><span class="sxs-lookup"><span data-stu-id="58061-179">Resend email confirmation</span></span>

<span data-ttu-id="58061-180">Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="58061-180">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="58061-181">E-Mail Debuggen</span><span class="sxs-lookup"><span data-stu-id="58061-181">Debug email</span></span>

<span data-ttu-id="58061-182">Wenn Sie keine e-Mail mehr erhalten können:</span><span class="sxs-lookup"><span data-stu-id="58061-182">If you can't get email working:</span></span>

* <span data-ttu-id="58061-183">Legen Sie einen Haltepunkt fest `EmailSender.Execute` , um zu überprüfen, ob `SendGridClient.SendEmailAsync` aufgerufen wird</span><span class="sxs-lookup"><span data-stu-id="58061-183">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="58061-184">Erstellen [Sie eine Konsolen-App zum Senden von e-Mails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mithilfe eines ähnlichen Codes an `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="58061-184">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="58061-185">Lesen Sie die Seite [e-Mail-Aktivität](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="58061-185">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="58061-186">Überprüfen Sie Ihren Spam-Ordner.</span><span class="sxs-lookup"><span data-stu-id="58061-186">Check your spam folder.</span></span>
* <span data-ttu-id="58061-187">Probieren Sie einen anderen e-Mail-Alias für einen anderen e-Mail-Anbieter aus (Microsoft, Yahoo, Gmail usw.)</span><span class="sxs-lookup"><span data-stu-id="58061-187">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="58061-188">Senden Sie an verschiedene e-Mail-Konten.</span><span class="sxs-lookup"><span data-stu-id="58061-188">Try sending to different email accounts.</span></span>

<span data-ttu-id="58061-189">**Eine bewährte Sicherheits** Maßnahme besteht darin, **keine** Produktionsgeheimnisse in Test-und Entwicklungsumgebungen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="58061-189">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="58061-190">Wenn Sie die app in Azure veröffentlichen, legen Sie die sendgrid-Geheimnisse als Anwendungseinstellungen im Azure-Web-App-Portal fest.</span><span class="sxs-lookup"><span data-stu-id="58061-190">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="58061-191">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="58061-191">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="58061-192">Kombinieren von sozialen und lokalen Anmeldekonten</span><span class="sxs-lookup"><span data-stu-id="58061-192">Combine social and local login accounts</span></span>

<span data-ttu-id="58061-193">Zum Durchführen dieses Abschnitts müssen Sie zuerst einen externen Authentifizierungs Anbieter aktivieren.</span><span class="sxs-lookup"><span data-stu-id="58061-193">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="58061-194">Weitere Informationen finden Sie unter [Authentifizierung für Facebook, Google und externe Anbieter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="58061-194">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="58061-195">Sie können lokale und soziale Konten kombinieren, indem Sie auf Ihren e-Mail-Link klicken.</span><span class="sxs-lookup"><span data-stu-id="58061-195">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="58061-196">In der folgenden Sequenz wird " RickAndMSFT@gmail.com " zuerst als lokale Anmeldung erstellt. Sie können das Konto jedoch zuerst als Social Login (Social Login) erstellen und dann einen lokalen Anmelde Namen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="58061-196">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webanwendung: RickAndMSFT@gmail.com Benutzer authentifiziert](accconfirm/_static/rick.png)

<span data-ttu-id="58061-198">Klicken Sie auf den Link **Verwalten** .</span><span class="sxs-lookup"><span data-stu-id="58061-198">Click on the **Manage** link.</span></span> <span data-ttu-id="58061-199">Beachten Sie die 0 (null) externen (Anmeldungen), die diesem Konto zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="58061-199">Note the 0 external (social logins) associated with this account.</span></span>

![Ansicht verwalten](accconfirm/_static/manage.png)

<span data-ttu-id="58061-201">Klicken Sie auf den Link zu einem anderen Anmeldedienst, und akzeptieren Sie die APP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="58061-201">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="58061-202">In der folgenden Abbildung ist Facebook der externe Authentifizierungs Anbieter:</span><span class="sxs-lookup"><span data-stu-id="58061-202">In the following image, Facebook is the external authentication provider:</span></span>

![Verwalten der Ansicht externer Anmeldungen mit Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="58061-204">Die beiden Konten wurden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="58061-204">The two accounts have been combined.</span></span> <span data-ttu-id="58061-205">Sie können sich mit einem der beiden Konten anmelden.</span><span class="sxs-lookup"><span data-stu-id="58061-205">You are able to sign in with either account.</span></span> <span data-ttu-id="58061-206">Möglicherweise möchten Sie, dass Ihre Benutzer lokale Konten hinzufügen, falls der Authentifizierungsdienst für soziale Anmelde Informationen ausgefallen ist oder Sie den Zugriff auf Ihr Social Media-Konto verlieren.</span><span class="sxs-lookup"><span data-stu-id="58061-206">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="58061-207">Aktivieren der Konto Bestätigung nach einem Standort Benutzer</span><span class="sxs-lookup"><span data-stu-id="58061-207">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="58061-208">Das Aktivieren der Konto Bestätigung auf einem Standort mit Benutzern sperrt alle vorhandenen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="58061-208">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="58061-209">Vorhandene Benutzer werden gesperrt, da ihre Konten nicht bestätigt werden.</span><span class="sxs-lookup"><span data-stu-id="58061-209">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="58061-210">Verwenden Sie einen der folgenden Ansätze, um die vorhandene Benutzer Sperre zu umgehen:</span><span class="sxs-lookup"><span data-stu-id="58061-210">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="58061-211">Aktualisieren Sie die Datenbank, um alle vorhandenen Benutzer als bestätigt zu markieren.</span><span class="sxs-lookup"><span data-stu-id="58061-211">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="58061-212">Vorhandene Benutzer bestätigen.</span><span class="sxs-lookup"><span data-stu-id="58061-212">Confirm existing users.</span></span> <span data-ttu-id="58061-213">Beispiel: Senden von e-Mails mit Bestätigungs Links per Batch.</span><span class="sxs-lookup"><span data-stu-id="58061-213">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="58061-214">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="58061-214">Prerequisites</span></span>

[<span data-ttu-id="58061-215">.Net Core 2,2 SDK oder höher</span><span class="sxs-lookup"><span data-stu-id="58061-215">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="58061-216">Erstellen einer Web-App und eines GerüstsIdentity</span><span class="sxs-lookup"><span data-stu-id="58061-216">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="58061-217">Führen Sie die folgenden Befehle aus, um eine Web-App mit Authentifizierung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="58061-217">Run the following commands to create a web app with authentication.</span></span>

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

## <a name="test-new-user-registration"></a><span data-ttu-id="58061-218">Neue Benutzerregistrierung testen</span><span class="sxs-lookup"><span data-stu-id="58061-218">Test new user registration</span></span>

<span data-ttu-id="58061-219">Führen Sie die APP aus, wählen Sie den Link **registrieren** aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="58061-219">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="58061-220">An diesem Punkt wird nur die e-Mail-Überprüfung mit dem- [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) Attribut durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="58061-220">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="58061-221">Nachdem Sie die Registrierung übermittelt haben, werden Sie bei der App angemeldet.</span><span class="sxs-lookup"><span data-stu-id="58061-221">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="58061-222">Später in diesem Tutorial wird der Code aktualisiert, sodass neue Benutzer sich erst anmelden können, wenn Ihre e-Mail-Nachricht überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="58061-222">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="58061-223">Beachten Sie, dass das Feld der Tabelle `EmailConfirmed` ist `False` .</span><span class="sxs-lookup"><span data-stu-id="58061-223">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="58061-224">Sie sollten diese e-Mail im nächsten Schritt erneut verwenden, wenn die APP eine Bestätigungs-e-Mail sendet.</span><span class="sxs-lookup"><span data-stu-id="58061-224">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="58061-225">Klicken Sie mit der rechten Maustaste auf die Zeile, und wählen Sie **Löschen**.</span><span class="sxs-lookup"><span data-stu-id="58061-225">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="58061-226">Wenn Sie den e-Mail-Alias löschen, wird dies in den folgenden Schritten vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="58061-226">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="58061-227">Bestätigung per e-Mail erforderlich</span><span class="sxs-lookup"><span data-stu-id="58061-227">Require email confirmation</span></span>

<span data-ttu-id="58061-228">Es wird empfohlen, die e-Mail-Adresse einer neuen Benutzerregistrierung zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="58061-228">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="58061-229">Mithilfe der e-Mail-Bestätigung können Sie sicherstellen, dass Sie keine andere Identität annehmen (d. h., Sie haben sich nicht bei einer anderen e-Mail registriert).</span><span class="sxs-lookup"><span data-stu-id="58061-229">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="58061-230">Angenommen, Sie hatten ein Diskussionsforum, und Sie wollten verhindern, dass sich " yli@example.com " als "" registriert nolivetto@contoso.com .</span><span class="sxs-lookup"><span data-stu-id="58061-230">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="58061-231">Ohne e-Mail-Bestätigung kann " nolivetto@contoso.com " unerwünschte e-Mails von Ihrer APP erhalten.</span><span class="sxs-lookup"><span data-stu-id="58061-231">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="58061-232">Angenommen, der Benutzer hat sich versehentlich als " ylo@example.com " registriert und die falsche Schreibweise von "Yli" nicht bemerkt.</span><span class="sxs-lookup"><span data-stu-id="58061-232">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="58061-233">Sie sind nicht in der Lage, die Kenn Wort Wiederherstellung zu verwenden, da die APP nicht über die richtige e-Mail verfügt</span><span class="sxs-lookup"><span data-stu-id="58061-233">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="58061-234">E-Mail-Bestätigung bietet eingeschränkten Schutz vor Bots.</span><span class="sxs-lookup"><span data-stu-id="58061-234">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="58061-235">E-Mail-Bestätigung bietet keinen Schutz vor böswilligen Benutzern mit vielen e-Mail-Konten</span><span class="sxs-lookup"><span data-stu-id="58061-235">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="58061-236">In der Regel möchten Sie, dass neue Benutzerdaten an Ihrer Website veröffentlichen, bevor Sie eine bestätigte e-Mail-Adresse haben.</span><span class="sxs-lookup"><span data-stu-id="58061-236">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="58061-237">Aktualisieren `Startup.ConfigureServices` Sie, um eine bestätigte e-Mail anzufordern:</span><span class="sxs-lookup"><span data-stu-id="58061-237">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="58061-238">`config.SignIn.RequireConfirmedEmail = true;`verhindert, dass registrierte Benutzer sich anmelden, bis Ihre e-Mail-Nachricht bestätigt wird.</span><span class="sxs-lookup"><span data-stu-id="58061-238">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="58061-239">E-Mail-Anbieter</span><span class="sxs-lookup"><span data-stu-id="58061-239">Configure email provider</span></span>

<span data-ttu-id="58061-240">In diesem Tutorial wird [sendgrid](https://sendgrid.com) zum Senden von e-Mails verwendet.</span><span class="sxs-lookup"><span data-stu-id="58061-240">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="58061-241">Sie benötigen ein sendgrid-Konto und einen Schlüssel zum Senden von e-Mails.</span><span class="sxs-lookup"><span data-stu-id="58061-241">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="58061-242">Sie können andere e-Mail-Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="58061-242">You can use other email providers.</span></span> <span data-ttu-id="58061-243">ASP.net Core 2. x umfasst `System.Net.Mail` , das Ihnen ermöglicht, e-Mails von Ihrer APP zu senden.</span><span class="sxs-lookup"><span data-stu-id="58061-243">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="58061-244">Es wird empfohlen, sendgrid oder einen anderen e-Mail-Dienst zum Senden von e-Mails zu verwenden</span><span class="sxs-lookup"><span data-stu-id="58061-244">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="58061-245">SMTP ist schwierig zu sichern und ordnungsgemäß eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="58061-245">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="58061-246">Erstellen Sie eine Klasse zum Abrufen des Schlüssels für sichere e-Mails.</span><span class="sxs-lookup"><span data-stu-id="58061-246">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="58061-247">Erstellen Sie für dieses Beispiel *Dienste/authmessagesenderoptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="58061-247">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="58061-248">Konfigurieren von sendgrid-Benutzer Geheimnissen</span><span class="sxs-lookup"><span data-stu-id="58061-248">Configure SendGrid user secrets</span></span>

<span data-ttu-id="58061-249">Legen Sie `SendGridUser` und `SendGridKey` mit dem [Secret-Manager-Tool](xref:security/app-secrets)fest.</span><span class="sxs-lookup"><span data-stu-id="58061-249">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="58061-250">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="58061-250">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="58061-251">Unter Windows speichert der Secret Manager Schlüssel/Wert-Paare in der Datei " *Secrets. JSON* " im `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="58061-251">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="58061-252">Der Inhalt der Datei " *Secrets. JSON* " wird nicht verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="58061-252">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="58061-253">Das folgende Markup zeigt die Datei " *Secrets. JSON* ".</span><span class="sxs-lookup"><span data-stu-id="58061-253">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="58061-254">Der `SendGridKey` Wert wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="58061-254">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="58061-255">Weitere Informationen finden Sie im [options Muster](xref:fundamentals/configuration/options) und in der [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="58061-255">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="58061-256">Installieren von sendgrid</span><span class="sxs-lookup"><span data-stu-id="58061-256">Install SendGrid</span></span>

<span data-ttu-id="58061-257">In diesem Tutorial wird gezeigt, wie Sie e-Mail-Benachrichtigungen über [sendgrid](https://sendgrid.com/)hinzufügen, aber Sie können e-Mails mithilfe von SMTP und anderen Mechanismen senden.</span><span class="sxs-lookup"><span data-stu-id="58061-257">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="58061-258">Installieren Sie das `SendGrid` nuget-Paket:</span><span class="sxs-lookup"><span data-stu-id="58061-258">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58061-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58061-259">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="58061-260">Geben Sie in der Paket-Manager-Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="58061-260">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="58061-261">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="58061-261">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="58061-262">Geben Sie in der Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="58061-262">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="58061-263">Weitere Informationen zur Registrierung für ein kostenloses sendgrid-Konto finden [Sie unter Get Started with sendgrid](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="58061-263">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="58061-264">Implementieren von iemailsender</span><span class="sxs-lookup"><span data-stu-id="58061-264">Implement IEmailSender</span></span>

<span data-ttu-id="58061-265">Erstellen Sie zum Implementieren von `IEmailSender` *Services/emailsender. cs* mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="58061-265">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="58061-266">Konfigurieren des Starts zur Unterstützung von e-Mails</span><span class="sxs-lookup"><span data-stu-id="58061-266">Configure startup to support email</span></span>

<span data-ttu-id="58061-267">Fügen Sie der- `ConfigureServices` Methode in der Datei *Startup.cs* den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="58061-267">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="58061-268">Fügen Sie `EmailSender` als vorübergehenden Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="58061-268">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="58061-269">Registrieren Sie die `AuthMessageSenderOptions` Konfigurations Instanz.</span><span class="sxs-lookup"><span data-stu-id="58061-269">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="58061-270">Aktivieren der Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="58061-270">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="58061-271">Die Vorlage enthält den Code für die Konto Bestätigung und Kenn Wort Wiederherstellung.</span><span class="sxs-lookup"><span data-stu-id="58061-271">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="58061-272">Suchen Sie die `OnPostAsync` -Methode in " *Areas/ Identity /pages/Account/Register.cshtml.cs*".</span><span class="sxs-lookup"><span data-stu-id="58061-272">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="58061-273">Verhindern Sie, dass neu registrierte Benutzer automatisch angemeldet werden, indem Sie die folgende Zeile auskommentieren:</span><span class="sxs-lookup"><span data-stu-id="58061-273">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="58061-274">Die gesamte-Methode wird angezeigt, wenn die geänderte Zeile hervorgehoben ist:</span><span class="sxs-lookup"><span data-stu-id="58061-274">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="58061-275">Registrieren, bestätigen der e-Mail und Zurücksetzen des Kennworts</span><span class="sxs-lookup"><span data-stu-id="58061-275">Register, confirm email, and reset password</span></span>

<span data-ttu-id="58061-276">Führen Sie die Web-App aus, und testen Sie den Ablauf der Konto Bestätigung und Kenn Wort Wiederherstellung.</span><span class="sxs-lookup"><span data-stu-id="58061-276">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="58061-277">Ausführen der APP und Registrieren eines neuen Benutzers</span><span class="sxs-lookup"><span data-stu-id="58061-277">Run the app and register a new user</span></span>
* <span data-ttu-id="58061-278">Überprüfen Sie Ihre e-Mail auf den Link zur Konto Bestätigung.</span><span class="sxs-lookup"><span data-stu-id="58061-278">Check your email for the account confirmation link.</span></span> <span data-ttu-id="58061-279">Siehe [Debuggen von e-Mails](#debug) , wenn Sie die e-Mail nicht erhalten.</span><span class="sxs-lookup"><span data-stu-id="58061-279">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="58061-280">Klicken Sie auf den Link, um Ihre e-Mail zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="58061-280">Click the link to confirm your email.</span></span>
* <span data-ttu-id="58061-281">Melden Sie sich mit Ihrer e-Mail und dem Kennwort an.</span><span class="sxs-lookup"><span data-stu-id="58061-281">Sign in with your email and password.</span></span>
* <span data-ttu-id="58061-282">Melden Sie sich ab.</span><span class="sxs-lookup"><span data-stu-id="58061-282">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="58061-283">Anzeigen der Seite "verwalten"</span><span class="sxs-lookup"><span data-stu-id="58061-283">View the manage page</span></span>

<span data-ttu-id="58061-284">Wählen Sie Ihren Benutzernamen im Browser aus: ![ Browserfenster mit Benutzername](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="58061-284">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="58061-285">Die Seite verwalten wird angezeigt, und die Registerkarte **Profil** ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="58061-285">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="58061-286">Die **e-Mail** zeigt ein Kontrollkästchen an, das anzeigt, dass die e-Mail bestätigt wurde.</span><span class="sxs-lookup"><span data-stu-id="58061-286">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="58061-287">Kenn Wort Zurücksetzung testen</span><span class="sxs-lookup"><span data-stu-id="58061-287">Test password reset</span></span>

* <span data-ttu-id="58061-288">Wenn Sie angemeldet sind, wählen Sie **Abmelden aus**.</span><span class="sxs-lookup"><span data-stu-id="58061-288">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="58061-289">Wählen Sie den Link **Anmelden aus** , und wählen Sie den Link **Kennwort vergessen?** aus.</span><span class="sxs-lookup"><span data-stu-id="58061-289">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="58061-290">Geben Sie die e-Mail ein, die Sie zum Registrieren des Kontos verwendet haben.</span><span class="sxs-lookup"><span data-stu-id="58061-290">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="58061-291">Eine e-Mail mit einem Link zum Zurücksetzen Ihres Kennworts wird gesendet.</span><span class="sxs-lookup"><span data-stu-id="58061-291">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="58061-292">Überprüfen Sie Ihre e-Mail, und klicken Sie auf den Link zum Zurücksetzen Ihres Kennworts</span><span class="sxs-lookup"><span data-stu-id="58061-292">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="58061-293">Nachdem das Kennwort erfolgreich zurückgesetzt wurde, können Sie sich mit Ihrer e-Mail-Adresse und dem neuen Kennwort anmelden.</span><span class="sxs-lookup"><span data-stu-id="58061-293">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="58061-294">E-Mail-und Aktivitäts Timeout ändern</span><span class="sxs-lookup"><span data-stu-id="58061-294">Change email and activity timeout</span></span>

<span data-ttu-id="58061-295">Das standardmäßige Inaktivitäts Timeout beträgt 14 Tage.</span><span class="sxs-lookup"><span data-stu-id="58061-295">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="58061-296">Mit dem folgenden Code wird das Inaktivitäts Timeout auf 5 Tage festgelegt:</span><span class="sxs-lookup"><span data-stu-id="58061-296">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="58061-297">Alle Datenschutz Token-Lebensdauer ändern</span><span class="sxs-lookup"><span data-stu-id="58061-297">Change all data protection token lifespans</span></span>

<span data-ttu-id="58061-298">Der folgende Code ändert den Timeout Zeitraum für alle Token für die Datenschutz Token in 3 Stunden:</span><span class="sxs-lookup"><span data-stu-id="58061-298">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="58061-299">Die integrierten Identity Benutzer Token (siehe [aspnetcore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) haben ein [Timeout von einem Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="58061-299">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="58061-300">Lebensdauer von e-Mail-Token</span><span class="sxs-lookup"><span data-stu-id="58061-300">Change the email token lifespan</span></span>

<span data-ttu-id="58061-301">Die standardmäßige tokengültigkeits Dauer der [ Identity Benutzer Token](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ist [ein Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="58061-301">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="58061-302">In diesem Abschnitt wird gezeigt, wie Sie die Lebensdauer von e-Mail-</span><span class="sxs-lookup"><span data-stu-id="58061-302">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="58061-303">Fügen Sie einen benutzerdefinierten [dataprotectortokenprovider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) und Folgendes hinzu <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="58061-303">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="58061-304">Fügen Sie dem Dienst Container den benutzerdefinierten Anbieter hinzu:</span><span class="sxs-lookup"><span data-stu-id="58061-304">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="58061-305">E-Mail-Bestätigung erneut senden</span><span class="sxs-lookup"><span data-stu-id="58061-305">Resend email confirmation</span></span>

<span data-ttu-id="58061-306">Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="58061-306">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="58061-307">E-Mail Debuggen</span><span class="sxs-lookup"><span data-stu-id="58061-307">Debug email</span></span>

<span data-ttu-id="58061-308">Wenn Sie keine e-Mail mehr erhalten können:</span><span class="sxs-lookup"><span data-stu-id="58061-308">If you can't get email working:</span></span>

* <span data-ttu-id="58061-309">Legen Sie einen Haltepunkt fest `EmailSender.Execute` , um zu überprüfen, ob `SendGridClient.SendEmailAsync` aufgerufen wird</span><span class="sxs-lookup"><span data-stu-id="58061-309">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="58061-310">Erstellen [Sie eine Konsolen-App zum Senden von e-Mails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mithilfe eines ähnlichen Codes an `EmailSender.Execute` .</span><span class="sxs-lookup"><span data-stu-id="58061-310">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="58061-311">Lesen Sie die Seite [e-Mail-Aktivität](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="58061-311">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="58061-312">Überprüfen Sie Ihren Spam-Ordner.</span><span class="sxs-lookup"><span data-stu-id="58061-312">Check your spam folder.</span></span>
* <span data-ttu-id="58061-313">Probieren Sie einen anderen e-Mail-Alias für einen anderen e-Mail-Anbieter aus (Microsoft, Yahoo, Gmail usw.)</span><span class="sxs-lookup"><span data-stu-id="58061-313">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="58061-314">Senden Sie an verschiedene e-Mail-Konten.</span><span class="sxs-lookup"><span data-stu-id="58061-314">Try sending to different email accounts.</span></span>

<span data-ttu-id="58061-315">**Eine bewährte Sicherheits** Maßnahme besteht darin, **keine** Produktionsgeheimnisse in Test-und Entwicklungsumgebungen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="58061-315">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="58061-316">Wenn Sie die app in Azure veröffentlichen, können Sie die sendgrid-Geheimnisse als Anwendungseinstellungen im Azure-Web-App-Portal festlegen.</span><span class="sxs-lookup"><span data-stu-id="58061-316">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="58061-317">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="58061-317">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="58061-318">Kombinieren von sozialen und lokalen Anmeldekonten</span><span class="sxs-lookup"><span data-stu-id="58061-318">Combine social and local login accounts</span></span>

<span data-ttu-id="58061-319">Zum Durchführen dieses Abschnitts müssen Sie zuerst einen externen Authentifizierungs Anbieter aktivieren.</span><span class="sxs-lookup"><span data-stu-id="58061-319">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="58061-320">Weitere Informationen finden Sie unter [Authentifizierung für Facebook, Google und externe Anbieter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="58061-320">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="58061-321">Sie können lokale und soziale Konten kombinieren, indem Sie auf Ihren e-Mail-Link klicken.</span><span class="sxs-lookup"><span data-stu-id="58061-321">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="58061-322">In der folgenden Sequenz wird " RickAndMSFT@gmail.com " zuerst als lokale Anmeldung erstellt. Sie können das Konto jedoch zuerst als Social Login (Social Login) erstellen und dann einen lokalen Anmelde Namen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="58061-322">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webanwendung: RickAndMSFT@gmail.com Benutzer authentifiziert](accconfirm/_static/rick.png)

<span data-ttu-id="58061-324">Klicken Sie auf den Link **Verwalten** .</span><span class="sxs-lookup"><span data-stu-id="58061-324">Click on the **Manage** link.</span></span> <span data-ttu-id="58061-325">Beachten Sie die 0 (null) externen (Anmeldungen), die diesem Konto zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="58061-325">Note the 0 external (social logins) associated with this account.</span></span>

![Ansicht verwalten](accconfirm/_static/manage.png)

<span data-ttu-id="58061-327">Klicken Sie auf den Link zu einem anderen Anmeldedienst, und akzeptieren Sie die APP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="58061-327">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="58061-328">In der folgenden Abbildung ist Facebook der externe Authentifizierungs Anbieter:</span><span class="sxs-lookup"><span data-stu-id="58061-328">In the following image, Facebook is the external authentication provider:</span></span>

![Verwalten der Ansicht externer Anmeldungen mit Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="58061-330">Die beiden Konten wurden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="58061-330">The two accounts have been combined.</span></span> <span data-ttu-id="58061-331">Sie können sich mit einem der beiden Konten anmelden.</span><span class="sxs-lookup"><span data-stu-id="58061-331">You are able to sign in with either account.</span></span> <span data-ttu-id="58061-332">Möglicherweise möchten Sie, dass Ihre Benutzer lokale Konten hinzufügen, falls der Authentifizierungsdienst für soziale Anmelde Informationen ausgefallen ist oder Sie den Zugriff auf Ihr Social Media-Konto verlieren.</span><span class="sxs-lookup"><span data-stu-id="58061-332">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="58061-333">Aktivieren der Konto Bestätigung nach einem Standort Benutzer</span><span class="sxs-lookup"><span data-stu-id="58061-333">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="58061-334">Das Aktivieren der Konto Bestätigung auf einem Standort mit Benutzern sperrt alle vorhandenen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="58061-334">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="58061-335">Vorhandene Benutzer werden gesperrt, da ihre Konten nicht bestätigt werden.</span><span class="sxs-lookup"><span data-stu-id="58061-335">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="58061-336">Verwenden Sie einen der folgenden Ansätze, um die vorhandene Benutzer Sperre zu umgehen:</span><span class="sxs-lookup"><span data-stu-id="58061-336">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="58061-337">Aktualisieren Sie die Datenbank, um alle vorhandenen Benutzer als bestätigt zu markieren.</span><span class="sxs-lookup"><span data-stu-id="58061-337">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="58061-338">Vorhandene Benutzer bestätigen.</span><span class="sxs-lookup"><span data-stu-id="58061-338">Confirm existing users.</span></span> <span data-ttu-id="58061-339">Beispiel: Senden von e-Mails mit Bestätigungs Links per Batch.</span><span class="sxs-lookup"><span data-stu-id="58061-339">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
