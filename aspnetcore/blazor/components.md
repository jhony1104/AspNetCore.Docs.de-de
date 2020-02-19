---
title: Erstellen und Verwenden von ASP.net Core Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, einschließlich Informationen zum Binden an Daten, behandeln von Ereignissen und Verwalten von Komponenten Lebenszyklen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: f9b4eab29fafe8113528062f57d28dadd0f57577
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447099"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Erstellen und Verwenden von ASP.net Core Razor-Komponenten

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Blazor-apps werden mithilfe von- *Komponenten*erstellt. Eine Komponente ist ein eigenständiges Segment von Benutzeroberflächen (UI), z. b. eine Seite, ein Dialogfeld oder ein Formular. Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum reagieren auf Benutzeroberflächen Ereignisse erforderlich ist. Komponenten sind flexibel und leicht zu gestalten. Sie können in Projekten eingebettet, wieder verwendet und freigegeben werden.

## <a name="component-classes"></a>Komponenten Klassen

Komponenten werden in [Razor](xref:mvc/views/razor) -Komponenten*Dateien (Razor*) mithilfe einer Kombination aus C# -und-HTML-Markup implementiert. Eine Komponente in Blazor wird formal als *Razor-Komponente*bezeichnet.

Der Name einer Komponente muss mit einem Großbuchstaben beginnen. Beispielsweise ist *mycoolcomponent. Razor* gültig, und *mycoolcomponent. Razor* ist ungültig.

Die Benutzeroberfläche für eine Komponente wird mit HTML definiert. Dynamische Renderinglogik (z.B. Schleifen, Bedingungen, Ausdrücken) wird über eine eingebettete C#-Syntax mit dem Namen [Razor](xref:mvc/views/razor) hinzugefügt. Wenn eine APP kompiliert wird, werden das HTML- C# Markup und die Renderinglogik in eine Komponenten Klasse konvertiert. Der Name der generierten Klasse entspricht dem Namen der Datei.

Member der Komponentenklasse werden in einem `@code`-Block definiert. Im `@code` Block wird der Komponenten Zustand (Eigenschaften, Felder) mit Methoden für die Ereignis Behandlung oder zum Definieren anderer Komponenten Logik angegeben. Mehrere `@code`-Blöcke sind zulässig.

Komponentenmember können als Teil der Renderinglogik der Komponente mithilfe C# von Ausdrücken verwendet werden, die mit `@`beginnen. Beispielsweise wird ein C# Feld gerendert, indem `@` dem Feldnamen vorangestellt wird. Im folgenden Beispiel wird ausgewertet und gerendert:

* `_headingFontStyle` zum CSS-Eigenschafts Wert für `font-style`.
* `_headingText` auf den Inhalt des `<h1>` Elements.

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

Nachdem die Komponente anfänglich gerendert wurde, generiert die Komponente die Renderstruktur in Reaktion auf Ereignisse erneut. Blazor vergleicht dann die neue Rendering-Struktur mit der vorherigen und wendet alle Änderungen auf die Dokumentobjektmodell des Browsers (DOM) an.

Komponenten sind normale C# Klassen und können an beliebiger Stelle innerhalb eines Projekts platziert werden. Komponenten, die Webseiten entwickeln, befinden sich normalerweise im Ordner *pages* . Nicht-Seiten Komponenten werden häufig im frei *gegebenen* Ordner oder in einem benutzerdefinierten Ordner abgelegt, der dem Projekt hinzugefügt wird.

In der Regel wird der Namespace einer Komponente aus dem Stamm Namespace der APP und dem Speicherort (Ordner) der Komponente in der APP abgeleitet. Wenn der Stamm Namespace der APP `BlazorApp` ist und sich die `Counter` Komponente im Ordner " *pages* " befindet:

* Der Namespace der `Counter` Komponente ist `BlazorApp.Pages`.
* Der voll qualifizierte Typname der Komponente ist `BlazorApp.Pages.Counter`.

Weitere Informationen finden Sie im Abschnitt [Importieren von Komponenten](#import-components) .

Um einen benutzerdefinierten Ordner zu verwenden, fügen Sie den Namespace des benutzerdefinierten Ordners entweder der übergeordneten Komponente oder der *_Imports Razor* -Datei der APP hinzu. Der folgende Namespace stellt z. b. Komponenten in einem *Komponenten* Ordner zur Verfügung, wenn der Stamm Namespace der APP `BlazorApp`ist:

```razor
@using BlazorApp.Components
```

## <a name="tag-helpers-arent-used-in-components"></a>Taghilfsprogramme werden in Komponenten nicht verwendet.

[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) werden*in Razor-Komponenten (Razor* -Dateien) nicht unterstützt. Um taghilfsobjekte in Blazorbereitzustellen, erstellen Sie eine-Komponente mit der gleichen Funktionalität wie das taghilfsprogramm, und verwenden Sie stattdessen die-Komponente.

## <a name="use-components"></a>Verwenden von Komponenten

Komponenten können andere Komponenten enthalten, indem Sie Sie mithilfe der HTML-Element Syntax deklarieren. Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.

Bei der Attribut Bindung wird Groß-/Kleinschreibung Beispielsweise ist `@bind` gültig, und `@Bind` ist ungültig.

Das folgende Markup in *Index. Razor* rendert eine `HeadingComponent` Instanz:

```razor
<HeadingComponent />
```

*Komponenten/headingcomponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Wenn eine Komponente ein HTML-Element mit einem in Großbuchstaben bestehenden ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, die anzeigt, dass das Element einen unerwarteten Namen aufweist. Durch das Hinzufügen einer `@using`-Direktive für den Namespace der Komponente wird die Komponente zur Verfügung gestellt, wodurch die Warnung aufgelöst wird.

## <a name="routing"></a>Routing

Das Routing in Blazor erfolgt durch Bereitstellen einer Routen Vorlage für jede barrierefreie Komponente in der app.

Wenn eine Razor-Datei mit einer `@page`-Direktive kompiliert wird, erhält die generierte Klasse eine <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>, die die Routen Vorlage angibt. Zur Laufzeit sucht der Router nach Komponenten Klassen mit einem `RouteAttribute` und rendert, welche Komponente eine Routen Vorlage hat, die mit der angeforderten URL übereinstimmt.

```razor
@page "/ParentComponent"

...
```

Weitere Informationen finden Sie unter <xref:blazor/routing>.

## <a name="parameters"></a>Parameter

### <a name="route-parameters"></a>Routen Parameter

Komponenten können Routen Parameter von der Routen Vorlage empfangen, die in der `@page`-Direktive bereitgestellt wird. Der Router verwendet Routen Parameter, um die entsprechenden Komponenten Parameter aufzufüllen.

*Pages/RouteParameter. Razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Optionale Parameter werden nicht unterstützt, sodass im vorherigen Beispiel zwei `@page` Direktiven angewendet werden. Der erste ermöglicht die Navigation zur Komponente ohne einen-Parameter. Die zweite `@page`-Direktive empfängt den `{text}` Route-Parameter und weist den Wert der `Text`-Eigenschaft zu.

Die *catch-all-* Parameter Syntax (`*`/`**`), die den Pfad über mehrere Ordner Grenzen hinweg erfasst, wird in Razor-*Komponenten (Razor*-Komponenten) **nicht** unterstützt.

### <a name="component-parameters"></a>Komponentenparameter

Komponenten können über *Komponenten Parameter*verfügen, die mithilfe von öffentlichen Eigenschaften für die Komponenten Klasse mit dem `[Parameter]`-Attribut definiert werden. Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.

*Komponenten/childcomponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

Im folgenden Beispiel aus der Beispiel-App legt der `ParentComponent` den Wert der `Title`-Eigenschaft der `ChildComponent`fest.

*Pages/para Component. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a>Untergeordneter Inhalt

Der Inhalt einer anderen Komponente kann von Komponenten festgelegt werden. Die Zuweisungs Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.

Im folgenden Beispiel verfügt die `ChildComponent` über eine `ChildContent`-Eigenschaft, die ein `RenderFragment`darstellt, das ein Segment der zu Rendering enden Benutzeroberfläche darstellt. Der Wert von `ChildContent` wird im Markup der Komponente positioniert, wo der Inhalt gerendert werden soll. Der Wert `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des `panel-body`des Bootstrap-Panels gerendert.

*Komponenten/childcomponent. Razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Die Eigenschaft, die den `RenderFragment` Inhalt empfängt, muss gemäß der Konvention `ChildContent` benannt werden.

Der `ParentComponent` in der Beispiel-App kann Inhalte zum Rendern des `ChildComponent` bereitstellen, indem der Inhalt innerhalb der `<ChildComponent>` Tags platziert wird.

*Pages/para Component. Razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Attribut-Verteilung und beliebige Parameter

Komponenten können zusätzlich zu den deklarierten Parametern der Komponente zusätzliche Attribute erfassen und Rendering. Zusätzliche Attribute können in einem Wörterbuch aufgezeichnet und dann auf ein Element *gesplattet* werden, wenn die Komponente mithilfe der [`@attributes`](xref:mvc/views/razor#attributes) Razor-Direktive gerendert wird. Dieses Szenario ist nützlich, wenn Sie eine Komponente definieren, die ein Markup Element erzeugt, das eine Vielzahl von Anpassungen unterstützt. Beispielsweise kann es mühsam sein, Attribute für eine `<input>`, die viele Parameter unterstützt, separat zu definieren.

Im folgenden Beispiel verwendet das erste `<input>`-Element (`id="useIndividualParams"`) einzelne Komponenten Parameter, während das zweite `<input>`-Element (`id="useAttributesDict"`) Attribut-splatting verwendet:

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

Der Typ des Parameters muss `IEnumerable<KeyValuePair<string, object>>` mit Zeichen folgen Schlüsseln implementieren. Die Verwendung von `IReadOnlyDictionary<string, object>` ist in diesem Szenario auch eine Option.

Die gerenderten `<input>` Elemente mit beiden Ansätzen sind identisch:

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

Um beliebige Attribute zu akzeptieren, definieren Sie einen Komponenten Parameter mithilfe des `[Parameter]`-Attributs, wobei die `CaptureUnmatchedValues`-Eigenschaft auf `true`festgelegt ist:

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

Die `CaptureUnmatchedValues`-Eigenschaft auf `[Parameter]` ermöglicht dem Parameter, alle Attribute abzugleichen, die keinem anderen Parameter entsprechen. Eine Komponente kann nur einen einzelnen Parameter mit `CaptureUnmatchedValues`definieren. Der mit `CaptureUnmatchedValues` verwendete Eigenschaftentyp muss von `Dictionary<string, object>` mit Zeichen folgen Schlüsseln zugewiesen werden können. `IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` sind auch Optionen in diesem Szenario.

Die Position `@attributes` relativ zur Position von Element Attributen ist wichtig. Wenn `@attributes` auf dem Element splatted sind, werden die Attribute von rechts nach links (zuletzt zu zuerst) verarbeitet. Sehen Sie sich das folgende Beispiel für eine Komponente an, die eine `Child` Komponente verwendet:

" *Parser Component. Razor*":

```razor
<ChildComponent extra="10" />
```

*Childcomponent. Razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Das `extra`-Attribut der `Child` Komponente ist rechts von `@attributes`festgelegt. Die gerenderte `<div>` der `Parent` Komponente enthält `extra="5"`, wenn Sie über das zusätzliche Attribut weitergeleitet wird, da die Attribute von rechts nach links (zuletzt zu zuerst) verarbeitet werden:

```html
<div extra="5" />
```

Im folgenden Beispiel wird die Reihenfolge der `extra` und `@attributes` im `<div>`der `Child` Komponente umgekehrt:

" *Parser Component. Razor*":

```razor
<ChildComponent extra="10" />
```

*Childcomponent. Razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Der gerenderte `<div>` in der `Parent` Komponente enthält beim Durchlaufen des zusätzlichen Attributs `extra="10"`:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Verweise auf Komponenten erfassen

Komponenten Verweise bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle für diese Instanz ausgeben können, z. b. `Show` oder `Reset`. So erfassen Sie einen Komponenten Verweis:

* Fügen Sie der untergeordneten Komponente ein [`@ref`](xref:mvc/views/razor#ref) Attribut hinzu.
* Definieren Sie ein Feld mit demselben Typ wie die untergeordnete Komponente.

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

Wenn die Komponente gerendert wird, wird das `_loginDialog` Feld mit der `MyLoginDialog` untergeordneten Komponenteninstanz aufgefüllt. Anschließend können Sie .NET-Methoden für die Komponenteninstanz aufrufen.

> [!IMPORTANT]
> Die `_loginDialog` Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde und die Ausgabe das `MyLoginDialog`-Element enthält. Bis zu diesem Punkt sind keine Verweise mehr vorhanden. Um Komponenten Verweise zu bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, verwenden Sie die [onafterrenderasync-Methode oder die onafterrendering-Methode](xref:blazor/lifecycle#after-component-render).

Beim Erfassen von Komponenten verweisen wird eine ähnliche Syntax zum [Erfassen von Element verweisen](xref:blazor/javascript-interop#capture-references-to-elements)verwendet, es handelt sich jedoch nicht um eine [JavaScript-Interop](xref:blazor/javascript-interop) -Funktion Komponenten Verweise werden nicht an JavaScript-Code übermittelt,&mdash;Sie nur in .NET-Code verwendet werden.

> [!NOTE]
> Verwenden Sie **keine** Komponenten Verweise, um den Status von untergeordneten Komponenten zu mutieren. Verwenden Sie stattdessen normale deklarative Parameter, um Daten an untergeordnete Komponenten zu übergeben. Die Verwendung normaler deklarativer Parameter führt dazu, dass untergeordnete Komponenten automatisch zu den richtigen Zeitpunkten gerenden werden.

## <a name="invoke-component-methods-externally-to-update-state"></a>Komponenten Methoden extern aufrufen, um den Status zu aktualisieren

Blazor verwendet einen `SynchronizationContext`, um einen einzelnen logischen Ausführungs Thread zu erzwingen. Die [Lebenszyklus Methoden](xref:blazor/lifecycle) einer Komponente und alle Ereignis Rückrufe, die von Blazor ausgelöst werden, werden für diese `SynchronizationContext`ausgeführt. Wenn eine Komponente auf der Grundlage eines externen Ereignisses (z. b. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, verwenden Sie die `InvokeAsync`-Methode, die an Blazor-`SynchronizationContext`weitergeleitet wird.

Stellen Sie sich beispielsweise einen *Benachrichtigungsdienst* vor, der jede überwachende Komponente des aktualisierten Zustands benachrichtigen kann:

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

Registrieren Sie den `NotifierService` als singletion:

* Registrieren Sie den Dienst in Blazor Webassembly in `Program.Main`:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Registrieren Sie in Blazor Server den Dienst in `Startup.ConfigureServices`:

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

Verwenden Sie die `NotifierService`, um eine Komponente zu aktualisieren:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

Im vorherigen Beispiel ruft `NotifierService` die `OnNotify` Methode der Komponente außerhalb des `SynchronizationContext`der Blazorauf. `InvokeAsync` wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Verwenden Sie \@Schlüssel, um die Beibehaltung von Elementen und Komponenten zu steuern.

Beim Rendern einer Liste von Elementen oder Komponenten und der nachfolgend ändernden Elemente bzw. Komponenten muss Blazorder diffingalgorithmus entscheiden, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modell Objekte Ihnen zugeordnet werden sollen. Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber es gibt Fälle, in denen Sie den Prozess möglicherweise steuern möchten.

Betrachten Sie das folgenden Beispiel:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Der Inhalt der `People` Sammlung kann sich mit eingefügten, gelöschten oder neu geordneten Einträgen ändern. Wenn die Komponente erneut ausgeführt wird, kann sich die `<DetailsEditor>` Komponente ändern, um unterschiedliche `Details` Parameterwerte zu erhalten. Dies kann zu einer komplexeren erneuten Ausführung führen als erwartet. In einigen Fällen kann die erneute Ausführung zu sichtbaren Verhaltens unterschieden führen, z. b. bei einem verlorenen Element Fokus.

Der Zuweisungs Prozess kann mit dem `@key` direktivenattribut gesteuert werden. `@key` bewirkt, dass der diffingalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels gewährleistet:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Wenn sich die `People`-Auflistung ändert, behält der diff-Algorithmus die Zuordnung zwischen `<DetailsEditor>`-Instanzen und `person`-Instanzen bei:

* Wenn ein `Person` aus der `People` Liste gelöscht wird, wird nur die entsprechende `<DetailsEditor>` Instanz aus der Benutzeroberfläche entfernt. Andere Instanzen bleiben unverändert.
* Wenn ein `Person` an einer bestimmten Position in der Liste eingefügt wird, wird eine neue `<DetailsEditor>` Instanz an der entsprechenden Position eingefügt. Andere Instanzen bleiben unverändert.
* Wenn `Person` Einträge neu angeordnet werden, werden die entsprechenden `<DetailsEditor>` Instanzen in der Benutzeroberfläche beibehalten und neu angeordnet.

In einigen Szenarios minimiert die Verwendung von `@key` die Komplexität der erneuten Ausführung und vermeidet potenzielle Probleme mit Zustands behafteten Teilen der Dom-Änderung, z. b. der Fokusposition.

> [!IMPORTANT]
> Schlüssel sind für jedes Containerelement oder jede Komponente lokal. Schlüssel werden nicht global über das Dokument hinweg verglichen.

### <a name="when-to-use-key"></a>Verwendung \@Schlüssels

In der Regel ist es sinnvoll, `@key` zu verwenden, wenn eine Liste gerendert wird (z. b. in einem `@foreach`-Block) und ein geeigneter Wert vorhanden ist, um die `@key`zu definieren.

Sie können `@key` auch verwenden, um zu verhindern, dass Blazor eine Element-oder Komponenten Teilstruktur behält, wenn sich ein Objekt ändert:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Wenn `@currentPerson` geändert wird, erzwingt die `@key` Attribute-Direktive Blazor, den gesamten `<div>` und seine Nachfolger zu verwerfen und die Teilstruktur in der Benutzeroberfläche mit neuen Elementen und Komponenten neu zu erstellen. Dies kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächen Zustand beibehalten wird, wenn `@currentPerson` geändert wird.

### <a name="when-not-to-use-key"></a>Verwendung \@Schlüssels nicht

Beim Durchlaufen der `@key`werden Kosten für die Leistung anfallen. Die Leistungskosten sind nicht groß, sondern geben nur `@key` an, wenn die Steuerung der Beibehaltungs Regeln für Elemente oder Komponenten die APP beeinträchtigt.

Auch wenn `@key` nicht verwendet wird, behält Blazor die untergeordneten Elemente und Komponenten Instanzen so weit wie möglich bei. Der einzige Vorteil bei der Verwendung von `@key` ist die Kontrolle darüber, *wie* Modell Instanzen den beibehaltenen Komponenten Instanzen zugeordnet werden, anstatt dem diff-Algorithmus, der die Zuordnung auswählt.

### <a name="what-values-to-use-for-key"></a>Welche Werte müssen für \@Schlüssel verwendet werden?

Im Allgemeinen ist es sinnvoll, eine der folgenden Arten von Werten für `@key`bereitzustellen:

* Modell Objektinstanzen (z. b. eine `Person`-Instanz, wie im vorherigen Beispiel). Dadurch wird die Beibehaltung basierend auf der Objekt Verweis Gleichheit sichergestellt.
* Eindeutige Bezeichner (z. b. Primärschlüssel Werte vom Typ `int`, `string`oder `Guid`).

Stellen Sie sicher, dass die für `@key` verwendeten Werte nicht kollidieren. Wenn innerhalb desselben übergeordneten Elements Werte für die Zwischenablage erkannt werden, löst Blazor eine Ausnahme aus, da Sie alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zuordnen kann. Verwenden Sie nur eindeutige Werte, z. b. Objektinstanzen oder Primärschlüssel Werte.

## <a name="partial-class-support"></a>Unterstützung für Teil Klassen

Razor-Komponenten werden als partielle Klassen generiert. Razor-Komponenten werden mithilfe eines der folgenden Ansätze erstellt:

* C#Code wird in einem [`@code`](xref:mvc/views/razor#code) -Block mit HTML-Markup und Razor-Code in einer einzelnen Datei definiert. Mithilfe dieses Ansatzes definieren Blazor Vorlagen ihre Razor-Komponenten.
* C#der Code wird in einer Code Behind-Datei platziert, die als partielle Klasse definiert ist.

Das folgende Beispiel zeigt die Standard `Counter` Komponente mit einem `@code`-Block in einer APP, die aus einer Blazor Vorlage generiert wurde. HTML-Markup, Razor-Code C# und Code befinden sich in derselben Datei:

*Counter. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

Die `Counter` Komponente kann auch mit einer Code Behind-Datei mit einer partiellen Klasse erstellt werden:

*Counter. Razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.Razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

Fügen Sie alle erforderlichen Namespaces der partiellen Klassendatei nach Bedarf hinzu. Typische Namespaces, die von Razor-Komponenten verwendet werden, sind:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Angeben einer Basisklasse

Die [`@inherits`](xref:mvc/views/razor#inherits) -Direktive kann verwendet werden, um eine Basisklasse für eine Komponente anzugeben. Das folgende Beispiel zeigt, wie eine Komponente eine Basisklasse erben kann, `BlazorRocksBase`, um die Eigenschaften und Methoden der Komponente bereitzustellen. Die Basisklasse sollte von `ComponentBase`abgeleitet werden.

*Pages/blazorrocks. Razor*:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs*:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a>Attribut angeben

Attribute können in Razor-Komponenten mit der [`@attribute`](xref:mvc/views/razor#attribute) -Direktive angegeben werden. Im folgenden Beispiel wird das `[Authorize]`-Attribut auf die-Komponenten Klasse angewendet:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Importieren von Komponenten

Der Namespace einer mit Razor erstellten Komponente basiert auf (in Prioritäts Reihenfolge):

* [`@namespace`](xref:mvc/views/razor#namespace) Bezeichnung in Razor-Datei ( *. Razor*) Markup (`@namespace BlazorSample.MyNamespace`).
* Das Projekt `RootNamespace` in der Projektdatei (`<RootNamespace>BlazorSample</RootNamespace>`).
* Der Projektname, der aus dem Dateinamen der Projektdatei ( *. csproj*) und dem Pfad des Projekt Stamms zur Komponente stammt. Das Framework löst z. b. *{Project root}/pages/index.Razor* (*blazorsample. csproj*) in den Namespace `BlazorSample.Pages`auf. Komponenten folgen C# den namens Bindungs Regeln. Für die `Index` Komponente in diesem Beispiel sind die Komponenten im Bereich alle Komponenten:
  * Im gleichen Ordner, *Seiten*.
  * Die Komponenten im Stammverzeichnis des Projekts, die nicht explizit einen anderen Namespace angeben.

Komponenten, die in einem anderen Namespace definiert sind, werden mithilfe der [`@using`](xref:mvc/views/razor#using) Direktive von Razor in den Gültigkeitsbereich

Wenn eine andere Komponente, `NavMenu.razor`im Ordner " *blazorsample/Shared/* " vorhanden ist, kann die Komponente in `Index.razor` mit der folgenden `@using`-Anweisung verwendet werden:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Auf Komponenten kann auch mit ihren voll qualifizierten Namen verwiesen werden, die die [`@using`](xref:mvc/views/razor#using) -Direktive nicht benötigen:

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> Die `global::` Qualifizierung wird nicht unterstützt.
>
> Das Importieren von Komponenten mit Alias `using` Anweisungen (z. b. `@using Foo = Bar`) wird nicht unterstützt.
>
> Teilweise qualifizierte Namen werden nicht unterstützt. Beispielsweise wird das Hinzufügen von `@using BlazorSample` und verweisen auf `NavMenu.razor` mit `<Shared.NavMenu></Shared.NavMenu>` nicht unterstützt.

## <a name="conditional-html-element-attributes"></a>Attribute für bedingtes HTML-Element

HTML-Element Attribute werden basierend auf dem .net-Wert bedingt gerendert. Wenn der Wert `false` oder `null`ist, wird das Attribut nicht gerendert. Wenn der Wert `true`ist, wird das Attribut minimiert gerendert.

Im folgenden Beispiel bestimmt `IsCompleted`, ob `checked` im Markup des Elements gerendert wird:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Wenn `IsCompleted` `true`ist, wird das Kontrollkästchen wie folgt gerendert:

```html
<input type="checkbox" checked />
```

Wenn `IsCompleted` `false`ist, wird das Kontrollkästchen wie folgt gerendert:

```html
<input type="checkbox" />
```

Weitere Informationen finden Sie unter <xref:mvc/views/razor>.

> [!WARNING]
> Einige HTML-Attribute, wie z. b. [Aria-gedrückt](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), funktionieren nicht ordnungsgemäß, wenn der .NET-Typ ein `bool`ist. Verwenden Sie in diesen Fällen einen `string` Typ anstelle eines `bool`.

## <a name="raw-html"></a>Unformatierte HTML

Zeichen folgen werden normalerweise mithilfe von Dom-Textknoten gerendert, was bedeutet, dass alle darin enthaltenen Markup ignoriert und als Literaltext behandelt werden. Um unformatierten HTML-Code zu erstellen, packen Sie den HTML-Inhalt in einen `MarkupString` Wert Der Wert wird als HTML oder SVG analysiert und in das DOM eingefügt.

> [!WARNING]
> Das Rendern von Rohdaten aus einer nicht vertrauenswürdigen Quelle stellt ein **Sicherheitsrisiko dar** und sollte vermieden werden.

Im folgenden Beispiel wird gezeigt, wie der `MarkupString`-Typ verwendet wird, um der gerenderten Ausgabe einer Komponente einen Block von statischem HTML-Inhalt hinzuzufügen:

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Kaskadierende Werte und Parameter

In einigen Szenarien ist es unpraktisch, Daten aus einer Vorgänger Komponente mithilfe von [Komponenten Parametern](#component-parameters)an eine Nachfolger Komponente zu übertragen, insbesondere, wenn mehrere Komponenten Ebenen vorhanden sind. Kaskadierende Werte und Parameter lösen dieses Problem, indem eine übergeordnete Komponente eine bequeme Möglichkeit bietet, für alle untergeordneten Komponenten einen Wert bereitzustellen. Kaskadierende Werte und Parameter bieten auch einen Ansatz für die Koordination von Komponenten.

### <a name="theme-example"></a>Designbeispiel

Im folgenden Beispiel der Beispiel-App gibt die `ThemeInfo`-Klasse die Design Informationen an, die in der Komponenten Hierarchie nach unten fließen, sodass alle Schaltflächen innerhalb eines bestimmten Teils der APP denselben Stil haben.

*Uithmeclasses/themerfo. cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Eine Vorgänger Komponente kann einen kaskadierenden Wert mithilfe der Komponente für den kaskadierenden Wert bereitstellen. Die `CascadingValue` Komponente umschließt eine Teilstruktur der Komponenten Hierarchie und stellt einen einzelnen Wert für alle Komponenten in dieser Unterstruktur bereit.

Beispielsweise gibt die Beispiel-App Design Informationen (`ThemeInfo`) in einem der Layouts der App als kaskadierenden Parameter für alle Komponenten an, die den Layouttext der `@Body`-Eigenschaft bilden. `ButtonClass` wird der Wert `btn-success` in der Layoutkomponente zugewiesen. Jede Nachfolger Komponente kann diese Eigenschaft über das `ThemeInfo` kaskadierenden Objekts nutzen.

`CascadingValuesParametersLayout` Komponente:

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Um kaskadierende Werte zu verwenden, deklarieren Komponenten kaskadierende Parameter mithilfe des `[CascadingParameter]`-Attributs. Kaskadierende Werte werden nach Typ an kaskadierende Parameter gebunden.

In der Beispiel-App bindet die `CascadingValuesParametersTheme` Komponente den `ThemeInfo` kaskadierenden Wert an einen kaskadierenden Parameter. Der-Parameter wird verwendet, um die CSS-Klasse für eine der Schaltflächen festzulegen, die von der Komponente angezeigt werden.

`CascadingValuesParametersTheme` Komponente:

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

Um mehrere Werte desselben Typs innerhalb derselben Unterstruktur zu kaskadieren, stellen Sie jeder `CascadingValue` Komponente und der entsprechenden `CascadingParameter`eine eindeutige `Name` Zeichenfolge bereit. Im folgenden Beispiel werden zwei `CascadingValue` Komponenten verschiedene Instanzen von `MyCascadingType` nach Namen kaskadieren:

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

In einer Nachfolger Komponente erhalten die kaskadierenden Parameter ihre Werte von den entsprechenden kaskadierenden Werten in der Vorgänger Komponente nach Namen:

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>TabSet-Beispiel

Kaskadierende Parameter ermöglichen auch die Zusammenarbeit von Komponenten in der Komponenten Hierarchie. Sehen Sie sich beispielsweise das folgende *Tabset* -Beispiel in der Beispiel-APP an.

Die Beispiel-App verfügt über eine `ITab` Schnittstelle, die Registerkarten implementieren:

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

Die `CascadingValuesParametersTabSet` Komponente verwendet die `TabSet`-Komponente, die mehrere `Tab` Komponenten enthält:

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

Die untergeordneten `Tab` Komponenten werden nicht explizit als Parameter an die `TabSet`übermittelt. Stattdessen sind die untergeordneten `Tab` Komponenten Teil des untergeordneten Inhalts des `TabSet`. Allerdings müssen die `TabSet` weiterhin über jede `Tab` Komponente informiert werden, damit Sie die Header und die aktive Registerkarte Rendering können. Um diese Koordination zu aktivieren, ohne dass zusätzlicher Code erforderlich ist, kann die `TabSet` Komponente *sich selbst als kaskadierenden Wert bereitstellen* , der dann von den Nachfolger `Tab` Komponenten abgerufen wird.

`TabSet` Komponente:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Die Nachfolger `Tab` Komponenten erfassen den enthaltenden `TabSet` als kaskadierenden Parameter, sodass sich die `Tab` Komponenten dem `TabSet` und der Koordinate, auf der die Registerkarte aktiv ist, hinzufügen.

`Tab` Komponente:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor-Vorlagen

Rendering-Fragmente können mithilfe der Razor-Vorlagen Syntax definiert werden. Mit Razor-Vorlagen können Sie einen UI-Code Ausschnitt definieren und das folgende Format annehmen:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Im folgenden Beispiel wird veranschaulicht, wie Sie `RenderFragment`-und `RenderFragment<T>` Werte angeben und Vorlagen direkt in einer-Komponente darstellen. Rendering-Fragmente können auch als Argumente an vorlagenbasierte [Komponenten](xref:blazor/templated-components)übermittelt werden.

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Gerenderte Ausgabe des vorangehenden Codes:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>SVG-Bilder (Scalable Vector Graphics)

Seit Blazor rendert HTML-Bilder, die vom Browser unterstützt werden, einschließlich der SVG (Scalable Vector Graphics)-*Bilder (SVG),* werden über das `<img>`-Tag unterstützt:

```html
<img alt="Example image" src="some-image.svg" />
```

Ebenso werden SVG-Images in den CSS-Regeln einer Stylesheet-Datei (*CSS*) unterstützt:

```css
.my-element {
    background-image: url("some-image.svg");
}
```

Inline-SVG-Markup wird jedoch nicht in allen Szenarien unterstützt. Wenn Sie ein `<svg>`-Tag direkt in eine Komponenten Datei (*Razor*) platzieren, wird das grundlegende Image Rendering unterstützt, aber viele erweiterte Szenarien werden noch nicht unterstützt. Beispielsweise werden `<use>` Tags derzeit nicht beachtet, und `@bind` können nicht mit einigen SVG-Tags verwendet werden. Wir gehen davon aus, dass diese Einschränkungen in einer zukünftigen Version behandelt werden.

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:security/blazor/server> &ndash; enthält Anleitungen zum Entwickeln von Blazor Server-apps, die sich mit der Ressourcenauslastung auseinandersetzen müssen.
