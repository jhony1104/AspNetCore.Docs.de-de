---
title: Kontobestätigung und kennwortwiederherstellung in ASP.NET Core
author: rick-anderson
description: Informationen Sie zum Erstellen einer ASP.NET Core-app mit e-Mail-Bestätigung und kennwortzurücksetzung.
ms.author: riande
ms.date: 3/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 59041bcf11f7deb351a2f0bb075ed80c8af5e12b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891677"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="ccc3d-103">Kontobestätigung und kennwortwiederherstellung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ccc3d-103">Account confirmation and password recovery in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="ccc3d-104">Finden Sie unter [diese PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) für die ASP.NET Core 1.1 und Version 2.1.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-104">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 and 2.1 version.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="ccc3d-105">Durch [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="ccc3d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="ccc3d-106">Dieses Tutorial veranschaulicht das Erstellen eine ASP.NET Core-app mit e-Mail-Bestätigung und kennwortzurücksetzung.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-106">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="ccc3d-107">Dieses Tutorial ist der **nicht** ein Thema ab.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-107">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="ccc3d-108">Sie sollten mit vertraut sein:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-108">You should be familiar with:</span></span>

* [<span data-ttu-id="ccc3d-109">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ccc3d-109">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="ccc3d-110">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="ccc3d-110">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="ccc3d-111">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="ccc3d-111">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

## <a name="prerequisites"></a><span data-ttu-id="ccc3d-112">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="ccc3d-112">Prerequisites</span></span>

[<span data-ttu-id="ccc3d-113">.NET Core-2.2-SDK oder höher</span><span class="sxs-lookup"><span data-stu-id="ccc3d-113">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="ccc3d-114">Erstellen einer Web-app und das Gerüst für Identity</span><span class="sxs-lookup"><span data-stu-id="ccc3d-114">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="ccc3d-115">Führen Sie die folgenden Befehle zum Erstellen einer Web-app mit Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-115">Run the following commands to create a web app with authentication.</span></span>

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="ccc3d-116">Testen Sie die Registrierung neuer Benutzer</span><span class="sxs-lookup"><span data-stu-id="ccc3d-116">Test new user registration</span></span>

<span data-ttu-id="ccc3d-117">Die app auszuführen, wählen Sie die **registrieren** verknüpfen, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-117">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="ccc3d-118">Die ausschließliche Überprüfung der auf die e-Mail-Adresse an diesem Punkt ist, mit der [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) Attribut.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-118">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="ccc3d-119">Nach der Übermittlung der Registrierungs, werden Sie bei der app angemeldet.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-119">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="ccc3d-120">Weiter unten in diesem Tutorial wird der Code aktualisiert, damit neue Benutzer nicht anmelden können, bis ihre e-Mail-Adresse überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-120">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="ccc3d-121">Beachten Sie die Tabelle `EmailConfirmed` Feld `False`.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-121">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="ccc3d-122">Sie möchten möglicherweise verwenden Sie diese e-Mail erneut im nächsten Schritt, wenn die app eine Bestätigung per e-Mail sendet.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-122">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="ccc3d-123">Mit der rechten Maustaste auf die Zeile, und wählen **löschen**.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-123">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="ccc3d-124">Löschen den e-Mail-Alias erleichtert es in den folgenden Schritten.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-124">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="ccc3d-125">E-Mail-Bestätigung erforderlich</span><span class="sxs-lookup"><span data-stu-id="ccc3d-125">Require email confirmation</span></span>

<span data-ttu-id="ccc3d-126">Es ist eine bewährte Methode, die e-Mail-Adresse einer neuen benutzerregistrierung zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-126">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="ccc3d-127">E-Mail-Bestätigung können, um zu überprüfen, ob sie sind eine andere Person keinen Identitätswechsel (d. h. sie noch nicht registriert mit einer anderen Person für den e-Mail-Adresse).</span><span class="sxs-lookup"><span data-stu-id="ccc3d-127">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="ccc3d-128">Angenommen, Sie haben ein Diskussionsforum, und Sie möchten, um zu verhindern, dass "yli@example.com"aus der Registrierung als"nolivetto@contoso.com".</span><span class="sxs-lookup"><span data-stu-id="ccc3d-128">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="ccc3d-129">Ohne e-Mail-Bestätigung "nolivetto@contoso.com" könnte unerwünschte e-Mail-Adresse aus Ihrer app erhalten.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-129">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="ccc3d-130">Angenommen, das der Benutzer versehentlich als registriert "ylo@example.com" und noch nicht bemerkt, dass die falsche Schreibweise von "Yli".</span><span class="sxs-lookup"><span data-stu-id="ccc3d-130">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="ccc3d-131">Sie wäre nicht kennwortwiederherstellung zu verwenden, da die app die korrekte e-Mail-Adresse besitzt.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-131">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="ccc3d-132">E-Mail-Bestätigung enthält nur begrenzten Schutz von Bots.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-132">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="ccc3d-133">E-Mail-Bestätigung stellt keinen Schutz vor böswilligen Benutzern viele e-Mail-Konten bereit.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-133">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="ccc3d-134">Im Allgemeinen möchten Sie verhindern, dass neue Benutzer keine Daten zu Ihrer Website veröffentlichen, bevor sie eine e-Mail bestätigte haben.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-134">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="ccc3d-135">Update `Startup.ConfigureServices` eine bestätigte e-Mail-Adresse erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-135">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="ccc3d-136">`config.SignIn.RequireConfirmedEmail = true;` verhindert, dass registrierte Benutzer anmelden, bis ihre-e-Mail bestätigt ist.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-136">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="ccc3d-137">Konfigurieren von e-Mail-Anbieter</span><span class="sxs-lookup"><span data-stu-id="ccc3d-137">Configure email provider</span></span>

<span data-ttu-id="ccc3d-138">In diesem Tutorial [SendGrid](https://sendgrid.com) wird verwendet, um e-Mail zu senden.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-138">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="ccc3d-139">Sie benötigen eine SendGrid-Konto und einen Schlüssel zum Senden von e-Mails.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-139">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="ccc3d-140">Sie können andere e-Mail-Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-140">You can use other email providers.</span></span> <span data-ttu-id="ccc3d-141">ASP.NET Core 2.x enthält `System.Net.Mail`, wodurch Sie zum Senden von e-Mail-Adresse aus Ihrer app.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-141">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="ccc3d-142">Es wird empfohlen, dass Sie über SendGrid oder eine andere e-Mail-Dienst verwenden, um e-Mail zu senden.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-142">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="ccc3d-143">SMTP ist schwierig, sichere und ordnungsgemäß eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-143">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="ccc3d-144">Erstellen Sie eine Klasse, um den Schlüssel für sichere e-Mails abrufen.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-144">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="ccc3d-145">In diesem Beispiel erstellen *Services/AuthMessageSenderOptions.cs*:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-145">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="ccc3d-146">Konfigurieren von SendGrid benutzergeheimnisse</span><span class="sxs-lookup"><span data-stu-id="ccc3d-146">Configure SendGrid user secrets</span></span>

<span data-ttu-id="ccc3d-147">Legen Sie die `SendGridUser` und `SendGridKey` mit der [Secret-Manager-Tool](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="ccc3d-147">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="ccc3d-148">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-148">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="ccc3d-149">Auf Windows, speichert Secret Manager-Schlüssel/Wert-Paare in einem *secrets.json* Datei die `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-149">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="ccc3d-150">Den Inhalt der *secrets.json* Datei sind nicht verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-150">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="ccc3d-151">Das folgende Markup zeigt die *secrets.json* Datei.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-151">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="ccc3d-152">Die `SendGridKey` Wert entfernt wurde.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-152">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="ccc3d-153">Weitere Informationen finden Sie unter den [optionsmuster](xref:fundamentals/configuration/options) und [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ccc3d-153">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="ccc3d-154">Installieren von SendGrid</span><span class="sxs-lookup"><span data-stu-id="ccc3d-154">Install SendGrid</span></span>

<span data-ttu-id="ccc3d-155">In diesem Tutorial wird gezeigt, wie Hinzufügen von e-Mail-Benachrichtigungen über [SendGrid](https://sendgrid.com/), aber Sie können e-Mails über SMTP und andere Mechanismen senden.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-155">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="ccc3d-156">Installieren Sie die `SendGrid` NuGet-Paket:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-156">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="ccc3d-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ccc3d-157">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ccc3d-158">Geben Sie über die Paket-Manager-Konsole den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-158">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="ccc3d-159">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ccc3d-159">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ccc3d-160">Geben Sie in der Konsole den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-160">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

---

<span data-ttu-id="ccc3d-161">Finden Sie unter [kostenlos erste Schritte mit SendGrid](https://sendgrid.com/free/) für ein kostenloses SendGrid-Konto zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-161">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="ccc3d-162">Implementieren von IEmailSender</span><span class="sxs-lookup"><span data-stu-id="ccc3d-162">Implement IEmailSender</span></span>

<span data-ttu-id="ccc3d-163">Das implementieren `IEmailSender`, erstellen Sie *Services/EmailSender.cs* mit ähnlich dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-163">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="ccc3d-164">Konfigurieren Sie beim Start zur Unterstützung von e-Mail-Adresse</span><span class="sxs-lookup"><span data-stu-id="ccc3d-164">Configure startup to support email</span></span>

<span data-ttu-id="ccc3d-165">Fügen Sie den folgenden Code der `ConfigureServices` -Methode in der die *"Startup.cs"* Datei:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-165">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="ccc3d-166">Hinzufügen `EmailSender` als vorübergehender Dienst.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-166">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="ccc3d-167">Registrieren der `AuthMessageSenderOptions` konfigurationsinstanz.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-167">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="ccc3d-168">Konto-Bestätigung und -Wiederherstellung aktivieren</span><span class="sxs-lookup"><span data-stu-id="ccc3d-168">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="ccc3d-169">Die Vorlage weist den Code für die Konto-Bestätigung und -Wiederherstellung.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-169">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="ccc3d-170">Suchen der `OnPostAsync` -Methode in der *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-170">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="ccc3d-171">Verhindern Sie, dass neu registrierte Benutzern automatisch durch die Anmeldung durch die Sie die folgende Zeile Kommentierung:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-171">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="ccc3d-172">Die complete-Methode wird mit der geänderten Zeile hervorgehoben dargestellt:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-172">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="ccc3d-173">Registrieren und bestätigen Sie die e-Mail-Adresse und die kennwortzurücksetzung</span><span class="sxs-lookup"><span data-stu-id="ccc3d-173">Register, confirm email, and reset password</span></span>

<span data-ttu-id="ccc3d-174">Führen Sie die Web-app, und Testen Sie die kontobestätigung und das Kennwort eine Wiederherstellung durchführen.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-174">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="ccc3d-175">Die app ausführen und einen neuen Benutzer registrieren</span><span class="sxs-lookup"><span data-stu-id="ccc3d-175">Run the app and register a new user</span></span>
* <span data-ttu-id="ccc3d-176">Überprüfen Sie Ihre e-Mail-Adresse für die der Link für die Bestätigung.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-176">Check your email for the account confirmation link.</span></span> <span data-ttu-id="ccc3d-177">Finden Sie unter [Debuggen-e-Mail](#debug) sollten Sie die e-Mail nicht erhalten.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-177">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="ccc3d-178">Klicken Sie auf den Link, um Ihre e-Mail-Adresse zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-178">Click the link to confirm your email.</span></span>
* <span data-ttu-id="ccc3d-179">Melden Sie sich mit Ihren e-Mail-Adresse und Ihr Kennwort.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-179">Sign in with your email and password.</span></span>
* <span data-ttu-id="ccc3d-180">Melden Sie sich ab.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-180">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="ccc3d-181">Anzeigen der Seite "verwalten"</span><span class="sxs-lookup"><span data-stu-id="ccc3d-181">View the manage page</span></span>

<span data-ttu-id="ccc3d-182">Wählen Sie Ihren Benutzernamen im Browser: ![Browserfenster mit Benutzername](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="ccc3d-182">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="ccc3d-183">Seite "verwalten" wird angezeigt, mit der **Profil** Registerkarte ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-183">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="ccc3d-184">Die **-e-Mail** zeigt ein Kontrollkästchen, der angibt, der e-Mail-Adresse wurde bestätigt.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-184">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="ccc3d-185">Test Zurücksetzen des Kennworts</span><span class="sxs-lookup"><span data-stu-id="ccc3d-185">Test password reset</span></span>

* <span data-ttu-id="ccc3d-186">Wenn Sie sich angemeldet haben, wählen Sie **Logout**.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-186">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="ccc3d-187">Wählen Sie die **melden Sie sich bei** verknüpfen, und wählen Sie die **haben Ihr Kennwort vergessen?** Link.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-187">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="ccc3d-188">Geben Sie die e-Mail-Adresse, die Sie verwendet, um das Konto zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-188">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="ccc3d-189">Es wird eine e-Mail mit einem Link zum Zurücksetzen Ihres Kennworts gesendet.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-189">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="ccc3d-190">Überprüfen Sie Ihre e-Mail-Adresse ein, und klicken Sie auf den Link zum Zurücksetzen Ihres Kennworts.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-190">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="ccc3d-191">Nachdem Sie Ihr Kennwort erfolgreich zurückgesetzt wurde, können Sie sich mit Ihrem e-Mail-Adresse und das neue Kennwort anmelden.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-191">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="ccc3d-192">Ändern Sie die e-Mail-Adresse und der Aktivität ein timeout</span><span class="sxs-lookup"><span data-stu-id="ccc3d-192">Change email and activity timeout</span></span>

<span data-ttu-id="ccc3d-193">Das Standardtimeout für die Inaktivität beträgt 14 Tage.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-193">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="ccc3d-194">Im folgenden Code wird das Timeout bei Inaktivität bis 5 Tagen:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-194">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="ccc3d-195">Ändern Sie alle Data Protection-token-Gültigkeitsdauer</span><span class="sxs-lookup"><span data-stu-id="ccc3d-195">Change all data protection token lifespans</span></span>

<span data-ttu-id="ccc3d-196">Der folgende Code ändert alle Data Protection Token Zeitlimit bis drei Stunden:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-196">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="ccc3d-197">Die integrierten im Benutzertoken für die Identität (finden Sie unter [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) haben eine [Timeout für einen Tag](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="ccc3d-197">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="ccc3d-198">Ändern Sie die Lebensdauer des Zugriffstoken-e-Mail</span><span class="sxs-lookup"><span data-stu-id="ccc3d-198">Change the email token lifespan</span></span>

<span data-ttu-id="ccc3d-199">Die Standard-token-Lebensdauer von [das Benutzertoken für die Identität](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ist [eines Tages](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span><span class="sxs-lookup"><span data-stu-id="ccc3d-199">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="ccc3d-200">In diesem Abschnitt veranschaulicht, wie die Lebensdauer des Zugriffstoken-e-Mail zu ändern.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-200">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="ccc3d-201">Hinzufügen ein benutzerdefinierten [DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) und <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-201">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="ccc3d-202">Fügen Sie den benutzerdefinierten Anbieter zum Dienstcontainer hinzu:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-202">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="ccc3d-203">Erneutes Senden von e-Mail-Bestätigung</span><span class="sxs-lookup"><span data-stu-id="ccc3d-203">Resend email confirmation</span></span>

<span data-ttu-id="ccc3d-204">Finden Sie unter [GitHub-Problem](https://github.com/aspnet/AspNetCore/issues/5410).</span><span class="sxs-lookup"><span data-stu-id="ccc3d-204">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="ccc3d-205">Debuggen von e-Mail-Adresse</span><span class="sxs-lookup"><span data-stu-id="ccc3d-205">Debug email</span></span>

<span data-ttu-id="ccc3d-206">Wenn Sie e-Mail-Adresse funktioniert nicht:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-206">If you can't get email working:</span></span>

* <span data-ttu-id="ccc3d-207">Festlegen eines Haltepunkts im `EmailSender.Execute` überprüfen `SendGridClient.SendEmailAsync` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-207">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="ccc3d-208">Erstellen Sie eine [Konsolen-app zum Senden von e-Mails](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) mit ähnlichen Code `EmailSender.Execute`.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-208">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="ccc3d-209">Überprüfen Sie die [-e-Mail-Aktivität](https://sendgrid.com/docs/User_Guide/email_activity.html) Seite.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-209">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="ccc3d-210">Überprüfen Sie Ihren Spam-Ordner.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-210">Check your spam folder.</span></span>
* <span data-ttu-id="ccc3d-211">Versuchen Sie es einer anderen e-Mail-Alias für einen anderen e-Mail-Anbieter (Microsoft, Yahoo, Gmail usw.)</span><span class="sxs-lookup"><span data-stu-id="ccc3d-211">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="ccc3d-212">Versuchen Sie es an andere e-Mail-Konten gesendet.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-212">Try sending to different email accounts.</span></span>

<span data-ttu-id="ccc3d-213">**Eine bewährte Sicherheitsmethode** besteht darin, **nicht** produktionsgeheimnisse in Test- und entwicklungsumgebungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-213">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="ccc3d-214">Wenn Sie die app in Azure veröffentlichen, können Sie die SendGrid-Geheimnisse Anwendungseinstellungen im Azure-Web-App-Portal festlegen.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-214">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="ccc3d-215">Das Konfigurationssystem ist zum Lesen von Schlüsseln aus Umgebungsvariablen eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-215">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="ccc3d-216">Kombinieren Sie Konten für soziale Netzwerke und lokale Anmeldung</span><span class="sxs-lookup"><span data-stu-id="ccc3d-216">Combine social and local login accounts</span></span>

<span data-ttu-id="ccc3d-217">Um diesen Abschnitt abgeschlossen haben, müssen Sie zuerst einen externer Authentifizierungsanbieter aktivieren.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-217">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="ccc3d-218">Finden Sie unter [Facebook, Google und externe Anbieter Authentifizierung](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="ccc3d-218">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="ccc3d-219">Sie können lokale als auch social kombinieren, durch Klicken auf Ihre e-Mail-Link.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-219">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="ccc3d-220">In der folgenden Reihenfolge "RickAndMSFT@gmail.com" wird zuerst als eine lokale Anmeldung; erstellt, Sie können jedoch zunächst erstellen Sie das Konto als Anmeldedaten eines sozialen Netzwerks, und fügen Sie eine lokale Anmeldung hinzu.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-220">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web-Anwendung: RickAndMSFT@gmail.com authentifizierte Benutzer](accconfirm/_static/rick.png)

<span data-ttu-id="ccc3d-222">Klicken Sie auf die **verwalten** Link.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-222">Click on the **Manage** link.</span></span> <span data-ttu-id="ccc3d-223">Beachten Sie die 0 externe (Anmeldungen per sozialem Netzwerk) mit diesem Konto verknüpft.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-223">Note the 0 external (social logins) associated with this account.</span></span>

![Verwalten von anzeigen](accconfirm/_static/manage.png)

<span data-ttu-id="ccc3d-225">Klicken Sie auf den Link, um einen anderen Anmeldenamen-Dienst, und akzeptieren Sie die app-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-225">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="ccc3d-226">In der folgenden Abbildung ist die Facebook dem externen Authentifizierungsanbieter:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-226">In the following image, Facebook is the external authentication provider:</span></span>

![Verwalten Sie Ihre Facebook auflisten externer Anmeldungen-Ansicht](accconfirm/_static/fb.png)

<span data-ttu-id="ccc3d-228">Die beiden Konten wurden kombiniert.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-228">The two accounts have been combined.</span></span> <span data-ttu-id="ccc3d-229">Sie können die Anmeldung der Konten.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-229">You are able to sign in with either account.</span></span> <span data-ttu-id="ccc3d-230">Sie sollten Ihre Benutzer lokale Konten hinzufügen, falls ihre sozialen Authentifizierungsdiensts ausgefallen ist oder eher sie den Zugriff auf ihre Konten sozialer Netzwerke verlieren.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-230">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="ccc3d-231">Kontobestätigung zu aktivieren, nachdem Sie einen Standort der Benutzer enthält</span><span class="sxs-lookup"><span data-stu-id="ccc3d-231">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="ccc3d-232">Aktivierung kontobestätigung auf einer Website für Benutzer, alle vorhandenen Benutzer sperren.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-232">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="ccc3d-233">Vorhandene Benutzer werden gesperrt, da es sich bei ihrem Konto bestätigt werden nicht.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-233">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="ccc3d-234">Um vorhandene Benutzersperre zu umgehen, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="ccc3d-234">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="ccc3d-235">Ändern der Datenbank markieren Sie alle vorhandenen Benutzer als bestätigt wird.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-235">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="ccc3d-236">Vergewissern Sie sich vorhandene Benutzer.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-236">Confirm existing users.</span></span> <span data-ttu-id="ccc3d-237">Z. B. Batch-Send-e-Mails mit Bestätigung Links.</span><span class="sxs-lookup"><span data-stu-id="ccc3d-237">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
