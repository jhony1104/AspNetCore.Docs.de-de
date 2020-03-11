---
title: Hinzufügen, herunterladen und Löschen von Benutzerdaten auf Identität in einem ASP.NET Core-Projekt
author: rick-anderson
description: Erfahren Sie, wie benutzerdefinierte Benutzerdaten Identität in einem ASP.NET Core-Projekt hinzugefügt. Löschen von Daten pro DSGVO.
ms.author: riande
ms.date: 01/28/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 7a67f55da0e685ed3fd5badb30e8be683411a5ae
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653281"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="57017-104">Hinzufügen, herunterladen und Löschen von benutzerdefinierten Daten die Identität in einem ASP.NET Core-Projekt</span><span class="sxs-lookup"><span data-stu-id="57017-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="57017-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="57017-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="57017-106">In diesem Artikel werden die folgenden Aufgaben erläutert:</span><span class="sxs-lookup"><span data-stu-id="57017-106">This article shows how to:</span></span>

* <span data-ttu-id="57017-107">Fügen Sie benutzerdefinierter Benutzerdaten in einer ASP.NET Core-Web-app hinzu.</span><span class="sxs-lookup"><span data-stu-id="57017-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="57017-108">Markieren Sie das benutzerdefinierte Benutzerdaten Modell mit dem <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute>-Attribut, damit es automatisch heruntergeladen und gelöscht werden kann.</span><span class="sxs-lookup"><span data-stu-id="57017-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="57017-109">Das herunterladen und Löschen der Daten hilft bei [der Einhaltung der](xref:security/gdpr) dsgvo-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="57017-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="57017-110">Die Project-Beispiels aus einer Razor Pages-Web-app erstellt wird, aber die Anweisungen ähneln denen für eine ASP.NET Core MVC-Web-app.</span><span class="sxs-lookup"><span data-stu-id="57017-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="57017-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="57017-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="57017-112">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="57017-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="57017-113">Erstellen einer Razor-Web-App</span><span class="sxs-lookup"><span data-stu-id="57017-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57017-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57017-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="57017-115">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="57017-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="57017-116">Benennen Sie das Projekt **"WebApp1"** Wenn Sie den Namespace des [Download Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Codes anpassen möchten.</span><span class="sxs-lookup"><span data-stu-id="57017-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="57017-117">Wählen Sie **ASP.net Core Webanwendung** > **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="57017-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="57017-118">Wählen Sie in der Dropdown Liste **ASP.net Core 3,0** aus.</span><span class="sxs-lookup"><span data-stu-id="57017-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="57017-119">**Webanwendung** auswählen > **OK**</span><span class="sxs-lookup"><span data-stu-id="57017-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="57017-120">Erstellen Sie das Projekt, und führen Sie es aus.</span><span class="sxs-lookup"><span data-stu-id="57017-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="57017-121">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="57017-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="57017-122">Benennen Sie das Projekt **"WebApp1"** Wenn Sie den Namespace des [Download Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Codes anpassen möchten.</span><span class="sxs-lookup"><span data-stu-id="57017-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="57017-123">Wählen Sie **ASP.net Core Webanwendung** > **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="57017-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="57017-124">Wählen Sie in der Dropdown Liste **ASP.net Core 2,2** aus.</span><span class="sxs-lookup"><span data-stu-id="57017-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="57017-125">**Webanwendung** auswählen > **OK**</span><span class="sxs-lookup"><span data-stu-id="57017-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="57017-126">Erstellen Sie das Projekt, und führen Sie es aus.</span><span class="sxs-lookup"><span data-stu-id="57017-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="57017-127">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="57017-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="57017-128">Führen Sie die Identity-gerüstbauer</span><span class="sxs-lookup"><span data-stu-id="57017-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57017-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57017-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="57017-130">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, > fügen Sie > **Neues Gerüst Element** **hinzu** .</span><span class="sxs-lookup"><span data-stu-id="57017-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="57017-131">Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identität** > **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="57017-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="57017-132">Im Dialogfeld **Identität hinzufügen** werden die folgenden Optionen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="57017-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="57017-133">Wählen Sie die vorhandene Layoutdatei aus *~/pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="57017-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="57017-134">Wählen Sie die folgenden Dateien überschreiben:</span><span class="sxs-lookup"><span data-stu-id="57017-134">Select the following files to override:</span></span>
    * <span data-ttu-id="57017-135">**Konto/Registrierung**</span><span class="sxs-lookup"><span data-stu-id="57017-135">**Account/Register**</span></span>
    * <span data-ttu-id="57017-136">**Konto/Verwaltung/Index**</span><span class="sxs-lookup"><span data-stu-id="57017-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="57017-137">Wählen Sie die Schaltfläche **+** aus, um eine neue **Datenkontext Klasse**zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="57017-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="57017-138">Akzeptieren Sie den Typ ( **"WebApp1". Models. WebApp1Context** , wenn das Projekt den Namen **"WebApp1"** hat).</span><span class="sxs-lookup"><span data-stu-id="57017-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="57017-139">Wählen Sie die Schaltfläche **+** aus, um eine neue **Benutzerklasse**zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="57017-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="57017-140">Akzeptieren Sie den Typ (**WebApp1User** , wenn das Projekt den Namen **"WebApp1"** hat), > **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="57017-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="57017-141">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="57017-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="57017-142">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="57017-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="57017-143">Wenn Sie die ASP.NET Core-gerüstbauer noch nicht installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="57017-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="57017-144">Fügen Sie der Projektdatei (. csproj) einen Paket Verweis auf [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="57017-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="57017-145">Führen Sie den folgenden Befehl im Verzeichnis Projekts ein:</span><span class="sxs-lookup"><span data-stu-id="57017-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="57017-146">Führen Sie den folgenden Befehl zum Auflisten von Optionen gerüstbauer Identität:</span><span class="sxs-lookup"><span data-stu-id="57017-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="57017-147">Führen Sie im Projektordner der Identity-gerüstbauer:</span><span class="sxs-lookup"><span data-stu-id="57017-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="57017-148">Befolgen Sie die Anweisungen in [Migrationen, UseAuthentication und Layout](xref:security/authentication/scaffold-identity#efm) , um die folgenden Schritte auszuführen:</span><span class="sxs-lookup"><span data-stu-id="57017-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="57017-149">Erstellen Sie eine Migration aus, und aktualisieren Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="57017-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="57017-150">`UseAuthentication` wurde `Startup.Configure` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="57017-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="57017-151">Fügen Sie `<partial name="_LoginPartial" />` der Layoutdatei hinzu.</span><span class="sxs-lookup"><span data-stu-id="57017-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="57017-152">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="57017-152">Test the app:</span></span>
  * <span data-ttu-id="57017-153">Registrieren eines Benutzers</span><span class="sxs-lookup"><span data-stu-id="57017-153">Register a user</span></span>
  * <span data-ttu-id="57017-154">Wählen Sie den neuen Benutzernamen (neben dem Link **Logout) aus** .</span><span class="sxs-lookup"><span data-stu-id="57017-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="57017-155">Möglicherweise müssen Sie das Fenster erweitern, oder wählen Sie das Navigationssymbol Leiste, um den Benutzernamen und anderen Links anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="57017-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="57017-156">Wählen Sie die Registerkarte **persönliche Daten** aus.</span><span class="sxs-lookup"><span data-stu-id="57017-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="57017-157">Wählen Sie die Schaltfläche **herunterladen** aus, und untersuchen Sie die Datei " *Personal*</span><span class="sxs-lookup"><span data-stu-id="57017-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="57017-158">Testen Sie die Schaltfläche **Löschen** , mit der der angemeldete Benutzer gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="57017-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="57017-159">Hinzufügen von benutzerdefinierten Benutzerdaten mit der Identity-DB</span><span class="sxs-lookup"><span data-stu-id="57017-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="57017-160">Aktualisieren Sie die `IdentityUser` abgeleitete Klasse mit benutzerdefinierten Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="57017-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="57017-161">Wenn Sie das Projekt "WebApp1" benannt haben, heißt die Datei " *Areas/Identity/Data/WebApp1User. cs*".</span><span class="sxs-lookup"><span data-stu-id="57017-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="57017-162">Aktualisieren Sie die Datei mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="57017-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="57017-163">Eigenschaften mit dem Attribut " [Personal Data](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) ":</span><span class="sxs-lookup"><span data-stu-id="57017-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="57017-164">Wird gelöscht, wenn die Razor page " *Areas/Identity/Pages/Account/Manage/deletepersonal Data. cshtml* " `UserManager.Delete`aufruft.</span><span class="sxs-lookup"><span data-stu-id="57017-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="57017-165">In den heruntergeladenen Daten nach der Razor page " *Bereiche/Identität/Seiten/Konto/verwalten/downloadpersonal Data. cshtml* " enthalten.</span><span class="sxs-lookup"><span data-stu-id="57017-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="57017-166">Aktualisieren Sie die Seite "Account/Manage/Index.cshtml"</span><span class="sxs-lookup"><span data-stu-id="57017-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="57017-167">Aktualisieren Sie die `InputModel` in " *Bereiche/Identität/Pages/Account/Manage/Index. cshtml. cs* " mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="57017-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="57017-168">Aktualisieren Sie die *Bereiche "Areas/Identity/Pages/Account/Manage/Index. cshtml* " mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="57017-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="57017-169">Aktualisieren Sie die *Bereiche "Areas/Identity/Pages/Account/Manage/Index. cshtml* " mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="57017-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="57017-170">Aktualisiert die Account/Register.cshtml-Seite</span><span class="sxs-lookup"><span data-stu-id="57017-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="57017-171">Aktualisieren Sie die `InputModel` in " *Bereiche/Identität/Pages/Account/Register. cshtml. cs* " mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="57017-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="57017-172">Aktualisieren Sie die *Bereiche "Areas/Identity/Pages/Account/Register. cshtml* " mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="57017-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="57017-173">Aktualisieren Sie die *Bereiche "Areas/Identity/Pages/Account/Register. cshtml* " mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="57017-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="57017-174">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="57017-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="57017-175">Fügen Sie eine Migration für die benutzerdefinierten Daten hinzu</span><span class="sxs-lookup"><span data-stu-id="57017-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="57017-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="57017-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="57017-177">In der Visual Studio- **Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="57017-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="57017-178">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="57017-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="57017-179">Test erstellen, anzeigen, herunterladen und Löschen von benutzerdefinierten Daten</span><span class="sxs-lookup"><span data-stu-id="57017-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="57017-180">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="57017-180">Test the app:</span></span>

* <span data-ttu-id="57017-181">Registrieren Sie einen neuen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="57017-181">Register a new user.</span></span>
* <span data-ttu-id="57017-182">Zeigen Sie die benutzerdefinierten Benutzerdaten auf der Seite `/Identity/Account/Manage` an.</span><span class="sxs-lookup"><span data-stu-id="57017-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="57017-183">Laden Sie die personenbezogenen Benutzerdaten auf der Seite `/Identity/Account/Manage/PersonalData` herunter, und zeigen Sie Sie an</span><span class="sxs-lookup"><span data-stu-id="57017-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>
