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
# <a name="build-your-first-blazor-app"></a>Erstellen Ihrer ersten Blazor-App

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

In diesem Tutorial erstellen Sie schrittweise eine Blazor-App und lernen schnell die grundlegenden Features des Razor Components-Frameworks.

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/build-your-first-blazor-app/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)). Informieren Sie sich in dem Thema [Erste Schritte](xref:razor-components/get-started) über die Voraussetzungen.

So erstellen Sie das Projekt in Visual Studio:

1. Wählen Sie **Datei** > **Neu** > **Projekt** aus. Wählen Sie **Web** > **ASP.NET Core-Webanwendung** aus. Benennen Sie das Projekt im Feld **Name** mit „BlazorApp1“. Klicken Sie auf **OK**.

    ![Neues ASP.NET Core-Projekt](build-your-first-blazor-app/_static/new-aspnet-core-project.png)

1. Das Dialogfeld **Neue ASP.NET Core-Webanwendung** wird angezeigt. Stellen Sie sicher, dass **.NET Core** oben ausgewählt ist. Wählen Sie **ASP.NET Core 2.1** aus. Wählen Sie die **Blazor**-Vorlage und dann **OK** aus.

    ![Dialogfeld „Neue Blazor-App“](build-your-first-blazor-app/_static/new-blazor-app-dialog.png)

1. Sobald das Projekt erstellt ist, drücken Sie **STRG+F5** zum Ausführen der App *ohne Debugger*. Die Ausführung mit dem Debugger (**F5**) wird zu diesem Zeitpunkt nicht unterstützt.

> [!NOTE]
> Wenn Sie nicht Visual Studio verwenden, erstellen Sie die Blazor-App an einer Eingabeaufforderung unter Windows, macOS oder Linux:
>
> ```console
> dotnet new --install "Microsoft.AspNetCore.Blazor.Templates"
> dotnet new blazor -o BlazorApp1
> cd BlazorApp1
> dotnet run
> ```
>
> Navigieren Sie mit der Localhostadresse und dem Port, die nach Ausführung von `dotnet run` im Konsolenfenster ausgegeben werden, zu der App. Fahren Sie die App mit **STRG+C** im Konsolenfenster herunter.

Die Blazor-App wird im Browser ausgeführt:

![Blazor-App-Startseite](https://user-images.githubusercontent.com/1874516/39509497-5515c3ea-4d9b-11e8-887f-019ea4fdb3ee.png)

## <a name="build-components"></a>Erstellen von Komponenten

1. Navigieren Sie zu jeder der drei Seiten der App: „Home“, „Counter“ und „Fetch data“.

    Diese drei Seiten werden durch die drei Razor-Dateien im *Pages*-Ordner implementiert: *Index.cshtml*, *Counter.cshtml* und *FetchData.cshtml*. Jede dieser Dateien implementiert eine Komponente, die auf der Clientseite im Browser kompiliert und ausgeführt wird.

1. Wählen Sie die Schaltfläche auf der Counter-Seite aus.

    ![Blazor-App-Startseite](https://user-images.githubusercontent.com/1874516/39509525-6e367c66-4d9b-11e8-9978-e52a9750c34b.png)

    Jedes Mal, wenn die Schaltfläche ausgewählt wird, wird der Zähler ohne Seitenaktualisierung heraufgesetzt. Normalerweise wird diese Art von clientseitigem Verhalten in JavaScript behandelt; hier ist es jedoch mit der `Counter`-Komponente in C# und .NET implementiert.

1. Sehen Sie sich die Implementierung der `Counter`-Komponente in der *Counter.cshtml*-Datei an:

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

    Die Benutzeroberfläche für die `Counter`-Komponente ist im normalen HTML-Format definiert. Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](https://docs.microsoft.com/aspnet/core/mvc/views/razor) hinzugefügt. Das HTML-Markup und die C#-Renderinglogik werden zur Erstellungszeit in eine Komponentenklasse konvertiert. Der Name der generierten .NET-Klasse stimmt mit dem Namen der Datei überein.

    Member der Komponentenklasse werden in einem `@functions`-Block definiert. Im `@functions`-Block können der Komponentenstatus (Eigenschaften, Felder) und Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben werden. Diese Member können dann als Teil der Renderinglogik der Komponente und zur Behandlung von Ereignissen verwendet werden.

    Wenn die Schaltfläche ausgewählt wird, wird der registrierte `onclick`-Handler der `Counter`-Komponente aufgerufen (die `IncrementCount`-Methode) und die `Counter`-Komponente generiert ihre Renderstruktur neu. Blazor vergleicht die neue Renderstruktur mit der vorherigen und wendet alle Änderungen auf das Browser-Dokumentobjektmodell (Document Object Model, DOM) an. Der angezeigte Zähler wird aktualisiert.

1. Aktualisieren Sie das Markup für die `Counter`-Komponente, damit der Header der obersten Ebene *interessanter* wird.

    ```cshtml
    @page "/counter"
    <h1><em>Counter!!</em></h1>
    ```

1. Ändern Sie auch die C#-Logik der `Counter`-Komponente, um das Zählerinkrement von eins auf zwei heraufzusetzen.

    ```cshtml
    @functions {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount += 2;
        }
    }
    ```

1. Aktualisieren Sie die Counter-Seite im Browser, um die Änderungen anzuzeigen.

    ![Interessanter Zähler](https://user-images.githubusercontent.com/1874516/39509668-e8949a92-4d9b-11e8-91e9-b6a494695d92.png)

## <a name="use-components"></a>Verwenden von Komponenten

Sobald eine Komponente definiert ist, kann sie zum Implementieren anderer Komponenten verwendet werden. Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.

1. Fügen Sie eine `Counter`-Komponente der Startseite der App (*Index.cshtml*) hinzu.

    ```cshtml
    @page "/"

    <h1>Hello, world!</h1>

    Welcome to your new app.

    <SurveyPrompt Title="How is Blazor working for you?" />

    <Counter />
    ```

1. Aktualisieren Sie die Startseite im Browser. Beachten Sie die separate Instanz der `Counter`-Komponente auf der Startseite.

    ![Startseite von Blazor mit Zähler](https://user-images.githubusercontent.com/1874516/39509718-224483f6-4d9c-11e8-9030-b4c7228d669d.png)

## <a name="component-parameters"></a>Komponentenparameter

Komponenten können auch Parameter haben, die mithilfe privater Eigenschaften für die Komponentenklasse definiert werden, die mit `[Parameter]` versehen ist. Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben. 

1. Aktualisieren Sie die `Counter`-Komponente, sodass sie über einen `IncrementAmount`-Parameter mit dem Standardwert 1 verfügt.

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
    > In Visual Studio können Sie mit dem `para`-Codeausschnitt schnell einen Komponentenparameter hinzufügen. Geben Sie `para` ein, und drücken Sie dann zweimal die `Tab`-Taste.

1. Ändern Sie auf der Startseite (*Index.cshtml*) den Wert des Inkrements für `Counter` in 10, indem Sie ein Attribut festlegen, das mit dem Namen der Eigenschaft der Komponente für `IncrementCount` übereinstimmt.

    ```cshtml
    <Counter IncrementAmount="10" />
    ```

1. Laden Sie die Seite neu.

    Der Zähler auf der Startseite wird jetzt um 10 heraufgesetzt, während der Zähler auf der Counter-Seite immer noch um 1 heraufgesetzt wird.

    ![Blazor-Inkrement von 10](https://user-images.githubusercontent.com/1874516/39509798-618f0720-4d9c-11e8-9125-3d4c634dff46.png)

## <a name="route-to-components"></a>Weiterleiten an Komponenten

Die `@page`-Anweisung im oberen Teil der *Counter.cshtml*-Datei gibt an, dass diese Komponente eine Seite ist, an die Anforderungen weitergeleitet werden können. Insbesondere die `Counter`-Komponente behandelt an `/Counter` gesendete Anforderungen. Ohne die `@page`würde die Komponente keine weitergeleiteten Anforderungen behandeln, aber die Komponente könnte immer noch von anderen Komponenten verwendet werden.

## <a name="dependency-injection"></a>Dependency Injection

Beim Dienstanbieter der App registrierte Dienste stehen für Komponenten über [Abhängigkeitsinjektion (DI, Dependency Injection)](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) zur Verfügung. Dienste können mit der `@inject`-Anweisung in eine Komponente eingefügt werden.

Sehen Sie sich die Implementierung der `FetchData`-Komponente in der *FetchData.cshtml*-Datei an. Mit der `@inject`-Anweisung wird eine [HttpClient](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient)-Instanz in die Komponente eingefügt.

```cshtml
@page "/fetchdata"
@inject HttpClient Http
```

Die `FetchData`-Komponente verwendet den eingefügten `HttpClient`, um JSON-Daten vom Server abzurufen, wenn die Komponente initialisiert wird. Im Hintergrund wird der durch die Blazor-Runtime bereitgestellte `HttpClient` mit JavaScript-Interop zum Aufrufen der Fetch-API des zugrunde liegenden Browsers zum Senden der Anforderung implementiert (von C# aus kann jede JavaScript-Bibliothek oder Browser-API aufgerufen werden). Die abgerufenen Daten werden in die `forecasts`-C#-Variable als Array von `WeatherForecast`-Objekten deserialisiert.

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

Eine `@foreach`-Schleife rendert jede Vorhersageinstanz als eine Zeile in die Wettertabelle.

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

## <a name="build-a-todo-list"></a>Erstellen einer Aufgabenliste

Fügen Sie der App eine neue Seite hinzu, die eine einfache Aufgabenliste implementiert.

1. Fügen Sie dem Ordner *Pages* eine leere Textdatei mit dem Namen *Todo.cshtml* hinzu.

1. Geben Sie das ursprüngliche Markup für die Seite an.

    ```cshtml
    @page "/todo"

    <h1>Todo</h1>
    ```

1. Fügen Sie die Todo-Seite der Navigationsleiste hinzu, indem Sie *Shared/NavMenu.cshtml* aktualisieren. Fügen Sie einen `NavLink` für die Todo-Seite hinzu, indem Sie das folgende Listenelementmarkup unterhalb der vorhandenen Listenelemente hinzufügen.

    ```cshtml
    <li class="nav-item px-3">
        <NavLink class="nav-link" href="todo">
            <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
        </NavLink>
    </li>
    ```

1. Aktualisieren Sie die App im Browser. Die neue Todo-Seite wird angezeigt.

    ![Blazor-Todo-Start](https://user-images.githubusercontent.com/1874516/39509907-bb27e77a-4d9c-11e8-91e7-ea1e7c01729e.png)

1. Fügen Sie eine *TodoItem.cs*-Datei dem Stamm des Projekts hinzu, die eine Klasse zum Darstellen der Aufgabenelemente enthalten soll.

1. Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse.

    ```csharp
    public class TodoItem
    {
        public string Title { get; set; }
        public bool IsDone { get; set; }
    }
    ```

1. Wechseln Sie zurück zu der `Todo`-Komponente in *Todo.cshtml*, und fügen Sie ein Feld für die Aufgabenelemente in einem `@functions`-Block hinzu. Die `Todo`-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.

    ```cshtml
    @functions {
        IList<TodoItem> todos = new List<TodoItem>();
    }
    ```

1. Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern.

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

1. Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgaben hinzugefügt werden können. Fügen Sie eine Texteingabe und eine Schaltfläche unterhalb der Liste hinzu.

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

1. Aktualisieren Sie den Browser.

    ![Schaltfläche „Add todo“](https://user-images.githubusercontent.com/1874516/39512402-bc88ab46-4da5-11e8-9e3f-87b875b56383.png)

    Bei Auswahl der Schaltfläche **Add todo** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.

1. Fügen Sie eine `AddTodo`-Methode der `Todo`-Komponente hinzu, und registrieren Sie sie mit dem `onclick`-Attribut für Schaltflächenklicks.

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

    Die `AddTodo`-C#-Methode wird jedes Mal aufgerufen, wenn die Schaltfläche ausgewählt wird.

1. Um den Titel des neuen Aufgabenelements abzurufen, fügen Sie ein `newTodo`-Zeichenfolgenfeld hinzu, und binden Sie es mithilfe des `bind`-Attributs an den Wert der Texteingabe.

    ```csharp
    IList<TodoItem> todos = new List<TodoItem>();
    string newTodo;
    ```

    ```cshtml
    <input placeholder="Something todo" bind="@newTodo" />
    ```

1. Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen. Vergessen Sie nicht, den Wert der Texteingabe zu löschen, indem Sie für `newTodo` eine leere Zeichenfolge festlegen.

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

1. Aktualisieren Sie den Browser. Fügen Sie einige Aufgaben der Aufgabenliste hinzu.

    ![Aufgaben hinzufügen](https://user-images.githubusercontent.com/1874516/39512531-2d2ff62e-4da6-11e8-8b83-291b0efc821b.png)

1. Was ist eine Aufgabenliste eigentlich ohne Kontrollkästchen? Der Titeltext für die einzelnen Aufgabenelemente kann auch bearbeitbar gemacht werden. Fügen Sie eine Kontrollkästcheneingabe und eine Texteingabe für jedes Aufgabenelement hinzu, und binden Sie ihre Werte jeweils an die Eigenschaften `Title` und `IsDone`.

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

1. Um sicherzustellen, dass diese Werte gebunden sind, aktualisieren Sie den `h1`-Header, um die Anzahl der noch nicht erledigten Aufgabenelemente anzuzeigen (`IsDone` ist `false`).

    ```cshtml
    <h1>Todo (@todos.Count(todo => !todo.IsDone))</h1>
    ```

1. Die abgeschlossene `Todo`-Komponente sollte wie folgt aussehen:

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

Aktualisieren Sie die App im Browser. Versuchen Sie, einige Aufgabenelemente hinzuzufügen.

![Fertige Blazor-Aufgabenliste](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

## <a name="publish-and-deploy"></a>Veröffentlichen und Bereitstellen

Wenn Sie Visual Studio verwenden, führen Sie die folgenden Schritte aus, um die Todo Blazor-App in Azure zu veröffentlichen:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

1. Wählen Sie im Dialogfeld **Veröffentlichungsziel auswählen** die Optionen **App Service** und **Neu erstellen** aus. Wählen Sie **Veröffentlichen**.

    ![Veröffentlichungsziel auswählen](build-your-first-blazor-app/_static/blazor-publish-pick-target.png)

1. Wählen Sie im Dialogfeld **App Service erstellen** einen Namen für die App sowie Abonnement, Ressourcengruppe und Hostingplan aus. Wählen Sie **Erstellen** aus, um den App Service zu erstellen und die App zu veröffentlichen.

    ![App Service erstellen](build-your-first-blazor-app/_static/blazor-publish-create-appservice2.png)

Warten Sie etwa eine Minute, bis die App bereitgestellt wird.

Die App sollte nun in Azure ausgeführt werden. Markieren Sie das Aufgabenelement zum Erstellen Ihrer ersten Blazor-App als *erledigt*. Gute Arbeit!

![Blazor in Azure](https://user-images.githubusercontent.com/1874516/39512621-83406508-4da6-11e8-8244-5bae2ac6b22d.png)

> [!NOTE]
> Wenn Sie nicht Visual Studio verwenden, veröffentlichen Sie die Blazor-App an einer Eingabeaufforderung unter Windows, macOS oder Linux:
>
> ```console
> dotnet publish -c Release
> ```
>
> Die Bereitstellung wird im Ordner */bin/Release/\<Zielframework>/publish* erstellt. Verschieben Sie die Inhalte des Ordners *publish* auf den Server oder den Hostingdienst.
>
> Weitere Informationen finden Sie unter dem Thema [Veröffentlichen der App](xref:host-and-deploy/razor-components/index#publish-the-app).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Ein Beispiel für eine anspruchsvollere Blazor-App ist das [Flight Finder-Beispiel](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/blazor) auf GitHub.

![Blazor Flight Finder](https://msdnshared.blob.core.windows.net/media/2018/03/blazor-flight-finder.png)
