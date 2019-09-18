---
title: Einführung in die Identität auf ASP.net Core
author: rick-anderson
description: Verwenden Sie die Identität mit einer ASP.net Core-app. Erfahren Sie, wie Sie Kenn Wort Anforderungen ("Requirements Digit", "Requirements dlength", "Requirements duniquechars" usw.) festlegen.
ms.author: riande
ms.date: 03/26/2019
uid: security/authentication/identity
ms.openlocfilehash: 979681cfc196aca9fb5097583d99a086e1c597ba
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082455"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="85edd-104">Einführung in die Identität auf ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="85edd-104">Introduction to Identity on ASP.NET Core</span></span>

<span data-ttu-id="85edd-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="85edd-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="85edd-106">ASP.net Core Identität ist ein Mitgliedschaftssystem, das ASP.net Core-apps Anmelde Funktionalität hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="85edd-106">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="85edd-107">Benutzer können ein Konto mit den in der Identität gespeicherten Anmelde Informationen erstellen, oder Sie können einen externen Anmelde Anbieter verwenden.</span><span class="sxs-lookup"><span data-stu-id="85edd-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="85edd-108">Zu den unterstützten externen Anmelde Anbietern zählen [Facebook, Google, Microsoft-Konto und Twitter](xref:security/authentication/social/index).</span><span class="sxs-lookup"><span data-stu-id="85edd-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="85edd-109">Die Identität kann mit einer SQL Server Datenbank konfiguriert werden, um Benutzernamen, Kenn Wörter und Profildaten zu speichern.</span><span class="sxs-lookup"><span data-stu-id="85edd-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="85edd-110">Alternativ können Sie auch einen weiteren permanenten Speicher verwenden, z. b. Azure Table Storage.</span><span class="sxs-lookup"><span data-stu-id="85edd-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="85edd-111">[Anzeigen oder Herunterladen des Beispielcodes](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([herunterladen)](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="85edd-111">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="85edd-112">In diesem Thema erfahren Sie, wie Sie mit der Identität einen Benutzer registrieren, anmelden und abmelden.</span><span class="sxs-lookup"><span data-stu-id="85edd-112">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="85edd-113">Ausführlichere Anweisungen zum Erstellen von apps, die die Identität verwenden, finden Sie im Abschnitt nächste Schritte am Ende dieses Artikels.</span><span class="sxs-lookup"><span data-stu-id="85edd-113">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="85edd-114">Adddefaultidentity und addidentity</span><span class="sxs-lookup"><span data-stu-id="85edd-114">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="85edd-115">[Adddefaultidentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) wurde in ASP.net Core 2,1 eingeführt.</span><span class="sxs-lookup"><span data-stu-id="85edd-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="85edd-116">Das `AddDefaultIdentity` Aufrufen von ähnelt dem Aufrufen der folgenden:</span><span class="sxs-lookup"><span data-stu-id="85edd-116">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* [<span data-ttu-id="85edd-117">Addidentity</span><span class="sxs-lookup"><span data-stu-id="85edd-117">AddIdentity</span></span>](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [<span data-ttu-id="85edd-118">AddDefaultUI</span><span class="sxs-lookup"><span data-stu-id="85edd-118">AddDefaultUI</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [<span data-ttu-id="85edd-119">AddDefaultTokenProviders</span><span class="sxs-lookup"><span data-stu-id="85edd-119">AddDefaultTokenProviders</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

<span data-ttu-id="85edd-120">Weitere Informationen finden Sie unter [adddefaultidentity-Quelle](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="85edd-120">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="85edd-121">Erstellen einer Web-App mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="85edd-121">Create a Web app with authentication</span></span>

<span data-ttu-id="85edd-122">Erstellen Sie ein ASP.net Core Webanwendungs Projekt mit einzelnen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="85edd-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85edd-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85edd-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="85edd-124">Wählen Sie **Datei** > **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="85edd-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="85edd-125">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="85edd-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="85edd-126">Nennen Sie das Projekt **"WebApp1"** , um den gleichen Namespace wie das Projekt herunterzuladen.</span><span class="sxs-lookup"><span data-stu-id="85edd-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="85edd-127">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="85edd-127">Click **OK**.</span></span>
* <span data-ttu-id="85edd-128">Wählen Sie eine ASP.net Core **Webanwendung**, und wählen Sie dann **Authentifizierung ändern**aus.</span><span class="sxs-lookup"><span data-stu-id="85edd-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="85edd-129">Wählen Sie **einzelne Benutzerkonten** , und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="85edd-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="85edd-130">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="85edd-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="85edd-131">Das generierte Projekt stellt [ASP.net Core Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class)bereit.</span><span class="sxs-lookup"><span data-stu-id="85edd-131">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="85edd-132">Die Razor-Klassenbibliothek für die Identität macht Endpunkte mit dem `Identity` Bereich verfügbar.</span><span class="sxs-lookup"><span data-stu-id="85edd-132">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="85edd-133">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="85edd-133">For example:</span></span>

* <span data-ttu-id="85edd-134">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="85edd-134">/Identity/Account/Login</span></span>
* <span data-ttu-id="85edd-135">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="85edd-135">/Identity/Account/Logout</span></span>
* <span data-ttu-id="85edd-136">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="85edd-136">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="85edd-137">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="85edd-137">Apply migrations</span></span>

<span data-ttu-id="85edd-138">Wenden Sie die Migrationen an, um die Datenbank zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="85edd-138">Apply the migrations to initialise the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85edd-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85edd-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="85edd-140">Führen Sie den folgenden Befehl in der Paket-Manager-Konsole (PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="85edd-140">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="85edd-141">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="85edd-141">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="85edd-142">Test Registrierung und Anmeldung</span><span class="sxs-lookup"><span data-stu-id="85edd-142">Test Register and Login</span></span>

<span data-ttu-id="85edd-143">Führen Sie die APP aus, und registrieren Sie einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="85edd-143">Run the app and register a user.</span></span> <span data-ttu-id="85edd-144">Abhängig von der Bildschirmgröße müssen Sie ggf. die Navigations Schaltfläche auswählen, um die Links **registrieren** und **Anmelden** anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="85edd-144">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="85edd-145">Konfigurieren von Identitäts Diensten</span><span class="sxs-lookup"><span data-stu-id="85edd-145">Configure Identity services</span></span>

<span data-ttu-id="85edd-146">Dienste werden in `ConfigureServices`hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="85edd-146">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="85edd-147">Das typische Muster besteht darin, alle `Add{Service}`-Methoden und dann alle `services.Configure{Service}`-Methoden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="85edd-147">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="85edd-148">Der vorangehende Code konfiguriert die Identität mit Standard Options Werten.</span><span class="sxs-lookup"><span data-stu-id="85edd-148">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="85edd-149">Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)der App zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="85edd-149">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="85edd-150">Die Identität wird durch Aufrufen von " [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)" aktiviert.</span><span class="sxs-lookup"><span data-stu-id="85edd-150">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="85edd-151">`UseAuthentication`Fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.</span><span class="sxs-lookup"><span data-stu-id="85edd-151">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   <span data-ttu-id="85edd-152">Dienste werden der Anwendung über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="85edd-152">Services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="85edd-153">Die Identität wird für die Anwendung aktiviert, `UseAuthentication` indem in `Configure` der-Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="85edd-153">Identity is enabled for the application by calling `UseAuthentication` in the `Configure` method.</span></span> <span data-ttu-id="85edd-154">`UseAuthentication`Fügt der Anforderungs Pipeline Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.</span><span class="sxs-lookup"><span data-stu-id="85edd-154">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   <span data-ttu-id="85edd-155">Diese Dienste werden der Anwendung über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="85edd-155">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="85edd-156">Die Identität wird für die Anwendung aktiviert, `UseIdentity` indem in `Configure` der-Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="85edd-156">Identity is enabled for the application by calling `UseIdentity` in the `Configure` method.</span></span> <span data-ttu-id="85edd-157">`UseIdentity`Fügt der Anforderungs Pipeline cookiebasierte Authentifizierungs [Middleware](xref:fundamentals/middleware/index) hinzu.</span><span class="sxs-lookup"><span data-stu-id="85edd-157">`UseIdentity` adds cookie-based authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

<span data-ttu-id="85edd-158">Weitere Informationen finden Sie unter [identityoptions-Klasse](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) und [Anwendungsstart](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="85edd-158">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="85edd-159">Gerüst: registrieren, anmelden und Abmelden</span><span class="sxs-lookup"><span data-stu-id="85edd-159">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="85edd-160">Befolgen Sie die [Gerüst Identität in einem Razor-Projekt mit Autorisierungs](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) Anweisungen, um den in diesem Abschnitt gezeigten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="85edd-160">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85edd-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85edd-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="85edd-162">Fügen Sie die Registrierungs-, Anmelde-und Abmelde Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="85edd-162">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="85edd-163">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="85edd-163">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="85edd-164">Wenn Sie das Projekt mit dem Namen **"WebApp1"** erstellt haben, führen Sie die folgenden Befehle aus.</span><span class="sxs-lookup"><span data-stu-id="85edd-164">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="85edd-165">Verwenden Sie andernfalls den korrekten Namespace für `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="85edd-165">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="85edd-166">PowerShell verwendet ein Semikolon als Befehls Trennzeichen.</span><span class="sxs-lookup"><span data-stu-id="85edd-166">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="85edd-167">Wenn Sie PowerShell verwenden, führen Sie einen Escapezeichen in der Datei Liste aus, oder fügen Sie die Datei Liste in doppelte Anführungszeichen ein, wie im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="85edd-167">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="85edd-168">Register überprüfen</span><span class="sxs-lookup"><span data-stu-id="85edd-168">Examine Register</span></span>

::: moniker range=">= aspnetcore-2.1"

   <span data-ttu-id="85edd-169">Wenn ein Benutzer auf den Link **registrieren** klickt, `RegisterModel.OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="85edd-169">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="85edd-170">Der Benutzer wird von " [kreateasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) " für `_userManager` das-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="85edd-170">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="85edd-171">`_userManager`wird durch Abhängigkeitsinjektion bereitgestellt):</span><span class="sxs-lookup"><span data-stu-id="85edd-171">`_userManager` is provided by dependency injection):</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="85edd-172">Wenn ein Benutzer auf den Link **registrieren** klickt, `Register` wird die Aktion für `AccountController`aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="85edd-172">When a user clicks the **Register** link, the `Register` action is invoked on `AccountController`.</span></span> <span data-ttu-id="85edd-173">Die `Register` Aktion erstellt den Benutzer durch Aufrufen `CreateAsync` von für `_userManager` `AccountController` das-Objekt (bereitgestellt von von der Abhängigkeitsinjektion):</span><span class="sxs-lookup"><span data-stu-id="85edd-173">The `Register` action creates the user by calling `CreateAsync` on the `_userManager` object (provided to `AccountController` by dependency injection):</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   <span data-ttu-id="85edd-174">Wenn der Benutzer erfolgreich erstellt wurde, wird der Benutzer durch den `_signInManager.SignInAsync`-Befehl angemeldet.</span><span class="sxs-lookup"><span data-stu-id="85edd-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

   <span data-ttu-id="85edd-175">**Hinweis**: Unter [Konto Bestätigung](xref:security/authentication/accconfirm#prevent-login-at-registration) finden Sie Schritte zum Verhindern der sofortigen Anmeldung bei der Registrierung.</span><span class="sxs-lookup"><span data-stu-id="85edd-175">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="85edd-176">Anmelden</span><span class="sxs-lookup"><span data-stu-id="85edd-176">Log in</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="85edd-177">Das Anmeldeformular wird angezeigt, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="85edd-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="85edd-178">Der Link " **Anmelden** " ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="85edd-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="85edd-179">Ein Benutzer versucht, auf eine eingeschränkte Seite zuzugreifen, für die er nicht autorisiert ist, **oder** wenn er nicht vom System authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="85edd-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="85edd-180">Wenn das Formular auf der Anmeldeseite übermittelt wird, `OnPostAsync` wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="85edd-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="85edd-181">`PasswordSignInAsync`wird für das `_signInManager` -Objekt aufgerufen (durch Abhängigkeitsinjektion bereitgestellt).</span><span class="sxs-lookup"><span data-stu-id="85edd-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   <span data-ttu-id="85edd-182">Die- `Controller` Basisklasse macht `User` eine-Eigenschaft verfügbar, auf die Sie über Controller Methoden zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="85edd-182">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="85edd-183">Beispielsweise können `User.Claims` Sie Autorisierungs Entscheidungen auflisten und treffen.</span><span class="sxs-lookup"><span data-stu-id="85edd-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="85edd-184">Weitere Informationen finden Sie unter <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="85edd-184">For more information, see <xref:security/authorization/introduction>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="85edd-185">Das Anmeldeformular wird angezeigt, wenn Benutzer den Link Anmelden auswählen oder umgeleitet werden, wenn Sie auf eine Seite zugreifen, **für** die eine Authentifizierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="85edd-185">The Login form is displayed when users select the **Log in** link or are redirected when accessing a page that requires authentication.</span></span> <span data-ttu-id="85edd-186">Wenn der Benutzer das Formular auf der Anmeldeseite sendet, wird `AccountController` die `Login` Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="85edd-186">When the user submits the form on the Login page, the `AccountController` `Login` action is called.</span></span>

<span data-ttu-id="85edd-187">Die `Login` Aktion ruft `PasswordSignInAsync` `_signInManager` für`AccountController` das-Objekt auf (bereitgestellt von von der Abhängigkeitsinjektion).</span><span class="sxs-lookup"><span data-stu-id="85edd-187">The `Login` action calls `PasswordSignInAsync` on the `_signInManager` object (provided to `AccountController` by dependency injection).</span></span>

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

<span data-ttu-id="85edd-188">Die Basisklasse`Controller` ( `PageModel`oder) macht eine `User` -Eigenschaft verfügbar.</span><span class="sxs-lookup"><span data-stu-id="85edd-188">The base (`Controller` or `PageModel`) class exposes a `User` property.</span></span> <span data-ttu-id="85edd-189">`User.Claims` Kann z. b. aufgelistet werden, um Autorisierungs Entscheidungen zu treffen.</span><span class="sxs-lookup"><span data-stu-id="85edd-189">For example, `User.Claims` can be enumerated to make authorization decisions.</span></span>

::: moniker-end

### <a name="log-out"></a><span data-ttu-id="85edd-190">Abmelden</span><span class="sxs-lookup"><span data-stu-id="85edd-190">Log out</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="85edd-191">Der Link " **Abmelden** " ruft `LogoutModel.OnPost` die Aktion auf.</span><span class="sxs-lookup"><span data-stu-id="85edd-191">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="85edd-192">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="85edd-192">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="85edd-193">Post wird in den *Seiten "pages/Shared/_LoginPartial. cshtml*" angegeben:</span><span class="sxs-lookup"><span data-stu-id="85edd-193">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="85edd-194">Wenn Sie auf den Link **Abmelden** klicken `LogOut` , wird die Aktion aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="85edd-194">Clicking the **Log out** link calls the `LogOut` action.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   <span data-ttu-id="85edd-195">Der vorangehende Code ruft `_signInManager.SignOutAsync` die-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="85edd-195">The preceding code calls the `_signInManager.SignOutAsync` method.</span></span> <span data-ttu-id="85edd-196">Die `SignOutAsync` -Methode löscht die in einem Cookie gespeicherten Ansprüche des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="85edd-196">The `SignOutAsync` method clears the user's claims stored in a cookie.</span></span>

::: moniker-end

## <a name="test-identity"></a><span data-ttu-id="85edd-197">Test Identität</span><span class="sxs-lookup"><span data-stu-id="85edd-197">Test Identity</span></span>

<span data-ttu-id="85edd-198">Die Standardweb Projektvorlagen ermöglichen den anonymen Zugriff auf die Startseiten.</span><span class="sxs-lookup"><span data-stu-id="85edd-198">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="85edd-199">Fügen Sie zum Testen der [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Identität der Seite Datenschutz hinzu.</span><span class="sxs-lookup"><span data-stu-id="85edd-199">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

<span data-ttu-id="85edd-200">Wenn Sie angemeldet sind, melden Sie sich ab. Führen Sie die APP aus, und wählen Sie den **Datenschutz** Link.</span><span class="sxs-lookup"><span data-stu-id="85edd-200">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="85edd-201">Sie werden zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="85edd-201">You are redirected to the login page.</span></span>

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a><span data-ttu-id="85edd-202">Identität erkunden</span><span class="sxs-lookup"><span data-stu-id="85edd-202">Explore Identity</span></span>

<span data-ttu-id="85edd-203">So untersuchen Sie die Identität ausführlicher:</span><span class="sxs-lookup"><span data-stu-id="85edd-203">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="85edd-204">Benutzeroberflächen Quelle für vollständige Identität erstellen</span><span class="sxs-lookup"><span data-stu-id="85edd-204">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="85edd-205">Überprüfen Sie die Quelle jeder Seite, und schrittweise durch den Debugger.</span><span class="sxs-lookup"><span data-stu-id="85edd-205">Examine the source of each page and step through the debugger.</span></span>

::: moniker-end

## <a name="identity-components"></a><span data-ttu-id="85edd-206">Identitäts Komponenten</span><span class="sxs-lookup"><span data-stu-id="85edd-206">Identity Components</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="85edd-207">Alle Identitäts abhängigen nuget-Pakete sind im [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)enthalten.</span><span class="sxs-lookup"><span data-stu-id="85edd-207">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="85edd-208">Das primäre Paket für Identity ist [Microsoft. aspnetcore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span><span class="sxs-lookup"><span data-stu-id="85edd-208">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="85edd-209">Dieses Paket enthält den Kernsatz von Schnittstellen für ASP.net Core Identität und ist in enthalten `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="85edd-209">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="85edd-210">Migrieren zu ASP.net Core Identität</span><span class="sxs-lookup"><span data-stu-id="85edd-210">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="85edd-211">Weitere Informationen und Anleitungen zum Migrieren Ihres vorhandenen Identitäts Speicher finden Sie unter [Migrieren von Authentifizierung und Identität](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="85edd-211">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="85edd-212">Festlegen der Kenn Wort Stärke</span><span class="sxs-lookup"><span data-stu-id="85edd-212">Setting password strength</span></span>

<span data-ttu-id="85edd-213">Ein Beispiel zum Festlegen der Mindestanforderungen für das Kennwort finden Sie unter [Konfiguration](#pw) .</span><span class="sxs-lookup"><span data-stu-id="85edd-213">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="85edd-214">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="85edd-214">Next Steps</span></span>

* [<span data-ttu-id="85edd-215">Konfigurieren von Identity</span><span class="sxs-lookup"><span data-stu-id="85edd-215">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
