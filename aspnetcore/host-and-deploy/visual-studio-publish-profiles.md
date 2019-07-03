---
title: Visual Studio-Veröffentlichungsprofile für die Bereitstellung von ASP.NET Core-Apps
author: rick-anderson
description: Informationen zum Erstellen von Veröffentlichungsprofilen in Visual Studio und zu deren Verwendung zum Verwalten von Bereitstellungen von ASP.NET Core-Apps an verschiedene Ziele.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/18/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: ac243a3898553b2e14a6c15d311afaf62f112a24
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207816"
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a><span data-ttu-id="51e59-103">Visual Studio-Veröffentlichungsprofile für die Bereitstellung von ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="51e59-103">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>

<span data-ttu-id="51e59-104">Von [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="51e59-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="51e59-105">Dieser Artikel konzertiert sich auf die Verwendung von Visual Studio 2017 oder höher zum Erstellen von Veröffentlichungsprofilen.</span><span class="sxs-lookup"><span data-stu-id="51e59-105">This document focuses on using Visual Studio 2017 or later to create and use publish profiles.</span></span> <span data-ttu-id="51e59-106">Die Veröffentlichungsprofile, die mit Visual Studio erstellt werden, können von MSBuild und Visual Studio ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="51e59-106">The publish profiles created with Visual Studio can be run from MSBuild and Visual Studio.</span></span> <span data-ttu-id="51e59-107">Anweisungen zum Veröffentlichen in Azure finden Sie unter [Veröffentlichen einer ASP.NET Core-Web-App in Azure App Service mit Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs).</span><span class="sxs-lookup"><span data-stu-id="51e59-107">See [Publish an ASP.NET Core web app to Azure App Service using Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) for instructions on publishing to Azure.</span></span>

<span data-ttu-id="51e59-108">Der Befehl `dotnet new mvc` erzeugt eine Projektdatei, die das folgende `<Project>`-Element auf der obersten Ebene enthält:</span><span class="sxs-lookup"><span data-stu-id="51e59-108">The `dotnet new mvc` command produces a project file containing the following top-level `<Project>` element:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="51e59-109">Das `Sdk`-Attribut des vorhergehenden `<Project>`-Elements importiert die [Eigenschaften](/visualstudio/msbuild/msbuild-properties) und [Ziele](/visualstudio/msbuild/msbuild-targets) von MSBuild aus *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.reps* bzw. *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk*.</span><span class="sxs-lookup"><span data-stu-id="51e59-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="51e59-110">Der Standardspeicherort für `$(MSBuildSDKsPath)` (mit Visual Studio 2019 Enterprise) ist der Ordner *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks*.</span><span class="sxs-lookup"><span data-stu-id="51e59-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="51e59-111">`Microsoft.NET.Sdk.Web` (Web SDK) hängt von anderen SDKs ab, einschließlich `Microsoft.NET.Sdk` (.NET Core SDK) und `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span><span class="sxs-lookup"><span data-stu-id="51e59-111">`Microsoft.NET.Sdk.Web` (Web SDK) depends on other SDKs, including `Microsoft.NET.Sdk` (.NET Core SDK) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="51e59-112">Die MSBuild-Eigenschaften und -Ziele, die jedem abhängigen SDK zugeordnet sind, werden importiert.</span><span class="sxs-lookup"><span data-stu-id="51e59-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="51e59-113">Veröffentlichungsziele importieren basierend auf der verwendeten Veröffentlichungsmethode die entsprechende Gruppe von Zielen.</span><span class="sxs-lookup"><span data-stu-id="51e59-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="51e59-114">Wenn in MSBuild oder Visual Studio ein Projekt geladen wird, werden die folgenden Aktionen auf hoher Ebene ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="51e59-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="51e59-115">Erstellen des Projekts</span><span class="sxs-lookup"><span data-stu-id="51e59-115">Build project</span></span>
* <span data-ttu-id="51e59-116">Berechnen der zu veröffentlichenden Dateien</span><span class="sxs-lookup"><span data-stu-id="51e59-116">Compute files to publish</span></span>
* <span data-ttu-id="51e59-117">Veröffentlichen der Dateien auf dem Ziel</span><span class="sxs-lookup"><span data-stu-id="51e59-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="51e59-118">Berechnen der Projektelemente</span><span class="sxs-lookup"><span data-stu-id="51e59-118">Compute project items</span></span>

<span data-ttu-id="51e59-119">Wenn das Projekt geladen ist, werden die [MSBuild-Projektelemente](/visualstudio/msbuild/common-msbuild-project-items) (Dateien) berechnet.</span><span class="sxs-lookup"><span data-stu-id="51e59-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="51e59-120">Der Elementtyp bestimmt, wie die Datei verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="51e59-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="51e59-121">Standardmäßig sind *CS*-Dateien in der `Compile`-Elementliste enthalten.</span><span class="sxs-lookup"><span data-stu-id="51e59-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="51e59-122">Dateien in der `Compile`-Elementliste werden kompiliert.</span><span class="sxs-lookup"><span data-stu-id="51e59-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="51e59-123">Die `Content`-Elementliste enthält Dateien, die zusätzlich zu den Buildausgaben veröffentlicht werden.</span><span class="sxs-lookup"><span data-stu-id="51e59-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="51e59-124">Standardmäßig sind Dateien, die mit den Mustern `wwwroot\**`, `**\*.config` und `**\*.json` übereinstimmen, in der Elementliste `Content` enthalten.</span><span class="sxs-lookup"><span data-stu-id="51e59-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="51e59-125">Das `wwwroot\**` [Globmuster](https://gruntjs.com/configuring-tasks#globbing-patterns) stimmt z.B. mit allen Dateien im *wwwroot*-Ordner **und**  in dessen Unterordnern überein.</span><span class="sxs-lookup"><span data-stu-id="51e59-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder **and** its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="51e59-126">Das Web SDK importiert das [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="51e59-126">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="51e59-127">Demzufolge sind Dateien, die mit den Mustern `**\*.cshtml` und `**\*.razor` übereinstimmen, in der Elementliste `Content` enthalten.</span><span class="sxs-lookup"><span data-stu-id="51e59-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="51e59-128">Das Web SDK importiert das [Razor SDK](xref:razor-pages/sdk).</span><span class="sxs-lookup"><span data-stu-id="51e59-128">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="51e59-129">Demzufolge sind Dateien, die mit dem Muster `**\*.cshtml` übereinstimmen, in der Elementliste `Content` enthalten.</span><span class="sxs-lookup"><span data-stu-id="51e59-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="51e59-130">Um eine Datei explizit der Veröffentlichungsliste hinzuzufügen, fügen Sie die Datei, wie im Abschnitt [Includedateien](#include-files) gezeigt, direkt der *CSPROJ*-Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="51e59-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="51e59-131">Wenn Sie auf die Schaltfläche **Veröffentlichen** in Visual Studio klicken oder aus der Befehlszeile veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="51e59-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="51e59-132">Die Eigenschaften und Elemente werden berechnet (die Dateien, die für den Build benötigt werden).</span><span class="sxs-lookup"><span data-stu-id="51e59-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="51e59-133">**Nur Visual Studio**: NuGet-Pakete werden wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="51e59-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="51e59-134">(Die Wiederherstellung muss explizit vom Benutzer auf der CLI durchgeführt werden.)</span><span class="sxs-lookup"><span data-stu-id="51e59-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="51e59-135">Das Projekt wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="51e59-135">The project builds.</span></span>
* <span data-ttu-id="51e59-136">Die zu veröffentlichenden Elemente werden berechnet (die Dateien, die für die Veröffentlichung benötigt werden).</span><span class="sxs-lookup"><span data-stu-id="51e59-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="51e59-137">Das Projekt wird veröffentlicht (die berechneten Dateien werden in das Veröffentlichungsziel kopiert).</span><span class="sxs-lookup"><span data-stu-id="51e59-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="51e59-138">Wenn ein ASP.NET Core-Projekt auf `Microsoft.NET.Sdk.Web` in der Projektdatei verweist, wird eine *app_offline.htm*-Datei im Stammverzeichnis des Verzeichnisses der Web-App abgelegt.</span><span class="sxs-lookup"><span data-stu-id="51e59-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="51e59-139">Wenn die Datei vorhanden ist, fährt das ASP.NET Core Module die App ordnungsgemäß herunter und verarbeitet die Datei *app_offline.htm* während der Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="51e59-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="51e59-140">Weitere Informationen finden Sie unter [Konfigurationsreferenz für das ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span><span class="sxs-lookup"><span data-stu-id="51e59-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="51e59-141">Grundlegendes zur Veröffentlichung über die Befehlszeile</span><span class="sxs-lookup"><span data-stu-id="51e59-141">Basic command-line publishing</span></span>

<span data-ttu-id="51e59-142">Die Veröffentlichung über die Befehlszeile funktioniert auf allen von .NET Core unterstützten Plattformen und setzt Visual Studio nicht voraus.</span><span class="sxs-lookup"><span data-stu-id="51e59-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="51e59-143">In den folgenden Beispielen wird der Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) im Projektverzeichnis ausgeführt (das die Datei *CSPROJ* enthält).</span><span class="sxs-lookup"><span data-stu-id="51e59-143">In the following samples, the [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="51e59-144">Wenn Sie nicht im Projektordner sind, können Sie explizit in den Projektdateipfad übergeben.</span><span class="sxs-lookup"><span data-stu-id="51e59-144">If not in the project folder, explicitly pass in the project file path.</span></span> <span data-ttu-id="51e59-145">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="51e59-145">For example:</span></span>

```console
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="51e59-146">Führen Sie die folgenden Befehle zum Erstellen und Veröffentlichen eine Web-App aus:</span><span class="sxs-lookup"><span data-stu-id="51e59-146">Run the following commands to create and publish a web app:</span></span>

```console
dotnet new mvc
dotnet publish
```

<span data-ttu-id="51e59-147">Der Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) erzeugt eine Ausgabe, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="51e59-147">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command produces output similar to the following:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {version} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\publish\
```

<span data-ttu-id="51e59-148">Der Standardveröffentlichungsordner ist `bin\$(Configuration)\netcoreapp<version>\publish`.</span><span class="sxs-lookup"><span data-stu-id="51e59-148">The default publish folder is `bin\$(Configuration)\netcoreapp<version>\publish`.</span></span> <span data-ttu-id="51e59-149">Der Standardwert für `$(Configuration)` ist *Debuggen*.</span><span class="sxs-lookup"><span data-stu-id="51e59-149">The default for `$(Configuration)` is *Debug*.</span></span> <span data-ttu-id="51e59-150">Im vorgehenden Beispiel ist `<TargetFramework>` gleich `netcoreapp{X.Y}`.</span><span class="sxs-lookup"><span data-stu-id="51e59-150">In the preceding sample, the `<TargetFramework>` is `netcoreapp{X.Y}`.</span></span>

<span data-ttu-id="51e59-151">`dotnet publish -h` zeigt Hilfeinformationen zum Veröffentlichen an.</span><span class="sxs-lookup"><span data-stu-id="51e59-151">`dotnet publish -h` displays help information for publish.</span></span>

<span data-ttu-id="51e59-152">Der folgende Befehl gibt einen `Release`-Build und das Veröffentlichungsverzeichnis an:</span><span class="sxs-lookup"><span data-stu-id="51e59-152">The following command specifies a `Release` build and the publishing directory:</span></span>

```console
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="51e59-153">Der Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) ruft MSBuild auf, das das `Publish`-Ziel aufruft.</span><span class="sxs-lookup"><span data-stu-id="51e59-153">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="51e59-154">Alle Parameter, die an `dotnet publish` übergeben werden, werden an MSBuild übergeben.</span><span class="sxs-lookup"><span data-stu-id="51e59-154">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="51e59-155">Der Parameter `-c` wird der MSBuild-Eigenschaft `Configuration` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="51e59-155">The `-c` parameter maps to the `Configuration` MSBuild property.</span></span> <span data-ttu-id="51e59-156">Der Parameter `-o` wird `OutputPath` zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="51e59-156">The `-o` parameter maps to `OutputPath`.</span></span>

<span data-ttu-id="51e59-157">MSBuild-Eigenschaften können mithilfe eines der folgenden Formate übergeben werden:</span><span class="sxs-lookup"><span data-stu-id="51e59-157">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="51e59-158">Der folgende Befehl veröffentlicht einen `Release`-Build auf einer Netzwerkfreigabe:</span><span class="sxs-lookup"><span data-stu-id="51e59-158">The following command publishes a `Release` build to a network share:</span></span>

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

<span data-ttu-id="51e59-159">Die Netzwerkfreigabe wird durch führende Schrägstriche ( *//r8/* ) angegeben und funktioniert auf allen Plattformen, die von .NET Core unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="51e59-159">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

<span data-ttu-id="51e59-160">Versichern Sie sich, dass die veröffentlichte App für die Bereitstellung nicht ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="51e59-160">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="51e59-161">Die Dateien im Ordner *publish* sind gesperrt, wenn die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="51e59-161">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="51e59-162">Die Bereitstellung kann nicht erfolgen, da die gesperrten Dateien nicht kopiert werden können.</span><span class="sxs-lookup"><span data-stu-id="51e59-162">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="51e59-163">Veröffentlichungsprofile</span><span class="sxs-lookup"><span data-stu-id="51e59-163">Publish profiles</span></span>

<span data-ttu-id="51e59-164">In diesem Abschnitt wird Visual Studio 2017 oder höher verwendet, um ein Veröffentlichungsprofil zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="51e59-164">This section uses Visual Studio 2017 or later to create a publishing profile.</span></span> <span data-ttu-id="51e59-165">Nachdem das Profil erstellt ist, ist Veröffentlichung aus Visual Studio oder über die Befehlszeile möglich.</span><span class="sxs-lookup"><span data-stu-id="51e59-165">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span>

<span data-ttu-id="51e59-166">Veröffentlichungsprofile können den Veröffentlichungsprozess vereinfachen, und eine beliebige Anzahl von Profilen kann vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="51e59-166">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span> <span data-ttu-id="51e59-167">Erstellen Sie ein Veröffentlichungsprofil in Visual Studio durch Auswahl eines der folgenden Pfade:</span><span class="sxs-lookup"><span data-stu-id="51e59-167">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="51e59-168">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="51e59-168">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="51e59-169">Wählen Sie **{PROJEKTNAME} veröffentlichen** im Menü **Build** aus.</span><span class="sxs-lookup"><span data-stu-id="51e59-169">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="51e59-170">Die Registerkarte **Veröffentlichen** der Seite für Appfunktionen wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="51e59-170">The **Publish** tab of the app capacities page is displayed.</span></span> <span data-ttu-id="51e59-171">Wenn das Projekt kein Veröffentlichungsprofil enthält, wird die folgende Seite angezeigt:</span><span class="sxs-lookup"><span data-stu-id="51e59-171">If the project lacks a publish profile, the following page is displayed:</span></span>

![Die Registerkarte „Veröffentlichen“ der Seite für Appfunktionen wird angezeigt](visual-studio-publish-profiles/_static/az.png)

<span data-ttu-id="51e59-173">Wenn **Ordner** ausgewählt ist, geben Sie einen Ordnerpfad ein, um die veröffentlichten Objekte zu speichern.</span><span class="sxs-lookup"><span data-stu-id="51e59-173">When **Folder** is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="51e59-174">Der Standardordner ist *bin\Release\PublishOutput*.</span><span class="sxs-lookup"><span data-stu-id="51e59-174">The default folder is *bin\Release\PublishOutput*.</span></span> <span data-ttu-id="51e59-175">Klicken Sie auf die Schaltfläche **Profil erstellen**, um den Vorgang abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="51e59-175">Click the **Create Profile** button to finish.</span></span>

<span data-ttu-id="51e59-176">Sobald ein Veröffentlichungsprofil erstellt wurde, ändert sich die Registerkarte **Veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="51e59-176">Once a publish profile is created, the **Publish** tab changes.</span></span> <span data-ttu-id="51e59-177">Das neu erstellte Profil wird in einer Dropdownliste angezeigt.</span><span class="sxs-lookup"><span data-stu-id="51e59-177">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="51e59-178">Klicken Sie auf **Neues Profil erstellen**, um ein anderes neues Profil zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="51e59-178">Click **Create new profile** to create another new profile.</span></span>

![Die Registerkarte „Veröffentlichen“ der Seite für Appfunktionen zeigt das Ordnerprofil an](visual-studio-publish-profiles/_static/create_new.png)

<span data-ttu-id="51e59-180">Der Webpublishing-Assistent unterstützt folgende Veröffentlichungsziele:</span><span class="sxs-lookup"><span data-stu-id="51e59-180">The Publish wizard supports the following publish targets:</span></span>

* <span data-ttu-id="51e59-181">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="51e59-181">Azure App Service</span></span>
* <span data-ttu-id="51e59-182">Azure Virtual Machines</span><span class="sxs-lookup"><span data-stu-id="51e59-182">Azure Virtual Machines</span></span>
* <span data-ttu-id="51e59-183">IIS, FTP usw. (für alle Webserver)</span><span class="sxs-lookup"><span data-stu-id="51e59-183">IIS, FTP, etc. (for any web server)</span></span>
* <span data-ttu-id="51e59-184">Ordner</span><span class="sxs-lookup"><span data-stu-id="51e59-184">Folder</span></span>
* <span data-ttu-id="51e59-185">Profil importieren</span><span class="sxs-lookup"><span data-stu-id="51e59-185">Import Profile</span></span>

<span data-ttu-id="51e59-186">Weitere Informationen finden Sie unter [Welche Optionen für die Veröffentlichung sind für mich geeignet?](/visualstudio/ide/not-in-toc/web-publish-options).</span><span class="sxs-lookup"><span data-stu-id="51e59-186">For more information, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="51e59-187">Beim Erstellen eines Veröffentlichungsprofils mit Visual Studio wird eine *Properties/PublishProfiles/{PROFILNAME}.pubxml*-MSBuild-Datei erstellt.</span><span class="sxs-lookup"><span data-stu-id="51e59-187">When creating a publish profile with Visual Studio, a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file is created.</span></span> <span data-ttu-id="51e59-188">Diese *PUBXML*-Datei ist eine MSBuild-Datei und enthält die Konfigurationseinstellungen für die Veröffentlichung.</span><span class="sxs-lookup"><span data-stu-id="51e59-188">The *.pubxml* file is a MSBuild file and contains publish configuration settings.</span></span> <span data-ttu-id="51e59-189">Die Datei kann geändert werden, um den Build- und Veröffentlichungsprozess anzupassen.</span><span class="sxs-lookup"><span data-stu-id="51e59-189">This file can be changed to customize the build and publish process.</span></span> <span data-ttu-id="51e59-190">Diese Datei wird vom Veröffentlichungsprozess gelesen.</span><span class="sxs-lookup"><span data-stu-id="51e59-190">This file is read by the publishing process.</span></span> <span data-ttu-id="51e59-191">`<LastUsedBuildConfiguration>` ist ein Sonderfall, da es sich dabei um eine globale Eigenschaft handelt, die nicht in einer Datei enthalten sein sollte, die in den Build importiert wurde.</span><span class="sxs-lookup"><span data-stu-id="51e59-191">`<LastUsedBuildConfiguration>` is special because it's a global property and shouldn't be in any file that's imported in the build.</span></span> <span data-ttu-id="51e59-192">Weitere Informationen finden Sie unter [MSBuild: how to set the configuration property (MSBuild: Festlegen der Konfigurationseigenschaft)](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span><span class="sxs-lookup"><span data-stu-id="51e59-192">See [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) for more information.</span></span>

<span data-ttu-id="51e59-193">Beim Veröffentlichen in einem Azure-Ziel enthält die *PUBXML*-Datei Ihre Azure-Abonnement-ID.</span><span class="sxs-lookup"><span data-stu-id="51e59-193">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="51e59-194">Bei diesem Zieltyp wird vom Hinzufügen der Datei zur Quellkontrolle abgeraten.</span><span class="sxs-lookup"><span data-stu-id="51e59-194">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="51e59-195">Beim Veröffentlichen auf einem Nicht-Azure-Ziel kann die *PUBXML*-Datei eingecheckt werden.</span><span class="sxs-lookup"><span data-stu-id="51e59-195">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="51e59-196">Vertrauliche Informationen (z.B. das Kennwort für die Veröffentlichung) werden pro Benutzer/Computer verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="51e59-196">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="51e59-197">Sie werden in der Datei *Properties/PublishProfiles/{PROFILNAME}.pubxml.user* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="51e59-197">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="51e59-198">Da diese Datei vertrauliche Informationen enthalten kann, sollte sie nicht in die Quellverwaltung eingecheckt werden.</span><span class="sxs-lookup"><span data-stu-id="51e59-198">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="51e59-199">Einen Überblick über das Veröffentlichen einer Web-App auf ASP.NET Core finden Sie unter [Hosten und Bereitstellen](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="51e59-199">For an overview of how to publish a web app on ASP.NET Core, see [Host and deploy](xref:host-and-deploy/index).</span></span> <span data-ttu-id="51e59-200">Die MSBuild-Aufgaben und -Ziele, die zum Veröffentlichen einer ASP.NET Core-App erforderlich sind, werden als Open Source im Repository [aspnet/websdk](https://github.com/aspnet/websdk) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="51e59-200">The MSBuild tasks and targets necessary to publish an ASP.NET Core app are open-source at the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="51e59-201">`dotnet publish` kann den Ordner, MSDeploy und [Kudu](https://github.com/projectkudu/kudu/wiki)-Veröffentlichungsprofile verwenden:</span><span class="sxs-lookup"><span data-stu-id="51e59-201">`dotnet publish` can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles:</span></span>

<span data-ttu-id="51e59-202">Ordner (funktioniert plattformübergreifend):</span><span class="sxs-lookup"><span data-stu-id="51e59-202">Folder (works cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="51e59-203">MSDeploy (funktioniert zurzeit nur in Windows, da MSDdeploy nicht plattformübergreifend ist):</span><span class="sxs-lookup"><span data-stu-id="51e59-203">MSDeploy (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="51e59-204">MSDeploy-Paket (funktioniert zurzeit nur in Windows, da MSDdeploy nicht plattformübergreifend ist):</span><span class="sxs-lookup"><span data-stu-id="51e59-204">MSDeploy package (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="51e59-205">Übergeben Sie in den vorherigen Beispielen `deployonbuild` nicht an `dotnet publish`.</span><span class="sxs-lookup"><span data-stu-id="51e59-205">In the preceding examples, don't pass `deployonbuild` to `dotnet publish`.</span></span>

<span data-ttu-id="51e59-206">Weitere Informationen finden Sie unter [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span><span class="sxs-lookup"><span data-stu-id="51e59-206">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="51e59-207">`dotnet publish` unterstützt Kudu-APIs zur Veröffentlichung in Azure von jeder Plattform aus.</span><span class="sxs-lookup"><span data-stu-id="51e59-207">`dotnet publish` supports Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="51e59-208">Die Visual Studio-Veröffentlichung unterstützt die Kudu-APIs, wird für die plattformübergreifende Veröffentlichung in Azure aber vom WebSDK unterstützt.</span><span class="sxs-lookup"><span data-stu-id="51e59-208">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>

<span data-ttu-id="51e59-209">Fügen Sie dem Ordner *Properties/PublishProfiles* ein Veröffentlichungsprofil mit folgendem Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="51e59-209">Add a publish profile to the *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="51e59-210">Führen Sie den folgenden Befehl aus, um die Veröffentlichungsinhalte zu zippen und über die Kudu-APIs in Azure zu veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="51e59-210">Run the following command to zip up the publish contents and publish it to Azure using the Kudu APIs:</span></span>

```console
dotnet publish /p:PublishProfile=Azure /p:Configuration=Release
```

<span data-ttu-id="51e59-211">Legen Sie die folgenden MSBuild-Eigenschaften fest, wenn Sie ein Veröffentlichungsprofil verwenden:</span><span class="sxs-lookup"><span data-stu-id="51e59-211">Set the following MSBuild properties when using a publish profile:</span></span>

* `DeployOnBuild=true`
* `PublishProfile={PUBLISH PROFILE}`

<span data-ttu-id="51e59-212">Beispielsweise können Sie einen der unten stehenden Befehle ausführen, wenn Sie mit einem Profil namens *FolderProfile* veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="51e59-212">When publishing with a profile named *FolderProfile*, either of the commands below can be executed:</span></span>

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="51e59-213">Beim Aufrufen von [dotnet build](/dotnet/core/tools/dotnet-build) wird `msbuild` aufgerufen, um den Build- und Veröffentlichungsprozess auszuführen.</span><span class="sxs-lookup"><span data-stu-id="51e59-213">When invoking [dotnet build](/dotnet/core/tools/dotnet-build), it calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="51e59-214">Der Aufruf von `dotnet build` oder `msbuild` ist bei Übergabe in einem Ordnerprofil gleichwertig.</span><span class="sxs-lookup"><span data-stu-id="51e59-214">Calling either `dotnet build` or `msbuild` is equivalent when passing in a folder profile.</span></span> <span data-ttu-id="51e59-215">Wenn Sie MSBuild direkt in Windows aufrufen, wird die .NET Framework-Version von MSBuild verwendet.</span><span class="sxs-lookup"><span data-stu-id="51e59-215">When calling MSBuild directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="51e59-216">Die Veröffentlichung mit MSDeploy ist aktuell auf Windows-Computer beschränkt.</span><span class="sxs-lookup"><span data-stu-id="51e59-216">MSDeploy is currently limited to Windows machines for publishing.</span></span> <span data-ttu-id="51e59-217">Wenn Sie `dotnet build` von einem Profil ohne Ordner aufrufen, wird MSBuild aufgerufen. MSBuild verwendet MSDeploy für Profile ohne Ordner.</span><span class="sxs-lookup"><span data-stu-id="51e59-217">Calling `dotnet build` on a non-folder profile invokes MSBuild, and MSBuild uses MSDeploy on non-folder profiles.</span></span> <span data-ttu-id="51e59-218">Wenn Sie `dotnet build` von einem Profil ohne Ordner aufrufen, wird MSBuild (mithilfe von MSDeploy) aufgerufen. Dies führt zu einem Fehler (auch beim Ausführen auf einer Windows-Plattform).</span><span class="sxs-lookup"><span data-stu-id="51e59-218">Calling `dotnet build` on a non-folder profile invokes MSBuild (using MSDeploy) and results in a failure (even when running on a Windows platform).</span></span> <span data-ttu-id="51e59-219">Rufen Sie MSBuild direkt auf, um mit einem Profil ohne Ordner zu veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="51e59-219">To publish with a non-folder profile, call MSBuild directly.</span></span>

<span data-ttu-id="51e59-220">Der folgende Ordner „Veröffentlichungsprofil“ wurde mit Visual Studio erstellt und veröffentlicht in eine Netzwerkfreigabe.</span><span class="sxs-lookup"><span data-stu-id="51e59-220">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="51e59-221">Im vorherigen Beispiel ist `<LastUsedBuildConfiguration>` auf `Release` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="51e59-221">In the preceding example, `<LastUsedBuildConfiguration>` is set to `Release`.</span></span> <span data-ttu-id="51e59-222">Bei der Veröffentlichung von Visual Studio wird der `<LastUsedBuildConfiguration>`-Wert für die Konfigurationseigenschaft mithilfe des Werts festgelegt, der beim Starten des Veröffentlichungsprozesses vorliegt.</span><span class="sxs-lookup"><span data-stu-id="51e59-222">When publishing from Visual Studio, the `<LastUsedBuildConfiguration>` configuration property value is set using the value when the publish process is started.</span></span> <span data-ttu-id="51e59-223">Die `<LastUsedBuildConfiguration>`-Konfigurationseigenschaft ist ein Sonderfall und sollte nicht in einer importierte MSBuild-Datei überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="51e59-223">The `<LastUsedBuildConfiguration>` configuration property is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="51e59-224">Diese Eigenschaft kann von der Befehlszeile aus überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="51e59-224">This property can be overridden from the command line.</span></span>

<span data-ttu-id="51e59-225">Mit der .NET Core-CLI:</span><span class="sxs-lookup"><span data-stu-id="51e59-225">Using the .NET Core CLI:</span></span>

```console
dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="51e59-226">Verwenden von MSBuild:</span><span class="sxs-lookup"><span data-stu-id="51e59-226">Using MSBuild:</span></span>

```console
msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="51e59-227">Veröffentlichen auf einen MSDeploy-Endpunkt von der Befehlszeile</span><span class="sxs-lookup"><span data-stu-id="51e59-227">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="51e59-228">Im folgenden Beispiel wird von Visual Studio eine ASP.NET Core-Web-App namens *AzureWebApp* erstellt.</span><span class="sxs-lookup"><span data-stu-id="51e59-228">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="51e59-229">Ein Azure Apps-Veröffentlichungsprofil wird mit Visual Studio hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="51e59-229">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="51e59-230">Weitere Informationen zum Erstellen eines Profils finden Sie unter im Abschnitt [Veröffentlichungsprofile](#publish-profiles).</span><span class="sxs-lookup"><span data-stu-id="51e59-230">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="51e59-231">Um eine App mit einem Veröffentlichungsprofil bereitzustellen, führen Sie den Befehl `msbuild` an einer **Developer-Eingabeaufforderung** von Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="51e59-231">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="51e59-232">Die Eingabeaufforderung finden Sie im Ordner *Visual Studio* im **Start**-Menü auf der Windows-Taskleiste.</span><span class="sxs-lookup"><span data-stu-id="51e59-232">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="51e59-233">Für einfacheren Zugriff können Sie die Eingabeaufforderung zum Menü **Tools** in Visual Studio hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="51e59-233">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="51e59-234">Weitere Informationen finden Sie unter [Developer-Eingabeaufforderung für Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="51e59-234">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="51e59-235">MSBuild hat die folgende Befehlssyntax:</span><span class="sxs-lookup"><span data-stu-id="51e59-235">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="51e59-236">{PATH}: der Pfad zur Projektdatei der App.</span><span class="sxs-lookup"><span data-stu-id="51e59-236">{PATH} &ndash; Path to the app's project file.</span></span>
* <span data-ttu-id="51e59-237">{PROFILE}: der Name des Veröffentlichungsprofils.</span><span class="sxs-lookup"><span data-stu-id="51e59-237">{PROFILE} &ndash; Name of the publish profile.</span></span>
* <span data-ttu-id="51e59-238">{USERNAME}: der MSDeploy-Benutzername.</span><span class="sxs-lookup"><span data-stu-id="51e59-238">{USERNAME} &ndash; MSDeploy username.</span></span> <span data-ttu-id="51e59-239">Die {USERNAME} ist im Veröffentlichungsprofil zu finden.</span><span class="sxs-lookup"><span data-stu-id="51e59-239">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="51e59-240">{PASSWORD}: das MSDeploy-Kennwort.</span><span class="sxs-lookup"><span data-stu-id="51e59-240">{PASSWORD} &ndash; MSDeploy password.</span></span> <span data-ttu-id="51e59-241">Sie finden das {PASSWORD} in der Datei *{PROFILE}.PublishSettings*.</span><span class="sxs-lookup"><span data-stu-id="51e59-241">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="51e59-242">Laden Sie die Datei *.PublishSettings* von einem der folgenden Speicherorte herunter:</span><span class="sxs-lookup"><span data-stu-id="51e59-242">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="51e59-243">Projektmappen-Explorer: Wählen Sie **Ansicht** > **Cloud-Explorer** aus.</span><span class="sxs-lookup"><span data-stu-id="51e59-243">Solution Explorer: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="51e59-244">Stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her.</span><span class="sxs-lookup"><span data-stu-id="51e59-244">Connect with your Azure subscription.</span></span> <span data-ttu-id="51e59-245">Öffnen Sie **App Services**.</span><span class="sxs-lookup"><span data-stu-id="51e59-245">Open **App Services**.</span></span> <span data-ttu-id="51e59-246">Klicken Sie mit der rechten Maustaste auf die App.</span><span class="sxs-lookup"><span data-stu-id="51e59-246">Right-click the app.</span></span> <span data-ttu-id="51e59-247">Wählen Sie **Veröffentlichungsprofil herunterladen** aus.</span><span class="sxs-lookup"><span data-stu-id="51e59-247">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="51e59-248">Azure-Portal: Wählen Sie **Veröffentlichungsprofil abrufen** im Bereich **Übersicht** der Web-App aus.</span><span class="sxs-lookup"><span data-stu-id="51e59-248">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="51e59-249">Im folgenden Beispiel wird ein Veröffentlichungsprofil namens *AzureWebApp – Web Deploy* verwendet:</span><span class="sxs-lookup"><span data-stu-id="51e59-249">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="51e59-250">Ein Veröffentlichungsprofil kann auch mit dem .NET Core-CLI-Befehl [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) an einer Windows-Eingabeaufforderung verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="51e59-250">A publish profile can also be used with the .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command prompt:</span></span>

```console
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!NOTE]
> <span data-ttu-id="51e59-251">Der Befehl [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) ist plattformübergreifend verfügbar und kann ASP.NET Core-Apps unter macOS und Linux kompilieren.</span><span class="sxs-lookup"><span data-stu-id="51e59-251">The [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command is available cross-platform and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="51e59-252">Allerdings ist es mit MSBuild unter macOS und Linux nicht möglich, eine App in Azure oder in einem anderen MSDeploy-Endpunkt bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="51e59-252">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoint.</span></span> <span data-ttu-id="51e59-253">MSDeploy ist nur unter Windows verfügbar.</span><span class="sxs-lookup"><span data-stu-id="51e59-253">MSDeploy is only available on Windows.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="51e59-254">Festlegen der Umgebung</span><span class="sxs-lookup"><span data-stu-id="51e59-254">Set the environment</span></span>

<span data-ttu-id="51e59-255">Beziehen Sie die `<EnvironmentName>`-Eigenschaft in das Veröffentlichungsprofil (*PUBXML*) oder die Projektdatei ein, um die [Umgebung](xref:fundamentals/environments) der App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="51e59-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="51e59-256">Wenn Sie *web.config*-Transformationen benötigen (z.B. Umgebungsvariablen basierend auf der Konfiguration, dem Profil oder der Umgebung), finden Sie weitere Informationen unter <xref:host-and-deploy/iis/transform-webconfig>.</span><span class="sxs-lookup"><span data-stu-id="51e59-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="51e59-257">Dateien ausschließen</span><span class="sxs-lookup"><span data-stu-id="51e59-257">Exclude files</span></span>

<span data-ttu-id="51e59-258">Wenn Sie ASP.NET Core-Web-Apps veröffentlichen, sind die folgenden Ressourcen enthalten:</span><span class="sxs-lookup"><span data-stu-id="51e59-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="51e59-259">Build-Artefakte</span><span class="sxs-lookup"><span data-stu-id="51e59-259">Build artifacts</span></span>
* <span data-ttu-id="51e59-260">Ordner und Dateien, die den folgenden Globmustern entsprechen:</span><span class="sxs-lookup"><span data-stu-id="51e59-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="51e59-261">`**\*.config` (Beispiel: *web.config*)</span><span class="sxs-lookup"><span data-stu-id="51e59-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="51e59-262">`**\*.json` (Beispiel: *appsettings.json*)</span><span class="sxs-lookup"><span data-stu-id="51e59-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="51e59-263">MSBuild unterstützt [Globmuster](https://gruntjs.com/configuring-tasks#globbing-patterns).</span><span class="sxs-lookup"><span data-stu-id="51e59-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="51e59-264">Das folgende `<Content>`-Element unterdrückt beispielsweise das Kopieren von Textdateien (*TXT*) aus dem Ordner *wwwroot/content* und allen seinen Unterordnern:</span><span class="sxs-lookup"><span data-stu-id="51e59-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="51e59-265">Das vorstehende Markup kann einem Veröffentlichungsprofil oder der *CSPROJ*-Datei hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="51e59-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="51e59-266">Wenn es zu der *CSPROJ*-Datei hinzugefügt wird, wird die Regel zu allen Veröffentlichungsprofilen im Projekt hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="51e59-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="51e59-267">Das folgende `<MsDeploySkipRules>`-Element schließt alle Dateien aus dem Ordner *wwwroot\content* aus:</span><span class="sxs-lookup"><span data-stu-id="51e59-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="51e59-268">`<MsDeploySkipRules>` löscht die *Skip*-Ziele von der Bereitstellungsseite nicht.</span><span class="sxs-lookup"><span data-stu-id="51e59-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="51e59-269">`<Content>`-Zieldateien und -ordner werden von der Bereitstellungsseite gelöscht.</span><span class="sxs-lookup"><span data-stu-id="51e59-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="51e59-270">Nehmen wir beispielsweise an, dass eine bereitgestellte Web-App folgende Dateien enthält:</span><span class="sxs-lookup"><span data-stu-id="51e59-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="51e59-271">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="51e59-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="51e59-272">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="51e59-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="51e59-273">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="51e59-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="51e59-274">Wenn folgende `<MsDeploySkipRules>`-Elemente hinzugefügt werden, würden diese Dateien nicht von der Bereitstellungsseite gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="51e59-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="51e59-275">Die vorangehenden `<MsDeploySkipRules>`-Elemente verhindern, dass *übersprungene* Dateien bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="51e59-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="51e59-276">Diese Dateien werden nach ihrer Bereitstellung nicht gelöscht.</span><span class="sxs-lookup"><span data-stu-id="51e59-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="51e59-277">Das folgende `<Content>`-Element löscht die Zieldateien auf der Bereitstellungsseite:</span><span class="sxs-lookup"><span data-stu-id="51e59-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="51e59-278">Die Bereitstellung über die Befehlszeile mit dem vorangehenden `<Content>`-Element führt zu folgender Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="51e59-278">Using command-line deployment with the preceding `<Content>` element yields the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="51e59-279">Includedateien</span><span class="sxs-lookup"><span data-stu-id="51e59-279">Include files</span></span>

<span data-ttu-id="51e59-280">Füg das folgende Markup gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="51e59-280">The following markup:</span></span>

* <span data-ttu-id="51e59-281">Schließt den Ordner *images* außerhalb des Projektverzeichnisses für den Ordner *wwwroot/images* auf der Veröffentlichungssite ein.</span><span class="sxs-lookup"><span data-stu-id="51e59-281">Includes an *images* folder outside the project directory to the *wwwroot/images* folder of the publish site.</span></span>
* <span data-ttu-id="51e59-282">Kann der *CSPROJ*-Datei oder dem Veröffentlichungsprofil hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="51e59-282">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="51e59-283">Wenn es der *CSPROJ*-Datei hinzugefügt wird, wird es in jedes Veröffentlichungsprofil im Projekt eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="51e59-283">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="51e59-284">Das folgende hervorgehobene Markup zeigt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="51e59-284">The following highlighted markup shows how to:</span></span>

* <span data-ttu-id="51e59-285">Kopieren einer Datei von außerhalb des Projekts in den Ordner *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="51e59-285">Copy a file from outside the project into the *wwwroot* folder.</span></span>
* <span data-ttu-id="51e59-286">Ausschließen des Ordners *wwwroot\Content*.</span><span class="sxs-lookup"><span data-stu-id="51e59-286">Exclude the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="51e59-287">Ausschließen von *Views\Home\About2.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="51e59-287">Exclude *Views\Home\About2.cshtml*.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework />
    <ProjectGuid>afa9f185-7ce0-4935-9da1-ab676229d68a</ProjectGuid>
    <publishUrl>bin\Release\PublishOutput</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
  <ItemGroup>
    <ResolvedFileToPublish Include="..\ReadMe2.MD">
      <RelativePath>wwwroot\ReadMe2.MD</RelativePath>
    </ResolvedFileToPublish>

    <Content Update="wwwroot\Content\**\*" CopyToPublishDirectory="Never" />
    <Content Update="Views\Home\About2.cshtml" CopyToPublishDirectory="Never" />

  </ItemGroup>
</Project>
```

<span data-ttu-id="51e59-288">Weitere Bereitstellungsbeispiele finden Sie in [Infodatei im Repository für das Web SDK](https://github.com/aspnet/websdk).</span><span class="sxs-lookup"><span data-stu-id="51e59-288">See the [Web SDK repository Readme](https://github.com/aspnet/websdk) for more deployment samples.</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="51e59-289">Führen Sie ein Ziel vor oder nach der Veröffentlichung aus.</span><span class="sxs-lookup"><span data-stu-id="51e59-289">Run a target before or after publishing</span></span>

<span data-ttu-id="51e59-290">Die integrierten Ziele `BeforePublish` und `AfterPublish` führen ein Ziel vor oder nach dem Veröffentlichungsziel aus.</span><span class="sxs-lookup"><span data-stu-id="51e59-290">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="51e59-291">Fügen Sie die folgenden Elemente dem Veröffentlichungsprofil hinzu, um Konsolenmeldungen vor und nach der Veröffentlichung zu protokollieren:</span><span class="sxs-lookup"><span data-stu-id="51e59-291">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="51e59-292">Veröffentlichen auf einem Server unter Verwendung eines nicht vertrauenswürdigen Zertifikats</span><span class="sxs-lookup"><span data-stu-id="51e59-292">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="51e59-293">Fügen Sie die Eigenschaft `<AllowUntrustedCertificate>` mit dem Wert `True` auf dem Veröffentlichungsprofil hinzu:</span><span class="sxs-lookup"><span data-stu-id="51e59-293">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="51e59-294">Der Kudu-Dienst</span><span class="sxs-lookup"><span data-stu-id="51e59-294">The Kudu service</span></span>

<span data-ttu-id="51e59-295">Verwenden Sie zum Anzeigen der Dateien in einer Web-App-Bereitstellung von Azure App Service den [Kudu-Dienst](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span><span class="sxs-lookup"><span data-stu-id="51e59-295">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="51e59-296">Fügen Sie das Token `scm` an den Namen Ihrer Web-App an.</span><span class="sxs-lookup"><span data-stu-id="51e59-296">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="51e59-297">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="51e59-297">For example:</span></span>

| <span data-ttu-id="51e59-298">URL</span><span class="sxs-lookup"><span data-stu-id="51e59-298">URL</span></span>                                    | <span data-ttu-id="51e59-299">Ergebnis</span><span class="sxs-lookup"><span data-stu-id="51e59-299">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="51e59-300">Webanwendung</span><span class="sxs-lookup"><span data-stu-id="51e59-300">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="51e59-301">Der Kudu-Dienst</span><span class="sxs-lookup"><span data-stu-id="51e59-301">Kudu service</span></span> |

<span data-ttu-id="51e59-302">Wählen Sie das Menüelement [Debugging-Konsole](https://github.com/projectkudu/kudu/wiki/Kudu-console), aus, um Dateien anzuzeigen, zu bearbeiten, zu löschen oder hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="51e59-302">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51e59-303">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="51e59-303">Additional resources</span></span>

* <span data-ttu-id="51e59-304">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) vereinfacht die Bereitstellung von Web-Apps und Websites auf IIS-Servern.</span><span class="sxs-lookup"><span data-stu-id="51e59-304">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="51e59-305">[GitHub-Repository für Web SDK](https://github.com/aspnet/websdk/issues): Probleme melden und Features für die Bereitstellung anfordern.</span><span class="sxs-lookup"><span data-stu-id="51e59-305">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="51e59-306">Veröffentlichen einer ASP.NET-Web-App auf einer Azure-VM aus Visual Studio</span><span class="sxs-lookup"><span data-stu-id="51e59-306">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
