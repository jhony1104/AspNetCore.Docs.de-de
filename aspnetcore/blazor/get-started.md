---
title: Erste Schritte mit ASP.NET Core Blazor
author: guardrex
description: Erste Schritte mit Blazor durch Erstellen einer Blazor-App mit den Tools Ihrer Wahl.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 89c7529d2b8ec97db731f7c7268e19937c398115
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083241"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="ca5db-103">Erste Schritte mit ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ca5db-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="ca5db-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ca5db-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="ca5db-105">Erste Schritte mit Blazor:</span><span class="sxs-lookup"><span data-stu-id="ca5db-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="ca5db-106">Installieren Sie das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="ca5db-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="ca5db-107">Installieren Sie optional die Vorlage [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="ca5db-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="ca5db-108">Installieren Sie das [.NET Core-SDK 3.1.102 oder höher (Vorschau)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="ca5db-108">Install the [.NET Core 3.1.102 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="ca5db-109">Führen Sie den folgenden Befehl in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-109">Run the following command in a command shell.</span></span> <span data-ttu-id="ca5db-110">Das Paket [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) enthält eine Vorschauversion, solange sich die Blazor WebAssembly in der Vorschauphase befindet.</span><span class="sxs-lookup"><span data-stu-id="ca5db-110">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview2.20160.5
   ```

   > [!NOTE]
   > <span data-ttu-id="ca5db-111">Das .NET Core SDK 3.1.102 oder höher ist **erforderlich**, um die Blazor WebAssembly-Vorlage „3.2 Preview 2“ zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="ca5db-111">.NET Core SDK version 3.1.102 or later is **required** to use the 3.2 Preview 2 Blazor WebAssembly template.</span></span> <span data-ttu-id="ca5db-112">Überprüfen Sie die installierte Version des .NET Core-SDK, indem Sie `dotnet --version` in einer Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="ca5db-112">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="ca5db-113">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="ca5db-113">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studio"></a>[<span data-ttu-id="ca5db-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ca5db-114">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="ca5db-115">1\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-115">1\.</span></span> <span data-ttu-id="ca5db-116">Installieren Sie [Version 16.4 von Visual Studio 2019 oder höher](https://visualstudio.microsoft.com/vs/preview/) mit der Workload **ASP.NET und Webentwicklung**.</span><span class="sxs-lookup"><span data-stu-id="ca5db-116">Install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="ca5db-117">2\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-117">2\.</span></span> <span data-ttu-id="ca5db-118">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="ca5db-118">Create a new project.</span></span>

   <span data-ttu-id="ca5db-119">3\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-119">3\.</span></span> <span data-ttu-id="ca5db-120">Wählen Sie **Blazor-App** aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-120">Select **Blazor App**.</span></span> <span data-ttu-id="ca5db-121">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="ca5db-121">Select **Next**.</span></span>

   <span data-ttu-id="ca5db-122">4\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-122">4\.</span></span> <span data-ttu-id="ca5db-123">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="ca5db-123">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="ca5db-124">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="ca5db-124">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="ca5db-125">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-125">Select **Create**.</span></span>

   <span data-ttu-id="ca5db-126">5\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-126">5\.</span></span> <span data-ttu-id="ca5db-127">Für eine Blazor WebAssembly-Benutzeroberfläche wählen Sie die **Blazor WebAssembly App**-Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-127">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="ca5db-128">Für eine Blazor Server-Benutzeroberfläche wählen Sie die **Blazor Server App**-Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-128">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="ca5db-129">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-129">Select **Create**.</span></span> <span data-ttu-id="ca5db-130">Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="ca5db-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span> <span data-ttu-id="ca5db-131">Wenn die Blazor WebAssembly-Vorlage nicht vorhanden ist, kehren Sie zum vorherigen Schritt zurück, und installieren Sie die Vorlage erneut.</span><span class="sxs-lookup"><span data-stu-id="ca5db-131">If the Blazor WebAssembly template isn't present, return to the previous step and reinstall the template.</span></span>

   <span data-ttu-id="ca5db-132">6\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-132">6\.</span></span> <span data-ttu-id="ca5db-133">Drücken Sie **STRG**+**F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ca5db-133">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="ca5db-134">Wenn Sie die Blazor Visual Studio-Erweiterung für eine frühere Vorschauversion von ASP.NET Core Blazor (Vorschauversion 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren.</span><span class="sxs-lookup"><span data-stu-id="ca5db-134">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="ca5db-135">Die Installation der Blazor-Vorlagen in einer Befehlsshell ist jetzt dafür geeignet, um die Vorlagen in Visual Studio zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="ca5db-135">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-code"></a>[<span data-ttu-id="ca5db-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ca5db-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="ca5db-137">1\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-137">1\.</span></span> <span data-ttu-id="ca5db-138">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="ca5db-138">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="ca5db-139">2\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-139">2\.</span></span> <span data-ttu-id="ca5db-140">Installieren Sie die aktuellste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="ca5db-140">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

   <span data-ttu-id="ca5db-141">3\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-141">3\.</span></span> <span data-ttu-id="ca5db-142">Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="ca5db-142">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="ca5db-143">Für eine Blazor Server-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="ca5db-143">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="ca5db-144">Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="ca5db-144">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="ca5db-145">4\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-145">4\.</span></span> <span data-ttu-id="ca5db-146">Öffnen Sie in Visual Studio Code den Ordner *WebApplication1*.</span><span class="sxs-lookup"><span data-stu-id="ca5db-146">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="ca5db-147">5\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-147">5\.</span></span> <span data-ttu-id="ca5db-148">Bei einem Blazor Server-Projekt werden Sie von der IDE aufgefordert, Objekte hinzuzufügen, um das Projekt zu erstellen und zu debuggen.</span><span class="sxs-lookup"><span data-stu-id="ca5db-148">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="ca5db-149">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="ca5db-149">Select **Yes**.</span></span>

   <span data-ttu-id="ca5db-150">6\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-150">6\.</span></span> <span data-ttu-id="ca5db-151">Wenn Sie eine Blazor Server-App verwenden, führen Sie die App mit dem Visual Studio Code-Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-151">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="ca5db-152">Wenn Sie eine Blazor WebAssembly-App verwenden, führen Sie `dotnet run` über den Projektordner der App aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-152">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="ca5db-153">7\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-153">7\.</span></span> <span data-ttu-id="ca5db-154">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ca5db-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ca5db-155">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ca5db-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="ca5db-156">1\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-156">1\.</span></span> <span data-ttu-id="ca5db-157">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="ca5db-157">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="ca5db-158">2\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-158">2\.</span></span> <span data-ttu-id="ca5db-159">Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="ca5db-159">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="ca5db-160">3\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-160">3\.</span></span> <span data-ttu-id="ca5db-161">Wählen Sie in der Randleiste **.NET Core** > **App** aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-161">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="ca5db-162">4\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-162">4\.</span></span> <span data-ttu-id="ca5db-163">Wählen Sie die Vorlage **Blazor Server App** aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-163">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="ca5db-164">Derzeit ist nur die Blazor Server-Vorlage in Visual Studio für Mac verfügbar.</span><span class="sxs-lookup"><span data-stu-id="ca5db-164">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="ca5db-165">Für eine Blazor WebAssembly-Benutzeroberfläche folgen Sie den Anweisungen auf der Registerkarte **.NET Core CLI**. Nach Auswahl der Blazor Server-Vorlage wählen Sie **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-165">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="ca5db-166">Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="ca5db-166">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="ca5db-167">5\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-167">5\.</span></span> <span data-ttu-id="ca5db-168">Legen Sie das **Zielframework** auf **.NET Core 3.1** fest, und wählen Sie **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-168">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="ca5db-169">6\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-169">6\.</span></span> <span data-ttu-id="ca5db-170">Benennen Sie im Feld **Projektname** die App `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="ca5db-170">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="ca5db-171">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-171">Select **Create**.</span></span>

   <span data-ttu-id="ca5db-172">7\.</span><span class="sxs-lookup"><span data-stu-id="ca5db-172">7\.</span></span> <span data-ttu-id="ca5db-173">Wählen Sie **Ausführen** > **Ohne Debuggen ausführen**, um die App *ohne Debugger* auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ca5db-173">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="ca5db-174">Führen Sie die App mit **Debugging starten** aus, um die App *mit dem Debugger* auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ca5db-174">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="ca5db-175">Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="ca5db-175">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-cli"></a>[<span data-ttu-id="ca5db-176">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="ca5db-176">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="ca5db-177">Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="ca5db-177">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="ca5db-178">Für eine Blazor Server-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="ca5db-178">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="ca5db-179">Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="ca5db-179">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="ca5db-180">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="ca5db-180">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="ca5db-181">Mehrere Seiten sind über Registerkarten in der Randleiste verfügbar:</span><span class="sxs-lookup"><span data-stu-id="ca5db-181">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="ca5db-182">Startseite</span><span class="sxs-lookup"><span data-stu-id="ca5db-182">Home</span></span>
* <span data-ttu-id="ca5db-183">Zähler</span><span class="sxs-lookup"><span data-stu-id="ca5db-183">Counter</span></span>
* <span data-ttu-id="ca5db-184">Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="ca5db-184">Fetch data</span></span>

<span data-ttu-id="ca5db-185">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="ca5db-185">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="ca5db-186">Für das Heraufsetzen eines Zählers auf einer Webseite müssen Sie JavaScript-Code schreiben, aber mit Blazor können Sie C# verwenden.</span><span class="sxs-lookup"><span data-stu-id="ca5db-186">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="ca5db-187">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="ca5db-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="ca5db-188">Eine Anforderung für `/counter` im Browser, wie durch die `@page`-Direktive oben angegeben, veranlasst die `Counter`-Komponente, ihren Inhalt zu rendern.</span><span class="sxs-lookup"><span data-stu-id="ca5db-188">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="ca5db-189">Die Komponenten werden in einer In-Memory-Darstellung der Renderstruktur gerendert, die anschließend verwendet werden kann, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="ca5db-189">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="ca5db-190">Jedes Mal, wenn die **Hier klicken**-Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="ca5db-190">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="ca5db-191">Das `onclick`-Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="ca5db-191">The `onclick` event is fired.</span></span>
* <span data-ttu-id="ca5db-192">Die `IncrementCount` -Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="ca5db-192">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="ca5db-193">Der `currentCount` wird inkrementiert.</span><span class="sxs-lookup"><span data-stu-id="ca5db-193">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="ca5db-194">Die Komponente wird erneut gerendert.</span><span class="sxs-lookup"><span data-stu-id="ca5db-194">The component is rendered again.</span></span>

<span data-ttu-id="ca5db-195">Die Laufzeit vergleicht den neuen Inhalt mit dem bisherigen Inhalt und wendet nur den geänderten Inhalt auf das Dokumentobjektmodell (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="ca5db-195">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="ca5db-196">Fügen Sie eine Komponente mithilfe der HTML-Syntax zu einer anderen Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="ca5db-196">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="ca5db-197">Fügen Sie z. B. die `Counter`-Komponente zur Startseite der App hinzu, indem Sie der `Index`-Komponente ein `<Counter />`-Element hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="ca5db-197">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="ca5db-198">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="ca5db-198">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="ca5db-199">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-199">Run the app.</span></span> <span data-ttu-id="ca5db-200">Die Startseite verfügt über einen eigenen Zähler, der von der `Counter`-Komponente bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="ca5db-200">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="ca5db-201">Komponentenparameter werden mithilfe von Attributen oder [untergeordneten Inhalten](xref:blazor/components#child-content) angegeben, die es Ihnen ermöglichen, Eigenschaften für die untergeordnete Komponente festzulegen.</span><span class="sxs-lookup"><span data-stu-id="ca5db-201">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="ca5db-202">Um der `Counter`-Komponente einen Parameter hinzuzufügen, aktualisieren Sie den `@code`-Block der Komponente:</span><span class="sxs-lookup"><span data-stu-id="ca5db-202">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="ca5db-203">Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem `[Parameter]`-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="ca5db-203">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="ca5db-204">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="ca5db-204">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="ca5db-205">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="ca5db-205">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="ca5db-206">Geben Sie `IncrementAmount` im `<Counter>`-Element der `Index`-Komponente mit einem Attribut an.</span><span class="sxs-lookup"><span data-stu-id="ca5db-206">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="ca5db-207">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="ca5db-207">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="ca5db-208">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="ca5db-208">Run the app.</span></span> <span data-ttu-id="ca5db-209">Die Komponente `Index` verfügt über einen eigenen Zähler, der bei jeder Auswahl der Schaltfläche **Hier klicken** um zehn erhöht wird.</span><span class="sxs-lookup"><span data-stu-id="ca5db-209">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="ca5db-210">Die `Counter`-Komponente (*Counter.razor*) bei `/counter` wird weiterhin um eins erhöht.</span><span class="sxs-lookup"><span data-stu-id="ca5db-210">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ca5db-211">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="ca5db-211">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="ca5db-212">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ca5db-212">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
