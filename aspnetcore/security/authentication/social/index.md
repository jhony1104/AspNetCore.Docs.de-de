---
title: Authentifizierung über Facebook, Google und externe Anbieter in ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht, wie Sie eine ASP.NET Core 2.x-App mithilfe von OAuth 2.0 und externen Authentifizierungsanbietern entwickeln.
ms.author: riande
ms.custom: mvc
ms.date: 4/19/2019
uid: security/authentication/social/index
ms.openlocfilehash: e2d68ac93bdcfa2fc015e8447ea38626787cdb02
ms.sourcegitcommit: a3926eae3f687013027a2828830c12a89add701f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65451041"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="4e5cc-103">Authentifizierung über Facebook, Google und externe Anbieter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4e5cc-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="4e5cc-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4e5cc-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4e5cc-105">In diesem Tutorial wird veranschaulicht, wie Sie eine ASP.NET Core 2.2-App erstellen, mit der Benutzer sich mithilfe von OAuth 2.0 und Anmeldeinformationen von externen Authentifizierungsanbietern anmelden können.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-105">This tutorial demonstrates how to build an ASP.NET Core 2.2 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="4e5cc-106">Die Anbieter [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) und [Microsoft](xref:security/authentication/microsoft-logins) werden in den folgenden Abschnitten behandelt.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections.</span></span> <span data-ttu-id="4e5cc-107">Andere Anbieter stehen in Paketen von Drittanbietern wie z.B. [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) und [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

![Symbole sozialer Medien wie Facebook, Twitter, Google Plus und Windows](index/_static/social.png)

<span data-ttu-id="4e5cc-109">Das Anmelden mit vorhandenen Anmeldeinformationen:</span><span class="sxs-lookup"><span data-stu-id="4e5cc-109">Enabling users to sign in with their existing credentials:</span></span>
* <span data-ttu-id="4e5cc-110">ist für Benutzer sehr praktisch.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-110">Is convenient for the users.</span></span>
* <span data-ttu-id="4e5cc-111">lagert die Verwaltung des Anmeldevorgangs größtenteils an einen Drittanbieter aus.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-111">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span> 

<span data-ttu-id="4e5cc-112">Beispiel dazu, wie Anmeldungen bei sozialen Medien für mehr Datenverkehr und gewonnene Kunden sorgen, finden Sie in Fallstudien von [Facebook](https://www.facebook.com/unsupportedbrowser) und [Twitter](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="4e5cc-112">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="4e5cc-113">Erstellen eines neuen ASP.NET Core-Projekts</span><span class="sxs-lookup"><span data-stu-id="4e5cc-113">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4e5cc-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4e5cc-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4e5cc-115">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="4e5cc-116">Erstellen Sie eine neue ASP.NET Core-Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-116">Create a new ASP.NET Core Web Application.</span></span>
* <span data-ttu-id="4e5cc-117">Wählen Sie in der Dropdownliste **ASP.NET Core 2.2** und anschließend **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-117">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>
* <span data-ttu-id="4e5cc-118">Klicken Sie auf **Authentifizierung ändern**, und legen Sie die Authentifizierung auf **Einzelne Benutzerkonten** fest.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-118">Select **Change Authentication** and set authentication to **Individual User Accounts**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4e5cc-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4e5cc-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="4e5cc-120">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="4e5cc-120">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="4e5cc-121">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-121">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="4e5cc-122">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="4e5cc-122">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o WebApp1 -au Individual -uld
  code -r WebApp1
  ```

  * <span data-ttu-id="4e5cc-123">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *WebApp1*.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-123">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="4e5cc-124">`-uld` verwendet LocalDB anstelle von SQLite.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-124">`-uld` uses LocalDB instead of SQLite.</span></span> <span data-ttu-id="4e5cc-125">Lassen Sie `-uld` weg, um SQLite zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-125">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="4e5cc-126">`-au Individual` erstellt den Code für die einzelne Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-126">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="4e5cc-127">Mit dem Befehl `code` wird der Ordner *WebApp1* in einer neuen Instanz von Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-127">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="4e5cc-128">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in „WebApp1“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="4e5cc-128">A dialog box appears with **Required assets to build and debug are missing from 'WebApp1'. Add them?**</span></span>

* <span data-ttu-id="4e5cc-129">Wählen Sie **Ja** aus.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-129">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="4e5cc-130">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4e5cc-130">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="4e5cc-131">Führen Sie über ein Terminal den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="4e5cc-131">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o WebApp1 -au Individual
```

<span data-ttu-id="4e5cc-132">Diese Befehle verwenden die [.NET Core-CLI](/dotnet/core/tools/dotnet), um ein Razor Pages-Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-132">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="4e5cc-133">Öffnen des Projekts</span><span class="sxs-lookup"><span data-stu-id="4e5cc-133">Open the project</span></span>

<span data-ttu-id="4e5cc-134">Klicken Sie in Visual Studio auf **Datei > Öffnen**, und wählen Sie dann die Datei *WebApp1.csproj* aus.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-134">From Visual Studio, select **File > Open**, and then select the *WebApp1.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="apply-migrations"></a><span data-ttu-id="4e5cc-135">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="4e5cc-135">Apply migrations</span></span>

* <span data-ttu-id="4e5cc-136">Führen Sie die App aus, und klicken Sie auf den Link **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-136">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="4e5cc-137">Geben Sie die E-Mail-Adresse und das Kennwort für das neue Konto ein, und wählen Sie dann **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-137">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="4e5cc-138">Befolgen Sie die Anweisungen zum Anwenden von Migrationen.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-138">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="4e5cc-139">Verwenden von SecretManager zum Speichern von Token, die von Anmeldeanbietern zugewiesen wurden</span><span class="sxs-lookup"><span data-stu-id="4e5cc-139">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="4e5cc-140">Anmeldeanbieter aus dem Bereich der sozialen Medien weisen während des Registrierungsvorgangs Token des Typs **Anwendungs-ID** und **Anwendungsgeheimnis** zu.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-140">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="4e5cc-141">Die genauen Tokennamen unterscheiden sich je nach Anbieter.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-141">The exact token names vary by provider.</span></span> <span data-ttu-id="4e5cc-142">Diese Token stellen die Anmeldeinformationen dar, mit denen Ihre App auf ihre API zugreift.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-142">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="4e5cc-143">Diese Token setzen sich zu „Geheimnissen“ zusammen, die mithilfe von [Secret Manager](xref:security/app-secrets#secret-manager) mit Ihrer App-Konfiguration verknüpft werden können.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-143">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="4e5cc-144">Secret Manager ist eine sicherere Alternative zum Speichern von Token in einer Konfigurationsdatei wie z.B. *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-144">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4e5cc-145">Secret Manager ist nur zur Entwicklung vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-145">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="4e5cc-146">Sie können Azure-Test- und -Produktionsgeheimnisse mit dem [Konfigurationsanbieter Azure Key Vault](xref:security/key-vault-configuration) speichern und schützen.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-146">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="4e5cc-147">Führen Sie die im Artikel [Sichere Speicherung von App-Geheimnissen bei einer Entwicklung in ASP.NET Core](xref:security/app-secrets) beschriebenen Schritte durch, um Token zu speichern, die von den folgenden Anmeldeanbietern zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-147">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="4e5cc-148">Einrichten der für Ihre Anwendung erforderlichen Anmeldeanbietern</span><span class="sxs-lookup"><span data-stu-id="4e5cc-148">Setup login providers required by your application</span></span>

<span data-ttu-id="4e5cc-149">In den folgenden Themen erfahren Sie, wie Sie Ihre Anwendung für die entsprechenden Anbieter konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="4e5cc-149">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="4e5cc-150">Anweisungen für [Facebook](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="4e5cc-150">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="4e5cc-151">Anweisungen für [Twitter](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="4e5cc-151">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="4e5cc-152">Anweisungen für [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="4e5cc-152">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="4e5cc-153">Anweisungen für [Microsoft](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="4e5cc-153">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="4e5cc-154">Anweisungen für [andere Anbieter](xref:security/authentication/otherlogins)</span><span class="sxs-lookup"><span data-stu-id="4e5cc-154">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="4e5cc-155">Optionales Festlegen des Kennworts</span><span class="sxs-lookup"><span data-stu-id="4e5cc-155">Optionally set password</span></span>

<span data-ttu-id="4e5cc-156">Wenn Sie sich bei einem externen Anmeldeanbieter registrieren, wird kein Kennwort bei der App registriert.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-156">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="4e5cc-157">Dadurch entfällt für Sie Erstellen und Merken eines Kennworts für die Website. Sie werden allerdings auch vom externen Anmeldeanbieter abhängig.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-157">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="4e5cc-158">Wenn der externe Anmeldeanbieter nicht verfügbar ist, können Sie sich nicht bei der Website anmelden.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-158">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="4e5cc-159">So erstellen Sie ein Kennwort und melden sich mithilfe Ihrer E-Mail-Adresse an, die Sie während des Anmeldevorgangs bei externen Anbietern festgelegt haben:</span><span class="sxs-lookup"><span data-stu-id="4e5cc-159">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="4e5cc-160">Klicken Sie rechts oben auf den Link **Hallo &lt;E-Mail-Alias&gt;**, um zur Ansicht **Verwalten** zu gelangen.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-160">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Ansicht „Verwalten“ der Webanwendung](index/_static/pass1a.png)

* <span data-ttu-id="4e5cc-162">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-162">Select **Create**</span></span>

![Seite zum Festlegen Ihres Kennworts](index/_static/pass2a.png)

* <span data-ttu-id="4e5cc-164">Legen Sie ein gültiges Kennwort fest, das Sie anschließend mit Ihrer E-Mail-Adresse zum Anmelden nutzen können.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-164">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4e5cc-165">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="4e5cc-165">Next steps</span></span>

* <span data-ttu-id="4e5cc-166">In diesem Artikel wurde die externe Authentifizierung behandelt. Ferner wurden die Voraussetzungen für das Hinzufügen externer Anmeldungen zu Ihrer ASP.NET Core-App erläutert.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-166">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>

* <span data-ttu-id="4e5cc-167">Konsultieren Sie anbieterspezifische Seiten zum Konfigurieren von Anmeldungen für die von Ihrer App angeforderten Anbieter.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-167">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>

* <span data-ttu-id="4e5cc-168">Möglicherweise möchten Sie zusätzliche Daten zum Benutzer und seinen Zugriffs- und Aktualisierungstoken persistent speichern.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-168">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="4e5cc-169">Weitere Informationen finden Sie unter <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="4e5cc-169">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
