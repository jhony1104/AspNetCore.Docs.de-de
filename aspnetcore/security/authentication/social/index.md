---
title: Authentifizierung über Facebook, Google und externe Anbieter in ASP.NET Core
author: rick-anderson
description: Dieses Tutorial veranschaulicht, wie Sie eine ASP.NET Core 2.x-App mithilfe von OAuth 2.0 und externen Authentifizierungsanbietern entwickeln.
ms.author: riande
ms.custom: mvc
ms.date: 05/10/2019
uid: security/authentication/social/index
ms.openlocfilehash: edaf9eeaf02879b2f7816bab0eb373a7de640c05
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082507"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="b99c3-103">Authentifizierung über Facebook, Google und externe Anbieter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b99c3-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="b99c3-104">Von [Valeriy Novytskyy](https://github.com/01binary) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b99c3-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b99c3-105">In diesem Tutorial wird veranschaulicht, wie Sie eine ASP.NET Core 2.2-App erstellen, mit der Benutzer sich mithilfe von OAuth 2.0 und Anmeldeinformationen von externen Authentifizierungsanbietern anmelden können.</span><span class="sxs-lookup"><span data-stu-id="b99c3-105">This tutorial demonstrates how to build an ASP.NET Core 2.2 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="b99c3-106">Die Anbieter [Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins) und [Microsoft](xref:security/authentication/microsoft-logins) werden in den folgenden Abschnitten behandelt.</span><span class="sxs-lookup"><span data-stu-id="b99c3-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections.</span></span> <span data-ttu-id="b99c3-107">Andere Anbieter stehen in Paketen von Drittanbietern wie z.B. [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) und [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="b99c3-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

![Symbole sozialer Medien wie Facebook, Twitter, Google Plus und Windows](index/_static/social.png)

<span data-ttu-id="b99c3-109">Das Anmelden mit vorhandenen Anmeldeinformationen:</span><span class="sxs-lookup"><span data-stu-id="b99c3-109">Enabling users to sign in with their existing credentials:</span></span>
* <span data-ttu-id="b99c3-110">ist für Benutzer sehr praktisch.</span><span class="sxs-lookup"><span data-stu-id="b99c3-110">Is convenient for the users.</span></span>
* <span data-ttu-id="b99c3-111">lagert die Verwaltung des Anmeldevorgangs größtenteils an einen Drittanbieter aus.</span><span class="sxs-lookup"><span data-stu-id="b99c3-111">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span> 

<span data-ttu-id="b99c3-112">Beispiel dazu, wie Anmeldungen bei sozialen Medien für mehr Datenverkehr und gewonnene Kunden sorgen, finden Sie in Fallstudien von [Facebook](https://www.facebook.com/unsupportedbrowser) und [Twitter](https://dev.twitter.com/resources/case-studies).</span><span class="sxs-lookup"><span data-stu-id="b99c3-112">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="b99c3-113">Erstellen eines neuen ASP.NET Core-Projekts</span><span class="sxs-lookup"><span data-stu-id="b99c3-113">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b99c3-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b99c3-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b99c3-115">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="b99c3-115">Create a new project.</span></span>
* <span data-ttu-id="b99c3-116">Wählen Sie **ASP.NET Core-Webanwendung** und **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="b99c3-116">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="b99c3-117">Geben Sie einen **Projektnamen** ein, und bestätigen oder ändern Sie den **Speicherort**.</span><span class="sxs-lookup"><span data-stu-id="b99c3-117">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="b99c3-118">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b99c3-118">Select **Create**.</span></span>
* <span data-ttu-id="b99c3-119">Wählen Sie in der Dropdownliste **ASP.NET Core 2.2** aus.</span><span class="sxs-lookup"><span data-stu-id="b99c3-119">Select **ASP.NET Core 2.2** in the drop down.</span></span> <span data-ttu-id="b99c3-120">Wählen Sie in der Vorlagenliste **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="b99c3-120">Select **Web Application** in the template list.</span></span>
* <span data-ttu-id="b99c3-121">Wählen Sie unter **Authentifizierung** die Option **Ändern** aus, und legen Sie die Authentifizierung auf **Einzelne Benutzerkonten** fest.</span><span class="sxs-lookup"><span data-stu-id="b99c3-121">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="b99c3-122">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="b99c3-122">Select **OK**.</span></span>
* <span data-ttu-id="b99c3-123">Wählen Sie im Fenster **Neue ASP.NET Core-Webanwendung erstellen** die Option **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b99c3-123">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="b99c3-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="b99c3-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="b99c3-125">Öffnen Sie das [integrierte Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span><span class="sxs-lookup"><span data-stu-id="b99c3-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="b99c3-126">Wechseln Sie mit `cd` zu einem Ordner, der das Projekt enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="b99c3-126">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="b99c3-127">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="b99c3-127">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  code -r WebApp1
  ```

  * <span data-ttu-id="b99c3-128">Der Befehl `dotnet new` erstellt ein neues Razor Pages-Projekt im Ordner *WebApp1*.</span><span class="sxs-lookup"><span data-stu-id="b99c3-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="b99c3-129">`-uld` verwendet LocalDB anstelle von SQLite.</span><span class="sxs-lookup"><span data-stu-id="b99c3-129">`-uld` uses LocalDB instead of SQLite.</span></span> <span data-ttu-id="b99c3-130">Lassen Sie `-uld` weg, um SQLite zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b99c3-130">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="b99c3-131">`-au Individual` erstellt den Code für die einzelne Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="b99c3-131">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="b99c3-132">Mit dem Befehl `code` wird der Ordner *WebApp1* in einer neuen Instanz von Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="b99c3-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

* <span data-ttu-id="b99c3-133">Es wird ein Dialogfeld mit folgender Meldung angezeigt: **Die erforderlichen Objekte zum Erstellen und Debuggen sind in „WebApp1“ nicht vorhanden. Sollen sie hinzugefügt werden?**</span><span class="sxs-lookup"><span data-stu-id="b99c3-133">A dialog box appears with **Required assets to build and debug are missing from 'WebApp1'. Add them?**</span></span> <span data-ttu-id="b99c3-134">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="b99c3-134">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="b99c3-135">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="b99c3-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="b99c3-136">Wählen Sie **Datei** > **Neue Projektmappe** aus.</span><span class="sxs-lookup"><span data-stu-id="b99c3-136">Select **File** > **New Solution**.</span></span>
* <span data-ttu-id="b99c3-137">Wählen Sie in der Randleiste **.NET Core** > **App** aus.</span><span class="sxs-lookup"><span data-stu-id="b99c3-137">Select **.NET Core** > **App** in the sidebar.</span></span> <span data-ttu-id="b99c3-138">Wählen Sie die Vorlage für **Webanwendungen** aus.</span><span class="sxs-lookup"><span data-stu-id="b99c3-138">Select the **Web Application** template.</span></span> <span data-ttu-id="b99c3-139">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b99c3-139">Select **Next**.</span></span>
* <span data-ttu-id="b99c3-140">Wählen Sie in der Dropdownliste **Zielframework** die Option **.NET Core 2.2** aus.</span><span class="sxs-lookup"><span data-stu-id="b99c3-140">Set the **Target Framework** drop down to **.NET Core 2.2**.</span></span> <span data-ttu-id="b99c3-141">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b99c3-141">Select **Next**.</span></span>
* <span data-ttu-id="b99c3-142">Geben Sie einen **Projektnamen** an.</span><span class="sxs-lookup"><span data-stu-id="b99c3-142">Provide a **Project Name**.</span></span> <span data-ttu-id="b99c3-143">Bestätigen oder ändern Sie den **Speicherort**.</span><span class="sxs-lookup"><span data-stu-id="b99c3-143">Confirm or change the **Location**.</span></span> <span data-ttu-id="b99c3-144">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b99c3-144">Select **Create**.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="b99c3-145">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="b99c3-145">Apply migrations</span></span>

* <span data-ttu-id="b99c3-146">Führen Sie die App aus, und klicken Sie auf den Link **Registrieren**.</span><span class="sxs-lookup"><span data-stu-id="b99c3-146">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="b99c3-147">Geben Sie die E-Mail-Adresse und das Kennwort für das neue Konto ein, und wählen Sie dann **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="b99c3-147">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="b99c3-148">Befolgen Sie die Anweisungen zum Anwenden von Migrationen.</span><span class="sxs-lookup"><span data-stu-id="b99c3-148">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="b99c3-149">Verwenden von SecretManager zum Speichern von Token, die von Anmeldeanbietern zugewiesen wurden</span><span class="sxs-lookup"><span data-stu-id="b99c3-149">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="b99c3-150">Anmeldeanbieter aus dem Bereich der sozialen Medien weisen während des Registrierungsvorgangs Token des Typs **Anwendungs-ID** und **Anwendungsgeheimnis** zu.</span><span class="sxs-lookup"><span data-stu-id="b99c3-150">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="b99c3-151">Die genauen Tokennamen unterscheiden sich je nach Anbieter.</span><span class="sxs-lookup"><span data-stu-id="b99c3-151">The exact token names vary by provider.</span></span> <span data-ttu-id="b99c3-152">Diese Token stellen die Anmeldeinformationen dar, mit denen Ihre App auf ihre API zugreift.</span><span class="sxs-lookup"><span data-stu-id="b99c3-152">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="b99c3-153">Diese Token setzen sich zu „Geheimnissen“ zusammen, die mithilfe von [Secret Manager](xref:security/app-secrets#secret-manager) mit Ihrer App-Konfiguration verknüpft werden können.</span><span class="sxs-lookup"><span data-stu-id="b99c3-153">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="b99c3-154">Secret Manager ist eine sicherere Alternative zum Speichern von Token in einer Konfigurationsdatei wie z.B. *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="b99c3-154">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b99c3-155">Secret Manager ist nur zur Entwicklung vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="b99c3-155">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="b99c3-156">Sie können Azure-Test- und -Produktionsgeheimnisse mit dem [Konfigurationsanbieter Azure Key Vault](xref:security/key-vault-configuration) speichern und schützen.</span><span class="sxs-lookup"><span data-stu-id="b99c3-156">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="b99c3-157">Führen Sie die im Artikel [Sichere Speicherung von App-Geheimnissen bei einer Entwicklung in ASP.NET Core](xref:security/app-secrets) beschriebenen Schritte durch, um Token zu speichern, die von den folgenden Anmeldeanbietern zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="b99c3-157">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="b99c3-158">Einrichten der für Ihre Anwendung erforderlichen Anmeldeanbietern</span><span class="sxs-lookup"><span data-stu-id="b99c3-158">Setup login providers required by your application</span></span>

<span data-ttu-id="b99c3-159">In den folgenden Themen erfahren Sie, wie Sie Ihre Anwendung für die entsprechenden Anbieter konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="b99c3-159">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="b99c3-160">Anweisungen für [Facebook](xref:security/authentication/facebook-logins)</span><span class="sxs-lookup"><span data-stu-id="b99c3-160">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="b99c3-161">Anweisungen für [Twitter](xref:security/authentication/twitter-logins)</span><span class="sxs-lookup"><span data-stu-id="b99c3-161">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="b99c3-162">Anweisungen für [Google](xref:security/authentication/google-logins)</span><span class="sxs-lookup"><span data-stu-id="b99c3-162">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="b99c3-163">Anweisungen für [Microsoft](xref:security/authentication/microsoft-logins)</span><span class="sxs-lookup"><span data-stu-id="b99c3-163">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="b99c3-164">Anweisungen für [andere Anbieter](xref:security/authentication/otherlogins)</span><span class="sxs-lookup"><span data-stu-id="b99c3-164">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="b99c3-165">Optionales Festlegen des Kennworts</span><span class="sxs-lookup"><span data-stu-id="b99c3-165">Optionally set password</span></span>

<span data-ttu-id="b99c3-166">Wenn Sie sich bei einem externen Anmeldeanbieter registrieren, wird kein Kennwort bei der App registriert.</span><span class="sxs-lookup"><span data-stu-id="b99c3-166">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="b99c3-167">Dadurch entfällt für Sie Erstellen und Merken eines Kennworts für die Website. Sie werden allerdings auch vom externen Anmeldeanbieter abhängig.</span><span class="sxs-lookup"><span data-stu-id="b99c3-167">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="b99c3-168">Wenn der externe Anmeldeanbieter nicht verfügbar ist, können Sie sich nicht bei der Website anmelden.</span><span class="sxs-lookup"><span data-stu-id="b99c3-168">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="b99c3-169">So erstellen Sie ein Kennwort und melden sich mithilfe Ihrer E-Mail-Adresse an, die Sie während des Anmeldevorgangs bei externen Anbietern festgelegt haben:</span><span class="sxs-lookup"><span data-stu-id="b99c3-169">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="b99c3-170">Klicken Sie rechts oben auf den Link **Hallo &lt;E-Mail-Alias&gt;** , um zur Ansicht **Verwalten** zu gelangen.</span><span class="sxs-lookup"><span data-stu-id="b99c3-170">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Ansicht „Verwalten“ der Webanwendung](index/_static/pass1a.png)

* <span data-ttu-id="b99c3-172">Klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="b99c3-172">Select **Create**</span></span>

![Seite zum Festlegen Ihres Kennworts](index/_static/pass2a.png)

* <span data-ttu-id="b99c3-174">Legen Sie ein gültiges Kennwort fest, das Sie anschließend mit Ihrer E-Mail-Adresse zum Anmelden nutzen können.</span><span class="sxs-lookup"><span data-stu-id="b99c3-174">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b99c3-175">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="b99c3-175">Next steps</span></span>

* <span data-ttu-id="b99c3-176">In diesem Artikel wurde die externe Authentifizierung behandelt. Ferner wurden die Voraussetzungen für das Hinzufügen externer Anmeldungen zu Ihrer ASP.NET Core-App erläutert.</span><span class="sxs-lookup"><span data-stu-id="b99c3-176">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>

* <span data-ttu-id="b99c3-177">Konsultieren Sie anbieterspezifische Seiten zum Konfigurieren von Anmeldungen für die von Ihrer App angeforderten Anbieter.</span><span class="sxs-lookup"><span data-stu-id="b99c3-177">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>

* <span data-ttu-id="b99c3-178">Möglicherweise möchten Sie zusätzliche Daten zum Benutzer und seinen Zugriffs- und Aktualisierungstoken persistent speichern.</span><span class="sxs-lookup"><span data-stu-id="b99c3-178">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="b99c3-179">Weitere Informationen finden Sie unter <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="b99c3-179">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
