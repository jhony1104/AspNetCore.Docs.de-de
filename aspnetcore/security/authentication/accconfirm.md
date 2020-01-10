---
title: Konto Bestätigung und Kenn Wort Wiederherstellung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie eine ASP.net Core-App mit e-Mail-Bestätigung und Kenn Wort Zurücksetzung
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 49d3d214fd64edc5b17df2df929ddc3c2af47ede
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829269"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="aa7a6-103">Konto Bestätigung und Kenn Wort Wiederherstellung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="aa7a6-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="aa7a6-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant)und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="aa7a6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="aa7a6-105">Dieses Tutorial zeigt, wie Sie eine ASP.net Core-App mit e-Mail-Bestätigung und Kenn Wort Zurücksetzung erstellen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="aa7a6-106">Dieses Tutorial ist **kein** Thema.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="aa7a6-107">Sie sollten mit vertraut sein:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-107">You should be familiar with:</span></span>

* [<span data-ttu-id="aa7a6-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa7a6-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="aa7a6-109">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="aa7a6-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="aa7a6-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="aa7a6-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="aa7a6-111">Informationen zum ASP.net Core 1,1-Version finden Sie in [dieser PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) .</span><span class="sxs-lookup"><span data-stu-id="aa7a6-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="aa7a6-112">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="aa7a6-112">Prerequisites</span></span>

[<span data-ttu-id="aa7a6-113">.NET Core 3.0 SDK oder höher</span><span class="sxs-lookup"><span data-stu-id="aa7a6-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="aa7a6-114">Erstellen und Testen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="aa7a6-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="aa7a6-115">Führen Sie die folgenden Befehle aus, um eine Web-App mit Authentifizierung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="aa7a6-116">Führen Sie die APP aus, wählen Sie den Link **registrieren** aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="aa7a6-117">Nach der Registrierung werden Sie zur Seite an `/Identity/Account/RegisterConfirmation` umgeleitet, die einen Link zum Simulieren einer e-Mail-Bestätigung enthält:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="aa7a6-118">Wählen Sie den `Click here to confirm your account` Link aus.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="aa7a6-119">Wählen Sie den **Anmelde** Link aus, und melden Sie sich mit denselben Anmelde Informationen an.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="aa7a6-120">Wählen Sie den `Hello YourEmail@provider.com!` Link aus, mit dem Sie zur Seite `/Identity/Account/Manage/PersonalData` umgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="aa7a6-121">Wählen Sie auf der linken Seite die Registerkarte **persönliche Daten** aus, und klicken Sie dann auf **Löschen**.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="aa7a6-122">Konfigurieren eines e-Mail-Anbieters</span><span class="sxs-lookup"><span data-stu-id="aa7a6-122">Configure an email provider</span></span>

<span data-ttu-id="aa7a6-123">In diesem Tutorial wird [sendgrid](https://sendgrid.com) zum Senden von e-Mails verwendet.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="aa7a6-124">Sie benötigen ein sendgrid-Konto und einen Schlüssel zum Senden von e-Mails.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="aa7a6-125">Sie können andere e-Mail-Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-125">You can use other email providers.</span></span> <span data-ttu-id="aa7a6-126">Es wird empfohlen, sendgrid oder einen anderen e-Mail-Dienst zum Senden von e-Mails zu verwenden</span><span class="sxs-lookup"><span data-stu-id="aa7a6-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="aa7a6-127">SMTP ist schwierig zu sichern und ordnungsgemäß eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="aa7a6-128">Erstellen Sie eine Klasse zum Abrufen des Schlüssels für sichere e-Mails.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="aa7a6-129">Erstellen Sie für dieses Beispiel *Dienste/authmessagesenderoptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="aa7a6-130">Konfigurieren von sendgrid-Benutzer Geheimnissen</span><span class="sxs-lookup"><span data-stu-id="aa7a6-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="aa7a6-131">Legen Sie die `SendGridUser` und `SendGridKey` mit dem [Secret-Manager-Tool](xref:security/app-secrets)fest.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="aa7a6-132">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="aa7a6-133">Unter Windows speichert Secret Manager Schlüssel/Wert-Paare in der Datei " *Secrets. JSON* " im `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="aa7a6-134">Der Inhalt der Datei " *Secrets. JSON* " wird nicht verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="aa7a6-135">Das folgende Markup zeigt die Datei " *Secrets. JSON* ".</span><span class="sxs-lookup"><span data-stu-id="aa7a6-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="aa7a6-136">Der `SendGridKey` Wert wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="aa7a6-137">Weitere Informationen finden Sie im [options Muster](xref:fundamentals/configuration/options) und in der [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="aa7a6-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="aa7a6-138">Installieren von sendgrid</span><span class="sxs-lookup"><span data-stu-id="aa7a6-138">Install SendGrid</span></span>

<span data-ttu-id="aa7a6-139">In diesem Tutorial wird gezeigt, wie Sie e-Mail-Benachrichtigungen über [sendgrid](https://sendgrid.com/)hinzufügen, aber Sie können e-Mails mithilfe von SMTP und anderen Mechanismen senden.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="aa7a6-140">Installieren Sie das `SendGrid` nuget-Paket:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aa7a6-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa7a6-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa7a6-142">Geben Sie in der Paket-Manager-Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="aa7a6-143">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="aa7a6-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="aa7a6-144">Geben Sie in der Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="aa7a6-145">Weitere Informationen zur Registrierung für ein kostenloses sendgrid-Konto finden [Sie unter Get Started with sendgrid](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="aa7a6-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="aa7a6-146">Implementieren von iemailsender</span><span class="sxs-lookup"><span data-stu-id="aa7a6-146">Implement IEmailSender</span></span>

<span data-ttu-id="aa7a6-147">Um `IEmailSender`zu implementieren, erstellen Sie *Services/emailsender. cs* mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="aa7a6-148">Konfigurieren des Starts zur Unterstützung von e-Mails</span><span class="sxs-lookup"><span data-stu-id="aa7a6-148">Configure startup to support email</span></span>

<span data-ttu-id="aa7a6-149">Fügen Sie der `ConfigureServices`-Methode in der Datei *Startup.cs* den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="aa7a6-150">Fügen Sie `EmailSender` als vorübergehenden Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="aa7a6-151">Registrieren Sie die `AuthMessageSenderOptions` Konfigurations Instanz.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="aa7a6-152">Registrieren, bestätigen der e-Mail und Zurücksetzen des Kennworts</span><span class="sxs-lookup"><span data-stu-id="aa7a6-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="aa7a6-153">Führen Sie die Web-App aus, und testen Sie den Ablauf der Konto Bestätigung und Kenn Wort Wiederherstellung.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="aa7a6-154">Die app ausführen und einen neuen Benutzer registrieren</span><span class="sxs-lookup"><span data-stu-id="aa7a6-154">Run the app and register a new user</span></span>
* <span data-ttu-id="aa7a6-155">Überprüfen Sie Ihre e-Mail auf den Link zur Konto Bestätigung.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="aa7a6-156">Siehe [Debuggen von e-Mails](#debug) , wenn Sie die e-Mail nicht erhalten.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="aa7a6-157">Klicken Sie auf den Link, um Ihre e-Mail zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="aa7a6-158">Melden Sie sich mit Ihrer e-Mail und dem Kennwort an.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="aa7a6-159">Melden Sie sich ab.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="aa7a6-160">Kenn Wort Zurücksetzung testen</span><span class="sxs-lookup"><span data-stu-id="aa7a6-160">Test password reset</span></span>

* <span data-ttu-id="aa7a6-161">Wenn Sie angemeldet sind, wählen Sie **Abmelden aus**.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="aa7a6-162">Wählen Sie den Link **Anmelden aus** , und wählen Sie den Link **Kennwort vergessen?** aus.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="aa7a6-163">Geben Sie die e-Mail ein, die Sie zum Registrieren des Kontos verwendet haben.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="aa7a6-164">Eine e-Mail mit einem Link zum Zurücksetzen Ihres Kennworts wird gesendet.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="aa7a6-165">Überprüfen Sie Ihre e-Mail, und klicken Sie auf den Link zum Zurücksetzen Ihres Kennworts</span><span class="sxs-lookup"><span data-stu-id="aa7a6-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="aa7a6-166">Nachdem das Kennwort erfolgreich zurückgesetzt wurde, können Sie sich mit Ihrer e-Mail-Adresse und dem neuen Kennwort anmelden.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="aa7a6-167">E-Mail-und Aktivitäts Timeout ändern</span><span class="sxs-lookup"><span data-stu-id="aa7a6-167">Change email and activity timeout</span></span>

<span data-ttu-id="aa7a6-168">Das standardmäßige Inaktivitäts Timeout beträgt 14 Tage.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="aa7a6-169">Mit dem folgenden Code wird das Inaktivitäts Timeout auf 5 Tage festgelegt:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="aa7a6-170">Alle Datenschutz Token-Lebensdauer ändern</span><span class="sxs-lookup"><span data-stu-id="aa7a6-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="aa7a6-171">Der folgende Code ändert den Timeout Zeitraum für alle Token für die Datenschutz Token in 3 Stunden:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="aa7a6-172">Die integrierten Identitäts Benutzer Token (siehe [aspnetcore/src/Identity/Extensions. Core/src/tokenoptions. cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) haben ein Timeout von einem [Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="aa7a6-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="aa7a6-173">Lebensdauer von e-Mail-Token</span><span class="sxs-lookup"><span data-stu-id="aa7a6-173">Change the email token lifespan</span></span>

<span data-ttu-id="aa7a6-174">Die standardmäßige tokengültigkeits Dauer der [Identitäts Benutzer Token](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ist [ein Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="aa7a6-174">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="aa7a6-175">In diesem Abschnitt wird gezeigt, wie Sie die Lebensdauer von e-Mail-</span><span class="sxs-lookup"><span data-stu-id="aa7a6-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="aa7a6-176">Fügen Sie einen benutzerdefinierten [dataprotectortokenprovider-\<tuser->](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) und <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="aa7a6-177">Fügen Sie dem Dienst Container den benutzerdefinierten Anbieter hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="aa7a6-178">E-Mail-Bestätigung erneut senden</span><span class="sxs-lookup"><span data-stu-id="aa7a6-178">Resend email confirmation</span></span>

<span data-ttu-id="aa7a6-179">Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="aa7a6-179">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="aa7a6-180">E-Mail Debuggen</span><span class="sxs-lookup"><span data-stu-id="aa7a6-180">Debug email</span></span>

<span data-ttu-id="aa7a6-181">Wenn Sie keine e-Mail mehr erhalten können:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-181">If you can't get email working:</span></span>

* <span data-ttu-id="aa7a6-182">Legen Sie in `EmailSender.Execute` einen Haltepunkt fest, um zu überprüfen, `SendGridClient.SendEmailAsync` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="aa7a6-183">Erstellen [Sie eine Konsolen-App zum Senden von e-Mails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mithilfe eines ähnlichen Codes an `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="aa7a6-184">Lesen Sie die Seite [e-Mail-Aktivität](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="aa7a6-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="aa7a6-185">Überprüfen Sie Ihren Spam-Ordner.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-185">Check your spam folder.</span></span>
* <span data-ttu-id="aa7a6-186">Probieren Sie einen anderen e-Mail-Alias für einen anderen e-Mail-Anbieter aus (Microsoft, Yahoo, Gmail usw.)</span><span class="sxs-lookup"><span data-stu-id="aa7a6-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="aa7a6-187">Senden Sie an verschiedene e-Mail-Konten.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="aa7a6-188">**Eine bewährte Sicherheits** Maßnahme besteht darin, **keine** Produktionsgeheimnisse in Test-und Entwicklungsumgebungen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="aa7a6-189">Wenn Sie die app in Azure veröffentlichen, legen Sie die sendgrid-Geheimnisse als Anwendungseinstellungen im Azure-Web-App-Portal fest.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="aa7a6-190">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="aa7a6-191">Kombinieren von sozialen und lokalen Anmeldekonten</span><span class="sxs-lookup"><span data-stu-id="aa7a6-191">Combine social and local login accounts</span></span>

<span data-ttu-id="aa7a6-192">Zum Durchführen dieses Abschnitts müssen Sie zuerst einen externen Authentifizierungs Anbieter aktivieren.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="aa7a6-193">Weitere Informationen finden Sie unter [Authentifizierung für Facebook, Google und externe Anbieter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="aa7a6-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="aa7a6-194">Sie können lokale und soziale Konten kombinieren, indem Sie auf Ihren e-Mail-Link klicken.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="aa7a6-195">In der folgenden Sequenz wird "RickAndMSFT@gmail.com" zuerst als lokale Anmeldung erstellt. Sie können das Konto jedoch zuerst als Social Login (Soziales anmelden) erstellen und dann einen lokalen Anmelde Namen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webanwendung: authentifizierter RickAndMSFT@gmail.com Benutzer](accconfirm/_static/rick.png)

<span data-ttu-id="aa7a6-197">Klicken Sie auf den Link **Verwalten** .</span><span class="sxs-lookup"><span data-stu-id="aa7a6-197">Click on the **Manage** link.</span></span> <span data-ttu-id="aa7a6-198">Beachten Sie die 0 (null) externen (Anmeldungen), die diesem Konto zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-198">Note the 0 external (social logins) associated with this account.</span></span>

![Ansicht verwalten](accconfirm/_static/manage.png)

<span data-ttu-id="aa7a6-200">Klicken Sie auf den Link zu einem anderen Anmeldedienst, und akzeptieren Sie die APP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="aa7a6-201">In der folgenden Abbildung ist Facebook der externe Authentifizierungs Anbieter:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-201">In the following image, Facebook is the external authentication provider:</span></span>

![Verwalten der Ansicht externer Anmeldungen mit Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="aa7a6-203">Die beiden Konten wurden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-203">The two accounts have been combined.</span></span> <span data-ttu-id="aa7a6-204">Sie können sich mit einem der beiden Konten anmelden.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-204">You are able to sign in with either account.</span></span> <span data-ttu-id="aa7a6-205">Möglicherweise möchten Sie, dass Ihre Benutzer lokale Konten hinzufügen, falls der Authentifizierungsdienst für soziale Anmelde Informationen ausgefallen ist oder Sie den Zugriff auf Ihr Social Media-Konto verlieren.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="aa7a6-206">Aktivieren der Konto Bestätigung nach einem Standort Benutzer</span><span class="sxs-lookup"><span data-stu-id="aa7a6-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="aa7a6-207">Das Aktivieren der Konto Bestätigung auf einem Standort mit Benutzern sperrt alle vorhandenen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="aa7a6-208">Vorhandene Benutzer werden gesperrt, da ihre Konten nicht bestätigt werden.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="aa7a6-209">Verwenden Sie einen der folgenden Ansätze, um die vorhandene Benutzer Sperre zu umgehen:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="aa7a6-210">Aktualisieren Sie die Datenbank, um alle vorhandenen Benutzer als bestätigt zu markieren.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="aa7a6-211">Vorhandene Benutzer bestätigen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-211">Confirm existing users.</span></span> <span data-ttu-id="aa7a6-212">Beispiel: Senden von e-Mails mit Bestätigungs Links per Batch.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="aa7a6-213">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="aa7a6-213">Prerequisites</span></span>

[<span data-ttu-id="aa7a6-214">.Net Core 2,2 SDK oder höher</span><span class="sxs-lookup"><span data-stu-id="aa7a6-214">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="aa7a6-215">Erstellen einer Web-App und einer Gerüstbau-Identität</span><span class="sxs-lookup"><span data-stu-id="aa7a6-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="aa7a6-216">Führen Sie die folgenden Befehle aus, um eine Web-App mit Authentifizierung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-216">Run the following commands to create a web app with authentication.</span></span>

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

## <a name="test-new-user-registration"></a><span data-ttu-id="aa7a6-217">Neue Benutzerregistrierung testen</span><span class="sxs-lookup"><span data-stu-id="aa7a6-217">Test new user registration</span></span>

<span data-ttu-id="aa7a6-218">Führen Sie die APP aus, wählen Sie den Link **registrieren** aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="aa7a6-219">An diesem Punkt wird die einzige Überprüfung der e-Mail mit dem [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) -Attribut durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-219">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="aa7a6-220">Nachdem Sie die Registrierung übermittelt haben, werden Sie bei der App angemeldet.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="aa7a6-221">Später in diesem Tutorial wird der Code aktualisiert, sodass neue Benutzer sich erst anmelden können, wenn Ihre e-Mail-Nachricht überprüft wurde.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="aa7a6-222">Beachten Sie, dass das `EmailConfirmed` Feld der Tabelle `False`ist.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="aa7a6-223">Sie sollten diese e-Mail im nächsten Schritt erneut verwenden, wenn die APP eine Bestätigungs-e-Mail sendet.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="aa7a6-224">Klicken Sie mit der rechten Maustaste auf die Zeile, und wählen Sie **Löschen**.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="aa7a6-225">Wenn Sie den e-Mail-Alias löschen, wird dies in den folgenden Schritten vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="aa7a6-226">Bestätigung per e-Mail erforderlich</span><span class="sxs-lookup"><span data-stu-id="aa7a6-226">Require email confirmation</span></span>

<span data-ttu-id="aa7a6-227">Es wird empfohlen, die e-Mail-Adresse einer neuen Benutzerregistrierung zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="aa7a6-228">Mithilfe der e-Mail-Bestätigung können Sie sicherstellen, dass Sie keine andere Identität annehmen (d. h., Sie haben sich nicht bei einer anderen e-Mail registriert).</span><span class="sxs-lookup"><span data-stu-id="aa7a6-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="aa7a6-229">Angenommen, Sie hatten ein Diskussionsforum, und Sie wollten verhindern, dass "yli@example.com" als "nolivetto@contoso.com" registriert wird.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="aa7a6-230">Ohne e-Mail-Bestätigung kann "nolivetto@contoso.com" unerwünschte e-Mails von Ihrer APP erhalten.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="aa7a6-231">Angenommen, der Benutzer hat sich versehentlich als "ylo@example.com" registriert und die falsche Schreibweise von "Yli" nicht bemerkt.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="aa7a6-232">Sie sind nicht in der Lage, die Kenn Wort Wiederherstellung zu verwenden, da die APP nicht über die richtige e-Mail verfügt</span><span class="sxs-lookup"><span data-stu-id="aa7a6-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="aa7a6-233">E-Mail-Bestätigung bietet eingeschränkten Schutz vor Bots.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="aa7a6-234">E-Mail-Bestätigung bietet keinen Schutz vor böswilligen Benutzern mit vielen e-Mail-Konten</span><span class="sxs-lookup"><span data-stu-id="aa7a6-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="aa7a6-235">In der Regel möchten Sie, dass neue Benutzerdaten an Ihrer Website veröffentlichen, bevor Sie eine bestätigte e-Mail-Adresse haben.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="aa7a6-236">Aktualisieren Sie `Startup.ConfigureServices`, damit eine bestätigte e-Mail erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="aa7a6-237">`config.SignIn.RequireConfirmedEmail = true;` verhindert, dass registrierte Benutzer sich anmelden, bis Ihre e-Mail-Nachricht bestätigt wird.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="aa7a6-238">E-Mail-Anbieter</span><span class="sxs-lookup"><span data-stu-id="aa7a6-238">Configure email provider</span></span>

<span data-ttu-id="aa7a6-239">In diesem Tutorial wird [sendgrid](https://sendgrid.com) zum Senden von e-Mails verwendet.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="aa7a6-240">Sie benötigen ein sendgrid-Konto und einen Schlüssel zum Senden von e-Mails.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="aa7a6-241">Sie können andere e-Mail-Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-241">You can use other email providers.</span></span> <span data-ttu-id="aa7a6-242">ASP.net Core 2. x enthält `System.Net.Mail`, mit dem Sie e-Mails von Ihrer APP senden können.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="aa7a6-243">Es wird empfohlen, sendgrid oder einen anderen e-Mail-Dienst zum Senden von e-Mails zu verwenden</span><span class="sxs-lookup"><span data-stu-id="aa7a6-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="aa7a6-244">SMTP ist schwierig zu sichern und ordnungsgemäß eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="aa7a6-245">Erstellen Sie eine Klasse zum Abrufen des Schlüssels für sichere e-Mails.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="aa7a6-246">Erstellen Sie für dieses Beispiel *Dienste/authmessagesenderoptions. cs*:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="aa7a6-247">Konfigurieren von sendgrid-Benutzer Geheimnissen</span><span class="sxs-lookup"><span data-stu-id="aa7a6-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="aa7a6-248">Legen Sie die `SendGridUser` und `SendGridKey` mit dem [Secret-Manager-Tool](xref:security/app-secrets)fest.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="aa7a6-249">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="aa7a6-250">Unter Windows speichert Secret Manager Schlüssel/Wert-Paare in der Datei " *Secrets. JSON* " im `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="aa7a6-251">Der Inhalt der Datei " *Secrets. JSON* " wird nicht verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="aa7a6-252">Das folgende Markup zeigt die Datei " *Secrets. JSON* ".</span><span class="sxs-lookup"><span data-stu-id="aa7a6-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="aa7a6-253">Der `SendGridKey` Wert wurde entfernt.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="aa7a6-254">Weitere Informationen finden Sie im [options Muster](xref:fundamentals/configuration/options) und in der [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="aa7a6-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="aa7a6-255">Installieren von sendgrid</span><span class="sxs-lookup"><span data-stu-id="aa7a6-255">Install SendGrid</span></span>

<span data-ttu-id="aa7a6-256">In diesem Tutorial wird gezeigt, wie Sie e-Mail-Benachrichtigungen über [sendgrid](https://sendgrid.com/)hinzufügen, aber Sie können e-Mails mithilfe von SMTP und anderen Mechanismen senden.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="aa7a6-257">Installieren Sie das `SendGrid` nuget-Paket:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aa7a6-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa7a6-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa7a6-259">Geben Sie in der Paket-Manager-Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-259">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="aa7a6-260">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="aa7a6-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="aa7a6-261">Geben Sie in der Konsole den folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-261">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="aa7a6-262">Weitere Informationen zur Registrierung für ein kostenloses sendgrid-Konto finden [Sie unter Get Started with sendgrid](https://sendgrid.com/free/) .</span><span class="sxs-lookup"><span data-stu-id="aa7a6-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="aa7a6-263">Implementieren von iemailsender</span><span class="sxs-lookup"><span data-stu-id="aa7a6-263">Implement IEmailSender</span></span>

<span data-ttu-id="aa7a6-264">Um `IEmailSender`zu implementieren, erstellen Sie *Services/emailsender. cs* mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="aa7a6-265">Konfigurieren des Starts zur Unterstützung von e-Mails</span><span class="sxs-lookup"><span data-stu-id="aa7a6-265">Configure startup to support email</span></span>

<span data-ttu-id="aa7a6-266">Fügen Sie der `ConfigureServices`-Methode in der Datei *Startup.cs* den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="aa7a6-267">Fügen Sie `EmailSender` als vorübergehenden Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="aa7a6-268">Registrieren Sie die `AuthMessageSenderOptions` Konfigurations Instanz.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="aa7a6-269">Aktivieren der Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="aa7a6-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="aa7a6-270">Die Vorlage enthält den Code für die Konto Bestätigung und Kenn Wort Wiederherstellung.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="aa7a6-271">Suchen Sie die `OnPostAsync`-Methode in " *Bereiche/Identität/Seiten/Konto/Register. cshtml. cs*".</span><span class="sxs-lookup"><span data-stu-id="aa7a6-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="aa7a6-272">Verhindern Sie, dass neu registrierte Benutzer automatisch angemeldet werden, indem Sie die folgende Zeile auskommentieren:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="aa7a6-273">Die gesamte-Methode wird angezeigt, wenn die geänderte Zeile hervorgehoben ist:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="aa7a6-274">Registrieren, bestätigen der e-Mail und Zurücksetzen des Kennworts</span><span class="sxs-lookup"><span data-stu-id="aa7a6-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="aa7a6-275">Führen Sie die Web-App aus, und testen Sie den Ablauf der Konto Bestätigung und Kenn Wort Wiederherstellung.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="aa7a6-276">Die app ausführen und einen neuen Benutzer registrieren</span><span class="sxs-lookup"><span data-stu-id="aa7a6-276">Run the app and register a new user</span></span>
* <span data-ttu-id="aa7a6-277">Überprüfen Sie Ihre e-Mail auf den Link zur Konto Bestätigung.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="aa7a6-278">Siehe [Debuggen von e-Mails](#debug) , wenn Sie die e-Mail nicht erhalten.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="aa7a6-279">Klicken Sie auf den Link, um Ihre e-Mail zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="aa7a6-280">Melden Sie sich mit Ihrer e-Mail und dem Kennwort an.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="aa7a6-281">Melden Sie sich ab.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="aa7a6-282">Anzeigen der Seite "verwalten"</span><span class="sxs-lookup"><span data-stu-id="aa7a6-282">View the manage page</span></span>

<span data-ttu-id="aa7a6-283">Wählen Sie Ihren Benutzernamen im Browser aus: ![Browserfenster mit Benutzername](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="aa7a6-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="aa7a6-284">Die Seite verwalten wird angezeigt, und die Registerkarte **Profil** ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="aa7a6-285">Die **e-Mail** zeigt ein Kontrollkästchen an, das anzeigt, dass die e-Mail bestätigt wurde.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="aa7a6-286">Kenn Wort Zurücksetzung testen</span><span class="sxs-lookup"><span data-stu-id="aa7a6-286">Test password reset</span></span>

* <span data-ttu-id="aa7a6-287">Wenn Sie angemeldet sind, wählen Sie **Abmelden aus**.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="aa7a6-288">Wählen Sie den Link **Anmelden aus** , und wählen Sie den Link **Kennwort vergessen?** aus.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="aa7a6-289">Geben Sie die e-Mail ein, die Sie zum Registrieren des Kontos verwendet haben.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="aa7a6-290">Eine e-Mail mit einem Link zum Zurücksetzen Ihres Kennworts wird gesendet.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="aa7a6-291">Überprüfen Sie Ihre e-Mail, und klicken Sie auf den Link zum Zurücksetzen Ihres Kennworts</span><span class="sxs-lookup"><span data-stu-id="aa7a6-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="aa7a6-292">Nachdem das Kennwort erfolgreich zurückgesetzt wurde, können Sie sich mit Ihrer e-Mail-Adresse und dem neuen Kennwort anmelden.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="aa7a6-293">E-Mail-und Aktivitäts Timeout ändern</span><span class="sxs-lookup"><span data-stu-id="aa7a6-293">Change email and activity timeout</span></span>

<span data-ttu-id="aa7a6-294">Das standardmäßige Inaktivitäts Timeout beträgt 14 Tage.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="aa7a6-295">Mit dem folgenden Code wird das Inaktivitäts Timeout auf 5 Tage festgelegt:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="aa7a6-296">Alle Datenschutz Token-Lebensdauer ändern</span><span class="sxs-lookup"><span data-stu-id="aa7a6-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="aa7a6-297">Der folgende Code ändert den Timeout Zeitraum für alle Token für die Datenschutz Token in 3 Stunden:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="aa7a6-298">Die integrierten Identitäts Benutzer Token (siehe [aspnetcore/src/Identity/Extensions. Core/src/tokenoptions. cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) haben ein Timeout von einem [Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="aa7a6-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="aa7a6-299">Lebensdauer von e-Mail-Token</span><span class="sxs-lookup"><span data-stu-id="aa7a6-299">Change the email token lifespan</span></span>

<span data-ttu-id="aa7a6-300">Die standardmäßige tokengültigkeits Dauer der [Identitäts Benutzer Token](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ist [ein Tag](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="aa7a6-300">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="aa7a6-301">In diesem Abschnitt wird gezeigt, wie Sie die Lebensdauer von e-Mail-</span><span class="sxs-lookup"><span data-stu-id="aa7a6-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="aa7a6-302">Fügen Sie einen benutzerdefinierten [dataprotectortokenprovider-\<tuser->](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) und <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="aa7a6-303">Fügen Sie dem Dienst Container den benutzerdefinierten Anbieter hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="aa7a6-304">E-Mail-Bestätigung erneut senden</span><span class="sxs-lookup"><span data-stu-id="aa7a6-304">Resend email confirmation</span></span>

<span data-ttu-id="aa7a6-305">Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="aa7a6-305">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="aa7a6-306">E-Mail Debuggen</span><span class="sxs-lookup"><span data-stu-id="aa7a6-306">Debug email</span></span>

<span data-ttu-id="aa7a6-307">Wenn Sie keine e-Mail mehr erhalten können:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-307">If you can't get email working:</span></span>

* <span data-ttu-id="aa7a6-308">Legen Sie in `EmailSender.Execute` einen Haltepunkt fest, um zu überprüfen, `SendGridClient.SendEmailAsync` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="aa7a6-309">Erstellen [Sie eine Konsolen-App zum Senden von e-Mails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mithilfe eines ähnlichen Codes an `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="aa7a6-310">Lesen Sie die Seite [e-Mail-Aktivität](https://sendgrid.com/docs/User_Guide/email_activity.html) .</span><span class="sxs-lookup"><span data-stu-id="aa7a6-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="aa7a6-311">Überprüfen Sie Ihren Spam-Ordner.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-311">Check your spam folder.</span></span>
* <span data-ttu-id="aa7a6-312">Probieren Sie einen anderen e-Mail-Alias für einen anderen e-Mail-Anbieter aus (Microsoft, Yahoo, Gmail usw.)</span><span class="sxs-lookup"><span data-stu-id="aa7a6-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="aa7a6-313">Senden Sie an verschiedene e-Mail-Konten.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="aa7a6-314">**Eine bewährte Sicherheits** Maßnahme besteht darin, **keine** Produktionsgeheimnisse in Test-und Entwicklungsumgebungen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="aa7a6-315">Wenn Sie die app in Azure veröffentlichen, können Sie die sendgrid-Geheimnisse als Anwendungseinstellungen im Azure-Web-App-Portal festlegen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="aa7a6-316">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="aa7a6-317">Kombinieren von sozialen und lokalen Anmeldekonten</span><span class="sxs-lookup"><span data-stu-id="aa7a6-317">Combine social and local login accounts</span></span>

<span data-ttu-id="aa7a6-318">Zum Durchführen dieses Abschnitts müssen Sie zuerst einen externen Authentifizierungs Anbieter aktivieren.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="aa7a6-319">Weitere Informationen finden Sie unter [Authentifizierung für Facebook, Google und externe Anbieter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="aa7a6-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="aa7a6-320">Sie können lokale und soziale Konten kombinieren, indem Sie auf Ihren e-Mail-Link klicken.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="aa7a6-321">In der folgenden Sequenz wird "RickAndMSFT@gmail.com" zuerst als lokale Anmeldung erstellt. Sie können das Konto jedoch zuerst als Social Login (Soziales anmelden) erstellen und dann einen lokalen Anmelde Namen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Webanwendung: authentifizierter RickAndMSFT@gmail.com Benutzer](accconfirm/_static/rick.png)

<span data-ttu-id="aa7a6-323">Klicken Sie auf den Link **Verwalten** .</span><span class="sxs-lookup"><span data-stu-id="aa7a6-323">Click on the **Manage** link.</span></span> <span data-ttu-id="aa7a6-324">Beachten Sie die 0 (null) externen (Anmeldungen), die diesem Konto zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-324">Note the 0 external (social logins) associated with this account.</span></span>

![Ansicht verwalten](accconfirm/_static/manage.png)

<span data-ttu-id="aa7a6-326">Klicken Sie auf den Link zu einem anderen Anmeldedienst, und akzeptieren Sie die APP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="aa7a6-327">In der folgenden Abbildung ist Facebook der externe Authentifizierungs Anbieter:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-327">In the following image, Facebook is the external authentication provider:</span></span>

![Verwalten der Ansicht externer Anmeldungen mit Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="aa7a6-329">Die beiden Konten wurden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-329">The two accounts have been combined.</span></span> <span data-ttu-id="aa7a6-330">Sie können sich mit einem der beiden Konten anmelden.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-330">You are able to sign in with either account.</span></span> <span data-ttu-id="aa7a6-331">Möglicherweise möchten Sie, dass Ihre Benutzer lokale Konten hinzufügen, falls der Authentifizierungsdienst für soziale Anmelde Informationen ausgefallen ist oder Sie den Zugriff auf Ihr Social Media-Konto verlieren.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="aa7a6-332">Aktivieren der Konto Bestätigung nach einem Standort Benutzer</span><span class="sxs-lookup"><span data-stu-id="aa7a6-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="aa7a6-333">Das Aktivieren der Konto Bestätigung auf einem Standort mit Benutzern sperrt alle vorhandenen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="aa7a6-334">Vorhandene Benutzer werden gesperrt, da ihre Konten nicht bestätigt werden.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="aa7a6-335">Verwenden Sie einen der folgenden Ansätze, um die vorhandene Benutzer Sperre zu umgehen:</span><span class="sxs-lookup"><span data-stu-id="aa7a6-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="aa7a6-336">Aktualisieren Sie die Datenbank, um alle vorhandenen Benutzer als bestätigt zu markieren.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="aa7a6-337">Vorhandene Benutzer bestätigen.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-337">Confirm existing users.</span></span> <span data-ttu-id="aa7a6-338">Beispiel: Senden von e-Mails mit Bestätigungs Links per Batch.</span><span class="sxs-lookup"><span data-stu-id="aa7a6-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
