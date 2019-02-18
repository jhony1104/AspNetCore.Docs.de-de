---
title: Erstellen Ihrer ersten Razor Components-App
author: guardrex
description: Erstellen Sie schrittweise eine Razor Components-App, und lernen Sie grundlegende Razor Components-Konzepte kennen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
uid: tutorials/first-razor-components-app
ms.openlocfilehash: 0c3dd2366581d73bad44e2911602e13c6c0daf9a
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159342"
---
# <a name="build-your-first-razor-components-app"></a><span data-ttu-id="d88d0-103">Erstellen Ihrer ersten Razor Components-App</span><span class="sxs-lookup"><span data-stu-id="d88d0-103">Build your first Razor Components app</span></span>

<span data-ttu-id="d88d0-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d88d0-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="d88d0-105">In diesem Tutorial erfahren Sie, wie Sie eine App mit Razor Components erstellen, und es demonstriert die grundlegenden Razor Components-Konzepte.</span><span class="sxs-lookup"><span data-stu-id="d88d0-105">This tutorial shows you how to build an app with Razor Components and demonstrates basic Razor Components concepts.</span></span> <span data-ttu-id="d88d0-106">Sie können für dieses Tutorial entweder ein Razor Components-basiertes (in .NET Core 3.0 oder höher unterstütztes) Projekt oder ein Blazor-basiertes (in einer zukünftigen Version von .NET Core unterstütztes) Projekt verwenden.</span><span class="sxs-lookup"><span data-stu-id="d88d0-106">You can enjoy this tutorial using either a Razor Components-based project (supported in .NET Core 3.0 or later) or using a Blazor-based project (supported in a future release of .NET Core).</span></span>

<span data-ttu-id="d88d0-107">Für eine Benutzeroberfläche mit ASP.NET Core-Razor Components (*empfohlen*):</span><span class="sxs-lookup"><span data-stu-id="d88d0-107">For an experience using ASP.NET Core Razor Components (*recommended*):</span></span>

* <span data-ttu-id="d88d0-108">Führen Sie die Anleitung in <xref:razor-components/get-started> aus, um ein Razor Components-basiertes Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d88d0-108">Follow the guidance in <xref:razor-components/get-started> to create a Razor Components-based project.</span></span>
* <span data-ttu-id="d88d0-109">Benennen Sie das Projekt mit `RazorComponents`.</span><span class="sxs-lookup"><span data-stu-id="d88d0-109">Name the project `RazorComponents`.</span></span>
* <span data-ttu-id="d88d0-110">Eine Projektmappe mit mehreren Projekten wird aus der Razor Components-Vorlage erstellt.</span><span class="sxs-lookup"><span data-stu-id="d88d0-110">A multi-project solution is created from the Razor Components template.</span></span> <span data-ttu-id="d88d0-111">Das Razor Components-Projekt wird als *RazorComponents.App* generiert.</span><span class="sxs-lookup"><span data-stu-id="d88d0-111">The Razor Components project is generated as *RazorComponents.App*.</span></span>

<span data-ttu-id="d88d0-112">Für eine Benutzeroberfläche mit Blazor:</span><span class="sxs-lookup"><span data-stu-id="d88d0-112">For an experience using Blazor:</span></span>

* <span data-ttu-id="d88d0-113">Führen Sie die Anleitung in <xref:spa/blazor/get-started> aus, um ein Blazor-basiertes Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d88d0-113">Follow the guidance in <xref:spa/blazor/get-started> to create a Blazor-based project.</span></span>
* <span data-ttu-id="d88d0-114">Benennen Sie das Projekt mit `Blazor`.</span><span class="sxs-lookup"><span data-stu-id="d88d0-114">Name the project `Blazor`.</span></span>
* <span data-ttu-id="d88d0-115">Eine Lösung für ein einzelnes Projekt wird aus der Blazor-Vorlage erstellt.</span><span class="sxs-lookup"><span data-stu-id="d88d0-115">A single-project solution is created from the Blazor template.</span></span>

<span data-ttu-id="d88d0-116">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d88d0-116">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-razor-components-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="d88d0-117">Weitere Voraussetzungen finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="d88d0-117">See the following topics for prerequisites:</span></span>

## <a name="build-components"></a><span data-ttu-id="d88d0-118">Erstellen von Komponenten</span><span class="sxs-lookup"><span data-stu-id="d88d0-118">Build components</span></span>

1. <span data-ttu-id="d88d0-119">Navigieren Sie zu jeder der drei Seiten der App: „Home“, „Counter“ und „Fetch data“.</span><span class="sxs-lookup"><span data-stu-id="d88d0-119">Browse to each of the app's three pages: Home, Counter, and Fetch data.</span></span> <span data-ttu-id="d88d0-120">Diese Seiten werden durch die Razor-Dateien im *Pages*-Ordner implementiert: *Index.cshtml*, *Counter.cshtml* und *FetchData.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d88d0-120">These pages are implemented by Razor files in the *Pages* folder: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.</span></span>

1. <span data-ttu-id="d88d0-121">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="d88d0-121">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="d88d0-122">Das Heraufsetzen eines Zählers auf einer Webseite erfordert normalerweise das Schreiben in JavaScript, aber Razor Components bietet einen besseren Ansatz mit C#.</span><span class="sxs-lookup"><span data-stu-id="d88d0-122">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor Components provides a better approach using C#.</span></span>

1. <span data-ttu-id="d88d0-123">Sehen Sie sich die Implementierung der Counter-Komponente in der *Counter.cshtml*-Datei an.</span><span class="sxs-lookup"><span data-stu-id="d88d0-123">Examine the implementation of the Counter component in the *Counter.cshtml* file.</span></span>

   <span data-ttu-id="d88d0-124">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d88d0-124">*Pages/Counter.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter1.cshtml)]

   <span data-ttu-id="d88d0-125">Die Benutzeroberfläche der Counter-Komponente wird mithilfe von HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="d88d0-125">The UI of the Counter component is defined using HTML.</span></span> <span data-ttu-id="d88d0-126">Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d88d0-126">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](xref:mvc/views/razor).</span></span> <span data-ttu-id="d88d0-127">Das HTML-Markup und die C#-Renderinglogik werden zur Erstellungszeit in eine Komponentenklasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="d88d0-127">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="d88d0-128">Der Name der generierten .NET-Klasse stimmt mit dem Namen der Datei überein.</span><span class="sxs-lookup"><span data-stu-id="d88d0-128">The name of the generated .NET class matches the file name.</span></span>

   <span data-ttu-id="d88d0-129">Member der Komponentenklasse werden in einem `@functions`-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="d88d0-129">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="d88d0-130">Im `@functions`-Block werden der Komponentenstatus (Eigenschaften, Felder) und Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben.</span><span class="sxs-lookup"><span data-stu-id="d88d0-130">In the `@functions` block, component state (properties, fields) and methods are specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="d88d0-131">Diese Member werden dann als Teil der Renderinglogik der Komponente und zur Behandlung von Ereignissen verwendet.</span><span class="sxs-lookup"><span data-stu-id="d88d0-131">These members are then used as part of the component's rendering logic and for handling events.</span></span>

   <span data-ttu-id="d88d0-132">Wenn die **Hier klicken**-Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="d88d0-132">When the **Click me** button is selected:</span></span>

   * <span data-ttu-id="d88d0-133">Der registrierte `onclick`-Handler der Counter-Komponente wird aufgerufen (die `IncrementCount`-Methode).</span><span class="sxs-lookup"><span data-stu-id="d88d0-133">The Counter component's registered `onclick` handler is called (the `IncrementCount` method).</span></span>
   * <span data-ttu-id="d88d0-134">Die Counter-Komponente generiert ihre Renderstruktur neu.</span><span class="sxs-lookup"><span data-stu-id="d88d0-134">The Counter component regenerates its render tree.</span></span>
   * <span data-ttu-id="d88d0-135">Die neue Renderstruktur wird mit der vorherigen verglichen.</span><span class="sxs-lookup"><span data-stu-id="d88d0-135">The new render tree is compared to the previous one.</span></span>
   * <span data-ttu-id="d88d0-136">Es werden nur Änderungen des Dokumentobjektmodells (Document Object Model, DOM) angewendet.</span><span class="sxs-lookup"><span data-stu-id="d88d0-136">Only modifications to the Document Object Model (DOM) are applied.</span></span> <span data-ttu-id="d88d0-137">Der angezeigte Zähler wird aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="d88d0-137">The displayed count is updated.</span></span>

1. <span data-ttu-id="d88d0-138">Ändern Sie die C#-Logik der Counter-Komponente, um das Zählerinkrement von eins auf zwei heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="d88d0-138">Modify the C# logic of the Counter component to make the count increment by two instead of one.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Counter2.cshtml?highlight=14)]

1. <span data-ttu-id="d88d0-139">Erstellen Sie die App neu, und führen Sie sie aus, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="d88d0-139">Rebuild and run the app to see the changes.</span></span> <span data-ttu-id="d88d0-140">Wählen Sie die Schaltfläche **Hier klicken** aus, und der Zähler wird um zwei heraufgesetzt.</span><span class="sxs-lookup"><span data-stu-id="d88d0-140">Select the **Click me** button, and the counter increments by two.</span></span>

## <a name="use-components"></a><span data-ttu-id="d88d0-141">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="d88d0-141">Use components</span></span>

<span data-ttu-id="d88d0-142">Beziehen Sie eine Komponente mithilfe einer HTML-ähnlichen Syntax in eine andere Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="d88d0-142">Include a component into another component using an HTML-like syntax.</span></span>

1. <span data-ttu-id="d88d0-143">Fügen Sie die Counter-Komponente der Index-Komponente (Startseite) der App durch Hinzufügen eines `<Counter />`-Elements zur Index-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="d88d0-143">Add the Counter component to the app's Index (home page) component by adding a `<Counter />` element to the Index component.</span></span>

   <span data-ttu-id="d88d0-144">Wenn Sie für diese Oberfläche Blazor verwenden, enthält die Index-Komponente eine Survey Prompt-Komponente (`<SurveyPrompt>`-Element).</span><span class="sxs-lookup"><span data-stu-id="d88d0-144">If you're using Blazor for this experience, a Survey Prompt component (`<SurveyPrompt>` element) is in the Index component.</span></span> <span data-ttu-id="d88d0-145">Ersetzen Sie das `<SurveyPrompt>`-Element durch das `<Counter>`-Element.</span><span class="sxs-lookup"><span data-stu-id="d88d0-145">Replace the `<SurveyPrompt>` element with the `<Counter>` element.</span></span>

   <span data-ttu-id="d88d0-146">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d88d0-146">*Pages/Index.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/Index.cshtml?highlight=7)]

1. <span data-ttu-id="d88d0-147">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="d88d0-147">Rebuild and run the app.</span></span> <span data-ttu-id="d88d0-148">Die Startseite verfügt über einen eigenen Zähler.</span><span class="sxs-lookup"><span data-stu-id="d88d0-148">The home page has its own counter.</span></span>

## <a name="component-parameters"></a><span data-ttu-id="d88d0-149">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="d88d0-149">Component parameters</span></span>

<span data-ttu-id="d88d0-150">Komponenten können auch Parameter aufweisen.</span><span class="sxs-lookup"><span data-stu-id="d88d0-150">Components can also have parameters.</span></span> <span data-ttu-id="d88d0-151">Komponentenparameter werden mithilfe nicht öffentlicher Eigenschaften für die Komponentenklasse definiert, die mit `[Parameter]` versehen ist.</span><span class="sxs-lookup"><span data-stu-id="d88d0-151">Component parameters are defined using non-public properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="d88d0-152">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="d88d0-152">Use attributes to specify arguments for a component in markup.</span></span>

1. <span data-ttu-id="d88d0-153">Aktualisieren Sie den `@functions`-C#-Code der Komponente:</span><span class="sxs-lookup"><span data-stu-id="d88d0-153">Update the component's `@functions` C# code:</span></span>

   * <span data-ttu-id="d88d0-154">Fügen Sie eine `IncrementAmount`-Eigenschaft hinzu, die mit dem `[Parameter]`-Attribut ausgestattet ist.</span><span class="sxs-lookup"><span data-stu-id="d88d0-154">Add a `IncrementAmount` property decorated with the `[Parameter]` attribute.</span></span>
   * <span data-ttu-id="d88d0-155">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d88d0-155">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

   <span data-ttu-id="d88d0-156">*Pages/Counter.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d88d0-156">*Pages/Counter.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Counter.cshtml?highlight=12,16)]

<!-- Add back when supported.
   > [!NOTE]
   > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
-->

1. <span data-ttu-id="d88d0-157">Geben Sie unter Verwendung eines Attributs einen `IncrementAmount`-Parameter in das `<Counter>`-Element der Home-Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="d88d0-157">Specify an `IncrementAmount` parameter in the Home component's `<Counter>` element using an attribute.</span></span> <span data-ttu-id="d88d0-158">Legen Sie den Wert fest, um den Zähler um zehn heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="d88d0-158">Set the value to increment the counter by ten.</span></span>

   <span data-ttu-id="d88d0-159">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d88d0-159">*Pages/Index.cshtml*:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Index.cshtml?highlight=7)]

1. <span data-ttu-id="d88d0-160">Laden Sie die Seite neu.</span><span class="sxs-lookup"><span data-stu-id="d88d0-160">Reload the page.</span></span> <span data-ttu-id="d88d0-161">Der Zähler auf der Startseite wird jedes Mal, wenn die Schaltfläche **Hier klicken** ausgewählt wird, um zehn heraufgesetzt.</span><span class="sxs-lookup"><span data-stu-id="d88d0-161">The home page counter increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="d88d0-162">Der Zähler auf der *Counter*-Seite wird um eins heraufgesetzt.</span><span class="sxs-lookup"><span data-stu-id="d88d0-162">The counter on the *Counter* page increments by one.</span></span>

## <a name="route-to-components"></a><span data-ttu-id="d88d0-163">Weiterleiten an Komponenten</span><span class="sxs-lookup"><span data-stu-id="d88d0-163">Route to components</span></span>

<span data-ttu-id="d88d0-164">Die `@page`-Anweisung im oberen Teil der *Counter.cshtml*-Datei gibt an, dass diese Komponente ein Routingendpunkt ist.</span><span class="sxs-lookup"><span data-stu-id="d88d0-164">The `@page` directive at the top of the *Counter.cshtml* file specifies that this component is a routing endpoint.</span></span> <span data-ttu-id="d88d0-165">Die Counter-Komponente behandelt Anforderungen, die an `/Counter` gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="d88d0-165">The Counter component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="d88d0-166">Ohne die `@page`-Anweisung behandelt die Komponente keine weitergeleiteten Anforderungen, aber die Komponente kann immer noch von anderen Komponenten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d88d0-166">Without the `@page` directive, the component doesn't handle routed requests, but the component can still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="d88d0-167">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="d88d0-167">Dependency injection</span></span>

<span data-ttu-id="d88d0-168">Im Dienstcontainer der App registrierte Dienste stehen für Komponenten über [Abhängigkeitsinjektion (DI, Dependency Injection)](xref:fundamentals/dependency-injection) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="d88d0-168">Services registered in the app's service container are available to components via [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d88d0-169">Fügen Sie Dienste mit der `@inject`-Anweisung in eine Komponente ein.</span><span class="sxs-lookup"><span data-stu-id="d88d0-169">Inject services into a component using the `@inject` directive.</span></span>

<span data-ttu-id="d88d0-170">Überprüfen Sie die Anweisungen der FetchData-Komponente (*Pages/FetchData.cshtml*).</span><span class="sxs-lookup"><span data-stu-id="d88d0-170">Examine the directives of the FetchData component (*Pages/FetchData.cshtml*).</span></span> <span data-ttu-id="d88d0-171">Mit der `@inject`-Anweisung wird die Instanz des `WeatherForecastService`-Diensts in die Komponente eingefügt:</span><span class="sxs-lookup"><span data-stu-id="d88d0-171">The `@inject` directive is used to inject the instance of the `WeatherForecastService` service into the component:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData1.cshtml?highlight=3)]

<span data-ttu-id="d88d0-172">Der `WeatherForecastService`-Dienst ist als [Singleton](xref:fundamentals/dependency-injection#service-lifetimes) registriert, sodass eine Instanz des Diensts in der gesamten App verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="d88d0-172">The `WeatherForecastService` service is registered as a [singleton](xref:fundamentals/dependency-injection#service-lifetimes), so one instance of the service is available throughout the app.</span></span>

<span data-ttu-id="d88d0-173">Die FetchData-Komponente verwendet den eingefügten Dienst als `ForecastService`, um ein Array von `WeatherForecast`-Objekten abzurufen:</span><span class="sxs-lookup"><span data-stu-id="d88d0-173">The FetchData component uses the injected service, as `ForecastService`, to retrieve an array of `WeatherForecast` objects:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData2.cshtml?highlight=6)]

<span data-ttu-id="d88d0-174">Eine [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife rendert jede Vorhersageinstanz als eine Zeile in der Wetterdatentabelle:</span><span class="sxs-lookup"><span data-stu-id="d88d0-174">A [@foreach](/dotnet/csharp/language-reference/keywords/foreach-in) loop is used to render each forecast instance as a row in the table of weather data:</span></span>

[!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData3.cshtml?highlight=11-19)]

## <a name="build-a-todo-list"></a><span data-ttu-id="d88d0-175">Erstellen einer Aufgabenliste</span><span class="sxs-lookup"><span data-stu-id="d88d0-175">Build a todo list</span></span>

<span data-ttu-id="d88d0-176">Fügen Sie der App eine neue Seite hinzu, die eine einfache Aufgabenliste implementiert.</span><span class="sxs-lookup"><span data-stu-id="d88d0-176">Add a new page to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="d88d0-177">Fügen Sie dem Ordner *Pages* eine leere Datei mit dem Namen *Todo.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d88d0-177">Add an empty file to the *Pages* folder named *Todo.cshtml*.</span></span>

1. <span data-ttu-id="d88d0-178">Geben Sie das ursprüngliche Markup für die Seite an:</span><span class="sxs-lookup"><span data-stu-id="d88d0-178">Provide the initial markup for the page:</span></span>

   ```cshtml
   @page "/todo"

   <h1>Todo</h1>
   ```

1. <span data-ttu-id="d88d0-179">Fügen Sie der Navigationsleiste die Todo-Seite hinzu.</span><span class="sxs-lookup"><span data-stu-id="d88d0-179">Add the Todo page to the navigation bar.</span></span>

   <span data-ttu-id="d88d0-180">Die NavMenu-Komponente (*Shared/NavMenu.csthml*) wird im Layout der App verwendet.</span><span class="sxs-lookup"><span data-stu-id="d88d0-180">The NavMenu component (*Shared/NavMenu.csthml*) is used in the app's layout.</span></span> <span data-ttu-id="d88d0-181">Layouts sind Komponenten, mit denen Sie die Duplizierung des Inhalts in der App vermeiden können.</span><span class="sxs-lookup"><span data-stu-id="d88d0-181">Layouts are components that allow you to avoid duplication of content in the app.</span></span> <span data-ttu-id="d88d0-182">Weitere Informationen finden Sie unter <xref:razor-components/layouts>.</span><span class="sxs-lookup"><span data-stu-id="d88d0-182">For more information, see <xref:razor-components/layouts>.</span></span>

   <span data-ttu-id="d88d0-183">Fügen Sie einen `<NavLink>` für die Todo-Seite hinzu, indem Sie das folgende Listenelementmarkup unterhalb der vorhandenen Listenelemente der Datei *Shared/NavMenu.csthml* hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="d88d0-183">Add a `<NavLink>` for the Todo page by adding the following list item markup below the existing list items in the *Shared/NavMenu.csthml* file:</span></span>

   ```cshtml
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. <span data-ttu-id="d88d0-184">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="d88d0-184">Rebuild and run the app.</span></span> <span data-ttu-id="d88d0-185">Besuchen Sie die neue Todo-Seite, um sicherzustellen, dass der Link zur Todo-Seite funktioniert.</span><span class="sxs-lookup"><span data-stu-id="d88d0-185">Visit the new Todo page to confirm that the link to the Todo page works.</span></span>

1. <span data-ttu-id="d88d0-186">Fügen Sie eine *TodoItem.cs*-Datei dem Stamm des Projekts hinzu, die eine Klasse zum Darstellen eines Aufgabenelements enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="d88d0-186">Add a *TodoItem.cs* file to the root of the project to hold a class that represents a todo item.</span></span> <span data-ttu-id="d88d0-187">Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="d88d0-187">Use the following C# code for the `TodoItem` class:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/TodoItem.cs)]

1. <span data-ttu-id="d88d0-188">Kehren Sie zur Todo-Komponente (*Todo.cshtml*) zurück:</span><span class="sxs-lookup"><span data-stu-id="d88d0-188">Return to the Todo component (*Todo.cshtml*):</span></span>

   * <span data-ttu-id="d88d0-189">Fügen Sie ein Feld für die Todo-Elemente in einem `@functions`-Block hinzu.</span><span class="sxs-lookup"><span data-stu-id="d88d0-189">Add a field for the todos in an `@functions` block.</span></span> <span data-ttu-id="d88d0-190">Die Todo-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.</span><span class="sxs-lookup"><span data-stu-id="d88d0-190">The Todo component uses this field to maintain the state of the todo list.</span></span>
   * <span data-ttu-id="d88d0-191">Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern.</span><span class="sxs-lookup"><span data-stu-id="d88d0-191">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData4.cshtml?highlight=5-10,12-14)]

1. <span data-ttu-id="d88d0-192">Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgaben hinzugefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="d88d0-192">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="d88d0-193">Fügen Sie eine Texteingabe und eine Schaltfläche unterhalb der Liste hinzu:</span><span class="sxs-lookup"><span data-stu-id="d88d0-193">Add a text input and a button below the list:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData5.cshtml?highlight=12-13)]

1. <span data-ttu-id="d88d0-194">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="d88d0-194">Rebuild and run the app.</span></span> <span data-ttu-id="d88d0-195">Bei Auswahl der Schaltfläche **Todo-Element hinzufügen** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="d88d0-195">When the **Add todo** button is selected, nothing happens because an event handler isn't wired up to the button.</span></span>

1. <span data-ttu-id="d88d0-196">Fügen Sie eine `AddTodo`-Methode der Todo-Komponente hinzu, und registrieren Sie sie mit dem `onclick`-Attribut für Schaltflächenklicks:</span><span class="sxs-lookup"><span data-stu-id="d88d0-196">Add an `AddTodo` method to the Todo component and register it for button clicks using the `onclick` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData6.cshtml?highlight=2,7-10)]

   <span data-ttu-id="d88d0-197">Die `AddTodo`-C#-Methode wird aufgerufen, wenn die Schaltfläche ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="d88d0-197">The `AddTodo` C# method is called when the button is selected.</span></span>

1. <span data-ttu-id="d88d0-198">Um den Titel des neuen Aufgabenelements abzurufen, fügen Sie ein `newTodo`-Zeichenfolgenfeld hinzu, und binden Sie es mithilfe des `bind`-Attributs an den Wert der Texteingabe:</span><span class="sxs-lookup"><span data-stu-id="d88d0-198">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData7.cshtml?highlight=2)]

   ```cshtml
   <input placeholder="Something todo" bind="@newTodo" />
   ```

1. <span data-ttu-id="d88d0-199">Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="d88d0-199">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="d88d0-200">Löschen Sie den Wert der Texteingabe, indem Sie für `newTodo` eine leere Zeichenfolge festlegen:</span><span class="sxs-lookup"><span data-stu-id="d88d0-200">Clear the value of the text input by setting `newTodo` to an empty string:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData8.cshtml?highlight=19-26)]

1. <span data-ttu-id="d88d0-201">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="d88d0-201">Rebuild and run the app.</span></span> <span data-ttu-id="d88d0-202">Fügen Sie der Aufgabenliste einige Aufgabenelemente hinzu, um den neuen Code zu testen.</span><span class="sxs-lookup"><span data-stu-id="d88d0-202">Add some todos to the todo list to test the new code.</span></span>

1. <span data-ttu-id="d88d0-203">Der Titeltext für die einzelnen Aufgabenelemente kann bearbeitet werden, und ein Kontrollkästchen kann dem Benutzer helfen, die erledigten Elemente nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="d88d0-203">The title text for each todo item can be made editable and a check box can help the user keep track of completed items.</span></span> <span data-ttu-id="d88d0-204">Fügen Sie für jedes Aufgabenelement eine Kontrollkästcheneingabe hinzu, und binden Sie ihren Wert an die `IsDone`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="d88d0-204">Add a check box input for each todo item and bind its value to the `IsDone` property.</span></span> <span data-ttu-id="d88d0-205">Ändern Sie `@todo.Title` in ein `<input>`-Element, das an `@todo.Title` gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="d88d0-205">Change `@todo.Title` to an `<input>` element bound to `@todo.Title`:</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples_snapshot/3.x/FetchData9.cshtml?highlight=5-6)]

1. <span data-ttu-id="d88d0-206">Um sicherzustellen, dass diese Werte gebunden sind, aktualisieren Sie den `<h1>`-Header, um die Anzahl der noch nicht erledigten Aufgabenelemente anzuzeigen (`IsDone` ist `false`).</span><span class="sxs-lookup"><span data-stu-id="d88d0-206">To verify that these values are bound, update the `<h1>` header to show a count of the number of todo items that aren't complete (`IsDone` is `false`).</span></span>

   ```cshtml
   <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
   ```

1. <span data-ttu-id="d88d0-207">Die erledigte Todo-Komponente (*Todo.cshtml*):</span><span class="sxs-lookup"><span data-stu-id="d88d0-207">The completed Todo component (*Todo.cshtml*):</span></span>

   [!code-cshtml[](build-your-first-razor-components-app/samples/3.x/RazorComponents/RazorComponents.App/Pages/Todo.cshtml)]

1. <span data-ttu-id="d88d0-208">Erstellen Sie die App neu, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="d88d0-208">Rebuild and run the app.</span></span> <span data-ttu-id="d88d0-209">Fügen Sie Aufgabenelemente hinzu, um den neuen Code zu testen.</span><span class="sxs-lookup"><span data-stu-id="d88d0-209">Add todo items to test the new code.</span></span>

## <a name="publish-and-deploy-the-app"></a><span data-ttu-id="d88d0-210">Veröffentlichen und Bereitstellen der App</span><span class="sxs-lookup"><span data-stu-id="d88d0-210">Publish and deploy the app</span></span>

<span data-ttu-id="d88d0-211">Wie Sie die App veröffentlichen, erfahren Sie unter <xref:host-and-deploy/razor-components/index#publish-the-app>.</span><span class="sxs-lookup"><span data-stu-id="d88d0-211">To publish the app, see <xref:host-and-deploy/razor-components/index#publish-the-app>.</span></span>
