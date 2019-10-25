---
title: Gerüst Identität in ASP.net Core Projekten
author: rick-anderson
description: Erfahren Sie, wie Sie die Identität in ein ASP.net Core Projekt eingerüstbau.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ca2046563281efc3c1cd8f4fec73fe4f8d3fbdda
ms.sourcegitcommit: 383017d7060a6d58f6a79cf4d7335d5b4b6c5659
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72816105"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="f5f80-103">Gerüst Identität in ASP.net Core Projekten</span><span class="sxs-lookup"><span data-stu-id="f5f80-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="f5f80-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f5f80-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f5f80-105">ASP.net Core 2,1 und höher stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="f5f80-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f5f80-106">Anwendungen, die die Identität enthalten, können das Gerüst zum selektiven Hinzufügen des Quellcodes verwenden, der in der Identity Razor Class Library (RCL) enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="f5f80-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="f5f80-107">Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können.</span><span class="sxs-lookup"><span data-stu-id="f5f80-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="f5f80-108">Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="f5f80-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="f5f80-109">Generierter Code hat Vorrang vor dem gleichen Code in der Razor-Klassenbibliothek „Identität“.</span><span class="sxs-lookup"><span data-stu-id="f5f80-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="f5f80-110">Informationen zum vollständigen Zugriff auf die Benutzeroberfläche und zur Verwendung der Standard-RCL finden Sie im Abschnitt [Erstellen einer vollständigen identitätsquelle](#full)für die Identität.</span><span class="sxs-lookup"><span data-stu-id="f5f80-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="f5f80-111">Anwendungen, die **keine** -Authentifizierung einschließen, können das Gerüst zum Hinzufügen des RCL-Identitäts Pakets anwenden.</span><span class="sxs-lookup"><span data-stu-id="f5f80-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="f5f80-112">Sie können Code der Klassenbibliothek „Identität“ auswählen, der generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="f5f80-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="f5f80-113">Obwohl das Gerüst den größten Teil des notwendigen Codes generiert, müssen Sie das Projekt aktualisieren, um den Vorgang abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="f5f80-113">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="f5f80-114">In diesem Dokument werden die erforderlichen Schritte zum Durchführen eines identitätsgerüstbau Updates erläutert.</span><span class="sxs-lookup"><span data-stu-id="f5f80-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="f5f80-115">Wenn das Identitäts Gerüst ausgeführt wird, wird im Projektverzeichnis eine Datei mit *gerüdodingreadme. txt* erstellt.</span><span class="sxs-lookup"><span data-stu-id="f5f80-115">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="f5f80-116">Die Datei " *gerüstoldingreadme. txt* " enthält allgemeine Anweisungen dazu, was zum Durchführen der Aktualisierung des Identitäts Gerüsts erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="f5f80-116">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="f5f80-117">Dieses Dokument enthält ausführlichere Anweisungen als die Datei " *Gerüst-dingreadme. txt* ".</span><span class="sxs-lookup"><span data-stu-id="f5f80-117">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="f5f80-118">Wir empfehlen die Verwendung eines Quell Code Verwaltungssystems, das Datei Unterschiede anzeigt und es Ihnen ermöglicht, Änderungen zurückzusetzen.</span><span class="sxs-lookup"><span data-stu-id="f5f80-118">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="f5f80-119">Überprüfen Sie die Änderungen nach dem Ausführen des Identitäts gerüstems.</span><span class="sxs-lookup"><span data-stu-id="f5f80-119">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="f5f80-120">Dienste sind erforderlich, wenn die [zweistufige Authentifizierung](xref:security/authentication/identity-enable-qrcodes), [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)sowie andere Sicherheitsfeatures mit der Identität verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f5f80-120">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="f5f80-121">Dienste oder Service-stubzeichen werden nicht generiert, wenn eine Gerüst Identität besteht</span><span class="sxs-lookup"><span data-stu-id="f5f80-121">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="f5f80-122">Dienste zum Aktivieren dieser Features müssen manuell hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="f5f80-122">Services to enable these features must be added manually.</span></span> <span data-ttu-id="f5f80-123">Weitere Informationen finden Sie unter Anfordern einer [e-Mail-Bestätigung](xref:security/authentication/accconfirm#require-email-confirmation)</span><span class="sxs-lookup"><span data-stu-id="f5f80-123">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="f5f80-124">Gerüst der Identität in ein leeres Projekt</span><span class="sxs-lookup"><span data-stu-id="f5f80-124">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="f5f80-125">Fügen Sie die folgenden markierten Aufrufe der `Startup`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="f5f80-125">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="f5f80-126">Gerüst der Identität in ein Razor-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="f5f80-126">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="f5f80-127">Die Identität wird in " *Areas/Identity/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="f5f80-127">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="f5f80-128">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="f5f80-128">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="f5f80-129">Migrationen, UseAuthentication und Layout</span><span class="sxs-lookup"><span data-stu-id="f5f80-129">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="f5f80-130">Aktivieren der Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="f5f80-130">Enable authentication</span></span>

<span data-ttu-id="f5f80-131">Nennen Sie in der `Configure`-Methode der `Startup`-Klasse nach `UseStaticFiles`die [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) :</span><span class="sxs-lookup"><span data-stu-id="f5f80-131">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="f5f80-132">Layoutänderungen</span><span class="sxs-lookup"><span data-stu-id="f5f80-132">Layout changes</span></span>

<span data-ttu-id="f5f80-133">Optional: Fügen Sie die Anmelde-partielle (`_LoginPartial`) der Layoutdatei hinzu:</span><span class="sxs-lookup"><span data-stu-id="f5f80-133">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="f5f80-134">Gerüst der Identität in ein Razor-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="f5f80-134">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="f5f80-135">Einige Identitäts Optionen werden in " *Bereiche/Identitäten/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="f5f80-135">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="f5f80-136">Weitere Informationen finden Sie unter [ihostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="f5f80-136">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="f5f80-137">Gerüst der Identität in ein MVC-Projekt ohne vorhandene Autorisierung</span><span class="sxs-lookup"><span data-stu-id="f5f80-137">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="f5f80-138">Optional: Fügen Sie der Datei *views/Shared/_Layout. cshtml* den Anmelde Namen partiell (`_LoginPartial`) hinzu:</span><span class="sxs-lookup"><span data-stu-id="f5f80-138">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="f5f80-139">Verschieben Sie die Datei *pages/Shared/_LoginPartial. cshtml* in *views/Shared/_LoginPartial. cshtml* .</span><span class="sxs-lookup"><span data-stu-id="f5f80-139">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="f5f80-140">Die Identität wird in " *Areas/Identity/identityhostingstartup. cs*" konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="f5f80-140">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="f5f80-141">Weitere Informationen finden Sie unter ihostingstartup.</span><span class="sxs-lookup"><span data-stu-id="f5f80-141">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="f5f80-142">[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) nach `UseStaticFiles`abrufen:</span><span class="sxs-lookup"><span data-stu-id="f5f80-142">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="f5f80-143">Gerüst der Identität in ein MVC-Projekt mit Autorisierung</span><span class="sxs-lookup"><span data-stu-id="f5f80-143">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="f5f80-144">Löschen Sie die *Seiten/* den freigegebenen Ordner und die Dateien in diesem Ordner.</span><span class="sxs-lookup"><span data-stu-id="f5f80-144">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="f5f80-145">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="f5f80-145">Create full identity UI source</span></span>

<span data-ttu-id="f5f80-146">Um die vollständige Kontrolle über die Benutzeroberfläche der Identität aufrechtzuerhalten, führen Sie das Identitäts Gerüst aus, und wählen Sie **alle Dateien überschreiben**</span><span class="sxs-lookup"><span data-stu-id="f5f80-146">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="f5f80-147">Der folgende hervorgehobene Code zeigt die Änderungen, um die Standardbenutzer Oberfläche der Identität durch Identität in einer ASP.net Core 2,1-Web-App zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="f5f80-147">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="f5f80-148">Möglicherweise möchten Sie dies tun, um die vollständige Kontrolle über die Identitäts Benutzeroberfläche zu haben.</span><span class="sxs-lookup"><span data-stu-id="f5f80-148">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="f5f80-149">Die Standard Identität wird im folgenden Code ersetzt:</span><span class="sxs-lookup"><span data-stu-id="f5f80-149">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="f5f80-150">Der folgende Code legt die Werte für [loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)und [accessdeniedpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)fest:</span><span class="sxs-lookup"><span data-stu-id="f5f80-150">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="f5f80-151">Registrieren Sie eine `IEmailSender` Implementierung, z. b.:</span><span class="sxs-lookup"><span data-stu-id="f5f80-151">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="f5f80-152">Registerseite deaktivieren</span><span class="sxs-lookup"><span data-stu-id="f5f80-152">Disable register page</span></span>

<span data-ttu-id="f5f80-153">So deaktivieren Sie die Benutzerregistrierung:</span><span class="sxs-lookup"><span data-stu-id="f5f80-153">To disable user registration:</span></span>

* <span data-ttu-id="f5f80-154">Gerüst Identität.</span><span class="sxs-lookup"><span data-stu-id="f5f80-154">Scaffold Identity.</span></span> <span data-ttu-id="f5f80-155">Schließen Sie Account. Register, Account. Login und Account. registerconfirmation ein.</span><span class="sxs-lookup"><span data-stu-id="f5f80-155">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="f5f80-156">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f5f80-156">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="f5f80-157">Aktualisieren Sie *Areas/Identity/Pages/Account/Register. cshtml. cs* , damit Benutzer sich nicht bei diesem Endpunkt registrieren können:</span><span class="sxs-lookup"><span data-stu-id="f5f80-157">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="f5f80-158">Aktualisieren Sie *Areas/Identity/Pages/Account/Register. cshtml* , damit Sie mit den vorangehenden Änderungen konsistent sind:</span><span class="sxs-lookup"><span data-stu-id="f5f80-158">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="f5f80-159">Kommentieren Sie den Registrierungs Link aus *Bereichen/Identität/Seiten/Konto/Login. cshtml* aus, oder entfernen Sie ihn.</span><span class="sxs-lookup"><span data-stu-id="f5f80-159">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="f5f80-160">Aktualisieren Sie die Seite *Bereiche/Identität/Seiten/Konto/Register Bestätigung* .</span><span class="sxs-lookup"><span data-stu-id="f5f80-160">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="f5f80-161">Entfernen Sie den Code und die Verknüpfungen aus der cshtml-Datei.</span><span class="sxs-lookup"><span data-stu-id="f5f80-161">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="f5f80-162">Entfernen Sie den Bestätigungscode aus dem `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="f5f80-162">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="f5f80-163">Verwenden einer anderen APP zum Hinzufügen von Benutzern</span><span class="sxs-lookup"><span data-stu-id="f5f80-163">Use another app to add users</span></span>

<span data-ttu-id="f5f80-164">Stellen Sie einen Mechanismus zum Hinzufügen von Benutzern außerhalb der Web-App bereit.</span><span class="sxs-lookup"><span data-stu-id="f5f80-164">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="f5f80-165">Zu den Optionen zum Hinzufügen von Benutzern gehören:</span><span class="sxs-lookup"><span data-stu-id="f5f80-165">Options to add users include:</span></span>

* <span data-ttu-id="f5f80-166">Eine dedizierte admin-Web-App.</span><span class="sxs-lookup"><span data-stu-id="f5f80-166">A dedicated admin web app.</span></span>
* <span data-ttu-id="f5f80-167">Eine Konsolen-app.</span><span class="sxs-lookup"><span data-stu-id="f5f80-167">A console app.</span></span>

<span data-ttu-id="f5f80-168">Der folgende Code beschreibt einen Ansatz zum Hinzufügen von Benutzern:</span><span class="sxs-lookup"><span data-stu-id="f5f80-168">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="f5f80-169">Eine Liste von Benutzern wird in den Arbeitsspeicher eingelesen.</span><span class="sxs-lookup"><span data-stu-id="f5f80-169">A list of users is read into memory.</span></span>
* <span data-ttu-id="f5f80-170">Für jeden Benutzer wird ein sicheres Kennwort generiert.</span><span class="sxs-lookup"><span data-stu-id="f5f80-170">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="f5f80-171">Der Benutzer wird der Identitätsdatenbank hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f5f80-171">The user is added to the Identity database.</span></span>
* <span data-ttu-id="f5f80-172">Der Benutzer wird benachrichtigt und aufgefordert, das Kennwort zu ändern.</span><span class="sxs-lookup"><span data-stu-id="f5f80-172">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="f5f80-173">Der folgende Code zeigt, wie Sie einen Benutzer hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="f5f80-173">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="f5f80-174">Ein ähnlicher Ansatz kann in Produktionsszenarien befolgt werden.</span><span class="sxs-lookup"><span data-stu-id="f5f80-174">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f5f80-175">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f5f80-175">Additional resources</span></span>

* [<span data-ttu-id="f5f80-176">Änderungen am Authentifizierungscode in ASP.net Core 2,1 und höher</span><span class="sxs-lookup"><span data-stu-id="f5f80-176">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)
