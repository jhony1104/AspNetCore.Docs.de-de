---
title: Erste Schritte mit ASP.NET Core Blazor
author: guardrex
description: Beginnen Sie mit der Blazor, indem Sie eine Blazor-App mit den Tools Ihrer Wahl entwickeln.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 642881b5400a70a99f6e7e262d2a2f1038389ce7
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726847"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="85515-103">Erste Schritte mit ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="85515-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="85515-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="85515-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="85515-105">Beginnen Sie mit Blazor:</span><span class="sxs-lookup"><span data-stu-id="85515-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="85515-106">Installieren Sie das [.net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="85515-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="85515-107">Installieren Sie optional die Vorlage [Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="85515-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="85515-108">Installieren Sie das [.net Core 3,1-oder spätere SDK (Vorschauversion)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="85515-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="85515-109">Führen Sie den folgenden Befehl in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="85515-109">Run the following command in a command shell.</span></span> <span data-ttu-id="85515-110">[Microsoft. aspnetcore.Blazor. Das Vorlagen](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) Paket verfügt über eine Vorschauversion, während sich Blazor Webassembly in der Vorschau Phase befindet.</span><span class="sxs-lookup"><span data-stu-id="85515-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="85515-111">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="85515-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85515-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85515-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="85515-113">1 \.</span><span class="sxs-lookup"><span data-stu-id="85515-113">1\.</span></span> <span data-ttu-id="85515-114">Installieren Sie [Visual Studio 2019, Version 16,4 oder](https://visualstudio.microsoft.com/vs/preview/) höher, mit der Arbeitsauslastung **ASP.net und Webentwicklung** .</span><span class="sxs-lookup"><span data-stu-id="85515-114">Install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="85515-115">2 \.</span><span class="sxs-lookup"><span data-stu-id="85515-115">2\.</span></span> <span data-ttu-id="85515-116">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="85515-116">Create a new project.</span></span>

   <span data-ttu-id="85515-117">3 \.</span><span class="sxs-lookup"><span data-stu-id="85515-117">3\.</span></span> <span data-ttu-id="85515-118">Wählen Sie **Blazor App**aus.</span><span class="sxs-lookup"><span data-stu-id="85515-118">Select **Blazor App**.</span></span> <span data-ttu-id="85515-119">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="85515-119">Select **Next**.</span></span>

   <span data-ttu-id="85515-120">4 \.</span><span class="sxs-lookup"><span data-stu-id="85515-120">4\.</span></span> <span data-ttu-id="85515-121">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="85515-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="85515-122">Vergewissern Sie sich, dass der **Orts** Eintrag korrekt ist, oder geben Sie einen Speicherort für das Projekt</span><span class="sxs-lookup"><span data-stu-id="85515-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="85515-123">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="85515-123">Select **Create**.</span></span>

   <span data-ttu-id="85515-124">5 \.</span><span class="sxs-lookup"><span data-stu-id="85515-124">5\.</span></span> <span data-ttu-id="85515-125">Wählen Sie für eine Blazor Webassembly die Vorlage **Blazor Webassembly-App** aus.</span><span class="sxs-lookup"><span data-stu-id="85515-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="85515-126">Wählen Sie für eine Blazor Server-Darstellung die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="85515-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="85515-127">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="85515-127">Select **Create**.</span></span> <span data-ttu-id="85515-128">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="85515-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="85515-129">6 \.</span><span class="sxs-lookup"><span data-stu-id="85515-129">6\.</span></span> <span data-ttu-id="85515-130">Drücken Sie **STRG**+**F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="85515-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="85515-131">Wenn Sie die Blazor Visual Studio-Erweiterung für eine vorherige Vorschauversion von ASP.net Core Blazor (Preview 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren.</span><span class="sxs-lookup"><span data-stu-id="85515-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="85515-132">Die Installation der Blazor Vorlagen in einer Befehlsshell reicht nun aus, um die Vorlagen in Visual Studio zu überstehen.</span><span class="sxs-lookup"><span data-stu-id="85515-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="85515-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="85515-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="85515-134">1 \.</span><span class="sxs-lookup"><span data-stu-id="85515-134">1\.</span></span> <span data-ttu-id="85515-135">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="85515-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="85515-136">2 \.</span><span class="sxs-lookup"><span data-stu-id="85515-136">2\.</span></span> <span data-ttu-id="85515-137">Installieren Sie die neueste [ C# für Visual Studio Code Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="85515-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="85515-138">3 \.</span><span class="sxs-lookup"><span data-stu-id="85515-138">3\.</span></span> <span data-ttu-id="85515-139">Führen Sie für eine Blazor Webassembly den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="85515-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="85515-140">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine Blazor Server-Funktion zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="85515-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="85515-141">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="85515-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="85515-142">4 \.</span><span class="sxs-lookup"><span data-stu-id="85515-142">4\.</span></span> <span data-ttu-id="85515-143">Öffnen Sie den Ordner *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="85515-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="85515-144">5 \.</span><span class="sxs-lookup"><span data-stu-id="85515-144">5\.</span></span> <span data-ttu-id="85515-145">Bei einem Blazor Server-Projekt fordert die IDE an, dass Sie Assets hinzufügen, um das Projekt zu erstellen und zu debuggen.</span><span class="sxs-lookup"><span data-stu-id="85515-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="85515-146">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="85515-146">Select **Yes**.</span></span>

   <span data-ttu-id="85515-147">6 \.</span><span class="sxs-lookup"><span data-stu-id="85515-147">6\.</span></span> <span data-ttu-id="85515-148">Wenn Sie eine Blazor Server-App verwenden, führen Sie die APP mit dem Visual Studio Code Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="85515-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="85515-149">Wenn Sie eine Blazor Webassembly-App verwenden, führen Sie `dotnet run` aus dem Projektordner der APP aus.</span><span class="sxs-lookup"><span data-stu-id="85515-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="85515-150">7 \.</span><span class="sxs-lookup"><span data-stu-id="85515-150">7\.</span></span> <span data-ttu-id="85515-151">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="85515-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="85515-152">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="85515-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="85515-153">1 \.</span><span class="sxs-lookup"><span data-stu-id="85515-153">1\.</span></span> <span data-ttu-id="85515-154">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="85515-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="85515-155">2 \.</span><span class="sxs-lookup"><span data-stu-id="85515-155">2\.</span></span> <span data-ttu-id="85515-156">Wählen Sie Datei > **neue** Projekt **Mappe** aus, oder erstellen Sie ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="85515-156">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="85515-157">3 \.</span><span class="sxs-lookup"><span data-stu-id="85515-157">3\.</span></span> <span data-ttu-id="85515-158">Wählen Sie in der Rand Leiste die Option **.net Core** - > **App**aus.</span><span class="sxs-lookup"><span data-stu-id="85515-158">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="85515-159">4 \.</span><span class="sxs-lookup"><span data-stu-id="85515-159">4\.</span></span> <span data-ttu-id="85515-160">Wählen Sie die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="85515-160">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="85515-161">Zurzeit ist nur die Blazor Server-Vorlage in Visual Studio für Mac verfügbar.</span><span class="sxs-lookup"><span data-stu-id="85515-161">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="85515-162">Befolgen Sie für eine Blazor Webassembly die Anweisungen auf der Registerkarte **.net Core-CLI** . Nachdem Sie die Vorlage Blazor Server ausgewählt haben, wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="85515-162">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="85515-163">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="85515-163">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="85515-164">5 \.</span><span class="sxs-lookup"><span data-stu-id="85515-164">5\.</span></span> <span data-ttu-id="85515-165">Legen Sie das **Ziel Framework** auf **.net Core 3,1** fest, und wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="85515-165">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="85515-166">6 \.</span><span class="sxs-lookup"><span data-stu-id="85515-166">6\.</span></span> <span data-ttu-id="85515-167">Benennen Sie die APP im Feld **Projektname** `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="85515-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="85515-168">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="85515-168">Select **Create**.</span></span>

   <span data-ttu-id="85515-169">7 \.</span><span class="sxs-lookup"><span data-stu-id="85515-169">7\.</span></span> <span data-ttu-id="85515-170">Wählen **Sie > ausführen** **ohne Debuggen** ausführen aus, um die APP *ohne den Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="85515-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="85515-171">Führen Sie die APP mit **Debuggen starten** aus, um die APP *mit dem Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="85515-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="85515-172">Wenn eine Eingabeaufforderung zum vertrauenswürdigen Entwicklungs Zertifikat angezeigt wird, Vertrauen Sie dem Zertifikat, und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="85515-172">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="85515-173">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="85515-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="85515-174">Führen Sie für eine Blazor Webassembly die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="85515-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="85515-175">Führen Sie die folgenden Befehle in einer Befehlsshell aus, um die Blazor Server-Darstellung auszuführen:</span><span class="sxs-lookup"><span data-stu-id="85515-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="85515-176">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="85515-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="85515-177">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="85515-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="85515-178">Auf den Registerkarten in der Rand Leiste sind mehrere Seiten verfügbar:</span><span class="sxs-lookup"><span data-stu-id="85515-178">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="85515-179">-Startseite</span><span class="sxs-lookup"><span data-stu-id="85515-179">Home</span></span>
* <span data-ttu-id="85515-180">Zähler</span><span class="sxs-lookup"><span data-stu-id="85515-180">Counter</span></span>
* <span data-ttu-id="85515-181">Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="85515-181">Fetch data</span></span>

<span data-ttu-id="85515-182">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="85515-182">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="85515-183">Das Inkrementieren eines Zählers in einer Webseite erfordert normalerweise das Schreiben von JavaScript C#, aber mit Blazor können Sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="85515-183">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="85515-184">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="85515-184">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="85515-185">Eine Anforderung für `/counter` im Browser, wie von der `@page`-Direktive angegeben, bewirkt, dass die `Counter` Komponente ihren Inhalt wieder gibt.</span><span class="sxs-lookup"><span data-stu-id="85515-185">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="85515-186">Komponenten werden in einer Speicher internen Darstellung der Rendering-Struktur dargestellt, die dann zum flexiblen und effizienten Aktualisieren der Benutzeroberfläche verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="85515-186">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="85515-187">Jedes Mal, wenn die Schaltfläche " **Click me** " ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="85515-187">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="85515-188">Das `onclick` Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="85515-188">The `onclick` event is fired.</span></span>
* <span data-ttu-id="85515-189">Die `IncrementCount`-Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="85515-189">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="85515-190">Der `currentCount` wird inkrementiert.</span><span class="sxs-lookup"><span data-stu-id="85515-190">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="85515-191">Die Komponente wird wieder gerendert.</span><span class="sxs-lookup"><span data-stu-id="85515-191">The component is rendered again.</span></span>

<span data-ttu-id="85515-192">Die Laufzeit vergleicht den neuen Inhalt mit dem vorherigen Inhalt und wendet nur den geänderten Inhalt auf die Dokumentobjektmodell (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="85515-192">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="85515-193">Fügen Sie einer anderen Komponente mithilfe der HTML-Syntax eine Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="85515-193">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="85515-194">Fügen Sie z. b. der Startseite der APP die `Counter` Komponente hinzu, indem Sie der `Index` Komponente ein `<Counter />`-Element hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="85515-194">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="85515-195">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="85515-195">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="85515-196">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="85515-196">Run the app.</span></span> <span data-ttu-id="85515-197">Die Startseite verfügt über einen eigenen Counter, der von der `Counter` Komponente bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="85515-197">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="85515-198">Komponenten Parameter werden mithilfe von Attributen oder untergeordnetem [Inhalt](xref:blazor/components#child-content)angegeben, sodass Sie Eigenschaften für die untergeordnete Komponente festlegen können.</span><span class="sxs-lookup"><span data-stu-id="85515-198">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="85515-199">Um der `Counter` Komponente einen Parameter hinzuzufügen, aktualisieren Sie den `@code` Block der Komponente:</span><span class="sxs-lookup"><span data-stu-id="85515-199">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="85515-200">Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem `[Parameter]` Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="85515-200">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="85515-201">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="85515-201">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="85515-202">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="85515-202">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="85515-203">Geben Sie die `IncrementAmount` im `<Counter>`-Element der `Index` Komponente mithilfe eines-Attributs an.</span><span class="sxs-lookup"><span data-stu-id="85515-203">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="85515-204">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="85515-204">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="85515-205">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="85515-205">Run the app.</span></span> <span data-ttu-id="85515-206">Die `Index` Komponente verfügt über einen eigenen Wert, der jedes Mal um zehn erhöht wird, wenn die Schaltfläche " **Click me** " ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="85515-206">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="85515-207">Die `Counter` Komponente (*counter. Razor*) bei `/counter` wird weiterhin um 1 erhöht.</span><span class="sxs-lookup"><span data-stu-id="85515-207">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="85515-208">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="85515-208">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="85515-209">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="85515-209">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
