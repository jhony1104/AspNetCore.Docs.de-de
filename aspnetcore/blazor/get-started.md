---
title: Erste Schritte mit ASP.NET Core Blazor
author: guardrex
description: Erste Schritte mit Blazor durch Erstellen einer Blazor-App mit den Tools Ihrer Wahl.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/28/2019
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: bd33d874b3d6122f2ab820e9b147b0e62ba03a58
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648631"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="826c1-103">Erste Schritte mit ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="826c1-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="826c1-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="826c1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="826c1-105">Erste Schritte mit Blazor:</span><span class="sxs-lookup"><span data-stu-id="826c1-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="826c1-106">Installieren Sie das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="826c1-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="826c1-107">Installieren Sie optional die Vorlage [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="826c1-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="826c1-108">Installieren Sie das [SDK für .NET Core 3.1 oder höher (Vorschau)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="826c1-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="826c1-109">Führen Sie den folgenden Befehl in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-109">Run the following command in a command shell.</span></span> <span data-ttu-id="826c1-110">Das Paket [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) enthält eine Vorschauversion, solange sich die Blazor WebAssembly in der Vorschauphase befindet.</span><span class="sxs-lookup"><span data-stu-id="826c1-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.2.0-preview1.20073.1
   ```

1. <span data-ttu-id="826c1-111">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="826c1-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studio"></a>[<span data-ttu-id="826c1-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="826c1-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="826c1-113">1\.</span><span class="sxs-lookup"><span data-stu-id="826c1-113">1\.</span></span> <span data-ttu-id="826c1-114">Installieren Sie [Version 16.4 von Visual Studio 2019 oder höher](https://visualstudio.microsoft.com/vs/preview/) mit der Workload **ASP.NET und Webentwicklung**.</span><span class="sxs-lookup"><span data-stu-id="826c1-114">Install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="826c1-115">2\.</span><span class="sxs-lookup"><span data-stu-id="826c1-115">2\.</span></span> <span data-ttu-id="826c1-116">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="826c1-116">Create a new project.</span></span>

   <span data-ttu-id="826c1-117">3\.</span><span class="sxs-lookup"><span data-stu-id="826c1-117">3\.</span></span> <span data-ttu-id="826c1-118">Wählen Sie **Blazor-App** aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-118">Select **Blazor App**.</span></span> <span data-ttu-id="826c1-119">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="826c1-119">Select **Next**.</span></span>

   <span data-ttu-id="826c1-120">4\.</span><span class="sxs-lookup"><span data-stu-id="826c1-120">4\.</span></span> <span data-ttu-id="826c1-121">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="826c1-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="826c1-122">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="826c1-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="826c1-123">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-123">Select **Create**.</span></span>

   <span data-ttu-id="826c1-124">5\.</span><span class="sxs-lookup"><span data-stu-id="826c1-124">5\.</span></span> <span data-ttu-id="826c1-125">Für eine Blazor WebAssembly-Benutzeroberfläche wählen Sie die **Blazor WebAssembly App**-Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="826c1-126">Für eine Blazor Server-Benutzeroberfläche wählen Sie die **Blazor Server App**-Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="826c1-127">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-127">Select **Create**.</span></span> <span data-ttu-id="826c1-128">Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="826c1-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="826c1-129">6\.</span><span class="sxs-lookup"><span data-stu-id="826c1-129">6\.</span></span> <span data-ttu-id="826c1-130">Drücken Sie **STRG**+**F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="826c1-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="826c1-131">Wenn Sie die Blazor Visual Studio-Erweiterung für eine frühere Vorschauversion von ASP.NET Core Blazor (Vorschauversion 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren.</span><span class="sxs-lookup"><span data-stu-id="826c1-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="826c1-132">Die Installation der Blazor-Vorlagen in einer Befehlsshell ist jetzt dafür geeignet, um die Vorlagen in Visual Studio zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="826c1-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-code"></a>[<span data-ttu-id="826c1-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="826c1-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="826c1-134">1\.</span><span class="sxs-lookup"><span data-stu-id="826c1-134">1\.</span></span> <span data-ttu-id="826c1-135">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="826c1-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="826c1-136">2\.</span><span class="sxs-lookup"><span data-stu-id="826c1-136">2\.</span></span> <span data-ttu-id="826c1-137">Installieren Sie die aktuellste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="826c1-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="826c1-138">3\.</span><span class="sxs-lookup"><span data-stu-id="826c1-138">3\.</span></span> <span data-ttu-id="826c1-139">Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="826c1-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="826c1-140">Für eine Blazor Server-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="826c1-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="826c1-141">Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="826c1-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="826c1-142">4\.</span><span class="sxs-lookup"><span data-stu-id="826c1-142">4\.</span></span> <span data-ttu-id="826c1-143">Öffnen Sie in Visual Studio Code den Ordner *WebApplication1*.</span><span class="sxs-lookup"><span data-stu-id="826c1-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="826c1-144">5\.</span><span class="sxs-lookup"><span data-stu-id="826c1-144">5\.</span></span> <span data-ttu-id="826c1-145">Bei einem Blazor Server-Projekt werden Sie von der IDE aufgefordert, Objekte hinzuzufügen, um das Projekt zu erstellen und zu debuggen.</span><span class="sxs-lookup"><span data-stu-id="826c1-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="826c1-146">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="826c1-146">Select **Yes**.</span></span>

   <span data-ttu-id="826c1-147">6\.</span><span class="sxs-lookup"><span data-stu-id="826c1-147">6\.</span></span> <span data-ttu-id="826c1-148">Wenn Sie eine Blazor Server-App verwenden, führen Sie die App mit dem Visual Studio Code-Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="826c1-149">Wenn Sie eine Blazor WebAssembly-App verwenden, führen Sie `dotnet run` über den Projektordner der App aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="826c1-150">7\.</span><span class="sxs-lookup"><span data-stu-id="826c1-150">7\.</span></span> <span data-ttu-id="826c1-151">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="826c1-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="826c1-152">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="826c1-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="826c1-153">1\.</span><span class="sxs-lookup"><span data-stu-id="826c1-153">1\.</span></span> <span data-ttu-id="826c1-154">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="826c1-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="826c1-155">2\.</span><span class="sxs-lookup"><span data-stu-id="826c1-155">2\.</span></span> <span data-ttu-id="826c1-156">Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="826c1-156">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="826c1-157">3\.</span><span class="sxs-lookup"><span data-stu-id="826c1-157">3\.</span></span> <span data-ttu-id="826c1-158">Wählen Sie in der Randleiste **.NET Core** > **App** aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-158">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="826c1-159">4\.</span><span class="sxs-lookup"><span data-stu-id="826c1-159">4\.</span></span> <span data-ttu-id="826c1-160">Wählen Sie die Vorlage **Blazor Server App** aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-160">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="826c1-161">Derzeit ist nur die Blazor Server-Vorlage in Visual Studio für Mac verfügbar.</span><span class="sxs-lookup"><span data-stu-id="826c1-161">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="826c1-162">Für eine Blazor WebAssembly-Benutzeroberfläche folgen Sie den Anweisungen auf der Registerkarte **.NET Core CLI**. Nach Auswahl der Blazor Server-Vorlage wählen Sie **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-162">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="826c1-163">Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="826c1-163">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="826c1-164">5\.</span><span class="sxs-lookup"><span data-stu-id="826c1-164">5\.</span></span> <span data-ttu-id="826c1-165">Legen Sie das **Zielframework** auf **.NET Core 3.1** fest, und wählen Sie **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-165">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="826c1-166">6\.</span><span class="sxs-lookup"><span data-stu-id="826c1-166">6\.</span></span> <span data-ttu-id="826c1-167">Benennen Sie im Feld **Projektname** die App `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="826c1-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="826c1-168">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-168">Select **Create**.</span></span>

   <span data-ttu-id="826c1-169">7\.</span><span class="sxs-lookup"><span data-stu-id="826c1-169">7\.</span></span> <span data-ttu-id="826c1-170">Wählen Sie **Ausführen** > **Ohne Debuggen ausführen**, um die App *ohne Debugger* auszuführen.</span><span class="sxs-lookup"><span data-stu-id="826c1-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="826c1-171">Führen Sie die App mit **Debugging starten** aus, um die App *mit dem Debugger* auszuführen.</span><span class="sxs-lookup"><span data-stu-id="826c1-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="826c1-172">Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="826c1-172">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-cli"></a>[<span data-ttu-id="826c1-173">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="826c1-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="826c1-174">Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="826c1-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="826c1-175">Für eine Blazor Server-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="826c1-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="826c1-176">Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="826c1-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="826c1-177">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="826c1-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="826c1-178">Mehrere Seiten sind über Registerkarten in der Randleiste verfügbar:</span><span class="sxs-lookup"><span data-stu-id="826c1-178">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="826c1-179">Startseite</span><span class="sxs-lookup"><span data-stu-id="826c1-179">Home</span></span>
* <span data-ttu-id="826c1-180">Zähler</span><span class="sxs-lookup"><span data-stu-id="826c1-180">Counter</span></span>
* <span data-ttu-id="826c1-181">Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="826c1-181">Fetch data</span></span>

<span data-ttu-id="826c1-182">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="826c1-182">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="826c1-183">Für das Heraufsetzen eines Zählers auf einer Webseite müssen Sie JavaScript-Code schreiben, aber mit Blazor können Sie C# verwenden.</span><span class="sxs-lookup"><span data-stu-id="826c1-183">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="826c1-184">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="826c1-184">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="826c1-185">Eine Anforderung für `/counter` im Browser, wie durch die `@page`-Direktive oben angegeben, veranlasst die `Counter`-Komponente, ihren Inhalt zu rendern.</span><span class="sxs-lookup"><span data-stu-id="826c1-185">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="826c1-186">Die Komponenten werden in einer In-Memory-Darstellung der Renderstruktur gerendert, die anschließend verwendet werden kann, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="826c1-186">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="826c1-187">Jedes Mal, wenn die **Hier klicken**-Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="826c1-187">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="826c1-188">Das `onclick`-Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="826c1-188">The `onclick` event is fired.</span></span>
* <span data-ttu-id="826c1-189">Die `IncrementCount` -Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="826c1-189">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="826c1-190">Der `currentCount` wird inkrementiert.</span><span class="sxs-lookup"><span data-stu-id="826c1-190">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="826c1-191">Die Komponente wird erneut gerendert.</span><span class="sxs-lookup"><span data-stu-id="826c1-191">The component is rendered again.</span></span>

<span data-ttu-id="826c1-192">Die Laufzeit vergleicht den neuen Inhalt mit dem bisherigen Inhalt und wendet nur den geänderten Inhalt auf das Dokumentobjektmodell (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="826c1-192">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="826c1-193">Fügen Sie eine Komponente mithilfe der HTML-Syntax zu einer anderen Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="826c1-193">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="826c1-194">Fügen Sie z. B. die `Counter`-Komponente zur Startseite der App hinzu, indem Sie der `Index`-Komponente ein `<Counter />`-Element hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="826c1-194">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="826c1-195">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="826c1-195">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="826c1-196">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-196">Run the app.</span></span> <span data-ttu-id="826c1-197">Die Startseite verfügt über einen eigenen Zähler, der von der `Counter`-Komponente bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="826c1-197">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="826c1-198">Komponentenparameter werden mithilfe von Attributen oder [untergeordneten Inhalten](xref:blazor/components#child-content) angegeben, die es Ihnen ermöglichen, Eigenschaften für die untergeordnete Komponente festzulegen.</span><span class="sxs-lookup"><span data-stu-id="826c1-198">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="826c1-199">Um der `Counter`-Komponente einen Parameter hinzuzufügen, aktualisieren Sie den `@code`-Block der Komponente:</span><span class="sxs-lookup"><span data-stu-id="826c1-199">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="826c1-200">Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem `[Parameter]`-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="826c1-200">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="826c1-201">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="826c1-201">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="826c1-202">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="826c1-202">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="826c1-203">Geben Sie `IncrementAmount` im `<Counter>`-Element der `Index`-Komponente mit einem Attribut an.</span><span class="sxs-lookup"><span data-stu-id="826c1-203">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="826c1-204">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="826c1-204">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="826c1-205">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="826c1-205">Run the app.</span></span> <span data-ttu-id="826c1-206">Die Komponente `Index` verfügt über einen eigenen Zähler, der bei jeder Auswahl der Schaltfläche **Hier klicken** um zehn erhöht wird.</span><span class="sxs-lookup"><span data-stu-id="826c1-206">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="826c1-207">Die `Counter`-Komponente (*Counter.razor*) bei `/counter` wird weiterhin um eins erhöht.</span><span class="sxs-lookup"><span data-stu-id="826c1-207">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="826c1-208">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="826c1-208">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="826c1-209">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="826c1-209">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
