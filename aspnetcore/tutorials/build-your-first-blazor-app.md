---
title: Erstellen Ihrer ersten Blazor-App
author: guardrex
description: Erstellen Sie Schritt-für-Schritt eine Blazor-App.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/14/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: c1b142ebdbd85eb10ddf8c8b70edd9782732a4f1
ms.sourcegitcommit: 3ee6ee0051c3d2c8d47a58cb17eef1a84a4c46a0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65621106"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="d64ee-103">Erstellen Ihrer ersten Blazor-App</span><span class="sxs-lookup"><span data-stu-id="d64ee-103">Build your first Blazor app</span></span>

<span data-ttu-id="d64ee-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d64ee-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d64ee-105">In diesem Tutorial erfahren Sie, wie Sie eine Blazor-App erstellen und ändern.</span><span class="sxs-lookup"><span data-stu-id="d64ee-105">This tutorial shows you how to build and modify a Blazor app.</span></span>

<span data-ttu-id="d64ee-106">Befolgen Sie die Anweisungen im Artikel <xref:blazor/get-started>, um ein Blazor-Projekt für dieses Tutorial zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d64ee-106">Follow the guidance in the <xref:blazor/get-started> article to create a Blazor project for this tutorial.</span></span>

## <a name="build-components"></a><span data-ttu-id="d64ee-107">Erstellen von Komponenten</span><span class="sxs-lookup"><span data-stu-id="d64ee-107">Build components</span></span>

1. <span data-ttu-id="d64ee-108">Navigieren Sie zu jeder der drei Seiten der App im Ordner *Pages*: „Home“, „Counter“ und „Fetch data“.</span><span class="sxs-lookup"><span data-stu-id="d64ee-108">Browse to each of the app's three pages in the *Pages* folder: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="d64ee-109">Diese Seiten werden durch die Razor-Komponentendateien *Index.razor*, *Counter.razor* und *FetchData.razor* implementiert.</span><span class="sxs-lookup"><span data-stu-id="d64ee-109">These pages are implemented by the Razor component files *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span>

1. <span data-ttu-id="d64ee-110">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="d64ee-110">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="d64ee-111">Das Heraufsetzen eines Zählers auf einer Webseite erfordert normalerweise das Schreiben in JavaScript, aber Blazor bietet einen besseren Ansatz mit C#.</span><span class="sxs-lookup"><span data-stu-id="d64ee-111">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="d64ee-112">Sehen Sie sich die Implementierung der Counter-Komponente in der *Counter.razor*-Datei an.</span><span class="sxs-lookup"><span data-stu-id="d64ee-112">Examine the implementation of the Counter component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="d64ee-113">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="d64ee-113">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="d64ee-114">Die Benutzeroberfläche der Counter-Komponente wird mithilfe von HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="d64ee-114">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="d64ee-115">Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d64ee-115">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="d64ee-116">Das HTML-Markup und die C#-Renderinglogik werden zur Erstellungszeit in eine Komponentenklasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="d64ee-116">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="d64ee-117">Der Name der generierten .NET-Klasse stimmt mit dem Namen der Datei überein.</span><span class="sxs-lookup"><span data-stu-id="d64ee-117">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="d64ee-118">Member der Komponentenklasse werden in einem `@functions`-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="d64ee-118">Members of the component class are defined in an `@functions` block.</span></span> <span data-ttu-id="d64ee-119">Im `@functions`-Block werden der Komponentenstatus (Eigenschaften, Felder) und Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben.</span><span class="sxs-lookup"><span data-stu-id="d64ee-119">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="d64ee-120">Diese Member werden dann als Teil der Renderinglogik der Komponente und zur Behandlung von Ereignissen verwendet.</span><span class="sxs-lookup"><span data-stu-id="d64ee-120">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="d64ee-121">Wenn die **Hier klicken**-Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="d64ee-121">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="d64ee-122">Der registrierte `onclick`-Handler der Counter-Komponente wird aufgerufen (die `IncrementCount`-Methode).</span><span class="sxs-lookup"><span data-stu-id="d64ee-122">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="d64ee-123">Die Counter-Komponente generiert ihre Renderstruktur neu.</span><span class="sxs-lookup"><span data-stu-id="d64ee-123">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="d64ee-124">Die neue Renderstruktur wird mit der vorherigen verglichen.</span><span class="sxs-lookup"><span data-stu-id="d64ee-124">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="d64ee-125">Es werden nur Änderungen des Dokumentobjektmodells (Document Object Model, DOM) angewendet.</span><span class="sxs-lookup"><span data-stu-id="d64ee-125">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="d64ee-126">Der angezeigte Zähler wird aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="d64ee-126">The displayed count is updated.</span></span>

1. <span data-ttu-id="d64ee-127">Ändern Sie die C#-Logik der Counter-Komponente, um das Zählerinkrement von eins auf zwei heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="d64ee-127">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="d64ee-128">Erstellen Sie die App neu, und führen Sie sie aus, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="d64ee-128">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="d64ee-129">Wählen Sie die Schaltfläche **Hier klicken** aus.</span><span class="sxs-lookup"><span data-stu-id="d64ee-129">Select the **Click me** button.</span></span> <span data-ttu-id="d64ee-130">Der Leistungsindikator wird um zwei erhöht.</span><span class="sxs-lookup"><span data-stu-id="d64ee-130">The counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="d64ee-131">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="d64ee-131">Use components</span></span>

<span data-ttu-id="d64ee-132">Beziehen Sie eine Komponente mithilfe einer HTML-Syntax in eine andere Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="d64ee-132">Include a component in another component using an HTML syntax.</span></span>

1. <span data-ttu-id="d64ee-133">Fügen Sie die Counter-Komponente der Index-Komponente der App durch Hinzufügen eines `<Counter />`-Elements zur Index-Komponente (*Index.razor*) hinzu.</span><span class="sxs-lookup"><span data-stu-id="d64ee-133">Add the Counter component to the app's Index component by adding a `<Counter />` element to the Index component (*Index.razor*).</span></span>

   <span data-ttu-id="d64ee-134">Wenn Sie für diese Oberfläche die Blazor-Clientseite verwenden, enthält die Index-Komponente eine Survey Prompt-Komponente (`<SurveyPrompt>`-Element).</span><span class="sxs-lookup"><span data-stu-id="d64ee-134">If you're using Blazor client-side for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="d64ee-135">Ersetzen Sie das `<SurveyPrompt>`-Element durch das `<Counter>`-Element.</span><span class="sxs-lookup"><span data-stu-id="d64ee-135">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span> <span data-ttu-id="d64ee-136">Wenn Sie für diese Oberfläche eine serverseitige Blazor-App verwenden, fügen Sie das `<Counter>`-Element zur Index-Komponente hinzu:</span><span class="sxs-lookup"><span data-stu-id="d64ee-136">If you're using a Blazor server-side app for this experience, add the `<Counter>` element to the Index component:</span></span>

   <span data-ttu-id="d64ee-137">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="d64ee-137">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. <span data-ttu-id="d64ee-138">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="d64ee-138">Rebuild and run the app.</span></span> <span data-ttu-id="d64ee-139">Die Index-Komponente verfügt über einen eigenen Leistungsindikator.</span><span class="sxs-lookup"><span data-stu-id="d64ee-139">The Index component has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="d64ee-140">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="d64ee-140">Component parameters</span></span>

<span data-ttu-id="d64ee-141">Komponenten können auch Parameter aufweisen.</span><span class="sxs-lookup"><span data-stu-id="d64ee-141">Components can also have parameters.</span></span> <span data-ttu-id="d64ee-142">Komponentenparameter werden mithilfe nicht öffentlicher Eigenschaften für die Komponentenklasse definiert, die mit `[Parameter]` versehen ist.</span><span class="sxs-lookup"><span data-stu-id="d64ee-142">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="d64ee-143">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="d64ee-143">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="d64ee-144">Aktualisieren Sie den `@functions`-C#-Code der Komponente:</span><span class="sxs-lookup"><span data-stu-id="d64ee-144">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="d64ee-145">Fügen Sie eine `IncrementAmount`-Eigenschaft hinzu, die mit dem `[Parameter]`-Attribut ausgestattet ist.</span><span class="sxs-lookup"><span data-stu-id="d64ee-145">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="d64ee-146">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d64ee-146">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="d64ee-147">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="d64ee-147">*Pages/Counter.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="d64ee-148">Geben Sie unter Verwendung eines Attributs einen `IncrementAmount`-Parameter in das `<Counter>`-Element der Index-Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="d64ee-148">Specify an `IncrementAmount` parameter in the Index component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="d64ee-149">Legen Sie den Wert fest, um den Zähler um zehn heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="d64ee-149">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="d64ee-150">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="d64ee-150">*Pages/Index.razor*:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. <span data-ttu-id="d64ee-151">Laden Sie die Index-Komponente neu.</span><span class="sxs-lookup"><span data-stu-id="d64ee-151">Reload the Index component.</span></span> <span data-ttu-id="d64ee-152">Der Zähler wird jedes Mal, wenn die Schaltfläche **Hier klicken** ausgewählt wird, um zehn heraufgesetzt.</span><span class="sxs-lookup"><span data-stu-id="d64ee-152">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="d64ee-153">Der Zähler in der Counter-Komponente wird weiter um eins erhöht.</span><span class="sxs-lookup"><span data-stu-id="d64ee-153">The counter in the Counter component continues to increment by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="d64ee-154">Weiterleiten an Komponenten</span><span class="sxs-lookup"><span data-stu-id="d64ee-154">Route to components</span></span>

<span data-ttu-id="d64ee-155">Die `@page`-Anweisung im oberen Teil der *Counter.razor*-Datei gibt an, dass die Counter-Komponente ein Routingendpunkt ist.</span><span class="sxs-lookup"><span data-stu-id="d64ee-155">The `@page` directive at the top of the *Counter.razor* file specifies that the Counter component is a routing endpoint.</span></span> <span data-ttu-id="d64ee-156">Die Counter-Komponente behandelt Anforderungen, die an `/counter` gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="d64ee-156">The Counter component handles requests sent to `/counter`.</span></span> <span data-ttu-id="d64ee-157">Ohne die `@page`-Anweisung behandelt eine Komponente keine weitergeleiteten Anforderungen, aber die Komponente kann immer noch von anderen Komponenten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d64ee-157">Without the `@page` directive, a component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="d64ee-158">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="d64ee-158">Dependency injection</span></span>

<span data-ttu-id="d64ee-159">Im Dienstcontainer der App registrierte Dienste stehen für Komponenten über [Abhängigkeitsinjektion (DI, Dependency Injection)](xref:fundamentals/dependency-injection) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="d64ee-159">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d64ee-160">Fügen Sie Dienste mit der `@inject`-Anweisung in eine Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="d64ee-160">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="d64ee-161">Sehen Sie sich die Anweisungen der FetchData-Komponente an.</span><span class="sxs-lookup"><span data-stu-id="d64ee-161">Examine the directives of the FetchData component.</span></span>

<span data-ttu-id="d64ee-162">Wenn Sie eine serverseitige Blazor-App verwenden, ist der `WeatherForecastService`-Dienst als [Singleton](xref:fundamentals/dependency-injection#service-lifetimes) registriert, sodass eine Instanz des Diensts in der gesamten App verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="d64ee-162">If working with a Blazor server-side app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="d64ee-163">Mit der `@inject`-Anweisung wird die Instanz des `WeatherForecastService`-Diensts in die Komponente eingefügt.</span><span class="sxs-lookup"><span data-stu-id="d64ee-163">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="d64ee-164">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="d64ee-164">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="d64ee-165">Die FetchData-Komponente verwendet den eingefügten Dienst als `ForecastService`, um ein Array von `WeatherForecast`-Objekten abzurufen:</span><span class="sxs-lookup"><span data-stu-id="d64ee-165">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="d64ee-166">Wenn Sie eine clientseitige Blazor-App verwenden, wird `HttpClient` eingefügt, um Wettervorhersagedaten aus der Datei *weather.json* in den Ordner *wwwroot/sample-data* zu abzurufen:</span><span class="sxs-lookup"><span data-stu-id="d64ee-166">If working with a Blazor client-side app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="d64ee-167">*Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="d64ee-167">*Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-8)]

<span data-ttu-id="d64ee-168">Eine [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife rendert jede Vorhersageinstanz als eine Zeile in der Wetterdatentabelle:</span><span class="sxs-lookup"><span data-stu-id="d64ee-168">A [\@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]


## <a name="build-a-todo-list"></a><span data-ttu-id="d64ee-169">Erstellen einer Aufgabenliste</span><span class="sxs-lookup"><span data-stu-id="d64ee-169">Build a todo list</span></span>

<span data-ttu-id="d64ee-170">Fügen Sie der App eine neue Komponente hinzu, die eine einfache Aufgabenliste implementiert.</span><span class="sxs-lookup"><span data-stu-id="d64ee-170">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="d64ee-171">Fügen Sie der App im Ordner *Pages* eine leere Datei mit dem Namen *Todo.razor* hinzu:</span><span class="sxs-lookup"><span data-stu-id="d64ee-171">Add an empty file named *Todo.razor* to the app in the *Pages* folder:</span></span>

1. <span data-ttu-id="d64ee-172">Geben Sie das ursprüngliche Markup für die Komponente an:</span><span class="sxs-lookup"><span data-stu-id="d64ee-172">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="d64ee-173">Fügen Sie der Navigationsleiste die Todo-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="d64ee-173">Add the Todo component to the navigation bar.</span></span>

   <span data-ttu-id="d64ee-174">Die NavMenu-Komponente (*Shared/NavMenu.razor*) wird im Layout der App verwendet.</span><span class="sxs-lookup"><span data-stu-id="d64ee-174">The NavMenu component (*Shared/NavMenu.razor*) is used in the app's layout.</span></span> <span data-ttu-id="d64ee-175">Layouts sind Komponenten, mit denen Sie die Duplizierung des Inhalts in der App vermeiden können.</span><span class="sxs-lookup"><span data-stu-id="d64ee-175">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="d64ee-176">Weitere Informationen finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="d64ee-176">For more information, see <xref:blazor/layouts>.</span></span>

   <span data-ttu-id="d64ee-177">Fügen Sie einen `<NavLink>` für die Todo-Komponente hinzu, indem Sie das folgende Listenelementmarkup unterhalb der vorhandenen Listenelemente der Datei *Shared/NavMenu.razor* hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="d64ee-177">Add a `<NavLink>` for the Todo component by adding the following list item markup below the existing list items in the *Shared/NavMenu.razor* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="d64ee-178">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="d64ee-178">Rebuild and run the app.</span></span> <span data-ttu-id="d64ee-179">Besuchen Sie die neue Todo-Seite, um sicherzustellen, dass der Link zur Todo-Komponente funktioniert.</span><span class="sxs-lookup"><span data-stu-id="d64ee-179">Visit the new Todo page to confirm that the link to the Todo component works.</span></span>

1. <span data-ttu-id="d64ee-180">Wenn Sie eine serverseitige Blazor-App erstellen, fügen Sie den Namespace der App zur Datei *\_Imports.razor* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d64ee-180">If building a Blazor server-side app, add the app's namespace to the *\_Imports.razor* file.</span></span> <span data-ttu-id="d64ee-181">Bei der folgenden `@using` Anweisung wird davon ausgegangen, dass der Namespace der App `WebApplication` ist:</span><span class="sxs-lookup"><span data-stu-id="d64ee-181">The following `@using` statement assumes that the app's namespace is `WebApplication`:</span></span>

   ```cshtml
   @using WebApplication
   ```
   
   <span data-ttu-id="d64ee-182">Clientseitige Blazor-Apps schließen den Namespace der App standardmäßig in die Datei *\_Imports.razor* ein.</span><span class="sxs-lookup"><span data-stu-id="d64ee-182">Blazor client-side apps include the app's namespace by default in the *\_Imports.razor* file.</span></span>

1. <span data-ttu-id="d64ee-183">Fügen Sie eine *TodoItem.cs*-Datei dem Stamm des Projekts hinzu, die eine Klasse zum Darstellen eines Aufgabenelements enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="d64ee-183">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="d64ee-184">Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="d64ee-184">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. <span data-ttu-id="d64ee-185">Kehren Sie zur Todo-Komponente (*Pages/Todo.razor*) zurück:</span><span class="sxs-lookup"><span data-stu-id="d64ee-185">Return to the Todo component (*Pages/Todo.razor*):</span></span>

   * <span data-ttu-id="d64ee-186">Fügen Sie ein Feld für die Aufgabenelemente in einem `@functions`-Block hinzu.</span><span class="sxs-lookup"><span data-stu-id="d64ee-186">Add a field for the todo items in an `@functions` block.</span></span> <span data-ttu-id="d64ee-187">Die Todo-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.</span><span class="sxs-lookup"><span data-stu-id="d64ee-187">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="d64ee-188">Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern.</span><span class="sxs-lookup"><span data-stu-id="d64ee-188">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="d64ee-189">Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgabenelemente hinzugefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="d64ee-189">The app requires UI elements for adding todo items to the list.</span></span> <span data-ttu-id="d64ee-190">Fügen Sie eine Texteingabe und eine Schaltfläche unterhalb der Liste hinzu:</span><span class="sxs-lookup"><span data-stu-id="d64ee-190">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="d64ee-191">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="d64ee-191">Rebuild and run the app.</span></span> <span data-ttu-id="d64ee-192">Bei Auswahl der Schaltfläche **Todo-Element hinzufügen** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="d64ee-192">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="d64ee-193">Fügen Sie eine `AddTodo`-Methode der Todo-Komponente hinzu, und registrieren Sie sie mit dem `onclick`-Attribut für Schaltflächenklicks:</span><span class="sxs-lookup"><span data-stu-id="d64ee-193">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   <span data-ttu-id="d64ee-194">Die `AddTodo`-C#-Methode wird aufgerufen, wenn die Schaltfläche ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="d64ee-194">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="d64ee-195">Um den Titel des neuen Aufgabenelements abzurufen, fügen Sie ein `newTodo`-Zeichenfolgenfeld hinzu, und binden Sie es mithilfe des `bind`-Attributs an den Wert der Texteingabe:</span><span class="sxs-lookup"><span data-stu-id="d64ee-195">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo">
   ```

1. <span data-ttu-id="d64ee-196">Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="d64ee-196">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="d64ee-197">Löschen Sie den Wert der Texteingabe, indem Sie für `newTodo` eine leere Zeichenfolge festlegen:</span><span class="sxs-lookup"><span data-stu-id="d64ee-197">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="d64ee-198">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="d64ee-198">Rebuild and run the app.</span></span> <span data-ttu-id="d64ee-199">Fügen Sie der Aufgabenliste einige Aufgabenelemente hinzu, um den neuen Code zu testen.</span><span class="sxs-lookup"><span data-stu-id="d64ee-199">Add some todo items to the todo list to test the new code.</span></span>

1. <span data-ttu-id="d64ee-200">Der Titeltext für die einzelnen Aufgabenelemente kann bearbeitet werden, und ein Kontrollkästchen kann dem Benutzer helfen, die erledigten Elemente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="d64ee-200">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="d64ee-201">Fügen Sie für jedes Aufgabenelement eine Kontrollkästcheneingabe hinzu, und binden Sie ihren Wert an die `IsDone`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="d64ee-201">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="d64ee-202">Ändern Sie `@todo.Title` in ein `<input>`-Element, das an `@todo.Title` gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="d64ee-202">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="d64ee-203">Um sicherzustellen, dass diese Werte gebunden sind, aktualisieren Sie den `<h1>`-Header, um die Anzahl der noch nicht erledigten Aufgabenelemente anzuzeigen (`IsDone` ist `false`).</span><span class="sxs-lookup"><span data-stu-id="d64ee-203">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="d64ee-204">Die erledigte Todo-Komponente (*Pages/Todo.razor*):</span><span class="sxs-lookup"><span data-stu-id="d64ee-204">The completed Todo component (*Pages/Todo.razor*):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. <span data-ttu-id="d64ee-205">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="d64ee-205">Rebuild and run the app.</span></span> <span data-ttu-id="d64ee-206">Fügen Sie Aufgabenelemente hinzu, um den neuen Code zu testen.</span><span class="sxs-lookup"><span data-stu-id="d64ee-206">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="d64ee-207">Veröffentlichen und Bereitstellen der App</span><span class="sxs-lookup"><span data-stu-id="d64ee-207">Publish and deploy the app</span></span>

<span data-ttu-id="d64ee-208">Wie Sie die App veröffentlichen, erfahren Sie unter <xref:host-and-deploy/blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="d64ee-208">To publish the app, see <xref:host-and-deploy/blazor/index>.</span></span>
