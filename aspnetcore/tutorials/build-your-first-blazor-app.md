---
title: Erstellen Ihrer ersten Blazor-App
author: guardrex
description: Erstellen Sie Schritt-für-Schritt eine Blazor-App.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: 310eb211f68076d6f52d6427940e07736d833e5b
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614709"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="2c242-103">Erstellen Ihrer ersten Blazor-App</span><span class="sxs-lookup"><span data-stu-id="2c242-103">Build your first Blazor app</span></span>

<span data-ttu-id="2c242-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2c242-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="2c242-105">In diesem Tutorial erfahren Sie, wie Sie eine App mit serverseitigem Razor Components oder clientseitigem Blazor erstellen können.</span><span class="sxs-lookup"><span data-stu-id="2c242-105">This tutorial shows you how to build an app with server-side Razor Components or client-side Blazor.</span></span>

<span data-ttu-id="2c242-106">Für eine Benutzeroberfläche mit serverseitigem ASP.NET Core-Razor Components (*empfohlen*):</span><span class="sxs-lookup"><span data-stu-id="2c242-106">For an experience using server-side ASP.NET Core Razor Components (*recommended*):</span></span>

* <span data-ttu-id="2c242-107">Führen Sie die Schritte in der Anleitung *Razor Components experience* (Razor Components-Benutzeroberfläche) in <xref:blazor/get-started#server-side-razor-components-experience> aus, um ein Razor Components-basiertes Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2c242-107">Follow the *Razor Components experience* guidance in <xref:blazor/get-started#server-side-razor-components-experience> to create a Razor Components-based project.</span></span>
* <span data-ttu-id="2c242-108">Benennen Sie das Projekt mit `RazorComponents`.</span><span class="sxs-lookup"><span data-stu-id="2c242-108">Name the project `RazorComponents`.</span></span>

<span data-ttu-id="2c242-109">Für eine Benutzeroberfläche mit Blazor:</span><span class="sxs-lookup"><span data-stu-id="2c242-109">For an experience using Blazor:</span></span>

* <span data-ttu-id="2c242-110">Führen Sie die Schritte in der Anleitung *Blazor experience* (Blazor-Benutzeroberfläche) in <xref:blazor/get-started#client-side-blazor-experience> aus, um ein Blazor-basiertes Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="2c242-110">Follow the *Blazor experience* guidance in <xref:blazor/get-started#client-side-blazor-experience> to create a Blazor-based project.</span></span>
* <span data-ttu-id="2c242-111">Benennen Sie das Projekt mit `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="2c242-111">Name the project `Blazor`.</span></span>

<span data-ttu-id="2c242-112">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="2c242-112">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="2c242-113">Weitere Voraussetzungen finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="2c242-113">See the following topics for prerequisites:</span></span>

## <a name="build-components"></a><span data-ttu-id="2c242-114">Erstellen von Komponenten</span><span class="sxs-lookup"><span data-stu-id="2c242-114">Build components</span></span>

1. <span data-ttu-id="2c242-115">Navigieren Sie zu jeder der drei App-Seiten im Ordner *Components/Pages* (in Blazor *Pages*): „Home“, „Counter“ und „Fetch data“.</span><span class="sxs-lookup"><span data-stu-id="2c242-115">Browse to each of the app's three pages in the *Components/Pages* folder (*Pages* in Blazor): Home, Counter, and Fetch data.</span></span> <span data-ttu-id="2c242-116">Diese Seiten werden von den folgenden Razor-Komponentendateien implementiert: *Index.razor*, *Counter.razor* und *FetchData.razor*.</span><span class="sxs-lookup"><span data-stu-id="2c242-116">These pages are implemented by Razor Component files: *Index.razor*, *Counter.razor*, and *FetchData.razor*.</span></span> <span data-ttu-id="2c242-117">(Blazor verwendet weiterhin Dateien mit der Erweiterung *CSHTML*: *Index.cshtml*, *Counter.cshtml* und *FetchData.cshtml*.)</span><span class="sxs-lookup"><span data-stu-id="2c242-117">(Blazor continues to use the *.cshtml* file extension: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.)</span></span>

1. <span data-ttu-id="2c242-118">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="2c242-118">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="2c242-119">Das Heraufsetzen eines Zählers auf einer Webseite erfordert normalerweise das Schreiben in JavaScript, aber Blazor bietet einen besseren Ansatz mit C#.</span><span class="sxs-lookup"><span data-stu-id="2c242-119">Incrementing a counter in a webpage normally requires writing JavaScript, but Blazor provides a better approach using C#.</span></span>

1. <span data-ttu-id="2c242-120">Sehen Sie sich die Implementierung der Counter-Komponente in der *Counter.razor*-Datei an.</span><span class="sxs-lookup"><span data-stu-id="2c242-120">Examine the implementation of the Counter component in the *Counter.razor* file.</span></span>

   <span data-ttu-id="2c242-121">*Components/Pages/Counter.razor* (in Blazor *Pages/Counter.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="2c242-121">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   <span data-ttu-id="2c242-122">Die Benutzeroberfläche der Counter-Komponente wird mithilfe von HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="2c242-122">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="2c242-123">Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2c242-123">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="2c242-124">Das HTML-Markup und die C#-Renderinglogik werden zur Erstellungszeit in eine Komponentenklasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="2c242-124">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="2c242-125">Der Name der generierten .NET-Klasse stimmt mit dem Namen der Datei überein.</span><span class="sxs-lookup"><span data-stu-id="2c242-125">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="2c242-126">Member der Komponentenklasse werden in einem `@functions`-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="2c242-126">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="2c242-127">Im `@functions`-Block werden der Komponentenstatus (Eigenschaften, Felder) und Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben.</span><span class="sxs-lookup"><span data-stu-id="2c242-127">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="2c242-128">Diese Member werden dann als Teil der Renderinglogik der Komponente und zur Behandlung von Ereignissen verwendet.</span><span class="sxs-lookup"><span data-stu-id="2c242-128">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="2c242-129">Wenn die **Hier klicken**-Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="2c242-129">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="2c242-130">Der registrierte `onclick`-Handler der Counter-Komponente wird aufgerufen (die `IncrementCount`-Methode).</span><span class="sxs-lookup"><span data-stu-id="2c242-130">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="2c242-131">Die Counter-Komponente generiert ihre Renderstruktur neu.</span><span class="sxs-lookup"><span data-stu-id="2c242-131">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="2c242-132">Die neue Renderstruktur wird mit der vorherigen verglichen.</span><span class="sxs-lookup"><span data-stu-id="2c242-132">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="2c242-133">Es werden nur Änderungen des Dokumentobjektmodells (Document Object Model, DOM) angewendet.</span><span class="sxs-lookup"><span data-stu-id="2c242-133">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="2c242-134">Der angezeigte Zähler wird aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="2c242-134">The displayed count is updated.</span></span>

1. <span data-ttu-id="2c242-135">Ändern Sie die C#-Logik der Counter-Komponente, um das Zählerinkrement von eins auf zwei heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="2c242-135">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. <span data-ttu-id="2c242-136">Erstellen Sie die App neu, und führen Sie sie aus, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="2c242-136">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="2c242-137">Wählen Sie die Schaltfläche **Hier klicken** aus, und der Zähler wird um zwei heraufgesetzt.</span><span class="sxs-lookup"><span data-stu-id="2c242-137">Select the **Click me** button, and the counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="2c242-138">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="2c242-138">Use components</span></span>

<span data-ttu-id="2c242-139">Beziehen Sie eine Komponente mithilfe einer HTML-ähnlichen Syntax in eine andere Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="2c242-139">Include a component into another component using an HTML-like syntax.</span></span>

1. <span data-ttu-id="2c242-140">Fügen Sie die Counter-Komponente der Index-Komponente (Start) der App durch Hinzufügen eines `<Counter />`-Elements zur Index-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="2c242-140">Add the Counter component to the app's Index (Home) component by adding a `<Counter />` element to the Index component.</span></span>

   <span data-ttu-id="2c242-141">Wenn Sie für diese Oberfläche Blazor verwenden, enthält die Index-Komponente eine Survey Prompt-Komponente (`<SurveyPrompt>`-Element).</span><span class="sxs-lookup"><span data-stu-id="2c242-141">If you're using Blazor for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="2c242-142">Ersetzen Sie das `<SurveyPrompt>`-Element durch das `<Counter>`-Element.</span><span class="sxs-lookup"><span data-stu-id="2c242-142">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span>

   <span data-ttu-id="2c242-143">*Components/Pages/Index.razor* (in Blazor *Pages/Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="2c242-143">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/Index.razor?highlight=7)]

1. <span data-ttu-id="2c242-144">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="2c242-144">Rebuild and run the app.</span></span> <span data-ttu-id="2c242-145">Die Startseite verfügt über einen eigenen Zähler.</span><span class="sxs-lookup"><span data-stu-id="2c242-145">The Home page has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="2c242-146">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="2c242-146">Component parameters</span></span>

<span data-ttu-id="2c242-147">Komponenten können auch Parameter aufweisen.</span><span class="sxs-lookup"><span data-stu-id="2c242-147">Components can also have parameters.</span></span> <span data-ttu-id="2c242-148">Komponentenparameter werden mithilfe nicht öffentlicher Eigenschaften für die Komponentenklasse definiert, die mit `[Parameter]` versehen ist.</span><span class="sxs-lookup"><span data-stu-id="2c242-148">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="2c242-149">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="2c242-149">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="2c242-150">Aktualisieren Sie den `@functions`-C#-Code der Komponente:</span><span class="sxs-lookup"><span data-stu-id="2c242-150">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="2c242-151">Fügen Sie eine `IncrementAmount`-Eigenschaft hinzu, die mit dem `[Parameter]`-Attribut ausgestattet ist.</span><span class="sxs-lookup"><span data-stu-id="2c242-151">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="2c242-152">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="2c242-152">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="2c242-153">*Components/Pages/Counter.razor* (in Blazor *Pages/Counter.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="2c242-153">*Components/Pages/Counter.razor* (*Pages/Counter.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Counter.razor?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="2c242-154">Geben Sie unter Verwendung eines Attributs einen `IncrementAmount`-Parameter in das `<Counter>`-Element der Home-Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="2c242-154">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="2c242-155">Legen Sie den Wert fest, um den Zähler um zehn heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="2c242-155">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="2c242-156">*Components/Pages/Index.razor* (in Blazor *Pages/Index.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="2c242-156">*Components/Pages/Index.razor* (*Pages/Index.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Index.razor?highlight=7)]

1. <span data-ttu-id="2c242-157">Laden Sie die Startseite erneut.</span><span class="sxs-lookup"><span data-stu-id="2c242-157">Reload the Home page.</span></span> <span data-ttu-id="2c242-158">Der Zähler wird jedes Mal, wenn die Schaltfläche **Hier klicken** ausgewählt wird, um zehn heraufgesetzt.</span><span class="sxs-lookup"><span data-stu-id="2c242-158">The counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="2c242-159">Der Zähler auf der „Counter“-Seite wird um eins heraufgesetzt.</span><span class="sxs-lookup"><span data-stu-id="2c242-159">The counter on the Counter page increments by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="2c242-160">Weiterleiten an Komponenten</span><span class="sxs-lookup"><span data-stu-id="2c242-160">Route to components</span></span>

<span data-ttu-id="2c242-161">Die `@page`-Anweisung im oberen Teil der *Counter.razor*-Datei gibt an, dass diese Komponente ein Routingendpunkt ist.</span><span class="sxs-lookup"><span data-stu-id="2c242-161">The `@page` directive at the top of the *Counter.razor* file specifies that this component is a routing endpoint.</span></span> <span data-ttu-id="2c242-162">Die Counter-Komponente behandelt Anforderungen, die an `/Counter` gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="2c242-162">The Counter component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="2c242-163">Ohne die `@page`-Anweisung behandelt die Komponente keine weitergeleiteten Anforderungen, aber die Komponente kann immer noch von anderen Komponenten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2c242-163">Without the `@page` directive, the component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="2c242-164">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="2c242-164">Dependency injection</span></span>

<span data-ttu-id="2c242-165">Im Dienstcontainer der App registrierte Dienste stehen für Komponenten über [Abhängigkeitsinjektion (DI, Dependency Injection)](xref:fundamentals/dependency-injection) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="2c242-165">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="2c242-166">Fügen Sie Dienste mit der `@inject`-Anweisung in eine Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="2c242-166">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="2c242-167">Sehen Sie sich die Anweisungen der FetchData-Komponente in der Beispiel-App an.</span><span class="sxs-lookup"><span data-stu-id="2c242-167">Examine the directives of the FetchData component in the sample app.</span></span>

<span data-ttu-id="2c242-168">In der Razor Components-Beispiel-App ist der `WeatherForecastService`-Dienst als [Singleton](xref:fundamentals/dependency-injection#service-lifetimes) registriert, sodass eine Instanz des Diensts in der gesamten App verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="2c242-168">In the Razor Components sample app, the `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span> <span data-ttu-id="2c242-169">Mit der `@inject`-Anweisung wird die Instanz des `WeatherForecastService`-Diensts in die Komponente eingefügt.</span><span class="sxs-lookup"><span data-stu-id="2c242-169">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component.</span></span>

<span data-ttu-id="2c242-170">*Components/Pages/FetchData.razor*:</span><span class="sxs-lookup"><span data-stu-id="2c242-170">*Components/Pages/FetchData.razor*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

<span data-ttu-id="2c242-171">Die FetchData-Komponente verwendet den eingefügten Dienst als `ForecastService`, um ein Array von `WeatherForecast`-Objekten abzurufen:</span><span class="sxs-lookup"><span data-stu-id="2c242-171">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

<span data-ttu-id="2c242-172">In der Blazor-Version der Beispiel-App wird `HttpClient` eingefügt, um Wettervorhersagedaten aus der Datei *weather.json* in den Ordner *wwwroot/sample-data* zu abzurufen:</span><span class="sxs-lookup"><span data-stu-id="2c242-172">In the Blazor version of the sample app, `HttpClient` is injected to obtain weather forecast data from the *weather.json* file in the *wwwroot/sample-data* folder:</span></span>

<span data-ttu-id="2c242-173">*Pages/FetchData.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="2c242-173">*Pages/FetchData.cshtml*:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=7)]

<span data-ttu-id="2c242-174">In beiden Beispiel-Apps wird eine [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife verwendet, um jede Vorhersageinstanz als eine Zeile in der Tabelle der Wetterdaten darzustellen:</span><span class="sxs-lookup"><span data-stu-id="2c242-174">In both sample apps, a [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="2c242-175">Erstellen einer Aufgabenliste</span><span class="sxs-lookup"><span data-stu-id="2c242-175">Build a todo list</span></span>

<span data-ttu-id="2c242-176">Fügen Sie der App eine neue Komponente hinzu, die eine einfache Aufgabenliste implementiert.</span><span class="sxs-lookup"><span data-stu-id="2c242-176">Add a new component to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="2c242-177">Fügen Sie eine leere Datei zur Beispiel-App hinzu:</span><span class="sxs-lookup"><span data-stu-id="2c242-177">Add an empty file to the sample app:</span></span>

   * <span data-ttu-id="2c242-178">Für die Razor-Komponentenoberfläche fügen Sie eine *Todo.razor*-Datei zum Ordner *Components/Pages* hinzu.</span><span class="sxs-lookup"><span data-stu-id="2c242-178">For the Razor Components experience, add a *Todo.razor* file to the *Components/Pages* folder.</span></span>
   * <span data-ttu-id="2c242-179">Für die Blazor-Oberfläche fügen Sie eine *Todo.cshtml*-Datei zum Ordner *Pages* hinzu.</span><span class="sxs-lookup"><span data-stu-id="2c242-179">For the Blazor experience, add a *Todo.cshtml* file to the *Pages* folder.</span></span>

1. <span data-ttu-id="2c242-180">Geben Sie das ursprüngliche Markup für die Komponente an:</span><span class="sxs-lookup"><span data-stu-id="2c242-180">Provide the initial markup for the component:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="2c242-181">Fügen Sie der Navigationsleiste die Todo-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="2c242-181">Add the Todo component to the navigation bar.</span></span>

   <span data-ttu-id="2c242-182">Die NavMenu-Komponente (*Components/Shared/NavMenu.razor* oder in Blazor *Shared/NavMenu.cshtml*) wird für das App-Layout verwendet.</span><span class="sxs-lookup"><span data-stu-id="2c242-182">The NavMenu component (*Components/Shared/NavMenu.razor* or *Shared/NavMenu.cshtml* in Blazor) is used in the app's layout.</span></span> <span data-ttu-id="2c242-183">Layouts sind Komponenten, mit denen Sie die Duplizierung des Inhalts in der App vermeiden können.</span><span class="sxs-lookup"><span data-stu-id="2c242-183">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="2c242-184">Weitere Informationen finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="2c242-184">For more information, see <xref:blazor/layouts>.</span></span>

   <span data-ttu-id="2c242-185">Fügen Sie der Todo-Komponente ein `<NavLink>`-Element hinzu. Fügen Sie dazu in der Datei *Components/Shared/NavMenu.razor* (in Blazor *Shared/NavMenu.cshtml*) unterhalb der vorhandenen Listenelemente das folgende Markup für ein Listenelement ein:</span><span class="sxs-lookup"><span data-stu-id="2c242-185">Add a `<NavLink>` for the Todo component by adding the following list item markup below the existing list items in the *Components/Shared/NavMenu.razor* (*Shared/NavMenu.cshtml* in Blazor) file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="2c242-186">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="2c242-186">Rebuild and run the app.</span></span> <span data-ttu-id="2c242-187">Besuchen Sie die neue Todo-Seite, um sicherzustellen, dass der Link zur Todo-Komponente funktioniert.</span><span class="sxs-lookup"><span data-stu-id="2c242-187">Visit the new Todo page to confirm that the link to the Todo component works.</span></span>

1. <span data-ttu-id="2c242-188">Fügen Sie eine *TodoItem.cs*-Datei dem Stamm des Projekts hinzu, die eine Klasse zum Darstellen eines Aufgabenelements enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="2c242-188">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="2c242-189">Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="2c242-189">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/TodoItem.cs)]

1. <span data-ttu-id="2c242-190">Navigieren Sie zurück zur Todo-Komponente (*Components/Pages/Todo.razor* oder in Blazor *Pages/Todo.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="2c242-190">Return to the Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   * <span data-ttu-id="2c242-191">Fügen Sie ein Feld für die Todo-Elemente in einem `@functions`-Block hinzu.</span><span class="sxs-lookup"><span data-stu-id="2c242-191">Add a field for the todos in an `@functions` block.</span></span> <span data-ttu-id="2c242-192">Die Todo-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.</span><span class="sxs-lookup"><span data-stu-id="2c242-192">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="2c242-193">Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern.</span><span class="sxs-lookup"><span data-stu-id="2c242-193">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. <span data-ttu-id="2c242-194">Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgaben hinzugefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="2c242-194">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="2c242-195">Fügen Sie eine Texteingabe und eine Schaltfläche unterhalb der Liste hinzu:</span><span class="sxs-lookup"><span data-stu-id="2c242-195">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. <span data-ttu-id="2c242-196">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="2c242-196">Rebuild and run the app.</span></span> <span data-ttu-id="2c242-197">Bei Auswahl der Schaltfläche **Todo-Element hinzufügen** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="2c242-197">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="2c242-198">Fügen Sie eine `AddTodo`-Methode der Todo-Komponente hinzu, und registrieren Sie sie mit dem `onclick`-Attribut für Schaltflächenklicks:</span><span class="sxs-lookup"><span data-stu-id="2c242-198">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

   <span data-ttu-id="2c242-199">Die `AddTodo`-C#-Methode wird aufgerufen, wenn die Schaltfläche ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="2c242-199">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="2c242-200">Um den Titel des neuen Aufgabenelements abzurufen, fügen Sie ein `newTodo`-Zeichenfolgenfeld hinzu, und binden Sie es mithilfe des `bind`-Attributs an den Wert der Texteingabe:</span><span class="sxs-lookup"><span data-stu-id="2c242-200">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo">
   ```

1. <span data-ttu-id="2c242-201">Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="2c242-201">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="2c242-202">Löschen Sie den Wert der Texteingabe, indem Sie für `newTodo` eine leere Zeichenfolge festlegen:</span><span class="sxs-lookup"><span data-stu-id="2c242-202">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. <span data-ttu-id="2c242-203">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="2c242-203">Rebuild and run the app.</span></span> <span data-ttu-id="2c242-204">Fügen Sie der Aufgabenliste einige Aufgabenelemente hinzu, um den neuen Code zu testen.</span><span class="sxs-lookup"><span data-stu-id="2c242-204">Add some todos to the todo list to test the new code.</span></span>

1. <span data-ttu-id="2c242-205">Der Titeltext für die einzelnen Aufgabenelemente kann bearbeitet werden, und ein Kontrollkästchen kann dem Benutzer helfen, die erledigten Elemente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="2c242-205">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="2c242-206">Fügen Sie für jedes Aufgabenelement eine Kontrollkästcheneingabe hinzu, und binden Sie ihren Wert an die `IsDone`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="2c242-206">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="2c242-207">Ändern Sie `@todo.Title` in ein `<input>`-Element, das an `@todo.Title` gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="2c242-207">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. <span data-ttu-id="2c242-208">Um sicherzustellen, dass diese Werte gebunden sind, aktualisieren Sie den `<h1>`-Header, um die Anzahl der noch nicht erledigten Aufgabenelemente anzuzeigen (`IsDone` ist `false`).</span><span class="sxs-lookup"><span data-stu-id="2c242-208">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="2c242-209">Die vollständige Todo-Komponente (*Components/Pages/Todo.razor* oder in Blazor *Pages/Todo.cshtml*) sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="2c242-209">The completed Todo component (*Components/Pages/Todo.razor* or *Pages/Todo.cshtml* in Blazor):</span></span>

   [!code-cshtml[](build-your-first-blazor-app/samples/3.x/RazorComponents/Components/Pages/Todo.razor)]

1. <span data-ttu-id="2c242-210">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="2c242-210">Rebuild and run the app.</span></span> <span data-ttu-id="2c242-211">Fügen Sie Aufgabenelemente hinzu, um den neuen Code zu testen.</span><span class="sxs-lookup"><span data-stu-id="2c242-211">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="2c242-212">Veröffentlichen und Bereitstellen der App</span><span class="sxs-lookup"><span data-stu-id="2c242-212">Publish and deploy the app</span></span>

<span data-ttu-id="2c242-213">Wie Sie die App veröffentlichen, erfahren Sie unter <xref:host-and-deploy/blazor/index>.</span><span class="sxs-lookup"><span data-stu-id="2c242-213">To publish the app, see <xref:host-and-deploy/blazor/index>.</span></span>
