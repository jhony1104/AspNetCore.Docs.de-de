---
title: Einführung in die Identität auf ASP.net Core
author: rick-anderson
description: Verwenden Sie die Identität mit einer ASP.net Core-app. Erfahren Sie, wie Sie Kenn Wort Anforderungen ("Requirements Digit", "Requirements dlength", "Requirements duniquechars" usw.) festlegen.
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 98fee261a741a20eed181ca5b9a4ebb693deeb63
ms.sourcegitcommit: cbd30479f42cbb3385000ef834d9c7d021fd218d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76146510"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="6bc86-104">Einführung in die Identität auf ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="6bc86-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6bc86-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6bc86-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6bc86-106">ASP.net Core Identität:</span><span class="sxs-lookup"><span data-stu-id="6bc86-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="6bc86-107">Ist eine API, die die Benutzeroberflächen-Anmelde Funktionalität unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="6bc86-108">Von werden Benutzer, Kenn Wörter, Profildaten, Rollen, Ansprüche, Token, e-Mail-Bestätigung und mehr verwaltet.</span><span class="sxs-lookup"><span data-stu-id="6bc86-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="6bc86-109">Benutzer können ein Konto mit den in der Identität gespeicherten Anmelde Informationen erstellen, oder Sie können einen externen Anmelde Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="6bc86-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="6bc86-110">Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="6bc86-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="6bc86-111">Der Quellcode der [identitätsquelle](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) ist auf GitHub verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6bc86-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="6bc86-112">[Gerüstbau der Identität](xref:security/authentication/scaffold-identity) und Anzeigen der generierten Dateien zum Überprüfen der Vorlagen Interaktion mit der Identität.</span><span class="sxs-lookup"><span data-stu-id="6bc86-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="6bc86-113">Die Identität wird in der Regel mit einer SQL Server Datenbank konfiguriert, um Benutzernamen, Kenn Wörter und Profildaten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="6bc86-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="6bc86-114">Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="6bc86-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="6bc86-115">In diesem Thema erfahren Sie, wie Sie mit der Identität einen Benutzer registrieren, anmelden und abmelden.</span><span class="sxs-lookup"><span data-stu-id="6bc86-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="6bc86-116">Ausführlichere Anweisungen zum Erstellen von apps, die die Identität verwenden, finden Sie im Abschnitt nächste Schritte am Ende dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="6bc86-116">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="6bc86-117">Die [Microsoft Identity-Plattform](/azure/active-directory/develop/) ist:</span><span class="sxs-lookup"><span data-stu-id="6bc86-117">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="6bc86-118">Eine Weiterentwicklung der Azure Active Directory (Azure AD)-Entwicklerplattform.</span><span class="sxs-lookup"><span data-stu-id="6bc86-118">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="6bc86-119">Nicht mit ASP.net Core Identität verknüpft.</span><span class="sxs-lookup"><span data-stu-id="6bc86-119">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="6bc86-120">[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="6bc86-120">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="6bc86-121">Erstellen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="6bc86-121">Create a Web app with authentication</span></span>

<span data-ttu-id="6bc86-122">Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="6bc86-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6bc86-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6bc86-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6bc86-124">Wählen Sie **Datei** > **neue** > **Projekt**aus.</span><span class="sxs-lookup"><span data-stu-id="6bc86-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="6bc86-125">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="6bc86-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="6bc86-126">Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="6bc86-127">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="6bc86-127">Click **OK**.</span></span>
* <span data-ttu-id="6bc86-128">Wählen Sie eine ASP.net Core **Webanwendung**, und wählen Sie dann **Authentifizierung ändern**aus.</span><span class="sxs-lookup"><span data-stu-id="6bc86-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="6bc86-129">Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="6bc86-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6bc86-130">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="6bc86-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="6bc86-131">Der vorherige Befehl erstellt eine Razor-Web-App mithilfe von SQLite.</span><span class="sxs-lookup"><span data-stu-id="6bc86-131">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="6bc86-132">Führen Sie den folgenden Befehl aus, um die Web-App mit localdb zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="6bc86-132">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="6bc86-133">Das generierte Projekt stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="6bc86-133">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="6bc86-134">Die Razor-Klassenbibliothek der Identität macht Endpunkte mit dem `Identity` Bereich verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6bc86-134">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="6bc86-135">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6bc86-135">For example:</span></span>

* <span data-ttu-id="6bc86-136">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="6bc86-136">/Identity/Account/Login</span></span>
* <span data-ttu-id="6bc86-137">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="6bc86-137">/Identity/Account/Logout</span></span>
* <span data-ttu-id="6bc86-138">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="6bc86-138">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="6bc86-139">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="6bc86-139">Apply migrations</span></span>

<span data-ttu-id="6bc86-140">Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="6bc86-140">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6bc86-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6bc86-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6bc86-142">Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="6bc86-142">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6bc86-143">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="6bc86-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6bc86-144">In diesem Schritt sind keine Migrationen erforderlich, wenn SQLite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="6bc86-144">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="6bc86-145">Führen Sie für localdb den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="6bc86-145">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="6bc86-146">Test Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="6bc86-146">Test Register and Login</span></span>

<span data-ttu-id="6bc86-147">Führen Sie die APP aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="6bc86-147">Run the app and register a user.</span></span> <span data-ttu-id="6bc86-148">Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-148">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="6bc86-149">Konfigurieren von Identitäts Diensten</span><span class="sxs-lookup"><span data-stu-id="6bc86-149">Configure Identity services</span></span>

<span data-ttu-id="6bc86-150">In `ConfigureServices`werden Dienste hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-150">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="6bc86-151">Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-151">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="6bc86-152">Der vorangehende markierte Code konfiguriert die Identität mit Standard Options Werten.</span><span class="sxs-lookup"><span data-stu-id="6bc86-152">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="6bc86-153">Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-153">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="6bc86-154">Die Identität wird durch Aufrufen von <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>aktiviert.</span><span class="sxs-lookup"><span data-stu-id="6bc86-154">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="6bc86-155">`UseAuthentication` fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.</span><span class="sxs-lookup"><span data-stu-id="6bc86-155">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="6bc86-156">Die von der Vorlage generierte App verwendet keine [Autorisierung](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="6bc86-156">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="6bc86-157">`app.UseAuthorization` ist enthalten, um sicherzustellen, dass Sie in der richtigen Reihenfolge hinzugefügt wird, wenn die APP eine Autorisierung</span><span class="sxs-lookup"><span data-stu-id="6bc86-157">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="6bc86-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`und `UseEndpoints` müssen in der Reihenfolge aufgerufen werden, die im vorangehenden Code angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="6bc86-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="6bc86-159">Weitere Informationen zu `IdentityOptions` und `Startup`finden Sie unter <xref:Microsoft.AspNetCore.Identity.IdentityOptions> und [Anwendungsstart](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="6bc86-159">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="6bc86-160">Gerüst: registrieren, anmelden und Abmelden</span><span class="sxs-lookup"><span data-stu-id="6bc86-160">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6bc86-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6bc86-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6bc86-162">Fügen Sie die Registrierungs-, Anmelde-und Abmelde Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="6bc86-162">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="6bc86-163">Befolgen Sie die [Gerüst Identität in einem Razor-Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="6bc86-163">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6bc86-164">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="6bc86-164">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6bc86-165">Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus.</span><span class="sxs-lookup"><span data-stu-id="6bc86-165">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="6bc86-166">Verwenden Sie andernfalls den korrekten Namespace für die `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="6bc86-166">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="6bc86-167">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-167">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="6bc86-168">Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-168">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="6bc86-169">Weitere Informationen zur Gerüstbau Identität finden Sie unter [Gerüst Identität in ein Razor-Projekt mit Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="6bc86-169">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="6bc86-170">Register überprüfen</span><span class="sxs-lookup"><span data-stu-id="6bc86-170">Examine Register</span></span>

<span data-ttu-id="6bc86-171">Wenn ein Benutzer auf den Link **registrieren** klickt, wird die `RegisterModel.OnPostAsync` Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-171">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="6bc86-172">Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für das Objekt "`_userManager`" erstellt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-172">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="6bc86-173">`_userManager` wird durch Abhängigkeitsinjektion bereitgestellt):</span><span class="sxs-lookup"><span data-stu-id="6bc86-173">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="6bc86-174">Wenn der Benutzer erfolgreich erstellt wurde, wird der Benutzer durch den `_signInManager.SignInAsync`aufgerufenen angemeldet.</span><span class="sxs-lookup"><span data-stu-id="6bc86-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="6bc86-175">Unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) finden Sie Schritte zum Verhindern der sofortigen Anmeldung bei der Registrierung.</span><span class="sxs-lookup"><span data-stu-id="6bc86-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="6bc86-176">Anmelden</span><span class="sxs-lookup"><span data-stu-id="6bc86-176">Log in</span></span>

<span data-ttu-id="6bc86-177">Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="6bc86-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="6bc86-178">Der Link " **Anmelden** " ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="6bc86-179">Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="6bc86-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="6bc86-180">Wenn das Formular auf der Anmeldeseite übermittelt wird, wird die `OnPostAsync` Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="6bc86-181">`PasswordSignInAsync` wird für das `_signInManager` Objekt aufgerufen (durch Abhängigkeitsinjektion bereitgestellt).</span><span class="sxs-lookup"><span data-stu-id="6bc86-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="6bc86-182">Die Basis `Controller` Klasse macht eine `User` Eigenschaft verfügbar, auf die von Controller Methoden aus zugegriffen werden kann.</span><span class="sxs-lookup"><span data-stu-id="6bc86-182">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="6bc86-183">Beispielsweise können Sie `User.Claims` aufzählen und Autorisierungs Entscheidungen treffen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="6bc86-184">Weitere Informationen finden Sie unter <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="6bc86-184">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="6bc86-185">Abmelden</span><span class="sxs-lookup"><span data-stu-id="6bc86-185">Log out</span></span>

<span data-ttu-id="6bc86-186">Der Link " **Abmelden** " Ruft die `LogoutModel.OnPost` Aktion auf.</span><span class="sxs-lookup"><span data-stu-id="6bc86-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="6bc86-187">Im vorangehenden Code muss der Code `return RedirectToPage();` eine Umleitung sein, damit der Browser eine neue Anforderung ausführt und die Identität des Benutzers aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="6bc86-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="6bc86-188">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="6bc86-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="6bc86-189">Post wird in der Datei *pages/Shared/_LoginPartial. cshtml*angegeben:</span><span class="sxs-lookup"><span data-stu-id="6bc86-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="6bc86-190">Test Identität</span><span class="sxs-lookup"><span data-stu-id="6bc86-190">Test Identity</span></span>

<span data-ttu-id="6bc86-191">Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten.</span><span class="sxs-lookup"><span data-stu-id="6bc86-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="6bc86-192">Fügen Sie zum Testen der Identität [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)hinzu:</span><span class="sxs-lookup"><span data-stu-id="6bc86-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="6bc86-193">Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link.</span><span class="sxs-lookup"><span data-stu-id="6bc86-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="6bc86-194">Sie werden zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="6bc86-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="6bc86-195">Identität erkunden</span><span class="sxs-lookup"><span data-stu-id="6bc86-195">Explore Identity</span></span>

<span data-ttu-id="6bc86-196">So untersuchen Sie die Identität ausführlicher:</span><span class="sxs-lookup"><span data-stu-id="6bc86-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="6bc86-197">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="6bc86-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="6bc86-198">Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.</span><span class="sxs-lookup"><span data-stu-id="6bc86-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="6bc86-199">Identitäts Komponenten</span><span class="sxs-lookup"><span data-stu-id="6bc86-199">Identity Components</span></span>

<span data-ttu-id="6bc86-200">Alle Identitäts abhängigen nuget-Pakete sind im [ASP.net Core freigegebenen Framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)enthalten.</span><span class="sxs-lookup"><span data-stu-id="6bc86-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="6bc86-201">Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="6bc86-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="6bc86-202">Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.net Core Identität und ist in `Microsoft.AspNetCore.Identity.EntityFrameworkCore`enthalten.</span><span class="sxs-lookup"><span data-stu-id="6bc86-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="6bc86-203">Migrieren zu ASP.net Core Identität</span><span class="sxs-lookup"><span data-stu-id="6bc86-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="6bc86-204">Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identitäts Speicher finden Sie unter [Migrieren von Authentifizierung und Identität](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="6bc86-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="6bc86-205">Festlegen der Kenn Wort Stärke</span><span class="sxs-lookup"><span data-stu-id="6bc86-205">Setting password strength</span></span>

<span data-ttu-id="6bc86-206">Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .</span><span class="sxs-lookup"><span data-stu-id="6bc86-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="6bc86-207">Adddefaultidentity und addidentity</span><span class="sxs-lookup"><span data-stu-id="6bc86-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="6bc86-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> wurde in ASP.net Core 2,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="6bc86-209">Das Aufrufen von `AddDefaultIdentity` ähnelt dem Aufrufen der folgenden:</span><span class="sxs-lookup"><span data-stu-id="6bc86-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="6bc86-210">Weitere Informationen finden Sie unter [adddefaultidentity-Quelle](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="6bc86-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="6bc86-211">Veröffentlichen statischer identitätsassets verhindern</span><span class="sxs-lookup"><span data-stu-id="6bc86-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="6bc86-212">Um zu verhindern, dass statische Identitäts Ressourcen (Stylesheets und JavaScript-Dateien für die Identitäts Benutzeroberfläche) im Webstamm Verzeichnis veröffentlicht werden, fügen Sie der Projektdatei der APP die folgende `ResolveStaticWebAssetsInputsDependsOn`-Eigenschaft und `RemoveIdentityAssets` Ziel hinzu:</span><span class="sxs-lookup"><span data-stu-id="6bc86-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a><span data-ttu-id="6bc86-213">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="6bc86-213">Next Steps</span></span>

* <span data-ttu-id="6bc86-214">Weitere Informationen zum Konfigurieren der Identität mithilfe von SQLite finden Sie in [diesem GitHub-Problem](https://github.com/aspnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="6bc86-214">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="6bc86-215">Konfigurieren von Identity</span><span class="sxs-lookup"><span data-stu-id="6bc86-215">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6bc86-216">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6bc86-216">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6bc86-217">ASP.net Core Identität ist ein Mitgliedschaftssystem, das ASP.net Core-apps Anmelde Funktionalität hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-217">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="6bc86-218">Benutzer können ein Konto mit den in der Identität gespeicherten Anmelde Informationen erstellen, oder Sie können einen externen Anmelde Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="6bc86-218">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="6bc86-219">Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="6bc86-219">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="6bc86-220">Die Identität kann mit einer SQL Server Datenbank konfiguriert werden, um Benutzernamen, Kenn Wörter und Profildaten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="6bc86-220">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="6bc86-221">Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="6bc86-221">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="6bc86-222">[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="6bc86-222">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="6bc86-223">In diesem Thema erfahren Sie, wie Sie mit der Identität einen Benutzer registrieren, anmelden und abmelden.</span><span class="sxs-lookup"><span data-stu-id="6bc86-223">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="6bc86-224">Ausführlichere Anweisungen zum Erstellen von apps, die die Identität verwenden, finden Sie im Abschnitt nächste Schritte am Ende dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="6bc86-224">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="6bc86-225">Adddefaultidentity und addidentity</span><span class="sxs-lookup"><span data-stu-id="6bc86-225">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="6bc86-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> wurde in ASP.net Core 2,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="6bc86-227">Das Aufrufen von `AddDefaultIdentity` ähnelt dem Aufrufen der folgenden:</span><span class="sxs-lookup"><span data-stu-id="6bc86-227">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="6bc86-228">Weitere Informationen finden Sie unter [adddefaultidentity-Quelle](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="6bc86-228">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="6bc86-229">Erstellen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="6bc86-229">Create a Web app with authentication</span></span>

<span data-ttu-id="6bc86-230">Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="6bc86-230">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6bc86-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6bc86-231">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="6bc86-232">Wählen Sie **Datei** > **neue** > **Projekt**aus.</span><span class="sxs-lookup"><span data-stu-id="6bc86-232">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="6bc86-233">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="6bc86-233">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="6bc86-234">Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-234">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="6bc86-235">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="6bc86-235">Click **OK**.</span></span>
* <span data-ttu-id="6bc86-236">Wählen Sie eine ASP.net Core **Webanwendung**, und wählen Sie dann **Authentifizierung ändern**aus.</span><span class="sxs-lookup"><span data-stu-id="6bc86-236">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="6bc86-237">Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="6bc86-237">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6bc86-238">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="6bc86-238">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="6bc86-239">Das generierte Projekt stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="6bc86-239">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="6bc86-240">Die Razor-Klassenbibliothek der Identität macht Endpunkte mit dem `Identity` Bereich verfügbar.</span><span class="sxs-lookup"><span data-stu-id="6bc86-240">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="6bc86-241">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6bc86-241">For example:</span></span>

* <span data-ttu-id="6bc86-242">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="6bc86-242">/Identity/Account/Login</span></span>
* <span data-ttu-id="6bc86-243">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="6bc86-243">/Identity/Account/Logout</span></span>
* <span data-ttu-id="6bc86-244">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="6bc86-244">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="6bc86-245">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="6bc86-245">Apply migrations</span></span>

<span data-ttu-id="6bc86-246">Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="6bc86-246">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6bc86-247">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6bc86-247">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6bc86-248">Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="6bc86-248">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6bc86-249">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="6bc86-249">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="6bc86-250">Test Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="6bc86-250">Test Register and Login</span></span>

<span data-ttu-id="6bc86-251">Führen Sie die APP aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="6bc86-251">Run the app and register a user.</span></span> <span data-ttu-id="6bc86-252">Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-252">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="6bc86-253">Konfigurieren von Identitäts Diensten</span><span class="sxs-lookup"><span data-stu-id="6bc86-253">Configure Identity services</span></span>

<span data-ttu-id="6bc86-254">In `ConfigureServices`werden Dienste hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-254">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="6bc86-255">Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-255">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="6bc86-256">Der vorangehende Code konfiguriert die Identität mit Standard Options Werten.</span><span class="sxs-lookup"><span data-stu-id="6bc86-256">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="6bc86-257">Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-257">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="6bc86-258">Die Identität wird durch Aufrufen von " [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)" aktiviert.</span><span class="sxs-lookup"><span data-stu-id="6bc86-258">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="6bc86-259">`UseAuthentication` fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.</span><span class="sxs-lookup"><span data-stu-id="6bc86-259">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="6bc86-260">Weitere Informationen finden Sie unter [identityoptions-Klasse](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) und [Anwendungsstart](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="6bc86-260">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="6bc86-261">Gerüst: registrieren, anmelden und Abmelden</span><span class="sxs-lookup"><span data-stu-id="6bc86-261">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="6bc86-262">Befolgen Sie die [Gerüst Identität in einem Razor-Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="6bc86-262">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="6bc86-263">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6bc86-263">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6bc86-264">Fügen Sie die Registrierungs-, Anmelde-und Abmelde Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="6bc86-264">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="6bc86-265">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="6bc86-265">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6bc86-266">Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus.</span><span class="sxs-lookup"><span data-stu-id="6bc86-266">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="6bc86-267">Verwenden Sie andernfalls den korrekten Namespace für die `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="6bc86-267">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="6bc86-268">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-268">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="6bc86-269">Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-269">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="6bc86-270">Register überprüfen</span><span class="sxs-lookup"><span data-stu-id="6bc86-270">Examine Register</span></span>

<span data-ttu-id="6bc86-271">Wenn ein Benutzer auf den Link **registrieren** klickt, wird die `RegisterModel.OnPostAsync` Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-271">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="6bc86-272">Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für das Objekt "`_userManager`" erstellt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-272">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="6bc86-273">`_userManager` wird durch Abhängigkeitsinjektion bereitgestellt):</span><span class="sxs-lookup"><span data-stu-id="6bc86-273">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="6bc86-274">Wenn der Benutzer erfolgreich erstellt wurde, wird der Benutzer durch den `_signInManager.SignInAsync`aufgerufenen angemeldet.</span><span class="sxs-lookup"><span data-stu-id="6bc86-274">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="6bc86-275">**Hinweis:** Unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) finden Sie Schritte zum Verhindern der sofortigen Anmeldung bei der Registrierung.</span><span class="sxs-lookup"><span data-stu-id="6bc86-275">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="6bc86-276">Anmelden</span><span class="sxs-lookup"><span data-stu-id="6bc86-276">Log in</span></span>

<span data-ttu-id="6bc86-277">Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="6bc86-277">The Login form is displayed when:</span></span>

* <span data-ttu-id="6bc86-278">Der Link " **Anmelden** " ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="6bc86-278">The **Log in** link is selected.</span></span>
* <span data-ttu-id="6bc86-279">Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="6bc86-279">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="6bc86-280">Wenn das Formular auf der Anmeldeseite übermittelt wird, wird die `OnPostAsync` Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-280">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="6bc86-281">`PasswordSignInAsync` wird für das `_signInManager` Objekt aufgerufen (durch Abhängigkeitsinjektion bereitgestellt).</span><span class="sxs-lookup"><span data-stu-id="6bc86-281">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="6bc86-282">Die Basis `Controller` Klasse macht eine `User` Eigenschaft verfügbar, auf die Sie über Controller Methoden zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="6bc86-282">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="6bc86-283">Beispielsweise können Sie `User.Claims` aufzählen und Autorisierungs Entscheidungen treffen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-283">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="6bc86-284">Weitere Informationen finden Sie unter <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="6bc86-284">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="6bc86-285">Abmelden</span><span class="sxs-lookup"><span data-stu-id="6bc86-285">Log out</span></span>

<span data-ttu-id="6bc86-286">Der Link " **Abmelden** " Ruft die `LogoutModel.OnPost` Aktion auf.</span><span class="sxs-lookup"><span data-stu-id="6bc86-286">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="6bc86-287">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="6bc86-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="6bc86-288">Post wird in der Datei *pages/Shared/_LoginPartial. cshtml*angegeben:</span><span class="sxs-lookup"><span data-stu-id="6bc86-288">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="6bc86-289">Test Identität</span><span class="sxs-lookup"><span data-stu-id="6bc86-289">Test Identity</span></span>

<span data-ttu-id="6bc86-290">Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten.</span><span class="sxs-lookup"><span data-stu-id="6bc86-290">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="6bc86-291">Fügen Sie der Datenschutzseite [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) hinzu, um die Identität zu testen.</span><span class="sxs-lookup"><span data-stu-id="6bc86-291">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="6bc86-292">Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link.</span><span class="sxs-lookup"><span data-stu-id="6bc86-292">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="6bc86-293">Sie werden zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="6bc86-293">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="6bc86-294">Identität erkunden</span><span class="sxs-lookup"><span data-stu-id="6bc86-294">Explore Identity</span></span>

<span data-ttu-id="6bc86-295">So untersuchen Sie die Identität ausführlicher:</span><span class="sxs-lookup"><span data-stu-id="6bc86-295">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="6bc86-296">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="6bc86-296">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="6bc86-297">Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.</span><span class="sxs-lookup"><span data-stu-id="6bc86-297">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="6bc86-298">Identitäts Komponenten</span><span class="sxs-lookup"><span data-stu-id="6bc86-298">Identity Components</span></span>

<span data-ttu-id="6bc86-299">Alle Identitäts abhängigen nuget-Pakete sind im [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)enthalten.</span><span class="sxs-lookup"><span data-stu-id="6bc86-299">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="6bc86-300">Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="6bc86-300">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="6bc86-301">Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.net Core Identität und ist in `Microsoft.AspNetCore.Identity.EntityFrameworkCore`enthalten.</span><span class="sxs-lookup"><span data-stu-id="6bc86-301">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="6bc86-302">Migrieren zu ASP.net Core Identität</span><span class="sxs-lookup"><span data-stu-id="6bc86-302">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="6bc86-303">Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identitäts Speicher finden Sie unter [Migrieren von Authentifizierung und Identität](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="6bc86-303">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="6bc86-304">Festlegen der Kenn Wort Stärke</span><span class="sxs-lookup"><span data-stu-id="6bc86-304">Setting password strength</span></span>

<span data-ttu-id="6bc86-305">Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .</span><span class="sxs-lookup"><span data-stu-id="6bc86-305">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6bc86-306">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="6bc86-306">Next Steps</span></span>

* <span data-ttu-id="6bc86-307">Weitere Informationen zum Konfigurieren der Identität mithilfe von SQLite finden Sie in [diesem GitHub-Problem](https://github.com/aspnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="6bc86-307">See [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="6bc86-308">Konfigurieren von Identity</span><span class="sxs-lookup"><span data-stu-id="6bc86-308">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
