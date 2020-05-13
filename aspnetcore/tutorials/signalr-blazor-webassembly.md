---
title: Verwenden von ASP.net Core SignalR mit Blazor WebAssembly
author: guardrex
description: Erstellen Sie eine Chat-App, die ASP.NET Core SignalR mit Blazor WebAssembly verwendet.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 1579b92dbc9db08bfdc5572e5d4245bd18d50590
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773787"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="f9c35-103">Verwenden von ASP.NET Core SignalR mit Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f9c35-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="f9c35-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f9c35-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="f9c35-105">In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR mit Blazor WebAssembly beschrieben.</span><span class="sxs-lookup"><span data-stu-id="f9c35-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="f9c35-106">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f9c35-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f9c35-107">Erstellen eines von Blazor WebAssembly gehosteten App-Projekts</span><span class="sxs-lookup"><span data-stu-id="f9c35-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="f9c35-108">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="f9c35-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="f9c35-109">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="f9c35-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="f9c35-110">Hinzufügen von SignalR-Diensten und eines Endpunkts für den SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="f9c35-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="f9c35-111">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="f9c35-111">Add Razor component code for chat</span></span>

<span data-ttu-id="f9c35-112">Am Ende dieses Tutorials verfügen Sie über eine funktionierende Chat-App.</span><span class="sxs-lookup"><span data-stu-id="f9c35-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="f9c35-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9c35-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f9c35-114">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f9c35-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f9c35-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9c35-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="f9c35-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f9c35-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f9c35-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f9c35-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="f9c35-118">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="f9c35-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="f9c35-119">Erstellen eines gehosteten Blazor WebAssembly-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="f9c35-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="f9c35-120">Wenn Sie nicht Visual Studio Version 16.6 Vorschauversion 2 oder höher verwenden, installieren Sie die [Blazor-WebAssembly](xref:blazor/hosting-models#blazor-webassembly)-Vorlage.</span><span class="sxs-lookup"><span data-stu-id="f9c35-120">When not using Visual Studio version 16.6 Preview 2 or later, install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="f9c35-121">Das Paket [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) enthält eine Vorschauversion, solange sich die Blazor WebAssembly in der Vorschauphase befindet.</span><span class="sxs-lookup"><span data-stu-id="f9c35-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="f9c35-122">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f9c35-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
```

<span data-ttu-id="f9c35-123">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="f9c35-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f9c35-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9c35-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f9c35-125">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="f9c35-125">Create a new project.</span></span>

1. <span data-ttu-id="f9c35-126">Wählen Sie **Blazor-App** und dann **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="f9c35-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="f9c35-127">Geben Sie in das Feld **Projektname** „BlazorSignalRApp“ ein.</span><span class="sxs-lookup"><span data-stu-id="f9c35-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="f9c35-128">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="f9c35-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="f9c35-129">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="f9c35-129">Select **Create**.</span></span>

1. <span data-ttu-id="f9c35-130">Wählen Sie die Vorlage **Blazor WebAssembly App** aus.</span><span class="sxs-lookup"><span data-stu-id="f9c35-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="f9c35-131">Aktivieren Sie unter **Erweitert** das Kontrollkästchen **Von ASP.net Core gehostet**.</span><span class="sxs-lookup"><span data-stu-id="f9c35-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="f9c35-132">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="f9c35-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="f9c35-133">Wenn Sie eine neue Version von Visual Studio aktualisiert oder installiert haben und die Blazor WebAssembly-Vorlage nicht in der VS-Benutzeroberfläche angezeigt wird, installieren Sie die Vorlage mit dem zuvor gezeigten Befehl `dotnet new` neu.</span><span class="sxs-lookup"><span data-stu-id="f9c35-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f9c35-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f9c35-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f9c35-135">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f9c35-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="f9c35-136">Öffnen Sie in Visual Studio Code den Projektordner der App.</span><span class="sxs-lookup"><span data-stu-id="f9c35-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="f9c35-137">Wenn das Dialogfeld angezeigt wird, um Assets zum Erstellen und Debuggen der App hinzuzufügen, wählen Sie **Ja**aus.</span><span class="sxs-lookup"><span data-stu-id="f9c35-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="f9c35-138">Visual Studio Code fügt automatisch den Ordner *.vscode* mit den generierten Dateien *launch.json* und *tasks.json* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f9c35-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f9c35-139">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f9c35-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f9c35-140">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f9c35-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="f9c35-141">Öffnen Sie das Projekt in Visual Studio für Mac, indem Sie zum Projektordner navigieren und die Projektmappendatei des Projekts öffnen ( *.sln*).</span><span class="sxs-lookup"><span data-stu-id="f9c35-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f9c35-142">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="f9c35-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f9c35-143">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f9c35-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="f9c35-144">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="f9c35-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f9c35-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9c35-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="f9c35-146">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **BlazorSignalRApp.Client**, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="f9c35-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f9c35-147">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die **Paketquelle** auf *nuget.org* eingestellt ist.</span><span class="sxs-lookup"><span data-stu-id="f9c35-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="f9c35-148">Wenn **Durchsuchen** ausgewählt ist, geben Sie „Microsoft.AspNetCore.SignalR.Client“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="f9c35-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="f9c35-149">Wählen Sie in den Suchergebnissen das Paket `Microsoft.AspNetCore.SignalR.Client` aus, und wählen Sie **Installation** aus.</span><span class="sxs-lookup"><span data-stu-id="f9c35-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="f9c35-150">Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="f9c35-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="f9c35-151">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="f9c35-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f9c35-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f9c35-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="f9c35-153">Führen Sie im **integrierten Terminal** (**Ansicht** > **Terminal** in der Symbolleiste) die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f9c35-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f9c35-154">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f9c35-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f9c35-155">Klicken Sie in der Seitenleiste **Projektmappe** mit der rechten Maustaste auf **BlazorSignalRApp.Client**, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="f9c35-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="f9c35-156">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die in der Dropdown-Liste für die Paketquelle *nuget.org* festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="f9c35-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="f9c35-157">Wenn **Durchsuchen** ausgewählt ist, geben Sie „Microsoft.AspNetCore.SignalR.Client“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="f9c35-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="f9c35-158">Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem `Microsoft.AspNetCore.SignalR.Client`-Paket, und wählen Sie **Paket hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f9c35-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="f9c35-159">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="f9c35-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f9c35-160">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="f9c35-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="f9c35-161">Führen Sie in einer Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f9c35-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="f9c35-162">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="f9c35-162">Add a SignalR hub</span></span>

<span data-ttu-id="f9c35-163">Erstellen Sie im Projekt **BlazorSignalRApp.Server** den Ordner *Hubs* (Plural), und fügen Sie die folgende `ChatHub`-Klasse (*Hubs/ChatHub.cs*) hinzu:</span><span class="sxs-lookup"><span data-stu-id="f9c35-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="f9c35-164">Hinzufügen von Diensten und eines Endpunkts für den SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="f9c35-164">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="f9c35-165">Öffnen Sie im Projekt **BlazorSignalRApp.Server** die Datei *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="f9c35-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="f9c35-166">Fügen Sie am Anfang der Datei den Namespace für die `ChatHub`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="f9c35-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="f9c35-167">Hinzufügen von SignalR und Middlewarediensten für die Komprimierung von Antworten zu `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f9c35-167">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="f9c35-168">Fügen Sie in `Startup.Configure` zwischen den Endpunkten für Controller und dem clientseitigen Fallback einen Endpunkt für den Hub hinzu:</span><span class="sxs-lookup"><span data-stu-id="f9c35-168">In `Startup.Configure` between the endpoints for controllers and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_UseEndpoints&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="f9c35-169">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="f9c35-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="f9c35-170">Öffnen Sie im Projekt **BlazorSignalRApp.Client** die Datei *Pages/Index.razor*.</span><span class="sxs-lookup"><span data-stu-id="f9c35-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="f9c35-171">Ersetzen Sie das Markup durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f9c35-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="f9c35-172">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="f9c35-172">Run the app</span></span>

1. <span data-ttu-id="f9c35-173">Befolgen Sie die Anleitungen für die Auswahl Ihrer Tools:</span><span class="sxs-lookup"><span data-stu-id="f9c35-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f9c35-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f9c35-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="f9c35-175">Wählen Sie im **Projektmappen-Explorer** das Projekt **BlazorSignalRApp.Server** aus.</span><span class="sxs-lookup"><span data-stu-id="f9c35-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="f9c35-176">Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f9c35-176">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f9c35-177">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="f9c35-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f9c35-178">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="f9c35-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="f9c35-179">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f9c35-179">The name and message are displayed on both pages instantly:</span></span>

   ![Die SignalR Blazor WebAssembly-Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="f9c35-181">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f9c35-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="f9c35-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f9c35-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="f9c35-183">Wenn VS Code anbietet, ein Startprofil für die Server-App zu erstellen ( *.vscode/launch.json*), scheint der `program`-Eintrag, ähnlich wie der folgende, auf die Assembly der App (`{APPLICATION NAME}.Server.dll`) zu zeigen:</span><span class="sxs-lookup"><span data-stu-id="f9c35-183">When VS Code offers to create a launch profile for the Server app (*.vscode/launch.json*), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="f9c35-184">Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f9c35-184">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f9c35-185">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="f9c35-185">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f9c35-186">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="f9c35-186">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="f9c35-187">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f9c35-187">The name and message are displayed on both pages instantly:</span></span>

   ![Die SignalR Blazor WebAssembly-Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="f9c35-189">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f9c35-189">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f9c35-190">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f9c35-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="f9c35-191">Wählen Sie in der Seitenleiste **Projektmappe** das Projekt **BlazorSignalRApp.Server** aus.</span><span class="sxs-lookup"><span data-stu-id="f9c35-191">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="f9c35-192">Drücken Sie <kbd>⌘</kbd>+<kbd>↩</kbd>\*\*, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, um die App ohne Debuggen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f9c35-192">Press <kbd>⌘</kbd>+<kbd>↩</kbd>\*\* to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="f9c35-193">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="f9c35-193">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f9c35-194">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="f9c35-194">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="f9c35-195">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f9c35-195">The name and message are displayed on both pages instantly:</span></span>

   ![Die SignalR Blazor WebAssembly-Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="f9c35-197">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f9c35-197">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f9c35-198">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="f9c35-198">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="f9c35-199">Führen Sie in einer Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="f9c35-199">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="f9c35-200">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="f9c35-200">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="f9c35-201">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="f9c35-201">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="f9c35-202">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f9c35-202">The name and message are displayed on both pages instantly:</span></span>

   ![Die SignalR Blazor WebAssembly-Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="f9c35-204">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="f9c35-204">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="f9c35-205">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="f9c35-205">Next steps</span></span>

<span data-ttu-id="f9c35-206">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="f9c35-206">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f9c35-207">Erstellen eines von Blazor WebAssembly gehosteten App-Projekts</span><span class="sxs-lookup"><span data-stu-id="f9c35-207">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="f9c35-208">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="f9c35-208">Add the SignalR client library</span></span>
> * <span data-ttu-id="f9c35-209">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="f9c35-209">Add a SignalR hub</span></span>
> * <span data-ttu-id="f9c35-210">Hinzufügen von SignalR-Diensten und eines Endpunkts zum SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="f9c35-210">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="f9c35-211">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="f9c35-211">Add Razor component code for chat</span></span>

<span data-ttu-id="f9c35-212">Weitere Informationen zum Erstellen von Blazor-Apps finden Sie in der Blazor-Dokumentation:</span><span class="sxs-lookup"><span data-stu-id="f9c35-212">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="f9c35-213">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f9c35-213">Additional resources</span></span>

* <xref:signalr/introduction>
