---
title: Einführung in die Identität auf ASP.net Core
author: rick-anderson
description: Verwenden Sie die Identität mit einer ASP.net Core-app. Erfahren Sie, wie Sie Kenn Wort Anforderungen ("Requirements Digit", "Requirements dlength", "Requirements duniquechars" usw.) festlegen.
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 4bc5f206b3aee7c2d34055703acc5b6c5218f964
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205942"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="5725a-104">Einführung in die Identität auf ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="5725a-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5725a-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5725a-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5725a-106">ASP.net Core Identität:</span><span class="sxs-lookup"><span data-stu-id="5725a-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="5725a-107">Ist eine API, die die Benutzeroberflächen-Anmelde Funktionalität unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5725a-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="5725a-108">Von werden Benutzer, Kenn Wörter, Profildaten, Rollen, Ansprüche, Token, e-Mail-Bestätigung und mehr verwaltet.</span><span class="sxs-lookup"><span data-stu-id="5725a-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="5725a-109">Benutzer können ein Konto mit den in der Identität gespeicherten Anmelde Informationen erstellen, oder Sie können einen externen Anmelde Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="5725a-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="5725a-110">Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="5725a-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="5725a-111">Der Quellcode der [identitätsquelle](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) ist auf GitHub verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5725a-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="5725a-112">[Gerüstbau der Identität](xref:security/authentication/scaffold-identity) und Anzeigen der generierten Dateien zum Überprüfen der Vorlagen Interaktion mit der Identität.</span><span class="sxs-lookup"><span data-stu-id="5725a-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="5725a-113">Die Identität wird in der Regel mit einer SQL Server Datenbank konfiguriert, um Benutzernamen, Kenn Wörter und Profildaten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="5725a-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="5725a-114">Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="5725a-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="5725a-115">In diesem Thema erfahren Sie, wie Sie mit der Identität einen Benutzer registrieren, anmelden und abmelden.</span><span class="sxs-lookup"><span data-stu-id="5725a-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="5725a-116">Hinweis: die Vorlagen behandeln Benutzername und e-Mail-Adresse für Benutzer als identisch.</span><span class="sxs-lookup"><span data-stu-id="5725a-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="5725a-117">Ausführlichere Anweisungen zum Erstellen von apps, die die Identität verwenden, finden Sie im Abschnitt nächste Schritte am Ende dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="5725a-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="5725a-118">Die [Microsoft Identity-Plattform](/azure/active-directory/develop/) ist:</span><span class="sxs-lookup"><span data-stu-id="5725a-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="5725a-119">Eine Weiterentwicklung der Azure Active Directory (Azure AD)-Entwicklerplattform.</span><span class="sxs-lookup"><span data-stu-id="5725a-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="5725a-120">Nicht mit ASP.net Core Identität verknüpft.</span><span class="sxs-lookup"><span data-stu-id="5725a-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="5725a-121">[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5725a-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="5725a-122">Erstellen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5725a-122">Create a Web app with authentication</span></span>

<span data-ttu-id="5725a-123">Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="5725a-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5725a-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5725a-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5725a-125">Wählen Sie **Datei** > **neu** > **Projekt**aus.</span><span class="sxs-lookup"><span data-stu-id="5725a-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="5725a-126">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="5725a-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="5725a-127">Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="5725a-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="5725a-128">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="5725a-128">Click **OK**.</span></span>
* <span data-ttu-id="5725a-129">Wählen Sie eine ASP.net Core **Webanwendung**, und wählen Sie dann **Authentifizierung ändern**aus.</span><span class="sxs-lookup"><span data-stu-id="5725a-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="5725a-130">Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="5725a-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5725a-131">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="5725a-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="5725a-132">Der vorherige Befehl erstellt eine Razor-Web-App mithilfe von SQLite.</span><span class="sxs-lookup"><span data-stu-id="5725a-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="5725a-133">Führen Sie den folgenden Befehl aus, um die Web-App mit localdb zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="5725a-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="5725a-134">Das generierte Projekt stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="5725a-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5725a-135">Die Razor-Klassenbibliothek für die Identität macht Endpunkte mit dem `Identity` Bereich verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5725a-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="5725a-136">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5725a-136">For example:</span></span>

* <span data-ttu-id="5725a-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="5725a-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="5725a-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="5725a-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="5725a-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="5725a-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="5725a-140">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="5725a-140">Apply migrations</span></span>

<span data-ttu-id="5725a-141">Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="5725a-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5725a-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5725a-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5725a-143">Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="5725a-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="5725a-144">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="5725a-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5725a-145">In diesem Schritt sind keine Migrationen erforderlich, wenn SQLite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5725a-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="5725a-146">Führen Sie für localdb den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="5725a-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="5725a-147">Test Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="5725a-147">Test Register and Login</span></span>

<span data-ttu-id="5725a-148">Führen Sie die APP aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="5725a-148">Run the app and register a user.</span></span> <span data-ttu-id="5725a-149">Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="5725a-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="5725a-150">Konfigurieren von Identitäts Diensten</span><span class="sxs-lookup"><span data-stu-id="5725a-150">Configure Identity services</span></span>

<span data-ttu-id="5725a-151">Dienste werden in `ConfigureServices`hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5725a-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="5725a-152">Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="5725a-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="5725a-153">Der vorangehende markierte Code konfiguriert die Identität mit Standard Options Werten.</span><span class="sxs-lookup"><span data-stu-id="5725a-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="5725a-154">Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="5725a-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5725a-155">Die Identität wird durch Aufrufen <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>von aktiviert.</span><span class="sxs-lookup"><span data-stu-id="5725a-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="5725a-156">`UseAuthentication`Fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.</span><span class="sxs-lookup"><span data-stu-id="5725a-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="5725a-157">Die von der Vorlage generierte App verwendet keine [Autorisierung](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="5725a-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="5725a-158">`app.UseAuthorization`ist enthalten, um sicherzustellen, dass Sie in der richtigen Reihenfolge hinzugefügt wird, wenn die APP eine Autorisierung</span><span class="sxs-lookup"><span data-stu-id="5725a-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="5725a-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`und `UseEndpoints` müssen in der Reihenfolge aufgerufen werden, die im vorangehenden Code angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="5725a-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="5725a-160">Weitere `IdentityOptions` Informationen zu `Startup`und finden <xref:Microsoft.AspNetCore.Identity.IdentityOptions> Sie unter und [Anwendungsstart](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="5725a-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="5725a-161">Gerüst: registrieren, anmelden und Abmelden</span><span class="sxs-lookup"><span data-stu-id="5725a-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5725a-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5725a-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5725a-163">Fügen Sie die Registrierungs-, Anmelde-und Abmelde Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="5725a-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="5725a-164">Befolgen Sie die [Gerüst Identität in einem Razor-Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="5725a-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5725a-165">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="5725a-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5725a-166">Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus.</span><span class="sxs-lookup"><span data-stu-id="5725a-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="5725a-167">Verwenden Sie andernfalls den korrekten Namespace für `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="5725a-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="5725a-168">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="5725a-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="5725a-169">Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="5725a-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="5725a-170">Weitere Informationen zur Gerüstbau Identität finden Sie unter [Gerüst Identität in ein Razor-Projekt mit Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="5725a-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="5725a-171">Register überprüfen</span><span class="sxs-lookup"><span data-stu-id="5725a-171">Examine Register</span></span>

<span data-ttu-id="5725a-172">Wenn ein Benutzer auf den Link **registrieren** klickt, `RegisterModel.OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="5725a-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="5725a-173">Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für `_userManager` das-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="5725a-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="5725a-174">`_userManager`wird durch Abhängigkeitsinjektion bereitgestellt):</span><span class="sxs-lookup"><span data-stu-id="5725a-174">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="5725a-175">Wenn der Benutzer erfolgreich erstellt wurde, wird der Benutzer durch den-Befehl angemeldet `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="5725a-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="5725a-176">Unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) finden Sie Schritte zum Verhindern der sofortigen Anmeldung bei der Registrierung.</span><span class="sxs-lookup"><span data-stu-id="5725a-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="5725a-177">Anmelden</span><span class="sxs-lookup"><span data-stu-id="5725a-177">Log in</span></span>

<span data-ttu-id="5725a-178">Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="5725a-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="5725a-179">Der Link " **Anmelden** " ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="5725a-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="5725a-180">Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="5725a-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="5725a-181">Wenn das Formular auf der Anmeldeseite übermittelt wird, `OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="5725a-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="5725a-182">`PasswordSignInAsync`wird für das `_signInManager` -Objekt aufgerufen (durch Abhängigkeitsinjektion bereitgestellt).</span><span class="sxs-lookup"><span data-stu-id="5725a-182">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="5725a-183">Die- `Controller` Basisklasse macht `User` eine-Eigenschaft verfügbar, auf die von Controller Methoden aus zugegriffen werden kann.</span><span class="sxs-lookup"><span data-stu-id="5725a-183">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="5725a-184">Beispielsweise können Sie Autorisierungs Entscheidungen `User.Claims` auflisten und treffen.</span><span class="sxs-lookup"><span data-stu-id="5725a-184">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="5725a-185">Weitere Informationen finden Sie unter <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="5725a-185">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="5725a-186">Abmelden</span><span class="sxs-lookup"><span data-stu-id="5725a-186">Log out</span></span>

<span data-ttu-id="5725a-187">Der Link " **Abmelden** " ruft `LogoutModel.OnPost` die Aktion auf.</span><span class="sxs-lookup"><span data-stu-id="5725a-187">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="5725a-188">Im vorangehenden Code muss der Code `return RedirectToPage();` eine Umleitung sein, damit der Browser eine neue Anforderung ausführt und die Identität des Benutzers aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="5725a-188">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="5725a-189">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="5725a-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="5725a-190">Post wird in der Datei *pages/Shared/_LoginPartial. cshtml*angegeben:</span><span class="sxs-lookup"><span data-stu-id="5725a-190">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="5725a-191">Test Identität</span><span class="sxs-lookup"><span data-stu-id="5725a-191">Test Identity</span></span>

<span data-ttu-id="5725a-192">Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten.</span><span class="sxs-lookup"><span data-stu-id="5725a-192">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="5725a-193">Fügen Sie zum Testen der [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Identität Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="5725a-193">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="5725a-194">Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link.</span><span class="sxs-lookup"><span data-stu-id="5725a-194">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="5725a-195">Sie werden zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="5725a-195">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="5725a-196">Identität erkunden</span><span class="sxs-lookup"><span data-stu-id="5725a-196">Explore Identity</span></span>

<span data-ttu-id="5725a-197">So untersuchen Sie die Identität ausführlicher:</span><span class="sxs-lookup"><span data-stu-id="5725a-197">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="5725a-198">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="5725a-198">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="5725a-199">Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.</span><span class="sxs-lookup"><span data-stu-id="5725a-199">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="5725a-200">Identitäts Komponenten</span><span class="sxs-lookup"><span data-stu-id="5725a-200">Identity Components</span></span>

<span data-ttu-id="5725a-201">Alle Identitäts abhängigen nuget-Pakete sind im [ASP.net Core freigegebenen Framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)enthalten.</span><span class="sxs-lookup"><span data-stu-id="5725a-201">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="5725a-202">Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="5725a-202">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="5725a-203">Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.net Core Identität und ist in enthalten `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="5725a-203">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="5725a-204">Migrieren zu ASP.net Core Identität</span><span class="sxs-lookup"><span data-stu-id="5725a-204">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="5725a-205">Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identitäts Speicher finden Sie unter [Migrieren von Authentifizierung und Identität](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="5725a-205">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="5725a-206">Festlegen der Kenn Wort Stärke</span><span class="sxs-lookup"><span data-stu-id="5725a-206">Setting password strength</span></span>

<span data-ttu-id="5725a-207">Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .</span><span class="sxs-lookup"><span data-stu-id="5725a-207">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="5725a-208">Adddefaultidentity und addidentity</span><span class="sxs-lookup"><span data-stu-id="5725a-208">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="5725a-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wurde in ASP.net Core 2,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="5725a-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="5725a-210">Das `AddDefaultIdentity` Aufrufen von ähnelt dem Aufrufen der folgenden:</span><span class="sxs-lookup"><span data-stu-id="5725a-210">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="5725a-211">Weitere Informationen finden Sie unter [adddefaultidentity-Quelle](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="5725a-211">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="5725a-212">Veröffentlichen statischer identitätsassets verhindern</span><span class="sxs-lookup"><span data-stu-id="5725a-212">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="5725a-213">Fügen Sie die folgende `ResolveStaticWebAssetsInputsDependsOn` Eigenschaft und `RemoveIdentityAssets` das Ziel der Projektdatei der APP hinzu, um zu verhindern, dass statische Identitäts Ressourcen (Stylesheets und JavaScript-Dateien für die Identitäts Benutzeroberfläche) im Webstamm Verzeichnis veröffentlicht werden:</span><span class="sxs-lookup"><span data-stu-id="5725a-213">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="5725a-214">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="5725a-214">Next Steps</span></span>

* <span data-ttu-id="5725a-215">Weitere Informationen zum Konfigurieren der Identität mithilfe von SQLite finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="5725a-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="5725a-216">Konfigurieren von Identity</span><span class="sxs-lookup"><span data-stu-id="5725a-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5725a-217">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5725a-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5725a-218">ASP.net Core Identität ist ein Mitgliedschaftssystem, das ASP.net Core-apps Anmelde Funktionalität hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="5725a-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="5725a-219">Benutzer können ein Konto mit den in der Identität gespeicherten Anmelde Informationen erstellen, oder Sie können einen externen Anmelde Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="5725a-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="5725a-220">Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="5725a-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="5725a-221">Die Identität kann mit einer SQL Server Datenbank konfiguriert werden, um Benutzernamen, Kenn Wörter und Profildaten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="5725a-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="5725a-222">Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="5725a-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="5725a-223">[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5725a-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="5725a-224">In diesem Thema erfahren Sie, wie Sie mit der Identität einen Benutzer registrieren, anmelden und abmelden.</span><span class="sxs-lookup"><span data-stu-id="5725a-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="5725a-225">Ausführlichere Anweisungen zum Erstellen von apps, die die Identität verwenden, finden Sie im Abschnitt nächste Schritte am Ende dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="5725a-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="5725a-226">Adddefaultidentity und addidentity</span><span class="sxs-lookup"><span data-stu-id="5725a-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="5725a-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wurde in ASP.net Core 2,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="5725a-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="5725a-228">Das `AddDefaultIdentity` Aufrufen von ähnelt dem Aufrufen der folgenden:</span><span class="sxs-lookup"><span data-stu-id="5725a-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="5725a-229">Weitere Informationen finden Sie unter [adddefaultidentity-Quelle](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="5725a-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="5725a-230">Erstellen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5725a-230">Create a Web app with authentication</span></span>

<span data-ttu-id="5725a-231">Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="5725a-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5725a-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5725a-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5725a-233">Wählen Sie **Datei** > **neu** > **Projekt**aus.</span><span class="sxs-lookup"><span data-stu-id="5725a-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="5725a-234">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="5725a-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="5725a-235">Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="5725a-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="5725a-236">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="5725a-236">Click **OK**.</span></span>
* <span data-ttu-id="5725a-237">Wählen Sie eine ASP.net Core **Webanwendung**, und wählen Sie dann **Authentifizierung ändern**aus.</span><span class="sxs-lookup"><span data-stu-id="5725a-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="5725a-238">Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="5725a-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5725a-239">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="5725a-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="5725a-240">Das generierte Projekt stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="5725a-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5725a-241">Die Razor-Klassenbibliothek für die Identität macht Endpunkte mit dem `Identity` Bereich verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5725a-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="5725a-242">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5725a-242">For example:</span></span>

* <span data-ttu-id="5725a-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="5725a-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="5725a-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="5725a-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="5725a-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="5725a-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="5725a-246">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="5725a-246">Apply migrations</span></span>

<span data-ttu-id="5725a-247">Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="5725a-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5725a-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5725a-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5725a-249">Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="5725a-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="5725a-250">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="5725a-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="5725a-251">Test Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="5725a-251">Test Register and Login</span></span>

<span data-ttu-id="5725a-252">Führen Sie die APP aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="5725a-252">Run the app and register a user.</span></span> <span data-ttu-id="5725a-253">Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="5725a-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="5725a-254">Konfigurieren von Identitäts Diensten</span><span class="sxs-lookup"><span data-stu-id="5725a-254">Configure Identity services</span></span>

<span data-ttu-id="5725a-255">Dienste werden in `ConfigureServices`hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5725a-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="5725a-256">Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="5725a-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="5725a-257">Der vorangehende Code konfiguriert die Identität mit Standard Options Werten.</span><span class="sxs-lookup"><span data-stu-id="5725a-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="5725a-258">Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="5725a-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5725a-259">Die Identität wird durch Aufrufen von " [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)" aktiviert.</span><span class="sxs-lookup"><span data-stu-id="5725a-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="5725a-260">`UseAuthentication`Fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.</span><span class="sxs-lookup"><span data-stu-id="5725a-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="5725a-261">Weitere Informationen finden Sie unter [identityoptions-Klasse](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) und [Anwendungsstart](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="5725a-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="5725a-262">Gerüst: registrieren, anmelden und Abmelden</span><span class="sxs-lookup"><span data-stu-id="5725a-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="5725a-263">Befolgen Sie die [Gerüst Identität in einem Razor-Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="5725a-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5725a-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5725a-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5725a-265">Fügen Sie die Registrierungs-, Anmelde-und Abmelde Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="5725a-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5725a-266">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="5725a-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5725a-267">Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus.</span><span class="sxs-lookup"><span data-stu-id="5725a-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="5725a-268">Verwenden Sie andernfalls den korrekten Namespace für `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="5725a-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="5725a-269">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="5725a-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="5725a-270">Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="5725a-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="5725a-271">Register überprüfen</span><span class="sxs-lookup"><span data-stu-id="5725a-271">Examine Register</span></span>

<span data-ttu-id="5725a-272">Wenn ein Benutzer auf den Link **registrieren** klickt, `RegisterModel.OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="5725a-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="5725a-273">Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für `_userManager` das-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="5725a-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="5725a-274">`_userManager`wird durch Abhängigkeitsinjektion bereitgestellt):</span><span class="sxs-lookup"><span data-stu-id="5725a-274">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="5725a-275">Wenn der Benutzer erfolgreich erstellt wurde, wird der Benutzer durch den-Befehl angemeldet `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="5725a-275">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="5725a-276">**Hinweis:** Unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) finden Sie Schritte zum Verhindern der sofortigen Anmeldung bei der Registrierung.</span><span class="sxs-lookup"><span data-stu-id="5725a-276">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="5725a-277">Anmelden</span><span class="sxs-lookup"><span data-stu-id="5725a-277">Log in</span></span>

<span data-ttu-id="5725a-278">Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="5725a-278">The Login form is displayed when:</span></span>

* <span data-ttu-id="5725a-279">Der Link " **Anmelden** " ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="5725a-279">The **Log in** link is selected.</span></span>
* <span data-ttu-id="5725a-280">Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="5725a-280">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="5725a-281">Wenn das Formular auf der Anmeldeseite übermittelt wird, `OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="5725a-281">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="5725a-282">`PasswordSignInAsync`wird für das `_signInManager` -Objekt aufgerufen (durch Abhängigkeitsinjektion bereitgestellt).</span><span class="sxs-lookup"><span data-stu-id="5725a-282">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="5725a-283">Die- `Controller` Basisklasse macht `User` eine-Eigenschaft verfügbar, auf die Sie über Controller Methoden zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="5725a-283">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="5725a-284">Beispielsweise können Sie Autorisierungs Entscheidungen `User.Claims` auflisten und treffen.</span><span class="sxs-lookup"><span data-stu-id="5725a-284">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="5725a-285">Weitere Informationen finden Sie unter <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="5725a-285">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="5725a-286">Abmelden</span><span class="sxs-lookup"><span data-stu-id="5725a-286">Log out</span></span>

<span data-ttu-id="5725a-287">Der Link " **Abmelden** " ruft `LogoutModel.OnPost` die Aktion auf.</span><span class="sxs-lookup"><span data-stu-id="5725a-287">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="5725a-288">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="5725a-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="5725a-289">Post wird in der Datei *pages/Shared/_LoginPartial. cshtml*angegeben:</span><span class="sxs-lookup"><span data-stu-id="5725a-289">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="5725a-290">Test Identität</span><span class="sxs-lookup"><span data-stu-id="5725a-290">Test Identity</span></span>

<span data-ttu-id="5725a-291">Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten.</span><span class="sxs-lookup"><span data-stu-id="5725a-291">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="5725a-292">Fügen Sie zum Testen der [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Identität der Seite Datenschutz hinzu.</span><span class="sxs-lookup"><span data-stu-id="5725a-292">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="5725a-293">Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link.</span><span class="sxs-lookup"><span data-stu-id="5725a-293">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="5725a-294">Sie werden zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="5725a-294">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="5725a-295">Identität erkunden</span><span class="sxs-lookup"><span data-stu-id="5725a-295">Explore Identity</span></span>

<span data-ttu-id="5725a-296">So untersuchen Sie die Identität ausführlicher:</span><span class="sxs-lookup"><span data-stu-id="5725a-296">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="5725a-297">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="5725a-297">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="5725a-298">Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.</span><span class="sxs-lookup"><span data-stu-id="5725a-298">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="5725a-299">Identitäts Komponenten</span><span class="sxs-lookup"><span data-stu-id="5725a-299">Identity Components</span></span>

<span data-ttu-id="5725a-300">Alle Identitäts abhängigen nuget-Pakete sind im [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)enthalten.</span><span class="sxs-lookup"><span data-stu-id="5725a-300">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5725a-301">Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="5725a-301">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="5725a-302">Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.net Core Identität und ist in enthalten `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="5725a-302">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="5725a-303">Migrieren zu ASP.net Core Identität</span><span class="sxs-lookup"><span data-stu-id="5725a-303">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="5725a-304">Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identitäts Speicher finden Sie unter [Migrieren von Authentifizierung und Identität](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="5725a-304">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="5725a-305">Festlegen der Kenn Wort Stärke</span><span class="sxs-lookup"><span data-stu-id="5725a-305">Setting password strength</span></span>

<span data-ttu-id="5725a-306">Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .</span><span class="sxs-lookup"><span data-stu-id="5725a-306">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5725a-307">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="5725a-307">Next Steps</span></span>

* <span data-ttu-id="5725a-308">Weitere Informationen zum Konfigurieren der Identität mithilfe von SQLite finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) .</span><span class="sxs-lookup"><span data-stu-id="5725a-308">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="5725a-309">Konfigurieren von Identity</span><span class="sxs-lookup"><span data-stu-id="5725a-309">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
