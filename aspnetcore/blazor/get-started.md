---
title: Erste Schritte mit ASP.NET Core Blazor
author: guardrex
description: Erste Schritte mit Blazor durch Erstellen einer Blazor-App mit den Tools Ihrer Wahl.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111070"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="985fe-103">Erste Schritte mit ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="985fe-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="985fe-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="985fe-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="985fe-105">Führen Sie für Ihre ersten Schritte mit Blazor die Anleitung für Ihre bevorzugten Tools durch:</span><span class="sxs-lookup"><span data-stu-id="985fe-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="985fe-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="985fe-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="985fe-107">Installieren Sie die neueste Version von [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) mit der Workload **ASP.NET und Webentwicklung**, um Blazor Server-Apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="985fe-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="985fe-108">Installieren Sie die neueste Preview von [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) mit der Workload **ASP.NET und Webentwicklung**, um Blazor Server- und Blazor WebAssembly-Apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="985fe-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="985fe-109">Informationen zu den zwei Blazor-Hostingmodellen, *Blazor WebAssembly* und *Blazor Server*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="985fe-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="985fe-110">Installieren Sie die Vorschauvorlage [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="985fe-110">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. <span data-ttu-id="985fe-111">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="985fe-111">Create a new project.</span></span>

1. <span data-ttu-id="985fe-112">Wählen Sie **Blazor-App** aus.</span><span class="sxs-lookup"><span data-stu-id="985fe-112">Select **Blazor App**.</span></span> <span data-ttu-id="985fe-113">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="985fe-113">Select **Next**.</span></span>

1. <span data-ttu-id="985fe-114">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="985fe-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="985fe-115">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="985fe-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="985fe-116">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="985fe-116">Select **Create**.</span></span>

1. <span data-ttu-id="985fe-117">Für eine Blazor WebAssembly-Benutzeroberfläche (ab Visual Studio 16.6 Preview 2) wählen Sie die Vorlage **Blazor WebAssembly-App** aus.</span><span class="sxs-lookup"><span data-stu-id="985fe-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="985fe-118">Für eine Blazor Server-Benutzeroberfläche (ab Visual Studio 16.4) wählen Sie die Vorlage **Blazor Server-App** aus.</span><span class="sxs-lookup"><span data-stu-id="985fe-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="985fe-119">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="985fe-119">Select **Create**.</span></span>

1. <span data-ttu-id="985fe-120">Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="985fe-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="985fe-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="985fe-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="985fe-122">Installieren Sie das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="985fe-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="985fe-123">Installieren Sie optional die Vorschauvorlage [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="985fe-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="985fe-124">Das [.NET Core SDK 3.1.201 oder höher](https://dotnet.microsoft.com/download/dotnet-core/3.1) ist für die Verwendung der Vorlage für Blazor WebAssembly 3.2 Preview 4 **erforderlich**.</span><span class="sxs-lookup"><span data-stu-id="985fe-124">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="985fe-125">Überprüfen Sie die installierte Version des .NET Core-SDK, indem Sie `dotnet --version` in einer Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="985fe-125">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="985fe-126">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="985fe-126">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="985fe-127">Installieren Sie die neueste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) und die Erweiterung [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), und legen Sie für `debug.javascript.usePreview` den Wert `true` fest.</span><span class="sxs-lookup"><span data-stu-id="985fe-127">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="985fe-128">Für eine Blazor Server-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="985fe-128">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="985fe-129">Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="985fe-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="985fe-130">Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="985fe-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="985fe-131">Öffnen Sie in Visual Studio Code den Ordner *WebApplication1*.</span><span class="sxs-lookup"><span data-stu-id="985fe-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="985fe-132">Die IDE fordert an, dass Sie Ressourcen zum Erstellen und Debuggen des Projekts hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="985fe-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="985fe-133">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="985fe-133">Select **Yes**.</span></span>

1. <span data-ttu-id="985fe-134">Bei Blazor Server führen Sie die App mit dem Visual Studio Code-Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="985fe-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="985fe-135">Bei Blazor App starten Sie die App mithilfe der Startkonfiguration **.NET Core Launch (Blazor Standalone)** und starten dann den Browser mithilfe der Startkonfiguration **.NET Core Debug Blazor Web Assembly in Chrome** (Chrome erforderlich).</span><span class="sxs-lookup"><span data-stu-id="985fe-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="985fe-136">Weitere Informationen finden Sie unter <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="985fe-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="985fe-137">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="985fe-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="985fe-138">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="985fe-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="985fe-139">Blazor Server wird in Visual Studio für Mac unterstützt.</span><span class="sxs-lookup"><span data-stu-id="985fe-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="985fe-140">Blazor WebAssembly wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="985fe-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="985fe-141">Befolgen Sie die Anweisungen auf der Registerkarte **.NET Core-CLI**, um Blazor WebAssembly-Apps unter macOS zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="985fe-141">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="985fe-142">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="985fe-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="985fe-143">Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="985fe-143">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="985fe-144">Wählen Sie in der Randleiste **.NET Core** > **App** aus.</span><span class="sxs-lookup"><span data-stu-id="985fe-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="985fe-145">Wählen Sie die Vorlage **Blazor Server App** aus.</span><span class="sxs-lookup"><span data-stu-id="985fe-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="985fe-146">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="985fe-146">Select **Create**.</span></span>

   <span data-ttu-id="985fe-147">Weitere Informationen zum Blazor Server-Hostingmodell finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="985fe-147">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="985fe-148">Legen Sie das **Zielframework** auf **.NET Core 3.1** fest, und wählen Sie **Weiter** aus.</span><span class="sxs-lookup"><span data-stu-id="985fe-148">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="985fe-149">Benennen Sie im Feld **Projektname** die App `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="985fe-149">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="985fe-150">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="985fe-150">Select **Create**.</span></span>

1. <span data-ttu-id="985fe-151">Wählen Sie **Ausführen** > **Ohne Debuggen ausführen**, um die App *ohne Debugger* auszuführen.</span><span class="sxs-lookup"><span data-stu-id="985fe-151">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="985fe-152">Führen Sie die App mit **Debugging starten** aus, um die App *mit dem Debugger* auszuführen.</span><span class="sxs-lookup"><span data-stu-id="985fe-152">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="985fe-153">Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="985fe-153">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="985fe-154">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="985fe-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="985fe-155">Installieren Sie das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="985fe-155">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="985fe-156">Installieren Sie optional die Vorschauvorlage [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="985fe-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="985fe-157">Das [.NET Core SDK 3.1.201 oder höher](https://dotnet.microsoft.com/download/dotnet-core/3.1) ist für die Verwendung der Vorlage für Blazor WebAssembly 3.2 Preview 4 **erforderlich**.</span><span class="sxs-lookup"><span data-stu-id="985fe-157">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="985fe-158">Überprüfen Sie die installierte Version des .NET Core-SDK, indem Sie `dotnet --version` in einer Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="985fe-158">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="985fe-159">Für eine Blazor Server-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="985fe-159">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="985fe-160">Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="985fe-160">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="985fe-161">Informationen zu den beiden Blazor-Hostingmodellen, *Blazor Server* und *Blazor WebAssembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="985fe-161">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="985fe-162">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="985fe-162">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="985fe-163">Mehrere Seiten sind über Registerkarten in der Randleiste verfügbar:</span><span class="sxs-lookup"><span data-stu-id="985fe-163">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="985fe-164">Startseite</span><span class="sxs-lookup"><span data-stu-id="985fe-164">Home</span></span>
* <span data-ttu-id="985fe-165">Zähler</span><span class="sxs-lookup"><span data-stu-id="985fe-165">Counter</span></span>
* <span data-ttu-id="985fe-166">Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="985fe-166">Fetch data</span></span>

<span data-ttu-id="985fe-167">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="985fe-167">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="985fe-168">Für das Heraufsetzen eines Zählers auf einer Webseite müssen Sie JavaScript-Code schreiben, aber mit Blazor können Sie C# verwenden.</span><span class="sxs-lookup"><span data-stu-id="985fe-168">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="985fe-169">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="985fe-169">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="985fe-170">Eine Anforderung für `/counter` im Browser, wie durch die `@page`-Direktive oben angegeben, veranlasst die `Counter`-Komponente, ihren Inhalt zu rendern.</span><span class="sxs-lookup"><span data-stu-id="985fe-170">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="985fe-171">Die Komponenten werden in einer In-Memory-Darstellung der Renderstruktur gerendert, die anschließend verwendet werden kann, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="985fe-171">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="985fe-172">Jedes Mal, wenn die **Hier klicken**-Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="985fe-172">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="985fe-173">Das `onclick`-Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="985fe-173">The `onclick` event is fired.</span></span>
* <span data-ttu-id="985fe-174">Die `IncrementCount` -Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="985fe-174">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="985fe-175">Der `currentCount` wird inkrementiert.</span><span class="sxs-lookup"><span data-stu-id="985fe-175">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="985fe-176">Die Komponente wird erneut gerendert.</span><span class="sxs-lookup"><span data-stu-id="985fe-176">The component is rendered again.</span></span>

<span data-ttu-id="985fe-177">Die Laufzeit vergleicht den neuen Inhalt mit dem bisherigen Inhalt und wendet nur den geänderten Inhalt auf das Dokumentobjektmodell (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="985fe-177">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="985fe-178">Fügen Sie eine Komponente mithilfe der HTML-Syntax zu einer anderen Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="985fe-178">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="985fe-179">Fügen Sie z. B. die `Counter`-Komponente zur Startseite der App hinzu, indem Sie der `Index`-Komponente ein `<Counter />`-Element hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="985fe-179">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="985fe-180">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="985fe-180">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="985fe-181">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="985fe-181">Run the app.</span></span> <span data-ttu-id="985fe-182">Die Startseite verfügt über einen eigenen Zähler, der von der `Counter`-Komponente bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="985fe-182">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="985fe-183">Komponentenparameter werden mithilfe von Attributen oder [untergeordneten Inhalten](xref:blazor/components#child-content) angegeben, die es Ihnen ermöglichen, Eigenschaften für die untergeordnete Komponente festzulegen.</span><span class="sxs-lookup"><span data-stu-id="985fe-183">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="985fe-184">Um der `Counter`-Komponente einen Parameter hinzuzufügen, aktualisieren Sie den `@code`-Block der Komponente:</span><span class="sxs-lookup"><span data-stu-id="985fe-184">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="985fe-185">Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem `[Parameter]`-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="985fe-185">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="985fe-186">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="985fe-186">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="985fe-187">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="985fe-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="985fe-188">Geben Sie `IncrementAmount` im `<Counter>`-Element der `Index`-Komponente mit einem Attribut an.</span><span class="sxs-lookup"><span data-stu-id="985fe-188">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="985fe-189">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="985fe-189">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="985fe-190">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="985fe-190">Run the app.</span></span> <span data-ttu-id="985fe-191">Die Komponente `Index` verfügt über einen eigenen Zähler, der bei jeder Auswahl der Schaltfläche **Hier klicken** um zehn erhöht wird.</span><span class="sxs-lookup"><span data-stu-id="985fe-191">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="985fe-192">Die `Counter`-Komponente (*Counter.razor*) bei `/counter` wird weiterhin um eins erhöht.</span><span class="sxs-lookup"><span data-stu-id="985fe-192">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="985fe-193">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="985fe-193">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="985fe-194">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="985fe-194">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
