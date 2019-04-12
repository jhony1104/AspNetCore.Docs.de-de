---
title: Erste Schritte mit Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie mit Razor-Komponenten beginnen, durch das Erstellen und ändern ein Projekt für die Razor-Komponenten.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/get-started
ms.openlocfilehash: 151e58497b0f22fa7c5a9bde1f665eeb73fd5dc3
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515531"
---
# <a name="get-started-with-razor-components"></a><span data-ttu-id="af7d4-103">Erste Schritte mit Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="af7d4-103">Get started with Razor Components</span></span>

<span data-ttu-id="af7d4-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="af7d4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="af7d4-105">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af7d4-105">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="af7d4-106">Erforderliche Komponenten:</span><span class="sxs-lookup"><span data-stu-id="af7d4-106">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="af7d4-107">So erstellen Ihr erste Projekt mit Razor-Komponenten in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="af7d4-107">To create your first Razor Components project in Visual Studio:</span></span>

1. <span data-ttu-id="af7d4-108">Installieren Sie das neueste [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) freizugeben.</span><span class="sxs-lookup"><span data-stu-id="af7d4-108">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>
1. <span data-ttu-id="af7d4-109">Aktivieren Sie die Visual Studio Preview-SDKs verwenden:</span><span class="sxs-lookup"><span data-stu-id="af7d4-109">Enable Visual Studio to use preview SDKs:</span></span>
   1. <span data-ttu-id="af7d4-110">Open **Tools** > **Optionen** in der Menüleiste.</span><span class="sxs-lookup"><span data-stu-id="af7d4-110">Open **Tools** > **Options** in the menu bar.</span></span>
   1. <span data-ttu-id="af7d4-111">Öffnen der **Projekte und Projektmappen** Knoten.</span><span class="sxs-lookup"><span data-stu-id="af7d4-111">Open the **Projects and Solutions** node.</span></span> <span data-ttu-id="af7d4-112">Öffnen der **.NET Core** Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="af7d4-112">Open the **.NET Core** tab.</span></span>
   1. <span data-ttu-id="af7d4-113">Aktivieren Sie das Kontrollkästchen **verwenden Sie die Preview-Versionen von .NET Core SDK**.</span><span class="sxs-lookup"><span data-stu-id="af7d4-113">Check the box for **Use previews of the .NET Core SDK**.</span></span> <span data-ttu-id="af7d4-114">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="af7d4-114">Select **OK**.</span></span>
1. <span data-ttu-id="af7d4-115">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="af7d4-115">Create a new project.</span></span>
1. <span data-ttu-id="af7d4-116">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="af7d4-116">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="af7d4-117">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="af7d4-117">Select **Next**.</span></span>
1. <span data-ttu-id="af7d4-118">Geben Sie einen Namen in der **Projektname** Feld.</span><span class="sxs-lookup"><span data-stu-id="af7d4-118">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="af7d4-119">Vergewissern Sie sich die **Speicherort** Eintrag richtig ist, oder geben Sie einen Speicherort für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="af7d4-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="af7d4-120">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="af7d4-120">Select **Create**.</span></span>
1. <span data-ttu-id="af7d4-121">Stellen Sie sicher, dass **.NET Core** und **ASP.NET Core 3.0** oben ausgewählt werden.</span><span class="sxs-lookup"><span data-stu-id="af7d4-121">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="af7d4-122">Wählen Sie die **Razor-Komponenten** Vorlage, und wählen **erstellen**.</span><span class="sxs-lookup"><span data-stu-id="af7d4-122">Choose the **Razor Components** template and select **Create**.</span></span>
1. <span data-ttu-id="af7d4-123">Drücken Sie **F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="af7d4-123">Press **F5** to run the app.</span></span>

<span data-ttu-id="af7d4-124">Herzlichen Glückwunsch!</span><span class="sxs-lookup"><span data-stu-id="af7d4-124">Congratulations!</span></span> <span data-ttu-id="af7d4-125">Sie haben soeben Ihre erste app mit Razor-Komponenten ausgeführt!</span><span class="sxs-lookup"><span data-stu-id="af7d4-125">You just ran your first Razor Components app!</span></span>

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Razor Components project in Visual Studio Code:

1. Execute the following command from a command shell:

   ```console
   dotnet new razorcomponents -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Add a *.vscode* folder.

1. Add a *tasks.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/tasks.json)]

1. Add a *launch.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/launch.json)]

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Razor Components app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Razor Components project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **ASP.NET Core Razor Components** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Razor Components app!
-->

# [<a name="net-core-cli"></a><span data-ttu-id="af7d4-126">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="af7d4-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="af7d4-127">Erforderliche Komponenten:</span><span class="sxs-lookup"><span data-stu-id="af7d4-127">Prerequisites:</span></span>

* [<span data-ttu-id="af7d4-128">.NET Core SDK 3.0 (Vorschau)</span><span class="sxs-lookup"><span data-stu-id="af7d4-128">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. <span data-ttu-id="af7d4-129">So erstellen Sie Ihr erste Projekt mit Razor-Komponenten über eine Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="af7d4-129">To create your first Razor Components project from a command shell:</span></span>

   ```console
   dotnet new razorcomponents -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="af7d4-130">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="af7d4-130">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="af7d4-131">Herzlichen Glückwunsch!</span><span class="sxs-lookup"><span data-stu-id="af7d4-131">Congratulations!</span></span> <span data-ttu-id="af7d4-132">Sie haben soeben Ihre erste app mit Razor-Komponenten ausgeführt!</span><span class="sxs-lookup"><span data-stu-id="af7d4-132">You just ran your first Razor Components app!</span></span>

---

## <a name="razor-components-project"></a><span data-ttu-id="af7d4-133">Razor-Komponenten-Projekt</span><span class="sxs-lookup"><span data-stu-id="af7d4-133">Razor Components project</span></span>

<span data-ttu-id="af7d4-134">Razor-Komponenten werden mithilfe der Razor-Syntax erstellt, aber anders als die Razor-Seiten und MVC-Ansichten kompiliert werden.</span><span class="sxs-lookup"><span data-stu-id="af7d4-134">Razor Components are authored using Razor syntax but are compiled differently than Razor Pages and MVC views.</span></span> <span data-ttu-id="af7d4-135">Die *.razor* Dateierweiterung wird an eine Razor-Komponente verwendet.</span><span class="sxs-lookup"><span data-stu-id="af7d4-135">The *.razor* file extension is used to specify a Razor Component.</span></span> <span data-ttu-id="af7d4-136">Razor-Seiten und MVC Ansichten verwenden weiterhin die *.cshtml* Dateierweiterung.</span><span class="sxs-lookup"><span data-stu-id="af7d4-136">Razor Pages and MVC views continue to use the *.cshtml* file extension.</span></span>

> [!NOTE]
> <span data-ttu-id="af7d4-137">Razor-Komponenten können erstellt werden, mithilfe der *.cshtml* Dateierweiterung aus, solange diese Dateien als Komponente der Razor-Dateien, die mit identifiziert werden die `_RazorComponentInclude` MSBuild-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="af7d4-137">Razor Components can be authored using the *.cshtml* file extension as long as those files are identified as Razor Component files using the `_RazorComponentInclude` MSBuild property.</span></span> <span data-ttu-id="af7d4-138">Eine app, die mit der Komponente der Razor-Vorlage erstellt z. B. gibt an, dass alle *.cshtml* Dateien unter den *Komponenten* Ordner als Razor-Komponenten behandelt werden soll:</span><span class="sxs-lookup"><span data-stu-id="af7d4-138">For example, an app created using the Razor Component template specifies that all *.cshtml* files under the *Components* folder should be treated as Razor Components:</span></span>
>
> ```xml
> <_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
> ```

<span data-ttu-id="af7d4-139">Wenn die app ausgeführt wird, stehen mehrere Seiten von Registerkarten in der Randleiste aus:</span><span class="sxs-lookup"><span data-stu-id="af7d4-139">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="af7d4-140">Startseite</span><span class="sxs-lookup"><span data-stu-id="af7d4-140">Home</span></span>
* <span data-ttu-id="af7d4-141">Zähler</span><span class="sxs-lookup"><span data-stu-id="af7d4-141">Counter</span></span>
* <span data-ttu-id="af7d4-142">Abrufen der Daten</span><span class="sxs-lookup"><span data-stu-id="af7d4-142">Fetch data</span></span>

<span data-ttu-id="af7d4-143">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="af7d4-143">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="af7d4-144">Das Heraufsetzen eines Zählers auf einer Webseite erfordert normalerweise das Schreiben in JavaScript, aber Razor Components bietet einen besseren Ansatz mit C#.</span><span class="sxs-lookup"><span data-stu-id="af7d4-144">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

<span data-ttu-id="af7d4-145">*WebApplication1/Components/Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="af7d4-145">*WebApplication1/Components/Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor)]

<span data-ttu-id="af7d4-146">Eine Anforderung für `/counter` im Browser, laut der `@page` -Direktive am Anfang, bewirkt, dass die Leistungsindikator-Komponente, um seinen Inhalt zu rendern.</span><span class="sxs-lookup"><span data-stu-id="af7d4-146">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="af7d4-147">Rendern von Komponenten in eine in-Memory-Darstellung der Render-Struktur, die zur Aktualisierung der Benutzeroberfläche in eine flexible und effiziente Möglichkeit verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="af7d4-147">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="af7d4-148">Jedes Mal die **hier klicken** ausgewählt ist:</span><span class="sxs-lookup"><span data-stu-id="af7d4-148">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="af7d4-149">Die `onclick` Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="af7d4-149">The `onclick` event is fired.</span></span>
* <span data-ttu-id="af7d4-150">Die `IncrementCount` -Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="af7d4-150">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="af7d4-151">Die `currentCount` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="af7d4-151">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="af7d4-152">Die Komponente wird erneut gerendert.</span><span class="sxs-lookup"><span data-stu-id="af7d4-152">The component is rendered again.</span></span>

<span data-ttu-id="af7d4-153">Die Runtime vergleicht den neuen Inhalt zum vorherigen Inhalt und den geänderten Inhalt gilt nur, die (DOKUMENTOBJEKTMODELL).</span><span class="sxs-lookup"><span data-stu-id="af7d4-153">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="af7d4-154">Fügen Sie eine Komponente an eine andere Komponente, die mithilfe einer HTML-ähnliche Syntax.</span><span class="sxs-lookup"><span data-stu-id="af7d4-154">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="af7d4-155">Parameter der Komponente werden mithilfe von Attributen oder untergeordneten Inhalt angegeben.</span><span class="sxs-lookup"><span data-stu-id="af7d4-155">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="af7d4-156">Eine Komponente eines Leistungsindikators kann z. B. an der app-Startseite hinzugefügt werden, durch das Hinzufügen einer `<Counter />` Element an die Index-Komponente.</span><span class="sxs-lookup"><span data-stu-id="af7d4-156">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="af7d4-157">*WebApplication1/Components/Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="af7d4-157">*WebApplication1/Components/Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="af7d4-158">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="af7d4-158">Run the app.</span></span> <span data-ttu-id="af7d4-159">Die Startseite verfügt über eine eigene Leistungsindikator.</span><span class="sxs-lookup"><span data-stu-id="af7d4-159">The homepage has its own counter.</span></span>

<span data-ttu-id="af7d4-160">Um einen Parameter an die Leistungsindikator-Komponente hinzuzufügen, aktualisieren Sie der Komponente `@functions` blockieren:</span><span class="sxs-lookup"><span data-stu-id="af7d4-160">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="af7d4-161">Hinzufügen einer Eigenschaft für `IncrementAmount` ergänzt die `[Parameter]` Attribut.</span><span class="sxs-lookup"><span data-stu-id="af7d4-161">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="af7d4-162">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="af7d4-162">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="af7d4-163">*WebApplication1/Components/Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="af7d4-163">*WebApplication1/Components/Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=4,8)]

<span data-ttu-id="af7d4-164">Geben Sie unter Verwendung eines Attributs einen `IncrementAmount`-Parameter in das `<Counter>`-Element der Home-Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="af7d4-164">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="af7d4-165">*WebApplication1/Components/Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="af7d4-165">*WebApplication1/Components/Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor)]

<span data-ttu-id="af7d4-166">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="af7d4-166">Run the app.</span></span> <span data-ttu-id="af7d4-167">Die Startseite verfügt über eine eigene Zähler, der mit zehn jedes Mal erhöht die **hier klicken** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="af7d4-167">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="af7d4-168">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="af7d4-168">Next steps</span></span>

<xref:tutorials/first-razor-components-app>
