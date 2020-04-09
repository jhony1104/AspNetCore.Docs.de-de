---
title: Hinzufügen, Herunterladen und Löschen von Benutzerdaten zu Identity in einem ASP.NET Core-Projekt
author: rick-anderson
description: Erfahren Sie, wie Sie in einem ASP.NET Core-Projekt benutzerdefinierte Benutzerdaten zu Identity hinzufügen. Löschen Sie Daten pro DSGVO.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501232"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="06513-104">Hinzufügen, Herunterladen und Löschen benutzerdefinierter Benutzerdaten zu Identity in einem ASP.NET Core-Projekt</span><span class="sxs-lookup"><span data-stu-id="06513-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="06513-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="06513-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="06513-106">In diesem Artikel werden die folgenden Aufgaben erläutert:</span><span class="sxs-lookup"><span data-stu-id="06513-106">This article shows how to:</span></span>

* <span data-ttu-id="06513-107">Fügen Sie einer ASP.NET Core-Web-App benutzerdefinierte Benutzerdaten hinzu.</span><span class="sxs-lookup"><span data-stu-id="06513-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="06513-108">Markieren Sie das benutzerdefinierte <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> Benutzerdatenmodell mit dem Attribut, damit es automatisch zum Herunterladen und Löschen verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="06513-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="06513-109">Die Möglichkeit, die Daten herunterzuladen und zu löschen, trägt dazu bei, die [DSGVO-Anforderungen](xref:security/gdpr) zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="06513-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="06513-110">Das Projektbeispiel wird aus einer Razor Pages-Web-App erstellt, aber die Anweisungen sind für eine ASP.NET Core MVC-Web-App ähnlich.</span><span class="sxs-lookup"><span data-stu-id="06513-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="06513-111">[Beispielcode anzeigen oder herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([downloaden](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="06513-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="06513-112">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="06513-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="06513-113">Erstellen einer Razor-Web-App</span><span class="sxs-lookup"><span data-stu-id="06513-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06513-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06513-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="06513-115">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="06513-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="06513-116">Benennen Sie das Projekt **WebApp1,** wenn Sie es mit dem Namespace des [Download-Beispielcodes](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) übereinstimmen möchten.</span><span class="sxs-lookup"><span data-stu-id="06513-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="06513-117">Auswählen **ASP.NET Core Web Application** > **OK**</span><span class="sxs-lookup"><span data-stu-id="06513-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="06513-118">Wählen Sie **ASP.NET Core 3.0** in der Dropdown-Liste</span><span class="sxs-lookup"><span data-stu-id="06513-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="06513-119">**Webanwendung** > **OK** auswählen</span><span class="sxs-lookup"><span data-stu-id="06513-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="06513-120">Erstellen Sie das Projekt, und führen Sie es aus.</span><span class="sxs-lookup"><span data-stu-id="06513-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="06513-121">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="06513-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="06513-122">Benennen Sie das Projekt **WebApp1,** wenn Sie es mit dem Namespace des [Download-Beispielcodes](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) übereinstimmen möchten.</span><span class="sxs-lookup"><span data-stu-id="06513-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="06513-123">Auswählen **ASP.NET Core Web Application** > **OK**</span><span class="sxs-lookup"><span data-stu-id="06513-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="06513-124">Wählen Sie **ASP.NET Core 2.2** in der Dropdown-Liste</span><span class="sxs-lookup"><span data-stu-id="06513-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="06513-125">**Webanwendung** > **OK** auswählen</span><span class="sxs-lookup"><span data-stu-id="06513-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="06513-126">Erstellen Sie das Projekt, und führen Sie es aus.</span><span class="sxs-lookup"><span data-stu-id="06513-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="06513-127">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="06513-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="06513-128">Ausführen des Identitätsgerüsts</span><span class="sxs-lookup"><span data-stu-id="06513-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06513-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06513-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="06513-130">Im **Projektmappen-Explorer**klicken Sie mit der rechten Maustaste auf das Projekt > **Neues** > **Gerüstelement**hinzufügen .</span><span class="sxs-lookup"><span data-stu-id="06513-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="06513-131">Wählen Sie im linken Bereich des Dialogfelds **Gerüst hinzufügen** die Option **Identität** > **hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="06513-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="06513-132">Im Dialogfeld **Identität hinzufügen** die folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="06513-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="06513-133">Wählen Sie die vorhandene Layout-Datei *aus: /Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="06513-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="06513-134">Wählen Sie die folgenden zu überschreibenden Dateien aus:</span><span class="sxs-lookup"><span data-stu-id="06513-134">Select the following files to override:</span></span>
    * <span data-ttu-id="06513-135">**Konto/Register**</span><span class="sxs-lookup"><span data-stu-id="06513-135">**Account/Register**</span></span>
    * <span data-ttu-id="06513-136">**Konto/Verwaltung/Index**</span><span class="sxs-lookup"><span data-stu-id="06513-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="06513-137">Wählen **+** Sie die Schaltfläche aus, um eine neue **Data-Kontextklasse**zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="06513-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="06513-138">Akzeptieren Sie den Typ (**WebApp1.Models.WebApp1Context,** wenn das Projekt **WebApp1**heißt .</span><span class="sxs-lookup"><span data-stu-id="06513-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="06513-139">Wählen **+** Sie die Schaltfläche aus, um eine neue **Benutzerklasse**zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="06513-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="06513-140">Akzeptieren Sie den Typ (**WebApp1User,** wenn das Projekt den Namen **WebApp1**) > **Hinzufügen .**</span><span class="sxs-lookup"><span data-stu-id="06513-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="06513-141">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="06513-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="06513-142">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="06513-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="06513-143">Wenn Sie das ASP.NET Core-Gerüst noch nicht installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="06513-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="06513-144">Fügen Sie der Projektdatei (.csproj) einen Paketverweis zu [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="06513-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="06513-145">Führen Sie den folgenden Befehl im Projektverzeichnis aus:</span><span class="sxs-lookup"><span data-stu-id="06513-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="06513-146">Führen Sie den folgenden Befehl aus, um die Optionen für das Identitätsgerüst aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="06513-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="06513-147">Führen Sie im Projektordner das Identitätsgerüst aus:</span><span class="sxs-lookup"><span data-stu-id="06513-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="06513-148">Befolgen Sie die Anweisungen in [Migrationen, UseAuthentication und Layout,](xref:security/authentication/scaffold-identity#efm) um die folgenden Schritte auszuführen:</span><span class="sxs-lookup"><span data-stu-id="06513-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="06513-149">Erstellen Sie eine Migration, und aktualisieren Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="06513-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="06513-150">`UseAuthentication` wurde `Startup.Configure` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="06513-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="06513-151">Der `<partial name="_LoginPartial" />` Layoutdatei hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="06513-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="06513-152">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="06513-152">Test the app:</span></span>
  * <span data-ttu-id="06513-153">Registrieren eines Benutzers</span><span class="sxs-lookup"><span data-stu-id="06513-153">Register a user</span></span>
  * <span data-ttu-id="06513-154">Wählen Sie den neuen Benutzernamen (neben dem **Abmeldelink)** aus.</span><span class="sxs-lookup"><span data-stu-id="06513-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="06513-155">Möglicherweise müssen Sie das Fenster erweitern oder das Symbol der Navigationsleiste auswählen, um den Benutzernamen und andere Links anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="06513-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="06513-156">Wählen Sie die Registerkarte **Persönliche Daten** aus.</span><span class="sxs-lookup"><span data-stu-id="06513-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="06513-157">Wählen Sie die **Schaltfläche Herunterladen** aus und haben Sie die Datei *PersonalData.json* untersucht.</span><span class="sxs-lookup"><span data-stu-id="06513-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="06513-158">Testen Sie die Schaltfläche **Löschen,** mit der der angemeldete Benutzer gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="06513-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="06513-159">Hinzufügen benutzerdefinierter Benutzerdaten zur Identitätsdatenbank</span><span class="sxs-lookup"><span data-stu-id="06513-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="06513-160">Aktualisieren `IdentityUser` Sie die abgeleitete Klasse mit benutzerdefinierten Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="06513-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="06513-161">Wenn Sie das Projekt WebApp1 benannt haben, heißt die Datei *Areas/Identity/Data/WebApp1User.cs*.</span><span class="sxs-lookup"><span data-stu-id="06513-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="06513-162">Aktualisieren Sie die Datei mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="06513-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="06513-163">Eigenschaften mit dem [PersonalData-Attribut](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) sind:</span><span class="sxs-lookup"><span data-stu-id="06513-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="06513-164">Gelöscht, wenn die *Razor-Seite Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* aufruft. `UserManager.Delete`</span><span class="sxs-lookup"><span data-stu-id="06513-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="06513-165">In den heruntergeladenen Daten von der *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page enthalten.</span><span class="sxs-lookup"><span data-stu-id="06513-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="06513-166">Aktualisieren der Seite Konto/Verwalten/Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="06513-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="06513-167">Aktualisieren `InputModel` Sie die in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="06513-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="06513-168">Aktualisieren Sie die *Bereiche/Identität/Seiten/Konto/Verwalten/Index.cshtml* mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="06513-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="06513-169">Aktualisieren Sie die *Bereiche/Identität/Seiten/Konto/Verwalten/Index.cshtml* mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="06513-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="06513-170">Aktualisieren der Seite Konto/Register.cshtml</span><span class="sxs-lookup"><span data-stu-id="06513-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="06513-171">Aktualisieren `InputModel` Sie die in *Areas/Identity/Pages/Account/Register.cshtml.cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="06513-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="06513-172">Aktualisieren Sie die *Bereiche/Identität/Seiten/Konto/Register.cshtml* mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="06513-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="06513-173">Aktualisieren Sie die *Bereiche/Identität/Seiten/Konto/Register.cshtml* mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="06513-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="06513-174">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="06513-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="06513-175">Hinzufügen einer Migration für die benutzerdefinierten Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="06513-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="06513-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="06513-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="06513-177">In der Visual Studio **Package Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="06513-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="06513-178">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="06513-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="06513-179">Testen, Anzeigen, Herunterladen, Löschen benutzerdefinierter Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="06513-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="06513-180">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="06513-180">Test the app:</span></span>

* <span data-ttu-id="06513-181">Registrieren Sie einen neuen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="06513-181">Register a new user.</span></span>
* <span data-ttu-id="06513-182">Zeigen Sie die benutzerdefinierten Benutzerdaten auf der `/Identity/Account/Manage` Seite an.</span><span class="sxs-lookup"><span data-stu-id="06513-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="06513-183">Laden Sie die persönlichen Daten `/Identity/Account/Manage/PersonalData` der Benutzer herunter und zeigen Sie sie von der Seite an.</span><span class="sxs-lookup"><span data-stu-id="06513-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="06513-184">Hinzufügen von Ansprüchen zur Identität mithilfe von IUserClaimsPrincipalFactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="06513-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

<span data-ttu-id="06513-185">Mithilfe der `IUserClaimsPrincipalFactory<T>` Schnittstelle können ASP.NET Core Identity zusätzliche Ansprüche hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="06513-185">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="06513-186">Diese Klasse kann der App `Startup.ConfigureServices` in der Methode hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="06513-186">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="06513-187">Fügen Sie die benutzerdefinierte Implementierung der Klasse wie folgt hinzu:</span><span class="sxs-lookup"><span data-stu-id="06513-187">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="06513-188">Der Democode `ApplicationUser` verwendet die Klasse.</span><span class="sxs-lookup"><span data-stu-id="06513-188">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="06513-189">Diese Klasse `IsAdmin` fügt eine Eigenschaft hinzu, die zum Hinzufügen des zusätzlichen Anspruchs verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="06513-189">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="06513-190">Die `AdditionalUserClaimsPrincipalFactory` implementiert die `UserClaimsPrincipalFactory`-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="06513-190">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="06513-191">Ein neuer Rollenanspruch wird `ClaimsPrincipal`der hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="06513-191">A new role claim is added to the `ClaimsPrincipal`.</span></span>

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

<span data-ttu-id="06513-192">Der zusätzliche Anspruch kann dann in der App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="06513-192">The additional claim can then be used in the app.</span></span> <span data-ttu-id="06513-193">In einer Razor-Seite kann die `IAuthorizationService` Instanz verwendet werden, um auf den Anspruchswert zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="06513-193">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
