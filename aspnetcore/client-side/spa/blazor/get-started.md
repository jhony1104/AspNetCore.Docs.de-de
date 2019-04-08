---
title: Erste Schritte mit Blazor
author: guardrex
description: Erfahren Sie, wie Blazor Einstieg durch Erstellen und ändern ein Projekt Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: spa/blazor/get-started
ms.openlocfilehash: b3928c2812be6f34cdf2f17295a1251106f651e5
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068234"
---
# <a name="get-started-with-blazor"></a><span data-ttu-id="bed74-103">Erste Schritte mit Blazor</span><span class="sxs-lookup"><span data-stu-id="bed74-103">Get started with Blazor</span></span>

<span data-ttu-id="bed74-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="bed74-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

# [<a name="visual-studio"></a><span data-ttu-id="bed74-105">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bed74-105">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bed74-106">Erforderliche Komponenten:</span><span class="sxs-lookup"><span data-stu-id="bed74-106">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="bed74-107">So erstellen Ihr erste Blazor-Projekt in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="bed74-107">To create your first Blazor project in Visual Studio:</span></span>

1. <span data-ttu-id="bed74-108">Installieren Sie das neueste [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) freizugeben.</span><span class="sxs-lookup"><span data-stu-id="bed74-108">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>
1. <span data-ttu-id="bed74-109">Aktivieren Sie die Visual Studio Preview-SDKs verwenden:</span><span class="sxs-lookup"><span data-stu-id="bed74-109">Enable Visual Studio to use preview SDKs:</span></span>
   1. <span data-ttu-id="bed74-110">Open **Tools** > **Optionen** in der Menüleiste.</span><span class="sxs-lookup"><span data-stu-id="bed74-110">Open **Tools** > **Options** in the menu bar.</span></span>
   1. <span data-ttu-id="bed74-111">Öffnen der **Projekte und Projektmappen** Knoten.</span><span class="sxs-lookup"><span data-stu-id="bed74-111">Open the **Projects and Solutions** node.</span></span> <span data-ttu-id="bed74-112">Öffnen der **.NET Core** Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="bed74-112">Open the **.NET Core** tab.</span></span>
   1. <span data-ttu-id="bed74-113">Aktivieren Sie das Kontrollkästchen **verwenden Sie die Preview-Versionen von .NET Core SDK**.</span><span class="sxs-lookup"><span data-stu-id="bed74-113">Check the box for **Use previews of the .NET Core SDK**.</span></span> <span data-ttu-id="bed74-114">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="bed74-114">Select **OK**.</span></span>
1. <span data-ttu-id="bed74-115">Installieren Sie das neueste [Blazor Erweiterung](https://go.microsoft.com/fwlink/?linkid=870389) in Visual Studio Marketplace.</span><span class="sxs-lookup"><span data-stu-id="bed74-115">Install the latest [Blazor extension](https://go.microsoft.com/fwlink/?linkid=870389) from the Visual Studio Marketplace.</span></span> <span data-ttu-id="bed74-116">Dieser Schritt stellt Blazor Vorlagen in Visual Studio zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="bed74-116">This step makes Blazor templates available to Visual Studio.</span></span>
1. <span data-ttu-id="bed74-117">Stellen Sie die Vorlagen Blazor mithilfe des folgenden Befehls in einer Befehlsshell für die Verwendung mit .NET Core-CLI zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="bed74-117">Make the Blazor templates available for use with the .NET Core CLI by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.9.0-preview3-19154-02
   ```
1. <span data-ttu-id="bed74-118">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="bed74-118">Create a new project.</span></span>
1. <span data-ttu-id="bed74-119">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="bed74-119">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="bed74-120">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="bed74-120">Select **Next**.</span></span>
1. <span data-ttu-id="bed74-121">Geben Sie einen Namen in der **Projektname** Feld.</span><span class="sxs-lookup"><span data-stu-id="bed74-121">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="bed74-122">Vergewissern Sie sich die **Speicherort** Eintrag richtig ist, oder geben Sie einen Speicherort für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="bed74-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="bed74-123">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="bed74-123">Select **Create**.</span></span>
1. <span data-ttu-id="bed74-124">Stellen Sie sicher, dass **.NET Core** und **ASP.NET Core 3.0** oben ausgewählt werden.</span><span class="sxs-lookup"><span data-stu-id="bed74-124">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="bed74-125">Wählen Sie die **Blazor** Vorlage, und wählen **erstellen**.</span><span class="sxs-lookup"><span data-stu-id="bed74-125">Select the **Blazor** template and select **Create**.</span></span>
1. <span data-ttu-id="bed74-126">Drücken Sie **F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="bed74-126">Press **F5** to run the app.</span></span>

<span data-ttu-id="bed74-127">Herzlichen Glückwunsch!</span><span class="sxs-lookup"><span data-stu-id="bed74-127">Congratulations!</span></span> <span data-ttu-id="bed74-128">Sie haben soeben Ihre erste app für die Blazor ausgeführt!</span><span class="sxs-lookup"><span data-stu-id="bed74-128">You just ran your first Blazor app!</span></span>

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Blazor project in Visual Studio Code:

1. Execute the following command in a command shell:

   ```console
   dotnet new blazor -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Visual Studio code offers to create assets to build and debug the app, which includes the *tasks.json* and *launch.json* files. Select **Yes** to add the assets.

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Blazor app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Blazor project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **Blazor** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Blazor app!
-->

# [<a name="net-core-cli"></a><span data-ttu-id="bed74-129">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="bed74-129">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="bed74-130">Erforderliche Komponenten:</span><span class="sxs-lookup"><span data-stu-id="bed74-130">Prerequisites:</span></span>

* [<span data-ttu-id="bed74-131">.NET Core SDK 3.0 (Vorschau)</span><span class="sxs-lookup"><span data-stu-id="bed74-131">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. <span data-ttu-id="bed74-132">Fügen Sie die Vorlagen Blazor mithilfe des folgenden Befehls in einer Befehlsshell hinzu:</span><span class="sxs-lookup"><span data-stu-id="bed74-132">Add the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.9.0-preview3-19154-02
   ```

1. <span data-ttu-id="bed74-133">Erstellen Sie Ihr erste Blazor-Projekt in einer Befehlsshell ein:</span><span class="sxs-lookup"><span data-stu-id="bed74-133">Create your first Blazor project in a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="bed74-134">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="bed74-134">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="bed74-135">Herzlichen Glückwunsch!</span><span class="sxs-lookup"><span data-stu-id="bed74-135">Congratulations!</span></span> <span data-ttu-id="bed74-136">Sie haben soeben Ihre erste app für die Blazor ausgeführt!</span><span class="sxs-lookup"><span data-stu-id="bed74-136">You just ran your first Blazor app!</span></span>

---

## <a name="blazor-project"></a><span data-ttu-id="bed74-137">Blazor-Projekt</span><span class="sxs-lookup"><span data-stu-id="bed74-137">Blazor project</span></span>

<span data-ttu-id="bed74-138">Wenn die app ausgeführt wird, stehen mehrere Seiten von Registerkarten in der Randleiste aus:</span><span class="sxs-lookup"><span data-stu-id="bed74-138">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="bed74-139">Startseite</span><span class="sxs-lookup"><span data-stu-id="bed74-139">Home</span></span>
* <span data-ttu-id="bed74-140">Zähler</span><span class="sxs-lookup"><span data-stu-id="bed74-140">Counter</span></span>
* <span data-ttu-id="bed74-141">Abrufen der Daten</span><span class="sxs-lookup"><span data-stu-id="bed74-141">Fetch data</span></span>

<span data-ttu-id="bed74-142">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="bed74-142">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="bed74-143">Erhöhen eines Zählers auf einer Webseite bei normalerweise erfordert das Schreiben von JavaScript allerdings Blazor bietet einen besseren Ansatz mit C#.</span><span class="sxs-lookup"><span data-stu-id="bed74-143">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

<span data-ttu-id="bed74-144">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bed74-144">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

<span data-ttu-id="bed74-145">Eine Anforderung für `/counter` im Browser, laut der `@page` -Direktive am Anfang, bewirkt, dass die Leistungsindikator-Komponente, um seinen Inhalt zu rendern.</span><span class="sxs-lookup"><span data-stu-id="bed74-145">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="bed74-146">Rendern von Komponenten in eine in-Memory-Darstellung der Render-Struktur, die zur Aktualisierung der Benutzeroberfläche in eine flexible und effiziente Möglichkeit verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="bed74-146">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="bed74-147">Jedes Mal die **hier klicken** ausgewählt ist:</span><span class="sxs-lookup"><span data-stu-id="bed74-147">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="bed74-148">Die `onclick` Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="bed74-148">The `onclick` event is fired.</span></span>
* <span data-ttu-id="bed74-149">Die `IncrementCount` -Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="bed74-149">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="bed74-150">Die `currentCount` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="bed74-150">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="bed74-151">Die Komponente wird erneut gerendert.</span><span class="sxs-lookup"><span data-stu-id="bed74-151">The component is rendered again.</span></span>

<span data-ttu-id="bed74-152">Die Runtime vergleicht den neuen Inhalt zum vorherigen Inhalt und den geänderten Inhalt gilt nur, die (DOKUMENTOBJEKTMODELL).</span><span class="sxs-lookup"><span data-stu-id="bed74-152">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="bed74-153">Fügen Sie eine Komponente an eine andere Komponente, die mithilfe einer HTML-ähnliche Syntax.</span><span class="sxs-lookup"><span data-stu-id="bed74-153">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="bed74-154">Parameter der Komponente werden mithilfe von Attributen oder untergeordneten Inhalt angegeben.</span><span class="sxs-lookup"><span data-stu-id="bed74-154">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="bed74-155">Eine Komponente eines Leistungsindikators kann z. B. an der app-Startseite hinzugefügt werden, durch das Hinzufügen einer `<Counter />` Element an die Index-Komponente.</span><span class="sxs-lookup"><span data-stu-id="bed74-155">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="bed74-156">In *Pages/Index.cshtml*, ersetzen Sie die Umfrage Eingabeaufforderung Komponente mit der eine Komponente eines Leistungsindikators:</span><span class="sxs-lookup"><span data-stu-id="bed74-156">In *Pages/Index.cshtml*, replace the Survey Prompt component with a Counter component:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

<span data-ttu-id="bed74-157">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="bed74-157">Run the app.</span></span> <span data-ttu-id="bed74-158">Die Startseite verfügt über eine eigene Leistungsindikator.</span><span class="sxs-lookup"><span data-stu-id="bed74-158">The homepage has its own counter.</span></span>

<span data-ttu-id="bed74-159">Um einen Parameter an die Leistungsindikator-Komponente hinzuzufügen, aktualisieren Sie der Komponente `@functions` blockieren:</span><span class="sxs-lookup"><span data-stu-id="bed74-159">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="bed74-160">Hinzufügen einer Eigenschaft für `IncrementAmount` ergänzt die `[Parameter]` Attribut.</span><span class="sxs-lookup"><span data-stu-id="bed74-160">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="bed74-161">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="bed74-161">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="bed74-162">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bed74-162">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4,8)]

<span data-ttu-id="bed74-163">Geben Sie unter Verwendung eines Attributs einen `IncrementAmount`-Parameter in das `<Counter>`-Element der Home-Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="bed74-163">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="bed74-164">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="bed74-164">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

<span data-ttu-id="bed74-165">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="bed74-165">Run the app.</span></span> <span data-ttu-id="bed74-166">Die Startseite verfügt über eine eigene Zähler, der mit zehn jedes Mal erhöht die **hier klicken** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="bed74-166">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bed74-167">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="bed74-167">Next steps</span></span>

<xref:tutorials/first-razor-components-app>
