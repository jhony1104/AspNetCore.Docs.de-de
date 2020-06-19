---
title: Verwenden von ASP.net Core SignalR mit Blazor WebAssembly
author: guardrex
description: Erstellen Sie eine Chat-App, die ASP.NET Core SignalR mit Blazor WebAssembly verwendet.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 8ef029af10f767ae505fddc636bb15f7e7c5e538
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102705"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="deda4-103">Verwenden von ASP.net Core SignalR mit Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="deda4-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="deda4-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="deda4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="deda4-105">In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mithilfe von SignalR mit Blazor WebAssembly beschrieben.</span><span class="sxs-lookup"><span data-stu-id="deda4-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="deda4-106">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="deda4-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="deda4-107">Erstellen eines von Blazor WebAssembly gehosteten App-Projekts</span><span class="sxs-lookup"><span data-stu-id="deda4-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="deda4-108">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="deda4-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="deda4-109">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="deda4-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="deda4-110">Hinzufügen von SignalR-Diensten und eines Endpunkts zum SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="deda4-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="deda4-111">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="deda4-111">Add Razor component code for chat</span></span>

<span data-ttu-id="deda4-112">Am Ende dieses Tutorials verfügen Sie über eine funktionierende Chat-App.</span><span class="sxs-lookup"><span data-stu-id="deda4-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="deda4-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="deda4-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="deda4-114">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="deda4-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="deda4-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="deda4-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="deda4-116">[Visual Studio 2019 Version 16.6 oder höher](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="deda4-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="deda4-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="deda4-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="deda4-118">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="deda4-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="deda4-119">Visual Studio für Mac Version 8.6 oder höher</span><span class="sxs-lookup"><span data-stu-id="deda4-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="deda4-120">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="deda4-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="deda4-121">Erstellen eines gehosteten Blazor-WebAssembly-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="deda4-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="deda4-122">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="deda4-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="deda4-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="deda4-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="deda4-124">Visual Studio 16.6 oder höher und das .NET Core SDK 3.1.300 oder höher sind erforderlich.</span><span class="sxs-lookup"><span data-stu-id="deda4-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="deda4-125">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="deda4-125">Create a new project.</span></span>

1. <span data-ttu-id="deda4-126">Klicken Sie auf **Blazor-App** und dann auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="deda4-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="deda4-127">Geben Sie in das Feld **Projektname** „BlazorSignalRApp“ ein.</span><span class="sxs-lookup"><span data-stu-id="deda4-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="deda4-128">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="deda4-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="deda4-129">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="deda4-129">Select **Create**.</span></span>

1. <span data-ttu-id="deda4-130">Wählen Sie die Vorlage **Blazor-WebAssembly-App** aus.</span><span class="sxs-lookup"><span data-stu-id="deda4-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="deda4-131">Aktivieren Sie unter **Erweitert** das Kontrollkästchen **Von ASP.net Core gehostet**.</span><span class="sxs-lookup"><span data-stu-id="deda4-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="deda4-132">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="deda4-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="deda4-133">Wenn Sie eine neue Version von Visual Studio installiert oder ein Upgrade auf diese Version durchgeführt haben und die Blazor-WebAssembly-Vorlage nicht auf der VS-Benutzeroberfläche angezeigt wird, installieren Sie die Vorlage mit dem zuvor gezeigten `dotnet new`-Befehl erneut.</span><span class="sxs-lookup"><span data-stu-id="deda4-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="deda4-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="deda4-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="deda4-135">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="deda4-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="deda4-136">Öffnen Sie in Visual Studio Code den Projektordner der App.</span><span class="sxs-lookup"><span data-stu-id="deda4-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="deda4-137">Wenn das Dialogfeld angezeigt wird, um Assets zum Erstellen und Debuggen der App hinzuzufügen, wählen Sie **Ja**aus.</span><span class="sxs-lookup"><span data-stu-id="deda4-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="deda4-138">Visual Studio Code fügt automatisch den Ordner *.vscode* mit den generierten Dateien *launch.json* und *tasks.json* hinzu.</span><span class="sxs-lookup"><span data-stu-id="deda4-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="deda4-139">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="deda4-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="deda4-140">Installieren Sie die neueste Version von [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/), und führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="deda4-140">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="deda4-141">Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="deda4-141">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="deda4-142">Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="deda4-142">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="deda4-143">Wählen Sie die Vorlage **Blazor-WebAssembly-App** aus.</span><span class="sxs-lookup"><span data-stu-id="deda4-143">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="deda4-144">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="deda4-144">Select **Next**.</span></span>

   <span data-ttu-id="deda4-145">Bestätigen Sie die folgenden Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="deda4-145">Confirm the following configurations:</span></span>

   * <span data-ttu-id="deda4-146">**Zielframework** auf **.NET Core 3.1** festgelegt</span><span class="sxs-lookup"><span data-stu-id="deda4-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="deda4-147">**Authentifizierung** auf **Keine Authentifizierung** festgelegt</span><span class="sxs-lookup"><span data-stu-id="deda4-147">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="deda4-148">Aktivieren Sie das Kontrollkästchen **ASP.NET Core Hosted** (Auf ASP.NET Core gehostet).</span><span class="sxs-lookup"><span data-stu-id="deda4-148">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="deda4-149">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="deda4-149">Select **Next**.</span></span>

1. <span data-ttu-id="deda4-150">Benennen Sie im Feld **Projektname** die App `BlazorSignalRApp`.</span><span class="sxs-lookup"><span data-stu-id="deda4-150">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="deda4-151">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="deda4-151">Select **Create**.</span></span>

   <span data-ttu-id="deda4-152">Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="deda4-152">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="deda4-153">Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="deda4-153">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="deda4-154">Öffnen Sie das Projekt, indem Sie zum Projektordner navigieren und die Projektmappendatei des Projekts öffnen ( *.sln*).</span><span class="sxs-lookup"><span data-stu-id="deda4-154">Open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="deda4-155">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="deda4-155">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="deda4-156">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="deda4-156">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="deda4-157">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="deda4-157">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="deda4-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="deda4-158">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="deda4-159">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **BlazorSignalRApp.Client**, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="deda4-159">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="deda4-160">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die **Paketquelle** auf *nuget.org* eingestellt ist.</span><span class="sxs-lookup"><span data-stu-id="deda4-160">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="deda4-161">Wenn **Durchsuchen** ausgewählt ist, geben Sie „Microsoft.AspNetCore.SignalR.Client“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="deda4-161">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="deda4-162">Wählen Sie in den Suchergebnissen das Paket [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) aus, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="deda4-162">In the search results, select the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Install**.</span></span>

1. <span data-ttu-id="deda4-163">Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="deda4-163">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="deda4-164">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="deda4-164">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="deda4-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="deda4-165">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="deda4-166">Führen Sie im **integrierten Terminal** (**Ansicht** > **Terminal** in der Symbolleiste) die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="deda4-166">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="deda4-167">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="deda4-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="deda4-168">Klicken Sie in der Seitenleiste **Projektmappe** mit der rechten Maustaste auf **BlazorSignalRApp.Client**, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="deda4-168">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="deda4-169">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die in der Dropdown-Liste für die Paketquelle *nuget.org* festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="deda4-169">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="deda4-170">Wenn **Durchsuchen** ausgewählt ist, geben Sie „Microsoft.AspNetCore.SignalR.Client“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="deda4-170">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="deda4-171">Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem Paket [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/), und klicken Sie dann auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="deda4-171">In the search results, select the check box next to the [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) package and select **Add Package**.</span></span>

1. <span data-ttu-id="deda4-172">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="deda4-172">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="deda4-173">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="deda4-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="deda4-174">Führen Sie in einer Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="deda4-174">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="deda4-175">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="deda4-175">Add a SignalR hub</span></span>

<span data-ttu-id="deda4-176">Erstellen Sie im Projekt **BlazorSignalRApp.Server** den Ordner *Hubs* (Plural), und fügen Sie die folgende `ChatHub`-Klasse (*Hubs/ChatHub.cs*) hinzu:</span><span class="sxs-lookup"><span data-stu-id="deda4-176">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="deda4-177">Hinzufügen von Diensten und eines Endpunkts zum SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="deda4-177">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="deda4-178">Öffnen Sie im Projekt **BlazorSignalRApp.Server** die Datei *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="deda4-178">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="deda4-179">Fügen Sie am Anfang der Datei den Namespace für die `ChatHub`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="deda4-179">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="deda4-180">Fügen Sie SignalR und Middlewaredienste für die Komprimierung von Antworten zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="deda4-180">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="deda4-181">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="deda4-181">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="deda4-182">Verwenden Sie die Middlewaredienste für die Komprimierung von Antworten am Anfang der Konfiguration der Verarbeitungspipeline.</span><span class="sxs-lookup"><span data-stu-id="deda4-182">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="deda4-183">Fügen Sie zwischen den Endpunkten für Controller und dem clientseitigen Fallback einen Endpunkt für den Hub hinzu.</span><span class="sxs-lookup"><span data-stu-id="deda4-183">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="deda4-184">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="deda4-184">Add Razor component code for chat</span></span>

1. <span data-ttu-id="deda4-185">Öffnen Sie im Projekt **BlazorSignalRApp.Client** die Datei *Pages/Index.razor*.</span><span class="sxs-lookup"><span data-stu-id="deda4-185">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="deda4-186">Ersetzen Sie das Markup durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="deda4-186">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="deda4-187">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="deda4-187">Run the app</span></span>

1. <span data-ttu-id="deda4-188">Befolgen Sie die Anleitungen für die Auswahl Ihrer Tools:</span><span class="sxs-lookup"><span data-stu-id="deda4-188">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="deda4-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="deda4-189">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="deda4-190">Wählen Sie im **Projektmappen-Explorer** das Projekt **BlazorSignalRApp.Server** aus.</span><span class="sxs-lookup"><span data-stu-id="deda4-190">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="deda4-191">Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="deda4-191">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="deda4-192">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="deda4-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="deda4-193">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="deda4-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="deda4-194">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="deda4-194">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="deda4-195">Die ![SignalR-Blazor WebAssembly-Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="deda4-195">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="deda4-196">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="deda4-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="deda4-197">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="deda4-197">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="deda4-198">Wenn VS Code anbietet, ein Startprofil für die Server-App zu erstellen ( *.vscode/launch.json*), scheint der `program`-Eintrag, ähnlich wie der folgende, auf die Assembly der App (`{APPLICATION NAME}.Server.dll`) zu zeigen:</span><span class="sxs-lookup"><span data-stu-id="deda4-198">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="deda4-199">Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="deda4-199">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="deda4-200">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="deda4-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="deda4-201">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="deda4-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="deda4-202">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="deda4-202">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="deda4-203">Die ![SignalR-Blazor WebAssembly-Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="deda4-203">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="deda4-204">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="deda4-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="deda4-205">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="deda4-205">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="deda4-206">Wählen Sie in der Seitenleiste **Projektmappe** das Projekt **BlazorSignalRApp.Server** aus.</span><span class="sxs-lookup"><span data-stu-id="deda4-206">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="deda4-207">Drücken Sie <kbd>⌘</kbd>+<kbd>↩</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="deda4-207">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="deda4-208">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="deda4-208">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="deda4-209">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="deda4-209">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="deda4-210">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="deda4-210">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="deda4-211">Die ![SignalR-Blazor WebAssembly-Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="deda4-211">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="deda4-212">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="deda4-212">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="deda4-213">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="deda4-213">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="deda4-214">Führen Sie in einer Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="deda4-214">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="deda4-215">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="deda4-215">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="deda4-216">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="deda4-216">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="deda4-217">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="deda4-217">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="deda4-218">Die ![SignalR-Blazor WebAssembly-Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="deda4-218">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="deda4-219">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="deda4-219">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="deda4-220">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="deda4-220">Next steps</span></span>

<span data-ttu-id="deda4-221">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="deda4-221">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="deda4-222">Erstellen eines von Blazor WebAssembly gehosteten App-Projekts</span><span class="sxs-lookup"><span data-stu-id="deda4-222">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="deda4-223">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="deda4-223">Add the SignalR client library</span></span>
> * <span data-ttu-id="deda4-224">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="deda4-224">Add a SignalR hub</span></span>
> * <span data-ttu-id="deda4-225">Hinzufügen von SignalR-Diensten und eines Endpunkts zum SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="deda4-225">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="deda4-226">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="deda4-226">Add Razor component code for chat</span></span>

<span data-ttu-id="deda4-227">Weitere Informationen zum Erstellen von Blazor-Apps finden Sie in der Blazor-Dokumentation:</span><span class="sxs-lookup"><span data-stu-id="deda4-227">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="deda4-228">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="deda4-228">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="deda4-229">[Ursprungsübergreifende SignalR-Aushandlung für die Authentifizierung](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="deda4-229">[SignalR cross-origin negotiation for authentication](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span></span>
