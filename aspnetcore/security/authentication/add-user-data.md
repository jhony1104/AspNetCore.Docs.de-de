---
title: Hinzufügen, herunterladen und Löschen von Benutzerdaten in einem ASP.net Core Projekt zur Identität
author: rick-anderson
description: Erfahren Sie, wie Sie benutzerdefinierte Benutzerdaten in einem ASP.net Core-Projekt zur Identität hinzufügen. Löschen von Daten pro dsgvo.
ms.author: riande
ms.date: 06/18/2019
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 6daca5776930f80eec8d81132b5a5c4d4d5c13ad
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681161"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="14d47-104">Hinzufügen, herunterladen und Löschen von benutzerdefinierten Benutzerdaten in einem ASP.net Core Projekt</span><span class="sxs-lookup"><span data-stu-id="14d47-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="14d47-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="14d47-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="14d47-106">In diesem Artikel wird Folgendes erläutert:</span><span class="sxs-lookup"><span data-stu-id="14d47-106">This article shows how to:</span></span>

* <span data-ttu-id="14d47-107">Fügen Sie benutzerdefinierte Benutzerdaten zu einer ASP.net Core-Web-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="14d47-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="14d47-108">Ergänzen Sie das benutzerdefinierte Benutzerdaten Modell mit dem <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute>-Attribut, damit es automatisch heruntergeladen und gelöscht werden kann.</span><span class="sxs-lookup"><span data-stu-id="14d47-108">Decorate the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="14d47-109">Das herunterladen und Löschen der Daten hilft bei [der Einhaltung der](xref:security/gdpr) dsgvo-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="14d47-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="14d47-110">Das Projektbeispiel wird aus einer Razor Pages-Web-App erstellt, die Anweisungen ähneln jedoch einer ASP.net Core MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="14d47-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="14d47-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="14d47-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="14d47-112">Erforderliche Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="14d47-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="14d47-113">Erstellen einer Razor-Web-App</span><span class="sxs-lookup"><span data-stu-id="14d47-113">Create a Razor web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14d47-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14d47-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="14d47-115">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="14d47-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="14d47-116">Benennen Sie das Projekt **"WebApp1"** Wenn Sie den Namespace des [Download Beispiel](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Codes anpassen möchten.</span><span class="sxs-lookup"><span data-stu-id="14d47-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="14d47-117">Wählen Sie **ASP.net Core Webanwendung** > **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="14d47-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="14d47-118">Wählen Sie in der Dropdown Liste **ASP.net Core 3,0** aus.</span><span class="sxs-lookup"><span data-stu-id="14d47-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="14d47-119">**Webanwendung** auswählen > **OK**</span><span class="sxs-lookup"><span data-stu-id="14d47-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="14d47-120">Erstellen Sie das Projekt, und führen Sie es aus.</span><span class="sxs-lookup"><span data-stu-id="14d47-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="14d47-121">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="14d47-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="14d47-122">Benennen Sie das Projekt **"WebApp1"** Wenn Sie den Namespace des [Download Beispiel](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Codes anpassen möchten.</span><span class="sxs-lookup"><span data-stu-id="14d47-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="14d47-123">Wählen Sie **ASP.net Core Webanwendung** > **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="14d47-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="14d47-124">Wählen Sie in der Dropdown Liste **ASP.net Core 2,2** aus.</span><span class="sxs-lookup"><span data-stu-id="14d47-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="14d47-125">**Webanwendung** auswählen > **OK**</span><span class="sxs-lookup"><span data-stu-id="14d47-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="14d47-126">Erstellen Sie das Projekt, und führen Sie es aus.</span><span class="sxs-lookup"><span data-stu-id="14d47-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="14d47-127">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="14d47-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="14d47-128">Ausführen des Identitäts gerüstems</span><span class="sxs-lookup"><span data-stu-id="14d47-128">Run the Identity scaffolder</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14d47-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14d47-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="14d47-130">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, > fügen Sie > **Neues Gerüst Element** **hinzu** .</span><span class="sxs-lookup"><span data-stu-id="14d47-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="14d47-131">Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identität** > **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="14d47-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="14d47-132">Im Dialogfeld **Identität hinzufügen** werden die folgenden Optionen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="14d47-132">In the **ADD Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="14d47-133">Wählen Sie die vorhandene Layoutdatei aus *~/pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="14d47-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="14d47-134">Wählen Sie die folgenden zu über schreibenden Dateien aus:</span><span class="sxs-lookup"><span data-stu-id="14d47-134">Select the following files to override:</span></span>
    * <span data-ttu-id="14d47-135">**Konto/Registrierung**</span><span class="sxs-lookup"><span data-stu-id="14d47-135">**Account/Register**</span></span>
    * <span data-ttu-id="14d47-136">**Konto/Verwaltung/Index**</span><span class="sxs-lookup"><span data-stu-id="14d47-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="14d47-137">Wählen Sie die Schaltfläche **+** aus, um eine neue **Datenkontext Klasse**zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="14d47-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="14d47-138">Akzeptieren Sie den Typ ( **"WebApp1". Models. WebApp1Context** , wenn das Projekt den Namen **"WebApp1"** hat).</span><span class="sxs-lookup"><span data-stu-id="14d47-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="14d47-139">Wählen Sie die Schaltfläche **+** aus, um eine neue **Benutzerklasse**zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="14d47-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="14d47-140">Akzeptieren Sie den Typ (**WebApp1User** , wenn das Projekt den Namen **"WebApp1"** hat), > **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="14d47-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="14d47-141">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="14d47-141">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="14d47-142">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="14d47-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="14d47-143">Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="14d47-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="14d47-144">Fügen Sie der Projektdatei (. csproj) einen Paket Verweis auf [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="14d47-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="14d47-145">Führen Sie den folgenden Befehl im Projektverzeichnis aus:</span><span class="sxs-lookup"><span data-stu-id="14d47-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="14d47-146">Führen Sie den folgenden Befehl aus, um die Einstellungen für das Identitäts Gerüst aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="14d47-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="14d47-147">Führen Sie im Projektordner das Identitäts Gerüst aus:</span><span class="sxs-lookup"><span data-stu-id="14d47-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="14d47-148">Befolgen Sie die Anweisungen in [Migrationen, UseAuthentication und Layout](xref:security/authentication/scaffold-identity#efm) , um die folgenden Schritte auszuführen:</span><span class="sxs-lookup"><span data-stu-id="14d47-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="14d47-149">Erstellen Sie eine Migration, und aktualisieren Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="14d47-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="14d47-150">Fügen Sie `UseAuthentication` zu `Startup.Configure` hinzu.</span><span class="sxs-lookup"><span data-stu-id="14d47-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="14d47-151">Fügen Sie `<partial name="_LoginPartial" />` der Layoutdatei hinzu.</span><span class="sxs-lookup"><span data-stu-id="14d47-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="14d47-152">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="14d47-152">Test the app:</span></span>
  * <span data-ttu-id="14d47-153">Registrieren eines Benutzers</span><span class="sxs-lookup"><span data-stu-id="14d47-153">Register a user</span></span>
  * <span data-ttu-id="14d47-154">Wählen Sie den neuen Benutzernamen (neben dem Link **Logout) aus** .</span><span class="sxs-lookup"><span data-stu-id="14d47-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="14d47-155">Möglicherweise müssen Sie das Fenster erweitern oder das Navigationsleisten Symbol auswählen, um den Benutzernamen und andere Links anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="14d47-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="14d47-156">Wählen Sie die Registerkarte **persönliche Daten** aus.</span><span class="sxs-lookup"><span data-stu-id="14d47-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="14d47-157">Wählen Sie die Schaltfläche **herunterladen** aus, und untersuchen Sie die Datei " *Personal*</span><span class="sxs-lookup"><span data-stu-id="14d47-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="14d47-158">Testen Sie die Schaltfläche **Löschen** , mit der der angemeldete Benutzer gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="14d47-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="14d47-159">Hinzufügen von benutzerdefinierten Benutzerdaten zur Identity DB</span><span class="sxs-lookup"><span data-stu-id="14d47-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="14d47-160">Aktualisieren Sie die `IdentityUser` abgeleitete Klasse mit benutzerdefinierten Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="14d47-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="14d47-161">Wenn Sie das Projekt "WebApp1" benannt haben, heißt die Datei " *Areas/Identity/Data/WebApp1User. cs*".</span><span class="sxs-lookup"><span data-stu-id="14d47-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="14d47-162">Aktualisieren Sie die Datei mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="14d47-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="14d47-163">Folgende Eigenschaften werden mit dem Attribut " [Personal Data](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) " ergänzt:</span><span class="sxs-lookup"><span data-stu-id="14d47-163">Properties decorated with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="14d47-164">Wird gelöscht, wenn die Razor page " *Areas/Identity/Pages/Account/Manage/deletepersonal Data. cshtml* " `UserManager.Delete`aufruft.</span><span class="sxs-lookup"><span data-stu-id="14d47-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="14d47-165">In den heruntergeladenen Daten nach der Razor page " *Bereiche/Identität/Seiten/Konto/verwalten/downloadpersonal Data. cshtml* " enthalten.</span><span class="sxs-lookup"><span data-stu-id="14d47-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="14d47-166">Aktualisieren Sie die Seite "Account/Manage/Index. cshtml".</span><span class="sxs-lookup"><span data-stu-id="14d47-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="14d47-167">Aktualisieren Sie die `InputModel` in " *Bereiche/Identität/Pages/Account/Manage/Index. cshtml. cs* " mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="14d47-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="14d47-168">Aktualisieren Sie die *Bereiche "Areas/Identity/Pages/Account/Manage/Index. cshtml* " mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="14d47-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="14d47-169">Aktualisieren Sie die *Bereiche "Areas/Identity/Pages/Account/Manage/Index. cshtml* " mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="14d47-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="14d47-170">Aktualisieren Sie die Seite "Account/Register. cshtml".</span><span class="sxs-lookup"><span data-stu-id="14d47-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="14d47-171">Aktualisieren Sie die `InputModel` in " *Bereiche/Identität/Pages/Account/Register. cshtml. cs* " mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="14d47-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="14d47-172">Aktualisieren Sie die *Bereiche "Areas/Identity/Pages/Account/Register. cshtml* " mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="14d47-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="14d47-173">Aktualisieren Sie die *Bereiche "Areas/Identity/Pages/Account/Register. cshtml* " mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="14d47-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="14d47-174">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="14d47-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="14d47-175">Hinzufügen einer Migration für die benutzerdefinierten Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="14d47-175">Add a migration for the custom user data</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="14d47-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="14d47-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="14d47-177">In der Visual Studio- **Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="14d47-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="14d47-178">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="14d47-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="14d47-179">Test erstellen, anzeigen, herunterladen, benutzerdefinierte Benutzerdaten löschen</span><span class="sxs-lookup"><span data-stu-id="14d47-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="14d47-180">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="14d47-180">Test the app:</span></span>

* <span data-ttu-id="14d47-181">Registrieren Sie einen neuen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="14d47-181">Register a new user.</span></span>
* <span data-ttu-id="14d47-182">Zeigen Sie die benutzerdefinierten Benutzerdaten auf der Seite `/Identity/Account/Manage` an.</span><span class="sxs-lookup"><span data-stu-id="14d47-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="14d47-183">Laden Sie die personenbezogenen Benutzerdaten auf der Seite `/Identity/Account/Manage/PersonalData` herunter, und zeigen Sie Sie an</span><span class="sxs-lookup"><span data-stu-id="14d47-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>
