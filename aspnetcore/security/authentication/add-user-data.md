---
title: Hinzufügen, herunterladen und Löschen von Benutzerdaten auf Identität in einem ASP.NET Core-Projekt
author: rick-anderson
description: Erfahren Sie, wie benutzerdefinierte Benutzerdaten Identität in einem ASP.NET Core-Projekt hinzugefügt. Löschen von Daten pro DSGVO.
ms.author: riande
ms.date: 06/18/2019
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: f5a47ffd2e068414268ed9037d4376bfd21ba1bb
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080810"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="f9a0d-104">Hinzufügen, herunterladen und Löschen von benutzerdefinierten Daten die Identität in einem ASP.NET Core-Projekt</span><span class="sxs-lookup"><span data-stu-id="f9a0d-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="f9a0d-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f9a0d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f9a0d-106">In diesem Artikel zeigt, wie Sie:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-106">This article shows how to:</span></span>

* <span data-ttu-id="f9a0d-107">Fügen Sie benutzerdefinierter Benutzerdaten in einer ASP.NET Core-Web-app hinzu.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="f9a0d-108">Ergänzen Sie das benutzerdefinierte Benutzerdaten Modell <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> mit dem-Attribut, damit es automatisch heruntergeladen und gelöscht werden kann.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-108">Decorate the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="f9a0d-109">Sodass die Daten, die heruntergeladen und gelöscht werden kann hilft bei der Erfüllung [DSGVO](xref:security/gdpr) Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="f9a0d-110">Die Project-Beispiels aus einer Razor Pages-Web-app erstellt wird, aber die Anweisungen ähneln denen für eine ASP.NET Core MVC-Web-app.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="f9a0d-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9a0d-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f9a0d-112">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f9a0d-112">Prerequisites</span></span>

[!INCLUDE [](~/includes/2.2-SDK.md)]

## <a name="create-a-razor-web-app"></a><span data-ttu-id="f9a0d-113">Erstellen einer Razor-Web-App</span><span class="sxs-lookup"><span data-stu-id="f9a0d-113">Create a Razor web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f9a0d-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9a0d-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f9a0d-115">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="f9a0d-116">Nennen Sie das Projekt **"WebApp1"** Wenn Sie möchten einen übereinstimmenden Namespace die [Beispiel herunterladen](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Code.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="f9a0d-117">Wählen Sie **ASP.NET Core-Webanwendung** > **OK**</span><span class="sxs-lookup"><span data-stu-id="f9a0d-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="f9a0d-118">Wählen Sie in der Dropdown Liste **ASP.net Core 2,2** aus.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-118">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="f9a0d-119">Wählen Sie **Webanwendung**  > **OK**</span><span class="sxs-lookup"><span data-stu-id="f9a0d-119">Select **Web Application**  > **OK**</span></span>
* <span data-ttu-id="f9a0d-120">Erstellen Sie das Projekt, und führen Sie es aus.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-120">Build and run the project.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f9a0d-121">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="f9a0d-121">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="f9a0d-122">Führen Sie die Identity-gerüstbauer</span><span class="sxs-lookup"><span data-stu-id="f9a0d-122">Run the Identity scaffolder</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f9a0d-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9a0d-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f9a0d-124">Von **Projektmappen-Explorer**, mit der rechten Maustaste auf das Projekt > **hinzufügen** > **neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-124">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="f9a0d-125">Im linken Bereich, der die **Gerüst hinzufügen** wählen Sie im Dialogfeld **Identität** > **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-125">From the left pane of the **Add Scaffold** dialog, select **Identity** > **ADD**.</span></span>
* <span data-ttu-id="f9a0d-126">In der **ADD Identity** Dialog, der die folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-126">In the **ADD Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="f9a0d-127">Wählen Sie die vorhandenen Layoutdatei *~/Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="f9a0d-127">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="f9a0d-128">Wählen Sie die folgenden Dateien überschreiben:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-128">Select the following files to override:</span></span>
    * <span data-ttu-id="f9a0d-129">**Konto/registrieren**</span><span class="sxs-lookup"><span data-stu-id="f9a0d-129">**Account/Register**</span></span>
    * <span data-ttu-id="f9a0d-130">**Konto / / Index verwalten**</span><span class="sxs-lookup"><span data-stu-id="f9a0d-130">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="f9a0d-131">Wählen Sie die **+** Schaltfläche zum Erstellen eines neuen **Datenkontextklasse**.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-131">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="f9a0d-132">Akzeptieren Sie den Typ (**WebApp1.Models.WebApp1Context** Wenn das Projekt den Namen **"WebApp1"** ).</span><span class="sxs-lookup"><span data-stu-id="f9a0d-132">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="f9a0d-133">Wählen Sie die **+** Schaltfläche zum Erstellen eines neuen **Benutzerklasse**.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-133">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="f9a0d-134">Akzeptieren Sie den Typ (**WebApp1User** Wenn das Projekt den Namen **"WebApp1"** ) > **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-134">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="f9a0d-135">Wählen Sie **hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-135">Select **ADD**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f9a0d-136">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="f9a0d-136">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f9a0d-137">Wenn Sie die ASP.NET Core-gerüstbauer noch nicht installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-137">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="f9a0d-138">Fügen Sie einen Paketverweis auf [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) zur Projektdatei (.csproj).</span><span class="sxs-lookup"><span data-stu-id="f9a0d-138">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="f9a0d-139">Führen Sie den folgenden Befehl im Verzeichnis Projekts ein:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-139">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="f9a0d-140">Führen Sie den folgenden Befehl zum Auflisten von Optionen gerüstbauer Identität:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-140">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="f9a0d-141">Führen Sie im Projektordner der Identity-gerüstbauer:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-141">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="f9a0d-142">Führen Sie die Anweisungen in [Migrationen, UseAuthentication und Layout](xref:security/authentication/scaffold-identity#efm) die folgenden Schritte ausführen:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-142">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="f9a0d-143">Erstellen Sie eine Migration aus, und aktualisieren Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-143">Create a migration and update the database.</span></span>
* <span data-ttu-id="f9a0d-144">Fügen Sie `UseAuthentication` zu `Startup.Configure` hinzu.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-144">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="f9a0d-145">Hinzufügen `<partial name="_LoginPartial" />` zur Layoutdatei.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-145">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="f9a0d-146">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-146">Test the app:</span></span>
  * <span data-ttu-id="f9a0d-147">Registrieren eines Benutzers</span><span class="sxs-lookup"><span data-stu-id="f9a0d-147">Register a user</span></span>
  * <span data-ttu-id="f9a0d-148">Wählen Sie den neuen Benutzernamen ein (neben der **Logout** Link).</span><span class="sxs-lookup"><span data-stu-id="f9a0d-148">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="f9a0d-149">Möglicherweise müssen Sie das Fenster erweitern, oder wählen Sie das Navigationssymbol Leiste, um den Benutzernamen und anderen Links anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-149">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="f9a0d-150">Wählen Sie die **personenbezogene Daten** Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-150">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="f9a0d-151">Wählen Sie die **herunterladen** Schaltfläche und untersucht die *PersonalData.json* Datei.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-151">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="f9a0d-152">Testen der **löschen** Schaltfläche, die der angemeldete Benutzer löscht.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-152">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="f9a0d-153">Hinzufügen von benutzerdefinierten Benutzerdaten mit der Identity-DB</span><span class="sxs-lookup"><span data-stu-id="f9a0d-153">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="f9a0d-154">Update der `IdentityUser` abgeleitete Klasse mit benutzerdefinierten Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-154">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="f9a0d-155">Wenn Sie das Projekt "WebApp1" genannt, kann die Datei heißt *Areas/Identity/Data/WebApp1User.cs*.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-155">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="f9a0d-156">Aktualisieren Sie die Datei mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-156">Update the file with the following code:</span></span>

[!code-csharp[Main](add-user-data/sample-2.2/Areas/Identity/Data/WebApp1User.cs)]

<span data-ttu-id="f9a0d-157">Eigenschaften mit ergänzt die [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute?view=aspnetcore-2.1) Attribut sind:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-157">Properties decorated with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute?view=aspnetcore-2.1) attribute are:</span></span>

* <span data-ttu-id="f9a0d-158">Gelöscht, wenn die *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor-Seite ruft `UserManager.Delete`.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-158">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="f9a0d-159">In der heruntergeladenen Daten enthalten die *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor-Seite.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-159">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="f9a0d-160">Aktualisieren Sie die Seite "Account/Manage/Index.cshtml"</span><span class="sxs-lookup"><span data-stu-id="f9a0d-160">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="f9a0d-161">Update der `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* mit folgendem hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-161">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

[!code-csharp[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="f9a0d-162">Update der *Areas/Identity/Pages/Account/Manage/Index.cshtml* mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-162">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-html[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="f9a0d-163">Aktualisiert die Account/Register.cshtml-Seite</span><span class="sxs-lookup"><span data-stu-id="f9a0d-163">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="f9a0d-164">Update der `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* mit folgendem hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-164">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

[!code-csharp[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="f9a0d-165">Update der *Areas/Identity/Pages/Account/Register.cshtml* mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-165">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-html[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

<span data-ttu-id="f9a0d-166">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-166">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="f9a0d-167">Fügen Sie eine Migration für die benutzerdefinierten Daten hinzu</span><span class="sxs-lookup"><span data-stu-id="f9a0d-167">Add a migration for the custom user data</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f9a0d-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9a0d-168">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f9a0d-169">In der Visual Studio **-Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-169">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f9a0d-170">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="f9a0d-170">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="f9a0d-171">Test erstellen, anzeigen, herunterladen und Löschen von benutzerdefinierten Daten</span><span class="sxs-lookup"><span data-stu-id="f9a0d-171">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="f9a0d-172">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="f9a0d-172">Test the app:</span></span>

* <span data-ttu-id="f9a0d-173">Registrieren Sie einen neuen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-173">Register a new user.</span></span>
* <span data-ttu-id="f9a0d-174">Zeigen Sie die benutzerdefinierten Daten, auf die `/Identity/Account/Manage` Seite.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-174">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="f9a0d-175">Herunterladen und Anzeigen der Benutzer persönliche Daten von der `/Identity/Account/Manage/PersonalData` Seite.</span><span class="sxs-lookup"><span data-stu-id="f9a0d-175">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>
