---
title: Gerüst Identität in ASP.net Core Projekten
author: rick-anderson
description: Erfahren Sie, wie Sie die Identität in ein ASP.net Core Projekt eingerüstbau.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ac95035b114274ddaa6ccb0b5b6e3da98885e39e
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604726"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="3a3e6-103">Gerüst Identität in ASP.net Core Projekten</span><span class="sxs-lookup"><span data-stu-id="3a3e6-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="3a3e6-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3a3e6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3a3e6-105">ASP.net Core stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="3a3e6-106">Anwendungen, die die Identität enthalten, können das Gerüst zum selektiven Hinzufügen des Quellcodes verwenden, der in der Identity Razor Class Library (RCL) enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="3a3e6-107">Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="3a3e6-108">Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="3a3e6-109">Generierter Code hat Vorrang vor dem gleichen Code in der Razor-Klassenbibliothek „Identität“.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="3a3e6-110">Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen einer vollständigen identitätsquelle](#full)für die Identität.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="3a3e6-111">Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL-Identitäts Pakets anwenden.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="3a3e6-112">Sie können Code der Klassenbibliothek „Identität“ auswählen, der generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="3a3e6-113">Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="3a3e6-114">In diesem Dokument werden die erforderlichen Schritte zum Durchführen eines identitätsgerüstbau Updates erläutert.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="3a3e6-115">Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="3a3e6-116">Überprüfen Sie die Änderungen nach dem Ausführen des Identitäts gerüstems.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="3a3e6-117">Dienste sind erforderlich, wenn die [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)sowie andere Sicherheitsfeatures mit der Identität verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="3a3e6-118">Dienste oder Service-stubzeichen werden nicht generiert, wenn eine Gerüst Identität besteht</span><span class="sxs-lookup"><span data-stu-id="3a3e6-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="3a3e6-119">Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="3a3e6-120">Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)</span><span class="sxs-lookup"><span data-stu-id="3a3e6-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="3a3e6-121">Beim Gerüstbau der Identität mit einem neuen Datenkontext in einem Projekt mit vorhandenen individuellen Konten:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="3a3e6-122">Entfernen `Startup.ConfigureServices`Sie in die Aufrufe von:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="3a3e6-123">Beispielsweise werden `AddDbContext` und `AddDefaultIdentity` im folgenden Code auskommentiert:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="3a3e6-124">Der vorangehende Code kommentiert den Code, der in " *Areas/Identity/identityhostingstartup. cs* " dupliziert wird.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="3a3e6-125">Apps, die mit einzelnen Konten erstellt wurden, sollten in der Regel ***keinen*** neuen Datenkontext erstellen.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="3a3e6-126">Gerüst der Identität in ein leeres Projekt</span><span class="sxs-lookup"><span data-stu-id="3a3e6-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="3a3e6-127">Aktualisieren Sie `Startup` die-Klasse mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="3a3e6-128">Gerüst der Identität in ein Razor-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="3a3e6-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="3a3e6-129">Die Identität wird in " *Areas/Identity/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3a3e6-130">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3a3e6-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="3a3e6-131">Migrationen, UseAuthentication und Layout</span><span class="sxs-lookup"><span data-stu-id="3a3e6-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="3a3e6-132">Authentifizierung aktivieren</span><span class="sxs-lookup"><span data-stu-id="3a3e6-132">Enable authentication</span></span>

<span data-ttu-id="3a3e6-133">Aktualisieren Sie `Startup` die-Klasse mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="3a3e6-134">Layoutänderungen</span><span class="sxs-lookup"><span data-stu-id="3a3e6-134">Layout changes</span></span>

<span data-ttu-id="3a3e6-135">Optional: Fügen Sie den Anmelde Namen`_LoginPartial`partiell () der Layoutdatei hinzu:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="3a3e6-136">Gerüst der Identität in ein Razor-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="3a3e6-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="3a3e6-137">Einige Identitäts Optionen werden in " *Bereiche/Identitäten/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3a3e6-138">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3a3e6-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="3a3e6-139">Gerüst der Identität in ein MVC-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="3a3e6-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="3a3e6-140">Optional: Fügen Sie der Datei`_LoginPartial` *views/Shared/_Layout. cshtml* den Anmelde Namen partiell () hinzu:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="3a3e6-141">Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="3a3e6-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="3a3e6-142">Die Identität wird in " *Areas/Identity/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3a3e6-143">Weitere Informationen finden Sie unter ihostingstartup.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="3a3e6-144">Aktualisieren Sie `Startup` die-Klasse mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="3a3e6-145">Gerüst der Identität in ein MVC-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="3a3e6-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="3a3e6-146">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="3a3e6-146">Create full identity UI source</span></span>

<span data-ttu-id="3a3e6-147">Um die vollständige Kontrolle über die Benutzeroberfläche der Identität aufrechtzuerhalten, führen Sie das Identitäts Gerüst aus, und wählen Sie **alle Dateien überschreiben**</span><span class="sxs-lookup"><span data-stu-id="3a3e6-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="3a3e6-148">Der folgende hervorgehobene Code zeigt die Änderungen, um die Standardbenutzer Oberfläche der Identität durch Identität in einer ASP.net Core 2,1-Web-App zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="3a3e6-149">Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle über die Identitäts Benutzeroberfläche zu haben.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="3a3e6-150">Die Standard Identität wird im folgenden Code ersetzt:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="3a3e6-151">Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="3a3e6-152">Registrieren Sie `IEmailSender` eine Implementierung, z. b.:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="3a3e6-153">Registerseite deaktivieren</span><span class="sxs-lookup"><span data-stu-id="3a3e6-153">Disable register page</span></span>

<span data-ttu-id="3a3e6-154">So deaktivieren Sie die Benutzerregistrierung:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-154">To disable user registration:</span></span>

* <span data-ttu-id="3a3e6-155">Gerüst Identität.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-155">Scaffold Identity.</span></span> <span data-ttu-id="3a3e6-156">Schließen Sie Account. Register, Account. Login und Account. registerconfirmation ein.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="3a3e6-157">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="3a3e6-158">Aktualisieren Sie *Areas/Identity/Pages/Account/Register. cshtml. cs* , damit Benutzer sich nicht bei diesem Endpunkt registrieren können:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="3a3e6-159">Aktualisieren Sie *Areas/Identity/Pages/Account/Register. cshtml* , damit Sie mit den vorangehenden Änderungen konsistent sind:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="3a3e6-160">Kommentieren Sie den Registrierungs Link aus *Bereichen/Identität/Seiten/Konto/Login. cshtml* aus, oder entfernen Sie ihn.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="3a3e6-161">Aktualisieren Sie die Seite *Bereiche/Identität/Seiten/Konto/Register Bestätigung* .</span><span class="sxs-lookup"><span data-stu-id="3a3e6-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="3a3e6-162">Entfernen Sie den Code und die Verknüpfungen aus der cshtml-Datei.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="3a3e6-163">Entfernen Sie den Bestätigungscode aus `PageModel`dem:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="3a3e6-164">Verwenden einer anderen APP zum Hinzufügen von Benutzern</span><span class="sxs-lookup"><span data-stu-id="3a3e6-164">Use another app to add users</span></span>

<span data-ttu-id="3a3e6-165">Stellen Sie einen Mechanismus zum Hinzufügen von Benutzern außerhalb der Web-App bereit.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="3a3e6-166">Zu den Optionen zum Hinzufügen von Benutzern gehören:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-166">Options to add users include:</span></span>

* <span data-ttu-id="3a3e6-167">Eine dedizierte admin-Web-App.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="3a3e6-168">Eine Konsolen-app.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-168">A console app.</span></span>

<span data-ttu-id="3a3e6-169">Der folgende Code beschreibt einen Ansatz zum Hinzufügen von Benutzern:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="3a3e6-170">Eine Liste von Benutzern wird in den Arbeitsspeicher eingelesen.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="3a3e6-171">Für jeden Benutzer wird ein sicheres Kennwort generiert.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="3a3e6-172">Der Benutzer wird der Identitätsdatenbank hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="3a3e6-173">Der Benutzer wird benachrichtigt und aufgefordert, das Kennwort zu ändern.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="3a3e6-174">Der folgende Code zeigt, wie Sie einen Benutzer hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="3a3e6-175">Ein ähnlicher Ansatz kann in Produktionsszenarien befolgt werden.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="3a3e6-176">Veröffentlichen statischer identitätsassets verhindern</span><span class="sxs-lookup"><span data-stu-id="3a3e6-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="3a3e6-177">Weitere Informationen darüber, wie Sie verhindern, dass statische Identitätsobjekte veröffentlicht werden, finden Sie unter <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3a3e6-178">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3a3e6-178">Additional resources</span></span>

* [<span data-ttu-id="3a3e6-179">Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher</span><span class="sxs-lookup"><span data-stu-id="3a3e6-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3a3e6-180">ASP.net Core 2,1 und höher stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="3a3e6-181">Anwendungen, die die Identität enthalten, können das Gerüst zum selektiven Hinzufügen des Quellcodes verwenden, der in der Identity Razor Class Library (RCL) enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="3a3e6-182">Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="3a3e6-183">Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="3a3e6-184">Generierter Code hat Vorrang vor dem gleichen Code in der Razor-Klassenbibliothek „Identität“.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="3a3e6-185">Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen einer vollständigen identitätsquelle](#full)für die Identität.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="3a3e6-186">Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL-Identitäts Pakets anwenden.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="3a3e6-187">Sie können Code der Klassenbibliothek „Identität“ auswählen, der generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="3a3e6-188">Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="3a3e6-189">In diesem Dokument werden die erforderlichen Schritte zum Durchführen eines identitätsgerüstbau Updates erläutert.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="3a3e6-190">Wenn das Identitäts Gerüst ausgeführt wird, wird im Projektverzeichnis eine Datei mit *gerüdodingreadme. txt* erstellt.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="3a3e6-191">Die Datei " *gerüstoldingreadme. txt* " enthält allgemeine Anweisungen dazu, was zum Durchführen der Aktualisierung des Identitäts Gerüsts erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="3a3e6-192">Dieses Dokument enthält ausführlichere Anweisungen als die Datei " *Gerüst-dingreadme. txt* ".</span><span class="sxs-lookup"><span data-stu-id="3a3e6-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="3a3e6-193">Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="3a3e6-194">Überprüfen Sie die Änderungen nach dem Ausführen des Identitäts gerüstems.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="3a3e6-195">Dienste sind erforderlich, wenn die [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)sowie andere Sicherheitsfeatures mit der Identität verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="3a3e6-196">Dienste oder Service-stubzeichen werden nicht generiert, wenn eine Gerüst Identität besteht</span><span class="sxs-lookup"><span data-stu-id="3a3e6-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="3a3e6-197">Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="3a3e6-198">Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)</span><span class="sxs-lookup"><span data-stu-id="3a3e6-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="3a3e6-199">Gerüst der Identität in ein leeres Projekt</span><span class="sxs-lookup"><span data-stu-id="3a3e6-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="3a3e6-200">Fügen Sie die folgenden markierten Aufrufe der `Startup` -Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="3a3e6-201">Gerüst der Identität in ein Razor-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="3a3e6-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="3a3e6-202">Die Identität wird in " *Areas/Identity/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-202">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3a3e6-203">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3a3e6-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="3a3e6-204">Migrationen, UseAuthentication und Layout</span><span class="sxs-lookup"><span data-stu-id="3a3e6-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="3a3e6-205">Authentifizierung aktivieren</span><span class="sxs-lookup"><span data-stu-id="3a3e6-205">Enable authentication</span></span>

<span data-ttu-id="3a3e6-206">In der `Configure` -Methode der `Startup` -Klasse wird [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach `UseStaticFiles`folgenden Aktionen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="3a3e6-207">Layoutänderungen</span><span class="sxs-lookup"><span data-stu-id="3a3e6-207">Layout changes</span></span>

<span data-ttu-id="3a3e6-208">Optional: Fügen Sie den Anmelde Namen`_LoginPartial`partiell () der Layoutdatei hinzu:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="3a3e6-209">Gerüst der Identität in ein Razor-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="3a3e6-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="3a3e6-210">Einige Identitäts Optionen werden in " *Bereiche/Identitäten/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3a3e6-211">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="3a3e6-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="3a3e6-212">Gerüst der Identität in ein MVC-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="3a3e6-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="3a3e6-213">Optional: Fügen Sie der Datei`_LoginPartial` *views/Shared/_Layout. cshtml* den Anmelde Namen partiell () hinzu:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="3a3e6-214">Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="3a3e6-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="3a3e6-215">Die Identität wird in " *Areas/Identity/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-215">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="3a3e6-216">Weitere Informationen finden Sie unter ihostingstartup.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="3a3e6-217">[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach `UseStaticFiles`folgenden Aktionen abrufen:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="3a3e6-218">Gerüst der Identität in ein MVC-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="3a3e6-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="3a3e6-219">Löschen Sie die *Seiten/* den freigegebenen Ordner und die Dateien in diesem Ordner.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="3a3e6-220">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="3a3e6-220">Create full identity UI source</span></span>

<span data-ttu-id="3a3e6-221">Um die vollständige Kontrolle über die Benutzeroberfläche der Identität aufrechtzuerhalten, führen Sie das Identitäts Gerüst aus, und wählen Sie **alle Dateien überschreiben**</span><span class="sxs-lookup"><span data-stu-id="3a3e6-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="3a3e6-222">Der folgende hervorgehobene Code zeigt die Änderungen, um die Standardbenutzer Oberfläche der Identität durch Identität in einer ASP.net Core 2,1-Web-App zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="3a3e6-223">Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle über die Identitäts Benutzeroberfläche zu haben.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="3a3e6-224">Die Standard Identität wird im folgenden Code ersetzt:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="3a3e6-225">Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="3a3e6-226">Registrieren Sie `IEmailSender` eine Implementierung, z. b.:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="3a3e6-227">Registerseite deaktivieren</span><span class="sxs-lookup"><span data-stu-id="3a3e6-227">Disable register page</span></span>

<span data-ttu-id="3a3e6-228">So deaktivieren Sie die Benutzerregistrierung:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-228">To disable user registration:</span></span>

* <span data-ttu-id="3a3e6-229">Gerüst Identität.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-229">Scaffold Identity.</span></span> <span data-ttu-id="3a3e6-230">Schließen Sie Account. Register, Account. Login und Account. registerconfirmation ein.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="3a3e6-231">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="3a3e6-232">Aktualisieren Sie *Areas/Identity/Pages/Account/Register. cshtml. cs* , damit Benutzer sich nicht bei diesem Endpunkt registrieren können:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="3a3e6-233">Aktualisieren Sie *Areas/Identity/Pages/Account/Register. cshtml* , damit Sie mit den vorangehenden Änderungen konsistent sind:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="3a3e6-234">Kommentieren Sie den Registrierungs Link aus *Bereichen/Identität/Seiten/Konto/Login. cshtml* aus, oder entfernen Sie ihn.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="3a3e6-235">Aktualisieren Sie die Seite *Bereiche/Identität/Seiten/Konto/Register Bestätigung* .</span><span class="sxs-lookup"><span data-stu-id="3a3e6-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="3a3e6-236">Entfernen Sie den Code und die Verknüpfungen aus der cshtml-Datei.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="3a3e6-237">Entfernen Sie den Bestätigungscode aus `PageModel`dem:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="3a3e6-238">Verwenden einer anderen APP zum Hinzufügen von Benutzern</span><span class="sxs-lookup"><span data-stu-id="3a3e6-238">Use another app to add users</span></span>

<span data-ttu-id="3a3e6-239">Stellen Sie einen Mechanismus zum Hinzufügen von Benutzern außerhalb der Web-App bereit.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="3a3e6-240">Zu den Optionen zum Hinzufügen von Benutzern gehören:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-240">Options to add users include:</span></span>

* <span data-ttu-id="3a3e6-241">Eine dedizierte admin-Web-App.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="3a3e6-242">Eine Konsolen-app.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-242">A console app.</span></span>

<span data-ttu-id="3a3e6-243">Der folgende Code beschreibt einen Ansatz zum Hinzufügen von Benutzern:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="3a3e6-244">Eine Liste von Benutzern wird in den Arbeitsspeicher eingelesen.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="3a3e6-245">Für jeden Benutzer wird ein sicheres Kennwort generiert.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="3a3e6-246">Der Benutzer wird der Identitätsdatenbank hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="3a3e6-247">Der Benutzer wird benachrichtigt und aufgefordert, das Kennwort zu ändern.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="3a3e6-248">Der folgende Code zeigt, wie Sie einen Benutzer hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="3a3e6-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="3a3e6-249">Ein ähnlicher Ansatz kann in Produktionsszenarien befolgt werden.</span><span class="sxs-lookup"><span data-stu-id="3a3e6-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3a3e6-250">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3a3e6-250">Additional resources</span></span>

* [<span data-ttu-id="3a3e6-251">Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher</span><span class="sxs-lookup"><span data-stu-id="3a3e6-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end