---
title: Verwenden von ASP.net Core SignalR mit Blazor WebAssembly
author: guardrex
description: Erstellen Sie eine Chat-App, die ASP.NET Core SignalR mit Blazor WebAssembly verwendet.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/31/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 605cf8ebd3e85586f3e479c815f0b9902ce5a91a
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083382"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="2af07-103">Verwenden von ASP.NET Core SignalR mit Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2af07-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="2af07-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2af07-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2af07-105">In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR mit Blazor WebAssembly beschrieben.</span><span class="sxs-lookup"><span data-stu-id="2af07-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="2af07-106">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2af07-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2af07-107">Erstellen eines von Blazor WebAssembly gehosteten App-Projekts</span><span class="sxs-lookup"><span data-stu-id="2af07-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="2af07-108">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="2af07-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="2af07-109">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="2af07-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="2af07-110">Hinzufügen von SignalR-Diensten und eines Endpunkts für den SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="2af07-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="2af07-111">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="2af07-111">Add Razor component code for chat</span></span>

<span data-ttu-id="2af07-112">Am Ende dieses Tutorials verfügen Sie über eine funktionierende Chat-App.</span><span class="sxs-lookup"><span data-stu-id="2af07-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="2af07-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2af07-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2af07-114">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="2af07-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2af07-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2af07-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="2af07-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2af07-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2af07-117">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="2af07-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="2af07-118">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2af07-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="2af07-119">Erstellen eines gehosteten Blazor WebAssembly-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="2af07-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="2af07-120">Installieren Sie die Vorlage [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly).</span><span class="sxs-lookup"><span data-stu-id="2af07-120">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="2af07-121">Das Paket [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) enthält eine Vorschauversion, solange sich die Blazor WebAssembly in der Vorschauphase befindet.</span><span class="sxs-lookup"><span data-stu-id="2af07-121">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="2af07-122">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="2af07-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview2.20160.5
```

<span data-ttu-id="2af07-123">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="2af07-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2af07-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2af07-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2af07-125">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="2af07-125">Create a new project.</span></span>

1. <span data-ttu-id="2af07-126">Wählen Sie **Blazor-App** und dann **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="2af07-127">Geben Sie in das Feld **Projektname** „BlazorSignalRApp“ ein.</span><span class="sxs-lookup"><span data-stu-id="2af07-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="2af07-128">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="2af07-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="2af07-129">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-129">Select **Create**.</span></span>

1. <span data-ttu-id="2af07-130">Wählen Sie die Vorlage **Blazor WebAssembly App** aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="2af07-131">Aktivieren Sie unter **Erweitert** das Kontrollkästchen **Von ASP.net Core gehostet**.</span><span class="sxs-lookup"><span data-stu-id="2af07-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="2af07-132">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="2af07-133">Wenn Sie eine neue Version von Visual Studio aktualisiert oder installiert haben und die Blazor WebAssembly-Vorlage nicht in der VS-Benutzeroberfläche angezeigt wird, installieren Sie die Vorlage mit dem zuvor gezeigten Befehl `dotnet new` neu.</span><span class="sxs-lookup"><span data-stu-id="2af07-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2af07-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2af07-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2af07-135">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="2af07-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="2af07-136">Öffnen Sie in Visual Studio Code den Projektordner der App.</span><span class="sxs-lookup"><span data-stu-id="2af07-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="2af07-137">Wenn das Dialogfeld angezeigt wird, um Assets zum Erstellen und Debuggen der App hinzuzufügen, wählen Sie **Ja**aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="2af07-138">Visual Studio Code fügt automatisch den Ordner *.vscode* mit den generierten Dateien *launch.json* und *tasks.json* hinzu.</span><span class="sxs-lookup"><span data-stu-id="2af07-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2af07-139">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="2af07-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2af07-140">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="2af07-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="2af07-141">Öffnen Sie das Projekt in Visual Studio für Mac, indem Sie zum Projektordner navigieren und die Projektmappendatei des Projekts öffnen ( *.sln*).</span><span class="sxs-lookup"><span data-stu-id="2af07-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2af07-142">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2af07-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="2af07-143">Führen Sie in einer Befehlsshell den folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="2af07-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="2af07-144">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="2af07-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2af07-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2af07-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="2af07-146">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **BlazorSignalRApp.Client**, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="2af07-147">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die **Paketquelle** auf *nuget.org* eingestellt ist.</span><span class="sxs-lookup"><span data-stu-id="2af07-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="2af07-148">Wenn **Durchsuchen** ausgewählt ist, geben Sie „Microsoft.AspNetCore.SignalR.Client“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="2af07-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="2af07-149">Wählen Sie in den Suchergebnissen das Paket `Microsoft.AspNetCore.SignalR.Client` aus, und wählen Sie **Installation** aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="2af07-150">Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="2af07-151">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="2af07-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2af07-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2af07-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="2af07-153">Führen Sie im **integrierten Terminal** (**Ansicht** > **Terminal** in der Symbolleiste) die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="2af07-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2af07-154">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="2af07-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2af07-155">Klicken Sie in der Seitenleiste **Projektmappe** mit der rechten Maustaste auf **BlazorSignalRApp.Client**, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="2af07-156">Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die in der Dropdown-Liste für die Paketquelle *nuget.org* festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="2af07-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="2af07-157">Wenn **Durchsuchen** ausgewählt ist, geben Sie „Microsoft.AspNetCore.SignalR.Client“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="2af07-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="2af07-158">Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem `Microsoft.AspNetCore.SignalR.Client`-Paket, und wählen Sie **Paket hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="2af07-159">Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.</span><span class="sxs-lookup"><span data-stu-id="2af07-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2af07-160">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2af07-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="2af07-161">Führen Sie in einer Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="2af07-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="2af07-162">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="2af07-162">Add a SignalR hub</span></span>

<span data-ttu-id="2af07-163">Erstellen Sie im Projekt **BlazorSignalRApp.Server** den Ordner *Hubs* (Plural), und fügen Sie die folgende `ChatHub`-Klasse (*Hubs/ChatHub.cs*) hinzu:</span><span class="sxs-lookup"><span data-stu-id="2af07-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="2af07-164">Hinzufügen von SignalR-Diensten und eines Endpunkts für den SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="2af07-164">Add SignalR services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="2af07-165">Öffnen Sie im Projekt **BlazorSignalRApp.Server** die Datei *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="2af07-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="2af07-166">Fügen Sie am Anfang der Datei den Namespace für die `ChatHub`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="2af07-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="2af07-167">Fügen Sie die SignalR-Dienste hinzu zu `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="2af07-167">Add the SignalR services to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddSignalR();
   ```

1. <span data-ttu-id="2af07-168">Fügen Sie in `Startup.Configure` zwischen den Endpunkten für die standardmäßige Controllerroute und dem clientseitigen Fallback einen Endpunkt für den Hub hinzu:</span><span class="sxs-lookup"><span data-stu-id="2af07-168">In `Startup.Configure` between the endpoints for the default controller route and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="2af07-169">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="2af07-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="2af07-170">Öffnen Sie im Projekt **BlazorSignalRApp.Client** die Datei *Pages/Index.razor*.</span><span class="sxs-lookup"><span data-stu-id="2af07-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="2af07-171">Ersetzen Sie das Markup durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="2af07-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="2af07-172">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="2af07-172">Run the app</span></span>

1. <span data-ttu-id="2af07-173">Befolgen Sie die Anleitungen für die Auswahl Ihrer Tools:</span><span class="sxs-lookup"><span data-stu-id="2af07-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2af07-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2af07-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="2af07-175">Wählen Sie im **Projektmappen-Explorer** das Projekt **BlazorSignalRApp.Server** aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="2af07-176">Drücken Sie **STRG+F5**, um die App ohne Debuggen zu starten.</span><span class="sxs-lookup"><span data-stu-id="2af07-176">Press **Ctrl+F5** to run the app without debugging.</span></span>

1. <span data-ttu-id="2af07-177">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="2af07-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2af07-178">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="2af07-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="2af07-179">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="2af07-179">The name and message are displayed on both pages instantly:</span></span>

   ![Die SignalR Blazor WebAssembly-Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="2af07-181">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2af07-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2af07-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2af07-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="2af07-183">Wählen Sie aus der Symbolleiste **Debuggen** > **Ohne Debuggen ausführen** aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-183">Select **Debug** > **Run Without Debugging** from the toolbar.</span></span>

1. <span data-ttu-id="2af07-184">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="2af07-184">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2af07-185">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="2af07-185">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="2af07-186">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="2af07-186">The name and message are displayed on both pages instantly:</span></span>

   ![Die SignalR Blazor WebAssembly-Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="2af07-188">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2af07-188">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2af07-189">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="2af07-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="2af07-190">Wählen Sie in der Seitenleiste **Projektmappe** das Projekt **BlazorSignalRApp.Server** aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-190">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="2af07-191">Wählen Sie im Menü **Ausführen** > **Starten ohne Debuggen** aus.</span><span class="sxs-lookup"><span data-stu-id="2af07-191">From the menu, select **Run** > **Start Without Debugging**.</span></span>

1. <span data-ttu-id="2af07-192">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="2af07-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2af07-193">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="2af07-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="2af07-194">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="2af07-194">The name and message are displayed on both pages instantly:</span></span>

   ![Die SignalR Blazor WebAssembly-Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="2af07-196">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2af07-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="2af07-197">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2af07-197">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="2af07-198">Führen Sie in einer Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="2af07-198">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="2af07-199">Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="2af07-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="2af07-200">Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="2af07-200">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="2af07-201">Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:</span><span class="sxs-lookup"><span data-stu-id="2af07-201">The name and message are displayed on both pages instantly:</span></span>

   ![Die SignalR Blazor WebAssembly-Beispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="2af07-203">Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="2af07-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="2af07-204">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="2af07-204">Next steps</span></span>

<span data-ttu-id="2af07-205">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="2af07-205">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2af07-206">Erstellen eines von Blazor WebAssembly gehosteten App-Projekts</span><span class="sxs-lookup"><span data-stu-id="2af07-206">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="2af07-207">Hinzufügen der SignalR-Clientbibliothek</span><span class="sxs-lookup"><span data-stu-id="2af07-207">Add the SignalR client library</span></span>
> * <span data-ttu-id="2af07-208">Hinzufügen eines SignalR-Hubs</span><span class="sxs-lookup"><span data-stu-id="2af07-208">Add a SignalR hub</span></span>
> * <span data-ttu-id="2af07-209">Hinzufügen von SignalR-Diensten und eines Endpunkts zum SignalR-Hub</span><span class="sxs-lookup"><span data-stu-id="2af07-209">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="2af07-210">Hinzufügen von Razor-Komponentencode für Chat</span><span class="sxs-lookup"><span data-stu-id="2af07-210">Add Razor component code for chat</span></span>

<span data-ttu-id="2af07-211">Weitere Informationen zum Erstellen von Blazor-Apps finden Sie in der Blazor-Dokumentation:</span><span class="sxs-lookup"><span data-stu-id="2af07-211">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="2af07-212">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2af07-212">Additional resources</span></span>

* <xref:signalr/introduction>
