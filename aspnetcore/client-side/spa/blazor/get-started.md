---
title: Erste Schritte mit Blazor
author: guardrex
description: Erfahren Sie, wie Blazor Einstieg durch Erstellen und ändern ein Projekt Blazor.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2019
uid: spa/blazor/get-started
ms.openlocfilehash: 8c984bab8a13b4fc2d87fd1a7e0b285dfa25ba09
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159586"
---
# <a name="get-started-with-blazor"></a><span data-ttu-id="3cdc6-103">Erste Schritte mit Blazor</span><span class="sxs-lookup"><span data-stu-id="3cdc6-103">Get started with Blazor</span></span>

<span data-ttu-id="3cdc6-104">Durch [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3cdc6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3cdc6-105">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cdc6-105">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cdc6-106">Erforderliche Komponenten:</span><span class="sxs-lookup"><span data-stu-id="3cdc6-106">Prerequisites:</span></span>

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

<span data-ttu-id="3cdc6-107">So erstellen Ihr erste Blazor-Projekt in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="3cdc6-107">To create your first Blazor project in Visual Studio:</span></span>

1. <span data-ttu-id="3cdc6-108">Installieren Sie das neueste [Blazor sprachdiensteerweiterung](https://go.microsoft.com/fwlink/?linkid=870389) in Visual Studio Marketplace.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-108">Install the latest [Blazor Language Services extension](https://go.microsoft.com/fwlink/?linkid=870389) from the Visual Studio Marketplace.</span></span> <span data-ttu-id="3cdc6-109">Dieser Schritt stellt Blazor Vorlagen in Visual Studio zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-109">This step makes Blazor templates available to Visual Studio.</span></span>
1. <span data-ttu-id="3cdc6-110">Stellen Sie die Vorlagen Blazor mithilfe des folgenden Befehls in einer Befehlsshell für die Verwendung mit .NET Core-CLI zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="3cdc6-110">Make the Blazor templates available for use with the .NET Core CLI by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates
   ```

1. <span data-ttu-id="3cdc6-111">Wählen Sie **Datei** > **neues Projekt** > **Web** > **ASP.NET Core-Webanwendung**.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-111">Select **File** > **New Project** > **Web** > **ASP.NET Core Web Application**.</span></span>
1. <span data-ttu-id="3cdc6-112">Stellen Sie sicher, dass **.NET Core** und **ASP.NET Core 3.0** oben ausgewählt werden.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-112">Make sure **.NET Core** and **ASP.NET Core 3.0** are selected at the top.</span></span>
1. <span data-ttu-id="3cdc6-113">Wählen Sie die **Blazor**-Vorlage und dann **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-113">Choose the **Blazor** template and select **OK**.</span></span>
1. <span data-ttu-id="3cdc6-114">Drücken Sie **F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-114">Press **F5** to run the app.</span></span>

<span data-ttu-id="3cdc6-115">Herzlichen Glückwunsch!</span><span class="sxs-lookup"><span data-stu-id="3cdc6-115">Congratulations!</span></span> <span data-ttu-id="3cdc6-116">Sie haben soeben Ihre erste app für die Blazor ausgeführt!</span><span class="sxs-lookup"><span data-stu-id="3cdc6-116">You just ran your first Blazor app!</span></span>

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

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="3cdc6-117">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="3cdc6-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="3cdc6-118">Erforderliche Komponenten:</span><span class="sxs-lookup"><span data-stu-id="3cdc6-118">Prerequisites:</span></span>

* [<span data-ttu-id="3cdc6-119">.NET Core SDK 3.0 (Vorschau)</span><span class="sxs-lookup"><span data-stu-id="3cdc6-119">.NET Core SDK 3.0 Preview</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. <span data-ttu-id="3cdc6-120">Fügen Sie die Vorlagen Blazor mithilfe des folgenden Befehls in einer Befehlsshell hinzu:</span><span class="sxs-lookup"><span data-stu-id="3cdc6-120">Add the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates
   ```

1. <span data-ttu-id="3cdc6-121">Erstellen Sie Ihr erste Blazor-Projekt in einer Befehlsshell ein:</span><span class="sxs-lookup"><span data-stu-id="3cdc6-121">Create your first Blazor project in a command shell:</span></span>

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. <span data-ttu-id="3cdc6-122">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-122">In a browser, navigate to `https://localhost:5001`.</span></span>

<span data-ttu-id="3cdc6-123">Herzlichen Glückwunsch!</span><span class="sxs-lookup"><span data-stu-id="3cdc6-123">Congratulations!</span></span> <span data-ttu-id="3cdc6-124">Sie haben soeben Ihre erste app für die Blazor ausgeführt!</span><span class="sxs-lookup"><span data-stu-id="3cdc6-124">You just ran your first Blazor app!</span></span>

---

## <a name="blazor-project"></a><span data-ttu-id="3cdc6-125">Blazor-Projekt</span><span class="sxs-lookup"><span data-stu-id="3cdc6-125">Blazor project</span></span>

<span data-ttu-id="3cdc6-126">Wenn die app ausgeführt wird, stehen mehrere Seiten von Registerkarten in der Randleiste aus:</span><span class="sxs-lookup"><span data-stu-id="3cdc6-126">When the app is run, multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="3cdc6-127">Startseite</span><span class="sxs-lookup"><span data-stu-id="3cdc6-127">Home</span></span>
* <span data-ttu-id="3cdc6-128">Zähler</span><span class="sxs-lookup"><span data-stu-id="3cdc6-128">Counter</span></span>
* <span data-ttu-id="3cdc6-129">Abrufen der Daten</span><span class="sxs-lookup"><span data-stu-id="3cdc6-129">Fetch data</span></span>

<span data-ttu-id="3cdc6-130">Wählen Sie auf der Seite Leistungsindikator der **hier klicken** Schaltfläche, um die Zähler ohne eine seitenaktualisierung zu erhöhen.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-130">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="3cdc6-131">Erhöhen eines Zählers auf einer Webseite bei normalerweise erfordert das Schreiben von JavaScript allerdings Blazor bietet einen besseren Ansatz mit C#.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-131">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

<span data-ttu-id="3cdc6-132">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3cdc6-132">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

<span data-ttu-id="3cdc6-133">Eine Anforderung für `/counter` im Browser, laut der `@page` -Direktive am Anfang, bewirkt, dass die Leistungsindikator-Komponente, um seinen Inhalt zu rendern.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-133">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the Counter component to render its content.</span></span> <span data-ttu-id="3cdc6-134">Rendern von Komponenten in eine in-Memory-Darstellung der Render-Struktur, die zur Aktualisierung der Benutzeroberfläche in eine flexible und effiziente Möglichkeit verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-134">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="3cdc6-135">Jedes Mal die **hier klicken** ausgewählt ist:</span><span class="sxs-lookup"><span data-stu-id="3cdc6-135">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="3cdc6-136">Die `onclick` Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-136">The `onclick` event is fired.</span></span>
* <span data-ttu-id="3cdc6-137">Die `IncrementCount` -Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-137">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="3cdc6-138">Die `currentCount` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-138">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="3cdc6-139">Die Komponente wird erneut gerendert.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-139">The component is rendered again.</span></span>

<span data-ttu-id="3cdc6-140">Die Runtime vergleicht den neuen Inhalt zum vorherigen Inhalt und den geänderten Inhalt gilt nur, die (DOKUMENTOBJEKTMODELL).</span><span class="sxs-lookup"><span data-stu-id="3cdc6-140">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="3cdc6-141">Fügen Sie eine Komponente an eine andere Komponente, die mithilfe einer HTML-ähnliche Syntax.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-141">Add a component to another component using an HTML-like syntax.</span></span> <span data-ttu-id="3cdc6-142">Parameter der Komponente werden mithilfe von Attributen oder untergeordneten Inhalt angegeben.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-142">Component parameters are specified using attributes or child content.</span></span> <span data-ttu-id="3cdc6-143">Eine Komponente eines Leistungsindikators kann z. B. an der app-Startseite hinzugefügt werden, durch das Hinzufügen einer `<Counter />` Element an die Index-Komponente.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-143">For example, a Counter component can be added to the app's homepage by adding a `<Counter />` element to the Index component.</span></span>

<span data-ttu-id="3cdc6-144">In *Pages/Index.cshtml*, ersetzen Sie die Umfrage Eingabeaufforderung Komponente mit der eine Komponente eines Leistungsindikators:</span><span class="sxs-lookup"><span data-stu-id="3cdc6-144">In *Pages/Index.cshtml*, replace the Survey Prompt component with a Counter component:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

<span data-ttu-id="3cdc6-145">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-145">Run the app.</span></span> <span data-ttu-id="3cdc6-146">Die Startseite verfügt über eine eigene Leistungsindikator.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-146">The homepage has its own counter.</span></span>

<span data-ttu-id="3cdc6-147">Um einen Parameter an die Leistungsindikator-Komponente hinzuzufügen, aktualisieren Sie der Komponente `@functions` blockieren:</span><span class="sxs-lookup"><span data-stu-id="3cdc6-147">To add a parameter to the Counter component, update the component's `@functions` block:</span></span>

* <span data-ttu-id="3cdc6-148">Hinzufügen einer Eigenschaft für `IncrementAmount` ergänzt die `[Parameter]` Attribut.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-148">Add a property for `IncrementAmount` decorated with the `[Parameter]` attribute.</span></span>
* <span data-ttu-id="3cdc6-149">Ändern der `IncrementCount` zu verwendende Methode der `IncrementAmount` beim Erhöhen des Werts der `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-149">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="3cdc6-150">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3cdc6-150">*Pages/Counter.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4,8)]

<span data-ttu-id="3cdc6-151">Geben Sie eine `IncrementAmount` Parameter in der Home-Komponente `<Counter>` Element unter Verwendung eines Attributs.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-151">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="3cdc6-152">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="3cdc6-152">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

<span data-ttu-id="3cdc6-153">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-153">Run the app.</span></span> <span data-ttu-id="3cdc6-154">Die Startseite verfügt über eine eigene Zähler, der mit zehn jedes Mal erhöht die **hier klicken** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="3cdc6-154">The homepage has its own counter that increments by ten each time the **Click me** button is selected.</span></span>

## <a name="next-steps"></a><span data-ttu-id="3cdc6-155">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="3cdc6-155">Next steps</span></span>

<xref:tutorials/first-razor-components-app>
