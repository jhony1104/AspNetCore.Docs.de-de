---
title: Erstellen und Verwenden von ASP.NET Core-Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, Datenbindungen durchführen, Ereignisse behandeln und die Lebenszyklen von Komponenten verwalten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/components
ms.openlocfilehash: 2a6de1a39737f98cb151a0556f36c223d86f9752
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/12/2020
ms.locfileid: "84723950"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>Erstellen und Verwenden von ASP.NET Core-Razor-Komponenten

Von [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) und [Tobias Bartsch](https://www.aveo-solutions.com/)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Blazor-Apps werden mithilfe von *Komponenten* erstellt. Eine Komponente ist ein eigenständiges Element einer Benutzeroberfläche, z. B. eine Seite, ein Dialogfeld oder ein Formular. Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum Reagieren auf Benutzeroberflächenereignisse erforderlich ist. Komponenten sind flexibel und kompakt. Sie können geschachtelt, wiederverwendet und für mehrere Projekte freigegeben werden.

## <a name="component-classes"></a>Komponentenklassen

Komponenten werden mithilfe einer Kombination aus C# und HTML-Markup in [Razor](xref:mvc/views/razor)-Komponentendateien ( *.razor*) implementiert. Eine Komponente in Blazor wird formal als *Razor-Komponente* bezeichnet.

Der Name einer Komponente muss mit einem Großbuchstaben beginnen. Beispielsweise ist *MyCoolComponent.razor* zulässig, *mycoolcomponent.razor* aber nicht.

Die Benutzeroberfläche einer Komponente wird mithilfe von HTML definiert. Dynamische Renderinglogik (z. B. Schleifen, Bedingungen, Ausdrücke) wird über eine eingebettete C#-Syntax mit dem Namen *Razor* hinzugefügt. Wenn eine App kompiliert wird, werden das HTML-Markup und die C#-Renderinglogik in eine Komponentenklasse konvertiert. Der Name der generierten Klasse entspricht dem Namen der Datei.

Member der Komponentenklasse werden in einem [`@code`][1]-Block definiert. Im [`@code`][1]-Block wird der Komponentenstatus (Eigenschaften, Felder) mit Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben. Mehrere [`@code`][1]-Blöcke sind zulässig.

Komponentenmember können als Teil der Renderinglogik der Komponente mithilfe von C#-Ausdrücken verwendet werden, die mit `@`beginnen. Beispielsweise wird ein C#-Feld gerendert, indem `@` dem Feldnamen vorangestellt wird. Das Beispiel wertet Folgendes aus und führt ein Rendering durch:

* `headingFontStyle` in den CSS-Eigenschaftswert für `font-style`
* `headingText` in den Inhalt des `<h1>`-Elements

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

Nachdem die Komponente zum ersten Mal gerendert wurde, generiert sie ihre Renderingstruktur als Reaktion auf Ereignisse erneut. Blazor vergleicht die neue Renderingstruktur dann mit der vorherigen und wendet alle Änderungen auf das Browser-Dokumentobjektmodell (Document Object Model, DOM) an.

Komponenten sind normale C#-Klassen und können an beliebiger Stelle innerhalb eines Projekts eingefügt werden. Komponenten, die Webseiten erzeugen, befinden sich in der Regel im Ordner *Pages*. Nicht für Seiten verwendete Komponenten werden häufig im Ordner *Shared* oder einem benutzerdefinierten Ordner gespeichert, der dem Projekt hinzugefügt wurde.

In der Regel wird der Namespace einer Komponente aus dem Stammnamespace der App und dem Speicherort (Ordner) der Komponente in der App abgeleitet. Wenn der Stammnamespace der App `BlazorApp` ist und sich die `Counter` Komponente im Ordner *Pages* befindet, ist

* der Namespace der `Counter`-Komponente `BlazorApp.Pages`, und
* der vollqualifizierten Typname der Komponente ist `BlazorApp.Pages.Counter`.

Fügen Sie für benutzerdefinierte Ordner, die Komponenten enthalten, eine `using`-Anweisung der übergeordneten Komponente oder der *_Imports.razor*-Datei der App hinzu. Im folgenden Beispiel werden Komponenten im Ordner *Components* (Komponenten) zur Verfügung gestellt:

```razor
@using BlazorApp.Components
```

Alternativ kann auf eine Komponente direkt verwiesen werden:

```razor
<BlazorApp.Components.MyCoolComponent />
```

Weitere Informationen finden Sie im Abschnitt [Importieren von Komponenten](#import-components).

## <a name="razor-syntax"></a>Razor-Syntax

Razor-Komponenten in Blazor-Apps verwenden Razor-Syntax ausführlich. Wenn Sie mit der Razor-Markupsprache nicht vertraut sind, sollten Sie <xref:mvc/views/razor> lesen, bevor Sie fortfahren.

Beachten Sie beim Zugriff auf den Inhalt der Razor-Syntax besonders die folgenden Abschnitte:

* [Direktiven](xref:mvc/views/razor#directives): Reservierte Schlüsselwörter mit `@`-Präfix, die in der Regel die Art und Weise ändern, wie das Komponentenmarkup analysiert wird oder funktioniert.
* [Direktivenattribute](xref:mvc/views/razor#directive-attributes): Reservierte Schlüsselwörter mit `@`-Präfix, die in der Regel die Art und Weise ändern, wie Komponentenelemente analysiert werden oder funktionieren.

## <a name="static-assets"></a>Statische Ressourcen

Blazor befolgt die Konvention für ASP.NET Core-Apps, statische Ressourcen unter dem Webstammordner ([wwwroot](xref:fundamentals/index#web-root)) des Projekts zu speichern.

Verwenden Sie einen zur Basis relativen Pfad (`/`), um auf den Webstamm einer statischen Ressource zu verweisen. Im folgenden Beispiel wird *logo.png* im Ordner *{Projektstamm}/wwwroot/images* gespeichert:

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor-Komponenten unterstützen **keine** Notation mit Tilde und Schrägstrich (`~/`).

Weitere Informationen zum Festlegen des Basispfads einer App finden Sie unter <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="tag-helpers-arent-supported-in-components"></a>Keine Unterstützung von Taghilfsprogrammen in Komponenten

[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) werden in Razor-Komponenten ( *.razor*-Dateien) nicht unterstützt. Sie können Taghilfsobjekte in Blazor bereitstellen, indem Sie eine Komponente mit der gleichen Funktionalität wie das Taghilfsprogramm erstellen und diese stattdessen verwenden.

## <a name="use-components"></a>Verwenden von Komponenten

Komponenten können andere Komponenten enthalten, sofern Sie sie mithilfe der HTML-Elementsyntax deklarieren. Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.

Das folgende Markup in *Index.razor* rendert eine `HeadingComponent`-Instanz:

```razor
<HeadingComponent />
```

*Components/HeadingComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

Wenn eine Komponente ein HTML-Element mit einem groß geschriebenen ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, dass das Element einen unerwarteten Namen aufweist. Durch das Hinzufügen einer [`@using`][2]-Anweisung für den Namespace der Komponente wird die Komponente zur Verfügung gestellt, wodurch die Warnung nicht mehr auftritt.

## <a name="routing"></a>Routing

Das Routing in Blazor erfolgt durch die Bereitstellung einer Routenvorlage für jede zugängliche Komponente in der App.

Wenn eine Razor-Datei mit einer [`@page`][9]-Anweisung kompiliert wird, wird der generierten Klasse ein <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> hinzugefügt und so die Routenvorlage angegeben. Zur Laufzeit sucht der Router nach Komponentenklassen mit <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> und rendert die Komponente, deren Routenvorlage mit der angeforderten URL übereinstimmt.

```razor
@page "/ParentComponent"

...
```

Weitere Informationen finden Sie unter <xref:blazor/routing>.

## <a name="parameters"></a>Parameter

### <a name="route-parameters"></a>Routenparameter

Komponenten können Routenparameter von der Routenvorlage empfangen, die in der [`@page`][9]-Anweisung bereitgestellt wird. Der Router verwendet Routenparameter, um die entsprechenden Komponentenparameter aufzufüllen.

*Pages/RouteParameter.razor*:

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

Optionale Parameter werden nicht unterstützt. Deshalb werden im vorherigen Beispiel zwei [`@page`][9]-Anweisungen angewendet. Die erste ermöglicht die Navigation zur Komponente ohne einen Parameter. Die zweite [`@page`][9]-Anweisung empfängt den `{text}`-Routenparameter und weist den Wert der `Text`-Eigenschaft zu.

Die *Catch-all*-Parametersyntax (`*`/`**`), die den Pfad ordnerübergreifend erfasst, wird in Razor-Komponenten ( *.razor*) **nicht** unterstützt.

### <a name="component-parameters"></a>Komponentenparameter

Komponenten können *Komponentenparameter* enthalten, die mithilfe öffentlicher Eigenschaften für die Komponentenklasse mit dem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attribut definiert werden. Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.

*Components/ChildComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

Im folgenden Beispiel aus der Beispiel-App legt der `ParentComponent` den Wert der `Title`-Eigenschaft von `ChildComponent` fest.

*Pages/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> Erstellen Sie keine Komponenten, die in ihre eigenen *Komponentenparameter* schreiben, sondern verwenden Sie stattdessen ein privates Feld. Weitere Informationen finden Sie im Abschnitt [Erstellen Sie keine Komponenten, die in ihre eigenen Parametereigenschaften schreiben](#dont-create-components-that-write-to-their-own-parameter-properties).

## <a name="child-content"></a>Untergeordneter Inhalt

Komponenten können den Inhalt anderer Komponenten festlegen. Die zuweisende Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.

Im folgenden Beispiel enthält `ChildComponent` eine `ChildContent`-Eigenschaft, die einen <xref:Microsoft.AspNetCore.Components.RenderFragment>-Delegaten darstellt, der ein zu renderndes Segment der Benutzeroberfläche darstellt. Der Wert von `ChildContent` wird im Markup der Komponente positioniert, wo der Inhalt gerendert werden soll. Der Wert von `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des `panel-body`-Elements des Bootstrappanels gerendert.

*Components/ChildComponent.razor*:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Die Eigenschaft, die den Inhalt von <xref:Microsoft.AspNetCore.Components.RenderFragment> empfängt, muss gemäß der Konvention `ChildContent` benannt werden.

Die `ParentComponent`-Datei in der Beispiel-App kann Inhalte zum Rendern von `ChildComponent` bereitstellen, indem der Inhalt innerhalb der `<ChildComponent>`-Tags eingefügt wird.

*Pages/ParentComponent.razor*:

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Attributsplatting und arbiträre Parameter

Komponenten können zusätzlich zu den deklarierten Parametern der Komponente weitere Attribute erfassen und rendern. Zusätzliche Attribute können in einem Wörterbuch erfasst und dann für ein Element *gesplattet* werden, wenn die Komponente mithilfe der [`@attributes`][3]-Anweisung Razor gerendert wird. Dieses Option ist nützlich, wenn Sie eine Komponente definieren, die ein Markupelement erzeugt, das viele verschiedene Anpassungen unterstützt. Beispielsweise kann es mühsam sein, Attribute für ein `<input>`-Element separat zu definieren, das viele Parameter unterstützt.

Im folgenden Beispiel verwendet das erste `<input>`-Element (`id="useIndividualParams"`) einzelne Komponentenparameter, während das zweite `<input>`-Element (`id="useAttributesDict"`) Attributsplatting verwendet:

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

Der Typ des Parameters muss `IEnumerable<KeyValuePair<string, object>>` mit Zeichenfolgenschlüsseln implementieren. Die Verwendung von `IReadOnlyDictionary<string, object>` ist in diesem Szenario auch möglich.

Die gerenderten `<input>`-Elemente sind mit beiden Ansätzen identisch:

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

Definieren Sie einen Komponentenparameter mithilfe des [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attributs, bei dem die <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>-Eigenschaft auf `true` festgelegt ist, damit beliebige Attribute akzeptiert werden:

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

Die <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>-Eigenschaft für [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) ermöglicht dem Parameter den Abgleich aller Attribute, die keinem anderen Parameter entsprechen. Eine Komponente kann nur einen einzelnen Parameter mit <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> definieren. Der mit <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> verwendete Eigenschaftentyp muss von `Dictionary<string, object>` mit Zeichenfolgenschlüsseln zugewiesen werden können. `IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` sind in diesem Szenario ebenfalls mögliche Optionen.

Die Position von [`@attributes`][3] relativ zur Position der Elementattribute ist wichtig. Wenn [`@attributes`][3]-Anweisungen für das Element gesplattet werden, werden die Attribute von rechts nach links (letztes bis erstes) verarbeitet. Sehen Sie sich das folgende Beispiel für eine Komponente an, die eine `Child`-Komponente verwendet:

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor*:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Das `extra`-Attribut der `Child`-Komponente ist rechts von [`@attributes`][3] festgelegt. Das gerenderte `<div>`-Element der `Parent`-Komponente enthält `extra="5"`, wenn dieses über das zusätzliche Attribut weitergeleitet wird, da die Attribute von rechts nach links (letztes bis erstes) verarbeitet werden:

```html
<div extra="5" />
```

Im folgenden Beispiel wird die Reihenfolge von `extra` und [`@attributes`][3] in `<div>` der `Child`-Komponente umgekehrt:

*ParentComponent.razor*:

```razor
<ChildComponent extra="10" />
```

*ChildComponent.razor*:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Das gerenderte `<div>`-Element in der `Parent`-Komponente enthält `extra="10"`, wenn es über das zusätzliche Attribut weitergeleitet wird:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Erfassen von Verweisen auf Komponenten

Komponentenverweise bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle wie `Show` oder `Reset` für diese Instanz ausgeben können. So erfassen Sie einen Komponentenverweis:

* Fügen Sie der untergeordneten Komponente ein [`@ref`][4]-Attribut hinzu.
* Definieren Sie ein Feld mit demselben Typ wie die untergeordnete Komponente.

```razor
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Wenn die Komponente gerendert wird, wird das `loginDialog`-Feld mit der untergeordneten `MyLoginDialog`-Komponenteninstanz aufgefüllt. Anschließend können Sie .NET-Methoden für die Komponenteninstanz aufrufen.

> [!IMPORTANT]
> Die `loginDialog`-Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde und die Ausgabe das `MyLoginDialog`-Element enthält. Bis zu diesem Punkt sind keine Verweise nötig. Sie können Komponentenverweise bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, indem Sie die Methode [OnAfterRenderAsync oder OnAfterRender](xref:blazor/lifecycle#after-component-render) verwenden.

Informationen zum Verweisen auf Komponenten in einer Schleife finden Sie unter [Erfassen von Verweisen auf mehrere ähnliche untergeordnete Komponenten (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Beim Erfassen von Komponentenverweisen wird zwar eine ähnliche Syntax verwendet, um [Elementverweise zu erfassen](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), es handelt sich jedoch nicht um ein JavaScript-Interop-Feature. Komponentenverweise werden nicht an JavaScript-Code übermittelt. Komponentenverweise werden nur in .NET-Code verwendet.

> [!NOTE]
> Verwenden Sie **keine** Komponentenverweise verwenden, um den Zustand von untergeordneten Komponenten zu verändern. Verwenden Sie stattdessen normale deklarative Parameter, um Daten an untergeordnete Komponenten zu übergeben. Die Verwendung normaler deklarativer Parameter führt dazu, dass untergeordnete Komponenten automatisch zu den richtigen Zeitpunkten gerendert werden.

## <a name="invoke-component-methods-externally-to-update-state"></a>Externes Aufrufen von Komponentenmethoden zur Aktualisierung des Status

Blazor verwendet einen Synchronisierungskontext (<xref:System.Threading.SynchronizationContext>), um einen einzelnen logischen Ausführungsthread zu erzwingen. Die [Lebenszyklusmethoden](xref:blazor/lifecycle) einer Komponente und alle Ereignisrückrufe, die von Blazor ausgelöst werden, werden in diesem Synchronisierungskontext ausgeführt.

Der Synchronisierungskontext des Blazor-Servers versucht, eine Singlethreadumgebung zu emulieren, sodass er genau mit dem WebAssembly-Modell im Browser übereinstimmt, das ein Singlethreadmodell ist. Zu jedem Zeitpunkt wird die Arbeit für genau einen Thread ausgeführt, woraus der Eindruck eines einzelnen logischen Threads entsteht. Zwei Vorgänge werden nicht gleichzeitig ausgeführt.

Wenn eine Komponente aufgrund eines externen Ereignisses (z. B. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, verwenden Sie die `InvokeAsync`-Methode, die an den Synchronisierungskontext von Blazor weitergeleitet wird. Nehmen Sie einen *Benachrichtigungsdienst* als Beispiel, der jede überwachende Komponente des aktualisierten Zustands benachrichtigen kann:

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

Registrieren Sie `NotifierService` als Singleton:

* Registrieren Sie den Dienst in Blazor WebAssembly in `Program.Main`:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Registrieren Sie den Dienst in Blazor Server in `Startup.ConfigureServices`:

  ```csharp
  services.AddScoped<NotifierService>();
  ```

Verwenden Sie `NotifierService`, um eine Komponente zu aktualisieren:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

Im vorherigen Beispiel ruft `NotifierService` die `OnNotify`-Methode der Komponente außerhalb des Synchronisierungskontexts von Blazor auf. `InvokeAsync` wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Verwenden von \@key zur Steuerung der Beibehaltung von Elementen und Komponenten

Wenn Sie eine Element- oder Komponentenliste rendern und die Elemente oder Komponenten nachfolgend geändert werden, muss der Vergleichsalgorithmus von Blazor bestimmen, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modellobjekte diesen zugeordnet werden sollen. Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber in einigen Fällen sollten Sie den Prozess steuern.

Betrachten Sie das folgende Beispiel:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Der Inhalt der `People`-Sammlung kann sich durch eingefügte, gelöschte oder neu sortierte Einträgen ändern. Wenn die Komponente wiederholt gerendert wird, kann sich die `<DetailsEditor>`-Komponente ändern, damit sie andere `Details`-Parameterwerte empfangen kann. Dadurch kann es zu einem unerwartet komplexen erneuten Rendering kommen. In einigen Fällen kann das erneute Rendering zu erkennbaren Verhaltensabweichungen führen, z. B. zu einem verlorenen Elementfokus.

Der Zuordnungsprozess kann mit dem [`@key`][5]-Anweisungsattribut gesteuert werden. [`@key`][5] bewirkt, dass der Vergleichsalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels garantiert:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Wenn sich die `People`-Sammlung ändert, behält der Vergleichsalgorithmus die Zuordnung zwischen `<DetailsEditor>`-Instanzen und `person`-Instanzen bei:

* Wenn ein `Person`-Element aus der `People`-Liste gelöscht wird, wird nur die entsprechende `<DetailsEditor>`-Instanz von der Benutzeroberfläche entfernt. Andere Instanzen bleiben unverändert.
* Wenn ein `Person` an einer bestimmten Position in der Liste eingefügt wird, wird eine neue `<DetailsEditor>`-Instanz an der entsprechenden Position eingefügt. Andere Instanzen bleiben unverändert.
* Wenn `Person`-Einträge neu sortiert werden, werden die entsprechenden `<DetailsEditor>`-Instanzen beibehalten und auf der Benutzeroberfläche neu angeordnet.

In einigen Szenarios minimiert die Verwendung von [`@key`][5] die Komplexität des erneuten Renderings und vermeidet potenzielle Probleme mit zustandsbehafteten Teilen der DOM-Änderung, z. B. der Fokusposition.

> [!IMPORTANT]
> Schlüssel sind für jedes Containerelement oder jede Komponente lokal. Schlüssel werden nicht dokumentübergreifend global verglichen.

### <a name="when-to-use-key"></a>Anwendungsfälle für \@key

In der Regel ist es sinnvoll, [`@key`][5] zu verwenden, wenn eine Liste gerendert wird (z. B. in einem [foreach](/dotnet/csharp/language-reference/keywords/foreach-in)-Block) und ein geeigneter Wert vorhanden ist, um den [`@key`][5] zu definieren.

Sie können [`@key`][5] auch verwenden, um zu verhindern, dass Blazor eine Element- oder Komponententeilstruktur beibehält, wenn sich ein Objekt ändert:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Wenn `@currentPerson` geändert wird, zwingt die [`@key`][5]-Attributanweisung Blazor, das gesamte `<div>`-Element und dessen Nachfolger zu verwerfen und die Teilstruktur auf der Benutzeroberfläche mit neuen Elementen und Komponenten noch mal zu erstellen. Das kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächenzustand beibehalten wird, wenn `@currentPerson` geändert wird.

### <a name="when-not-to-use-key"></a>Ungeeignete Anwendungsfälle für \@key

Bei einem Vergleich mit [`@key`][5] wird die Leistung beeinträchtigt. Die Beeinträchtigung der Leistung ist nicht erheblich. Sie sollten [`@key`][5] jedoch nur angeben, wenn sich die Beibehaltungsregeln für das Element oder die Komponente positiv auf die App auswirken.

Auch wenn [`@key`][5] nicht verwendet wird, behält Blazor die untergeordneten Element- und Komponenteninstanzen so weit wie möglich bei. Der einzige Vorteil bei der Verwendung von [`@key`][5] besteht in der Kontrolle darüber, *wie* Modellinstanzen den beibehaltenen Komponenteninstanzen zugeordnet werden, anstatt die Zuordnung durch den Vergleichsalgorithmus bestimmen zu lassen.

### <a name="what-values-to-use-for-key"></a>Zu verwendende Werte für \@key

Im Allgemeinen ist es sinnvoll, Werte der folgenden Kategorien für [`@key`][5] bereitzustellen:

* Modellobjektinstanzen (z. B. eine `Person`-Instanz wie im vorherigen Beispiel): Dadurch wird die Beibehaltung basierend auf der Objektverweisgleichheit sichergestellt.
* Eindeutige Bezeichner (z. B. Primärschlüsselwerte vom Typ `int`, `string`oder `Guid`):

Stellen Sie sicher, dass die für [`@key`][5] verwendeten Werte nicht kollidieren. Wenn innerhalb desselben übergeordneten Elements kollidierende Werte erkannt werden, löst Blazor eine Ausnahme aus, da alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zugeordnet werden können. Verwenden Sie nur eindeutige Werte wie Objektinstanzen oder Primärschlüsselwerte.

## <a name="dont-create-components-that-write-to-their-own-parameter-properties"></a>Erstellen Sie keine Komponenten, die in ihre eigenen Parametereigenschaften schreiben

Parameter werden unter den folgenden Bedingungen überschrieben:

* Der Inhalt einer untergeordneten Komponente wird mit einem <xref:Microsoft.AspNetCore.Components.RenderFragment> gerendert.
* <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> wird in der übergeordneten Komponente aufgerufen.

Parameter werden zurückgesetzt, weil die übergeordnete Komponente erneut gerendert wird, wenn <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufgerufen wird und der untergeordneten Komponente neue Parameterwerte bereitgestellt werden.

Angenommen, die folgende `Expander`-Komponente:

* rendert untergeordneten Inhalt.
* schaltet die Anzeige von untergeordnetem Inhalt mit einem Komponentenparameter um.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @Expanded)

    @if (Expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

Die `Expander`-Komponente wird einer übergeordneten Komponente hinzugefügt, die <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufrufen kann:

```razor
<Expander Expanded="true">
    <h1>Hello, world!</h1>
</Expander>

<Expander Expanded="true" />

<button @onclick="@(() => StateHasChanged())">
    Call StateHasChanged
</button>
```

Anfänglich verhalten sich die `Expander`-Komponenten unabhängig, wenn ihre `Expanded`-Eigenschaften umgeschaltet werden. Die untergeordneten Komponenten behalten ihre Zustände erwartungsgemäß bei. Wenn <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in der übergeordneten Komponente aufgerufen wird, wird der `Expanded`-Parameter der ersten untergeordneten Komponente wieder zurück auf seinen ursprünglichen Wert gesetzt (`true`). Der `Expanded`-Wert der zweiten `Expander`-Komponente wird nicht zurückgesetzt, weil in der zweiten Komponente kein untergeordneter Inhalt gerendert wird.

Um den Zustand im vorangehenden Szenario beizubehalten, verwenden Sie ein *privates Feld* in der `Expander`-Komponente, um dessen Umschaltungszustand beizubehalten.

Die folgende `Expander`-Komponente:

* akzeptiert den Wert des `Expanded`-Komponentenparameters aus der übergeordneten Komponente.
* weist den Wert des Komponentenparameters einem *privaten Feld* (`expanded`) im [OnInitialized-Ereignis](xref:blazor/lifecycle#component-initialization-methods) zu.
* verwendet das private Feld, um seinen internen Umschaltungszustand beizubehalten.

```razor
<div @onclick="@Toggle">
    Toggle (Expanded = @expanded)

    @if (expanded)
    {
        @ChildContent
    }
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private bool expanded;

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

## <a name="partial-class-support"></a>Unterstützung für partielle Klassen

Razor-Komponenten werden als partielle Klassen generiert. Razor-Komponenten werden mithilfe eines der folgenden Ansätze erstellt:

* C#-Code wird in einem [`@code`][1]-Block mit HTML-Markup und Razor-Code in einer einzelnen Datei definiert. Blazor-Vorlagen definieren Razor-Komponenten mithilfe dieses Ansatzes.
* C#-Code wird in einer CodeBehind-Datei gespeichert, die als partielle Klasse definiert ist.

Das folgende Beispiel zeigt die `Counter`-Standardkomponente mit einem [`@code`][1]-Block in einer App, die aus einer Blazor-Vorlage generiert wurde. HTML-Markup, Razor-Code und C#-Code befinden sich in derselben Datei:

*Counter.razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

Die `Counter`-Komponente kann auch mit einer CodeBehind-Datei mit einer partiellen Klasse erstellt werden:

*Counter.razor*:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.razor.cs*:

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

Fügen Sie der partiellen Klassendatei alle erforderlichen Namespaces nach Bedarf hinzu. Folgende typischen Namespaces werden von Razor-Komponenten verwendet:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>Angeben einer Basisklasse

Die [`@inherits`][6]-Anweisung kann verwendet werden, um eine Basisklasse für eine Komponente anzugeben. Das folgende Beispiel zeigt, wie eine Komponente eine Basisklasse (`BlazorRocksBase`) erben kann, um die Eigenschaften und Methoden der Komponente bereitzustellen. Die Basisklasse sollte von <xref:Microsoft.AspNetCore.Components.ComponentBase> abgeleitet werden.

*Pages/BlazorRocks.razor*:

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

## <a name="specify-an-attribute"></a>Angeben eines Attributs

Attribute können in Razor-Komponenten mit der [`@attribute`][7]-Anweisung angegeben werden. Im folgenden Beispiel wird das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut auf die Komponentenklasse angewendet:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>Importieren von Komponenten

Der Namespace einer mit Razor erstellten Komponente basiert auf Folgendem (nach Priorität):

* [`@namespace`][8]-Bezeichnung im Markup (`@namespace BlazorSample.MyNamespace`) der Razor-Datei ( *.razor*).
* Die `RootNamespace`-Eigenschaft des Projekts in der Projektdatei (`<RootNamespace>BlazorSample</RootNamespace>`)
* Der Projektname, der aus dem Namen der Projektdatei ( *.csproj*) und dem Pfad vom Projektstamm zur Komponente resultiert. Das Framework löst z. B. *{Projektstamm}/Pages/Index.razor* (*BlazorSample.csproj*) in den Namespace `BlazorSample.Pages` auf. Komponenten folgen den Namensbindungsregeln für C#. Für die `Index`-Komponente in diesem Beispiel werden folgende Komponenten berücksichtigt:
  * Komponenten im selben Ordner (*Pages*)
  * Komponenten im Stammverzeichnis des Projekts, die nicht explizit einen anderen Namespace angeben

Komponenten, die in einem anderen Namespace definiert sind, werden mithilfe der Razor-Anweisung [`@using`][2] in den Geltungsbereich einbezogen.

Wenn eine andere Komponente (`NavMenu.razor`) im Ordner *BlazorSample/Shared/* vorhanden ist, kann die Komponente mithilfe der folgenden [`@using`][2]-Anweisung in `Index.razor` verwendet werden:

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

Auf Komponenten kann auch mit ihrem vollqualifizierten Namen verwiesen werden. Hierfür ist die Anweisung [`@using`][2] nicht erforderlich:

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> Die `global::`-Qualifizierung wird nicht unterstützt.
>
> Das Importieren von Komponenten mit [using](/dotnet/csharp/language-reference/keywords/using-statement)-Aliasanweisungen (z B. `@using Foo = Bar`) wird nicht unterstützt.
>
> Teilweise qualifizierte Namen werden nicht unterstützt. Das Hinzufügen von `@using BlazorSample` und das Verweisen auf die `NavMenu`-Komponente (`NavMenu.razor`) mit `<Shared.NavMenu></Shared.NavMenu>` werden beispielsweise nicht unterstützt.

## <a name="conditional-html-element-attributes"></a>Attribute für bedingte HTML-Elemente

HTML-Elementattribute werden basierend auf dem .NET-Wert bedingt gerendert. Wenn der Wert `false` oder `null` ist, wird das Attribut nicht gerendert. Wenn der Wert `true` ist, wird das Attribut minimiert gerendert.

Im folgenden Beispiel bestimmt `IsCompleted`, ob `checked` im Markup des Elements gerendert wird:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Wenn `IsCompleted` den Wert `true` aufweist, wird das Kontrollkästchen wie folgt gerendert:

```html
<input type="checkbox" checked />
```

Wenn `IsCompleted` den Wert `false` aufweist, wird das Kontrollkästchen wie folgt gerendert:

```html
<input type="checkbox" />
```

Weitere Informationen finden Sie unter <xref:mvc/views/razor>.

> [!WARNING]
> Einige HTML-Attribute wie [aria-pressed](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) funktionieren nicht ordnungsgemäß, wenn der .NET-Typ `bool` ist. Verwenden Sie in diesen Fällen statt `bool` einen `string`-Typ.

## <a name="raw-html"></a>Unformatierter HTML-Code

Zeichenfolgen werden normalerweise mithilfe von DOM-Textknoten gerendert. Das bedeutet, dass das darin enthaltene Markup vollständig ignoriert und als Literaltext behandelt wird. Sie können unformatierten HTML-Code rendern, indem Sie den HTML-Inhalt mit einem `MarkupString`-Wert umschließen. Der Wert wird als HTML oder SVG analysiert und in das DOM eingefügt.

> [!WARNING]
> Das Rendern von unformatiertem HTML-Code, der aus einer nicht vertrauenswürdigen Quelle stammt, gilt als **Sicherheitsrisiko** und sollte vermieden werden.

Im folgenden Beispiel wird veranschaulicht, wie der `MarkupString`-Typ verwendet wird, um der gerenderten Ausgabe einer Komponente einen Block mit statischem HTML-Inhalt hinzuzufügen:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>Kaskadierende Werte und Parameter

In einigen Szenarios ist es unpraktisch, Daten mithilfe von [Komponentenparametern](#component-parameters) von einer Vorgängerkomponente an eine Nachfolgerkomponente zu übertragen. Das gilt insbesondere, wenn es mehrere Komponentenebenen gibt. Kaskadierende Werte und Parameter lösen dieses Problem, indem es Vorgängerkomponenten einfach ermöglicht wird, für alle Nachfolgerkomponenten einen Wert bereitzustellen. Kaskadierende Werte und Parameter bieten auch einen Ansatz für die Koordination von Komponenten.

### <a name="theme-example"></a>Designbeispiel

Im folgenden Beispiel aus der Beispiel-App gibt die `ThemeInfo`-Klasse die Designinformationen an, die in der Komponentenhierarchie nach unten weitergegeben werden, sodass alle Schaltflächen innerhalb eines bestimmten Teils der App denselben Stil aufweisen.

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

Eine Vorgängerkomponente kann einen kaskadierenden Wert mithilfe der CascadingValue-Komponente bereitstellen. Die <xref:Microsoft.AspNetCore.Components.CascadingValue%601>-Komponente umschließt eine Teilstruktur der Komponentenhierarchie und stellt einen einzelnen Wert für alle Komponenten in dieser Unterstruktur bereit.

Beispielsweise gibt die Beispiel-App Designinformationen (`ThemeInfo`) in einem der Layouts der App als kaskadierenden Parameter für alle Komponenten an, die den Layouttext der `@Body`-Eigenschaft bilden. `ButtonClass` wird in der Layoutkomponente der Wert `btn-success` zugewiesen. Jede Nachfolgerkomponente kann diese Eigenschaft über das kaskadierende `ThemeInfo`-Objekt nutzen.

`CascadingValuesParametersLayout`-Komponente:

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

Komponenten deklarieren kaskadierende Parameter mithilfe des [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)-Attributs, um kaskadierende Werte zu verwenden. Kaskadierende Werte werden nach Typ an kaskadierende Parameter gebunden.

In der Beispiel-App bindet die `CascadingValuesParametersTheme`-Komponente den kaskadierenden `ThemeInfo`-Wert an einen kaskadierenden Parameter. Der Parameter wird verwendet, um die CSS-Klasse für eine der Schaltflächen festzulegen, die von der Komponente angezeigt werden.

`CascadingValuesParametersTheme`-Komponente:

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

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
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Sie können mehrere Werte desselben Typs innerhalb derselben Unterstruktur kaskadieren, indem Sie jeder <xref:Microsoft.AspNetCore.Components.CascadingValue%601>-Komponente und dem entsprechenden [`[CascadingParameter]`](xref:Microsoft.AspNetCore.Components.CascadingParameterAttribute)-Attribut eine eindeutige <xref:Microsoft.AspNetCore.Components.CascadingValue%601.Name%2A>-Zeichenfolge bereitstellen. Im folgenden Beispiel kaskadieren zwei <xref:Microsoft.AspNetCore.Components.CascadingValue%601>-Komponenten verschiedene Instanzen von `MyCascadingType` nach Namen:

```razor
<CascadingValue Value=@parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

In einer Nachfolgerkomponente erhalten die kaskadierenden Parameter ihre Werte nach Namen von den entsprechenden kaskadierenden Werten in der Vorgängerkomponente:

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

Kaskadierende Parameter ermöglichen auch die Zusammenarbeit von Komponenten in der Komponentenhierarchie. Sehen Sie sich z. B. das folgende *TabSet*-Beispiel in der Beispiel-App an.

Die Beispiel-App enthält eine `ITab`-Schnittstelle, die Registerkarten implementieren:

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

Die `CascadingValuesParametersTabSet`-Komponente verwendet die `TabSet`-Komponente, die mehrere `Tab`-Komponenten enthält:

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

Die untergeordneten `Tab`-Komponenten werden nicht explizit als Parameter an `TabSet`übermittelt. Stattdessen sind die untergeordneten `Tab`-Komponenten Teil des untergeordneten Inhalts von `TabSet`. Allerdings muss `TabSet` weiterhin über jede `Tab`-Komponente informiert werden, damit die Header und die aktive Registerkarte gerendert werden können. Damit diese Koordination ohne zusätzlichen Code möglich ist, kann die `TabSet`-Komponente *sich selbst als kaskadierenden Wert angeben*, der dann von der `Tab`-Nachfolgerkomponente abgerufen wird.

`TabSet`-Komponente:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

Die `Tab`-Nachfolgerkomponenten erfassen die enthaltende `TabSet`-Komponente als kaskadierenden Parameter. Das bedeutet, dass die `Tab`-Komponenten sich zu `TabSet` hinzufügen und koordinieren, welche Registerkarte aktiv ist.

`Tab`-Komponente:

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor-Vorlagen

Renderingfragmente können mithilfe der Razor-Vorlagensyntax definiert werden. Mit Razor-Vorlagen können Sie einen Benutzeroberflächencodeausschnitt festlegen und das folgende Format voraussetzen:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Im folgenden Beispiel wird veranschaulicht, wie Sie <xref:Microsoft.AspNetCore.Components.RenderFragment>- und <xref:Microsoft.AspNetCore.Components.RenderFragment%601>-Werte angeben und Vorlagen direkt in einer-Komponente rendern können. Renderingfragmente können auch als Argumente an [Komponentenvorlagen](xref:blazor/templated-components) übergeben werden.

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

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

## <a name="scalable-vector-graphics-svg-images"></a>SVG-Bilder

Da Blazor HTML rendert, werden browsergestützte Bilder wie SVG-Bilder (Scalable Vector Graphics, skalierbare Vektorgrafiken, *.svg*) über das `<img>`-Tag unterstützt:

```html
<img alt="Example image" src="some-image.svg" />
```

Ebenso werden SVG-Bilder in den CSS-Regeln einer Stylesheetdatei ( *.css*) unterstützt:

```css
.my-element {
    background-image: url("some-image.svg");
}
```

SVG-Inlinemarkup wird jedoch nicht in allen Szenarios unterstützt. Wenn Sie ein `<svg>`-Tag direkt in eine Komponentendatei ( *.razor*) einfügen, wird das grundlegende Bildrendering unterstützt, aber viele fortgeschrittene Szenarios noch nicht. Beispielsweise werden `<use>`-Tags derzeit nicht beachtet, und [`@bind`][10] kann nicht mit einigen SVG-Tags verwendet werden. Weitere Informationen finden Sie unter [Improve SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271) (Verbessern der SVG-Unterstützung in Blazor).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/blazor/server/threat-mitigation>: Enthält Anleitung zum Entwickeln von Blazor-Server-Apps, die sich mit Ressourcenauslastung auseinandersetzen müssen.

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
