---
title: Hinzufügen, herunterladen und Löschen von Benutzerdaten auf Identität in einem ASP.NET Core-Projekt
author: rick-anderson
description: Erfahren Sie, wie benutzerdefinierte Benutzerdaten Identität in einem ASP.NET Core-Projekt hinzugefügt. Löschen von Daten pro DSGVO.
ms.author: riande
ms.date: 12/05/2019
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: f54df68834cd3e2493e558aaab9851f036f3f01b
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2019
ms.locfileid: "74880756"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="d5240-104">Hinzufügen, herunterladen und Löschen von benutzerdefinierten Daten die Identität in einem ASP.NET Core-Projekt</span><span class="sxs-lookup"><span data-stu-id="d5240-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="d5240-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d5240-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d5240-106">In diesem Artikel zeigt, wie Sie:</span><span class="sxs-lookup"><span data-stu-id="d5240-106">This article shows how to:</span></span>

* <span data-ttu-id="d5240-107">Fügen Sie benutzerdefinierter Benutzerdaten in einer ASP.NET Core-Web-app hinzu.</span><span class="sxs-lookup"><span data-stu-id="d5240-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="d5240-108">Markieren Sie das benutzerdefinierte Benutzerdaten Modell mit dem <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute>-Attribut, damit es automatisch heruntergeladen und gelöscht werden kann.</span><span class="sxs-lookup"><span data-stu-id="d5240-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="d5240-109">Sodass die Daten, die heruntergeladen und gelöscht werden kann hilft bei der Erfüllung [DSGVO](xref:security/gdpr) Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="d5240-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="d5240-110">Die Project-Beispiels aus einer Razor Pages-Web-app erstellt wird, aber die Anweisungen ähneln denen für eine ASP.NET Core MVC-Web-app.</span><span class="sxs-lookup"><span data-stu-id="d5240-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="d5240-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d5240-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d5240-112">Erforderliche Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d5240-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="d5240-113">Erstellen einer Razor-Web-App</span><span class="sxs-lookup"><span data-stu-id="d5240-113">Create a Razor web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d5240-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5240-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="d5240-115">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d5240-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="d5240-116">Nennen Sie das Projekt **"WebApp1"** Wenn Sie möchten einen übereinstimmenden Namespace die [Beispiel herunterladen](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Code.</span><span class="sxs-lookup"><span data-stu-id="d5240-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="d5240-117">Wählen Sie **ASP.net Core Webanwendung** > **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="d5240-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="d5240-118">Wählen Sie in der Dropdown Liste **ASP.net Core 3,0** aus.</span><span class="sxs-lookup"><span data-stu-id="d5240-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="d5240-119">**Webanwendung** auswählen > **OK**</span><span class="sxs-lookup"><span data-stu-id="d5240-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="d5240-120">Erstellen Sie das Projekt, und führen Sie es aus.</span><span class="sxs-lookup"><span data-stu-id="d5240-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="d5240-121">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d5240-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="d5240-122">Nennen Sie das Projekt **"WebApp1"** Wenn Sie möchten einen übereinstimmenden Namespace die [Beispiel herunterladen](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Code.</span><span class="sxs-lookup"><span data-stu-id="d5240-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="d5240-123">Wählen Sie **ASP.net Core Webanwendung** > **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="d5240-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="d5240-124">Wählen Sie in der Dropdown Liste **ASP.net Core 2,2** aus.</span><span class="sxs-lookup"><span data-stu-id="d5240-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="d5240-125">**Webanwendung** auswählen > **OK**</span><span class="sxs-lookup"><span data-stu-id="d5240-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="d5240-126">Erstellen Sie das Projekt, und führen Sie es aus.</span><span class="sxs-lookup"><span data-stu-id="d5240-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d5240-127">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="d5240-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="d5240-128">Führen Sie die Identity-gerüstbauer</span><span class="sxs-lookup"><span data-stu-id="d5240-128">Run the Identity scaffolder</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d5240-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5240-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d5240-130">Von **Projektmappen-Explorer**, mit der rechten Maustaste auf das Projekt > **hinzufügen** > **neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="d5240-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="d5240-131">Im linken Bereich, der die **Gerüst hinzufügen** wählen Sie im Dialogfeld **Identität** > **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d5240-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="d5240-132">In der **ADD Identity** Dialog, der die folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="d5240-132">In the **ADD Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="d5240-133">Wählen Sie die vorhandenen Layoutdatei *~/Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="d5240-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="d5240-134">Wählen Sie die folgenden Dateien überschreiben:</span><span class="sxs-lookup"><span data-stu-id="d5240-134">Select the following files to override:</span></span>
    * <span data-ttu-id="d5240-135">**Konto/registrieren**</span><span class="sxs-lookup"><span data-stu-id="d5240-135">**Account/Register**</span></span>
    * <span data-ttu-id="d5240-136">**Konto / / Index verwalten**</span><span class="sxs-lookup"><span data-stu-id="d5240-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="d5240-137">Wählen Sie die **+** Schaltfläche zum Erstellen eines neuen **Datenkontextklasse**.</span><span class="sxs-lookup"><span data-stu-id="d5240-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="d5240-138">Akzeptieren Sie den Typ (**WebApp1.Models.WebApp1Context** Wenn das Projekt den Namen **"WebApp1"** ).</span><span class="sxs-lookup"><span data-stu-id="d5240-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="d5240-139">Wählen Sie die **+** Schaltfläche zum Erstellen eines neuen **Benutzerklasse**.</span><span class="sxs-lookup"><span data-stu-id="d5240-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="d5240-140">Akzeptieren Sie den Typ (**WebApp1User** Wenn das Projekt den Namen **"WebApp1"** ) > **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d5240-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="d5240-141">Wählen Sie **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d5240-141">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d5240-142">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="d5240-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d5240-143">Wenn Sie die ASP.NET Core-gerüstbauer noch nicht installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="d5240-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="d5240-144">Fügen Sie einen Paketverweis auf [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) zur Projektdatei (.csproj).</span><span class="sxs-lookup"><span data-stu-id="d5240-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="d5240-145">Führen Sie den folgenden Befehl im Verzeichnis Projekts ein:</span><span class="sxs-lookup"><span data-stu-id="d5240-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="d5240-146">Führen Sie den folgenden Befehl zum Auflisten von Optionen gerüstbauer Identität:</span><span class="sxs-lookup"><span data-stu-id="d5240-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="d5240-147">Führen Sie im Projektordner der Identity-gerüstbauer:</span><span class="sxs-lookup"><span data-stu-id="d5240-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="d5240-148">Führen Sie die Anweisungen in [Migrationen, UseAuthentication und Layout](xref:security/authentication/scaffold-identity#efm) die folgenden Schritte ausführen:</span><span class="sxs-lookup"><span data-stu-id="d5240-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="d5240-149">Erstellen Sie eine Migration aus, und aktualisieren Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="d5240-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="d5240-150">Fügen Sie `UseAuthentication` zu `Startup.Configure` hinzu.</span><span class="sxs-lookup"><span data-stu-id="d5240-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="d5240-151">Hinzufügen `<partial name="_LoginPartial" />` zur Layoutdatei.</span><span class="sxs-lookup"><span data-stu-id="d5240-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="d5240-152">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="d5240-152">Test the app:</span></span>
  * <span data-ttu-id="d5240-153">Registrieren eines Benutzers</span><span class="sxs-lookup"><span data-stu-id="d5240-153">Register a user</span></span>
  * <span data-ttu-id="d5240-154">Wählen Sie den neuen Benutzernamen ein (neben der **Logout** Link).</span><span class="sxs-lookup"><span data-stu-id="d5240-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="d5240-155">Möglicherweise müssen Sie das Fenster erweitern, oder wählen Sie das Navigationssymbol Leiste, um den Benutzernamen und anderen Links anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="d5240-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="d5240-156">Wählen Sie die **personenbezogene Daten** Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="d5240-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="d5240-157">Wählen Sie die **herunterladen** Schaltfläche und untersucht die *PersonalData.json* Datei.</span><span class="sxs-lookup"><span data-stu-id="d5240-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="d5240-158">Testen der **löschen** Schaltfläche, die der angemeldete Benutzer löscht.</span><span class="sxs-lookup"><span data-stu-id="d5240-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="d5240-159">Hinzufügen von benutzerdefinierten Benutzerdaten mit der Identity-DB</span><span class="sxs-lookup"><span data-stu-id="d5240-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="d5240-160">Update der `IdentityUser` abgeleitete Klasse mit benutzerdefinierten Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="d5240-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="d5240-161">Wenn Sie das Projekt "WebApp1" genannt, kann die Datei heißt *Areas/Identity/Data/WebApp1User.cs*.</span><span class="sxs-lookup"><span data-stu-id="d5240-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="d5240-162">Aktualisieren Sie die Datei mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d5240-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="d5240-163">Eigenschaften mit dem Attribut " [Personal Data](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) ":</span><span class="sxs-lookup"><span data-stu-id="d5240-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="d5240-164">Gelöscht, wenn die *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor-Seite ruft `UserManager.Delete`.</span><span class="sxs-lookup"><span data-stu-id="d5240-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="d5240-165">In der heruntergeladenen Daten enthalten die *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor-Seite.</span><span class="sxs-lookup"><span data-stu-id="d5240-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="d5240-166">Aktualisieren Sie die Seite "Account/Manage/Index.cshtml"</span><span class="sxs-lookup"><span data-stu-id="d5240-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="d5240-167">Update der `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* mit folgendem hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="d5240-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="d5240-168">Update der *Areas/Identity/Pages/Account/Manage/Index.cshtml* mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="d5240-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="d5240-169">Update der *Areas/Identity/Pages/Account/Manage/Index.cshtml* mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="d5240-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="d5240-170">Aktualisiert die Account/Register.cshtml-Seite</span><span class="sxs-lookup"><span data-stu-id="d5240-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="d5240-171">Update der `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* mit folgendem hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="d5240-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="d5240-172">Update der *Areas/Identity/Pages/Account/Register.cshtml* mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="d5240-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="d5240-173">Update der *Areas/Identity/Pages/Account/Register.cshtml* mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="d5240-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="d5240-174">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="d5240-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="d5240-175">Fügen Sie eine Migration für die benutzerdefinierten Daten hinzu</span><span class="sxs-lookup"><span data-stu-id="d5240-175">Add a migration for the custom user data</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d5240-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d5240-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d5240-177">In der Visual Studio **-Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="d5240-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="d5240-178">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="d5240-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="d5240-179">Test erstellen, anzeigen, herunterladen und Löschen von benutzerdefinierten Daten</span><span class="sxs-lookup"><span data-stu-id="d5240-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="d5240-180">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="d5240-180">Test the app:</span></span>

* <span data-ttu-id="d5240-181">Registrieren Sie einen neuen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="d5240-181">Register a new user.</span></span>
* <span data-ttu-id="d5240-182">Zeigen Sie die benutzerdefinierten Daten, auf die `/Identity/Account/Manage` Seite.</span><span class="sxs-lookup"><span data-stu-id="d5240-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="d5240-183">Herunterladen und Anzeigen der Benutzer persönliche Daten von der `/Identity/Account/Manage/PersonalData` Seite.</span><span class="sxs-lookup"><span data-stu-id="d5240-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>
