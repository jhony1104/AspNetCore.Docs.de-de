---
title: Einführung in Identity ASP.net Core
author: rick-anderson
description: Verwenden Sie dies Identity mit einer ASP.net Core-app. Erfahren Sie, wie Sie Kenn Wort Anforderungen ("Requirements Digit", "Requirements dlength", "Requirements duniquechars" usw.) festlegen.
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 97cbc5f68d20c06e92a56ead85c8377d4e8ef582
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404586"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="cb62d-104">Einführung in Identity ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="cb62d-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cb62d-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cb62d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cb62d-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="cb62d-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="cb62d-107">Ist eine API, die die Benutzeroberflächen-Anmelde Funktionalität unterstützt.</span><span class="sxs-lookup"><span data-stu-id="cb62d-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="cb62d-108">Von werden Benutzer, Kenn Wörter, Profildaten, Rollen, Ansprüche, Token, e-Mail-Bestätigung und mehr verwaltet.</span><span class="sxs-lookup"><span data-stu-id="cb62d-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="cb62d-109">Benutzer können ein Konto mit den Anmelde Informationen erstellen, die in gespeichert Identity sind, oder Sie können einen externen Anmelde Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="cb62d-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="cb62d-110">Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cb62d-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="cb62d-111">Der [ Identity Quellcode](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) ist auf GitHub verfügbar.</span><span class="sxs-lookup"><span data-stu-id="cb62d-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="cb62d-112">[Gerüst Identity ](xref:security/authentication/scaffold-identity) und zeigen Sie die generierten Dateien an, um die Vorlagen Interaktion mit zu überprüfen Identity .</span><span class="sxs-lookup"><span data-stu-id="cb62d-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="cb62d-113">wird in der Regel mit einer SQL Server Datenbank konfiguriert, um Benutzernamen, Kenn Wörter und Profildaten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="cb62d-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="cb62d-114">Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="cb62d-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="cb62d-115">In diesem Thema erfahren Sie, wie Sie Identity einen Benutzer mit registrieren, anmelden und abmelden.</span><span class="sxs-lookup"><span data-stu-id="cb62d-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="cb62d-116">Hinweis: die Vorlagen behandeln Benutzername und e-Mail-Adresse für Benutzer als identisch.</span><span class="sxs-lookup"><span data-stu-id="cb62d-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="cb62d-117">Ausführlichere Anweisungen zum Erstellen von apps, die verwenden Identity , finden Sie im Abschnitt nächste Schritte am Ende dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="cb62d-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="cb62d-118">Die [Microsoft Identity-Plattform](/azure/active-directory/develop/) ist:</span><span class="sxs-lookup"><span data-stu-id="cb62d-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="cb62d-119">Eine Weiterentwicklung der Azure Active Directory (Azure AD)-Entwicklerplattform.</span><span class="sxs-lookup"><span data-stu-id="cb62d-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="cb62d-120">Nicht im Zusammenhang mit ASP.net Core Identity .</span><span class="sxs-lookup"><span data-stu-id="cb62d-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="cb62d-121">[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="cb62d-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="cb62d-122">Erstellen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="cb62d-122">Create a Web app with authentication</span></span>

<span data-ttu-id="cb62d-123">Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="cb62d-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb62d-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb62d-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cb62d-125">Wählen Sie **Datei** > **neu** > **Projekt**aus.</span><span class="sxs-lookup"><span data-stu-id="cb62d-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="cb62d-126">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="cb62d-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="cb62d-127">Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="cb62d-128">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="cb62d-128">Click **OK**.</span></span>
* <span data-ttu-id="cb62d-129">Wählen Sie eine ASP.net Core **Webanwendung**, und wählen Sie dann **Authentifizierung ändern**aus.</span><span class="sxs-lookup"><span data-stu-id="cb62d-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="cb62d-130">Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="cb62d-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cb62d-131">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="cb62d-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="cb62d-132">Der vorherige Befehl erstellt eine Razor Web-App mithilfe von SQLite.</span><span class="sxs-lookup"><span data-stu-id="cb62d-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="cb62d-133">Führen Sie den folgenden Befehl aus, um die Web-App mit localdb zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="cb62d-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="cb62d-134">Das generierte Projekt stellt [ASP.net Core Identity ](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="cb62d-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cb62d-135">Die Identity Razor Klassenbibliothek macht Endpunkte mit dem `Identity` Bereich verfügbar.</span><span class="sxs-lookup"><span data-stu-id="cb62d-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="cb62d-136">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cb62d-136">For example:</span></span>

* <span data-ttu-id="cb62d-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="cb62d-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="cb62d-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="cb62d-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="cb62d-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="cb62d-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="cb62d-140">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="cb62d-140">Apply migrations</span></span>

<span data-ttu-id="cb62d-141">Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="cb62d-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb62d-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb62d-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cb62d-143">Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="cb62d-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="cb62d-144">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="cb62d-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cb62d-145">In diesem Schritt sind keine Migrationen erforderlich, wenn SQLite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="cb62d-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="cb62d-146">Führen Sie für localdb den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="cb62d-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="cb62d-147">Test Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="cb62d-147">Test Register and Login</span></span>

<span data-ttu-id="cb62d-148">Führen Sie die APP aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="cb62d-148">Run the app and register a user.</span></span> <span data-ttu-id="cb62d-149">Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="cb62d-150">IdentityDienste konfigurieren</span><span class="sxs-lookup"><span data-stu-id="cb62d-150">Configure Identity services</span></span>

<span data-ttu-id="cb62d-151">Dienste werden in hinzugefügt `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb62d-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="cb62d-152">Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="cb62d-153">Der vorangehende markierte Code konfiguriert Identity mit Standard Options Werten.</span><span class="sxs-lookup"><span data-stu-id="cb62d-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="cb62d-154">Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="cb62d-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="cb62d-155">wird aktiviert, indem aufgerufen wird <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="cb62d-155"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="cb62d-156">`UseAuthentication`Fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.</span><span class="sxs-lookup"><span data-stu-id="cb62d-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="cb62d-157">Die von der Vorlage generierte App verwendet keine [Autorisierung](xref:security/authorization/secure-data).</span><span class="sxs-lookup"><span data-stu-id="cb62d-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="cb62d-158">`app.UseAuthorization`ist enthalten, um sicherzustellen, dass Sie in der richtigen Reihenfolge hinzugefügt wird, wenn die APP eine Autorisierung</span><span class="sxs-lookup"><span data-stu-id="cb62d-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="cb62d-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` und `UseEndpoints` müssen in der Reihenfolge aufgerufen werden, die im vorangehenden Code angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="cb62d-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="cb62d-160">Weitere Informationen zu `IdentityOptions` und `Startup` finden Sie unter <xref:Microsoft.AspNetCore.Identity.IdentityOptions> und [Anwendungsstart](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="cb62d-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="cb62d-161">Gerüst: registrieren, anmelden und Abmelden</span><span class="sxs-lookup"><span data-stu-id="cb62d-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb62d-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb62d-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cb62d-163">Fügen Sie die Registrierungs-, Anmelde-und Abmelde Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="cb62d-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="cb62d-164">Befolgen Sie die [Gerüst Identität in einem Razor Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cb62d-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cb62d-165">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="cb62d-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cb62d-166">Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus.</span><span class="sxs-lookup"><span data-stu-id="cb62d-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="cb62d-167">Verwenden Sie andernfalls den korrekten Namespace für `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="cb62d-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="cb62d-168">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="cb62d-169">Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="cb62d-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="cb62d-170">Weitere Informationen zum Gerüstbau finden Sie unter Identity [Gerüst Identität in einem Razor Projekt mit Autorisierung](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="cb62d-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="cb62d-171">Register überprüfen</span><span class="sxs-lookup"><span data-stu-id="cb62d-171">Examine Register</span></span>

<span data-ttu-id="cb62d-172">Wenn ein Benutzer auf den Link **registrieren** klickt, `RegisterModel.OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="cb62d-173">Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für das `_userManager` Objekt erstellt:</span><span class="sxs-lookup"><span data-stu-id="cb62d-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="cb62d-174">Wenn der Benutzer erfolgreich erstellt wurde, wird der Benutzer durch den-Befehl angemeldet `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="cb62d-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="cb62d-175">Unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) finden Sie Schritte zum Verhindern der sofortigen Anmeldung bei der Registrierung.</span><span class="sxs-lookup"><span data-stu-id="cb62d-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="cb62d-176">Anmelden</span><span class="sxs-lookup"><span data-stu-id="cb62d-176">Log in</span></span>

<span data-ttu-id="cb62d-177">Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="cb62d-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="cb62d-178">Der Link " **Anmelden** " ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="cb62d-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="cb62d-179">Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="cb62d-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="cb62d-180">Wenn das Formular auf der Anmeldeseite übermittelt wird, `OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="cb62d-181">`PasswordSignInAsync`wird für das- `_signInManager` Objekt aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-181">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="cb62d-182">Informationen dazu, wie Sie Autorisierungs Entscheidungen treffen, finden Sie unter <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="cb62d-182">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="cb62d-183">Abmelden</span><span class="sxs-lookup"><span data-stu-id="cb62d-183">Log out</span></span>

<span data-ttu-id="cb62d-184">Der Link " **Abmelden** " Ruft die `LogoutModel.OnPost` Aktion auf.</span><span class="sxs-lookup"><span data-stu-id="cb62d-184">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="cb62d-185">Im vorangehenden Code muss der Code `return RedirectToPage();` eine Umleitung sein, damit der Browser eine neue Anforderung ausführt und die Identität des Benutzers aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="cb62d-185">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="cb62d-186">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="cb62d-186">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="cb62d-187">Post wird in der Datei *pages/Shared/_LoginPartial. cshtml*angegeben:</span><span class="sxs-lookup"><span data-stu-id="cb62d-187">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="cb62d-188">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="cb62d-188">Test Identity</span></span>

<span data-ttu-id="cb62d-189">Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten.</span><span class="sxs-lookup"><span data-stu-id="cb62d-189">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="cb62d-190">IdentityFügen Sie zum Testen Folgendes hinzu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="cb62d-190">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="cb62d-191">Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link.</span><span class="sxs-lookup"><span data-stu-id="cb62d-191">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="cb62d-192">Sie werden zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="cb62d-192">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="cb62d-193">EntdeckenIdentity</span><span class="sxs-lookup"><span data-stu-id="cb62d-193">Explore Identity</span></span>

<span data-ttu-id="cb62d-194">Weitere Informationen finden Sie unter Identity :</span><span class="sxs-lookup"><span data-stu-id="cb62d-194">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="cb62d-195">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="cb62d-195">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="cb62d-196">Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.</span><span class="sxs-lookup"><span data-stu-id="cb62d-196">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="cb62d-197">Komponenten</span><span class="sxs-lookup"><span data-stu-id="cb62d-197"> Components</span></span>

<span data-ttu-id="cb62d-198">Alle Identity -abhängigen nuget-Pakete sind im ASP.net Core frei [gegebenen Framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)enthalten.</span><span class="sxs-lookup"><span data-stu-id="cb62d-198">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="cb62d-199">Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="cb62d-199">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="cb62d-200">Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.net Core Identity und ist in enthalten `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="cb62d-200">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="cb62d-201">Migrieren zu ASP.net CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="cb62d-201">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="cb62d-202">Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identity Stores finden Sie unter [Migrieren Identity der Authentifizierung und ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="cb62d-202">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="cb62d-203">Festlegen der Kenn Wort Stärke</span><span class="sxs-lookup"><span data-stu-id="cb62d-203">Setting password strength</span></span>

<span data-ttu-id="cb62d-204">Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .</span><span class="sxs-lookup"><span data-stu-id="cb62d-204">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="cb62d-205">Adddefaultidentity und addidentity</span><span class="sxs-lookup"><span data-stu-id="cb62d-205">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="cb62d-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wurde in ASP.net Core 2,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="cb62d-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="cb62d-207">`AddDefaultIdentity`Das Aufrufen von ähnelt dem Aufrufen der folgenden:</span><span class="sxs-lookup"><span data-stu-id="cb62d-207">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="cb62d-208">Weitere Informationen finden Sie unter [adddefaultidentity-Quelle](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="cb62d-208">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="cb62d-209">Veröffentlichen statischer Identity Assets verhindern</span><span class="sxs-lookup"><span data-stu-id="cb62d-209">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="cb62d-210">Identity Identity Fügen Sie die folgende `ResolveStaticWebAssetsInputsDependsOn` Eigenschaft und das `RemoveIdentityAssets` Ziel der Projektdatei der APP hinzu, um zu verhindern, dass statische Assets (Stylesheets und JavaScript-Dateien für die Benutzeroberfläche) im Webstamm Verzeichnis veröffentlicht werden:</span><span class="sxs-lookup"><span data-stu-id="cb62d-210">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="cb62d-211">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="cb62d-211">Next Steps</span></span>

* <span data-ttu-id="cb62d-212">[ASP.net Core Identity Quellcode](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="cb62d-212">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="cb62d-213">Weitere Informationen zum Konfigurieren von mithilfe von SQLite finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="cb62d-213">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="cb62d-214">[KonfigurierenIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="cb62d-214">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cb62d-215">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cb62d-215">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cb62d-216">ASP.net Core Identity ist ein Mitgliedschaftssystem, das ASP.net Core-apps Anmelde Funktionalität hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="cb62d-216">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="cb62d-217">Benutzer können ein Konto mit den Anmelde Informationen erstellen, die in gespeichert Identity sind, oder Sie können einen externen Anmelde Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="cb62d-217">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="cb62d-218">Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="cb62d-218">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="cb62d-219">kann mithilfe einer SQL Server-Datenbank zum Speichern von Benutzernamen, Kenn Wörtern und Profildaten konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="cb62d-219"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="cb62d-220">Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="cb62d-220">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="cb62d-221">[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="cb62d-221">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="cb62d-222">In diesem Thema erfahren Sie, wie Sie Identity einen Benutzer mit registrieren, anmelden und abmelden.</span><span class="sxs-lookup"><span data-stu-id="cb62d-222">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="cb62d-223">Ausführlichere Anweisungen zum Erstellen von apps, die verwenden Identity , finden Sie im Abschnitt nächste Schritte am Ende dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="cb62d-223">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="cb62d-224">Adddefaultidentity und addidentity</span><span class="sxs-lookup"><span data-stu-id="cb62d-224">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="cb62d-225"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>wurde in ASP.net Core 2,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="cb62d-225"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="cb62d-226">`AddDefaultIdentity`Das Aufrufen von ähnelt dem Aufrufen der folgenden:</span><span class="sxs-lookup"><span data-stu-id="cb62d-226">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="cb62d-227">Weitere Informationen finden Sie unter [adddefaultidentity-Quelle](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="cb62d-227">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="cb62d-228">Erstellen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="cb62d-228">Create a Web app with authentication</span></span>

<span data-ttu-id="cb62d-229">Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="cb62d-229">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb62d-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb62d-230">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="cb62d-231">Wählen Sie **Datei** > **neu** > **Projekt**aus.</span><span class="sxs-lookup"><span data-stu-id="cb62d-231">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="cb62d-232">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="cb62d-232">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="cb62d-233">Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-233">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="cb62d-234">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="cb62d-234">Click **OK**.</span></span>
* <span data-ttu-id="cb62d-235">Wählen Sie eine ASP.net Core **Webanwendung**, und wählen Sie dann **Authentifizierung ändern**aus.</span><span class="sxs-lookup"><span data-stu-id="cb62d-235">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="cb62d-236">Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="cb62d-236">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cb62d-237">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="cb62d-237">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="cb62d-238">Das generierte Projekt stellt [ASP.net Core Identity ](xref:security/authentication/identity) als [ Razor Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="cb62d-238">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="cb62d-239">Die Identity Razor Klassenbibliothek macht Endpunkte mit dem `Identity` Bereich verfügbar.</span><span class="sxs-lookup"><span data-stu-id="cb62d-239">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="cb62d-240">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="cb62d-240">For example:</span></span>

* <span data-ttu-id="cb62d-241">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="cb62d-241">/Identity/Account/Login</span></span>
* <span data-ttu-id="cb62d-242">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="cb62d-242">/Identity/Account/Logout</span></span>
* <span data-ttu-id="cb62d-243">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="cb62d-243">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="cb62d-244">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="cb62d-244">Apply migrations</span></span>

<span data-ttu-id="cb62d-245">Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="cb62d-245">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb62d-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb62d-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cb62d-247">Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="cb62d-247">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="cb62d-248">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="cb62d-248">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="cb62d-249">Test Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="cb62d-249">Test Register and Login</span></span>

<span data-ttu-id="cb62d-250">Führen Sie die APP aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="cb62d-250">Run the app and register a user.</span></span> <span data-ttu-id="cb62d-251">Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-251">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="cb62d-252">IdentityDienste konfigurieren</span><span class="sxs-lookup"><span data-stu-id="cb62d-252">Configure Identity services</span></span>

<span data-ttu-id="cb62d-253">Dienste werden in hinzugefügt `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cb62d-253">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="cb62d-254">Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-254">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="cb62d-255">Der vorangehende Code konfiguriert Identity mit Standard Options Werten.</span><span class="sxs-lookup"><span data-stu-id="cb62d-255">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="cb62d-256">Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="cb62d-256">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="cb62d-257">wird durch Aufrufen von [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)aktiviert.</span><span class="sxs-lookup"><span data-stu-id="cb62d-257"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="cb62d-258">`UseAuthentication`Fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.</span><span class="sxs-lookup"><span data-stu-id="cb62d-258">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="cb62d-259">Weitere Informationen finden Sie unter [identityoptions-Klasse](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) und [Anwendungsstart](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="cb62d-259">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="cb62d-260">Gerüst: registrieren, anmelden und Abmelden</span><span class="sxs-lookup"><span data-stu-id="cb62d-260">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="cb62d-261">Befolgen Sie die [Gerüst Identität in einem Razor Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="cb62d-261">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="cb62d-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="cb62d-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="cb62d-263">Fügen Sie die Registrierungs-, Anmelde-und Abmelde Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="cb62d-263">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="cb62d-264">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="cb62d-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="cb62d-265">Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus.</span><span class="sxs-lookup"><span data-stu-id="cb62d-265">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="cb62d-266">Verwenden Sie andernfalls den korrekten Namespace für `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="cb62d-266">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="cb62d-267">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-267">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="cb62d-268">Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="cb62d-268">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="cb62d-269">Register überprüfen</span><span class="sxs-lookup"><span data-stu-id="cb62d-269">Examine Register</span></span>

<span data-ttu-id="cb62d-270">Wenn ein Benutzer auf den Link **registrieren** klickt, `RegisterModel.OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-270">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="cb62d-271">Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für das `_userManager` Objekt erstellt:</span><span class="sxs-lookup"><span data-stu-id="cb62d-271">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="cb62d-272">Wenn der Benutzer erfolgreich erstellt wurde, wird der Benutzer durch den-Befehl angemeldet `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="cb62d-272">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="cb62d-273">**Hinweis:** Unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) finden Sie Schritte zum Verhindern der sofortigen Anmeldung bei der Registrierung.</span><span class="sxs-lookup"><span data-stu-id="cb62d-273">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="cb62d-274">Anmelden</span><span class="sxs-lookup"><span data-stu-id="cb62d-274">Log in</span></span>

<span data-ttu-id="cb62d-275">Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="cb62d-275">The Login form is displayed when:</span></span>

* <span data-ttu-id="cb62d-276">Der Link " **Anmelden** " ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="cb62d-276">The **Log in** link is selected.</span></span>
* <span data-ttu-id="cb62d-277">Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="cb62d-277">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="cb62d-278">Wenn das Formular auf der Anmeldeseite übermittelt wird, `OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-278">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="cb62d-279">`PasswordSignInAsync`wird für das- `_signInManager` Objekt aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="cb62d-279">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="cb62d-280">Informationen dazu, wie Sie Autorisierungs Entscheidungen treffen, finden Sie unter <xref:security/authorization/introduction> .</span><span class="sxs-lookup"><span data-stu-id="cb62d-280">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="cb62d-281">Abmelden</span><span class="sxs-lookup"><span data-stu-id="cb62d-281">Log out</span></span>

<span data-ttu-id="cb62d-282">Der Link " **Abmelden** " Ruft die `LogoutModel.OnPost` Aktion auf.</span><span class="sxs-lookup"><span data-stu-id="cb62d-282">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="cb62d-283">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="cb62d-283">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="cb62d-284">Post wird in der Datei *pages/Shared/_LoginPartial. cshtml*angegeben:</span><span class="sxs-lookup"><span data-stu-id="cb62d-284">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="cb62d-285">TestIdentity</span><span class="sxs-lookup"><span data-stu-id="cb62d-285">Test Identity</span></span>

<span data-ttu-id="cb62d-286">Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten.</span><span class="sxs-lookup"><span data-stu-id="cb62d-286">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="cb62d-287">IdentityFügen Sie zum Testen [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) der Seite Datenschutz hinzu.</span><span class="sxs-lookup"><span data-stu-id="cb62d-287">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="cb62d-288">Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link.</span><span class="sxs-lookup"><span data-stu-id="cb62d-288">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="cb62d-289">Sie werden zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="cb62d-289">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="cb62d-290">EntdeckenIdentity</span><span class="sxs-lookup"><span data-stu-id="cb62d-290">Explore Identity</span></span>

<span data-ttu-id="cb62d-291">Weitere Informationen finden Sie unter Identity :</span><span class="sxs-lookup"><span data-stu-id="cb62d-291">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="cb62d-292">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="cb62d-292">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="cb62d-293">Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.</span><span class="sxs-lookup"><span data-stu-id="cb62d-293">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="cb62d-294">Komponenten</span><span class="sxs-lookup"><span data-stu-id="cb62d-294"> Components</span></span>

<span data-ttu-id="cb62d-295">Alle Identity abhängigen nuget-Pakete sind im [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)enthalten.</span><span class="sxs-lookup"><span data-stu-id="cb62d-295">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="cb62d-296">Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="cb62d-296">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="cb62d-297">Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.net Core Identity und ist in enthalten `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="cb62d-297">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="cb62d-298">Migrieren zu ASP.net CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="cb62d-298">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="cb62d-299">Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identity Stores finden Sie unter [Migrieren Identity der Authentifizierung und ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="cb62d-299">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="cb62d-300">Festlegen der Kenn Wort Stärke</span><span class="sxs-lookup"><span data-stu-id="cb62d-300">Setting password strength</span></span>

<span data-ttu-id="cb62d-301">Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .</span><span class="sxs-lookup"><span data-stu-id="cb62d-301">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cb62d-302">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="cb62d-302">Next Steps</span></span>

* <span data-ttu-id="cb62d-303">Weitere Informationen zum Konfigurieren von mithilfe von SQLite finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity .</span><span class="sxs-lookup"><span data-stu-id="cb62d-303">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="cb62d-304">[KonfigurierenIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="cb62d-304">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
