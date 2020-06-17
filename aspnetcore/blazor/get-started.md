---
title: Erste Schritte mit ASP.NET Core Blazor
author: guardrex
description: Erste Schritte mit Blazor durch Erstellen einer Blazor-App mit den Tools Ihrer Wahl.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 08229283882928c4cc733de19840d25872846c97
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452030"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="d8f40-103">Erste Schritte mit ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="d8f40-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="d8f40-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d8f40-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d8f40-105">Befolgen Sie für Ihre ersten Schritte mit Blazor die Anweisungen für Ihre bevorzugten Tools:</span><span class="sxs-lookup"><span data-stu-id="d8f40-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8f40-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8f40-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d8f40-107">Installieren Sie die neueste Version von [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) mit der Workload **ASP.NET und Webentwicklung**.</span><span class="sxs-lookup"><span data-stu-id="d8f40-107">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="d8f40-108">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="d8f40-108">Create a new project.</span></span>

1. <span data-ttu-id="d8f40-109">Klicken Sie auf **Blazor-App**.</span><span class="sxs-lookup"><span data-stu-id="d8f40-109">Select **Blazor App**.</span></span> <span data-ttu-id="d8f40-110">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d8f40-110">Select **Next**.</span></span>

1. <span data-ttu-id="d8f40-111">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="d8f40-111">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="d8f40-112">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="d8f40-112">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="d8f40-113">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="d8f40-113">Select **Create**.</span></span>

1. <span data-ttu-id="d8f40-114">Wählen Sie für eine Blazor-WebAssembly-Benutzeroberfläche die Vorlage **Blazor-WebAssembly-App** aus.</span><span class="sxs-lookup"><span data-stu-id="d8f40-114">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d8f40-115">Wählen Sie für eine Blazor-Server-Benutzeroberfläche die Vorlage **Blazor-Server-App** aus.</span><span class="sxs-lookup"><span data-stu-id="d8f40-115">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="d8f40-116">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="d8f40-116">Select **Create**.</span></span>

   <span data-ttu-id="d8f40-117">Informationen zu den zwei Blazor-Hostingmodellen ( *Blazor-WebAssembly* und *Blazor-Server*) finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="d8f40-117">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="d8f40-118">Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d8f40-118">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8f40-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8f40-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="d8f40-120">Installieren Sie die neueste Version des [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="d8f40-120">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="d8f40-121">Wenn Sie das SDK zuvor installiert haben, können Sie die installierte Version ermitteln, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="d8f40-121">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="d8f40-122">Installieren Sie die neueste Version von [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="d8f40-122">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="d8f40-123">Installieren Sie die neueste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) und die Erweiterung [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly), und legen Sie für `debug.javascript.usePreview` den Wert `true` fest.</span><span class="sxs-lookup"><span data-stu-id="d8f40-123">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

  <span data-ttu-id="d8f40-124">Öffnen Sie **Datei** > **Einstellungen** > **Einstellungen**, und suchen Sie nach `debug javascript use preview`, um `debug.javascript.usePreview` mithilfe der VS Code-Benutzeroberfläche auf `true` festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d8f40-124">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="d8f40-125">Aktivieren Sie das Kontrollkästchen für die Option **Use the new in-preview JavaScript debugger for Node.js and Chrome** (Neuen JavaScript-Debugger in der Vorschauversion für Node.js und Chrome verwenden).</span><span class="sxs-lookup"><span data-stu-id="d8f40-125">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="d8f40-126">Für eine Blazor-WebAssembly-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="d8f40-126">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="d8f40-127">Für eine Blazor-Server-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="d8f40-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="d8f40-128">Informationen zu den zwei Blazor-Hostingmodellen ( *Blazor-WebAssembly* und *Blazor-Server*) finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="d8f40-128">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="d8f40-129">Öffnen Sie in Visual Studio Code den Ordner *WebApplication1*.</span><span class="sxs-lookup"><span data-stu-id="d8f40-129">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="d8f40-130">Die IDE fordert an, dass Sie Ressourcen zum Erstellen und Debuggen des Projekts hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="d8f40-130">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="d8f40-131">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="d8f40-131">Select **Yes**.</span></span>

1. <span data-ttu-id="d8f40-132">Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d8f40-132">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8f40-133">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8f40-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="d8f40-134">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="d8f40-134">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="d8f40-135">Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="d8f40-135">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="d8f40-136">Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="d8f40-136">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="d8f40-137">Wählen Sie für eine Blazor-WebAssembly-Benutzeroberfläche die Vorlage **Blazor-WebAssembly-App** aus.</span><span class="sxs-lookup"><span data-stu-id="d8f40-137">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="d8f40-138">Wählen Sie für eine Blazor-Server-Benutzeroberfläche die Vorlage **Blazor-Server-App** aus.</span><span class="sxs-lookup"><span data-stu-id="d8f40-138">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="d8f40-139">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d8f40-139">Select **Next**.</span></span>

   <span data-ttu-id="d8f40-140">Informationen zu den zwei Blazor-Hostingmodellen ( *Blazor-WebAssembly* und *Blazor-Server*) finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="d8f40-140">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="d8f40-141">Bestätigen Sie die folgenden Konfigurationen:</span><span class="sxs-lookup"><span data-stu-id="d8f40-141">Confirm the following configurations:</span></span>

   * <span data-ttu-id="d8f40-142">**Zielframework** auf **.NET Core 3.1** festgelegt</span><span class="sxs-lookup"><span data-stu-id="d8f40-142">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="d8f40-143">**Authentifizierung** auf **Keine Authentifizierung** festgelegt</span><span class="sxs-lookup"><span data-stu-id="d8f40-143">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="d8f40-144">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="d8f40-144">Select **Next**.</span></span>

1. <span data-ttu-id="d8f40-145">Benennen Sie im Feld **Projektname** die App `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="d8f40-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="d8f40-146">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="d8f40-146">Select **Create**.</span></span>

1. <span data-ttu-id="d8f40-147">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App *ohne Debugger* auszuführen.</span><span class="sxs-lookup"><span data-stu-id="d8f40-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="d8f40-148">Führen Sie die App entweder über **Ausführen** > **Debuggen starten** oder über die Schaltfläche „Ausführen“ (&#9654;) *mit dem Debugger aus*.</span><span class="sxs-lookup"><span data-stu-id="d8f40-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="d8f40-149">Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="d8f40-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="d8f40-150">Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="d8f40-150">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d8f40-151">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="d8f40-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="d8f40-152">Installieren Sie die neueste Version des [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="d8f40-152">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="d8f40-153">Wenn Sie das SDK zuvor installiert haben, können Sie die installierte Version ermitteln, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="d8f40-153">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="d8f40-154">Für eine Blazor-WebAssembly-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="d8f40-154">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="d8f40-155">Für eine Blazor-Server-Benutzeroberfläche führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="d8f40-155">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="d8f40-156">Informationen zu den zwei Blazor-Hostingmodellen ( *Blazor-WebAssembly* und *Blazor-Server*) finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="d8f40-156">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="d8f40-157">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="d8f40-157">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="d8f40-158">Mehrere Seiten sind über Registerkarten in der Randleiste verfügbar:</span><span class="sxs-lookup"><span data-stu-id="d8f40-158">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="d8f40-159">Startseite</span><span class="sxs-lookup"><span data-stu-id="d8f40-159">Home</span></span>
* <span data-ttu-id="d8f40-160">Zähler</span><span class="sxs-lookup"><span data-stu-id="d8f40-160">Counter</span></span>
* <span data-ttu-id="d8f40-161">Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="d8f40-161">Fetch data</span></span>

<span data-ttu-id="d8f40-162">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="d8f40-162">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="d8f40-163">Für das Heraufsetzen eines Zählers auf einer Webseite müssen Sie JavaScript-Code schreiben, aber mit Blazor können Sie C# verwenden.</span><span class="sxs-lookup"><span data-stu-id="d8f40-163">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="d8f40-164">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="d8f40-164">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="d8f40-165">Eine Anforderung für `/counter` im Browser, wie durch die `@page`-Direktive oben angegeben, veranlasst die `Counter`-Komponente, ihren Inhalt zu rendern.</span><span class="sxs-lookup"><span data-stu-id="d8f40-165">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="d8f40-166">Die Komponenten werden in einer In-Memory-Darstellung der Renderstruktur gerendert, die anschließend verwendet werden kann, um die Benutzeroberfläche auf flexible und effiziente Weise zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="d8f40-166">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="d8f40-167">Jedes Mal, wenn die **Hier klicken**-Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="d8f40-167">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="d8f40-168">Das `onclick`-Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="d8f40-168">The `onclick` event is fired.</span></span>
* <span data-ttu-id="d8f40-169">Die `IncrementCount` -Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="d8f40-169">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="d8f40-170">Der `currentCount` wird inkrementiert.</span><span class="sxs-lookup"><span data-stu-id="d8f40-170">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="d8f40-171">Die Komponente wird erneut gerendert.</span><span class="sxs-lookup"><span data-stu-id="d8f40-171">The component is rendered again.</span></span>

<span data-ttu-id="d8f40-172">Die Laufzeit vergleicht den neuen Inhalt mit dem bisherigen Inhalt und wendet nur den geänderten Inhalt auf das Dokumentobjektmodell (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="d8f40-172">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="d8f40-173">Fügen Sie eine Komponente mithilfe der HTML-Syntax zu einer anderen Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8f40-173">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="d8f40-174">Fügen Sie z. B. die `Counter`-Komponente zur Startseite der App hinzu, indem Sie der `Index`-Komponente ein `<Counter />`-Element hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="d8f40-174">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="d8f40-175">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="d8f40-175">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="d8f40-176">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="d8f40-176">Run the app.</span></span> <span data-ttu-id="d8f40-177">Die Startseite verfügt über einen eigenen Zähler, der von der `Counter`-Komponente bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="d8f40-177">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="d8f40-178">Komponentenparameter werden mithilfe von Attributen oder [untergeordneten Inhalten](xref:blazor/components#child-content) angegeben, die es Ihnen ermöglichen, Eigenschaften für die untergeordnete Komponente festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d8f40-178">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="d8f40-179">Um der `Counter`-Komponente einen Parameter hinzuzufügen, aktualisieren Sie den `@code`-Block der Komponente:</span><span class="sxs-lookup"><span data-stu-id="d8f40-179">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="d8f40-180">Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8f40-180">Add a public property for `IncrementAmount` with a [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
* <span data-ttu-id="d8f40-181">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d8f40-181">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="d8f40-182">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="d8f40-182">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="d8f40-183">Geben Sie `IncrementAmount` im `<Counter>`-Element der `Index`-Komponente mit einem Attribut an.</span><span class="sxs-lookup"><span data-stu-id="d8f40-183">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="d8f40-184">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="d8f40-184">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="d8f40-185">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="d8f40-185">Run the app.</span></span> <span data-ttu-id="d8f40-186">Die Komponente `Index` verfügt über einen eigenen Zähler, der bei jeder Auswahl der Schaltfläche **Hier klicken** um zehn erhöht wird.</span><span class="sxs-lookup"><span data-stu-id="d8f40-186">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="d8f40-187">Die `Counter`-Komponente (*Counter.razor*) bei `/counter` wird weiterhin um eins erhöht.</span><span class="sxs-lookup"><span data-stu-id="d8f40-187">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d8f40-188">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="d8f40-188">Next steps</span></span>

<span data-ttu-id="d8f40-189">Schrittweises Erstellen einer Blazor-App:</span><span class="sxs-lookup"><span data-stu-id="d8f40-189">Build a Blazor app step-by-step:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="d8f40-190">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d8f40-190">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
