---
title: Hinzufügen, herunterladen und Löschen von Identity Benutzerdaten in einem ASP.net Core Projekt
author: rick-anderson
description: Erfahren Sie, wie Sie Identity in einem ASP.net Core Projekt benutzerdefinierte Benutzerdaten hinzufügen. Löschen von Daten pro dsgvo.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/add-user-data
ms.openlocfilehash: 29c23e10d11eb1042b64fc071c221a9ead857fcc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777331"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a><span data-ttu-id="2cc7a-104">Hinzufügen, herunterladen und Löschen von benutzerdefinierten Benutzerdaten in einem ASP.net Core Projekt</span><span class="sxs-lookup"><span data-stu-id="2cc7a-104">Add, download, and delete custom user data to Identity in an ASP.NET Core project</span></span>

<span data-ttu-id="2cc7a-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2cc7a-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2cc7a-106">In diesem Artikel werden die folgenden Aufgaben erläutert:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-106">This article shows how to:</span></span>

* <span data-ttu-id="2cc7a-107">Fügen Sie benutzerdefinierte Benutzerdaten zu einer ASP.net Core-Web-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-107">Add custom user data to an ASP.NET Core web app.</span></span>
* <span data-ttu-id="2cc7a-108">Markieren Sie das benutzerdefinierte Benutzerdaten Modell <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> mit dem-Attribut, damit es automatisch heruntergeladen und gelöscht werden kann.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-108">Mark the custom user data model with the <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> attribute so it's automatically available for download and deletion.</span></span> <span data-ttu-id="2cc7a-109">Das herunterladen und Löschen der Daten hilft bei [der Einhaltung der](xref:security/gdpr) dsgvo-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-109">Making the data able to be downloaded and deleted helps meet [GDPR](xref:security/gdpr) requirements.</span></span>

<span data-ttu-id="2cc7a-110">Das Projektbeispiel wird aus einer Razor Pages-Web-App erstellt, die Anweisungen ähneln jedoch einer ASP.net Core MVC-Web-App.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-110">The project sample is created from a Razor Pages web app, but the instructions are similar for a ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="2cc7a-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2cc7a-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2cc7a-112">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="2cc7a-112">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a><span data-ttu-id="2cc7a-113">Erstellen einer Razor-Web-App</span><span class="sxs-lookup"><span data-stu-id="2cc7a-113">Create a Razor web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2cc7a-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2cc7a-114">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="2cc7a-115">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-115">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="2cc7a-116">Benennen Sie das Projekt **"WebApp1"** Wenn Sie den Namespace des [Download Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Codes anpassen möchten.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-116">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="2cc7a-117">**ASP.net Core Webanwendung** > **OK** auswählen</span><span class="sxs-lookup"><span data-stu-id="2cc7a-117">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="2cc7a-118">Wählen Sie in der Dropdown Liste **ASP.net Core 3,0** aus.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-118">Select **ASP.NET Core 3.0** in the dropdown</span></span>
* <span data-ttu-id="2cc7a-119">**Webanwendung** > auswählen **OK**</span><span class="sxs-lookup"><span data-stu-id="2cc7a-119">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="2cc7a-120">Erstellen Sie das Projekt, und führen Sie es aus.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-120">Build and run the project.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="2cc7a-121">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span> <span data-ttu-id="2cc7a-122">Benennen Sie das Projekt **"WebApp1"** Wenn Sie den Namespace des [Download Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) Codes anpassen möchten.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-122">Name the project **WebApp1** if you want to it match the namespace of the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) code.</span></span>
* <span data-ttu-id="2cc7a-123">**ASP.net Core Webanwendung** > **OK** auswählen</span><span class="sxs-lookup"><span data-stu-id="2cc7a-123">Select **ASP.NET Core Web Application** > **OK**</span></span>
* <span data-ttu-id="2cc7a-124">Wählen Sie in der Dropdown Liste **ASP.net Core 2,2** aus.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-124">Select **ASP.NET Core 2.2** in the dropdown</span></span>
* <span data-ttu-id="2cc7a-125">**Webanwendung** > auswählen **OK**</span><span class="sxs-lookup"><span data-stu-id="2cc7a-125">Select **Web Application** > **OK**</span></span>
* <span data-ttu-id="2cc7a-126">Erstellen Sie das Projekt, und führen Sie es aus.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-126">Build and run the project.</span></span>

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="2cc7a-127">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2cc7a-127">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a><span data-ttu-id="2cc7a-128">Ausführen des Identitäts gerüstems</span><span class="sxs-lookup"><span data-stu-id="2cc7a-128">Run the Identity scaffolder</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2cc7a-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2cc7a-129">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2cc7a-130">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf das Projekt, >**Neues Gerüst Element** **Hinzufügen** > .</span><span class="sxs-lookup"><span data-stu-id="2cc7a-130">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="2cc7a-131">Wählen Sie im linken Bereich des Dialog Felds **Gerüst hinzufügen** die Option **Identität** > **Hinzufügen**aus.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-131">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="2cc7a-132">Im Dialogfeld **Identität hinzufügen** werden die folgenden Optionen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-132">In the **Add Identity** dialog, the following options:</span></span>
  * <span data-ttu-id="2cc7a-133">Wählen Sie die vorhandene Layoutdatei aus *~/pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="2cc7a-133">Select the existing layout  file  *~/Pages/Shared/_Layout.cshtml*</span></span>
  * <span data-ttu-id="2cc7a-134">Wählen Sie die folgenden zu über schreibenden Dateien aus:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-134">Select the following files to override:</span></span>
    * <span data-ttu-id="2cc7a-135">**Konto/Registrierung**</span><span class="sxs-lookup"><span data-stu-id="2cc7a-135">**Account/Register**</span></span>
    * <span data-ttu-id="2cc7a-136">**Konto/Verwaltung/Index**</span><span class="sxs-lookup"><span data-stu-id="2cc7a-136">**Account/Manage/Index**</span></span>
  * <span data-ttu-id="2cc7a-137">Wählen Sie **+** die Schaltfläche aus, um eine neue **Datenkontext Klasse**zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-137">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="2cc7a-138">Akzeptieren Sie den Typ (**"WebApp1". Models. WebApp1Context** , wenn das Projekt den Namen **"WebApp1"** hat).</span><span class="sxs-lookup"><span data-stu-id="2cc7a-138">Accept the type (**WebApp1.Models.WebApp1Context** if the project is named **WebApp1**).</span></span>
  * <span data-ttu-id="2cc7a-139">Wählen Sie **+** die Schaltfläche aus, um eine neue **Benutzerklasse**zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-139">Select the **+** button to create a new **User class**.</span></span> <span data-ttu-id="2cc7a-140">Akzeptieren Sie den Typ (**WebApp1User** , wenn das Projekt den Namen **"WebApp1"** hat), > **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-140">Accept the type (**WebApp1User** if the project is named **WebApp1**) > **Add**.</span></span>
* <span data-ttu-id="2cc7a-141">Wählen Sie **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-141">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2cc7a-142">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2cc7a-142">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2cc7a-143">Wenn Sie das ASP.net Core Gerüst nicht bereits installiert haben, installieren Sie es jetzt:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-143">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="2cc7a-144">Fügen Sie der Projektdatei (. csproj) einen Paket Verweis auf [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-144">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project (.csproj) file.</span></span> <span data-ttu-id="2cc7a-145">Führen Sie den folgenden Befehl im Projektverzeichnis aus:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-145">Run the following command in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="2cc7a-146">Führen Sie den folgenden Befehl aus, um die Einstellungen für das Identitäts Gerüst aufzulisten:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-146">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="2cc7a-147">Führen Sie im Projektordner das Identitäts Gerüst aus:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-147">In the project folder, run the Identity scaffolder:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

<span data-ttu-id="2cc7a-148">Befolgen Sie die Anweisungen in [Migrationen, UseAuthentication und Layout](xref:security/authentication/scaffold-identity#efm) , um die folgenden Schritte auszuführen:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-148">Follow the instruction in [Migrations, UseAuthentication, and layout](xref:security/authentication/scaffold-identity#efm) to perform the following steps:</span></span>

* <span data-ttu-id="2cc7a-149">Erstellen Sie eine Migration, und aktualisieren Sie die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-149">Create a migration and update the database.</span></span>
* <span data-ttu-id="2cc7a-150">Fügen Sie `UseAuthentication` zu `Startup.Configure` hinzu.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-150">Add `UseAuthentication` to `Startup.Configure`.</span></span>
* <span data-ttu-id="2cc7a-151">Fügen `<partial name="_LoginPartial" />` Sie der Layoutdatei hinzu.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-151">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>
* <span data-ttu-id="2cc7a-152">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-152">Test the app:</span></span>
  * <span data-ttu-id="2cc7a-153">Registrieren eines Benutzers</span><span class="sxs-lookup"><span data-stu-id="2cc7a-153">Register a user</span></span>
  * <span data-ttu-id="2cc7a-154">Wählen Sie den neuen Benutzernamen (neben dem Link **Logout) aus** .</span><span class="sxs-lookup"><span data-stu-id="2cc7a-154">Select the new user name (next to the **Logout** link).</span></span> <span data-ttu-id="2cc7a-155">Möglicherweise müssen Sie das Fenster erweitern oder das Navigationsleisten Symbol auswählen, um den Benutzernamen und andere Links anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-155">You might need to expand the window or select the navigation bar icon to show the user name and other links.</span></span>
  * <span data-ttu-id="2cc7a-156">Wählen Sie die Registerkarte **persönliche Daten** aus.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-156">Select the **Personal Data** tab.</span></span>
  * <span data-ttu-id="2cc7a-157">Wählen Sie die Schaltfläche **herunterladen** aus, und untersuchen Sie die Datei " *Personal*</span><span class="sxs-lookup"><span data-stu-id="2cc7a-157">Select the **Download** button and examined the *PersonalData.json* file.</span></span>
  * <span data-ttu-id="2cc7a-158">Testen Sie die Schaltfläche **Löschen** , mit der der angemeldete Benutzer gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-158">Test the **Delete** button, which deletes the logged on user.</span></span>

## <a name="add-custom-user-data-to-the-identity-db"></a><span data-ttu-id="2cc7a-159">Hinzufügen von benutzerdefinierten Benutzerdaten zur Identity DB</span><span class="sxs-lookup"><span data-stu-id="2cc7a-159">Add custom user data to the Identity DB</span></span>

<span data-ttu-id="2cc7a-160">Aktualisieren Sie `IdentityUser` die abgeleitete Klasse mit benutzerdefinierten Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-160">Update the `IdentityUser` derived class with custom properties.</span></span> <span data-ttu-id="2cc7a-161">Wenn Sie das Projekt "WebApp1" benannt haben, heißt die Datei " *Areas/Identity/Data/WebApp1User. cs*".</span><span class="sxs-lookup"><span data-stu-id="2cc7a-161">If you named the project WebApp1, the file is named *Areas/Identity/Data/WebApp1User.cs*.</span></span> <span data-ttu-id="2cc7a-162">Aktualisieren Sie die Datei mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-162">Update the file with the following code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

<span data-ttu-id="2cc7a-163">Eigenschaften mit dem Attribut " [Personal Data](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) ":</span><span class="sxs-lookup"><span data-stu-id="2cc7a-163">Properties with the [PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) attribute are:</span></span>

* <span data-ttu-id="2cc7a-164">Wird gelöscht, wenn die Razor page " *Areas/Identity/Pages/Account/Manage/deletepersonal Data. cshtml* " aufruft `UserManager.Delete`.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-164">Deleted when the *Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor Page calls `UserManager.Delete`.</span></span>
* <span data-ttu-id="2cc7a-165">In den heruntergeladenen Daten nach der Razor page " *Bereiche/Identität/Seiten/Konto/verwalten/downloadpersonal Data. cshtml* " enthalten.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-165">Included in the downloaded data by the *Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor Page.</span></span>

### <a name="update-the-accountmanageindexcshtml-page"></a><span data-ttu-id="2cc7a-166">Aktualisieren Sie die Seite "Account/Manage/Index. cshtml".</span><span class="sxs-lookup"><span data-stu-id="2cc7a-166">Update the Account/Manage/Index.cshtml page</span></span>

<span data-ttu-id="2cc7a-167">Aktualisieren Sie `InputModel` das in *Areas/Identity/Pages/Account/Manage/Index. cshtml. cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-167">Update the `InputModel` in *Areas/Identity/Pages/Account/Manage/Index.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

<span data-ttu-id="2cc7a-168">Aktualisieren Sie die *Bereiche "Areas/Identity/Pages/Account/Manage/Index. cshtml* " mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-168">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

<span data-ttu-id="2cc7a-169">Aktualisieren Sie die *Bereiche "Areas/Identity/Pages/Account/Manage/Index. cshtml* " mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-169">Update the *Areas/Identity/Pages/Account/Manage/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a><span data-ttu-id="2cc7a-170">Aktualisieren Sie die Seite "Account/Register. cshtml".</span><span class="sxs-lookup"><span data-stu-id="2cc7a-170">Update the Account/Register.cshtml page</span></span>

<span data-ttu-id="2cc7a-171">Aktualisieren Sie `InputModel` das in *Areas/Identity/Pages/Account/Register. cshtml. cs* mit dem folgenden hervorgehobenen Code:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-171">Update the `InputModel` in *Areas/Identity/Pages/Account/Register.cshtml.cs* with the following highlighted code:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

<span data-ttu-id="2cc7a-172">Aktualisieren Sie die *Bereiche "Areas/Identity/Pages/Account/Register. cshtml* " mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-172">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

<span data-ttu-id="2cc7a-173">Aktualisieren Sie die *Bereiche "Areas/Identity/Pages/Account/Register. cshtml* " mit dem folgenden markierten Markup:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-173">Update the *Areas/Identity/Pages/Account/Register.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


<span data-ttu-id="2cc7a-174">Erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-174">Build the project.</span></span>

### <a name="add-a-migration-for-the-custom-user-data"></a><span data-ttu-id="2cc7a-175">Hinzufügen einer Migration für die benutzerdefinierten Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="2cc7a-175">Add a migration for the custom user data</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2cc7a-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2cc7a-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2cc7a-177">In der Visual Studio- **Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-177">In the Visual Studio **Package Manager Console**:</span></span>

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="2cc7a-178">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2cc7a-178">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a><span data-ttu-id="2cc7a-179">Test erstellen, anzeigen, herunterladen, benutzerdefinierte Benutzerdaten löschen</span><span class="sxs-lookup"><span data-stu-id="2cc7a-179">Test create, view, download, delete custom user data</span></span>

<span data-ttu-id="2cc7a-180">Testen der App:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-180">Test the app:</span></span>

* <span data-ttu-id="2cc7a-181">Registrieren Sie einen neuen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-181">Register a new user.</span></span>
* <span data-ttu-id="2cc7a-182">Zeigen Sie die benutzerdefinierten Benutzerdaten `/Identity/Account/Manage` auf der Seite an.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-182">View the custom user data on the `/Identity/Account/Manage` page.</span></span>
* <span data-ttu-id="2cc7a-183">Herunterladen und Anzeigen der persönlichen Daten der Benutzer `/Identity/Account/Manage/PersonalData` auf der Seite.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-183">Download and view the users personal data from the `/Identity/Account/Manage/PersonalData` page.</span></span>

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a><span data-ttu-id="2cc7a-184">Hinzufügen von Identity Ansprüchen zu mithilfe von iuserclaimsprincipalfactory<ApplicationUser></span><span class="sxs-lookup"><span data-stu-id="2cc7a-184">Add claims to Identity using IUserClaimsPrincipalFactory<ApplicationUser></span></span>

<span data-ttu-id="2cc7a-185">Zusätzliche Ansprüche können ASP.net Core Identity mithilfe der `IUserClaimsPrincipalFactory<T>` -Schnittstelle hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-185">Additional claims can be added to ASP.NET Core Identity by using the `IUserClaimsPrincipalFactory<T>` interface.</span></span> <span data-ttu-id="2cc7a-186">Diese Klasse kann der app in der `Startup.ConfigureServices` -Methode hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-186">This class can be added to the app in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2cc7a-187">Fügen Sie die benutzerdefinierte Implementierung der-Klasse wie folgt hinzu:</span><span class="sxs-lookup"><span data-stu-id="2cc7a-187">Add the custom implementation of the class as follows:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

<span data-ttu-id="2cc7a-188">Der Democode verwendet die `ApplicationUser` -Klasse.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-188">The demo code uses the `ApplicationUser` class.</span></span> <span data-ttu-id="2cc7a-189">Diese Klasse fügt eine `IsAdmin` Eigenschaft hinzu, die zum Hinzufügen des zusätzlichen Anspruchs verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-189">This class adds an `IsAdmin` property which is used to add the additional claim.</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

<span data-ttu-id="2cc7a-190">Die `AdditionalUserClaimsPrincipalFactory` implementiert die `UserClaimsPrincipalFactory`-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-190">The `AdditionalUserClaimsPrincipalFactory` implements the `UserClaimsPrincipalFactory` interface.</span></span> <span data-ttu-id="2cc7a-191">Dem `ClaimsPrincipal`wird ein neuer Rollen Anspruch hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-191">A new role claim is added to the `ClaimsPrincipal`.</span></span>

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

<span data-ttu-id="2cc7a-192">Der zusätzliche Anspruch kann dann in der APP verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-192">The additional claim can then be used in the app.</span></span> <span data-ttu-id="2cc7a-193">Auf einer Razor Seite kann die `IAuthorizationService` Instanz verwendet werden, um auf den Anspruchs Wert zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="2cc7a-193">In a Razor Page, the `IAuthorizationService` instance can be used to access the claim value.</span></span>

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
