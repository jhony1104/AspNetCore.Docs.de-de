---
title: "title: 'Erstellen Ihrer ersten Blazor-App' author: guardrex description: 'Erstellen Sie Schritt für Schritt eine Blazor-App.'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.0' ms.author: riande ms.custom: mvc ms.date: 05/19/2020 no-loc:"
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
ms.openlocfilehash: 8cf356b5c9876a4114942af27a22df6bd324d0e2
ms.sourcegitcommit: 829dca1d5a7dcccbfe90644101c6be1d1c94ac62
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84355213"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="6124e-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6124e-103">'Blazor'</span></span>

<span data-ttu-id="6124e-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6124e-104">'Identity'</span></span>

<span data-ttu-id="6124e-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6124e-105">'Let's Encrypt'</span></span> <span data-ttu-id="6124e-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6124e-106">'Razor'</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6124e-107">'SignalR' uid: tutorials/first-blazor-app</span><span class="sxs-lookup"><span data-stu-id="6124e-107">'SignalR' uid: tutorials/first-blazor-app</span></span>
> * <span data-ttu-id="6124e-108">Erstellen Ihrer ersten Blazor-App</span><span class="sxs-lookup"><span data-stu-id="6124e-108">Build your first Blazor app</span></span>
> * <span data-ttu-id="6124e-109">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6124e-109">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>
> * <span data-ttu-id="6124e-110">In diesem Tutorial erfahren Sie, wie Sie eine Blazor-App erstellen und ändern.</span><span class="sxs-lookup"><span data-stu-id="6124e-110">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="6124e-111">Sie lernen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6124e-111">You learn how to:</span></span>

## <a name="build-components"></a><span data-ttu-id="6124e-112">Erstellen eines Aufgabenlisten-Blazor-App-Projekts</span><span class="sxs-lookup"><span data-stu-id="6124e-112">Create a todo list Blazor app project</span></span>

1. <span data-ttu-id="6124e-113">Ändern von Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="6124e-113">Modify Razor components</span></span> <span data-ttu-id="6124e-114">Verwenden von Ereignisbehandlung und Datenbindung in Komponenten</span><span class="sxs-lookup"><span data-stu-id="6124e-114">Use event handling and data binding in components</span></span>

1. <span data-ttu-id="6124e-115">Verwenden von Abhängigkeitsinjektion (DI, Dependency Injection) und Routing in einer Blazor-App</span><span class="sxs-lookup"><span data-stu-id="6124e-115">Use dependency injection (DI) and routing in a Blazor app</span></span> <span data-ttu-id="6124e-116">Am Ende dieses Tutorials verfügen Sie über eine funktionierende Chat-App.</span><span class="sxs-lookup"><span data-stu-id="6124e-116">At the end of this tutorial, you'll have a working chat app.</span></span>

1. <span data-ttu-id="6124e-117">Erstellen von Komponenten</span><span class="sxs-lookup"><span data-stu-id="6124e-117">Build components</span></span> <span data-ttu-id="6124e-118">Befolgen Sie die Anweisungen im Artikel <xref:blazor/get-started>, um ein Blazor-Projekt für dieses Tutorial zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="6124e-118">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span> <span data-ttu-id="6124e-119">Name der *ToDoList* für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="6124e-119">Name the project *ToDoList*.</span></span>

1. <span data-ttu-id="6124e-120">Navigieren Sie zu jeder der drei Seiten der App im Ordner *Pages*: „Home“, „Counter“ und „Fetch data“.</span><span class="sxs-lookup"><span data-stu-id="6124e-120">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span>

   <span data-ttu-id="6124e-121">Diese Seiten werden durch die Razor-Komponentendateien *Index.razor*, *Counter.razor* und *FetchData.razor* implementiert.</span><span class="sxs-lookup"><span data-stu-id="6124e-121">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="6124e-122">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="6124e-122">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="6124e-123">Für das Heraufsetzen eines Zählers auf einer Webseite müssen Sie JavaScript-Code schreiben.</span><span class="sxs-lookup"><span data-stu-id="6124e-123">Incrementing a counter in a webpage normally requires writing JavaScript.</span></span> <span data-ttu-id="6124e-124">Mit Blazor können Sie stattdessen auch C#-Code schreiben.</span><span class="sxs-lookup"><span data-stu-id="6124e-124">With Blazor, you can write C# instead.</span></span> <span data-ttu-id="6124e-125">Sehen Sie sich die Implementierung der `Counter`-Komponente in der *Counter.razor*-Datei an.</span><span class="sxs-lookup"><span data-stu-id="6124e-125">Examine the implementation of the `Counter` component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="6124e-126">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6124e-126">*Pages/Counter.razor*:</span></span> <span data-ttu-id="6124e-127">Die Benutzeroberfläche der `Counter`-Komponente wird mithilfe von HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="6124e-127">The UI of the `Counter` component is defined using HTML.</span></span> <span data-ttu-id="6124e-128">Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6124e-128">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span>

   <span data-ttu-id="6124e-129">Das HTML-Markup und die C#-Renderinglogik werden zur Erstellungszeit in eine Komponentenklasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="6124e-129">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span>

   * <span data-ttu-id="6124e-130">Der Name der generierten .NET-Klasse stimmt mit dem Namen der Datei überein.</span><span class="sxs-lookup"><span data-stu-id="6124e-130">The name of the generated .NET class matches the file name.</span></span>
   * <span data-ttu-id="6124e-131">Member der Komponentenklasse werden in einem `@code`-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="6124e-131">Members of the component class are defined in an `@code` block.</span></span>
   * <span data-ttu-id="6124e-132">Im `@code`-Block werden der Komponentenstatus (Eigenschaften, Felder) und Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben.</span><span class="sxs-lookup"><span data-stu-id="6124e-132">In the `@code` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span>
   * <span data-ttu-id="6124e-133">Diese Member werden dann als Teil der Renderinglogik der Komponente und zur Behandlung von Ereignissen verwendet.</span><span class="sxs-lookup"><span data-stu-id="6124e-133">These members are then used as part of the component's rendering logic and for handling events.</span></span> <span data-ttu-id="6124e-134">Wenn die **Hier klicken**-Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="6124e-134">When the **Click me** button is selected:</span></span>

1. <span data-ttu-id="6124e-135">Der registrierte `onclick`-Handler der `Counter`-Komponente wird aufgerufen (die `IncrementCount`-Methode).</span><span class="sxs-lookup"><span data-stu-id="6124e-135">The `Counter` component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="6124e-136">Die `Counter`-Komponente generiert ihre Renderstruktur neu.</span><span class="sxs-lookup"><span data-stu-id="6124e-136">The `Counter` component regenerates its render tree.</span></span> <span data-ttu-id="6124e-137">Die neue Renderstruktur wird mit der vorherigen verglichen.</span><span class="sxs-lookup"><span data-stu-id="6124e-137">The new render tree is compared to the previous one.</span></span> <span data-ttu-id="6124e-138">Es werden nur Änderungen des Dokumentobjektmodells (Document Object Model, DOM) angewendet.</span><span class="sxs-lookup"><span data-stu-id="6124e-138">Only modifications to the Document Object Model (DOM) are applied.</span></span>

## <a name="use-components"></a><span data-ttu-id="6124e-139">Der angezeigte Zähler wird aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="6124e-139">The displayed count is updated.</span></span>

<span data-ttu-id="6124e-140">Ändern Sie die C#-Logik der `Counter`-Komponente, um das Zählerinkrement von eins auf zwei heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="6124e-140">Modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

1. <span data-ttu-id="6124e-141">Erstellen Sie die App neu, und führen Sie sie aus, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="6124e-141">Rebuild and run the app to see the changes.</span></span>

   <span data-ttu-id="6124e-142">Wählen Sie die Schaltfläche **Hier klicken** aus.</span><span class="sxs-lookup"><span data-stu-id="6124e-142">Select the **Click me** button.</span></span> <span data-ttu-id="6124e-143">Der Leistungsindikator wird um zwei erhöht.</span><span class="sxs-lookup"><span data-stu-id="6124e-143">The counter increments by two.</span></span> <span data-ttu-id="6124e-144">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="6124e-144">Use components</span></span>

   <span data-ttu-id="6124e-145">Beziehen Sie eine Komponente mithilfe einer HTML-Syntax in eine andere Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="6124e-145">Include a component in another component using an HTML syntax.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="6124e-146">Fügen Sie die `Index`-Komponente der `Counter`-Komponente der App durch Hinzufügen eines `<Counter />`-Elements zur `Index`-Komponente (*Index.razor*) hinzu.</span><span class="sxs-lookup"><span data-stu-id="6124e-146">Add the `Counter` component to the app's `Index` component by adding a `<Counter />` element to the `Index` component (*Index.razor*).</span></span> <span data-ttu-id="6124e-147">Wenn Sie für diese Oberfläche Blazor WebAssembly verwenden, verwendet die `Index`-Komponente eine `SurveyPrompt`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="6124e-147">If you're using Blazor WebAssembly for this experience, a `SurveyPrompt` component is used by the `Index` component.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="6124e-148">Ersetzen Sie das `<SurveyPrompt>`-Element durch ein `<Counter />`-Element.</span><span class="sxs-lookup"><span data-stu-id="6124e-148">Replace the `<SurveyPrompt>` element with a `<Counter />` element.</span></span>

<span data-ttu-id="6124e-149">Wenn Sie für diese Oberfläche eine Blazor Server-App verwenden, fügen Sie der `Index`-Komponente das `<Counter />`-Element hinzu:</span><span class="sxs-lookup"><span data-stu-id="6124e-149">If you're using a Blazor Server app for this experience, add the `<Counter />` element to the `Index` component:</span></span> <span data-ttu-id="6124e-150">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="6124e-150">*Pages/Index.razor*:</span></span> <span data-ttu-id="6124e-151">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="6124e-151">Rebuild and run the app.</span></span>

1. <span data-ttu-id="6124e-152">Die `Index`-Komponente verfügt über einen eigenen Zähler.</span><span class="sxs-lookup"><span data-stu-id="6124e-152">The `Index` component has its own counter.</span></span>

   * <span data-ttu-id="6124e-153">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="6124e-153">Component parameters</span></span>
   * <span data-ttu-id="6124e-154">Komponenten können auch Parameter aufweisen.</span><span class="sxs-lookup"><span data-stu-id="6124e-154">Components can also have parameters.</span></span>

   <span data-ttu-id="6124e-155">Komponentenparameter werden mithilfe öffentlicher Eigenschaften für die Komponentenklasse mit dem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attribut definiert.</span><span class="sxs-lookup"><span data-stu-id="6124e-155">Component parameters are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. <span data-ttu-id="6124e-156">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="6124e-156">Use attributes to specify arguments for a component in markup.</span></span> <span data-ttu-id="6124e-157">Ändern Sie den C#-Code `@code` der Komponente wie folgt:</span><span class="sxs-lookup"><span data-stu-id="6124e-157">Update the component's `@code` C# code as follows:</span></span>

   <span data-ttu-id="6124e-158">Fügen Sie eine öffentliche `IncrementAmount`-Eigenschaft mit dem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="6124e-158">Add a public `IncrementAmount` property with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="6124e-159">Ändern Sie die `IncrementCount`-Methode, sodass die Eigenschaft `IncrementAmount` verwendet wird, um den Wert von `currentCount` zu erhöhen.</span><span class="sxs-lookup"><span data-stu-id="6124e-159">Change the `IncrementCount` method to use the `IncrementAmount` property when increasing the value of `currentCount`.</span></span> <span data-ttu-id="6124e-160">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="6124e-160">*Pages/Counter.razor*:</span></span> <span data-ttu-id="6124e-161">Geben Sie unter Verwendung eines Attributs einen `IncrementAmount`-Parameter im `<Counter>`-Element der `Index`-Komponente an.</span><span class="sxs-lookup"><span data-stu-id="6124e-161">Specify an `IncrementAmount` parameter in the `Index` component's `<Counter>` element using an attribute.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="6124e-162">Legen Sie den Wert fest, um den Zähler um zehn heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="6124e-162">Set the value to increment the counter by ten.</span></span>

<span data-ttu-id="6124e-163">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="6124e-163">*Pages/Index.razor*:</span></span> <span data-ttu-id="6124e-164">Laden Sie die `Index`-Komponente neu.</span><span class="sxs-lookup"><span data-stu-id="6124e-164">Reload the `Index` component.</span></span> <span data-ttu-id="6124e-165">Der Zähler wird jedes Mal, wenn die Schaltfläche **Hier klicken** ausgewählt wird, um zehn heraufgesetzt.</span><span class="sxs-lookup"><span data-stu-id="6124e-165">The counter increments by ten each time the **Click me** button is selected.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="6124e-166">Der Zähler in der `Counter`-Komponente wird weiter um eins erhöht.</span><span class="sxs-lookup"><span data-stu-id="6124e-166">The counter in the `Counter` component continues to increment by one.</span></span>

### <a name="blazor-server-experience"></a><span data-ttu-id="6124e-167">Weiterleiten an Komponenten</span><span class="sxs-lookup"><span data-stu-id="6124e-167">Route to components</span></span>

<span data-ttu-id="6124e-168">Die `@page`-Anweisung im oberen Teil der *Counter.razor*-Datei gibt an, dass die `Counter`-Komponente ein Routingendpunkt ist.</span><span class="sxs-lookup"><span data-stu-id="6124e-168">The `@page` directive at the top of the *Counter.razor* file specifies that the `Counter` component is a routing endpoint.</span></span> <span data-ttu-id="6124e-169">Die `Counter`-Komponente verarbeitet Anforderungen, die an `/counter` gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="6124e-169">The `Counter` component handles requests sent to `/counter`.</span></span>

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="6124e-170">Ohne die `@page`-Anweisung behandelt eine Komponente keine weitergeleiteten Anforderungen, aber die Komponente kann immer noch von anderen Komponenten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6124e-170">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

<span data-ttu-id="6124e-171">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="6124e-171">Dependency injection</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Blazor<span data-ttu-id="6124e-172"> Server-Benutzererfahrung</span><span class="sxs-lookup"><span data-stu-id="6124e-172"> Server experience</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a><span data-ttu-id="6124e-173">Wenn Sie mit einer Blazor Server-App arbeiten, wird der `WeatherForecastService`-Dienst in `Startup.ConfigureServices` als [Singleton](xref:fundamentals/dependency-injection#service-lifetimes) registriert.</span><span class="sxs-lookup"><span data-stu-id="6124e-173">If working with a Blazor Server app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes) in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="6124e-174">Eine Instanz des Diensts steht überall in der App mittels [Abhängigkeitsinjektion (Dependency Injection, DI)](xref:fundamentals/dependency-injection) zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="6124e-174">An instance of the service is available throughout the app via [dependency injection (DI)](xref:fundamentals/dependency-injection):</span></span>

<span data-ttu-id="6124e-175">Mit der [`@inject`](xref:mvc/views/razor#inject)-Anweisung wird die Instanz des `WeatherForecastService`-Diensts in die `FetchData`-Komponente eingefügt.</span><span class="sxs-lookup"><span data-stu-id="6124e-175">The [`@inject`](xref:mvc/views/razor#inject) directive is used to inject the instance of the `WeatherForecastService` service into the `FetchData` component.</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

<span data-ttu-id="6124e-176">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="6124e-176">*Pages/FetchData.razor*:</span></span>

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="6124e-177">Die `FetchData`-Komponente verwendet den eingefügten Dienst wie `ForecastService`, um ein Array aus `WeatherForecast`-Objekten abzurufen:</span><span class="sxs-lookup"><span data-stu-id="6124e-177">The `FetchData` component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

Blazor<span data-ttu-id="6124e-178"> WebAssembly-Benutzererfahrung</span><span class="sxs-lookup"><span data-stu-id="6124e-178"> WebAssembly experience</span></span>

1. <span data-ttu-id="6124e-179">Wenn Sie eine Blazor WebAssembly-App verwenden, wird <xref:System.Net.Http.HttpClient> eingefügt, um Wettervorhersagedaten aus der Datei *weather.json* im Ordner *wwwroot/sample-data* abzurufen.</span><span class="sxs-lookup"><span data-stu-id="6124e-179">If working with a Blazor WebAssembly app, <xref:System.Net.Http.HttpClient> is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder.</span></span> <span data-ttu-id="6124e-180">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="6124e-180">*Pages/FetchData.razor*:</span></span> <span data-ttu-id="6124e-181">Eine [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife rendert jede Vorhersageinstanz als eine Zeile in der Wetterdatentabelle:</span><span class="sxs-lookup"><span data-stu-id="6124e-181">An [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span> <span data-ttu-id="6124e-182">Erstellen einer Aufgabenliste</span><span class="sxs-lookup"><span data-stu-id="6124e-182">Build a todo list</span></span>

1. <span data-ttu-id="6124e-183">Fügen Sie der App eine neue Komponente hinzu, die eine einfache Aufgabenliste implementiert.</span><span class="sxs-lookup"><span data-stu-id="6124e-183">Add a new component to the app that implements a simple todo list.</span></span>

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. <span data-ttu-id="6124e-184">Fügen Sie der App im Ordner *Seiten* eine neue `Todo` Razor-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="6124e-184">Add a new `Todo` Razor component to the app in the *Pages* folder.</span></span>

   <span data-ttu-id="6124e-185">Wenn Sie Visual Studio verwenden, klicken Sie mit der rechten Maustaste auf den Ordner **Seiten** und dann auf **Hinzufügen** > **Neues Element** >  **Razor-Komponente**.</span><span class="sxs-lookup"><span data-stu-id="6124e-185">If you're using Visual Studio, right-click the **Pages** folder and select **Add** > **New Item** > **Razor Component**.</span></span> <span data-ttu-id="6124e-186">Nennen Sie die Datei der Komponente *Todo.razor*.</span><span class="sxs-lookup"><span data-stu-id="6124e-186">Name the component's file *Todo.razor*.</span></span>

   <span data-ttu-id="6124e-187">Fügen Sie dem Ordner **Seiten** in anderen Entwicklungsumgebungen eine leere Datei namens *Todo.razor* hinzu.</span><span class="sxs-lookup"><span data-stu-id="6124e-187">In other development environments, add a blank file to the **Pages** folder named *Todo.razor*.</span></span>

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="6124e-188">Geben Sie das ursprüngliche Markup für die Komponente an:</span><span class="sxs-lookup"><span data-stu-id="6124e-188">Provide the initial markup for the component:</span></span> <span data-ttu-id="6124e-189">Fügen Sie der Navigationsleiste die `Todo`-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="6124e-189">Add the `Todo` component to the navigation bar.</span></span>

1. <span data-ttu-id="6124e-190">Die `NavMenu`-Komponente (*Shared/NavMenu.razor*) wird im Layout der App verwendet.</span><span class="sxs-lookup"><span data-stu-id="6124e-190">The `NavMenu` component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="6124e-191">Layouts sind Komponenten, mit denen Sie die Duplizierung des Inhalts in der App vermeiden können.</span><span class="sxs-lookup"><span data-stu-id="6124e-191">Layouts are components that allow you to avoid duplication of content in the app.</span></span>

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="6124e-192">Fügen Sie einen `Todo` für die `<NavLink>`-Komponente hinzu, indem Sie das folgende Listenelementmarkup unterhalb der vorhandenen Listenelemente in der *Datei Shared/NavMenu.razor* hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="6124e-192">Add a `<NavLink>` element for the `Todo` component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   * <span data-ttu-id="6124e-193">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="6124e-193">Rebuild and run the app.</span></span> <span data-ttu-id="6124e-194">Besuchen Sie die neue Todo-Seite, um sicherzustellen, dass der Link zur `Todo`-Komponente funktioniert.</span><span class="sxs-lookup"><span data-stu-id="6124e-194">Visit the new Todo page to confirm that the link to the `Todo` component works.</span></span>
   * <span data-ttu-id="6124e-195">Fügen Sie eine *TodoItem.cs*-Datei dem Stamm des Projekts hinzu, die eine Klasse zum Darstellen eines Aufgabenelements enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="6124e-195">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="6124e-196">Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="6124e-196">Use the following C# code for the `TodoItem` class:</span></span> <span data-ttu-id="6124e-197">Kehren Sie zur `Todo`-Komponente (*Pages/Todo.razor*) zurück:</span><span class="sxs-lookup"><span data-stu-id="6124e-197">Return to the `Todo` component (*Pages/Todo.razor*):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="6124e-198">Fügen Sie ein Feld für die Aufgabenelemente in einem `@code`-Block hinzu.</span><span class="sxs-lookup"><span data-stu-id="6124e-198">Add a field for the todo items in an `@code` block.</span></span> <span data-ttu-id="6124e-199">Die `Todo`-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.</span><span class="sxs-lookup"><span data-stu-id="6124e-199">The `Todo` component uses this field to maintain the state of the todo list.</span></span>

1. <span data-ttu-id="6124e-200">Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern (`<li>`).</span><span class="sxs-lookup"><span data-stu-id="6124e-200">Add unordered list markup and a `foreach` loop to render each todo item as a list item (`<li>`).</span></span> <span data-ttu-id="6124e-201">Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgabenelemente hinzugefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="6124e-201">The app requires UI elements for adding todo items to the list.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. <span data-ttu-id="6124e-202">Fügen Sie eine Texteingabe (`<input>`) und eine Schaltfläche (`<button>`) unterhalb der Liste (`<ul>...</ul>`) hinzu:</span><span class="sxs-lookup"><span data-stu-id="6124e-202">Add a text input (`<input>`) and a button (`<button>`) below the unordered list (`<ul>...</ul>`):</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. <span data-ttu-id="6124e-203">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="6124e-203">Rebuild and run the app.</span></span> <span data-ttu-id="6124e-204">Bei Auswahl der Schaltfläche **Todo-Element hinzufügen** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="6124e-204">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="6124e-205">Fügen Sie eine `AddTodo`-Methode zur `Todo`-Komponente hinzu, und registrieren Sie sie mit dem `@onclick`-Attribut für eine Schaltflächenauswahl.</span><span class="sxs-lookup"><span data-stu-id="6124e-205">Add an `AddTodo` method to the `Todo` component and register it for button selections using the `@onclick` attribute.</span></span> <span data-ttu-id="6124e-206">Die C#-Methode `AddTodo` wird aufgerufen, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="6124e-206">The `AddTodo` C# method is called when the button is selected:</span></span>

1. <span data-ttu-id="6124e-207">Um den Titel des neuen Aufgabenelements abzurufen, fügen Sie oben im `@code`-Block ein `newTodo`-Zeichenfolgenfeld hinzu, und binden Sie es mithilfe des `bind`-Attributs im `<input>`-Element an den Wert der Texteingabe:</span><span class="sxs-lookup"><span data-stu-id="6124e-207">To get the title of the new todo item, add a `newTodo` string field at the top of the `@code` block and bind it to the value of the text input using the `bind` attribute in the `<input>` element:</span></span> <span data-ttu-id="6124e-208">Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="6124e-208">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="6124e-209">Löschen Sie den Wert der Texteingabe, indem Sie für `newTodo` eine leere Zeichenfolge festlegen:</span><span class="sxs-lookup"><span data-stu-id="6124e-209">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="6124e-210">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="6124e-210">Rebuild and run the app.</span></span>

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. <span data-ttu-id="6124e-211">Fügen Sie der Aufgabenliste einige Aufgabenelemente hinzu, um den neuen Code zu testen.</span><span class="sxs-lookup"><span data-stu-id="6124e-211">Add some todo items to the todo list to test the new code.</span></span>

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="6124e-212">Der Titeltext für die einzelnen Aufgabenelemente kann als bearbeitbar festgelegt werden, und ein Kontrollkästchen kann dem Benutzer helfen, die erledigten Elemente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="6124e-212">The title text for each todo item can be made editable, and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="6124e-213">Fügen Sie für jedes Aufgabenelement eine Kontrollkästcheneingabe hinzu, und binden Sie ihren Wert an die `IsDone`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="6124e-213">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6124e-214">Ändern Sie `@todo.Title` in ein `<input>`-Element, das an `@todo.Title` gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="6124e-214">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

<span data-ttu-id="6124e-215">Um sicherzustellen, dass diese Werte gebunden sind, aktualisieren Sie den `<h3>`-Header, um die Anzahl der noch nicht erledigten Aufgabenelemente anzuzeigen (`IsDone` ist `false`).</span><span class="sxs-lookup"><span data-stu-id="6124e-215">To verify that these values are bound, update the `<h3>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="6124e-216">Die abgeschlossene `Todo`-Komponente (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="6124e-216">The completed `Todo` component (*Pages/Todo.razor*):</span></span>
> * <span data-ttu-id="6124e-217">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="6124e-217">Rebuild and run the app.</span></span>
> * <span data-ttu-id="6124e-218">Fügen Sie Aufgabenelemente hinzu, um den neuen Code zu testen.</span><span class="sxs-lookup"><span data-stu-id="6124e-218">Add todo items to test the new code.</span></span>
> * <span data-ttu-id="6124e-219">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="6124e-219">Next steps</span></span>

<span data-ttu-id="6124e-220">In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="6124e-220">In this tutorial, you learned how to:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/components>
