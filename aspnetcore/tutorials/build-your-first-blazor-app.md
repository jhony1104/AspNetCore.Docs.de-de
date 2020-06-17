---
title: Erstellen Ihrer ersten Blazor-App
author: guardrex
description: Erstellen Sie Schritt für Schritt eine Blazor-App.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-blazor-app
ms.openlocfilehash: ce3f20f7ee4ccfa73afc5f80a4429d9f4fe05591
ms.sourcegitcommit: 05490855e0c70565f0c4b509d392b0828bcfd141
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84507256"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="f6efd-103">Erstellen Ihrer ersten Blazor-App</span><span class="sxs-lookup"><span data-stu-id="f6efd-103">Build your first Blazor app</span></span>

<span data-ttu-id="f6efd-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f6efd-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f6efd-105">In diesem Tutorial erfahren Sie, wie Sie eine Blazor-App erstellen und ändern.</span><span class="sxs-lookup"><span data-stu-id="f6efd-105">This tutorial shows you how to build and modify a Blazor app.</span></span> <span data-ttu-id="f6efd-106">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f6efd-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f6efd-107">Erstellen eines Aufgabenlisten-Blazor-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="f6efd-107">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="f6efd-108">Ändern von Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="f6efd-108">Modify Razor components</span></span>
> * <span data-ttu-id="f6efd-109">Verwenden von Ereignisbehandlung und Datenbindung in Komponenten</span><span class="sxs-lookup"><span data-stu-id="f6efd-109">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="f6efd-110">Verwenden von Abhängigkeitsinjektion (DI, Dependency Injection) und Routing in einer Blazor-App</span><span class="sxs-lookup"><span data-stu-id="f6efd-110">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="f6efd-111">Am Ende dieses Tutorials verfügen Sie über eine funktionierende Aufgabenlisten-App.</span><span class="sxs-lookup"><span data-stu-id="f6efd-111">At the end of this tutorial, you'll have a working todo list app.</span></span>

## <a name="build-components"></a><span data-ttu-id="f6efd-112">Erstellen von Komponenten</span><span class="sxs-lookup"><span data-stu-id="f6efd-112">Build components</span></span>

1. <span data-ttu-id="f6efd-113">Befolgen Sie die Anweisungen im Artikel <xref:blazor/get-started>, um ein Blazor-Projekt für dieses Tutorial zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f6efd-113">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="f6efd-114">Name der *ToDoList* für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="f6efd-114">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="f6efd-115">Navigieren Sie zu jeder der drei Seiten der App im Ordner *Pages*: „Home“, „Counter“ und „Fetch data“.</span><span class="sxs-lookup"><span data-stu-id="f6efd-115">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="f6efd-116">Diese Seiten werden durch die Razor-Komponentendateien *Index.razor*, *Counter.razor* und *FetchData.razor* implementiert.</span><span class="sxs-lookup"><span data-stu-id="f6efd-116">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="f6efd-117">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="f6efd-117">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="f6efd-118">Für das Heraufsetzen eines Zählers auf einer Webseite müssen Sie JavaScript-Code schreiben.</span><span class="sxs-lookup"><span data-stu-id="f6efd-118">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="f6efd-119">Mit Blazor können Sie stattdessen auch C#-Code schreiben.</span><span class="sxs-lookup"><span data-stu-id="f6efd-119">With Blazor, you can write C# instead.</span></span>

1. <span data-ttu-id="f6efd-120">Sehen Sie sich die Implementierung der `Counter`-Komponente in der *Counter.razor*-Datei an.</span><span class="sxs-lookup"><span data-stu-id="f6efd-120">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="f6efd-121">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="f6efd-121">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="f6efd-122">Die Benutzeroberfläche der `Counter`-Komponente wird mithilfe von HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="f6efd-122">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="f6efd-123">Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f6efd-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="f6efd-124">Das HTML-Markup und die C#-Renderinglogik werden zur Erstellungszeit in eine Komponentenklasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="f6efd-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="f6efd-125">Der Name der generierten .NET-Klasse stimmt mit dem Namen der Datei überein.</span><span class="sxs-lookup"><span data-stu-id="f6efd-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="f6efd-126">Member der Komponentenklasse werden in einem `@code`-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="f6efd-126">Members of the component class are defined in an `@code` block.</span></span> <span data-ttu-id="f6efd-127">Im `@code`-Block werden der Komponentenstatus (Eigenschaften, Felder) und Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben.</span><span class="sxs-lookup"><span data-stu-id="f6efd-127">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="f6efd-128">Diese Member werden dann als Teil der Renderinglogik der Komponente und zur Behandlung von Ereignissen verwendet.</span><span class="sxs-lookup"><span data-stu-id="f6efd-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="f6efd-129">Wenn die **Hier klicken**-Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="f6efd-129">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="f6efd-130">Der registrierte `onclick`-Handler der `Counter`-Komponente wird aufgerufen (die `IncrementCount`-Methode).</span><span class="sxs-lookup"><span data-stu-id="f6efd-130">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="f6efd-131">Die `Counter`-Komponente generiert ihre Renderstruktur neu.</span><span class="sxs-lookup"><span data-stu-id="f6efd-131">The `Counter` component regenerates its render tree.</span></span>
   * <span data-ttu-id="f6efd-132">Die neue Renderstruktur wird mit der vorherigen verglichen.</span><span class="sxs-lookup"><span data-stu-id="f6efd-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="f6efd-133">Es werden nur Änderungen des Dokumentobjektmodells (Document Object Model, DOM) angewendet.</span><span class="sxs-lookup"><span data-stu-id="f6efd-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="f6efd-134">Der angezeigte Zähler wird aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="f6efd-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="f6efd-135">Ändern Sie die C#-Logik der `Counter`-Komponente, um das Zählerinkrement von eins auf zwei heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="f6efd-135">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="f6efd-136">Erstellen Sie die App neu, und führen Sie sie aus, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="f6efd-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="f6efd-137">Wählen Sie die Schaltfläche **Hier klicken** aus.</span><span class="sxs-lookup"><span data-stu-id="f6efd-137">Select the **Click me** button.</span></span> <span data-ttu-id="f6efd-138">Der Leistungsindikator wird um zwei erhöht.</span><span class="sxs-lookup"><span data-stu-id="f6efd-138">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="f6efd-139">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="f6efd-139">Use components</span></span>

<span data-ttu-id="f6efd-140">Beziehen Sie eine Komponente mithilfe einer HTML-Syntax in eine andere Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="f6efd-140">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="f6efd-141">Fügen Sie die `Index`-Komponente der `Counter`-Komponente der App durch Hinzufügen eines `<Counter />`-Elements zur `Index`-Komponente (*Index.razor*) hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6efd-141">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span>

   <span data-ttu-id="f6efd-142">Wenn Sie für diese Oberfläche Blazor WebAssembly verwenden, verwendet die `Index`-Komponente eine `SurveyPrompt`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="f6efd-142">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span> <span data-ttu-id="f6efd-143">Ersetzen Sie das `<SurveyPrompt>`-Element durch ein `<Counter />`-Element.</span><span class="sxs-lookup"><span data-stu-id="f6efd-143">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span> <span data-ttu-id="f6efd-144">Wenn Sie für diese Oberfläche eine Blazor Server-App verwenden, fügen Sie der `Index`-Komponente das `<Counter />`-Element hinzu:</span><span class="sxs-lookup"><span data-stu-id="f6efd-144">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span>

   <span data-ttu-id="f6efd-145">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="f6efd-145">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="f6efd-146">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="f6efd-146">Rebuild and run the app.</span></span> <span data-ttu-id="f6efd-147">Die `Index`-Komponente verfügt über einen eigenen Zähler.</span><span class="sxs-lookup"><span data-stu-id="f6efd-147">The `Index` component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="f6efd-148">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="f6efd-148">Component parameters</span></span>

<span data-ttu-id="f6efd-149">Komponenten können auch Parameter aufweisen.</span><span class="sxs-lookup"><span data-stu-id="f6efd-149">Components can also have parameters.</span></span> <span data-ttu-id="f6efd-150">Komponentenparameter werden mithilfe öffentlicher Eigenschaften für die Komponentenklasse mit dem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attribut definiert.</span><span class="sxs-lookup"><span data-stu-id="f6efd-150">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="f6efd-151">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="f6efd-151">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="f6efd-152">Ändern Sie den C#-Code `@code` der Komponente wie folgt:</span><span class="sxs-lookup"><span data-stu-id="f6efd-152">Update the component's `@code` C# code as follows:</span></span>

   * <span data-ttu-id="f6efd-153">Fügen Sie eine öffentliche `IncrementAmount`-Eigenschaft mit dem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6efd-153">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
   * <span data-ttu-id="f6efd-154">Ändern Sie die `IncrementCount`-Methode, sodass die Eigenschaft `IncrementAmount` verwendet wird, um den Wert von `currentCount` zu erhöhen.</span><span class="sxs-lookup"><span data-stu-id="f6efd-154">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="f6efd-155">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="f6efd-155">*Pages/Counter.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="f6efd-156">Geben Sie unter Verwendung eines Attributs einen `IncrementAmount`-Parameter im `<Counter>`-Element der `Index`-Komponente an.</span><span class="sxs-lookup"><span data-stu-id="f6efd-156">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="f6efd-157">Legen Sie den Wert fest, um den Zähler um zehn heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="f6efd-157">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="f6efd-158">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="f6efd-158">*Pages/Index.razor*:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="f6efd-159">Laden Sie die `Index`-Komponente neu.</span><span class="sxs-lookup"><span data-stu-id="f6efd-159">Reload the `Index` component.</span></span> <span data-ttu-id="f6efd-160">Der Zähler wird jedes Mal, wenn die Schaltfläche **Hier klicken** ausgewählt wird, um zehn heraufgesetzt.</span><span class="sxs-lookup"><span data-stu-id="f6efd-160">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="f6efd-161">Der Zähler in der `Counter`-Komponente wird weiter um eins erhöht.</span><span class="sxs-lookup"><span data-stu-id="f6efd-161">The counter in the `Counter` component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="f6efd-162">Weiterleiten an Komponenten</span><span class="sxs-lookup"><span data-stu-id="f6efd-162">Route to components</span></span>

<span data-ttu-id="f6efd-163">Die `@page`-Anweisung im oberen Teil der *Counter.razor*-Datei gibt an, dass die `Counter`-Komponente ein Routingendpunkt ist.</span><span class="sxs-lookup"><span data-stu-id="f6efd-163">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="f6efd-164">Die `Counter`-Komponente verarbeitet Anforderungen, die an `/counter` gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="f6efd-164">The `Counter` component handles requests sent to `/counter`.</span></span> <span data-ttu-id="f6efd-165">Ohne die `@page`-Anweisung behandelt eine Komponente keine weitergeleiteten Anforderungen, aber die Komponente kann immer noch von anderen Komponenten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f6efd-165">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="f6efd-166">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="f6efd-166">Dependency injection</span></span>

### <a name="blazor-server-experience"></a>Blazor<span data-ttu-id="f6efd-167"> Server-Benutzererfahrung</span><span class="sxs-lookup"><span data-stu-id="f6efd-167"> Server experience</span></span>

<span data-ttu-id="f6efd-168">Wenn Sie mit einer Blazor Server-App arbeiten, wird der `WeatherForecastService`-Dienst in `Startup.ConfigureServices` als [Singleton](xref:fundamentals/dependency-injection#service-lifetimes) registriert.</span><span class="sxs-lookup"><span data-stu-id="f6efd-168">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f6efd-169">Eine Instanz des Diensts steht überall in der App mittels [Abhängigkeitsinjektion (Dependency Injection, DI)](xref:fundamentals/dependency-injection) zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="f6efd-169">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="f6efd-170">Mit der [`@inject`](xref:mvc/views/razor#inject)-Anweisung wird die Instanz des `WeatherForecastService`-Diensts in die `FetchData`-Komponente eingefügt.</span><span class="sxs-lookup"><span data-stu-id="f6efd-170">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

<span data-ttu-id="f6efd-171">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="f6efd-171">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="f6efd-172">Die `FetchData`-Komponente verwendet den eingefügten Dienst wie `ForecastService`, um ein Array aus `WeatherForecast`-Objekten abzurufen:</span><span class="sxs-lookup"><span data-stu-id="f6efd-172">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Blazor<span data-ttu-id="f6efd-173"> WebAssembly-Benutzererfahrung</span><span class="sxs-lookup"><span data-stu-id="f6efd-173"> WebAssembly experience</span></span>

<span data-ttu-id="f6efd-174">Wenn Sie eine Blazor WebAssembly-App verwenden, wird <xref:System.Net.Http.HttpClient> eingefügt, um Wettervorhersagedaten aus der Datei *weather.json* im Ordner *wwwroot/sample-data* abzurufen.</span><span class="sxs-lookup"><span data-stu-id="f6efd-174">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span>

<span data-ttu-id="f6efd-175">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="f6efd-175">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="f6efd-176">Eine [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife rendert jede Vorhersageinstanz als eine Zeile in der Wetterdatentabelle:</span><span class="sxs-lookup"><span data-stu-id="f6efd-176">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="f6efd-177">Erstellen einer Aufgabenliste</span><span class="sxs-lookup"><span data-stu-id="f6efd-177">Build a todo list</span></span>

<span data-ttu-id="f6efd-178">Fügen Sie der App eine neue Komponente hinzu, die eine einfache Aufgabenliste implementiert.</span><span class="sxs-lookup"><span data-stu-id="f6efd-178">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="f6efd-179">Fügen Sie der App im Ordner *Seiten* eine neue `Todo` Razor-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6efd-179">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span> <span data-ttu-id="f6efd-180">Wenn Sie Visual Studio verwenden, klicken Sie mit der rechten Maustaste auf den Ordner **Seiten** und dann auf **Hinzufügen** > **Neues Element** >  **Razor-Komponente**.</span><span class="sxs-lookup"><span data-stu-id="f6efd-180">If you're using Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="f6efd-181">Nennen Sie die Datei der Komponente *Todo.razor*.</span><span class="sxs-lookup"><span data-stu-id="f6efd-181">Name the component's file *Todo.razor*.</span></span> <span data-ttu-id="f6efd-182">Fügen Sie dem Ordner **Seiten** in anderen Entwicklungsumgebungen eine leere Datei namens *Todo.razor* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6efd-182">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

1. <span data-ttu-id="f6efd-183">Geben Sie das ursprüngliche Markup für die Komponente an:</span><span class="sxs-lookup"><span data-stu-id="f6efd-183">Provide the initial markup for the component:</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="f6efd-184">Fügen Sie der Navigationsleiste die `Todo`-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6efd-184">Add the `Todo` component to the navigation bar.</span></span>

   <span data-ttu-id="f6efd-185">Die `NavMenu`-Komponente (*Shared/NavMenu.razor*) wird im Layout der App verwendet.</span><span class="sxs-lookup"><span data-stu-id="f6efd-185">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="f6efd-186">Layouts sind Komponenten, mit denen Sie die Duplizierung des Inhalts in der App vermeiden können.</span><span class="sxs-lookup"><span data-stu-id="f6efd-186">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   <span data-ttu-id="f6efd-187">Fügen Sie einen `Todo` für die `<NavLink>`-Komponente hinzu, indem Sie das folgende Listenelementmarkup unterhalb der vorhandenen Listenelemente in der *Datei Shared/NavMenu.razor* hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="f6efd-187">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="f6efd-188">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="f6efd-188">Rebuild and run the app.</span></span> <span data-ttu-id="f6efd-189">Besuchen Sie die neue Todo-Seite, um sicherzustellen, dass der Link zur `Todo`-Komponente funktioniert.</span><span class="sxs-lookup"><span data-stu-id="f6efd-189">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>

1. <span data-ttu-id="f6efd-190">Fügen Sie eine *TodoItem.cs*-Datei dem Stamm des Projekts hinzu, die eine Klasse zum Darstellen eines Aufgabenelements enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="f6efd-190">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="f6efd-191">Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="f6efd-191">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="f6efd-192">Kehren Sie zur `Todo`-Komponente (*Pages/Todo.razor*) zurück:</span><span class="sxs-lookup"><span data-stu-id="f6efd-192">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="f6efd-193">Fügen Sie ein Feld für die Aufgabenelemente in einem `@code`-Block hinzu.</span><span class="sxs-lookup"><span data-stu-id="f6efd-193">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="f6efd-194">Die `Todo`-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.</span><span class="sxs-lookup"><span data-stu-id="f6efd-194">The `Todo` component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="f6efd-195">Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="f6efd-195">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="f6efd-196">Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgabenelemente hinzugefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="f6efd-196">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="f6efd-197">Fügen Sie eine Texteingabe (`<input>`) und eine Schaltfläche (`<button>`) unterhalb der Liste (`<ul>...</ul>`) hinzu:</span><span class="sxs-lookup"><span data-stu-id="f6efd-197">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="f6efd-198">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="f6efd-198">Rebuild and run the app.</span></span> <span data-ttu-id="f6efd-199">Bei Auswahl der Schaltfläche **Todo-Element hinzufügen** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="f6efd-199">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="f6efd-200">Fügen Sie eine `AddTodo`-Methode zur `Todo`-Komponente hinzu, und registrieren Sie sie mit dem `@onclick`-Attribut für eine Schaltflächenauswahl.</span><span class="sxs-lookup"><span data-stu-id="f6efd-200">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="f6efd-201">Die C#-Methode `AddTodo` wird aufgerufen, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="f6efd-201">The `AddTodo` C# method is called when the button is selected:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="f6efd-202">Um den Titel des neuen Aufgabenelements abzurufen, fügen Sie oben im `@code`-Block ein `newTodo`-Zeichenfolgenfeld hinzu, und binden Sie es mithilfe des `bind`-Attributs im `<input>`-Element an den Wert der Texteingabe:</span><span class="sxs-lookup"><span data-stu-id="f6efd-202">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="f6efd-203">Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="f6efd-203">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="f6efd-204">Löschen Sie den Wert der Texteingabe, indem Sie für `newTodo` eine leere Zeichenfolge festlegen:</span><span class="sxs-lookup"><span data-stu-id="f6efd-204">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="f6efd-205">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="f6efd-205">Rebuild and run the app.</span></span> <span data-ttu-id="f6efd-206">Fügen Sie der Aufgabenliste einige Aufgabenelemente hinzu, um den neuen Code zu testen.</span><span class="sxs-lookup"><span data-stu-id="f6efd-206">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="f6efd-207">Der Titeltext für die einzelnen Aufgabenelemente kann als bearbeitbar festgelegt werden, und ein Kontrollkästchen kann dem Benutzer helfen, die erledigten Elemente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="f6efd-207">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="f6efd-208">Fügen Sie für jedes Aufgabenelement eine Kontrollkästcheneingabe hinzu, und binden Sie ihren Wert an die `IsDone`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f6efd-208">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="f6efd-209">Ändern Sie `@todo.Title` in ein `<input>`-Element, das an `@todo.Title` gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="f6efd-209">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="f6efd-210">Um sicherzustellen, dass diese Werte gebunden sind, aktualisieren Sie den `<h3>`-Header, um die Anzahl der noch nicht erledigten Aufgabenelemente anzuzeigen (`IsDone` ist `false`).</span><span class="sxs-lookup"><span data-stu-id="f6efd-210">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="f6efd-211">Die abgeschlossene `Todo`-Komponente (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="f6efd-211">The completed `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="f6efd-212">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="f6efd-212">Rebuild and run the app.</span></span> <span data-ttu-id="f6efd-213">Fügen Sie Aufgabenelemente hinzu, um den neuen Code zu testen.</span><span class="sxs-lookup"><span data-stu-id="f6efd-213">Add todo items to test the new code.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f6efd-214">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="f6efd-214">Next steps</span></span>

<span data-ttu-id="f6efd-215">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="f6efd-215">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f6efd-216">Erstellen eines Aufgabenlisten-Blazor-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="f6efd-216">Create a todo list Blazor app project</span></span>
> * <span data-ttu-id="f6efd-217">Ändern von Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="f6efd-217">Modify Razor components</span></span>
> * <span data-ttu-id="f6efd-218">Verwenden von Ereignisbehandlung und Datenbindung in Komponenten</span><span class="sxs-lookup"><span data-stu-id="f6efd-218">Use event handling and data binding in components</span></span>
> * <span data-ttu-id="f6efd-219">Verwenden von Abhängigkeitsinjektion (DI, Dependency Injection) und Routing in einer Blazor-App</span><span class="sxs-lookup"><span data-stu-id="f6efd-219">Use dependency injection (DI) and routing in a Blazor app</span></span>

<span data-ttu-id="f6efd-220">Erfahren Sie, wie Sie Komponenten erstellen und verwenden:</span><span class="sxs-lookup"><span data-stu-id="f6efd-220">Learn how to build and use components:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
