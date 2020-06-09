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
# <a name="build-your-first-blazor-app"></a>'Blazor'

'Identity'

'Let's Encrypt' 'Razor'

> [!div class="checklist"]
> * 'SignalR' uid: tutorials/first-blazor-app
> * Erstellen Ihrer ersten Blazor-App
> * Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)
> * In diesem Tutorial erfahren Sie, wie Sie eine Blazor-App erstellen und ändern.

Sie lernen Folgendes:

## <a name="build-components"></a>Erstellen eines Aufgabenlisten-Blazor-App-Projekts

1. Ändern von Razor-Komponenten Verwenden von Ereignisbehandlung und Datenbindung in Komponenten

1. Verwenden von Abhängigkeitsinjektion (DI, Dependency Injection) und Routing in einer Blazor-App Am Ende dieses Tutorials verfügen Sie über eine funktionierende Chat-App.

1. Erstellen von Komponenten Befolgen Sie die Anweisungen im Artikel <xref:blazor/get-started>, um ein Blazor-Projekt für dieses Tutorial zu erstellen. Name der *ToDoList* für das Projekt.

1. Navigieren Sie zu jeder der drei Seiten der App im Ordner *Pages*: „Home“, „Counter“ und „Fetch data“.

   Diese Seiten werden durch die Razor-Komponentendateien *Index.razor*, *Counter.razor* und *FetchData.razor* implementiert.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter1.razor)]

   Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen. Für das Heraufsetzen eines Zählers auf einer Webseite müssen Sie JavaScript-Code schreiben. Mit Blazor können Sie stattdessen auch C#-Code schreiben. Sehen Sie sich die Implementierung der `Counter`-Komponente in der *Counter.razor*-Datei an.

   *Pages/Counter.razor*: Die Benutzeroberfläche der `Counter`-Komponente wird mithilfe von HTML definiert. Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt.

   Das HTML-Markup und die C#-Renderinglogik werden zur Erstellungszeit in eine Komponentenklasse konvertiert.

   * Der Name der generierten .NET-Klasse stimmt mit dem Namen der Datei überein.
   * Member der Komponentenklasse werden in einem `@code`-Block definiert.
   * Im `@code`-Block werden der Komponentenstatus (Eigenschaften, Felder) und Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben.
   * Diese Member werden dann als Teil der Renderinglogik der Komponente und zur Behandlung von Ereignissen verwendet. Wenn die **Hier klicken**-Schaltfläche ausgewählt wird:

1. Der registrierte `onclick`-Handler der `Counter`-Komponente wird aufgerufen (die `IncrementCount`-Methode).

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter2.razor?highlight=14)]

1. Die `Counter`-Komponente generiert ihre Renderstruktur neu. Die neue Renderstruktur wird mit der vorherigen verglichen. Es werden nur Änderungen des Dokumentobjektmodells (Document Object Model, DOM) angewendet.

## <a name="use-components"></a>Der angezeigte Zähler wird aktualisiert.

Ändern Sie die C#-Logik der `Counter`-Komponente, um das Zählerinkrement von eins auf zwei heraufzusetzen.

1. Erstellen Sie die App neu, und führen Sie sie aus, um die Änderungen anzuzeigen.

   Wählen Sie die Schaltfläche **Hier klicken** aus. Der Leistungsindikator wird um zwei erhöht. Verwenden von Komponenten

   Beziehen Sie eine Komponente mithilfe einer HTML-Syntax in eine andere Komponente ein.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index1.razor?highlight=7)]

1. Fügen Sie die `Index`-Komponente der `Counter`-Komponente der App durch Hinzufügen eines `<Counter />`-Elements zur `Index`-Komponente (*Index.razor*) hinzu. Wenn Sie für diese Oberfläche Blazor WebAssembly verwenden, verwendet die `Index`-Komponente eine `SurveyPrompt`-Komponente.

## <a name="component-parameters"></a>Ersetzen Sie das `<SurveyPrompt>`-Element durch ein `<Counter />`-Element.

Wenn Sie für diese Oberfläche eine Blazor Server-App verwenden, fügen Sie der `Index`-Komponente das `<Counter />`-Element hinzu: *Pages/Index.razor*: Erstellen Sie die App neu, und führen Sie sie aus.

1. Die `Index`-Komponente verfügt über einen eigenen Zähler.

   * Komponentenparameter
   * Komponenten können auch Parameter aufweisen.

   Komponentenparameter werden mithilfe öffentlicher Eigenschaften für die Komponentenklasse mit dem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attribut definiert.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Counter.razor?highlight=13,17)]

   <!-- Add back when supported.
       > [!NOTE]
       > From Visual Studio, you can quickly add a component parameter by using the `para` snippet. Type `para` and press the `Tab` key twice.
   -->

1. Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben. Ändern Sie den C#-Code `@code` der Komponente wie folgt:

   Fügen Sie eine öffentliche `IncrementAmount`-Eigenschaft mit dem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attribut hinzu.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Index2.razor?highlight=7)]

1. Ändern Sie die `IncrementCount`-Methode, sodass die Eigenschaft `IncrementAmount` verwendet wird, um den Wert von `currentCount` zu erhöhen. *Pages/Counter.razor*: Geben Sie unter Verwendung eines Attributs einen `IncrementAmount`-Parameter im `<Counter>`-Element der `Index`-Komponente an.

## <a name="route-to-components"></a>Legen Sie den Wert fest, um den Zähler um zehn heraufzusetzen.

*Pages/Index.razor*: Laden Sie die `Index`-Komponente neu. Der Zähler wird jedes Mal, wenn die Schaltfläche **Hier klicken** ausgewählt wird, um zehn heraufgesetzt.

## <a name="dependency-injection"></a>Der Zähler in der `Counter`-Komponente wird weiter um eins erhöht.

### <a name="blazor-server-experience"></a>Weiterleiten an Komponenten

Die `@page`-Anweisung im oberen Teil der *Counter.razor*-Datei gibt an, dass die `Counter`-Komponente ein Routingendpunkt ist. Die `Counter`-Komponente verarbeitet Anforderungen, die an `/counter` gesendet werden.

[!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/Startup.cs?highlight=5)]

Ohne die `@page`-Anweisung behandelt eine Komponente keine weitergeleiteten Anforderungen, aber die Komponente kann immer noch von anderen Komponenten verwendet werden.

Dependency Injection

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1.razor?highlight=3)]

Blazor Server-Benutzererfahrung

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData2.razor?highlight=6)]

### <a name="blazor-webassembly-experience"></a>Wenn Sie mit einer Blazor Server-App arbeiten, wird der `WeatherForecastService`-Dienst in `Startup.ConfigureServices` als [Singleton](xref:fundamentals/dependency-injection#service-lifetimes) registriert.

Eine Instanz des Diensts steht überall in der App mittels [Abhängigkeitsinjektion (Dependency Injection, DI)](xref:fundamentals/dependency-injection) zur Verfügung:

Mit der [`@inject`](xref:mvc/views/razor#inject)-Anweisung wird die Instanz des `WeatherForecastService`-Diensts in die `FetchData`-Komponente eingefügt.

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData1_client.razor?highlight=7-9)]

*Pages/FetchData.razor*:

[!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/FetchData3.razor?highlight=11-19)]

## <a name="build-a-todo-list"></a>Die `FetchData`-Komponente verwendet den eingefügten Dienst wie `ForecastService`, um ein Array aus `WeatherForecast`-Objekten abzurufen:

Blazor WebAssembly-Benutzererfahrung

1. Wenn Sie eine Blazor WebAssembly-App verwenden, wird <xref:System.Net.Http.HttpClient> eingefügt, um Wettervorhersagedaten aus der Datei *weather.json* im Ordner *wwwroot/sample-data* abzurufen. *Pages/FetchData.razor*: Eine [`@foreach`](/dotnet/csharp/language-reference/keywords/foreach-in)-Schleife rendert jede Vorhersageinstanz als eine Zeile in der Wetterdatentabelle: Erstellen einer Aufgabenliste

1. Fügen Sie der App eine neue Komponente hinzu, die eine einfache Aufgabenliste implementiert.

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

1. Fügen Sie der App im Ordner *Seiten* eine neue `Todo` Razor-Komponente hinzu.

   Wenn Sie Visual Studio verwenden, klicken Sie mit der rechten Maustaste auf den Ordner **Seiten** und dann auf **Hinzufügen** > **Neues Element** >  **Razor-Komponente**. Nennen Sie die Datei der Komponente *Todo.razor*.

   Fügen Sie dem Ordner **Seiten** in anderen Entwicklungsumgebungen eine leere Datei namens *Todo.razor* hinzu.

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

1. Geben Sie das ursprüngliche Markup für die Komponente an: Fügen Sie der Navigationsleiste die `Todo`-Komponente hinzu.

1. Die `NavMenu`-Komponente (*Shared/NavMenu.razor*) wird im Layout der App verwendet. Layouts sind Komponenten, mit denen Sie die Duplizierung des Inhalts in der App vermeiden können.

   [!code-csharp[](build-your-first-blazor-app/samples_snapshot/3.x/TodoItem.cs)]

1. Fügen Sie einen `Todo` für die `<NavLink>`-Komponente hinzu, indem Sie das folgende Listenelementmarkup unterhalb der vorhandenen Listenelemente in der *Datei Shared/NavMenu.razor* hinzufügen:

   * Erstellen Sie die App neu, und führen Sie sie aus. Besuchen Sie die neue Todo-Seite, um sicherzustellen, dass der Link zur `Todo`-Komponente funktioniert.
   * Fügen Sie eine *TodoItem.cs*-Datei dem Stamm des Projekts hinzu, die eine Klasse zum Darstellen eines Aufgabenelements enthalten soll.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo4.razor?highlight=5-10,12-14)]

1. Verwenden Sie den folgenden C#-Code für die `TodoItem`-Klasse: Kehren Sie zur `Todo`-Komponente (*Pages/Todo.razor*) zurück:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo5.razor?highlight=12-13)]

1. Fügen Sie ein Feld für die Aufgabenelemente in einem `@code`-Block hinzu. Die `Todo`-Komponente verwaltet mit diesem Feld den Status der Aufgabenliste.

1. Fügen Sie ein Markup für eine unsortierte Liste und eine `foreach`-Schleife hinzu, um jedes Aufgabenelement als Listenelement zu rendern (`<li>`). Die App benötigt Benutzeroberflächenelemente, damit der Liste Aufgabenelemente hinzugefügt werden können.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo6.razor?highlight=2,7-10)]

1. Fügen Sie eine Texteingabe (`<input>`) und eine Schaltfläche (`<button>`) unterhalb der Liste (`<ul>...</ul>`) hinzu:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo7.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. Erstellen Sie die App neu, und führen Sie sie aus. Bei Auswahl der Schaltfläche **Todo-Element hinzufügen** geschieht nichts, da kein Ereignishandler mit der Schaltfläche verknüpft ist.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo8.razor?highlight=19-26)]

1. Fügen Sie eine `AddTodo`-Methode zur `Todo`-Komponente hinzu, und registrieren Sie sie mit dem `@onclick`-Attribut für eine Schaltflächenauswahl. Die C#-Methode `AddTodo` wird aufgerufen, wenn die Schaltfläche ausgewählt wird:

1. Um den Titel des neuen Aufgabenelements abzurufen, fügen Sie oben im `@code`-Block ein `newTodo`-Zeichenfolgenfeld hinzu, und binden Sie es mithilfe des `bind`-Attributs im `<input>`-Element an den Wert der Texteingabe: Aktualisieren Sie die `AddTodo`-Methode, um das `TodoItem` mit dem angegebenen Titel der Liste hinzuzufügen. Löschen Sie den Wert der Texteingabe, indem Sie für `newTodo` eine leere Zeichenfolge festlegen:

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/ToDo9.razor?highlight=5-6)]

1. Erstellen Sie die App neu, und führen Sie sie aus.

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. Fügen Sie der Aufgabenliste einige Aufgabenelemente hinzu, um den neuen Code zu testen.

   [!code-razor[](build-your-first-blazor-app/samples_snapshot/3.x/Todo.razor)]

1. Der Titeltext für die einzelnen Aufgabenelemente kann als bearbeitbar festgelegt werden, und ein Kontrollkästchen kann dem Benutzer helfen, die erledigten Elemente nachzuverfolgen. Fügen Sie für jedes Aufgabenelement eine Kontrollkästcheneingabe hinzu, und binden Sie ihren Wert an die `IsDone`-Eigenschaft.

## <a name="next-steps"></a>Ändern Sie `@todo.Title` in ein `<input>`-Element, das an `@todo.Title` gebunden ist:

Um sicherzustellen, dass diese Werte gebunden sind, aktualisieren Sie den `<h3>`-Header, um die Anzahl der noch nicht erledigten Aufgabenelemente anzuzeigen (`IsDone` ist `false`).

> [!div class="checklist"]
> * Die abgeschlossene `Todo`-Komponente (*Pages/Todo.razor*):
> * Erstellen Sie die App neu, und führen Sie sie aus.
> * Fügen Sie Aufgabenelemente hinzu, um den neuen Code zu testen.
> * Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="nextstepaction"]
> <xref:blazor/components>
