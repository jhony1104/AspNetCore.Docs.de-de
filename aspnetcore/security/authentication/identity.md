---
title: Einführung in die Identität auf ASP.net Core
author: rick-anderson
description: Verwenden Sie die Identität mit einer ASP.net Core-app. Erfahren Sie, wie Sie Kenn Wort Anforderungen ("Requirements Digit", "Requirements dlength", "Requirements duniquechars" usw.) festlegen.
ms.author: riande
ms.date: 10/15/2019
uid: security/authentication/identity
ms.openlocfilehash: 8da13ca5f74a9c829eb8137d33af0684ff88266d
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333547"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="603e2-104">Einführung in die Identität auf ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="603e2-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="603e2-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="603e2-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="603e2-106">ASP.net Core Identität ist ein Mitgliedschaftssystem, das die Benutzeroberflächen-Anmelde Funktionalität unterstützt.</span><span class="sxs-lookup"><span data-stu-id="603e2-106">ASP.NET Core Identity is a membership system that supports user interface (UI) login functionality.</span></span> <span data-ttu-id="603e2-107">Benutzer können ein Konto mit den in der Identität gespeicherten Anmelde Informationen erstellen, oder Sie können einen externen Anmelde Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="603e2-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="603e2-108">Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="603e2-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="603e2-109">Die Identität kann mit einer SQL Server Datenbank konfiguriert werden, um Benutzernamen, Kenn Wörter und Profildaten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="603e2-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="603e2-110">Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="603e2-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="603e2-111">In diesem Thema erfahren Sie, wie Sie mit der Identität einen Benutzer registrieren, anmelden und abmelden.</span><span class="sxs-lookup"><span data-stu-id="603e2-111">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="603e2-112">Ausführlichere Anweisungen zum Erstellen von apps, die die Identität verwenden, finden Sie im Abschnitt nächste Schritte am Ende dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="603e2-112">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="603e2-113">[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="603e2-113">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="603e2-114">Erstellen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="603e2-114">Create a Web app with authentication</span></span>

<span data-ttu-id="603e2-115">Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="603e2-115">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="603e2-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="603e2-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="603e2-117">Wählen Sie **Datei** > **Neues** >- **Projekt**aus.</span><span class="sxs-lookup"><span data-stu-id="603e2-117">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="603e2-118">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="603e2-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="603e2-119">Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="603e2-119">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="603e2-120">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="603e2-120">Click **OK**.</span></span>
* <span data-ttu-id="603e2-121">Wählen Sie eine ASP.net Core **Webanwendung**, und wählen Sie dann **Authentifizierung ändern**aus.</span><span class="sxs-lookup"><span data-stu-id="603e2-121">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="603e2-122">Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="603e2-122">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="603e2-123">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="603e2-123">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="603e2-124">Der vorherige Befehl erstellt eine Razor-Web-App mithilfe von SQLite.</span><span class="sxs-lookup"><span data-stu-id="603e2-124">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="603e2-125">Führen Sie den folgenden Befehl aus, um die Web-App mit localdb zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="603e2-125">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="603e2-126">Das generierte Projekt stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="603e2-126">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="603e2-127">Die Razor-Klassenbibliothek der Identität macht Endpunkte mit dem Bereich `Identity` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="603e2-127">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="603e2-128">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="603e2-128">For example:</span></span>

* <span data-ttu-id="603e2-129">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="603e2-129">/Identity/Account/Login</span></span>
* <span data-ttu-id="603e2-130">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="603e2-130">/Identity/Account/Logout</span></span>
* <span data-ttu-id="603e2-131">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="603e2-131">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="603e2-132">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="603e2-132">Apply migrations</span></span>

<span data-ttu-id="603e2-133">Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="603e2-133">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="603e2-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="603e2-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="603e2-135">Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="603e2-135">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="603e2-136">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="603e2-136">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="603e2-137">In diesem Schritt sind keine Migrationen erforderlich, wenn SQLite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="603e2-137">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="603e2-138">Führen Sie für localdb den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="603e2-138">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="603e2-139">Test Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="603e2-139">Test Register and Login</span></span>

<span data-ttu-id="603e2-140">Führen Sie die APP aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="603e2-140">Run the app and register a user.</span></span> <span data-ttu-id="603e2-141">Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="603e2-141">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="603e2-142">Konfigurieren von Identitäts Diensten</span><span class="sxs-lookup"><span data-stu-id="603e2-142">Configure Identity services</span></span>

<span data-ttu-id="603e2-143">Dienste werden in `ConfigureServices` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="603e2-143">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="603e2-144">Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="603e2-144">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="603e2-145">Der vorangehende markierte Code konfiguriert die Identität mit Standard Options Werten.</span><span class="sxs-lookup"><span data-stu-id="603e2-145">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="603e2-146">Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="603e2-146">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="603e2-147">Die Identität wird durch Aufrufen von "<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>" aktiviert.</span><span class="sxs-lookup"><span data-stu-id="603e2-147">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="603e2-148">`UseAuthentication` fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.</span><span class="sxs-lookup"><span data-stu-id="603e2-148">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="603e2-149">Die von der Vorlage generierte App verwendet keine [Autorisierung](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="603e2-149">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="603e2-150">`app.UseAuthorization` ist enthalten, um sicherzustellen, dass Sie in der richtigen Reihenfolge hinzugefügt wird, wenn die APP eine Autorisierung hinzufügt</span><span class="sxs-lookup"><span data-stu-id="603e2-150">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="603e2-151">`UseRouting`, `UseAuthentication`, `UseAuthorization` und `UseEndpoints` müssen in der im vorangehenden Code gezeigten Reihenfolge aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="603e2-151">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="603e2-152">Weitere Informationen zu `IdentityOptions` und `Startup` finden Sie unter <xref:Microsoft.AspNetCore.Identity.IdentityOptions> und [Anwendungsstart](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="603e2-152">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="603e2-153">Gerüst: registrieren, anmelden und Abmelden</span><span class="sxs-lookup"><span data-stu-id="603e2-153">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="603e2-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="603e2-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="603e2-155">Fügen Sie die Registrierungs-, Anmelde-und Abmelde Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="603e2-155">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="603e2-156">Befolgen Sie die [Gerüst Identität in einem Razor-Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="603e2-156">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="603e2-157">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="603e2-157">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="603e2-158">Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus.</span><span class="sxs-lookup"><span data-stu-id="603e2-158">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="603e2-159">Verwenden Sie andernfalls den korrekten Namespace für die `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="603e2-159">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="603e2-160">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="603e2-160">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="603e2-161">Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="603e2-161">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="603e2-162">Weitere Informationen zur Gerüstbau Identität finden Sie unter [Gerüst Identität in ein Razor-Projekt mit Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="603e2-162">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="603e2-163">Register überprüfen</span><span class="sxs-lookup"><span data-stu-id="603e2-163">Examine Register</span></span>

<span data-ttu-id="603e2-164">Wenn ein Benutzer auf den Link **registrieren** klickt, wird die `RegisterModel.OnPostAsync`-Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="603e2-164">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="603e2-165">Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für das Objekt "`_userManager`" erstellt.</span><span class="sxs-lookup"><span data-stu-id="603e2-165">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="603e2-166">`_userManager` wird durch Abhängigkeitsinjektion bereitgestellt):</span><span class="sxs-lookup"><span data-stu-id="603e2-166">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="603e2-167">Wenn der Benutzer erfolgreich erstellt wurde, wird der Benutzer durch den `_signInManager.SignInAsync`-aufgerufenen angemeldet.</span><span class="sxs-lookup"><span data-stu-id="603e2-167">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="603e2-168">Unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) finden Sie Schritte zum Verhindern der sofortigen Anmeldung bei der Registrierung.</span><span class="sxs-lookup"><span data-stu-id="603e2-168">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="603e2-169">Anmelden</span><span class="sxs-lookup"><span data-stu-id="603e2-169">Log in</span></span>

<span data-ttu-id="603e2-170">Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="603e2-170">The Login form is displayed when:</span></span>

* <span data-ttu-id="603e2-171">Der Link " **Anmelden** " ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="603e2-171">The **Log in** link is selected.</span></span>
* <span data-ttu-id="603e2-172">Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="603e2-172">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="603e2-173">Wenn das Formular auf der Anmeldeseite übermittelt wird, wird die Aktion "`OnPostAsync`" aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="603e2-173">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="603e2-174">`PasswordSignInAsync` wird für das `_signInManager`-Objekt aufgerufen (durch Abhängigkeitsinjektion bereitgestellt).</span><span class="sxs-lookup"><span data-stu-id="603e2-174">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="603e2-175">Die Basis `Controller`-Klasse macht eine Eigenschaft `User` verfügbar, auf die von Controller Methoden aus zugegriffen werden kann.</span><span class="sxs-lookup"><span data-stu-id="603e2-175">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="603e2-176">Beispielsweise können Sie `User.Claims` aufzählen und Autorisierungs Entscheidungen treffen.</span><span class="sxs-lookup"><span data-stu-id="603e2-176">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="603e2-177">Weitere Informationen finden Sie unter <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="603e2-177">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="603e2-178">Abmelden</span><span class="sxs-lookup"><span data-stu-id="603e2-178">Log out</span></span>

<span data-ttu-id="603e2-179">Der Link " **Abmelden** " Ruft die Aktion "`LogoutModel.OnPost`" auf.</span><span class="sxs-lookup"><span data-stu-id="603e2-179">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="603e2-180">Im vorangehenden Code muss der Code `return RedirectToPage();` eine Umleitung sein, damit der Browser eine neue Anforderung ausführt und die Identität des Benutzers aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="603e2-180">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="603e2-181">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="603e2-181">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="603e2-182">Post wird in den *Seiten "pages/Shared/_LoginPartial. cshtml*" angegeben:</span><span class="sxs-lookup"><span data-stu-id="603e2-182">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="603e2-183">Test Identität</span><span class="sxs-lookup"><span data-stu-id="603e2-183">Test Identity</span></span>

<span data-ttu-id="603e2-184">Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten.</span><span class="sxs-lookup"><span data-stu-id="603e2-184">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="603e2-185">Fügen Sie zum Testen der Identität [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)hinzu:</span><span class="sxs-lookup"><span data-stu-id="603e2-185">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="603e2-186">Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link.</span><span class="sxs-lookup"><span data-stu-id="603e2-186">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="603e2-187">Sie werden zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="603e2-187">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="603e2-188">Identität erkunden</span><span class="sxs-lookup"><span data-stu-id="603e2-188">Explore Identity</span></span>

<span data-ttu-id="603e2-189">So untersuchen Sie die Identität ausführlicher:</span><span class="sxs-lookup"><span data-stu-id="603e2-189">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="603e2-190">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="603e2-190">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="603e2-191">Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.</span><span class="sxs-lookup"><span data-stu-id="603e2-191">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="603e2-192">Identitäts Komponenten</span><span class="sxs-lookup"><span data-stu-id="603e2-192">Identity Components</span></span>

<span data-ttu-id="603e2-193">Alle Identitäts abhängigen nuget-Pakete sind im [ASP.net Core freigegebenen Framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)enthalten.</span><span class="sxs-lookup"><span data-stu-id="603e2-193">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="603e2-194">Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="603e2-194">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="603e2-195">Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.net Core Identität und ist in `Microsoft.AspNetCore.Identity.EntityFrameworkCore` enthalten.</span><span class="sxs-lookup"><span data-stu-id="603e2-195">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="603e2-196">Migrieren zu ASP.net Core Identität</span><span class="sxs-lookup"><span data-stu-id="603e2-196">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="603e2-197">Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identitäts Speicher finden Sie unter [Migrieren von Authentifizierung und Identität](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="603e2-197">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="603e2-198">Festlegen der Kenn Wort Stärke</span><span class="sxs-lookup"><span data-stu-id="603e2-198">Setting password strength</span></span>

<span data-ttu-id="603e2-199">Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .</span><span class="sxs-lookup"><span data-stu-id="603e2-199">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="603e2-200">Adddefaultidentity und addidentity</span><span class="sxs-lookup"><span data-stu-id="603e2-200">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="603e2-201"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> wurde in ASP.net Core 2,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="603e2-201"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="603e2-202">Das Aufrufen von `AddDefaultIdentity` ähnelt dem Aufrufen der folgenden:</span><span class="sxs-lookup"><span data-stu-id="603e2-202">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="603e2-203">Weitere Informationen finden Sie unter [adddefaultidentity-Quelle](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="603e2-203">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="next-steps"></a><span data-ttu-id="603e2-204">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="603e2-204">Next Steps</span></span>

* [<span data-ttu-id="603e2-205">Konfigurieren von Identity</span><span class="sxs-lookup"><span data-stu-id="603e2-205">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="603e2-206">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="603e2-206">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="603e2-207">ASP.net Core Identität ist ein Mitgliedschaftssystem, das ASP.net Core-apps Anmelde Funktionalität hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="603e2-207">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="603e2-208">Benutzer können ein Konto mit den in der Identität gespeicherten Anmelde Informationen erstellen, oder Sie können einen externen Anmelde Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="603e2-208">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="603e2-209">Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="603e2-209">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="603e2-210">Die Identität kann mit einer SQL Server Datenbank konfiguriert werden, um Benutzernamen, Kenn Wörter und Profildaten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="603e2-210">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="603e2-211">Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="603e2-211">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="603e2-212">[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="603e2-212">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="603e2-213">In diesem Thema erfahren Sie, wie Sie mit der Identität einen Benutzer registrieren, anmelden und abmelden.</span><span class="sxs-lookup"><span data-stu-id="603e2-213">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="603e2-214">Ausführlichere Anweisungen zum Erstellen von apps, die die Identität verwenden, finden Sie im Abschnitt nächste Schritte am Ende dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="603e2-214">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="603e2-215">Adddefaultidentity und addidentity</span><span class="sxs-lookup"><span data-stu-id="603e2-215">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="603e2-216"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> wurde in ASP.net Core 2,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="603e2-216"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="603e2-217">Das Aufrufen von `AddDefaultIdentity` ähnelt dem Aufrufen der folgenden:</span><span class="sxs-lookup"><span data-stu-id="603e2-217">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="603e2-218">Weitere Informationen finden Sie unter [adddefaultidentity-Quelle](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="603e2-218">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="603e2-219">Erstellen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="603e2-219">Create a Web app with authentication</span></span>

<span data-ttu-id="603e2-220">Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="603e2-220">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="603e2-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="603e2-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="603e2-222">Wählen Sie **Datei** > **Neues** >- **Projekt**aus.</span><span class="sxs-lookup"><span data-stu-id="603e2-222">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="603e2-223">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="603e2-223">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="603e2-224">Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="603e2-224">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="603e2-225">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="603e2-225">Click **OK**.</span></span>
* <span data-ttu-id="603e2-226">Wählen Sie eine ASP.net Core **Webanwendung**, und wählen Sie dann **Authentifizierung ändern**aus.</span><span class="sxs-lookup"><span data-stu-id="603e2-226">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="603e2-227">Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="603e2-227">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="603e2-228">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="603e2-228">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="603e2-229">Das generierte Projekt stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="603e2-229">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="603e2-230">Die Razor-Klassenbibliothek der Identität macht Endpunkte mit dem Bereich `Identity` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="603e2-230">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="603e2-231">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="603e2-231">For example:</span></span>

* <span data-ttu-id="603e2-232">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="603e2-232">/Identity/Account/Login</span></span>
* <span data-ttu-id="603e2-233">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="603e2-233">/Identity/Account/Logout</span></span>
* <span data-ttu-id="603e2-234">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="603e2-234">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="603e2-235">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="603e2-235">Apply migrations</span></span>

<span data-ttu-id="603e2-236">Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="603e2-236">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="603e2-237">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="603e2-237">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="603e2-238">Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="603e2-238">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="603e2-239">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="603e2-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="603e2-240">Test Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="603e2-240">Test Register and Login</span></span>

<span data-ttu-id="603e2-241">Führen Sie die APP aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="603e2-241">Run the app and register a user.</span></span> <span data-ttu-id="603e2-242">Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="603e2-242">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="603e2-243">Konfigurieren von Identitäts Diensten</span><span class="sxs-lookup"><span data-stu-id="603e2-243">Configure Identity services</span></span>

<span data-ttu-id="603e2-244">Dienste werden in `ConfigureServices` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="603e2-244">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="603e2-245">Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="603e2-245">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="603e2-246">Der vorangehende Code konfiguriert die Identität mit Standard Options Werten.</span><span class="sxs-lookup"><span data-stu-id="603e2-246">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="603e2-247">Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="603e2-247">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="603e2-248">Die Identität wird durch Aufrufen von " [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)" aktiviert.</span><span class="sxs-lookup"><span data-stu-id="603e2-248">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="603e2-249">`UseAuthentication` fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.</span><span class="sxs-lookup"><span data-stu-id="603e2-249">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="603e2-250">Weitere Informationen finden Sie unter [identityoptions-Klasse](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) und [Anwendungsstart](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="603e2-250">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="603e2-251">Gerüst: registrieren, anmelden und Abmelden</span><span class="sxs-lookup"><span data-stu-id="603e2-251">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="603e2-252">Befolgen Sie die [Gerüst Identität in einem Razor-Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="603e2-252">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="603e2-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="603e2-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="603e2-254">Fügen Sie die Registrierungs-, Anmelde-und Abmelde Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="603e2-254">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="603e2-255">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="603e2-255">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="603e2-256">Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus.</span><span class="sxs-lookup"><span data-stu-id="603e2-256">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="603e2-257">Verwenden Sie andernfalls den korrekten Namespace für die `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="603e2-257">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="603e2-258">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="603e2-258">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="603e2-259">Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="603e2-259">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="603e2-260">Register überprüfen</span><span class="sxs-lookup"><span data-stu-id="603e2-260">Examine Register</span></span>

<span data-ttu-id="603e2-261">Wenn ein Benutzer auf den Link **registrieren** klickt, wird die `RegisterModel.OnPostAsync`-Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="603e2-261">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="603e2-262">Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für das Objekt "`_userManager`" erstellt.</span><span class="sxs-lookup"><span data-stu-id="603e2-262">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="603e2-263">`_userManager` wird durch Abhängigkeitsinjektion bereitgestellt):</span><span class="sxs-lookup"><span data-stu-id="603e2-263">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="603e2-264">Wenn der Benutzer erfolgreich erstellt wurde, wird der Benutzer durch den `_signInManager.SignInAsync`-aufgerufenen angemeldet.</span><span class="sxs-lookup"><span data-stu-id="603e2-264">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="603e2-265">**Hinweis:** Unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) finden Sie Schritte zum Verhindern der sofortigen Anmeldung bei der Registrierung.</span><span class="sxs-lookup"><span data-stu-id="603e2-265">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="603e2-266">Anmelden</span><span class="sxs-lookup"><span data-stu-id="603e2-266">Log in</span></span>

<span data-ttu-id="603e2-267">Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="603e2-267">The Login form is displayed when:</span></span>

* <span data-ttu-id="603e2-268">Der Link " **Anmelden** " ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="603e2-268">The **Log in** link is selected.</span></span>
* <span data-ttu-id="603e2-269">Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="603e2-269">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="603e2-270">Wenn das Formular auf der Anmeldeseite übermittelt wird, wird die Aktion "`OnPostAsync`" aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="603e2-270">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="603e2-271">`PasswordSignInAsync` wird für das `_signInManager`-Objekt aufgerufen (durch Abhängigkeitsinjektion bereitgestellt).</span><span class="sxs-lookup"><span data-stu-id="603e2-271">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="603e2-272">Die Basis `Controller`-Klasse macht eine Eigenschaft `User` verfügbar, auf die Sie über Controller Methoden zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="603e2-272">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="603e2-273">Beispielsweise können Sie `User.Claims` aufzählen und Autorisierungs Entscheidungen treffen.</span><span class="sxs-lookup"><span data-stu-id="603e2-273">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="603e2-274">Weitere Informationen finden Sie unter <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="603e2-274">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="603e2-275">Abmelden</span><span class="sxs-lookup"><span data-stu-id="603e2-275">Log out</span></span>

<span data-ttu-id="603e2-276">Der Link " **Abmelden** " Ruft die Aktion "`LogoutModel.OnPost`" auf.</span><span class="sxs-lookup"><span data-stu-id="603e2-276">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="603e2-277">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="603e2-277">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="603e2-278">Post wird in den *Seiten "pages/Shared/_LoginPartial. cshtml*" angegeben:</span><span class="sxs-lookup"><span data-stu-id="603e2-278">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="603e2-279">Test Identität</span><span class="sxs-lookup"><span data-stu-id="603e2-279">Test Identity</span></span>

<span data-ttu-id="603e2-280">Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten.</span><span class="sxs-lookup"><span data-stu-id="603e2-280">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="603e2-281">Fügen Sie der Seite "Datenschutz" [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) hinzu, um die Identität zu testen.</span><span class="sxs-lookup"><span data-stu-id="603e2-281">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="603e2-282">Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link.</span><span class="sxs-lookup"><span data-stu-id="603e2-282">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="603e2-283">Sie werden zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="603e2-283">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="603e2-284">Identität erkunden</span><span class="sxs-lookup"><span data-stu-id="603e2-284">Explore Identity</span></span>

<span data-ttu-id="603e2-285">So untersuchen Sie die Identität ausführlicher:</span><span class="sxs-lookup"><span data-stu-id="603e2-285">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="603e2-286">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="603e2-286">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="603e2-287">Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.</span><span class="sxs-lookup"><span data-stu-id="603e2-287">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="603e2-288">Identitäts Komponenten</span><span class="sxs-lookup"><span data-stu-id="603e2-288">Identity Components</span></span>

<span data-ttu-id="603e2-289">Alle Identitäts abhängigen nuget-Pakete sind im [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)enthalten.</span><span class="sxs-lookup"><span data-stu-id="603e2-289">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="603e2-290">Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="603e2-290">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="603e2-291">Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.net Core Identität und ist in `Microsoft.AspNetCore.Identity.EntityFrameworkCore` enthalten.</span><span class="sxs-lookup"><span data-stu-id="603e2-291">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="603e2-292">Migrieren zu ASP.net Core Identität</span><span class="sxs-lookup"><span data-stu-id="603e2-292">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="603e2-293">Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identitäts Speicher finden Sie unter [Migrieren von Authentifizierung und Identität](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="603e2-293">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="603e2-294">Festlegen der Kenn Wort Stärke</span><span class="sxs-lookup"><span data-stu-id="603e2-294">Setting password strength</span></span>

<span data-ttu-id="603e2-295">Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .</span><span class="sxs-lookup"><span data-stu-id="603e2-295">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="603e2-296">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="603e2-296">Next Steps</span></span>

* [<span data-ttu-id="603e2-297">Konfigurieren von Identity</span><span class="sxs-lookup"><span data-stu-id="603e2-297">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
