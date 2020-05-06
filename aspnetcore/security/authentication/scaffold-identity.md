---
title: Gerüst Identity in ASP.net Core Projekten
author: rick-anderson
description: Erfahren Sie, wie Sie Identity ein Gerüst in einem ASP.net Core Projekt aufbauen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768389"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="a0701-103">Gerüst Identity in ASP.net Core Projekten</span><span class="sxs-lookup"><span data-stu-id="a0701-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="a0701-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a0701-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a0701-105">ASP.net Core stellt [ASP.net Core Identity ](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="a0701-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="a0701-106">Anwendungen, die Identity einschließen, können das Gerüsten anwenden, um den Quellcode, der in Identity Razor der Klassenbibliothek (RCL) enthalten ist, selektiv hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="a0701-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="a0701-107">Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können.</span><span class="sxs-lookup"><span data-stu-id="a0701-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="a0701-108">Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a0701-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="a0701-109">Generierter Code hat Vorrang vor dem gleichen Code in Identity der RCL.</span><span class="sxs-lookup"><span data-stu-id="a0701-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="a0701-110">Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen einer vollständigen identitätsquelle](#full)für die Identität.</span><span class="sxs-lookup"><span data-stu-id="a0701-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="a0701-111">Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL Identity -Pakets anwenden.</span><span class="sxs-lookup"><span data-stu-id="a0701-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="a0701-112">Sie können Code auswählen Identity , der generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="a0701-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="a0701-113">Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="a0701-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="a0701-114">In diesem Dokument werden die erforderlichen Schritte zum durch Identity führen eines Gerüstbau Updates erläutert.</span><span class="sxs-lookup"><span data-stu-id="a0701-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="a0701-115">Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen.</span><span class="sxs-lookup"><span data-stu-id="a0701-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="a0701-116">Überprüfen Sie die Änderungen, Identity nachdem Sie das Gerüst ausgeführt haben.</span><span class="sxs-lookup"><span data-stu-id="a0701-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="a0701-117">Dienste sind erforderlich, wenn [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)und andere Sicherheitsfunktionen Identitymit verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a0701-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="a0701-118">Dienste oder Service-stubdienste werden beim IdentityGerüstbau nicht generiert.</span><span class="sxs-lookup"><span data-stu-id="a0701-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="a0701-119">Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a0701-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="a0701-120">Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)</span><span class="sxs-lookup"><span data-stu-id="a0701-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="a0701-121">Beim Gerüstbau Identity mit einem neuen Datenkontext in einem Projekt mit vorhandenen individuellen Konten:</span><span class="sxs-lookup"><span data-stu-id="a0701-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="a0701-122">Entfernen `Startup.ConfigureServices`Sie in die Aufrufe von:</span><span class="sxs-lookup"><span data-stu-id="a0701-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="a0701-123">Beispielsweise werden `AddDbContext` und `AddDefaultIdentity` im folgenden Code auskommentiert:</span><span class="sxs-lookup"><span data-stu-id="a0701-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="a0701-124">Der vorangehende Code kommentiert den Code, der in " *Areas/Identity/IdentityHostingStartup.cs* " dupliziert wird.</span><span class="sxs-lookup"><span data-stu-id="a0701-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="a0701-125">Apps, die mit einzelnen Konten erstellt wurden, sollten in der Regel ***keinen*** neuen Datenkontext erstellen.</span><span class="sxs-lookup"><span data-stu-id="a0701-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="a0701-126">Gerüst der Identität in ein leeres Projekt</span><span class="sxs-lookup"><span data-stu-id="a0701-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="a0701-127">Aktualisieren Sie `Startup` die-Klasse mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="a0701-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="a0701-128">Gerüst der Identität in ein Razor Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="a0701-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="a0701-129">wird in " *Bereiche/Identity/IdentityHostingStartup.cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a0701-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a0701-130">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="a0701-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="a0701-131">Migrationen, UseAuthentication und Layout</span><span class="sxs-lookup"><span data-stu-id="a0701-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="a0701-132">Authentifizierung aktivieren</span><span class="sxs-lookup"><span data-stu-id="a0701-132">Enable authentication</span></span>

<span data-ttu-id="a0701-133">Aktualisieren Sie `Startup` die-Klasse mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="a0701-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="a0701-134">Layoutänderungen</span><span class="sxs-lookup"><span data-stu-id="a0701-134">Layout changes</span></span>

<span data-ttu-id="a0701-135">Optional: Fügen Sie den Anmelde Namen`_LoginPartial`partiell () der Layoutdatei hinzu:</span><span class="sxs-lookup"><span data-stu-id="a0701-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="a0701-136">Gerüst der Identität in ein Razor Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="a0701-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="a0701-137">Einige Identity Optionen werden in " *Bereiche/Identity/IdentityHostingStartup.cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a0701-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a0701-138">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="a0701-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="a0701-139">Gerüst der Identität in ein MVC-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="a0701-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="a0701-140">Optional: Fügen Sie der Datei`_LoginPartial` *views/Shared/_Layout. cshtml* den Anmelde Namen partiell () hinzu:</span><span class="sxs-lookup"><span data-stu-id="a0701-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="a0701-141">Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="a0701-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="a0701-142">wird in " *Bereiche/Identity/IdentityHostingStartup.cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a0701-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a0701-143">Weitere Informationen finden Sie unter ihostingstartup.</span><span class="sxs-lookup"><span data-stu-id="a0701-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="a0701-144">Aktualisieren Sie `Startup` die-Klasse mit Code, der dem folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="a0701-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="a0701-145">Gerüst der Identität in ein MVC-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="a0701-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="a0701-146">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="a0701-146">Create full identity UI source</span></span>

<span data-ttu-id="a0701-147">Um die vollständige Kontrolle über Identity die Benutzeroberfläche zu Identity behalten, führen Sie das Gerüst aus, und wählen Sie **alle Dateien überschreiben**</span><span class="sxs-lookup"><span data-stu-id="a0701-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="a0701-148">Der folgende markierte Code zeigt die Änderungen zum Ersetzen der Standard Identity Benutzeroberfläche Identity durch in einer ASP.net Core 2,1-Web-App.</span><span class="sxs-lookup"><span data-stu-id="a0701-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="a0701-149">Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle Identity über die Benutzeroberfläche zu haben.</span><span class="sxs-lookup"><span data-stu-id="a0701-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="a0701-150">Der Standard Identity Wert wird im folgenden Code ersetzt:</span><span class="sxs-lookup"><span data-stu-id="a0701-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="a0701-151">Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:</span><span class="sxs-lookup"><span data-stu-id="a0701-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="a0701-152">Registrieren Sie `IEmailSender` eine Implementierung, z. b.:</span><span class="sxs-lookup"><span data-stu-id="a0701-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="a0701-153">Registerseite deaktivieren</span><span class="sxs-lookup"><span data-stu-id="a0701-153">Disable register page</span></span>

<span data-ttu-id="a0701-154">So deaktivieren Sie die Benutzerregistrierung:</span><span class="sxs-lookup"><span data-stu-id="a0701-154">To disable user registration:</span></span>

* <span data-ttu-id="a0701-155">Gerüstbau Identity.</span><span class="sxs-lookup"><span data-stu-id="a0701-155">Scaffold Identity.</span></span> <span data-ttu-id="a0701-156">Schließen Sie Account. Register, Account. Login und Account. registerconfirmation ein.</span><span class="sxs-lookup"><span data-stu-id="a0701-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="a0701-157">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a0701-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="a0701-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so können sich Benutzer nicht bei diesem Endpunkt registrieren:</span><span class="sxs-lookup"><span data-stu-id="a0701-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="a0701-159">Aktualisieren Sie *BereicheIdentity//Pages/Account/Register.cshtml* , damit Sie mit den vorangehenden Änderungen konsistent sind:</span><span class="sxs-lookup"><span data-stu-id="a0701-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="a0701-160">Kommentieren Sie den Registrierungs Link aus *Bereichen/Identity/Pages/Account/Login.cshtml* aus, oder entfernen Sie ihn.</span><span class="sxs-lookup"><span data-stu-id="a0701-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="a0701-161">Aktualisieren Sie die Seite " *Bereiche/Identity/Pages/Account/RegisterConfirmation* ".</span><span class="sxs-lookup"><span data-stu-id="a0701-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="a0701-162">Entfernen Sie den Code und die Verknüpfungen aus der cshtml-Datei.</span><span class="sxs-lookup"><span data-stu-id="a0701-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="a0701-163">Entfernen Sie den Bestätigungscode aus `PageModel`dem:</span><span class="sxs-lookup"><span data-stu-id="a0701-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="a0701-164">Verwenden einer anderen APP zum Hinzufügen von Benutzern</span><span class="sxs-lookup"><span data-stu-id="a0701-164">Use another app to add users</span></span>

<span data-ttu-id="a0701-165">Stellen Sie einen Mechanismus zum Hinzufügen von Benutzern außerhalb der Web-App bereit.</span><span class="sxs-lookup"><span data-stu-id="a0701-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="a0701-166">Zu den Optionen zum Hinzufügen von Benutzern gehören:</span><span class="sxs-lookup"><span data-stu-id="a0701-166">Options to add users include:</span></span>

* <span data-ttu-id="a0701-167">Eine dedizierte admin-Web-App.</span><span class="sxs-lookup"><span data-stu-id="a0701-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="a0701-168">Eine Konsolen-app.</span><span class="sxs-lookup"><span data-stu-id="a0701-168">A console app.</span></span>

<span data-ttu-id="a0701-169">Der folgende Code beschreibt einen Ansatz zum Hinzufügen von Benutzern:</span><span class="sxs-lookup"><span data-stu-id="a0701-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="a0701-170">Eine Liste von Benutzern wird in den Arbeitsspeicher eingelesen.</span><span class="sxs-lookup"><span data-stu-id="a0701-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="a0701-171">Für jeden Benutzer wird ein sicheres Kennwort generiert.</span><span class="sxs-lookup"><span data-stu-id="a0701-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="a0701-172">Der Benutzer wird der Identity -Datenbank hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="a0701-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="a0701-173">Der Benutzer wird benachrichtigt und aufgefordert, das Kennwort zu ändern.</span><span class="sxs-lookup"><span data-stu-id="a0701-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="a0701-174">Der folgende Code zeigt, wie Sie einen Benutzer hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="a0701-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="a0701-175">Ein ähnlicher Ansatz kann in Produktionsszenarien befolgt werden.</span><span class="sxs-lookup"><span data-stu-id="a0701-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="a0701-176">Veröffentlichen statischer Identity Assets verhindern</span><span class="sxs-lookup"><span data-stu-id="a0701-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="a0701-177">Informationen zum Verhindern der Identity Veröffentlichung statischer Assets im Webstamm <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>finden Sie unter.</span><span class="sxs-lookup"><span data-stu-id="a0701-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0701-178">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a0701-178">Additional resources</span></span>

* [<span data-ttu-id="a0701-179">Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher</span><span class="sxs-lookup"><span data-stu-id="a0701-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a0701-180">ASP.net Core 2,1 und höher [ Identity ASP.net Core](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="a0701-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="a0701-181">Anwendungen, die Identity einschließen, können das Gerüsten anwenden, um den Quellcode, der in Identity Razor der Klassenbibliothek (RCL) enthalten ist, selektiv hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="a0701-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="a0701-182">Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können.</span><span class="sxs-lookup"><span data-stu-id="a0701-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="a0701-183">Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a0701-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="a0701-184">Generierter Code hat Vorrang vor dem gleichen Code in Identity der RCL.</span><span class="sxs-lookup"><span data-stu-id="a0701-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="a0701-185">Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen einer vollständigen identitätsquelle](#full)für die Identität.</span><span class="sxs-lookup"><span data-stu-id="a0701-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="a0701-186">Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL Identity -Pakets anwenden.</span><span class="sxs-lookup"><span data-stu-id="a0701-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="a0701-187">Sie können Code auswählen Identity , der generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="a0701-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="a0701-188">Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="a0701-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="a0701-189">In diesem Dokument werden die erforderlichen Schritte zum durch Identity führen eines Gerüstbau Updates erläutert.</span><span class="sxs-lookup"><span data-stu-id="a0701-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="a0701-190">Wenn das Identity Gerüst ausgeführt wird, wird im Projektverzeichnis eine Datei mit *gerüdodingreadme. txt* erstellt.</span><span class="sxs-lookup"><span data-stu-id="a0701-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="a0701-191">Die Datei " *gerüstoldingreadme. txt* " enthält allgemeine Anweisungen dazu, was zum Identity Abschluss des Gerüstbau Updates erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="a0701-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="a0701-192">Dieses Dokument enthält ausführlichere Anweisungen als die Datei " *Gerüst-dingreadme. txt* ".</span><span class="sxs-lookup"><span data-stu-id="a0701-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="a0701-193">Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen.</span><span class="sxs-lookup"><span data-stu-id="a0701-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="a0701-194">Überprüfen Sie die Änderungen, Identity nachdem Sie das Gerüst ausgeführt haben.</span><span class="sxs-lookup"><span data-stu-id="a0701-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="a0701-195">Dienste sind erforderlich, wenn [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)und andere Sicherheitsfunktionen Identitymit verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a0701-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="a0701-196">Dienste oder Service-stubdienste werden beim IdentityGerüstbau nicht generiert.</span><span class="sxs-lookup"><span data-stu-id="a0701-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="a0701-197">Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a0701-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="a0701-198">Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)</span><span class="sxs-lookup"><span data-stu-id="a0701-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="a0701-199">Gerüst der Identität in ein leeres Projekt</span><span class="sxs-lookup"><span data-stu-id="a0701-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="a0701-200">Fügen Sie die folgenden markierten Aufrufe der `Startup` -Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="a0701-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="a0701-201">Gerüst der Identität in ein Razor Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="a0701-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="a0701-202">wird in " *Bereiche/Identity/IdentityHostingStartup.cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a0701-202"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a0701-203">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="a0701-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="a0701-204">Migrationen, UseAuthentication und Layout</span><span class="sxs-lookup"><span data-stu-id="a0701-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="a0701-205">Authentifizierung aktivieren</span><span class="sxs-lookup"><span data-stu-id="a0701-205">Enable authentication</span></span>

<span data-ttu-id="a0701-206">In der `Configure` -Methode der `Startup` -Klasse wird [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach `UseStaticFiles`folgenden Aktionen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="a0701-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="a0701-207">Layoutänderungen</span><span class="sxs-lookup"><span data-stu-id="a0701-207">Layout changes</span></span>

<span data-ttu-id="a0701-208">Optional: Fügen Sie den Anmelde Namen`_LoginPartial`partiell () der Layoutdatei hinzu:</span><span class="sxs-lookup"><span data-stu-id="a0701-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="a0701-209">Gerüst der Identität in ein Razor Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="a0701-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="a0701-210">Einige Identity Optionen werden in " *Bereiche/Identity/IdentityHostingStartup.cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a0701-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a0701-211">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="a0701-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="a0701-212">Gerüst der Identität in ein MVC-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="a0701-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="a0701-213">Optional: Fügen Sie der Datei`_LoginPartial` *views/Shared/_Layout. cshtml* den Anmelde Namen partiell () hinzu:</span><span class="sxs-lookup"><span data-stu-id="a0701-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="a0701-214">Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml.*</span><span class="sxs-lookup"><span data-stu-id="a0701-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="a0701-215">wird in " *Bereiche/Identity/IdentityHostingStartup.cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a0701-215"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="a0701-216">Weitere Informationen finden Sie unter ihostingstartup.</span><span class="sxs-lookup"><span data-stu-id="a0701-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="a0701-217">[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach `UseStaticFiles`folgenden Aktionen abrufen:</span><span class="sxs-lookup"><span data-stu-id="a0701-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="a0701-218">Gerüst der Identität in ein MVC-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="a0701-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="a0701-219">Löschen Sie die *Seiten/* den freigegebenen Ordner und die Dateien in diesem Ordner.</span><span class="sxs-lookup"><span data-stu-id="a0701-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="a0701-220">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="a0701-220">Create full identity UI source</span></span>

<span data-ttu-id="a0701-221">Um die vollständige Kontrolle über Identity die Benutzeroberfläche zu Identity behalten, führen Sie das Gerüst aus, und wählen Sie **alle Dateien überschreiben**</span><span class="sxs-lookup"><span data-stu-id="a0701-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="a0701-222">Der folgende markierte Code zeigt die Änderungen zum Ersetzen der Standard Identity Benutzeroberfläche Identity durch in einer ASP.net Core 2,1-Web-App.</span><span class="sxs-lookup"><span data-stu-id="a0701-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="a0701-223">Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle Identity über die Benutzeroberfläche zu haben.</span><span class="sxs-lookup"><span data-stu-id="a0701-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="a0701-224">Der Standard Identity Wert wird im folgenden Code ersetzt:</span><span class="sxs-lookup"><span data-stu-id="a0701-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="a0701-225">Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:</span><span class="sxs-lookup"><span data-stu-id="a0701-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="a0701-226">Registrieren Sie `IEmailSender` eine Implementierung, z. b.:</span><span class="sxs-lookup"><span data-stu-id="a0701-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="a0701-227">Registerseite deaktivieren</span><span class="sxs-lookup"><span data-stu-id="a0701-227">Disable register page</span></span>

<span data-ttu-id="a0701-228">So deaktivieren Sie die Benutzerregistrierung:</span><span class="sxs-lookup"><span data-stu-id="a0701-228">To disable user registration:</span></span>

* <span data-ttu-id="a0701-229">Gerüstbau Identity.</span><span class="sxs-lookup"><span data-stu-id="a0701-229">Scaffold Identity.</span></span> <span data-ttu-id="a0701-230">Schließen Sie Account. Register, Account. Login und Account. registerconfirmation ein.</span><span class="sxs-lookup"><span data-stu-id="a0701-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="a0701-231">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a0701-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="a0701-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so können sich Benutzer nicht bei diesem Endpunkt registrieren:</span><span class="sxs-lookup"><span data-stu-id="a0701-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="a0701-233">Aktualisieren Sie *BereicheIdentity//Pages/Account/Register.cshtml* , damit Sie mit den vorangehenden Änderungen konsistent sind:</span><span class="sxs-lookup"><span data-stu-id="a0701-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="a0701-234">Kommentieren Sie den Registrierungs Link aus *Bereichen/Identity/Pages/Account/Login.cshtml* aus, oder entfernen Sie ihn.</span><span class="sxs-lookup"><span data-stu-id="a0701-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="a0701-235">Aktualisieren Sie die Seite " *Bereiche/Identity/Pages/Account/RegisterConfirmation* ".</span><span class="sxs-lookup"><span data-stu-id="a0701-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="a0701-236">Entfernen Sie den Code und die Verknüpfungen aus der cshtml-Datei.</span><span class="sxs-lookup"><span data-stu-id="a0701-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="a0701-237">Entfernen Sie den Bestätigungscode aus `PageModel`dem:</span><span class="sxs-lookup"><span data-stu-id="a0701-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="a0701-238">Verwenden einer anderen APP zum Hinzufügen von Benutzern</span><span class="sxs-lookup"><span data-stu-id="a0701-238">Use another app to add users</span></span>

<span data-ttu-id="a0701-239">Stellen Sie einen Mechanismus zum Hinzufügen von Benutzern außerhalb der Web-App bereit.</span><span class="sxs-lookup"><span data-stu-id="a0701-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="a0701-240">Zu den Optionen zum Hinzufügen von Benutzern gehören:</span><span class="sxs-lookup"><span data-stu-id="a0701-240">Options to add users include:</span></span>

* <span data-ttu-id="a0701-241">Eine dedizierte admin-Web-App.</span><span class="sxs-lookup"><span data-stu-id="a0701-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="a0701-242">Eine Konsolen-app.</span><span class="sxs-lookup"><span data-stu-id="a0701-242">A console app.</span></span>

<span data-ttu-id="a0701-243">Der folgende Code beschreibt einen Ansatz zum Hinzufügen von Benutzern:</span><span class="sxs-lookup"><span data-stu-id="a0701-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="a0701-244">Eine Liste von Benutzern wird in den Arbeitsspeicher eingelesen.</span><span class="sxs-lookup"><span data-stu-id="a0701-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="a0701-245">Für jeden Benutzer wird ein sicheres Kennwort generiert.</span><span class="sxs-lookup"><span data-stu-id="a0701-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="a0701-246">Der Benutzer wird der Identity -Datenbank hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="a0701-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="a0701-247">Der Benutzer wird benachrichtigt und aufgefordert, das Kennwort zu ändern.</span><span class="sxs-lookup"><span data-stu-id="a0701-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="a0701-248">Der folgende Code zeigt, wie Sie einen Benutzer hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="a0701-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="a0701-249">Ein ähnlicher Ansatz kann in Produktionsszenarien befolgt werden.</span><span class="sxs-lookup"><span data-stu-id="a0701-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a0701-250">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a0701-250">Additional resources</span></span>

* [<span data-ttu-id="a0701-251">Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher</span><span class="sxs-lookup"><span data-stu-id="a0701-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end