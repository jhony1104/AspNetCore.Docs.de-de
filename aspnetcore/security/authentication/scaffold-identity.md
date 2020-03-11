---
title: Gerüst Identität in ASP.net Core Projekten
author: rick-anderson
description: Erfahren Sie, wie Sie die Identität in ein ASP.net Core Projekt eingerüstbau.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: b3e077aeac11e62d9e992884100476f7be35b59a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653713"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="7c3a4-103">Gerüst Identität in ASP.net Core Projekten</span><span class="sxs-lookup"><span data-stu-id="7c3a4-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="7c3a4-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7c3a4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7c3a4-105">ASP.net Core stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="7c3a4-106">Anwendungen, die die Identität enthalten, können das Gerüst zum selektiven Hinzufügen des Quellcodes verwenden, der in der Identity Razor Class Library (RCL) enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="7c3a4-107">Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="7c3a4-108">Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="7c3a4-109">Generierter Code hat Vorrang vor dem gleichen Code in der Razor-Klassenbibliothek „Identität“.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="7c3a4-110">Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen einer vollständigen identitätsquelle](#full)für die Identität.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="7c3a4-111">Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL-Identitäts Pakets anwenden.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="7c3a4-112">Sie können Code der Klassenbibliothek „Identität“ auswählen, der generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="7c3a4-113">Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="7c3a4-114">In diesem Dokument werden die erforderlichen Schritte zum Durchführen eines identitätsgerüstbau Updates erläutert.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="7c3a4-115">Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="7c3a4-116">Überprüfen Sie die Änderungen nach dem Ausführen des Identitäts gerüstems.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="7c3a4-117">Dienste sind erforderlich, wenn die [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)sowie andere Sicherheitsfeatures mit der Identität verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="7c3a4-118">Dienste oder Service-stubzeichen werden nicht generiert, wenn eine Gerüst Identität besteht</span><span class="sxs-lookup"><span data-stu-id="7c3a4-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="7c3a4-119">Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="7c3a4-120">Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)</span><span class="sxs-lookup"><span data-stu-id="7c3a4-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="7c3a4-121">Dieses Dokument enthält eine ausführlichere Anleitung als die Datei " *Gerüst* Datei", die generiert wird, wenn das Gerüst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-121">This document contains more complete instructions than the *ScaffoldingReadme.txt* file which is generated when running the scaffolder.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="7c3a4-122">Gerüst der Identität in ein leeres Projekt</span><span class="sxs-lookup"><span data-stu-id="7c3a4-122">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="7c3a4-123">Aktualisieren Sie die `Startup`-Klasse mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-123">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="7c3a4-124">Gerüst der Identität in ein Razor-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="7c3a4-124">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="7c3a4-125">Die Identität wird in " *Areas/Identity/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-125">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="7c3a4-126">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="7c3a4-126">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="7c3a4-127">Migrationen, UseAuthentication und Layout</span><span class="sxs-lookup"><span data-stu-id="7c3a4-127">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="7c3a4-128">Authentifizierung aktivieren</span><span class="sxs-lookup"><span data-stu-id="7c3a4-128">Enable authentication</span></span>

<span data-ttu-id="7c3a4-129">Aktualisieren Sie die `Startup`-Klasse mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-129">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="7c3a4-130">Layoutänderungen</span><span class="sxs-lookup"><span data-stu-id="7c3a4-130">Layout changes</span></span>

<span data-ttu-id="7c3a4-131">Optional: Fügen Sie die Anmelde-partielle (`_LoginPartial`) der Layoutdatei hinzu:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-131">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="7c3a4-132">Gerüst der Identität in ein Razor-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="7c3a4-132">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="7c3a4-133">Einige Identitäts Optionen werden in " *Bereiche/Identitäten/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-133">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="7c3a4-134">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="7c3a4-134">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="7c3a4-135">Gerüst der Identität in ein MVC-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="7c3a4-135">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="7c3a4-136">Optional: Fügen Sie die Anmeldungs partielle (`_LoginPartial`) der Datei *views/Shared/_Layout. cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-136">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="7c3a4-137">Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="7c3a4-137">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="7c3a4-138">Die Identität wird in " *Areas/Identity/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-138">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="7c3a4-139">Weitere Informationen finden Sie unter ihostingstartup.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-139">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="7c3a4-140">Aktualisieren Sie die `Startup`-Klasse mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-140">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="7c3a4-141">Gerüst der Identität in ein MVC-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="7c3a4-141">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="7c3a4-142">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="7c3a4-142">Create full identity UI source</span></span>

<span data-ttu-id="7c3a4-143">Um die vollständige Kontrolle über die Benutzeroberfläche der Identität aufrechtzuerhalten, führen Sie das Identitäts Gerüst aus, und wählen Sie **alle Dateien überschreiben**</span><span class="sxs-lookup"><span data-stu-id="7c3a4-143">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="7c3a4-144">Der folgende hervorgehobene Code zeigt die Änderungen, um die Standardbenutzer Oberfläche der Identität durch Identität in einer ASP.net Core 2,1-Web-App zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-144">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="7c3a4-145">Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle über die Identitäts Benutzeroberfläche zu haben.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-145">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="7c3a4-146">Die Standard Identität wird im folgenden Code ersetzt:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-146">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="7c3a4-147">Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-147">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="7c3a4-148">Registrieren Sie eine `IEmailSender` Implementierung, z. b.:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-148">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="7c3a4-149">Registerseite deaktivieren</span><span class="sxs-lookup"><span data-stu-id="7c3a4-149">Disable register page</span></span>

<span data-ttu-id="7c3a4-150">So deaktivieren Sie die Benutzerregistrierung:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-150">To disable user registration:</span></span>

* <span data-ttu-id="7c3a4-151">Gerüst Identität.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-151">Scaffold Identity.</span></span> <span data-ttu-id="7c3a4-152">Schließen Sie Account. Register, Account. Login und Account. registerconfirmation ein.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-152">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="7c3a4-153">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-153">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="7c3a4-154">Aktualisieren Sie *Areas/Identity/Pages/Account/Register. cshtml. cs* , damit Benutzer sich nicht bei diesem Endpunkt registrieren können:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-154">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="7c3a4-155">Aktualisieren Sie *Areas/Identity/Pages/Account/Register. cshtml* , damit Sie mit den vorangehenden Änderungen konsistent sind:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-155">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="7c3a4-156">Kommentieren Sie den Registrierungs Link aus *Bereichen/Identität/Seiten/Konto/Login. cshtml* aus, oder entfernen Sie ihn.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-156">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="7c3a4-157">Aktualisieren Sie die Seite *Bereiche/Identität/Seiten/Konto/Register Bestätigung* .</span><span class="sxs-lookup"><span data-stu-id="7c3a4-157">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="7c3a4-158">Entfernen Sie den Code und die Verknüpfungen aus der cshtml-Datei.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-158">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="7c3a4-159">Entfernen Sie den Bestätigungscode aus dem `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-159">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="7c3a4-160">Verwenden einer anderen APP zum Hinzufügen von Benutzern</span><span class="sxs-lookup"><span data-stu-id="7c3a4-160">Use another app to add users</span></span>

<span data-ttu-id="7c3a4-161">Stellen Sie einen Mechanismus zum Hinzufügen von Benutzern außerhalb der Web-App bereit.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-161">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="7c3a4-162">Zu den Optionen zum Hinzufügen von Benutzern gehören:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-162">Options to add users include:</span></span>

* <span data-ttu-id="7c3a4-163">Eine dedizierte admin-Web-App.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-163">A dedicated admin web app.</span></span>
* <span data-ttu-id="7c3a4-164">Eine Konsolen-app.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-164">A console app.</span></span>

<span data-ttu-id="7c3a4-165">Der folgende Code beschreibt einen Ansatz zum Hinzufügen von Benutzern:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-165">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="7c3a4-166">Eine Liste von Benutzern wird in den Arbeitsspeicher eingelesen.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-166">A list of users is read into memory.</span></span>
* <span data-ttu-id="7c3a4-167">Für jeden Benutzer wird ein sicheres Kennwort generiert.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-167">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="7c3a4-168">Der Benutzer wird der Identitätsdatenbank hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-168">The user is added to the Identity database.</span></span>
* <span data-ttu-id="7c3a4-169">Der Benutzer wird benachrichtigt und aufgefordert, das Kennwort zu ändern.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-169">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="7c3a4-170">Der folgende Code zeigt, wie Sie einen Benutzer hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-170">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="7c3a4-171">Ein ähnlicher Ansatz kann in Produktionsszenarien befolgt werden.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-171">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="7c3a4-172">Veröffentlichen statischer identitätsassets verhindern</span><span class="sxs-lookup"><span data-stu-id="7c3a4-172">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="7c3a4-173">Weitere Informationen darüber, wie Sie verhindern, dass statische Identitätsobjekte veröffentlicht werden, finden Sie unter <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-173">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c3a4-174">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7c3a4-174">Additional resources</span></span>

* [<span data-ttu-id="7c3a4-175">Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher</span><span class="sxs-lookup"><span data-stu-id="7c3a4-175">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7c3a4-176">ASP.net Core 2,1 und höher stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-176">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="7c3a4-177">Anwendungen, die die Identität enthalten, können das Gerüst zum selektiven Hinzufügen des Quellcodes verwenden, der in der Identity Razor Class Library (RCL) enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-177">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="7c3a4-178">Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-178">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="7c3a4-179">Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-179">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="7c3a4-180">Generierter Code hat Vorrang vor dem gleichen Code in der Razor-Klassenbibliothek „Identität“.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-180">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="7c3a4-181">Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen einer vollständigen identitätsquelle](#full)für die Identität.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-181">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="7c3a4-182">Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL-Identitäts Pakets anwenden.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-182">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="7c3a4-183">Sie können Code der Klassenbibliothek „Identität“ auswählen, der generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-183">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="7c3a4-184">Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-184">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="7c3a4-185">In diesem Dokument werden die erforderlichen Schritte zum Durchführen eines identitätsgerüstbau Updates erläutert.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-185">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="7c3a4-186">Wenn das Identitäts Gerüst ausgeführt wird, wird im Projektverzeichnis eine Datei mit *gerüdodingreadme. txt* erstellt.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-186">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="7c3a4-187">Die Datei " *gerüstoldingreadme. txt* " enthält allgemeine Anweisungen dazu, was zum Durchführen der Aktualisierung des Identitäts Gerüsts erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-187">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="7c3a4-188">Dieses Dokument enthält ausführlichere Anweisungen als die Datei " *Gerüst-dingreadme. txt* ".</span><span class="sxs-lookup"><span data-stu-id="7c3a4-188">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="7c3a4-189">Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-189">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="7c3a4-190">Überprüfen Sie die Änderungen nach dem Ausführen des Identitäts gerüstems.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-190">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="7c3a4-191">Dienste sind erforderlich, wenn die [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)sowie andere Sicherheitsfeatures mit der Identität verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-191">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="7c3a4-192">Dienste oder Service-stubzeichen werden nicht generiert, wenn eine Gerüst Identität besteht</span><span class="sxs-lookup"><span data-stu-id="7c3a4-192">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="7c3a4-193">Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-193">Services to enable these features must be added manually.</span></span> <span data-ttu-id="7c3a4-194">Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)</span><span class="sxs-lookup"><span data-stu-id="7c3a4-194">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="7c3a4-195">Gerüst der Identität in ein leeres Projekt</span><span class="sxs-lookup"><span data-stu-id="7c3a4-195">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="7c3a4-196">Fügen Sie die folgenden markierten Aufrufe der `Startup`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-196">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="7c3a4-197">Gerüst der Identität in ein Razor-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="7c3a4-197">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="7c3a4-198">Die Identität wird in " *Areas/Identity/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-198">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="7c3a4-199">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="7c3a4-199">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="7c3a4-200">Migrationen, UseAuthentication und Layout</span><span class="sxs-lookup"><span data-stu-id="7c3a4-200">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="7c3a4-201">Authentifizierung aktivieren</span><span class="sxs-lookup"><span data-stu-id="7c3a4-201">Enable authentication</span></span>

<span data-ttu-id="7c3a4-202">Nennen Sie in der `Configure`-Methode der `Startup`-Klasse nach `UseStaticFiles`die [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) :</span><span class="sxs-lookup"><span data-stu-id="7c3a4-202">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="7c3a4-203">Layoutänderungen</span><span class="sxs-lookup"><span data-stu-id="7c3a4-203">Layout changes</span></span>

<span data-ttu-id="7c3a4-204">Optional: Fügen Sie die Anmelde-partielle (`_LoginPartial`) der Layoutdatei hinzu:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-204">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="7c3a4-205">Gerüst der Identität in ein Razor-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="7c3a4-205">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="7c3a4-206">Einige Identitäts Optionen werden in " *Bereiche/Identitäten/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-206">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="7c3a4-207">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="7c3a4-207">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="7c3a4-208">Gerüst der Identität in ein MVC-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="7c3a4-208">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="7c3a4-209">Optional: Fügen Sie die Anmeldungs partielle (`_LoginPartial`) der Datei *views/Shared/_Layout. cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-209">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="7c3a4-210">Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="7c3a4-210">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="7c3a4-211">Die Identität wird in " *Areas/Identity/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-211">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="7c3a4-212">Weitere Informationen finden Sie unter ihostingstartup.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-212">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="7c3a4-213">[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach `UseStaticFiles`abrufen:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-213">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="7c3a4-214">Gerüst der Identität in ein MVC-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="7c3a4-214">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="7c3a4-215">Löschen Sie die *Seiten/* den freigegebenen Ordner und die Dateien in diesem Ordner.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-215">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="7c3a4-216">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="7c3a4-216">Create full identity UI source</span></span>

<span data-ttu-id="7c3a4-217">Um die vollständige Kontrolle über die Benutzeroberfläche der Identität aufrechtzuerhalten, führen Sie das Identitäts Gerüst aus, und wählen Sie **alle Dateien überschreiben**</span><span class="sxs-lookup"><span data-stu-id="7c3a4-217">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="7c3a4-218">Der folgende hervorgehobene Code zeigt die Änderungen, um die Standardbenutzer Oberfläche der Identität durch Identität in einer ASP.net Core 2,1-Web-App zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-218">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="7c3a4-219">Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle über die Identitäts Benutzeroberfläche zu haben.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-219">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="7c3a4-220">Die Standard Identität wird im folgenden Code ersetzt:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-220">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="7c3a4-221">Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-221">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="7c3a4-222">Registrieren Sie eine `IEmailSender` Implementierung, z. b.:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-222">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="7c3a4-223">Registerseite deaktivieren</span><span class="sxs-lookup"><span data-stu-id="7c3a4-223">Disable register page</span></span>

<span data-ttu-id="7c3a4-224">So deaktivieren Sie die Benutzerregistrierung:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-224">To disable user registration:</span></span>

* <span data-ttu-id="7c3a4-225">Gerüst Identität.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-225">Scaffold Identity.</span></span> <span data-ttu-id="7c3a4-226">Schließen Sie Account. Register, Account. Login und Account. registerconfirmation ein.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-226">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="7c3a4-227">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-227">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="7c3a4-228">Aktualisieren Sie *Areas/Identity/Pages/Account/Register. cshtml. cs* , damit Benutzer sich nicht bei diesem Endpunkt registrieren können:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-228">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="7c3a4-229">Aktualisieren Sie *Areas/Identity/Pages/Account/Register. cshtml* , damit Sie mit den vorangehenden Änderungen konsistent sind:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-229">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="7c3a4-230">Kommentieren Sie den Registrierungs Link aus *Bereichen/Identität/Seiten/Konto/Login. cshtml* aus, oder entfernen Sie ihn.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-230">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="7c3a4-231">Aktualisieren Sie die Seite *Bereiche/Identität/Seiten/Konto/Register Bestätigung* .</span><span class="sxs-lookup"><span data-stu-id="7c3a4-231">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="7c3a4-232">Entfernen Sie den Code und die Verknüpfungen aus der cshtml-Datei.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-232">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="7c3a4-233">Entfernen Sie den Bestätigungscode aus dem `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-233">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="7c3a4-234">Verwenden einer anderen APP zum Hinzufügen von Benutzern</span><span class="sxs-lookup"><span data-stu-id="7c3a4-234">Use another app to add users</span></span>

<span data-ttu-id="7c3a4-235">Stellen Sie einen Mechanismus zum Hinzufügen von Benutzern außerhalb der Web-App bereit.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-235">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="7c3a4-236">Zu den Optionen zum Hinzufügen von Benutzern gehören:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-236">Options to add users include:</span></span>

* <span data-ttu-id="7c3a4-237">Eine dedizierte admin-Web-App.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-237">A dedicated admin web app.</span></span>
* <span data-ttu-id="7c3a4-238">Eine Konsolen-app.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-238">A console app.</span></span>

<span data-ttu-id="7c3a4-239">Der folgende Code beschreibt einen Ansatz zum Hinzufügen von Benutzern:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-239">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="7c3a4-240">Eine Liste von Benutzern wird in den Arbeitsspeicher eingelesen.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-240">A list of users is read into memory.</span></span>
* <span data-ttu-id="7c3a4-241">Für jeden Benutzer wird ein sicheres Kennwort generiert.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-241">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="7c3a4-242">Der Benutzer wird der Identitätsdatenbank hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-242">The user is added to the Identity database.</span></span>
* <span data-ttu-id="7c3a4-243">Der Benutzer wird benachrichtigt und aufgefordert, das Kennwort zu ändern.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-243">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="7c3a4-244">Der folgende Code zeigt, wie Sie einen Benutzer hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="7c3a4-244">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="7c3a4-245">Ein ähnlicher Ansatz kann in Produktionsszenarien befolgt werden.</span><span class="sxs-lookup"><span data-stu-id="7c3a4-245">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7c3a4-246">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7c3a4-246">Additional resources</span></span>

* [<span data-ttu-id="7c3a4-247">Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher</span><span class="sxs-lookup"><span data-stu-id="7c3a4-247">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end