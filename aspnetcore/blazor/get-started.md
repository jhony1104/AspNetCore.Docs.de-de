---
title: Erste Schritte mit ASP.NET Core Blazor
author: guardrex
description: Erste Schritte mit Blazor durch Erstellen einer Blazor-App mit den Tools Ihrer Wahl.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 052a787fbe6411dbaa953f10fcd982dfbd41f1af
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82769454"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="11ab4-103">Erste Schritte mit ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="11ab4-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="11ab4-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="11ab4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="11ab4-105">Führen Sie für Ihre ersten Schritte mit Blazor die Anleitung für Ihre bevorzugten Tools durch:</span><span class="sxs-lookup"><span data-stu-id="11ab4-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="11ab4-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="11ab4-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="11ab4-107">Installieren Sie die neueste Version von [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) mit der Workload **ASP.NET und Webentwicklung**, um Blazor Server-Apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="11ab4-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="11ab4-108">Installieren Sie die neueste Preview von [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) mit der Workload **ASP.NET und Webentwicklung**, um Blazor Server- und Blazor WebAssembly-Apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="11ab4-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="11ab4-109">Informationen zu den zwei Blazor-Hostingmodellen, *Blazor WebAssembly* und *Blazor Server*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="11ab4-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="11ab4-110">Installieren Sie die Vorlage Blazor WebAssembly Preview, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="11ab4-110">Install the Blazor WebAssembly Preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```

1. <span data-ttu-id="11ab4-111">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="11ab4-111">Create a new project.</span></span>

1. <span data-ttu-id="11ab4-112">Wählen Sie **Blazor-App** aus.</span><span class="sxs-lookup"><span data-stu-id="11ab4-112">Select **Blazor App**.</span></span> <span data-ttu-id="11ab4-113">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="11ab4-113">Select **Next**.</span></span>

1. <span data-ttu-id="11ab4-114">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="11ab4-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="11ab4-115">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="11ab4-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="11ab4-116">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="11ab4-116">Select **Create**.</span></span>

1. <span data-ttu-id="11ab4-117">Für eine Blazor WebAssembly-Benutzeroberfläche (ab Visual Studio 16.6 Preview 2) wählen Sie die Vorlage **Blazor WebAssembly-App** aus.</span><span class="sxs-lookup"><span data-stu-id="11ab4-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="11ab4-118">Für eine Blazor Server-Benutzeroberfläche (ab Visual Studio 16.4) wählen Sie die Vorlage **Blazor Server-App** aus.</span><span class="sxs-lookup"><span data-stu-id="11ab4-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="11ab4-119">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="11ab4-119">Select **Create**.</span></span>

1. <span data-ttu-id="11ab4-120">Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="11ab4-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="11ab4-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="11ab4-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="11ab4-122">Installieren Sie das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="11ab4-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="11ab4-123">Installieren Sie optional die Vorschauvorlage [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly), indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="11ab4-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="11ab4-124">Informationen zu den zwei Blazor-Hostingmodellen, *Blazor WebAssembly* und *Blazor Server*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="11ab4-124">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="11ab4-125">Das [.NET Core SDK 3.1.201 oder höher](https://dotnet.microsoft.com/download/dotnet-core/3.1) ist für die Verwendung der Vorlage für Blazor WebAssembly 3.2 Preview **erforderlich**.</span><span class="sxs-lookup"><span data-stu-id="11ab4-125">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="11ab4-126">Überprüfen Sie die installierte Version des .NET Core-SDK, indem Sie `dotnet --version` in einer Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="11ab4-126">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="11ab4-127">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="11ab4-127">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="11ab4-128">Installieren Sie die neueste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) und die Erweiterung [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), und legen Sie für `debug.javascript.usePreview` den Wert `true` fest.</span><span class="sxs-lookup"><span data-stu-id="11ab4-128">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="11ab4-129">Für eine Blazor Server-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="11ab4-129">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="11ab4-130">Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="11ab4-130">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

1. <span data-ttu-id="11ab4-131">Öffnen Sie in Visual Studio Code den Ordner *WebApplication1*.</span><span class="sxs-lookup"><span data-stu-id="11ab4-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="11ab4-132">Die IDE fordert an, dass Sie Ressourcen zum Erstellen und Debuggen des Projekts hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="11ab4-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="11ab4-133">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="11ab4-133">Select **Yes**.</span></span>

1. <span data-ttu-id="11ab4-134">Bei Blazor Server führen Sie die App mit dem Visual Studio Code-Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="11ab4-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="11ab4-135">Bei Blazor App starten Sie die App mithilfe der Startkonfiguration **.NET Core Launch (Blazor Standalone)** und starten dann den Browser mithilfe der Startkonfiguration **.NET Core Debug Blazor Web Assembly in Chrome** (Chrome erforderlich).</span><span class="sxs-lookup"><span data-stu-id="11ab4-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="11ab4-136">Weitere Informationen finden Sie unter <xref:blazor/debug#visual-studio-code>.</span><span class="sxs-lookup"><span data-stu-id="11ab4-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="11ab4-137">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="11ab4-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="11ab4-138">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="11ab4-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="11ab4-139">Blazor Server wird in Visual Studio für Mac unterstützt.</span><span class="sxs-lookup"><span data-stu-id="11ab4-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="11ab4-140">Blazor WebAssembly wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="11ab4-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="11ab4-141">Befolgen Sie die Anweisungen auf der Registerkarte **.NET Core-CLI**, um Blazor WebAssembly-Apps unter macOS zu erstellen. Informationen zu den zwei Blazor-Hostingmodellen, *Blazor WebAssembly* und *Blazor Server*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="11ab4-141">To create Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab. For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="11ab4-142">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="11ab4-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="11ab4-143">Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="11ab4-143">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="11ab4-144">Wählen Sie in der Randleiste **.NET Core** > **App** aus.</span><span class="sxs-lookup"><span data-stu-id="11ab4-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="11ab4-145">Wählen Sie die Vorlage **Blazor Server App** aus.</span><span class="sxs-lookup"><span data-stu-id="11ab4-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="11ab4-146">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="11ab4-146">Select **Next**.</span></span>

1. <span data-ttu-id="11ab4-147">Bestätigen Sie die folgenden Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="11ab4-147">Confirm the following configurations:</span></span>

   * <span data-ttu-id="11ab4-148">**Zielframework** auf **.NET Core 3.1** festgelegt</span><span class="sxs-lookup"><span data-stu-id="11ab4-148">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="11ab4-149">**Authentifizierung** auf **Keine Authentifizierung** festgelegt</span><span class="sxs-lookup"><span data-stu-id="11ab4-149">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="11ab4-150">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="11ab4-150">Select **Next**.</span></span>

1. <span data-ttu-id="11ab4-151">Benennen Sie im Feld **Projektname** die App `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="11ab4-151">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="11ab4-152">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="11ab4-152">Select **Create**.</span></span>

1. <span data-ttu-id="11ab4-153">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App *ohne Debugger* auszuführen.</span><span class="sxs-lookup"><span data-stu-id="11ab4-153">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="11ab4-154">Debuggen wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="11ab4-154">Debugging isn't supported at this time.</span></span>

<!-- HOLD FOR 8.6 GA

1. Select **File** > **New Solution** or create a **New** project from the **Start Window**.

1. In the sidebar, select **Web and Console** > **App**.

1. For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template. For a Blazor Server experience, choose the **Blazor Server App** template. Select **Next**.

   For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.

1. Confirm the following configurations:

   * **Target Framework** set to **.NET Core 3.1**.
   * **Authentication** set to **No Authentication**.
   
   Select **Next**.

1. In the **Project Name** field, name the app `WebApplication1`. Select **Create**.

1. Select **Run** > **Start Without Debugging** to run the app *without the debugger*. Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.

-->

<span data-ttu-id="11ab4-155">Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="11ab4-155">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="11ab4-156">Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="11ab4-156">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="11ab4-157">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="11ab4-157">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="11ab4-158">Installieren Sie das [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="11ab4-158">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="11ab4-159">Installieren Sie optional die Vorlage Blazor WebAssembly Preview, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="11ab4-159">Optionally install the Blazor WebAssembly preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-rc1.20223.4
   ```
   
   <span data-ttu-id="11ab4-160">Informationen zu den zwei Blazor-Hostingmodellen, *Blazor WebAssembly* und *Blazor Server*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="11ab4-160">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

   > [!NOTE]
   > <span data-ttu-id="11ab4-161">Das [.NET Core SDK 3.1.201 oder höher](https://dotnet.microsoft.com/download/dotnet-core/3.1) ist für die Verwendung der Vorlage für Blazor WebAssembly 3.2 Preview **erforderlich**.</span><span class="sxs-lookup"><span data-stu-id="11ab4-161">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview Blazor WebAssembly template.</span></span> <span data-ttu-id="11ab4-162">Überprüfen Sie die installierte Version des .NET Core-SDK, indem Sie `dotnet --version` in einer Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="11ab4-162">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="11ab4-163">Für eine Blazor Server-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="11ab4-163">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="11ab4-164">Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="11ab4-164">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="11ab4-165">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="11ab4-165">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="11ab4-166">Mehrere Seiten sind über Registerkarten in der Randleiste verfügbar:</span><span class="sxs-lookup"><span data-stu-id="11ab4-166">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="11ab4-167">Startseite</span><span class="sxs-lookup"><span data-stu-id="11ab4-167">Home</span></span>
* <span data-ttu-id="11ab4-168">Zähler</span><span class="sxs-lookup"><span data-stu-id="11ab4-168">Counter</span></span>
* <span data-ttu-id="11ab4-169">Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="11ab4-169">Fetch data</span></span>

<span data-ttu-id="11ab4-170">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="11ab4-170">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="11ab4-171">Für das Heraufsetzen eines Zählers auf einer Webseite müssen Sie JavaScript-Code schreiben, aber mit Blazor können Sie C# verwenden.</span><span class="sxs-lookup"><span data-stu-id="11ab4-171">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="11ab4-172">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="11ab4-172">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="11ab4-173">Eine Anforderung für `/counter` im Browser, wie durch die `@page`-Direktive oben angegeben, veranlasst die `Counter`-Komponente, ihren Inhalt zu rendern.</span><span class="sxs-lookup"><span data-stu-id="11ab4-173">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="11ab4-174">Die Komponenten werden in einer In-Memory-Darstellung der Renderstruktur gerendert, die anschließend verwendet werden kann, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="11ab4-174">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="11ab4-175">Jedes Mal, wenn die **Hier klicken**-Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="11ab4-175">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="11ab4-176">Das `onclick`-Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="11ab4-176">The `onclick` event is fired.</span></span>
* <span data-ttu-id="11ab4-177">Die `IncrementCount` -Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="11ab4-177">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="11ab4-178">Der `currentCount` wird inkrementiert.</span><span class="sxs-lookup"><span data-stu-id="11ab4-178">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="11ab4-179">Die Komponente wird erneut gerendert.</span><span class="sxs-lookup"><span data-stu-id="11ab4-179">The component is rendered again.</span></span>

<span data-ttu-id="11ab4-180">Die Laufzeit vergleicht den neuen Inhalt mit dem bisherigen Inhalt und wendet nur den geänderten Inhalt auf das Dokumentobjektmodell (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="11ab4-180">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="11ab4-181">Fügen Sie eine Komponente mithilfe der HTML-Syntax zu einer anderen Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="11ab4-181">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="11ab4-182">Fügen Sie z. B. die `Counter`-Komponente zur Startseite der App hinzu, indem Sie der `Index`-Komponente ein `<Counter />`-Element hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="11ab4-182">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="11ab4-183">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="11ab4-183">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="11ab4-184">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="11ab4-184">Run the app.</span></span> <span data-ttu-id="11ab4-185">Die Startseite verfügt über einen eigenen Zähler, der von der `Counter`-Komponente bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="11ab4-185">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="11ab4-186">Komponentenparameter werden mithilfe von Attributen oder [untergeordneten Inhalten](xref:blazor/components#child-content) angegeben, die es Ihnen ermöglichen, Eigenschaften für die untergeordnete Komponente festzulegen.</span><span class="sxs-lookup"><span data-stu-id="11ab4-186">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="11ab4-187">Um der `Counter`-Komponente einen Parameter hinzuzufügen, aktualisieren Sie den `@code`-Block der Komponente:</span><span class="sxs-lookup"><span data-stu-id="11ab4-187">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="11ab4-188">Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem `[Parameter]`-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="11ab4-188">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="11ab4-189">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="11ab4-189">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="11ab4-190">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="11ab4-190">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="11ab4-191">Geben Sie `IncrementAmount` im `<Counter>`-Element der `Index`-Komponente mit einem Attribut an.</span><span class="sxs-lookup"><span data-stu-id="11ab4-191">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="11ab4-192">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="11ab4-192">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="11ab4-193">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="11ab4-193">Run the app.</span></span> <span data-ttu-id="11ab4-194">Die Komponente `Index` verfügt über einen eigenen Zähler, der bei jeder Auswahl der Schaltfläche **Hier klicken** um zehn erhöht wird.</span><span class="sxs-lookup"><span data-stu-id="11ab4-194">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="11ab4-195">Die `Counter`-Komponente (*Counter.razor*) bei `/counter` wird weiterhin um eins erhöht.</span><span class="sxs-lookup"><span data-stu-id="11ab4-195">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="11ab4-196">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="11ab4-196">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="11ab4-197">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="11ab4-197">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
