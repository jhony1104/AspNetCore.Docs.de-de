---
title: Erstellen Ihrer ersten Blazor-App
author: guardrex
description: Erstellen Sie schrittweise eine Blazor-App, und lernen Sie schnell die grundlegenden Features des Razor Components-Frameworks.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: tutorials/first-blazor-app
ms.openlocfilehash: 99396e200eb121524abccc20a7d461062c94ecc7
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667994"
---
# <a name="build-your-first-blazor-app"></a><span data-ttu-id="b2439-103">Erstellen Ihrer ersten Blazor-App</span><span class="sxs-lookup"><span data-stu-id="b2439-103">Build your first Blazor app</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="b2439-104">In diesem Tutorial erstellen Sie schrittweise eine Blazor-App und lernen schnell die grundlegenden Features des Razor Components-Frameworks.</span><span class="sxs-lookup"><span data-stu-id="b2439-104">In this tutorial, you build a Blazor app step-by-step and quickly learn the basic features of the Razor Components framework.</span></span>

<span data-ttu-id="b2439-105">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="b2439-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="b2439-106">Informieren Sie sich in dem Thema [Erste Schritte](xref:razor-components/get-started) über die Voraussetzungen.</span><span class="sxs-lookup"><span data-stu-id="b2439-106">See the [Get started](xref:razor-components/get-started) topic for prerequisites.</span></span>

<span data-ttu-id="b2439-107">So erstellen Sie das Projekt in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="b2439-107">To create the project in Visual Studio:</span></span>

1. <span data-ttu-id="b2439-108">Wählen Sie **Datei** > **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="b2439-108">Select **File** > **New** > **Project**.</span></span> <span data-ttu-id="b2439-109">Wählen Sie **Web** > **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="b2439-109">Select **Web** > **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="b2439-110">Benennen Sie das Projekt im Feld **Name** mit „BlazorApp1“.</span><span class="sxs-lookup"><span data-stu-id="b2439-110">Name the project "BlazorApp1" in the **Name** field.</span></span> <span data-ttu-id="b2439-111">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="b2439-111">Select **OK**.</span></span>

    ![Neues ASP.NET Core-Projekt](build-your-first-blazor-app/_static/new-aspnet-core-project.png)

1. <span data-ttu-id="b2439-113">Das Dialogfeld **Neue ASP.NET Core-Webanwendung** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b2439-113">The **New ASP.NET Core Web Application** dialog appears.</span></span> <span data-ttu-id="b2439-114">Stellen Sie sicher, dass **.NET Core** oben ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="b2439-114">Make sure **.NET Core** is selected at the top.</span></span> <span data-ttu-id="b2439-115">Wählen Sie **ASP.NET Core 2.1** aus.</span><span class="sxs-lookup"><span data-stu-id="b2439-115">Select **ASP.NET Core 2.1**.</span></span> <span data-ttu-id="b2439-116">Wählen Sie die **Blazor**-Vorlage und dann **OK** aus.</span><span class="sxs-lookup"><span data-stu-id="b2439-116">Choose the **Blazor** template and select **OK**.</span></span>

    ![Dialogfeld „Neue Blazor-App“](build-your-first-blazor-app/_static/new-blazor-app-dialog.png)

1. <span data-ttu-id="b2439-118">Sobald das Projekt erstellt ist, drücken Sie **STRG+F5** zum Ausführen der App *ohne Debugger*.</span><span class="sxs-lookup"><span data-stu-id="b2439-118">Once the project is created, press **Ctrl-F5** to run the app *without the debugger*.</span></span> <span data-ttu-id="b2439-119">Die Ausführung mit dem Debugger (**F5**) wird zu diesem Zeitpunkt nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b2439-119">Running with the debugger (**F5**) isn't supported at this time.</span></span>

> [!NOTE]
> <span data-ttu-id="b2439-120">Wenn Sie nicht Visual Studio verwenden, erstellen Sie die Blazor-App an einer Eingabeaufforderung unter Windows, macOS oder Linux:</span><span class="sxs-lookup"><span data-stu-id="b2439-120">If not using Visual Studio, create the Blazor app at a command prompt on Windows, macOS, or Linux:</span></span>
>
> ```console
> dotnet new --install "Microsoft.AspNetCore.Blazor.Templates"
> dotnet new blazor -o BlazorApp1
> cd BlazorApp1
> dotnet run
> ```
>
> <span data-ttu-id="b2439-121">Navigieren Sie mit der Localhostadresse und dem Port, die nach Ausführung von `dotnet run` im Konsolenfenster ausgegeben werden, zu der App.</span><span class="sxs-lookup"><span data-stu-id="b2439-121">Navigate to the app using the localhost address and port provided in the console window output after `dotnet run` is executed.</span></span> <span data-ttu-id="b2439-122">Fahren Sie die App mit **STRG+C** im Konsolenfenster herunter.</span><span class="sxs-lookup"><span data-stu-id="b2439-122">Use **Ctrl-C** in the console window to shutdown the app.</span></span>

<span data-ttu-id="b2439-123">Die Blazor-App wird im Browser ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="b2439-123">The Blazor app runs in the browser:</span></span>

![Blazor-App-Startseite](https://user-images.githubusercontent.com/1874516/39509497-5515c3ea-4d9b-11e8-887f-019ea4fdb3ee.png)

## <a name="build-components"></a><span data-ttu-id="b2439-125">Erstellen von Komponenten</span><span class="sxs-lookup"><span data-stu-id="b2439-125">Build components</span></span>

1. <span data-ttu-id="b2439-126">Navigieren Sie zu jeder der drei Seiten der App: „Home“, „Counter“ und „Fetch data“.</span><span class="sxs-lookup"><span data-stu-id="b2439-126">Browse to each of the app's three pages: Home, Counter, and Fetch data.</span></span>

    <span data-ttu-id="b2439-127">Diese drei Seiten werden durch die drei Razor-Dateien im *Pages*-Ordner implementiert: *Index.cshtml*, *Counter.cshtml* und *FetchData.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="b2439-127">These three pages are implemented by the three Razor files in the *Pages* folder: *Index.cshtml*, *Counter.cshtml*, and *FetchData.cshtml*.</span></span> <span data-ttu-id="b2439-128">Jede dieser Dateien implementiert eine Komponente, die auf der Clientseite im Browser kompiliert und ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b2439-128">Each of these files implements a component that's compiled and executed client-side in the browser.</span></span>

1. <span data-ttu-id="b2439-129">Wählen Sie die Schaltfläche auf der Counter-Seite aus.</span><span class="sxs-lookup"><span data-stu-id="b2439-129">Select the button on the Counter page.</span></span>

    ![Blazor-App-Startseite](https://user-images.githubusercontent.com/1874516/39509525-6e367c66-4d9b-11e8-9978-e52a9750c34b.png)

    <span data-ttu-id="b2439-131">Jedes Mal, wenn die Schaltfläche ausgewählt wird, wird der Zähler ohne Seitenaktualisierung heraufgesetzt.</span><span class="sxs-lookup"><span data-stu-id="b2439-131">Each time the button is selected, the counter is incremented without a page refresh.</span></span> <span data-ttu-id="b2439-132">Normalerweise wird diese Art von clientseitigem Verhalten in JavaScript behandelt; hier ist es jedoch mit der `Counter`-Komponente in C# und .NET implementiert.</span><span class="sxs-lookup"><span data-stu-id="b2439-132">Normally, this kind of client-side behavior is handled in JavaScript; but here, it's implemented in C# and .NET by the `Counter` component.</span></span>

1. <span data-ttu-id="b2439-133">Sehen Sie sich die Implementierung der `Counter`-Komponente in der *Counter.cshtml*-Datei an:</span><span class="sxs-lookup"><span data-stu-id="b2439-133">Take a look at the implementation of the `Counter` component in the *Counter.cshtml* file:</span></span>

    ```cshtml
    @page "/counter"

    <h1>Counter</h1>

    <p>Current count: @currentCount</p>

    <button class="btn btn-primary" onclick="@IncrementCount">Click me</button>

    @functions {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
    ```

    <span data-ttu-id="b2439-134">Die Benutzeroberfläche für die `Counter`-Komponente ist im normalen HTML-Format definiert.</span><span class="sxs-lookup"><span data-stu-id="b2439-134">The UI for the `Counter` component is defined using normal HTML.</span></span> <span data-ttu-id="b2439-135">Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b2439-135">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor).</span></span> <span data-ttu-id="b2439-136">Das HTML-Markup und die C#-Renderinglogik werden zur Erstellungszeit in eine Komponentenklasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="b2439-136">The HTML markup and C# rendering logic are converted into a component class at build time.</span></span> <span data-ttu-id="b2439-137">Der Name der generierten .NET-Klasse stimmt mit dem Namen der Datei überein.</span><span class="sxs-lookup"><span data-stu-id="b2439-137">The name of the generated .NET class matches the name of the file.</span></span>

    <span data-ttu-id="b2439-138">Member der Komponentenklasse werden in einem `@functions`-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="b2439-138">Members of the component class are defined in a `@functions` block.</span></span> <span data-ttu-id="b2439-139">Im `@functions`-Block können der Komponentenstatus (Eigenschaften, Felder) und Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b2439-139">In the `@functions` block, component state (properties, fields) and methods can be specified for event handling or for defining other component logic.</span></span> <span data-ttu-id="b2439-140">Diese Member können dann als Teil der Renderinglogik der Komponente und zur Behandlung von Ereignissen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b2439-140">These members can then be used as part of the component's rendering logic and for handling events.</span></span>

    <span data-ttu-id="b2439-141">Wenn die Schaltfläche ausgewählt wird, wird der registrierte `onclick`-Handler der `Counter`-Komponente aufgerufen (die `IncrementCount`-Methode) und die `Counter`-Komponente generiert ihre Renderstruktur neu.</span><span class="sxs-lookup"><span data-stu-id="b2439-141">When the button is selected, the `Counter` component's registered `onclick` handler is called (the `IncrementCount` method) and the `Counter` component regenerates its render tree.</span></span> <span data-ttu-id="b2439-142">Blazor vergleicht die neue Renderstruktur mit der vorherigen und wendet alle Änderungen auf das Browser-Dokumentobjektmodell (Document Object Model, DOM) an.</span><span class="sxs-lookup"><span data-stu-id="b2439-142">Blazor compares the new render tree against the previous one and applies any modifications to the browser Document Object Model (DOM).</span></span> <span data-ttu-id="b2439-143">Der angezeigte Zähler wird aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="b2439-143">The displayed count is updated.</span></span>

1. <span data-ttu-id="b2439-144">Aktualisieren Sie das Markup für die `Counter`-Komponente, damit der Header der obersten Ebene *interessanter* wird.</span><span class="sxs-lookup"><span data-stu-id="b2439-144">Update the markup for the `Counter` component to make the top-level header more *exciting*.</span></span>

    ```cshtml
    @page "/counter"
    <h1><em>Counter!!</em></h1>
    ```

1. <span data-ttu-id="b2439-145">Ändern Sie auch die C#-Logik der `Counter`-Komponente, um das Zählerinkrement von eins auf zwei heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="b2439-145">Also modify the C# logic of the `Counter` component to make the count increment by two instead of one.</span></span>

    ```cshtml
    @functions {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount += 2;
        }
    }
    ```

1. <span data-ttu-id="b2439-146">Aktualisieren Sie die Counter-Seite im Browser, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="b2439-146">Refresh the counter page in the browser to see the changes.</span></span>

    ![Interessanter Zähler](https://user-images.githubusercontent.com/1874516/39509668-e8949a92-4d9b-11e8-91e9-b6a494695d92.png)

## <a name="use-components"></a><span data-ttu-id="b2439-148">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="b2439-148">Use components</span></span>

<span data-ttu-id="b2439-149">Sobald eine Komponente definiert ist, kann sie zum Implementieren anderer Komponenten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b2439-149">After a component is defined, the component can be used to implement other components.</span></span> <span data-ttu-id="b2439-150">Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.</span><span class="sxs-lookup"><span data-stu-id="b2439-150">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

1. <span data-ttu-id="b2439-151">Fügen Sie eine `Counter`-Komponente der Startseite der App (*Index.cshtml*) hinzu.</span><span class="sxs-lookup"><span data-stu-id="b2439-151">Add a `Counter` component to the Home page of the app (*Index.cshtml*).</span></span>

    ```cshtml
    @page "/"

    <h1>Hello, world!</h1>

    Welcome to your new app.

    <SurveyPrompt Title="How is Blazor working for you?" />

    <Counter />
    ```

1. <span data-ttu-id="b2439-152">Aktualisieren Sie die Startseite im Browser.</span><span class="sxs-lookup"><span data-stu-id="b2439-152">Refresh the home page in the browser.</span></span> <span data-ttu-id="b2439-153">Beachten Sie die separate Instanz der `Counter`-Komponente auf der Startseite.</span><span class="sxs-lookup"><span data-stu-id="b2439-153">Note the separate instance of the `Counter` component on the Home page.</span></span>

    ![Startseite von Blazor mit Zähler](https://user-images.githubusercontent.com/1874516/39509718-224483f6-4d9c-11e8-9030-b4c7228d669d.png)

## <a name="component-parameters"></a><span data-ttu-id="b2439-155">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="b2439-155">Component parameters</span></span>

<span data-ttu-id="b2439-156">Komponenten können auch Parameter haben, die mithilfe privater Eigenschaften für die Komponentenklasse definiert werden, die mit `[Parameter]` versehen ist.</span><span class="sxs-lookup"><span data-stu-id="b2439-156">Components can also have parameters, which are defined using private properties on the component class decorated with `[Parameter]`.</span></span> <span data-ttu-id="b2439-157">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="b2439-157">Use attributes to specify arguments for a component in markup.</span></span> 

1. <span data-ttu-id="b2439-158">Aktualisieren Sie die `Counter`-Komponente, sodass sie über einen `IncrementAmount`-Parameter mit dem Standardwert 1 verfügt.</span><span class="sxs-lookup"><span data-stu-id="b2439-158">Update the `Counter` component to have an `IncrementAmount` parameter that defaults to 1.</span></span>

    ```cshtml
    @functions {
        int currentCount = 0;

        [Parameter]
        private int IncrementAmount { get; set; } = 1;

        void IncrementCount()
        {
            currentCount += IncrementAmount;
        }
    }
    ```

    > [!NOTE]
    > <span data-ttu-id="b2439-159">In Visual Studio können Sie mit dem `para`-Codeausschnitt schnell einen Komponentenparameter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="b2439-159">From Visual Studio, you can quickly add a component parameter by using the `para` snippet.</span></span> <span data-ttu-id="b2439-160">Geben Sie `para` ein, und drücken Sie dann zweimal die `Tab`-Taste.</span><span class="sxs-lookup"><span data-stu-id="b2439-160">Type `para` and then press the `Tab` key twice.</span></span>

1. <span data-ttu-id="b2439-161">Ändern Sie auf der Startseite (*Index.cshtml*) den Wert des Inkrements für `Counter` in 10, indem Sie ein Attribut festlegen, das mit dem Namen der Eigenschaft der Komponente für `IncrementCount` übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="b2439-161">On the Home page (*Index.cshtml*), change the increment amount for the `Counter` to 10 by setting an attribute that matches the name of the component's property for `IncrementCount`.</span></span>

    ```cshtml
    <Counter IncrementAmount="10" />
    ```

1. <span data-ttu-id="b2439-162">Laden Sie die Seite neu.</span><span class="sxs-lookup"><span data-stu-id="b2439-162">Reload the page.</span></span>

    <span data-ttu-id="b2439-163">Der Zähler auf der Startseite wird jetzt um 10 heraufgesetzt, während der Zähler auf der Counter-Seite immer noch um 1 heraufgesetzt wird.</span><span class="sxs-lookup"><span data-stu-id="b2439-163">The counter on the Home page now increments by 10, while the counter on the Counter page still increments by 1.</span></span>

    ![Blazor-Inkrement von 10](https://user-images.githubusercontent.com/1874516/39509798-618f0720-4d9c-11e8-9125-3d4c634dff46.png)

## <a name="route-to-components"></a><span data-ttu-id="b2439-165">Weiterleiten an Komponenten</span><span class="sxs-lookup"><span data-stu-id="b2439-165">Route to components</span></span>

<span data-ttu-id="b2439-166">Die `@page`-Anweisung im oberen Teil der *Counter.cshtml*-Datei gibt an, dass diese Komponente eine Seite ist, an die Anforderungen weitergeleitet werden können.</span><span class="sxs-lookup"><span data-stu-id="b2439-166">The `@page` directive at the top of the *Counter.cshtml* file specifies that this component is a page to which requests can be routed.</span></span> <span data-ttu-id="b2439-167">Insbesondere die `Counter`-Komponente behandelt an `/Counter` gesendete Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="b2439-167">Specifically, the `Counter` component handles requests sent to `/Counter`.</span></span> <span data-ttu-id="b2439-168">Ohne die `@page`würde die Komponente keine weitergeleiteten Anforderungen behandeln, aber die Komponente könnte immer noch von anderen Komponenten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b2439-168">Without the `@page` directive, the component wouldn't handle routed requests, but the component could still be used by other components.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="b2439-169">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="b2439-169">Dependency injection</span></span>

<span data-ttu-id="b2439-170">Beim Dienstanbieter der App registrierte Dienste stehen für Komponenten über [Abhängigkeitsinjektion (DI, Dependency Injection)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="b2439-170">Services registered with the app's service provider are available to components via [dependency injection (DI)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection).</span></span> <span data-ttu-id="b2439-171">Dienste können mit der `@inject`-Anweisung in eine Komponente eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="b2439-171">Services can be injected into a component using the `@inject` directive.</span></span>

<span data-ttu-id="b2439-172">Sehen Sie sich die Implementierung der `FetchData`-Komponente in der *FetchData.cshtml*-Datei an.</span><span class="sxs-lookup"><span data-stu-id="b2439-172">Take a look at the implementation of the `FetchData` component in *FetchData.cshtml*.</span></span> <span data-ttu-id="b2439-173">Mit der `@inject`-Anweisung wird eine [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient)-Instanz in die Komponente eingefügt.</span><span class="sxs-lookup"><span data-stu-id="b2439-173">The `@inject` directive is used to inject an [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) instance into the component.</span></span>

```cshtml
@page "/fetchdata"
@inject HttpClient Http
```

<span data-ttu-id="b2439-174">Die `FetchData`-Komponente verwendet den eingefügten `HttpClient`, um JSON-Daten vom Server abzurufen, wenn die Komponente initialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="b2439-174">The `FetchData` component uses the injected `HttpClient` to retrieve JSON data from the server when the component is initialized.</span></span> <span data-ttu-id="b2439-175">Im Hintergrund wird der durch die Blazor-Runtime bereitgestellte `HttpClient` mit JavaScript-Interop zum Aufrufen der Fetch-API des zugrunde liegenden Browsers zum Senden der Anforderung implementiert (von C# aus kann jede JavaScript-Bibliothek oder Browser-API aufgerufen werden).</span><span class="sxs-lookup"><span data-stu-id="b2439-175">Under the covers, the `HttpClient` provided by the Blazor runtime is implemented using JavaScript interop to call the underlying browser's Fetch API to send the request (from C#, it's possible to call any JavaScript library or browser API).</span></span> <span data-ttu-id="b2439-176">Die abgerufenen Daten werden in die `forecasts`-C#-Variable als Array von `WeatherForecast`-Objekten deserialisiert.</span><span class="sxs-lookup"><span data-stu-id="b2439-176">The retrieved data is deserialized into the `forecasts` C# variable as an array of `WeatherForecast` objects.</span></span>

```cshtml
@functions {
    WeatherForecast[] forecasts;

    protected override async Task OnInitAsync()
    {
        forecasts = await Http.GetJsonAsync<WeatherForecast[]>("/sample-data/weather.json");
    }

    class WeatherForecast
    {
        public DateTime Date { get; set; }
        public int TemperatureC { get; set; }
        public int TemperatureF { get; set; }
        public string Summary { get; set; }
    }
}
```

<span data-ttu-id="b2439-177">Eine `@foreach`-Schleife rendert jede Vorhersageinstanz als eine Zeile in die Wettertabelle.</span><span class="sxs-lookup"><span data-stu-id="b2439-177">A `@foreach` loop is used to render each forecast instance as a row in the weather table.</span></span>

```cshtml
<table class="table">
    <thead>
        <tr>
            <th>Date</th>
            <th>Temp. (C)</th>
            <th>Temp. (F)</th>
            <th>Summary</th>
        </tr>
    </thead>
    <tbody>
        @foreach (var forecast in forecasts)
        {
            <tr>
                <td>@forecast.Date.ToShortDateString()</td>
                <td>@forecast.TemperatureC</td>
                <td>@forecast.TemperatureF</td>
                <td>@forecast.Summary</td>
            </tr>
        }
    </tbody>
</table>
```

## <a name="build-a-todo-list"></a><span data-ttu-id="b2439-178">Erstellen einer Aufgabenliste</span><span class="sxs-lookup"><span data-stu-id="b2439-178">Build a todo list</span></span>

<span data-ttu-id="b2439-179">Fügen Sie der App eine neue Seite hinzu, die eine einfache Aufgabenliste implementiert.</span><span class="sxs-lookup"><span data-stu-id="b2439-179">Add a new page to the app that implements a simple todo list.</span></span>

1. <span data-ttu-id="b2439-180">Fügen Sie dem Ordner *Pages* eine leere Textdatei mit dem Namen *Todo.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="b2439-180">Add an empty text file to the *Pages* folder named *Todo.cshtml*.</span></span>

1. <span data-ttu-id="b2439-181">Geben Sie das ursprüngliche Markup für die Seite an.</span><span class="sxs-lookup"><span data-stu-id="b2439-181">Provide the initial markup for the page.</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>
    ```

1. <span data-ttu-id="b2439-182">Fügen Sie die Todo-Seite der Navigationsleiste hinzu, indem Sie *Shared/NavMenu.cshtml* aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="b2439-182">Add the Todo page to the navigation bar by updating *Shared/NavMenu.cshtml*.</span></span> <span data-ttu-id="b2439-183">Fügen Sie einen `NavLink` für die Todo-Seite hinzu, indem Sie das folgende Listenelementmarkup unterhalb der vorhandenen Listenelemente hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="b2439-183">Add a `NavLink` for the Todo page by adding the following list item markup below the existing list items.</span></span>

    ```cshtml
    <li class="nav-item px-3">
        <NavLink class="nav-link" href="todo">
            <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
        </NavLink>
    </li>
    ```

1. <span data-ttu-id="b2439-184">Aktualisieren Sie die App im Browser.</span><span class="sxs-lookup"><span data-stu-id="b2439-184">Refresh the app in the browser.</span></span> <span data-ttu-id="b2439-185">Die neue Todo-Seite wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b2439-185">See the new Todo page.</span></span>

    ![Blazor-Todo-Start](https://user-images.githubusercontent.com/1874516/39509907-bb27e77a-4d9c-11e8-91e7-ea1e7c01729e.png)

1. <span data-ttu-id="b2439-187">Fügen Sie eine *TodoItem.cs*-Datei dem Stamm des Projekts hinzu, die eine Klasse zum Darstellen der Aufgabenelemente enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="b2439-187">Add a *TodoItem.cs* file to the root of the project to hold a class to represent the todo items.</span></span>

1. <span data-ttu-id="b2439-188">Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="b2439-188">Use the following C# code for the `TodoItem` class.</span></span>

    ```csharp
    public class TodoItem
    {
        public string Title { get; set; }
        public bool IsDone { get; set; }
    }
    ```

1. <span data-ttu-id="b2439-189">Wechseln Sie zurück zu der `Todo`-Komponente in *Todo.cshtml*, und fügen Sie ein Feld für die Aufgabenelemente in einem `@functions`-Block hinzu.</span><span class="sxs-lookup"><span data-stu-id="b2439-189">Go back to the `Todo` component in *Todo.cshtml* and add a field for the todos in a `@functions` block.</span></span> <span data-ttu-id="b2439-190">Die `Todo`-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.</span><span class="sxs-lookup"><span data-stu-id="b2439-190">The `Todo` component uses this field to maintain the state of the todo list.</span></span>

    ```cshtml
    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
    }
    ```

1. <span data-ttu-id="b2439-191">Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern.</span><span class="sxs-lookup"><span data-stu-id="b2439-191">Add unordered list markup and a `foreach` loop to render each todo item as a list item.</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>
    ```

1. <span data-ttu-id="b2439-192">Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgaben hinzugefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="b2439-192">The app requires UI elements for adding todos to the list.</span></span> <span data-ttu-id="b2439-193">Fügen Sie eine Texteingabe und eine Schaltfläche unterhalb der Liste hinzu.</span><span class="sxs-lookup"><span data-stu-id="b2439-193">Add a text input and a button below the list.</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>

    <input placeholder="Something todo" />
    <button>Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
    }
    ```

1. <span data-ttu-id="b2439-194">Aktualisieren Sie den Browser.</span><span class="sxs-lookup"><span data-stu-id="b2439-194">Refresh the browser.</span></span>

    ![Schaltfläche „Add todo“](https://user-images.githubusercontent.com/1874516/39512402-bc88ab46-4da5-11e8-9e3f-87b875b56383.png)

    <span data-ttu-id="b2439-196">Bei Auswahl der Schaltfläche **Add todo** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="b2439-196">Nothing happens when the **Add todo** button is selected because no event handler is wired up to the button.</span></span>

1. <span data-ttu-id="b2439-197">Fügen Sie eine `AddTodo`-Methode der `Todo`-Komponente hinzu, und registrieren Sie sie mit dem `onclick`-Attribut für Schaltflächenklicks.</span><span class="sxs-lookup"><span data-stu-id="b2439-197">Add an `AddTodo` method to the `Todo` component and register it for button clicks using the `onclick` attribute.</span></span>

    ```cshtml
    <input placeholder="Something todo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();

        void AddTodo()
        {
            // Todo: Add the todo
        }
    }
    ```

    <span data-ttu-id="b2439-198">Die `AddTodo`-C#-Methode wird jedes Mal aufgerufen, wenn die Schaltfläche ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="b2439-198">The `AddTodo` C# method is called every time the button is selected.</span></span>

1. <span data-ttu-id="b2439-199">Um den Titel des neuen Aufgabenelements abzurufen, fügen Sie ein `newTodo`-Zeichenfolgenfeld hinzu, und binden Sie es mithilfe des `bind`-Attributs an den Wert der Texteingabe.</span><span class="sxs-lookup"><span data-stu-id="b2439-199">To get the title of the new todo item, add a `newTodo` string field and bind it to the value of the text input using the `bind` attribute.</span></span>

    ```csharp
    IList<TodoItem> todos = new List<TodoItem>();
    string newTodo;
    ```

    ```cshtml
    <input placeholder="Something todo" bind="@newTodo" />
    ```

1. <span data-ttu-id="b2439-200">Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b2439-200">Update the `AddTodo` method to add the `TodoItem` with the specified title to the list.</span></span> <span data-ttu-id="b2439-201">Vergessen Sie nicht, den Wert der Texteingabe zu löschen, indem Sie für `newTodo` eine leere Zeichenfolge festlegen.</span><span class="sxs-lookup"><span data-stu-id="b2439-201">Don't forget to clear the value of the text input by setting `newTodo` to an empty string.</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>@todo.Title</li>
        }
    </ul>

    <input placeholder="Something todo" bind="@newTodo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
        string newTodo;

        void AddTodo()
        {
            if (!string.IsNullOrWhiteSpace(newTodo))
            {
                todos.Add(new TodoItem { Title = newTodo });
                newTodo = string.Empty;
            }
        }
    }
    ```

1. <span data-ttu-id="b2439-202">Aktualisieren Sie den Browser.</span><span class="sxs-lookup"><span data-stu-id="b2439-202">Refresh the browser.</span></span> <span data-ttu-id="b2439-203">Fügen Sie einige Aufgaben der Aufgabenliste hinzu.</span><span class="sxs-lookup"><span data-stu-id="b2439-203">Add some todos to the todo list.</span></span>

    ![Aufgaben hinzufügen](https://user-images.githubusercontent.com/1874516/39512531-2d2ff62e-4da6-11e8-8b83-291b0efc821b.png)

1. <span data-ttu-id="b2439-205">Was ist eine Aufgabenliste eigentlich ohne Kontrollkästchen?</span><span class="sxs-lookup"><span data-stu-id="b2439-205">Lastly, what's a todo list without check boxes?</span></span> <span data-ttu-id="b2439-206">Der Titeltext für die einzelnen Aufgabenelemente kann auch bearbeitbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="b2439-206">The title text for each todo item can be made editable as well.</span></span> <span data-ttu-id="b2439-207">Fügen Sie eine Kontrollkästcheneingabe und eine Texteingabe für jedes Aufgabenelement hinzu, und binden Sie ihre Werte jeweils an die Eigenschaften `Title` und `IsDone`.</span><span class="sxs-lookup"><span data-stu-id="b2439-207">Add a check box input and text input for each todo item and bind their values to the `Title` and `IsDone` properties, respectively.</span></span>

    ```cshtml
    <ul>
        @foreach (var todo in todos)
        {
            <li>
                <input type="checkbox" bind="@todo.IsDone" />
                <input bind="@todo.Title" />
            </li>
        }
    </ul>
    ```

1. <span data-ttu-id="b2439-208">Um sicherzustellen, dass diese Werte gebunden sind, aktualisieren Sie den `h1`-Header, um die Anzahl der noch nicht erledigten Aufgabenelemente anzuzeigen (`IsDone` ist `false`).</span><span class="sxs-lookup"><span data-stu-id="b2439-208">To verify that these values are bound, update the `h1` header to show a count of the number of todo items that are not yet done (`IsDone` is `false`).</span></span>

    ```cshtml
    <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
    ```

1. <span data-ttu-id="b2439-209">Die abgeschlossene `Todo`-Komponente sollte wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="b2439-209">The completed `Todo` component should look like this:</span></span>

    ```cshtml
    @page "/todo"

    <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>

    <ul>
        @foreach (var todo in todos)
        {
            <li>
                <input type="checkbox" bind="@todo.IsDone" />
                <input bind="@todo.Title" />
            </li>
        }
    </ul>

    <input placeholder="Something todo" bind="@newTodo" />
    <button onclick="@AddTodo">Add todo</button>

    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
        string newTodo;

        void AddTodo()
        {
            if (!string.IsNullOrWhiteSpace(newTodo))
            {
                todos.Add(new TodoItem { Title = newTodo });
                newTodo = string.Empty;
            }
        }
    }
    ```

<span data-ttu-id="b2439-210">Aktualisieren Sie die App im Browser.</span><span class="sxs-lookup"><span data-stu-id="b2439-210">Refresh the app in the browser.</span></span> <span data-ttu-id="b2439-211">Versuchen Sie, einige Aufgabenelemente hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b2439-211">Try adding some todo items.</span></span>

![Fertige Blazor-Aufgabenliste](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

## <a name="publish-and-deploy"></a><span data-ttu-id="b2439-213">Veröffentlichen und Bereitstellen</span><span class="sxs-lookup"><span data-stu-id="b2439-213">Publish and deploy</span></span>

<span data-ttu-id="b2439-214">Wenn Sie Visual Studio verwenden, führen Sie die folgenden Schritte aus, um die Todo Blazor-App in Azure zu veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="b2439-214">When using Visual Studio, perform the following steps to publish the Todo Blazor app to Azure:</span></span>

1. <span data-ttu-id="b2439-215">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="b2439-215">Right-click on the project in **Solution Explorer** and select **Publish**.</span></span>

1. <span data-ttu-id="b2439-216">Wählen Sie im Dialogfeld **Veröffentlichungsziel auswählen** die Optionen **App Service** und **Neu erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b2439-216">In the **Pick a publish target** dialog, select **App Service** and **Create New**.</span></span> <span data-ttu-id="b2439-217">Wählen Sie **Veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="b2439-217">Select **Publish**.</span></span>

    ![Veröffentlichungsziel auswählen](build-your-first-blazor-app/_static/blazor-publish-pick-target.png)

1. <span data-ttu-id="b2439-219">Wählen Sie im Dialogfeld **App Service erstellen** einen Namen für die App sowie Abonnement, Ressourcengruppe und Hostingplan aus.</span><span class="sxs-lookup"><span data-stu-id="b2439-219">In the **Create App Service** dialog, choose a name for the app and select the subscription, resource group, and hosting plan.</span></span> <span data-ttu-id="b2439-220">Wählen Sie **Erstellen** aus, um den App Service zu erstellen und die App zu veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="b2439-220">Select **Create** to create the app service and publish the app.</span></span>

    ![App Service erstellen](build-your-first-blazor-app/_static/blazor-publish-create-appservice2.png)

<span data-ttu-id="b2439-222">Warten Sie etwa eine Minute, bis die App bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="b2439-222">Wait a minute or so for the app to be deployed.</span></span>

<span data-ttu-id="b2439-223">Die App sollte nun in Azure ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b2439-223">The app should now be running in Azure.</span></span> <span data-ttu-id="b2439-224">Markieren Sie das Aufgabenelement zum Erstellen Ihrer ersten Blazor-App als *erledigt*.</span><span class="sxs-lookup"><span data-stu-id="b2439-224">Mark the todo item to build your first Blazor app as *done*.</span></span> <span data-ttu-id="b2439-225">Gute Arbeit!</span><span class="sxs-lookup"><span data-stu-id="b2439-225">Nice job!</span></span>

![Blazor in Azure](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

> [!NOTE]
> <span data-ttu-id="b2439-227">Wenn Sie nicht Visual Studio verwenden, veröffentlichen Sie die Blazor-App an einer Eingabeaufforderung unter Windows, macOS oder Linux:</span><span class="sxs-lookup"><span data-stu-id="b2439-227">If not using Visual Studio, publish the Blazor app at a command prompt on Windows, macOS, or Linux:</span></span>
>
> ```console
> dotnet publish -c Release
> ```
>
> <span data-ttu-id="b2439-228">Die Bereitstellung wird im Ordner */bin/Release/\<Zielframework>/publish* erstellt.</span><span class="sxs-lookup"><span data-stu-id="b2439-228">The deployment is created in the */bin/Release/\<target-framework>/publish* folder.</span></span> <span data-ttu-id="b2439-229">Verschieben Sie die Inhalte des Ordners *publish* auf den Server oder den Hostingdienst.</span><span class="sxs-lookup"><span data-stu-id="b2439-229">Move the contents of the *publish* folder to the server or hosting service.</span></span>
>
> <span data-ttu-id="b2439-230">Weitere Informationen finden Sie unter dem Thema [Veröffentlichen der App](xref:host-and-deploy/razor-components/index#publish-the-app).</span><span class="sxs-lookup"><span data-stu-id="b2439-230">For more information, see the [Host and deploy](xref:host-and-deploy/razor-components/index#publish-the-app) topic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b2439-231">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b2439-231">Additional resources</span></span>

<span data-ttu-id="b2439-232">Ein Beispiel für eine anspruchsvollere Blazor-App ist das [Flight Finder-Beispiel](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor) auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="b2439-232">For a more involved Blazor sample app, check out the [Flight Finder sample](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor) on GitHub.</span></span>

![Blazor Flight Finder](https://msdnshared.blob.core.windows.net/media/2018/03/blazor-flight-finder.png)
