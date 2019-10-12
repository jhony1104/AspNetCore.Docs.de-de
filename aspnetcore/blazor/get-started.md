---
title: Beginnen Sie mit ASP.net Core blazor
author: guardrex
description: Beginnen Sie mit blazor, indem Sie eine blazor-App mit den Tools Ihrer Wahl entwickeln.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/25/2019
uid: blazor/get-started
ms.openlocfilehash: ef9113dbfdbbd5920c4358cdac0c77c60f40b7c8
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288799"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="5c7de-103">Beginnen Sie mit ASP.net Core blazor</span><span class="sxs-lookup"><span data-stu-id="5c7de-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="5c7de-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5c7de-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="5c7de-105">Beginnen Sie mit blazor:</span><span class="sxs-lookup"><span data-stu-id="5c7de-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="5c7de-106">Installieren Sie die neueste Version des [.net Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="5c7de-106">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="5c7de-107">Installieren Sie die Vorlage [blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) , indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="5c7de-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview9.19465.2
   ```

1. <span data-ttu-id="5c7de-108">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="5c7de-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5c7de-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c7de-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="5c7de-110">1 \.</span><span class="sxs-lookup"><span data-stu-id="5c7de-110">1\.</span></span> <span data-ttu-id="5c7de-111">Installieren Sie die neueste Version von [Visual Studio](https://visualstudio.com/vs/) mit der Arbeitsauslastung **ASP.net und Webentwicklung** .</span><span class="sxs-lookup"><span data-stu-id="5c7de-111">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="5c7de-112">2 \.</span><span class="sxs-lookup"><span data-stu-id="5c7de-112">2\.</span></span> <span data-ttu-id="5c7de-113">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="5c7de-113">Create a new project.</span></span>

   <span data-ttu-id="5c7de-114">3 \.</span><span class="sxs-lookup"><span data-stu-id="5c7de-114">3\.</span></span> <span data-ttu-id="5c7de-115">Wählen Sie **blazor-App**aus.</span><span class="sxs-lookup"><span data-stu-id="5c7de-115">Select **Blazor App**.</span></span> <span data-ttu-id="5c7de-116">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="5c7de-116">Select **Next**.</span></span>

   <span data-ttu-id="5c7de-117">4 \.</span><span class="sxs-lookup"><span data-stu-id="5c7de-117">4\.</span></span> <span data-ttu-id="5c7de-118">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="5c7de-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="5c7de-119">Vergewissern Sie sich, dass der **Orts** Eintrag korrekt ist, oder geben Sie einen Speicherort für das Projekt</span><span class="sxs-lookup"><span data-stu-id="5c7de-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="5c7de-120">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="5c7de-120">Select **Create**.</span></span>

   <span data-ttu-id="5c7de-121">5 \.</span><span class="sxs-lookup"><span data-stu-id="5c7de-121">5\.</span></span> <span data-ttu-id="5c7de-122">Wählen Sie für eine blazor-Webassembly die Vorlage " **blazor Webassembly-App** " aus.</span><span class="sxs-lookup"><span data-stu-id="5c7de-122">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="5c7de-123">Wählen Sie für eine blazor-Server-APP die Vorlage für den **blazor-Server** aus.</span><span class="sxs-lookup"><span data-stu-id="5c7de-123">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="5c7de-124">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="5c7de-124">Select **Create**.</span></span> <span data-ttu-id="5c7de-125">Informationen zu den beiden blazor-Hostingmodellen, *blazor Server* und *blazor Webassembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="5c7de-125">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="5c7de-126">6 \.</span><span class="sxs-lookup"><span data-stu-id="5c7de-126">6\.</span></span> <span data-ttu-id="5c7de-127">Drücken Sie **F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="5c7de-127">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="5c7de-128">Wenn Sie die Erweiterung blazor Visual Studio für eine vorherige Vorschauversion von ASP.net Core blazor (Preview 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren.</span><span class="sxs-lookup"><span data-stu-id="5c7de-128">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="5c7de-129">Die Installation der blazor-Vorlagen in einer Befehlsshell reicht nun aus, um die Vorlagen in Visual Studio zu überstehen.</span><span class="sxs-lookup"><span data-stu-id="5c7de-129">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5c7de-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5c7de-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="5c7de-131">1 \.</span><span class="sxs-lookup"><span data-stu-id="5c7de-131">1\.</span></span> <span data-ttu-id="5c7de-132">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="5c7de-132">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="5c7de-133">2 \.</span><span class="sxs-lookup"><span data-stu-id="5c7de-133">2\.</span></span> <span data-ttu-id="5c7de-134">Installieren Sie die neueste [ C# für Visual Studio Code Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="5c7de-134">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="5c7de-135">3 \.</span><span class="sxs-lookup"><span data-stu-id="5c7de-135">3\.</span></span> <span data-ttu-id="5c7de-136">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine blazor-Webassembly zu nutzen:</span><span class="sxs-lookup"><span data-stu-id="5c7de-136">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="5c7de-137">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um einen blazor-Server zu nutzen:</span><span class="sxs-lookup"><span data-stu-id="5c7de-137">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="5c7de-138">Informationen zu den beiden blazor-Hostingmodellen, *blazor Server* und *blazor Webassembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="5c7de-138">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="5c7de-139">4 \.</span><span class="sxs-lookup"><span data-stu-id="5c7de-139">4\.</span></span> <span data-ttu-id="5c7de-140">Öffnen Sie den Ordner *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5c7de-140">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="5c7de-141">5 \.</span><span class="sxs-lookup"><span data-stu-id="5c7de-141">5\.</span></span> <span data-ttu-id="5c7de-142">Für ein blazor-Server Projekt fordert die IDE an, dass Sie Assets hinzufügen, um das Projekt zu erstellen und zu debuggen.</span><span class="sxs-lookup"><span data-stu-id="5c7de-142">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="5c7de-143">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="5c7de-143">Select **Yes**.</span></span>

   <span data-ttu-id="5c7de-144">6 \.</span><span class="sxs-lookup"><span data-stu-id="5c7de-144">6\.</span></span> <span data-ttu-id="5c7de-145">Wenn Sie eine blazor-Server-App verwenden, führen Sie die APP mit dem Visual Studio Code Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="5c7de-145">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="5c7de-146">Wenn Sie eine blazor Webassembly-App verwenden, führen Sie `dotnet run` aus dem Projektordner der APP aus.</span><span class="sxs-lookup"><span data-stu-id="5c7de-146">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="5c7de-147">7 \.</span><span class="sxs-lookup"><span data-stu-id="5c7de-147">7\.</span></span> <span data-ttu-id="5c7de-148">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="5c7de-148">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="5c7de-149">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="5c7de-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="5c7de-150">Führen Sie für eine blazor-Webassembly die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="5c7de-150">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="5c7de-151">Führen Sie für eine blazor-Server Darstellung die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="5c7de-151">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="5c7de-152">Informationen zu den beiden blazor-Hostingmodellen, *blazor Server* und *blazor Webassembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="5c7de-152">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="5c7de-153">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="5c7de-153">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="5c7de-154">Auf den Registerkarten in der Rand Leiste sind mehrere Seiten verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5c7de-154">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="5c7de-155">Startseite</span><span class="sxs-lookup"><span data-stu-id="5c7de-155">Home</span></span>
* <span data-ttu-id="5c7de-156">Zähler</span><span class="sxs-lookup"><span data-stu-id="5c7de-156">Counter</span></span>
* <span data-ttu-id="5c7de-157">Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="5c7de-157">Fetch data</span></span>

<span data-ttu-id="5c7de-158">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="5c7de-158">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="5c7de-159">Das Inkrementieren eines Zählers in einer Webseite erfordert normalerweise das Schreiben von JavaScript, C#aber mit blazor können Sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="5c7de-159">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="5c7de-160">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="5c7de-160">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="5c7de-161">Eine Anforderung für `/counter` im Browser, wie von der `@page`-Direktive angegeben, bewirkt, dass die `Counter`-Komponente ihren Inhalt wieder gibt.</span><span class="sxs-lookup"><span data-stu-id="5c7de-161">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="5c7de-162">Komponenten werden in einer Speicher internen Darstellung der Rendering-Struktur dargestellt, die dann zum flexiblen und effizienten Aktualisieren der Benutzeroberfläche verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="5c7de-162">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="5c7de-163">Jedes Mal, wenn die Schaltfläche " **Click me** " ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="5c7de-163">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="5c7de-164">Das `onclick`-Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="5c7de-164">The `onclick` event is fired.</span></span>
* <span data-ttu-id="5c7de-165">Die `IncrementCount` -Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="5c7de-165">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="5c7de-166">Der `currentCount` wird inkrementiert.</span><span class="sxs-lookup"><span data-stu-id="5c7de-166">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="5c7de-167">Die Komponente wird wieder gerendert.</span><span class="sxs-lookup"><span data-stu-id="5c7de-167">The component is rendered again.</span></span>

<span data-ttu-id="5c7de-168">Die Laufzeit vergleicht den neuen Inhalt mit dem vorherigen Inhalt und wendet nur den geänderten Inhalt auf die Dokumentobjektmodell (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="5c7de-168">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="5c7de-169">Fügen Sie einer anderen Komponente mithilfe der HTML-Syntax eine Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="5c7de-169">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="5c7de-170">Fügen Sie beispielsweise der Startseite der APP die Komponente `Counter` hinzu, indem Sie der Komponente `Index` ein `<Counter />`-Element hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="5c7de-170">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="5c7de-171">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="5c7de-171">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="5c7de-172">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="5c7de-172">Run the app.</span></span> <span data-ttu-id="5c7de-173">Die Startseite verfügt über einen eigenen Wert, der von der Komponente `Counter` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="5c7de-173">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="5c7de-174">Komponenten Parameter werden mithilfe von Attributen oder untergeordnetem [Inhalt](xref:blazor/components#child-content)angegeben, sodass Sie Eigenschaften für die untergeordnete Komponente festlegen können.</span><span class="sxs-lookup"><span data-stu-id="5c7de-174">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="5c7de-175">Aktualisieren Sie den Block "`@code`" der Komponente, um der Komponente "`Counter`" einen Parameter hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="5c7de-175">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="5c7de-176">Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem `[Parameter]`-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="5c7de-176">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="5c7de-177">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="5c7de-177">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="5c7de-178">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="5c7de-178">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="5c7de-179">Geben Sie den `IncrementAmount` im `<Counter>`-Element der `Index`-Komponente mithilfe eines-Attributs an.</span><span class="sxs-lookup"><span data-stu-id="5c7de-179">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="5c7de-180">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="5c7de-180">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="5c7de-181">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="5c7de-181">Run the app.</span></span> <span data-ttu-id="5c7de-182">Die Komponente "`Index`" hat einen eigenen Wert, der jedes Mal um zehn erhöht wird, wenn die Schaltfläche " **Click me** " ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="5c7de-182">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="5c7de-183">Die Komponente "`Counter`" (*counter. Razor*) bei `/counter` wird weiterhin um 1 erhöht.</span><span class="sxs-lookup"><span data-stu-id="5c7de-183">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5c7de-184">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="5c7de-184">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="5c7de-185">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5c7de-185">Additional resources</span></span>

* <xref:signalr/introduction>
